---
layout: post
title:  "CLI Data Gem Project"
date:   2017-08-16 00:01:52 +0000
---


This blog post covers the making of my CLI data gem project for learn.co.

When I started the project, I had in mind that I wanted to build a gem for something that I had some familiarity with. From that came the idea to do something based around a video or card game that I enjoyed. I chose to do *Magic: the Gathering* because I knew where I could find a database of info that would fit well into CLI data gem. The website mtggoldfish.com keeps a record of the top performing decks for recent *Magic* events that I could scrape and put into a gem for users to access.
 
I started by using bundler to create a gem structure(bundle gem *mtg_top_decks*). Using this helped with the dependencies and basic stuff needed to make a gem.
 
At this point, I wanted to form a model for how the classes might relate to each other. The first class was the CLI class or the controller. It contains the code for the user to interact with the classes that provide the information. The first decision was going to be how many metagames to provide to the user to get a list of events for. *Magic* has many formats, or metagames, that can be used to play that game. Basically the format defines which set of cards can be played. For the gem, I chose Standard, which includes cards from up to the last 8 set releases, Modern, which includes cards from *Eigth Edition*(2003) onwards, and Legacy, which includes cards from the inception of the game. These metagames are the three most popular, and would suffice for a good percentage of people that would use this gem. From there the model took shape: Metagames had many events; Events had many decks; and decks had many cards.

From there I started to build the scraper for each metagame that got to load the events. I built a scraper for the standard metagame using a css selector to get the event names from the table at right hand side of the website(mtggoldfish.com/metagame/standard) Once I found the right selector, the code to list the event names was easy to finish. At this time, I realized that the css selectors for the other two metagames would be the same, and that the only difference in the three metagames was the last section of the website name(mtggoldfish.com/metagame/modern;mtggoldfish.com/metagame/legacy). Using this info, I created a Urls module that contained to code scrape the metagame for events and to scrape the events to get the decks
. 
```
module Urls

  def standard_url
   @standard = Nokogiri::HTML(open("https://www.mtggoldfish.com/metagame/standard"))
  end

  def standard_event_url
   @standardevent = Nokogiri::HTML(open("https://www.mtggoldfish.com#{@standard.css(".decks-sidebar h4 a")[@event_date_selected]["href"]}"))
  end

  def modern_url
   @modern = Nokogiri::HTML(open("https://www.mtggoldfish.com/metagame/modern"))
  end

  def modern_event_url
   @modernevent = Nokogiri::HTML(open("https://www.mtggoldfish.com#{@modern.css(".decks-sidebar h4 a")[@event_date_selected]["href"]}"))
  end

  def legacy_url
   @legacy = Nokogiri::HTML(open("https://www.mtggoldfish.com/metagame/legacy"))
  end

  def legacy_event_url
   @legacyevent = Nokogiri::HTML(open("https://www.mtggoldfish.com#{@legacy.css(".decks-sidebar h4 a")[@event_date_selected]["href"]}"))
  end
end

```

I could then take the css code that gave me the event names and put it into a method by itself. After adding the proper class variables, the MetagameScraper class was finished.

```
class MetagameScraper
  include Urls
  attr_accessor :standard, :modern, :legacy

  def standard_scraper
    standard_url
    self.get_events(@standard)
  end

  def modern_scraper
    modern_url
    self.get_events(@modern)
  end

  def legacy_scraper
    legacy_url
    self.get_events(@legacy)
  end

  def get_events(metagame)
    counter = 1
    metagame.css(".decks-sidebar h4").each do |event|
      puts "#{counter}. #{event.text.strip}"
      counter += 1
    end
  end
end

```

I then went to work on the Events class. Using the same Urls module to load in the selected metagame url and event selected url, I only needed to write code that could scrape the events for the decks  and then list the deck names for the user. After making sure I had the proper class variables inserted into the attr_accessor, I was then finished with the Event class.

```
class Events
  include Urls
  attr_accessor :event_date_selected, :standard, :standard_event, :modern, :modern_event, :legacy, :legacy_event

  def initialize(number_selected)
    @event_date_selected = (number_selected * 2) - 1
    @array_of_decks = []
  end


  def standard_events
    standard_url
    standard_event_url
    self.scrape_event(@standardevent)
    self.list_decks
  end

  def modern_events
    modern_url
    modern_event_url
    self.scrape_event(@modernevent)
    self.list_decks
  end

  def legacy_events
    legacy_url
    legacy_event_url
    self.scrape_event(@legacyevent)
    self.list_decks
  end

  def scrape_event(event)
    event.css(".tournament-decklist-odd  td a").each do |deck|
      @array_of_decks << deck.text
    end
    event.css(".tournament-decklist-event  td a").each do |deck|
      @array_of_decks << deck.text
    end
  end

  def list_decks
    counter = 3
    counter2 = 1
    @array_of_decks.each do |deck|
      if counter % 3 == 0
        puts "#{counter2}. #{deck}"
        counter2 += 1
      end
      counter +=1
    end
  end
end

```

