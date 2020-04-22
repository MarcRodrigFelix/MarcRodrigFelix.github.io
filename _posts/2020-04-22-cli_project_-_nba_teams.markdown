---
layout: post
title:      "CLI  Project - NBA Teams."
date:       2020-04-22 06:30:54 +0000
permalink:  cli_project_-_nba_teams
---

Project one in the books! I have to say I do feel pretty good right now. However, I was not feeling too good before I started the project. I guess you have to start somewhere. Everyone ever has had to start at 0. No one is just great at anything (even if you think you are, sorry.. you are NOT), well not without any practice and experience at least.
I am glad I started where I did. I think It helped me push my self and face the challenge, instead of the alternative. Which would not be good.
When the project week started, I was thinking nervously and panicking, "what am I going to do? what am I even doing? I have no clue what I am doing!". I somehow realized that as long as I take it a step at a time, I should be fine. So I managed to calm myself, and focus. It's always going to be a process. I can't just sit and fully build something out of nowhere. There are steps to this.
So the first thing was to pick a topic for my project. I went with something I have a passion for, something that I have always enjoyed, and for me that was basketball. I've always loved playing ball and followed the NBA since I was a child. I thought the NBA would have fun data to play with. It was either that or video games. Plus, teams and players have an easy relationship, so I made my choice. Next, I went with pulling data from an API because I felt like I understood scraping pretty well, so I decided with the opposite of what I felt I had more knowledge in, that way I could have more practice.
Everything was going pretty well. You know, your typical hold-ups for a beginner, but still making pretty good strides. I found a solid API to use. I built out my file tree. I created a Github repo. I found a way to pull my team data, which went pretty smoothly.
Then, I got to my player data. The player data was a bit tougher to deal with for me because it consisted of each player (which was a hash) having a team hash within that player hash. So I had to create a way to pull the player data and initialize the player from that player hash, and also store the team information. Well, it was an easier fix then I thought. Most of my code was correct (by correct I mean at least half-decent functioning code, you know what I mean..), but for some reason, I couldn't get my Player class to initialize a player from the hash. Well, the problem, after getting some help and using google, was that I was trying to pass in all my key/values into my new instance as individually, so my create method thought that the team attribute was supposed to be just one key/value pair, but it was a string with a hash of multiple key/value pairs stored inside it. Instead, my create method had to be able to pass in a hash and split up the attributes as needed instead of specifying each attribute.
Then, as I was adding the I finishing touches. I noticed I was only getting the same 25 players when I tested the CLI.. something seemed weird. I know as a fan of the NBA that there are more than 10 players on one team (max of 15 players) so where are the other 425 players? Welp turns out my API has a ton of pages and only 25 players per page. So pagination here we come. I have never had to paginate through API pages before. So that was a treat let me tell you. Luckily I am blessed enough to have a couple of great classmates that we're able to point me in the right direction to at least get me started. After a couple of hours of playing with the code. I got the pagination to work! I'll put a snippet below so you can see the while loop.

```
def self.get_nba_players
    url = "https://www.balldontlie.io/api/v1/"
      response = HTTParty.get(url + "players?page=1")

      data = response["data"]
      meta = response["meta"]

      players_per_page = data.count
      players_total = meta["total_count"]

      page = 100
      last_page = meta["total_pages"]

      while page <= 131
        page_url = "https://www.balldontlie.io/api/v1/players?page=#{page}"
        puts "Please allow to fully load."
        puts "Loading.. #{page}/131"
        puts ""

        page_response = HTTParty.get(page_url)
        players_hash = page_response["data"]

        NBATeams::Player.create_players_from_api(players_hash)

        page += 1
      end

  end
```

Anyways, that's it for the fun I had with this project. I will say I am very proud of my project. It might not be the prettiest or the best, but it works and its something I built myself within only one month and a half into school. I never imagined building this on my own. This makes me excited for future projects and my future career!
