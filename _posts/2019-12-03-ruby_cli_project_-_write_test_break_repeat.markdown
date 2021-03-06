---
layout: post
title:      "Ruby CLI Project - Write, Test, Break, Repeat"
date:       2019-12-03 19:41:42 -0500
permalink:  ruby_cli_project_-_write_test_break_repeat
---


I got an incredibly late start to this CLI project in the wake of a wedding and a period of time at work that left me without the brainpower to do much more than stare at a tv. 2 months later, far behind my intended schedule, I arrived at the CLI project. It was intimidating but exciting, but all problems are right?


**Step 1 - Define the End Product**

Presented with the challenge of finding a site to scrape data from, and to ensure good OO programming, I initially thought I'd scrape Etsy's main page for categories of adorable items, allow users to drill down to a category and view information about each item in it. And then I saw the javascript, while also thinking that scraping a site whose main appeal is visual, I reconsidered my options. 

I then settled on checking out the New York Time's Bestseller list, inspired by casually browsing it for a gift for my mother-in-law. The site was simple, elegant, and primed for scraping. I also enjoyed that I could drill down three levels. A view of categories of books, a list of books by that category, and then a deep dive into a book itself within that category.

I wanted to build a scraper CLI that took the [New York Times' best seller list](https://www.nytimes.com/books/best-sellers/), and do several things with data avaialble in that site.
1. Present the user with a list of categories of books on the Best Seller list
2. Prompt the user to select one of those categories to see the books within it
3. Present the user with a list of the books in that given category
4. Prompt the user to select a book within that category
5. Present the user with details about the book they selected
6. Allow the user to navigate between any "level" in a non linear fashion


**Step 2 - Ghost in the State Machine**

Steps 1-5 were fairly straightforward to write. When thinking about the objects that would be working together, I had this view.

> CLI 
> 
>-Scraper
>
>   -Category

>     -Book

And I built out classes for each of these. The Scraper class would take care of creating the Categories and Books by Category, while Books would be nested underneath Categories. 

I'd like to spend more time talking about my favorite problem that I encountered while writing my CLI because I actually learned a ton and it was I think, a concept we haven't encountered yet in the curriculum so far (or if we have, I had certainly forgotten it by the time I got to the project).

I wanted the user to be able to be able to navigate to any "level" of menu at any time after they were at least 2 levels deep, and from the third level, to be able to navigate up one or two levels. And for me, this was *hard.*

What do I mean when I say "level" in this case? Here's how I imagined it, so we have a common language.

* Level 0 - Category View
* Level 1 - Books by Category View
* Level 2 - Selected Book View

When the user opens the CLI, they are immediately on Level 0, and presented with a list of categories. 

They are then prompted to select a category, to see the Books by Category View, or Level 1. At this point, it was pretty easy to allow them to either continue drilling down to Level 2, the Selected Book View, or go back "up" a level to the Category View.  It was when we drilled one level deeper that I started running into a wall.

When it came to dealing with how to let the user go back to the Category View from the Selected Book View by letting them skip the Books by Category View, I was stumped. I wanted something more elegant than having them just go back to Level 1 to get back to Level 0 because let's face it, that's slow!

Enter, State Machine. 

Checking out this Wikipedia doc, specifically the[ Turnstile Example](https://en.wikipedia.org/wiki/Finite-state_machine#Example:_coin-operated_turnstile) was super helpful in helping me visualize the specific "states" or "views" my CLI could be in. 

I even drew a [version](https://www.draw.io/?lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=BestSeller%20State%20Machine#R3Vpbk6I4GP01Pq5FgKA8jnbvzOylamqt3dndtzRETYnEhdjq%2FPpNJFySoGgP4DR2VRf5yI1zTr58uYyc%2Bfb4MUG79e80xNHItsLjyHka2fbE9vl%2FYThlBncyyQyrhISZCZSGBfmGpdGS1j0JcapkZJRGjOxUY0DjGAdMsaEkoQc125JGaqs7tMKGYRGgyLR%2BJSFbZ9YptEr7J0xW67xlYMk3W5RnloZ0jUJ6qJic55EzTyhl2dP2OMeRwC7HJSv384W3RccSHLNbCnibX%2F%2F4d%2BOtnj%2F9slxY25c5OMU%2FyVpeUbSXH%2Fx8JEx2mJ1yFHhNHHCemB3WhOHFDgXizYFTzm1rto14CvBHlO4yFpbkiHnDsyWJojmNaHKuyFlC8cftKUvoBlfeeOefKEFjVrFnP26XPcUJw8eLEIACWC5ITLeYJSeeRRbIqTipyUOFWGlaVzjNbUhKaVXUW6LNHyTgd4BvG%2BDPEcMreu7ybyTlPFh%2FEXzojo4Q4ekyqKUjmOKXZTuwuyrswDdxB7AG%2BMLYOvKOgfyM0k3KTS%2Bi38OkwZ78cDy4Bg8LHHGoOEi2JSgZCPTQ%2FuGghwb0BsjBPnkVmJ2BxHH4QcynPBlEKE1JoAKN%2BbzxN3%2B2xlCm%2Fqm8eRJAWHniJBNZizg0pmANV94ruk8C3ORLTfwr%2BNbBm9sSHCFGXtVu1EEuW%2FhCScxKegFQ6XV01rLuy1LVOVqrqCh4qSKGkhVmRkWcGXSqZNuJDOmVDjtqO64eO%2Bj9up6fP2Q9KOVYcPB2hXrdKdSrSnRs2ZOrMhWJLzgh%2FINwktti%2FnEVvYtkIXiRKKs6pzqQvJzEMkk0OdlHDQ0HwnaGBvQbKmppaDgTtZ2moQH1%2FE4PQ2PSi%2FO2%2BnLezkMV6qoEAj3ev1WhwGqoqC2FWlowYV1XKIBX83ejUH9YCn2oD9Vd35sVqjvjrhQKryvOyO9eV3Q3Cs1jti4k6k60AOPu%2BGIo2vWshoj2du%2FaT2gMtVAXggbv6mn5e9GuuW%2FXlnaB71Qd7NgHziPF%2B9B1nUZtsQq6V7t%2BP9LVlOs0RK76otVvCHS1JWDDuNC%2B2e1lWJj7SW0NC2uqjAowvX%2FJONBRUWyj3x2N1E%2F6bY%2BKC1tvN44K4Pah27Y3426V2uN2BS7sh94dFWh0eR15Vq9eq5e6pTvLPvYEQIf7ZY4aE1hed97vcYssd6yvs3w4tly%2F%2FME3ilQPXTuKXLXue%2FBqr9ypml3J3ZFEzX2rP1OuEK6LmCtFnL%2BNbO%2B%2FvTgSn81QsClThpQZPjLtqCciq1iI%2BVwZN4gjGxKg6IN8sSVhKIrPEpySb%2BjlXJXQpkSW1wtnI%2FgkmuPV7RlNs%2FsIpUv9vnNQoOkrF0L1FMiukbg%2Bw7Z2CASmlwjJzuEEI9kx3ADQt7UN5GkN%2BqBP9G3zGLoW%2FZpz6fdPB9BuBdTS4fdKh7l6MOiQDBCcDoEDWz%2B7mNRw4PbKgRkJf455gheyLcnG53i3N68ovUP4XRX8fHZ4nD8yI8ja22DvEGrgqzOvawq9X52bE%2B9QoNave8FHQ20eRQ0Faj2kqbnQ2CvUeTx7bQaVF%2BrocnCzKcgHdRHeu6ZDr7tg%2BgY%2BeLK8OZwtz8rr187z%2Fw%3D%3D) of my state machine for my CLI which really helped me visualize my views and how I wanted them to interact with eachother, and how I wanted the user to move between them. 

By thinking about my CLI views as "states", it really helped me flesh out how I wanted users to be able to transition to those states, and what actions were required to do that. 

With this design fleshed out in text and on paper, it was time to put it into... more text, in my code. I got to work building out a method called #menu, that had a pretty robust case when statement that set variable named "state", to default to "category_view", which you may recall is Level 0. From there, the menu method prompts the user for specific information depending on what "state" or "view" they are in, then depending on their input, changes the "state". 

It's not a perfect method, and I may go back to optimize it, but I was pretty proud with how this worked out and how an end user could bounce from Level 2 to Level 0 without having to go to Level 1 at all. 

**Step 3 - A QA Engineer Walks into a Bar**

My second favorite part of this project is maybe going to sound absurd. Once I had painstakingly built my scraper, figured out my state machine, built out my ridiculous menu method, and had a CLI that I was happy with, I tried to break it. 

My day job is working with software, and while I'm not a QA person, I absolutely LOVE trying to break things. I really enjoyed looking at my CLI and trying to figure out how to make it as solid and unbreakable as possible right now.

Questions I asked myself, and that I'd encourage anyone who writes code, were the following.

1. What happens if I enter an unexpected input. I know I'm expecting a string that represents a numbered selection. What if I enter "will this break?" on any view?
2. What happens if I enter an unexpected input 5 times, then enter the expected input?
3. What happens if I bounce around the different levels, entering unexpected inputs several times at each level before changing levels?
4. Can I really "exit" at every level?

I think maybe everyone who starts writing code, or works in software has heard this joke, and honestly while working on this project it inspired me to to really try and break my CLI, but maybe with less fire. 

> A software QA engineer walks into a bar.
> They order a beer. Orders 0 beers. Orders 99999999999 beers. Orders a lizard. Orders -1 beers. Orders a ueicbksjdhd.
> 
> First real customer walks in and asks where the bathroom is. The bar bursts into flames, killing everyone.

When trying to break my CLI, I discovered a lot of gaps in how I expected my code to work, compared to what it's actually doing, and learned something pretty useful. As the person writing the code, you know precisely what you're expecting, but end users may not. Try to approach your CLI as if you're an end user who doesn't know what is going on under the hood. Ordering a lizard can go a long way when it comes to making your code more robust!

