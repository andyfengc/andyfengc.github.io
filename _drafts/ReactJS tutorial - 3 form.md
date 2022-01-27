---
layout: post
title: ReactJS tutorial - form
author: Andy Feng
---

# Event
- React event is a wrapper of Javascript event
- React events are named using camelCase, rather than lowercase. 
- usually event handler is a method of component class

HTML

	<button onclick="activateLasers()">
	  Activate Lasers
	</button>

is equivalent to JSX

	<button onClick={activateLasers}>
	  Activate Lasers
	</button>

demo1:

	function Form() {
	  function handleSubmit(e) {
	    e.preventDefault();
	    console.log('You clicked submit.');
	  }
	
	  return (
	    <form onSubmit={handleSubmit}>
	      <button type="submit">Submit</button>
	    </form>
	  );
	}

- because Javascript callback issue, usually, we have to bind event handler method to `this` and pass it to the event handler name in the constructor

demo2:

	class Toggle extends React.Component {
	  constructor(props) {
	    super(props);
	    this.state = {isToggleOn: true};
	
	    // This binding is necessary to make `this` work in the callback
	    this.handleClick = this.handleClick.bind(this);
	  }
	
	  handleClick() {
	    this.setState(prevState => ({
	      isToggleOn: !prevState.isToggleOn
	    }));
	  }
	
	  render() {
	    return (
	      <button onClick={this.handleClick}>
	        {this.state.isToggleOn ? 'ON' : 'OFF'}
	      </button>
	    );
	  }
	}
	
	ReactDOM.render(
	  <Toggle />,
	  document.getElementById('root')
	);

React event handlers are written inside curly braces:

	onClick={shoot}  instead of onClick="shoot()".

React:

	<button onClick={shoot}>Take the Shot!</button>

HTML:

	<button onclick="shoot()">Take the Shot!</button>

## Passing Arguments
To pass an argument to an event handler, use an arrow function.

Example: Send "Goal!" as a parameter to the shoot function, using arrow function:
	
	function Football() {
	  const shoot = (a) => {
	    alert(a);
	  }
	
	  return (
	    <button onClick={() => shoot("Goal!")}>Take the shot!</button>
	  );
	}
	
	ReactDOM.render(<Football />, document.getElementById('root'));

## React Event Object
Event handlers have access to the React event that triggered the function.

e.g.

	<button onClick={(event) => shoot("Goal!", event)}>Take the shot!</button>

