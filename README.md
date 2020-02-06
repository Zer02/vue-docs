# Vue Documentation Tutorial

## Note on Setup: 
If you have an existing project and would like to keep documentation inside the project, you should install VuePress as a local dependency. This setup also allows you to use CI or services like Netlify for automatic deployment on push.
```bash
# Add vuepress into an existing project
yarn add -D vuepress

# Run vuepress
yarn docs:dev
```

## Introduction

### What is Vue.js?
Vue is a **progressive framework** for building user interfaces. It is designed from the ground up to be incrementally adoptable. The core library is focused on the view layer only and is easy to pick up and integrate with other libraries or existing projects. 

Vue is also perfectly capable of powering sophisticated Single-Page Applications when used in combination with modern tooling and supporting libraries.

### Declarative Rendering
At the core of Vue.js is a system that enables us to declaratively render data to the DOM using straightforward template syntax:

```html
<div id="app">
  <p>{{ message }}</p>
</div>
```

```js
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

### Directives
`Directives` are attributes added to your HTML files as attributes. They are prefixed with `v-` to indicate that they are special attributes provided by Vue, and as you may have guessed, they apply special reactive behavior to the rendered DOM.


### Conditionals and Loops

```html
<div id="app-3">
  <span v-if="seen">Now you see me</span>
</div>
```

```js
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```

If you enter `app3.seen = false` in the console, you should see the message disappear.

This example demonstrates that we can bind data to not only text and attributes, but also the **structure** of the DOM. Moreover, Vue also provides a powerful transition effect system that can automatically apply *transition effects* when elements are inserted/updated/removed by Vue.

There are other directives, each with their own special functionality. For example, the `v-for` directive can be used for diaplaying a list of items using the data from an Array:

```html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```

```js
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Learn JavaScript' },
      { text: 'Learn Vue' },
      { text: 'Build something awesome' }
    ]
  }
})
```

Enter `app4.todos.push({ text: 'New item' })`. You should see a new item appended to the list.

### Handling User Input
To let users interact with your app, we can use the `v-on` directive to attach event listeners that invoke methods on our Vue instances:

```html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>
```

```js
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
}
```

Note that in this method, we update the state of our app without touching the DOM - all DOM manipulations are handled by Vue, and the code you write is focused on the underlying logic.

Vue also provides the `v-model` directive that makes two-way binding between form input and app state a breeze:

```html
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```

```js
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```

### Composing with Components
The component system is another important concept in Vue. It's an abstraction that allows us to build large-scale apps composed of small, self-contained, reusable components. Almost any type of application interface can be abstracted into a tree of components.

In Vue, a component is essentially a Vue instance with pre-defined options. Registering a component in Vue is straightforward:

```js
// Define a new component called todo-item
Vue.component('todo-item', {
  template: '<li>This is a todo</li>'
})
```

Now you can compose it in another component's template:

```html
<ol>
  <!-- Create an instance of the todo-item component -->
  <todo-item></todo-item>
