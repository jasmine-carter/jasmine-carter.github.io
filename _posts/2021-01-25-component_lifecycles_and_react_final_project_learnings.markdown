---
layout: post
title:      "Component Lifecycles & React Final Project Learnings"
date:       2021-01-26 02:05:54 +0000
permalink:  component_lifecycles_and_react_final_project_learnings
---


For my final project, I wanted to create an app for the user that has too many projects on their plate, and is morbidly curious about just how much money they're sinking into those projects. Having recently bought a house, I may be that user...

Users to be able to create Projects, which have many Materials and Comments.  See the schema below for a bit of a data roadmap.

```
ActiveRecord::Schema.define(version: 2021_01_24_181637) do

  create_table "comments", force: :cascade do |t|
    t.string "content"
    t.integer "project_id"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

  create_table "materials", force: :cascade do |t|
    t.string "name"
    t.integer "quantity"
    t.float "cost", default: 0.0
    t.boolean "purchased", default: false
    t.integer "project_id"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

  create_table "projects", force: :cascade do |t|
    t.string "name"
    t.float "cost", default: 0.0
    t.string "description"
    t.boolean "completed", default: false
    t.string "image"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

end
```

I knew that I wanted users to be able to be able to be able to do CRUD actions for Projects, Materials, and Comments, which in turn would inform my app's states and was is in turn rendered back to the user. As a user I can create my project, see maybe an inspirational photo of what I'm aiming to do, see the total cost of the project as I add more and more materials to it, and take notes about said project as I work on it. 

Here's a quick outline of the component hierarchy
```
App
- ProjectsContainer
	- ProjectInput
	- ProjectList
		- Project
			- MaterialsContainer
				- MaterialList
					- Material
				- MaterialInput
			- CommentsContainer
				- CommentList
					- Comment
				- CommentInput
```

My most important container component was my ProjectsContainer, which managed mapStateToProps to its state to pass on to child components which would be the ProjectList which passed props to our Project component. Project has two child components, MaterialsContainer and CommentsContainer, and passes them props in the form of a project object.

A challenge I ran into semi frequently, was that if I navigated from my ProjectsList component to an individual Project view, no problem! However, if I refreshed my page or app while on that show project page, I'd get an error indicating that the value of the prop being passed to MaterialsContainer and CommentsContainer, was undefined.  
TypeError: Cannot read property 'id' of undefined

Why?! 

Well, enter the component lifecycle, and an awesome learning opportunity to help cement some React and state concepts for me. 

Basically when the Project component intially rendered, the props it received was an empty array, which is the state that was intially set in my ProjectReducer. This then impacted this line of code, which determined the value of the props being passed to the child components
let project = this.props.projects.filter(project => project.id == this.props.match.params.id)[0]

<MaterialsContainer project={project}/>
<CommentsContainer project={project}/>

Because props, "project" was defined as an empty array after the initial render, when the above two components rendered, they did so with the props initially passed, which in turn would raise TypeErrors, where the property I'm trying to read, is being run against something that is yet to be defined. 

In order to get around this to avoid embarassment on my assessment when inevitably I'd refresh the page in front of the instructor, I added some logic to my CommentsContainer and MaterialsContainer to first check and see what the value of props.project was, before taking an action.

```
class CommentsContainer extends Component {

  
componentDidMount(){
    //console.log(this.props)
    if (this.props.project != undefined){
      this.props.fetchProjectComments(this.props.project.id)
    } else {
      console.log("no projects here yet")
    }
  }
```

and
```
const MaterialsList = (props) => {

function whatRenders(){
  if (materials == undefined || materials.length === 0) {
    return("There are no materials yet, add some below!")
  } else {
    const newMaterials = materials.map(material => {
      return(
        <div className="Material" key={material.id}><Material
        key={material.id}
        material = {material}
        deleteMaterial={props.deleteMaterial}
        updateMaterial={props.updateMaterial}
        />
        </div>
      )
    })
     return newMaterials

  }
}


  const {materials} = props

  //debugger;
  return (
    <div style={{background: "#ffdaae"}}>
    <div class="MaterialsList">
    {whatRenders()}
    </div>
    </div>
  )

}

export default MaterialsList;
```

This experience helped cement the concept of how a component initially renders when mounting, and how your initial state can make or break your app in that phase. Defensive coding can definitely save some heartache here. 

A tip that I found super helpful in tracing my props from component to component was actually adding logging that the component rendered as well as the props it received at that time.  Where things move so quickly with React, console.log() is incredibly helpful, as well as the ol' debugger;.

There's still so much to learn with React and Redux, and once I have time and space after this project, I look forward to continuing to play with React and Redux with personal projects. It's been an incredibly fun, challenging project and I look forward to working more with these tools. 





