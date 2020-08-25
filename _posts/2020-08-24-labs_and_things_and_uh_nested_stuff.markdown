---
layout: post
title:      "Labs and Things and.. uh.. nested stuff.`"
date:       2020-08-25 03:54:21 +0000
permalink:  labs_and_things_and_uh_nested_stuff
---


One of the tougher things to grasp in rails was nested routes. Nested routes and how to use those routes in with the actions. Also, how to use the parent and child relationships within forms and creating those new attributes. Crazy I know but trust me its a thing. So what im gonna do is show you a bit of what I struggled with in my rails project so maybe both you and I can understand it a bit better.

So first lets start with our relationships, mine has been great the last few years but there comes a time when.. oh wait nevermind. Wrong relationship. Uhhum anyways.. we have a user that can have many appointments, and many laboratories through appointments. And Same for laboratories, while an appointment can belong to a lab and a user.
So here are my models for them.

**you can ignore the validates for now*
```
class Appointment < ApplicationRecord
  validates :type_of_appt, :appt_time, :appt_date, presence: true
  belongs_to :user, optional: true
  belongs_to :laboratory, optional: true
end

class Laboratory < ApplicationRecord
  validates :name, :lab_type, :phone_number, :address, :lab_hours, :days_of_operation, presence: true
  has_many :appointments
  has_many :users, through: :appointments
end

class User < ApplicationRecord
  has_many :appointments
  has_many :laboratories, through: :appointments
  accepts_nested_attributes_for :appointments, :allow_destroy => true
end
```

Ok. Next we can set up our nested routes in `config/routes.rb` as we need. We need appointments to be nested under user, so when you create the appointment you can create it on the current user. We will nest appointments with laboratories also.

**remember to add whichever actions you need in the only array*
```
resources :users do 
    resources :appointments, only: [:index, :new, :create, :show, :edit, :destroy]
  end
	
	resources :laboratories do 
    resources :appointments, only: [:index, :show]
  end
```

So far so good. Now we need to go to our controller(s) and create the actions that we need to use. We will need to make an action for each path. Ok, so in appointments controller, becuase thats the child resources nested in users resources, lets create the actions. I will explain them below. In this case, we will set up our actions in full to save time, but you get the idea.

**show all the users appointments in the index action*
```
def index
    @appointments = @user.appointments
  end
```

**show the current appointment in the show action*
```
 def show
    @appointment = Appointment.find(params[:id])
  end
```

**instantiate a new appointment*
```
  def new
    @appointment = Appointment.new
  end
```

**create the new appointment with params*
```
  def create
      @appointment = Appointment.create(appointments_params)    
    if @appointment.valid?
      redirect_to user_appointment_path(@appointment.user, @appointment)
    else
      render :new
    end
  end
	
	private 
	
	def appointments_params
    params.require(:appointment).permit(:type_of_appt, :appt_time, :appt_date, :laboratory_id, laboratory_attributes:[:name, :lab_type, :phone_number, :address, :lab_hours, :days_of_operation] ).merge(user_id: current_user.id)
  end
```

Finally, we need to create our views pages to use the actions right? Yes. (well I kinda lied, for now we will just do a nested form view but the other views are much easier trust me) When we are signed in as a user and creating a new appointment we need to also nest the parent and child in the form. However, I should add that we need to be able to select or create a laboratory when we create the appointment to associate all 3 at once. So here we have the `@appointment` nested in the `@user` form. What you are saying here pretty much is that the appointment you are creating is pertaining to that current user. And further down the form you are able to select a laboratory if it exists already by displaying all the laboratories with `Laboratory.all` and selecting with `f.collection_select`. If in any case the laboratory does not exist then you need to create a new one and that is where the `fields_for` comes in. You are setting up fields for the laboratory model by finding the current appointment being built, then iterating through the laboratories attributes to fill in each attribute.

```
<%= form_for [@user, @appointment]  do |f| %>
  <%= f.hidden_field :user_id %>
  <%= f.label :type_of_appt %>
  <%= f.text_field :type_of_appt %>
  <br>
  <%= f.label :appt_time %>
  <%= f.text_field :appt_time %>
  <br>
  <%= f.label :appt_date %>
  <%= f.text_field :appt_date %>
  <br>
  ------------------------------------------
  <p>Pick existing Lab</p>
  <%= f.label :laboratory_id %>
  <%= f.collection_select :laboratory_id, Laboratory.all, :id, :name, include_blank: "Choose" %>

  <p>Or add a new Laboratory</p>

    <%= f.fields_for :laboratory , @appointment.build_laboratory do |lab| %>
      <%= lab.label :name%>
      <%= lab.text_field :name %>
      <br>
      <%= lab.label :lab_type %>
      <%= lab.text_field :lab_type %>
      <br>
      <%= lab.label :phone_number %>
      <%= lab.text_field :phone_number %>
      <br>
      <%= lab.label :address %>
      <%= lab.text_field :address %>
      <br>
      <%= lab.label :lab_hours %>
      <%= lab.text_field :lab_hours %>
      <br>
      <%= lab.label :days_of_operation %>
      <%= lab.text_field :days_of_operation %>
      <br>
    <% end %>
<br>
  <%= f.submit %>
<% end %>
```

Ok so were done right. Not quite, but almost I promise. We need to tell the appointments model that we can have laboratory attributes created here. We do that by creating a method in the appointments model that allows that to happen.

*this looks worse than it is, you are really just creating or finding a laboratory by all attributes*
```
def laboratory_attributes=(laboratory_attributes)
    if !laboratory_attributes[:name].empty?
      laboratory = Laboratory.find_or_create_by(name: laboratory_attributes[:name],lab_type: laboratory_attributes[:lab_type], phone_number: laboratory_attributes[:phone_number], address: laboratory_attributes[:address], lab_hours: laboratory_attributes[:lab_hours], days_of_operation: laboratory_attributes[:days_of_operation])
      laboratory.appointments << self
    end
  end
```

If you notice, our appointments controller params has a `:laboratory_id,` and  `laboratory_attributes:[:name, :lab_type, :phone_number, :address, :lab_hours, :days_of_operation] `. That is becuase here we are allowing appointments to accept the `laboratory_id` or attributes in its params to create the new laboratory or find it! I know that's a lot but I hope it comes in handy for some of you, and if some how no one reads it, then I can at least be happy with coming back to this in future jobs as a cheat sheet.


