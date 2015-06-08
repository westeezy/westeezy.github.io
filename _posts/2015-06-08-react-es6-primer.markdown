---
layout: post
title: "React ES6 Primer"
excerpt: "Get your feet wet with React and es6 by building an input component."
modified:
categories:
tags: [react, javascript]
image:
  feature:
date: 2015-06-08T20:32:20-05:00
comments: true
---


With the recent surge of companies picking up React, I felt this was a good time to demonstrate how to leverage es6 while writing React components. The general idea is not complicated, but I feel it is nice to have a concise example in a easy to read format and thus supply a quick reference on how to get started.

### Basic Component

{% highlight js linenos %}
{% raw %}
'use strict';

import './_Input.scss'; //I prefer to style at the component level
import React from 'react'; //Pull in react through the module loader

class MyInput extends React.Component {

  constructor(...args) {
    super(...args);
    this.state ={}; //will explain state later but let's initialize it to empty for now.
  }

  render() {
    return (
      <div>
        <input />
      </div>
    );
  }
}

export default MyInput;

{% endraw %}
{% endhighlight %}

What's great about above is we can type into the input but what if we want to do something with it's value? For example let's try to wire up a tag that displays what you typed.  React Components have both states and props. State is internal to the component, while prop is something that is passed in. So in this situation it makes perfect sense to use set the input value on state.

{% highlight js linenos %}
{% raw %}
  render() {
    return (
      <div>
        <input value={this.state.inputValue}/>
        <div>{'Hello ' + this.state.inputValue}</div>
      </div>
    );
  }

{% endraw %}
{% endhighlight %}

No luck yet as now when you type in the input nothing happens. Well this is unfortunate. Let's take a second to think about why. React state is not being set when you type in the input only initially on first render. This component is not rerendering as nothing is calling render again.  
<br />
Luckily for us 1. React Components rerender on state or prop changes and 2. React supplies an onChange event.  
<br />
Note: you can not modify state inside of render, and as you can imagine if you could that would essentially be an infinite loop of rerendering. When updating state you use ```this.setState``` which is an asynchronous method on React components to update the components internal state (which is sealed so ```this.state.value = 'foo'``` will fail).

{% highlight js linenos %}
{% raw %}
class MyInput extends React.Component {

  constructor(...args) {
    super(...args);
    this.state ={};
  }

  updateValue(e) {
    this.setState({ //built in way for React to modify state.
      inputValue: e.target.value
    });
  }

  render() {
    return (
      <div>
        <input onChange={this.updateValue}
               value={this.state.inputValue.bind(this)}/>
        <div>{'Hello ' + this.state.inputValue}</div>
      </div>
    );
  }
}

export default MyInput;

{% endraw %}
{% endhighlight %}

Ugh... now we get `Hello undefined`. This is because unlike when writing React with es5 we need to bind out methods to the proper context and there are two ways to do that which I will show below.

{% highlight js linenos %}
{% raw %}
class MyInput extends React.Component {

  constructor(...args) {
    super(...args);
    this.state ={};
    //handling the context binding for each method in the constructor
    //this.updateValue.bind(this); //which I am not doing for this demo
  }

  updateValue(e) {
    this.setState({ //built in way for React to modify state.
      inputValue: e.target.value
    });
  }

  render() {
    return (
      <div>
        <input onChange={this.updateValue}
               value={this.state.inputValue.bind(this)}/>
        //binding context on use. also can use .call or .apply
        <div>{'Hello ' + this.state.inputValue.bind(this)}</div>
      </div>
    );
  }
}

export default MyInput;

{% endraw %}
{% endhighlight %}

Finally we have a working input. And for fun let's set an initial value.
<br />
When using es6 you can set state and props in the constructor rather than using `getInitialProps` and `setInitialState`  
<br  />

And here it is all together
-------------------
{% highlight js linenos %}
{% raw %}
'use strict';

import './_Input.scss';
import React from 'react';

class MyInput extends React.Component {

  constructor(...args) {
     super(...args);
     this.state = {
       inputValue: 'User'
     };
   }



   updateValue(e) {
     this.setState({ //built in way for React to modify state.
       inputValue: e.target.value
     });
   }

   render() {
     return (
       <div>
         <input onChange={this.updateValue.bind(this)}
                value={this.state.inputValue}/>
         <div>{'Hello' + this.state.inputValue}</div>
       </div>
     );
   }
}

export default MyInput;

{% endraw %}
{% endhighlight %}
