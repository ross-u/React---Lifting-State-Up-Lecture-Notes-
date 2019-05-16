# React | Lifting State Up



## Introduction

We’ve learnt how to **pass data from a parent component to a child component using props**. 

We’ve also learnt how to **change the data within the same component using state**. 

**To summarize**: *state belongs to the component itself and props are used to pass data to child components*


<br>


## Lifting State Up



Often, several components need to reflect the same changing data.  

Moving the state to the closes common ancestor component is called **lifting the state up** . 

<br>

### Getting Started



#### [Starter code repo](<https://github.com/ross-u/React---Lifting-State-Starter-Repo-.git>)

```bash
# Clone the repository with the starter code
git clone https://github.com/ross-u/React---Lifting-State-Starter-Repo-.git

cd React---Lifting-State-Starter-Repo-/


# install the dependencies
npm install

# Start the development server
npm start
```

<br>

### To Do List app


<br>

In our current example  we have `<ToDoList>` component which renders other components:  `<Summary>` and a list of  `<Task>` components. 

<br>



![img](https://github.com/ross-u/React---Lifting-State-Up-Lecture-Notes-/blob/master/to%20do%20list.png)


<br>


Each `<Task>` card component is a `class` component and has a `state` that holds `taskCompleted` value.



<br>


![img](
https://github.com/ross-u/React---Lifting-State-Up-Lecture-Notes-/blob/master/lifting%20state%20up%2001.png)



In `<Summary>` component which is hard coded at the moment,  we want to display how many *tasks is completed* and to update that number whenever we click green button in any of the `<Task>` components.

In order to do that we have to **lift the state** from   `<Task>` components to the closest common ancestor (`<ToDoList>`), so that both `<Summary>` and all `<Task>` components can access the same value of how many tasks is completed, as they both depend on this value.



<br>

First we will remove `state` from `<Task>` component and instead create a new property in the `state` of  `<ToDoList>` which is the most common ancestor of `<Task>` and `<Summary>`.

 **This is lifting of the `state` to the closest common ancestor.**



![img](
https://github.com/ross-u/React---Lifting-State-Up-Lecture-Notes-/blob/master/lifting%20state%20up%2002.png)







**components/Task.js**

```js
//		/components/Task.js

	import React from 'react';

	class Task extends React.Component {
//   state = {
//     taskCompleted: false
//   }
  
//	    ⇡         ⇡ 						
//   Delete `state` from `Task.js`
```





#### Create new property in `ToDoList` state



**components/ToDoList.js**

```js
//		/components/ToDoList.js

class ToDoList extends Component {
  constructor() {
    super();
    this.state = {
      tasks: data,
      tasksCompleted: 0	 	//	  ⟻  create new property
    };
```





After lifting the state to `ToDoList.js` we now need to create all the methods needed for this functionality (updating `tasksCompleted`) and then pass props that we want to use in `<Task>` and `<Summary>` .

<br>

We will have to update all 3 components to have the new lifted state properly connected and functioning.



![img](
https://github.com/ross-u/React---Lifting-State-Up-Lecture-Notes-/blob/master/lifting%20state%20up%2003.png)



#### Create methods to update the `tasksCompleted`



**components/TodoList.js**

```js
//		/components/ToDoList.js

class ToDoList extends Component {
  constructor() {
    super();
    this.state = {
      tasks: data,
      tasksCompleted: 0
    };
    
    this.deleteTaskById = this.deleteTaskById.bind(this);   
    
    this.toggleTaskDone = this.toggleTaskDone.bind(this);
    
//	 ↥  `bind` new method toggleTaskDone    ↥
  };
  
  
//   ⤥    create method to update number of tasksCompleted   ⤦
    toggleTaskDone(id) {
    const tasksCopy = [...this.state.tasks];
    let tasksCompleted = this.state.tasksCompleted;

//	 Find selected task, update that task's `isDone` property,
//	 and update `tasksCompleted` value in the shared `state`
    tasksCopy.forEach((oneTask) => {
      if(oneTask.id === id) {
        oneTask.isDone = oneTask.isDone ? false : true;
        if (oneTask.isDone) tasksCompleted++;
        else if (!oneTask.isDone) tasksCompleted--;        
      }
    })
    this.setState( {tasks: tasksCopy, tasksCompleted } );
  }
```


<br>





#### Pass method `toggleTaskDone` to the `<Task>` and tie it to the button



**components/ToDoList.js**

```jsx
//		/components/ToDoList.js

render() {
	...
			...
          this.state.tasks.map( (task) => {
            return <Task key={task.id} 
                     {...task}
                     deleteTask={ this.deleteTaskById }
                     updateTaskStatus={ this.toggleTaskDone } />
// 	 	   ⤤       PASS METHOD AS PROP		⤣

     ...
  ...
```



<br>



#### Update `Task.js` and remove method `toggleTask` and 

#### replace `this.state.taskCompleted` with `this.props.isDone`





**components/Task.js**

```jsx
//		/components/Task.js


render() {
  return (
    <div className='task-card'>
      <div className='task-card-half'>
        <h1>{this.props.name}</h1>
        {
          this.props.isDone ?	     // 	 ⟻	UPDATE THE CONDITION HERE
            <h3 style={{color: 'green'}}>DONE ✅</h3>
            :
            <h3 style={{color: 'red'}}>PENDING</h3>
        }
      </div>
      
      ...
      
      ...
      
  <button className='add' 
    onClick={()=> this.props.updateTaskStatus(this.props.id) }>
{/* 	        	⤤      PASS METHOD AS PROP 	⤣		*/}
    {
        this.props.isDone ?	// 	 ⟻	UPDATE THE CONDITION HERE
        <span>UNDO ❌</span>
        :
        <span>✅</span>
      }
      </button>
```


<br>



#### Pass the data from the shared `state` of `ToDoList` to `<Summary>`



**components/ToDoList.js**

```jsx
  render() {
    return (
      <div>
        <Summary tasksCompleted={this.state.tasksCompleted}/>
        {/*	⤤      PASS DATA AS A PROP      ⤣        */}
```


<br>



#### Update `Summary.js` to show the data passed through `props`



**components/Summary.js**

```jsx
import React from 'react'

function summary(props) {

  return (
    <div>
      <h1>TASKS COMPLETED:</h1>
      <p className="tasks-completed">{props.tasksCompleted}</p>
{/*				        ⤤   HERE    ⤣	       */}
    </div>
  )
}

export default summary;

```


<br>



We have a minor bug. Our Tasks Completed don't have a check that prevent the `tasksCompleted` being a negative number when we delete a task and tasks completed are 0.

#### Update `deleteTaskById` in `ToDoList.js` to prevent subtracting from `tasksCompleted` when Task is deleted and count is 0.



**components/ToDoList.js**

```jsx
//		components/ToDoList.js

deleteTaskById (id)  {
  const taskListCopy = [...this.state.tasks];
  let tasksCompleted = this.state.tasksCompleted;
``
  taskListCopy.forEach((taskObj, index) => {
    if(taskObj.id === id) {
      taskListCopy.splice(index, 1);
      if (tasksCompleted > 0) tasksCompleted--;	// 	⟻	UPDATE
    }
  })
  this.setState( {tasks: taskListCopy, tasksCompleted} );
};
```


<br>



### [Lecture Code Example - Done](<https://github.com/ross-u/React---Lifting-State-Done->)
