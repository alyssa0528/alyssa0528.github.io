---
layout: post
title:      "Keeping It Dry in CLI"
date:       2018-04-06 07:19:20 +0000
permalink:  keeping_it_dry_in_cli
---


I'll admit that I was pretty terrified at the prospect of creating my own CLI. Like Avi mentioned in one of his videos, there's nothing more daunting than the sight of a blank file with the cursor blinking at you. I felt exhausted from the Music Playlist, TTT w/AI, and Student Scraper labs. (I can't wait to get back into lesson mode!)

I also have to admit that just getting started on my data gem was a bit tricky. Since I've been using the Learn IDE, I felt like I wasn’t too familiar with GitHub and its various commands (clearly the teachings from the Github lesson earlier in the curriculum didn’t stick with me). One of the teaching assistants was kind enough to help me out, though, and after a few minutes I was ready to get started.

I decided to use the Rock ‘n’ Roll race series site to scrape. Running is one of my hobbies, and I wanted my gem to provide the user with information to the 30 races offered throughout the year. Its main race events page (http://www.runrocknroll.com/es/#findrace) lists all the races in neatly separated divs, but I noticed that the individual race sites varied slightly in terms of format. It limited me slightly in what I could and couldn’t scrape, but it looked like the race dates, descriptions, and hashtags were uniform across all sites, so I focused on scraping those. I also wanted to include the distances offered for each city. For that information, I had to go one page deeper on each of the city's individual sites. 

Before actually coding anything, I watched Avi’s Daily Deal gem video several times. The first time, I watched it all the way through to get an overview, and then I re-watched it, pausing along the way to “code along” with him (while in awe that he was coding everything so quickly). I found his way of thinking and planning extremely helpful. I know that I myself have a tendency to want to jump right in and start coding, but it really makes so much more sense to think about how you want your interface to look and how you want your data gem to function. Starting to code with fake data helped me figure out what type of methods and objects I needed to code.

By biggest challenge was trying to keep my code DRY. When I first got my CLI working and all my attributes scraping properly, it took 42 seconds for the program to load. The way I had written my code forced my program to scrape for ALL attributes for ALL 30 cities from the get-go (below). That's a lot of pages to scrape through and info to gather (and no one's going to wait 42 seconds for a program to start)! 


```
  def scrape_races
    html = open("http://www.runrocknroll.com/")
    @doc = Nokogiri::HTML(html)
    @doc.search("div.mix").each do |race_div|
      @race = RockNRoll::Race.new

      @race.location = race_div.css("h5 a").text
      @race.url = race_div.css("h5 a").attribute("href").text
        if !@race.url.end_with? ("/")
          @race.url += "/"
        end
      scrape_race_date
      scrape_race_description
      scrape_race_hashtag
      scrape_race_distances
    end
      RockNRoll::Race.all
  end
```

All of my "scrape" methods were very repetitive, too, and essentially followed this format:

```
  def scrape_race_date #method that parses indiv race site
    @race_site = Nokogiri::HTML(open(@race.url))
    binding.pry
    @race_site.search("#hero").each do |header|
      @race.date = header.css("span strong").text.strip
    end
  end
```

I looked at the code for the Top 50 Best Restaurants in the World gem for inspiration, since that gem scraped two different types of pages and the CLI operated smoothly with barely any lag time. I did away with the various scrape attribute methods. I greatly simplified my #scrape_races method to do just that: scrape races and nothing else:

```
  def scrape_races
    @doc = Nokogiri::HTML(open("http://www.runrocknroll.com/"))
    @doc.search("div.mix")
  end
```

I also created a new method in my Scraper class called #create_races, as well as a new method in my Race class called #self.new_from_list(race). The #create_races method would now be responsible for making new instances of Races, and it would call upon the #self.new_from_list(race) method to do that:

```
  @@all = []

  def initialize(location = nil, url = nil)
    @location = location
    @url = url
    @@all << self
  end

  def self.new_from_list(race)
    url = race.css("h5 a").attribute("href").text
    if !url.end_with?("/")
      url += "/"
    end
    self.new(race.css("h5 a").text, url)
  end
```

```
  def create_races
    scrape_races.each do |race|
      RockNRoll::Race.new_from_list(race)
    end
  end
```

This code would take care of acquiring the location and URL attributes for each Race object. For the other attributes (date, distances, description, and hashtag), the scraping itself was pretty straightforward, but putting the code together was a bit more tricky for a couple of attributes.

The distances attribute was probably the most difficult. First, I kept getting a 404 Error whenever I tried to run my gem. I couldn't understand why because as far as i knew, every Race object's URL was formatted as such: "http://www.runrocknroll/city/." The page I was scraping the distances information from was "http://www.runrocknroll/city/the-races/distances," so my Nokogiri code for parsing out the HTML was written as 'self.url + "/the-races/distances."' Everything looked right to me even when I went into Pry, and my code had actually been correctly scraping for this attribute the night before. I turned to Slack for help, and Ian suggested I add a puts statement in my code that would output every object's URL string as it was looping through the iteration. His suggestion worked; it turned out that for Santiago's site, the URL was saving as "http://www.runrocknroll.com/santiago" (no ending backslash). Therefore I had to write a condition that would check to make sure that every URL ended in a backslash and to add one if it didn't.

The second tricky bit was having the list of distances print properly. Every city offers different races; some offer just one race, while others offer several different distances. I had to make sure to get all of the distances scraped into an array, and then use #join to create a nice, readable string of distances offered. 

```
  def distances
    @race_distances = []
    distance_url = self.url + "the-races/distances/"
    @distance_site = Nokogiri::HTML(open(distance_url))
    @distance_site.search("div.sidenav").each do |distances|
      distances.search("a").collect do |distance| #iterate through the XML of distances
        race_distance = distance.text
        @race_distances << race_distance
      end
    end
    @distances = @race_distances.join(", ")
  end
```

I also discovered that Madrid's site was laid out a little differently in that it didn't really have a race description. I therefore had to write a condition that would direct the user to click on Madrid's URL to get more information.

Once I reformatted my code, my gem ran smoothly. No 42-second wait! Now it was scraping attributes only for a particular object (the one the user requests), instead of all 30 objects at once. 

Here's a look at my final code:

```
class RockNRoll::CLI

  def call
    puts "~~~~~Welcome to the Rock 'n' Roll 2018-19 races!~~~~~"
    sleep(1)
    RockNRoll::Scraper.new.create_races
    show_list
    menu
  end

  def show_list
    @races = RockNRoll::Race.all
    @races.each.with_index(1) do |race, index|
      puts "#{index}. #{race.location}"
    end
  end

  def menu
    input = nil
      puts "Enter the number of the race you'd like more information about. Alternatively, type 'list' to see the full list of races, or type 'exit':"
      while input != "exit"
      input = gets.strip.downcase
      if input.to_i > 0 && input.to_i <= RockNRoll::Race.all.length
        race = RockNRoll::Race.retrieve(input.to_i)
        show_details(race)
        puts ""
        puts "Enter another number to see race details. Type 'list' to see the full list or 'exit':"
      elsif input == "list"
        show_list
      elsif input == "exit"
        exit_program
      else
        puts "Sorry, please enter a valid number or type 'list' or 'exit':"
      end
    end
  end

  def show_details(race)
    puts "~*~*~*~*~*~*~* Details for #{race.location} ~*~*~*~*~*~*~*"
    puts "Date(s):              #{race.date}"
    puts "Distance(s):          #{race.distances}"
    puts "Description:          #{race.description}"
    puts "Event Hashtag:        #{race.hashtag}"
    puts "Event URL:            #{race.url}"
  end

  def exit_program
    puts "Thanks for checking, and keep on training!"
  end

end

```

```
class RockNRoll::Scraper

  def scrape_races
    @doc = Nokogiri::HTML(open("http://www.runrocknroll.com/"))
    @doc.search("div.mix")
  end

  def create_races
    scrape_races.each do |race|
      RockNRoll::Race.new_from_list(race)
    end
  end

end

```

```
class RockNRoll::Race
  attr_accessor :location, :url, :date, :distances, :description, :hashtag

  @@all = []

  def initialize(location = nil, url = nil)
    @location = location
    @url = url
    @@all << self
  end

  def self.new_from_list(race)
    url = race.css("h5 a").attribute("href").text
    if !url.end_with?("/")
      url += "/"
    end
    self.new(race.css("h5 a").text, url)
  end

  def self.all
    @@all
  end

  def self.retrieve(input)
    self.all[input.to_i - 1]
  end

  def race_site
    @race_site ||= Nokogiri::HTML(open(self.url))
  end

  def date
    @date = race_site.search("#hero span strong").text.strip
  end

  def description
    @description = race_site.search("#features div.column p").first.text
    if @description == "Leer Más"
      @description = "Please click on the race's URL for more information."
    else
      @description
    end
  end

  def hashtag
      @hashtag = race_site.search("#ribbon div.hash").text.chomp(' /')
  end

  def distances
    @race_distances = []
    distance_url = self.url + "the-races/distances/"
    @distance_site = Nokogiri::HTML(open(distance_url))
    @distance_site.search("div.sidenav").each do |distances|
      distances.search("a").collect do |distance| 
        race_distance = distance.text
        @race_distances << race_distance
      end
    end
    @distances = @race_distances.join(", ")
  end
end

```