To start on the Decks class, I used the Urls module to load in the urls needed. After that, I needed to get the deck url from the event page, and then scrape that webpage in order to get the cards for the deck. The list_cards method gets the card name and quanity of cards  in the deck and lists them for the user.

```
class Decks
  include Urls
  attr_accessor :deck_selected, :event_date_selected, :standard, :standard_event, :modern, :modern_event, :legacy, :legacy_event

  def initialize(deck_selected, event_date_selected)
    @deck_selected = (deck_selected * 3) - 3
    @event_date_selected = (event_date_selected * 2) - 1
    @array_of_deck_urls = []
  end

  def show_decklist_standard
    standard_url
    standard_event_url
    self.scrape_url(@standardevent)
    self.list_cards
  end

  def show_decklist_modern
    modern_url
    modern_event_url
    self.scrape_url(@modernevent)
    self.list_cards
  end

  def show_decklist_legacy
    legacy_url
    legacy_event_url
    self.scrape_url(@legacyevent)
    self.list_cards
  end

  def scrape_url(event)
    event.css(".tournament-decklist-odd  td a").each do |url|
      @array_of_deck_urls << url["href"]
    end
    event.css(".tournament-decklist-event  td a").each do |url|
      @array_of_deck_urls << url["href"]
    end
  end

  def list_cards
    currentdeckselection = Nokogiri::HTML(open("https://www.mtggoldfish.com#{@array_of_deck_urls[@deck_selected]}"))
    deckformat = currentdeckselection.css(".deck-col-qty,.deck-col-card").collect do |card|
      card.text.strip
    end

    counter = 0
    while counter < deckformat.length
      puts "#{deckformat[counter]} #{deckformat[counter + 1]}"
      counter += 2
    end
    puts "The last 15 cards listed are the sideboard for the deck."
 end
end
```

 The last part was making sure the CLI was user-friendly and easy to read. After more than a few interations, the current interface works as intended everytime, giving the user the correct prompts at every intersection and maintaining a feedback loop until the user is finished with the gem.
 
```
class CommandLine

  def call
    puts "welcome to MtG: Top Decks. Please type a number of a metagame to view recent winning decklists: Type 'exit' to quit."
    puts "1. Standard"
    puts "2. Modern"
    puts "3. Legacy"
    input = gets.chomp
    if input.to_i == 1
       puts "Please select an event:"
       MetagameScraper.new.standard_scraper
       input2 = gets.chomp
       puts "Please select a deck to see the decklist:"
       Events.new(input2.to_i).standard_events
       input3 = gets.chomp
       Decks.new(input3.to_i, input2.to_i).show_decklist_standard
       self.after_list_call
     elsif input.to_i == 2
       puts "Please select a event:"
       MetagameScraper.new.modern_scraper
       input2 = gets.chomp
       puts "Please select a deck to see the decklist:"
       Events.new(input2.to_i).modern_events
       input3 = gets.chomp
       Decks.new(input3.to_i, input2.to_i).show_decklist_modern
       self.after_list_call
     elsif input.to_i == 3
       puts "Please select a event:"
       MetagameScraper.new.legacy_scraper
       input2 = gets.chomp
       puts "Please select a deck to see the decklist:"
       Events.new(input2.to_i).legacy_events
       input3 = gets.chomp
       Decks.new(input3.to_i, input2.to_i).show_decklist_legacy
       self.after_list_call
     elsif input == "exit"
       puts "Thank you for using MtG: Top Decks!"
     else
       puts "Please enter a valid input."
       self.call
    end
  end

  def after_list_call
    puts "Would you like to view more decks?(y/n)"
    input = gets.chomp
    if input == "y"
        self.call
      elsif input == "n"
        puts "Thank you for using MtG: Top Decks!"
      else
        puts "You can't seem to make up your mind! We will start over for you!"
        self.call
    end
  end
end

```

Thus is the CLI data gem I have created. It may not be perfect code, but it works as intended, and to be honest, looks better than I thought I was originally capable of. 

Thanks!

Randall Robinson
