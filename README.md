## Minimalistic and Extensible Data >< DOM Binding library

- dx.js is a minimalistic and extensible library to allow to push and pull data into and from a DOM structure. 
- It uses the css class "dx" to mark the elements that must be "pushed/pulled" (class name is one of the fastest way to query a DOM structure, so, we use this as a first filter).
- You can define your *property path* as *data-dx="job.title"* or in the class name as *class="dx dx-job-title"* (*dx-* prefixed and *-* rather than *.* when in class name), or in the name attribute of an input (*name="job.title"*)

*(Still experimental: While we use this library in some of our production code, the API and functionalities might change overtime. Obviously, feel free to use it as is, or make it your own. Feedback always welcome.)*

So, if you have an HTML structure (already in your DOM) like:

```html
<!-- is css class name, property path is prefixed by 'dx-' and dot replaced by '-' -->
<div class="dx dx-name"></div>
<!-- or can use the data-dx to put the exiplity title -->
<div class="dx" data-dx="job.title"></div> 
<!-- or can use the name attriblte as well (for input elements) -->
<input class="dx" name="job.title" />
```

You call something like that: 

```js
$("body").dxPush({name:"John Smith",{job:{title:"Senior Developer"}}});
```

You get something like this: 

```html
<!-- is css class name, property path is prefixed by 'dx-' and dot replaced by '-' -->
<div class="dx dx-name">John Smith</div> 
<!-- or can use the data-dx to put the exiplity title -->
<div class="dx" data-dx="job.title">Senior Developer</div> 
<!-- or can use the name attriblte as well (for input elements) -->
<input class="dx" name="job.title" value="Principal Developer" />
```

And if you got HTML like above, you can do a ```dxPull()``` to get the data back.
```js
var data = $("body").dxPull();
// data == {name:"John Smith",{job:{title:"Principal Developer"}}} (for now, last one wins, later, input will win)
```

And best of all, you can add your own ```pusher``` or ```pullers``` based on jquery selector. 

Pushers are used when we call a *$element.dxPush(data)*, and will be called only if the *selector* (here *input.custom*) will match the $dx.is(selector) (so, you are free to use anything you would in a jquery.is). *value* is the value node that match the path 
```js
// any "input" tags with tha class "custom"
dx.addPusher("input.custom", function(value){
  // here "this" is the jQuery object of the html element that have the .dx
  this.val("custom: " + value);
  this.css("border","solid 1px green");
});
```

Similarely, you can use the *$element.dxPull()* to extract data from the DOM.
```js
// any "input" tags with tha class custom 
dx.addPuller("input.custom", function(existingValue){
  // Here existingValue is the value that might exists already in the data at this property path.
  // This way, you can decide what you want to do if a value has already been found for this property path. 
  var newValue = this.val();
  // need to remove the "custom: "
  if (newValue.indexOf("custom: ") === 0){
    newValue = newValue.substring("custom: ".length);
  }
  return newValue;
});
```

The pushers and pullers are taking as LIFO (last in, first out), so, make sure to add our more specifics one last. 


