---
layout: post
title:      "Sinatra Final Project Thoughts"
date:       2020-09-23 00:19:10 +0000
permalink:  sinatra_final_project_thoughts
---


When I first started on the Sinatra module, I really liked the MVC structure and getting to play with it. However life had other plans and I quickly became unable to keep up with my day job AND have brain cells left to dedicate to homework and learning. Sprinkle in the external stresses of a global pandemic and I found myself caught in a loop of avoidance. I did the learning equivalent of playing a video game right up until the final boss, then stopping entirely. This made coming back INTIMIDATING. But listen here, if I can do it, you can too.

So here are some thoughts from an almost-Sinatra-dropout to you!

# 1. You can do it 

I promise you, you can. Maybe there's something about the MVC structure that is confusing to you and you just can't grok it right away, maybe you hated html forms with a fiery passion because of previous Capybara tests. Don't worry, lean into that and learn. Embrace it.



# 2. Shotgun is critical

Shotgun is your friend, but be aware of the "back" button while using it. For example, while testing out some adjustments to a form I was building, I changed the following line from:
`<li><a href="/console/<%=c.id%>"> View this Console</a>`
To:
`<li><a href="/consoles/<%=c.id%>"> View this Console</a>`

Note that the old value contains a singular version of "console" when it should be pluralized.

In shotgun, I'd simply press back instead of refreshing my page, and was stumped about why my saved change which I knew should have worked, was throwing the same error. Well it turns out when you don't refresh the page, your change is not reflected. The take away? Always refresh the page you're working on in shotgun or simply start over at the beginning of the chain of actions you're testing. Also use shotgun to test. 

# 3. Get inside the user's head when designing
One thing I really enjoyed about this project, and the CLI project, was making it "user proof". What user behaviors that I knew to avoid, could break my project and how could I prevent a user from doing the same thing? I encourage you to ask yourself the same kind of question and build rails to guide the user behavior. Ask yourself, "What happens if I do X?" Try it, break it, prevent it from happening and probably break something else in the process, but that's the fun!

One of my favorite problems was this. I wanted a user to be able to add a rating of a game they created. However, I wanted to make that rating on a scale of 0-10 for a few reasons. Consistency for one, but then I also didn't want users to enter shenanigans into my beautiful database. So I both had to communicate to the user that the proper input format was 00-10 AND prevent them from inputing garbage. Using both the **title** attribute to tell the user what I needed them to input and the **pattern** attrtibute to enforce that behavior, I pulled it off. 

```
<br>
  <label for "rating">Rating: </label>
  <input type="text" id="rating" name="rating" placeholder="Rate from 00-10" pattern="(?![1-9][1-9]|\d{3,})\d\S+" title="Enter 2-digit rating">
  <br>
```

I honestly really didn't love the html sections earlier on, but this project really made me appreciate forms and creating protections for the user. Like using html input attributes to make a field required (username and password anyone?) Or brushing up on my regex chops to make users create secure (ok secure-ish) passwords, or make sure they can't register without what looks to be a valid email address. And finally using the title to give the user some guidance on what you expect. 

```
<div class="container">
  <form method="post" action="/signup">
    <label for "username">Enter Your Name:</label>
    <input type="text" id="username" name="username" required >
    <br>
    <label for "email">Enter Your Email:</label>
    <input type="text" id="email" name="email" pattern="[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}$" title="Valid email address" required>
    <br>
    <label for "password">Enter Your Password:</label>
    <input type="password" id="password" name="password" pattern="(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,}" title="Must contain at least one number and one uppercase and lowercase letter, and at least 8 or more characters" required>
    <br>
    <input type="submit" id="submit" value="Sign Up">
  </form>
  </div>
```

Doing required form inputs and user param validation checks in the controller may be a belt and suspenders approach, but in my opinion, it's better to give users real time feedback on why their inputs are bad and how to correct them while they're still on the view, rather than making them submit the form and then getting an error back. 

Harkening back to rule #2, while navigating my app, I found myself wanting more shortcuts to specific views vs. having to rely on the urls, so I was generous with hrefs. Little things that irritated me I would make slight adjustments to correct. Like, what if I wanted to go back to my profile from the create console view? 


I came back dreading this project and worried it was insurmountable but I hope the ramblings of an almost-drop out are helpful to anyone out there reading this and working through the same projects. 
