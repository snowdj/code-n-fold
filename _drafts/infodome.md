---

layout: default
title: The InfoDome - an IoT project
category: DIY
tags:
- iot
- arduino
- tinkering 
type: post
excerpt: A mashup of nixie tubes, neopixels and Arduino Yùn to display information.  
image: header-infodome.jpg
gradient: 1

---

{% include media-youtube.html file="v8ImjMalxpY" title="Demo Video" %}
 
## The project
 
The InfoDome is an IoT project using an Arduino yùn to fetch and display remote information as well as temperature and RTC data through four [QS30-1](https://www.google.ch/search?q=QS30-1) nixie tubes.

 The goal was to design a lightweight control system to operate the Dome sensors and peripherals. Because multiple operations should be done in parallel and independent of each other (read hand sensor, fade ambient lights) blocking the processor should be avoided at any cost.
 
 The SMLib library helps to achieve this by turning the whole system into state machines which are able to execute periodically without blocking each other.
 
 A different approach for a typical Arduino project was taken to keep the code flexible and fast.
 
 State machines are handling data input and output. SMLib allows or _enforces_ a layered system where one machine controls the execution cycle of other machines.
 
 Each machine serves different purposes with different execution times.
 
 | Machine | Description |
 |---------|-------------|
 | Master | Executes all other state machines and handles the booting process. |
 | Display | Display values on the nixie tubes based on the current mode |
 | Ambient | Control the ambient leds based on the mode |
 | Detect | Detect and debounce hand movements |
 | Data | Download data via WiFi from Temboo |
 | Cylcer | If turned on, cycle to a new mode after a specific interval until turned off |
 
### Online Data
 
 The online data is aggregated using the "Choreo Editor" [Twyla](https://temboo.com/download/twyla) from the Arduino built-in IoT Stack [Temboo](https://temboo.com/).
 Temboo is used in this project because it requires no additional
 software in order to work - this is a good choice because the Arduino Yùn mini doesn't ship with an sd card reader, so we can't easily extend its very limited storage.
 
 The editor takes some getting used to but is, all in all, quite easily understandable. Using the editor I've created a custom "choreo" or task which does these sequential tasks in order:
 
 1. Fetch open merge requests from git server
 2. Fetch open tickes from Atlassian Jira API
 3. Fetch unread email from Google API
 4. Fetch current local time for RTC adjustments
 
 The great thing about the editor is that choreos are directly testable :smile:.
 
 A run of this combined choreo returns:
 
 ```
     SUCCESSFUL COMPLETION:
     OUTPUT VARIABLES:
 
     currentTime = 18:07
     totalEmailCount = 45
     totalJiraTickets = 39
     totalMergeRequests = 1
 ```
 
 This data can be fetched and parsed by the Arduino Temboo library.
 
# Caveats
 
 The biggest obstacle in this project by far is the extremely limited program storage. With only 28,672 bytes (or 0.001 kbytes) the options are limited and minimal sized code is key.
 The source code is currently occupying 99% program storage.
 Imagine what could be done with even more space!
 
 Because the temperature sensor is placed inside the dome, it'll read the inner temperature which will be a little over the current room temperature. We could compensate for this but it's a nice way of surveying the Dome itself.
 
# Closing Thoughts
 
 As always, there are still some things left to be done... But I'm quite happy how this project turned out.
 
 You could technically fetch and display any data you'd like to as long as it's accessible via (auhtenticated) APIs. Temboo also has a free plan with enough requests to test the choreos.
 
 If you're an experienced :snake: (Python) programmer, there might be the option to shift the bulk of the code to the linux distribution and turn the Arduino into peripheral just displaying information.
 Check the Arduino Yùn Tutorials for more information.
 
            

{% include media-image.html file="infodome/front.jpg" title="The front"%}
{% include media-image.html file="infodome/side.jpg" title="Sideview"%}
{% include media-image.html file="infodome/side_closeup.jpg" title="Sideview Closeup"%}
{% include media-image.html file="infodome/closeup_top.jpg" title="Closeup"%}
{% include media-image.html file="infodome/dome1.jpg" title="InfoDome"%}
{% include media-image.html file="infodome/dome2.jpg" title="InfoDome"%}


































