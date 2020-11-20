---
layout: post
title:      "Rails Project Learnings: Accessing Objects in fields_for"
date:       2020-11-20 18:41:44 +0000
permalink:  rails_project_learnings_accessing_objects_in_fields_for
---


Coming into Rails out of Sinatra, was intimidating at first, and then when I learned all of the built in functionality and magic Rails just does, my mind was blown. There were certain things about Rails that didn't quite stick with me until I got to this project, and I figured I'd share with you something I learned about fields_for during this project.

**The Models**

My models that are relevent to this learning opportunity, looked like this:

Workout
```
class Workout < ApplicationRecord
belongs_to :user
has_many :workout_exercises
  has_many :exercises, through: :workout_exercises
  has_many :comments
  has_many :users, through: :comments
  has_many :categories, through: :exercises
	
	.....
	end
```

Exercises
```
class Exercise < ApplicationRecord
  belongs_to :user
  belongs_to :category
  belongs_to :muscle_group
  has_many :workout_exercises
  has_many :workouts, through: :workout_exercises
	
	...
	end
```

WorkoutExercises
```
class WorkoutExercises < ApplicationRecord
  belongs_to :exercise
  belongs_to :workout
	
	
	....
	end
```



So a Workout has a name, and has many WorkoutExercises, and a Workout has many Exercises through WorkoutExercises. And Exercises have many Workouts through WorkoutExercises.

**The Challenge**

On my workouts/:id/edit view, I wanted a user to be able to edit not only the workout name, but also the workout exercises within it. Addtionally, I wanted the user to be able to delete a workout exercise from that view. To look something like this:

![Edit Workout View](https://imgur.com/lt0IUXd)

My view looked like this:
```
<h1> Edit <%=@workout.name%> Workout </h1>

<%= form_for @workout do |f| %>

Edit Workout Name: <%= f.text_field :name %>
<br>
<h4> Edit the Exercises in this Workout? </h4>
  <%=f.fields_for :workout_exercises do |we| %>
    <li>
      <%= we.text_field :id%>
    Change Exercise: <%= we.collection_select(:exercise_id, Exercise.all, :id, :name)%>
    Edit Rep Count: <%= we.number_field :reps, min: 1, max:100%>
    Edit Set Count: <%= we.number_field :sets, min: 1, max:100%>
    <%=link_to "Delete this Workout Exercise?", workout_exercise_path(we), method: :delete, :confirm => "You Sure?" %>
    <% end %>
  </li>
  <br>
<%=f.submit "Update Workout" %>
<% end %>

<h4>Or add more Exercises to this Workout</h4>
<%=link_to "Create a New Workout Exercise", new_workout_workout_exercise_path(@workout.id)%>

```

All well and good right? Newp. Commence a frustrating hour long learning experience, that when things finally worked, was awesome! 

I was expecting the id of 'we' to get passed to my params. Instead, here's what I was seeing

```
=> <ActionController::Parameters {"_method"=>"delete", "authenticity_token"=>"uwat7LrKthjpw/02Zu7hHO...", "controller"=>"workout_exercises", "action"=>"destroy", "id"=>"#<ActionView::Helpers::FormBuilder:0x00007fba3aca6880>"} permitted: false>
```

What the heck is "ActionView::Helpers::FormBuilder:0x00007fba3aca6880"? Well, it was the builder object in my fields_for. 




**The Solution**

After much Googling and Stack-Overflowing, I found the key to passing the WorkoutExercise object's id to the DELETE workout_exercises/:id action.

```
<%=link_to "Delete this Workout Exercise?", workout_exercise_path(we.object.id), method: :delete, :confirm => "You Sure?" %>
```

TLDR if you ever need to access the object of the fields_for, use `builder.object`
we was my builder, and the object we saw being passed earlier in my params above. But if I call we.object.id, I get the id for the WorkoutExercise I need to delete! 
