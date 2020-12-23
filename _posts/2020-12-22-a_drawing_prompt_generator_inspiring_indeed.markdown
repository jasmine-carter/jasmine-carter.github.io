---
layout: post
title:      "A Drawing Prompt Generator: Inspiring Indeed"
date:       2020-12-23 00:26:41 +0000
permalink:  a_drawing_prompt_generator_inspiring_indeed
---


Going into the Javascript project, I was a bit intimidated. I can't say that I love Javascript but my goodness is it satisfying to be able to set up a backend API and have it talk to a basic frontend and actually see something a bit fancier than a basic html form get manipulated by your code!

My project at the start seemed simple. I wanted users to be able to go to my app, click a button, and get a drawing prompt and hopefully get inspired. I also wanted them to be able to contribute to the drawing prompt library by filling out a form with the attributes of a prompt, to then save that prompt to the database and return it to user. Lastly, I wanted them to get extra inspired, by seeing the work of past users. 

In order to allow for maximum flexibility in case I wanted to go back and add more funcitonality to this project, I broke out my models quite a bit. (I had considered eventually build out filters by way of Prompt attributes but that was a stetch and made my initial UI busier than I wanted)

```
Prompt
has_many :sentences
  has_many :nouns, through: :sentences
  has_many :verbs, through: :sentences
  has_many :adjectives, through: :sentences
  has_many :images
  belongs_to :style
```

```
Noun
  has_many :sentences
  has_many :prompts, through: :sentences
```

```
Verb
  has_many :sentences
  has_many :prompts, through: :sentences

```


```
Adjective
  has_many :sentences
  has_many :prompts, through: :sentences
  validates :value, uniqueness: true
```

```
Style
has_many :prompts
```

```
Image
belongs_to :prompt
```

```
Sentence
  belongs_to :prompt
  belongs_to :noun
  belongs_to :verb
  belongs_to :adjective

```

I know... it's a lot. 

From the user's point of view, a lot of this is obsifcated. Their form is much simpler. All they need to do is fill out a simple form to provide an Adjective, a Verb, a Noun, and a Style. So in the flow where they are submitting a form to create a new prompt, a fetch request is made, making a POST call to Prompts create. Their form finds or creates each attribute, and finally compiles a sentence, Mad Libs style, to generate a prompt. 

For the more adventurous doodler, the user can press a button to generate a random prompt. This action again sends a POST request to create, but rather than relying on user submitted params, instead we use a combination of the Faker gem, and a small hand crafted library of words that are randomly selected with some class methods, and returned to the userin the front end as a shiny new prompt. 

For the extra splash of inspiration, users can click a button to see a random assortment of all generated images, and the prompt that spawned them. This was pretty simple in that the buton click kicks off a fetch request to the Images controller and the index action. From there, the controller returns 4 random images from all available images, to be displayed to the user in the same page. Once the user has basked in the beauty returned to them long enough, they can simply click another button to close out these images and see their old prompt again. What that "close" button click does is basically manipulate the DOM to delete the child elements these images were stored in and restore display visibility to the divs that create the "main" view. This deletion of child elements allows for users to keep on clicking to see another batch of random images without cluttering up the ui with the ghosts (divs) of images past.


As someone with a background in the arts and someone who deals with their fair share of inspiration slumps, it was really cool to work on a project that spanned both of my worlds: my old art background, and my technical future!