</ol>
```

But this would render the same text fot every todo. We should be able to pass data from the parent scope into child components. Let's modify the component definition to make it accept a `prop`:

```js
Vue.component('todo-item', {
  // The todo-item component now accepts a
  // "prop", which is like a custom attribute.
  // This prop is called todo.
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

Then we can pass the todo into each repeated component using `v-bind`:

```html
<div id="app-7">
  <ol>
    <!--
      Now we provide each todo-item with the todo object
      it's representing, so that its content can be dynamic.
      We also need to provide each component with a "key",
      which will be explained later.
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id"
    ></todo-item>
  </ol>
</div>
```

```js
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { id: 0, text: 'Vegetables' },
      { id: 1, text: 'Cheese' },
      { id: 2, text: 'Whatever else humans are supposed to eat' }
    ]
  }
})
```

We have managed to separate our app into two smaller units, and the child is reasonably well-decoupled from the parent via the props interface. We can now further improve our `<todo-item>` component with more complex template and logic without affecting the parent app.

In a large app, it is necessary to divide the whole thing into components to make development manageable. Here's an imaginary sample of what that might look like.

```html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

## The Vue Instance

### Creating a Vue Instance
Every Vue function starts by creating a new Vue instance with the Vue function:

```js
var vm = new Vue({
  // options
})
```
As a convention, we often use the variable `vm` (short for ViewModel) to refer to our Vue instance.

When you create a Vue instance, you pass in an **options object**. The majority of this guide describes how you can use these options to create your desired behavior.

### Data and Methods
When a Vue instance is created, it adds all the properties found in its `data` object to Vue's **reactivity system**. When the values of those properties change, the view will "react", updating to match the new values.

```js
// Our data object
var data = { a: 1 }

// The object is added to a Vue instance
var vm = new Vue({
  data: data
})

// Getting the property on the instance
// returns the one from the original data
vm.a == data.a // => true

// Setting the property on the instance
// also affects the original data
vm.a = 2
data.a // => 2

// ... and vice-versa
data.a = 3
vm.a // => 3
```

When this data changes, the view will re-render. Properties in `data` are onlt reactive if they existed when the instance was created. If you know you'll need a property later, but it starts out empty or non-existent, you'll need to set some initial value. 

The only exception to this is the use of `Object.freeze()`, which prevents existing properties from being changed, which also means the reactivity system can't track changes.

In addition to data properties, Vue instances expose a number of useful instance properties and methods. These are prefixed with `$` to differentiate them from user-defined properties. For example:

```js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch is an instance method
vm.$watch('a', function (newValue, oldValue) {
  // This callback will be called when `vm.a` changes
})
```

## Template Syntax
Vue.js uses an HTML-based template syntax that allows you to declaritively bind the rendered DOM to the underlying Vue instance's data. All Vue.js templates are valid HTML that can be parsed by spec-compliant browsers and HTML parsers.

Under the hood, Vue compiles the templates into Virtual DOM render functions. Combined with the reactivity system, Vue is able to intelligently figure out the minimal amount of components to re-render and apply the minimal amount of DOM manipulations when the app state changes.

### Interpolations

#### Text
The most basic form of data binding is text interpolation using the "Mustache" syntax:

```html
<span>Message: {{ msg }}</span>
```

The mustache tag will be replaced with the value of the `msg` property on the corresponding data object. It will also be updated whenever the data object's `msg` property changes.

You can also perform one-time interpolations that do not update on data change by using the `v-once` directive, but keep in mind this will also affect any other bindings on the same node:

```html
<span v-once>This will never change: {{ msg }}</span>
```

#### Raw HTML
The double mustaches interpret the data as plain text, not HTML. To output real HTML, you will need the `v-html` directive:

```html
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

#### Attributes
Mustaches cannot be used inside HTML attributes. Instead, use a `v-bind` directive:

```html
<div v-bind:id="dynamicId"></div>
```

In the case of boolean attributes, where their mere existence implies `true`, `v-bind` works a little differently. In this example:

```html
<div v-bind:disabled="isButtonDisabled">Button</button>
```

If `isButtonDisabled` has the value of `null`, `undefined`, or `false`, the `disabled` attribute will not even be included in the rendered `<button>` element.

#### Using JavaScript Expressions
Vue supports the full power of JavaScript expressions inside all data bindings:

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

These expressions will be evaluated as JavaScript in the data scope of the owner Vue instance. One restriction is that each binding can only contain one single expression, so the following will NOT work:

```html
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```

### Directives
Directives are special attributes with the  `v-` prefix. Directive attribute values are expected to be a single JavaScript expression (with the exception of `v-for`). A directive's job is to reactively apply side effects to the DOM when the value of its expression changes. Let's review the example we saw in the intro:

```html
<p v-if="seen">Now you see me</p>
```

Here, the `v-if` directive would remove/insert the `<p>` element based on the truthiness of the value of the expression `seen`.

#### Arguments
Some directives can take an "argument", denoted by a colon after the directive name. For example, the `v-bind` directive is used to reactively update an HTML attribute:

```html
<a v-bind:href="url">...</a>
```

Here, `href` is the argument, which tells the `v-bind` directive to bind the element's `href` attribute to the value of the expression `url`.

Another example is the `v-on` directive, which listens to DOM events. In this example, the argument is the event name we listen to:

```html
<a v-on:click="doSomething"> ... </a>
```

#### Dynamic Arguments
Starting in version 2.6.0, it is also possible to use a JavaScript expression in a directive argument by wrapping it with square brackets:

```html
<a v-bind:[attributeName]="url"> ... </a>
```

Here, `attributeName` will be dynamically evaluated as a JavaScript expression, and its evaluated value will be used as the final value for the argument. For example, if your Vue instance has a data property, `attributeName`, whose value is `"href"`, then this binding will be equivalent to `v-bind:href`.

Similarly, you can use dynamic arguments to bind a handler to a dynamic event name:

```html
<a v-on:[eventName]="doSomething"> ... </a>
```

Similarly, when `eventName`'s value is `"focus"`, for example, `v-on:[eventName]` will be equivalent to `v-on:focus`.

##### Dynamic Argument Value Constraints
Dynamic arguments are expected to evaluate to a string, with the exception of `null`. The special value `null` can be used to explicitly remove the binding. ANy other non-string value will trigger a warning.

##### Dynamic Argument Expression Contstraints
Dynamic argument expressions have some syntax constraints because certain characters are invalid inside HTML attribute names, such as spaces and quotes.

For example, the following is invalid:

```html
<!-- This will trigger a compiler warning. -->
<a v-bind:['foo' + bar]="value"> ... </a>
```

The workaround is to either use expressions without spaces or quotes, or replace the complex expression with a computed property.

---

#### Modifiers
Modifiers are special postfixes denoted by a dot, which indicate that a directive should be bound in some special way. For example, the `.prevent` modifier tells the `v-on` directive to call `event.preventDefault()` on the triggered event:

```html
<form v-on:submit.prevent="onSubmit"> ... </form>
```
You’ll see other examples of modifiers later, for `v-on` and for `v-model`, when we explore those features.

### Shorthands
The `v-` prefix serves as a visual cue for identifying Vue-specific attributes in your templates. This is useful when you are using Vue.js to apply dynamic behavior to some existing markup, but can feel verbose for some frequently used directives. At the same time, the need for the `v-` prefix becomes less important when you are building a SPA, where Vue manages every template. Therefore, Vue provides special shorthands for two of the most often used directives, `v-bind` and `v-on`:

#### `v-bind` shorthand
```html
<!-- full syntax -->
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>

<!-- shorthand with dynamic argument (2.6.0+) -->
<a :[key]="url"> ... </a>
```

#### `v-on` shorthand
```html
<!-- full syntax -->
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>

<!-- shorthand with dynamic argument (2.6.0+) -->
<a @[event]="doSomething"> ... </a>
```

They may look a bit different from normal HTML, but `:` and `@` are valid characters for attribute names and all Vue-supported browsers can parse it correctly. In addition, they do not appear in the final rendered markup. The shorthand syntax is totally optional, but you will likely appreciate it when you learn more about its usage later.


## Computed Properties and Watchers

### Computed Properties
In-template expressions are convenient, but they are meant for simple operations. Putting too much logic in your templates can make them bloated and hard to maintain. That's why for any complex logic, you should use a computed property.

#### Basic Example

```html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
```

```js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // a computed getter
    reversedMessage: function () {
      // `this` points to the vm instance
      return this.message.split('').reverse().join('')
    }
  }
})
```

Here we have declared a computed property `reversedMessage`. The function we provided will be used as the getter function for the property `vm.reversedMessage`: 
```js
console.log(vm.reversedMessage) // => 'olleH'
vm.message = 'Goodbye'
console.log(vm.reversedMessage) // => 'eybdooG'
```
You can data-bind to computed properties in templates just like a normal property. Vue is aware that `vm.reversedMessage` depends on `vm.message`, so it will update any bindings that depend on `vm.reversedMessage` when `vm.message` changes. And the best part is that we've created this dependency relationship declaratively: the computed getter function has no side effects, which makes is easier to test and understand.

#### Computed Caching vs Methods
Instead of a computed property, we can define the same function as a method. The end result is the same, but **computed properties are cached based on their reactive dependencies.** A computed property will only re-evaluate when some of its reactive dependencies have changed. This means as long as `message` has not changed, multiple access to the `reversedMessage` computed property will immediately return the previously computed result without having to run the function again.

This also means the following computed property will never update, because `Date.now()` is not a reactive dependency:

```js
computed: {
  now: function () {
    return Date.now()
  }
}
```

In comparison, a method invocation will **always** run the function whenever a re-render happens.

Why do we need caching? Imagine we have an expensive computed property **A**, which requires looping through a huge Array and doing a lot of computations. Then we may have other computed properties that in turn depend on **A**. Without caching, we would be executing **A**’s getter many more times than necessary! In cases where you do not want caching, use a method instead.

#### Computed vs Watched Property
Vue does provide a more generic way to observe and react to data changes on a Vue instance: **watch properties**. When you have some data that needs to change based on some other data, it is temptting to overuse `watch`, especially if you are coming from an AngularJS background. However, it is often a better idea to use a computed property rather than an imperative `watch` callback. Consider this example:

```html
<div id="demo">{{ fullName }}</div>
```

```js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

The above code is imperative and repetitive. Compare it with a computed property version:

```js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

#### Computed Setter
Computed properties are by default getter-only, but you can also provide a setter when you need it:

```js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

Now when you run `vm.fullName = 'John Doe'`, the setter will be invoked and `vm.firstName` and `vm.lastName` will be updated successfully.

### Watchers

While computed properties are more appropriate in most cases, there are times when a custom watcher is necessary. That's why Vue provides a more generic way to react to data changes through the `watch` option. This is most useful when you want to perform asynchronous or expensive operations in response to changing data.

For example:
```html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
```

```html
<!-- Since there is already a rich ecosystem of ajax libraries    -->
<!-- and collections of general-purpose utility methods, Vue core -->
<!-- is able to remain small by not reinventing them. This also   -->
<!-- gives you the freedom to use what you're familiar with.      -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // whenever question changes, this function will run
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // _.debounce is a function provided by lodash to limit how
    // often a particularly expensive operation can be run.
    // In this case, we want to limit how often we access
    // yesno.wtf/api, waiting until the user has completely
    // finished typing before making the ajax request. To learn
    // more about the _.debounce function (and its cousin
    // _.throttle), visit: https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
</script>
```

In this case, using the `watch` option allows us to perform an async operation (accessing an API), limit how often we perform that operation, and set intermediary states until we get a final answer. None of that would be possible with a computed property.

In addition to the `watch` option, you can also use the imperative `vm.$watch` API.


## Class and Style Bindings
A common need for data binding is manipulating an element's class list and its inline styles. Since they are both attributes, we can use `v-bind` to handle them. However, meddling with a string concatenation is annoying and error-prone. For this reason, Vue provides special enhancements when `v-bind` is used with `class` and `style`. In addition to strings, the expressions can also evaluate to objects and arrays.

### Binding HTML Classes

#### Object Syntax
We can pass an object to `v-bind:class` to dynamically toggle classes:

```html
<div v-bind:class="{ active: isActive }"></div>
```

The above syntax means the presence of the `active` class will be determined by the **truthiness** of the data property `isActive`.

You can have multiple classes toggled by having more fields in the object. In addition, the `v-bind:class` directive can also co-exist with the plain `class` attribute. So given the following template:
```html
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

And the following data:
```js
data: {
  isActive: true,
  hasError: false
}
```

It will render:
```html
<div v-bind:class="classObject"></div>
```

When `isActive` or `hasError` changes, the class list will be updated accordingly. For example, if `hasError` becomes `true`, the class list will become `"static active text-danger"`.

The bound object doesn't have to be inline:
```html
<div v-bind:class="classObject"></div>
```
```js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

This will render the same result. We can also bing to a **computed property** that returns an object. This is a common and powerful pattern:
```html
<div v-bind:class="classObject"></div>
```
```js
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

#### Array Syntax
We can pass an array to `v-bind:class` to apply a list of classes:

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```
```js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

Which will render:

```html
<div class="active text-danger"></div>
```

If you would like to also toggle a class in the list conditionally, you can do it with a ternary expression:

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

This will always apply `errorClass`, but will only apply `activeClass` when `isActive` is truthy.

However, this can be a bit verbose if you have multiple conditional classes. That's why it's also possible to use the object syntax inside array syntax:

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

#### With Components
When you use the `class` attribute on a custom component, those classes will be added to the component's root element. Existing classes on this element will not be overwritten.

For example, if you declare this component:

```js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

Then add some classes when using it:
```html
<my-component class="baz boo"></my-component>
```

The rendered HTML will be:
```html
<p class="foo bar baz boo">Hi</p>
```

The same is true for class bindings:
```html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

When `isActive` is truthy, the rendered HTML will be:
```html
<p class="foo bar active">Hi</p>
```


## Components Basics

### Base Example

```js
// Define a new component called button-counter
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```

Components are reusable Vue instances with a name: in this case, `<button-counter>`. We can use this component as a custom element inside a root Vue instance created with `new Vue`:

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```
```js
new Vue({ el: '#components-demo' })
```

Since components are reusable Vue instances, they accept the same options as `new Vue`, such as `data`, `computed`, `watch`, `methods`, and lifecycle hooks. The only exceptions are a few root-specific options like `el`.

### Reusing Components
Components can be reused as many times as you want:

```html
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

Notice that when clicking on the buttons, each one maintains its own, separate `count`. That's because each time you use a component, a new **instance** of it is created.

#### `data` Must Be a Function

When we defined the `<button-counter>` component, you may have noticed that `data` wasn't directly provided an object, like this:

```js
data: {
  count: 0
}
```

Instead, **a component's `data` option must be a function**, so that each instance can maintain an independent copy of the returned data object:

```js
data: function () {
  return {
    count: 0
  }
}
```

If Vue didn't have this rule, clicking on one button would affect the data of *all other instances*.


### Organizing Components