# Form
In reactjs, an input form element's value is controlled by React and this is called a “controlled component”. We use JavaScript function to handle the submission of the form and access the data that the user entered into the form. 
> for controlled component, we need to write an event handler for every change event and sometimes tedious. We might use uncontrolled component in some cases.
> 
> [Formik ](https://formik.org/) provides a complete solution including validation, keeping track of the visited fields, and handling form submission
> 用户在表单填入的内容，属于用户跟组件的互动，props 表示那些一旦定义，就不再改变的特性，而 state 是会随着用户互动而产生变化的特性。。所以表单的组件不能用props，只能用state

## Create a form
Basic logic of form in class component

1. input element bind to an event handler
2. event handler call `setState()` to change state
3. input element display state value via `value` attribute. e.g. `<input type="text" value="{this.state.value}"/>`

e.g. 

	class NameForm extends React.Component {
	  constructor(props) {
	    super(props);
	    this.state = {value: ''};
		// bind
	    this.handleChange = this.handleChange.bind(this);
	    this.handleSubmit = this.handleSubmit.bind(this);
	  }
	
	  handleChange(event) {
	    this.setState({value: event.target.value});
	  }
	
	  handleSubmit(event) {
	    alert('A name was submitted: ' + this.state.value);
	    event.preventDefault();
	  }
	
	  render() {
	    return (
	      <form onSubmit={this.handleSubmit}>
	        <label>
	          for input:
	          <input type="text" value={this.state.value} onChange={this.handleChange} />

			  for textarea:
			  <textarea value={this.state.value} onChange={this.handleChange} />

	          for select:
	          <select value={this.state.value} onChange={this.handleChange}>
	            <option value="grapefruit">Grapefruit</option>
	            <option value="lime">Lime</option>
	            ...
	          </select>
	        </label>
	        <input type="submit" value="Submit" />
	      </form>
	    );
	  }
	}


For function component, We can use the `useState` Hook to keep track of each inputs value and provide a "single source of truth" for the entire application.

e.g.

	import { useState } from "react";
	import ReactDOM from 'react-dom';
	
	function MyForm() {
	  const [name, setName] = useState("");
	
	  return (
	    <form>
	      <label>Enter your name:
	        <input
	          type="text" 
	          value={name}
	          onChange={(e) => setName(e.target.value)}
	        />
	      </label>
	    </form>
	  )
	}
	
	ReactDOM.render(<MyForm />, document.getElementById('root'));

## Submit form
adding an event handler in the `onSubmit` attribute for the <form>

	import { useState } from "react";
	import ReactDOM from 'react-dom';
	
	function MyForm() {
	  const [name, setName] = useState("");
	
	  const handleSubmit = (event) => {
	    event.preventDefault();
	    alert(`The name you entered was: ${name}`)
	  }
	
	  return (
	    <form onSubmit={handleSubmit}>
	      <label>Enter your name:
	        <input 
	          type="text" 
	          value={name}
	          onChange={(e) => setName(e.target.value)}
	        />
	      </label>
	      <input type="submit" />
	    </form>
	  )
	}
	ReactDOM.render(<MyForm />, document.getElementById('root'));

## Input fields
We can control the values of more than one input field by adding a `name` attribute to each element.

To access the fields in the event handler use the `event.target.name` and `event.target.value`.

To update the state, use square brackets [bracket notation] around the property name.

### Input
e.g. 

	import { useState } from "react";
	import ReactDOM from "react-dom";
	
	function MyForm() {
	  const [inputs, setInputs] = useState({});
	  // a version of generic event handler
	  const handleChange = (event) => {
	    const name = event.target.name;
	    const value = event.target.value;
	    setInputs(values => ({...values, [name]: value}))
	  }
	
	  const handleSubmit = (event) => {
	    event.preventDefault();
	    alert(inputs);
	  }
	
	  return (
	    <form onSubmit={handleSubmit}>
	      <label>Enter your name:
	      <input 
	        type="text" 
	        name="username" 
	        value={inputs.username || ""} 
	        onChange={handleChange}
	      />
	      </label>
	      <label>Enter your age:
	        <input 
	          type="number" 
	          name="age" 
	          value={inputs.age || ""} 
	          onChange={handleChange}
	        />
	        </label>
	        <input type="submit" />
	    </form>
	  )
	}

### Text fields
html

	<textarea>
	  Content of the textarea.
	</textarea>

react

	import { useState } from "react";
	import ReactDOM from "react-dom";
	
	function MyForm() {
	  const [textarea, setTextarea] = useState(
	    "The content of a textarea goes in the value attribute"
	  );
	
	  const handleChange = (event) => {
	    setTextarea(event.target.value)
	  }
	
	  return (
	    <form>
	      <textarea value={textarea} onChange={handleChange} />
	    </form>
	  )
	}
	
	ReactDOM.render(<MyForm />, document.getElementById('root'));
### Select
HTML

	<select>
	  <option value="Ford">Ford</option>
	  <option value="Volvo" selected>Volvo</option>
	  <option value="Fiat">Fiat</option>
	</select>
> the selected value in the drop down list was defined with the `selected` attribute:

react

	function MyForm() {
	  const [myCar, setMyCar] = useState("Volvo");
	
	  const handleChange = (event) => {
	    setMyCar(event.target.value)
	  }
	
	  return (
	    <form>
	      <select value={myCar} onChange={handleChange}>
	        <option value="Ford">Ford</option>
	        <option value="Volvo">Volvo</option>
	        <option value="Fiat">Fiat</option>
	      </select>
	    </form>
	  )
	}
> the selected value is defined with a `value` attribute on the select tag


# Reference