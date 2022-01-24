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

# Form
In reactjs, an input form element's value is controlled by React and this is called a “controlled component”. We use JavaScript function to handle the submission of the form and access the data that the user entered into the form. 
> for controlled component, we need to write an event handler for every change event and sometimes tedious. We might use uncontrolled component in some cases.
> 
> [Formik ](https://formik.org/) provides a complete solution including validation, keeping track of the visited fields, and handling form submission
> 用户在表单填入的内容，属于用户跟组件的互动，props 表示那些一旦定义，就不再改变的特性，而 state 是会随着用户互动而产生变化的特性。。所以表单的组件不能用props，只能用state

Basic logic of controlled component

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


# Reference