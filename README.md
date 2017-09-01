# Reading-notes-for-Secrets-of-the-JavaScript-Ninja-Second-Edition

- P65. `arguments` object (it's an object with `length` property, not an array) is an alias to function parameters. For example, if we set a new value to `arguments[0]`, the value of the first parameter will also be changed. This feature (`arguments` aliasing) is disabled in strict mode.

      function infiltrate(person) {
        assert(person === 'gardener',
            'The person is a gardener');
        assert(arguments[0] === 'gardener',
            'The first argument is a gardener');
        arguments[0] = 'ninja';
        assert(person === 'ninja',
            'The person is a ninja now');
        assert(arguments[0] === 'ninja',
            'The first argument is a ninja');
        person = 'gardener';
        assert(person === 'gardener',
            'The person is a gardener once more');
        assert(arguments[0] === 'gardener',
            'The first argument is a gardener again');
        }
      infiltrate("gardener");

- P68. When function is invoked as a function, the function context (the value of the this keyword) can be two things: In nonstrict mode, it will be the global context (the `window` object), whereas in strict mode, it will be `undefined`.

- P72. A function constructor enables us to create functions from dynamically created strings. For example: `new Function('a','b','return a+b')` creates a new function with two parameters, `a` and `b`, that returns their sum. 

- P76. For function invocated as a constructor (`new Car()`):
    - Normally a new empty object is created and passed to the contructor as the `this` parameter, and the newly constructed object is returned as the `new` operator's value
    - If the constructor returns an object, that object is returned as the value of the whole `new` expression, and newly constructed object passed as `this` to the constructor is discarded
    - If, however, a nonobject is returned from the constructor, the returned value is ignored, and the newly created object is returned

- P79. The event-handling system of the browser defines the context (`this`) of the event handler invocation to be the target element of the event.

- P84. Arrow functions don't have their own context. Instead, the context is inherited from the function in which they’re defined. If an arrow function is defined within an object literal that’s defined in global code, the value of the `this` parameter associated with the arrow function is the global `window` object.

- P90. Good exercise

- P94. Closure: When we declare inner function inside the outer function, not only is the function declaration defined, but a closure is created that encompasses the function definition as well as all variables in scope **at the point of function definition**.

- P110. When defining variables with the keyword `var`, a variable is defined in the closest function or global environment (while ignoring block environments); while the `let` and `const` keywords define variables in the closest lexical environment (which can be a block environment, a loop environment, a function environment, or even the global environment)

- P115. Calling functions before their declarations.

      assert(typeof fun === "function", "fun is a function even though its definition isn't reached yet!");
      assert(typeof myFunExp === "undefined", "But we cannot access function expressions");
      assert(typeof myArrow === "undefined", "Nor arrow functions");

      function fun(){}
      var myFunExpr = function(){};
      var myArrow = (x) => x;

- P116. Overriding functions.

      assert(typeof fun === "function", "We access the function");
      var fun = 3; 
      assert(typeof fun === "number", "Now we access the number");
      function fun(){}
      assert(typeof fun === "number", "Still a number");

- P129. Good tutorial about generator functions.

- P147. We can't catch error in the following way as the code invoking the callback usually isn't executed in the same step of the event loop as the code that starts the long-running task. That's why in the Node.js world, callbacks customarily take two arguments, err and data, where err will be a non-null value if an error occurs somewhere along the way.

      try {
        getJSON("data/ninjas.json", function() {
            //Handle results
        });
      } catch(e) {/*Handle errors*/}

- P152. There are two ways of rejecting a promise: explicitly, by calling the passed-in `reject` method in the executor function of a promise, and implicitly, if during the handling of a promise, an unhandled exception occurs. 

- P170. `Object.setPrototypeOf` takes in two object arguments and sets the second object as the prototype of the first.

- P176.

      function Ninja(){
        this.swung = true;
      }
      const ninja1 = new Ninja();
      Ninja.prototype.swingSword = function(){
        return this.swung;
      };
      assert(ninja1.swingSword(), "Method exists, even out of order.");
      Ninja.prototype = {
        pierce: function() {
          return true;
        }
      }
      assert(ninja1.swingSword(), "Our ninja can still swing!");
      const ninja2 = new Ninja();
      assert(ninja2.pierce(),"Newly created ninjas can pierce");
      assert(!ninja2.swingSword, "But they cannot swing!");

- P180.

      function Ninja(){}
      const ninja = new Ninja();
      assert(typeof ninja === "object", "The type of the instance is object.");
      assert(ninja instanceof Ninja, "instanceof identifies the constructor." );
      assert(ninja.constructor === Ninja, "The ninja object was created by the Ninja function.");
      const ninja2 = new ninja.constructor();
      assert(ninja2 instanceof Ninja, "It's a Ninja!");
      assert(ninja !== ninja2, "But not the same Ninja!");

- P184. Never use the `Person` prototype object directly as the `Ninja` prototype, like this: `Ninja.prototype = Person.prototype`. Any changes to the `Ninja` prototype will then also change the `Person` prototype (because they’re the same object), and that’s bound to have undesirable side effects.

- P185. In JavaScript, every object property is described with a property descriptor through which
we can configure the following keys:

    - `configurable` — If set to true, the property’s descriptor can be changed and the property can be deleted. If set to `false`, we can do neither of these things.
    - `enumerable` — If set to `true`, the property shows up during a `for-in` loop over the object’s properties.
    - `value` — Specifies the value of the property. Defaults to `undefined`.
    - `writable` — If set to true, the property value can be changed by using an
    assignment.
    - `get` — Defines the `getter` function, which will be called when we access the property. Can’t be defined in conjunction with `value` and `writable`.
    - `set` — Defines the `setter` function, which will be called whenever an assignment is made to the property. Also can’t be defined in conjunction with `value` and `writable`.

- P188. `instanceof` operator checks whether the prototype of the right-side function is in the prototype chain of the object on the left.

      function Ninja(){}
      const ninja = new Ninja();
      assert(ninja instanceof Ninja, "Our ninja is a Ninja!");
      Ninja.prototype = {};
      assert(!(ninja instanceof Ninja), "The ninja is now not a Ninja!?");

- P196. A function’s `prototype` object has a `constructor` property pointing back to the function itself. This property is accessible to all objects instantiated with that function and, with certain limitations, can be used to find out whether an object was created by a particular function.

- P202.

      const ninjaCollection = {
        ninjas: ["Yoshi", "Kuma", "Hattori"], 
        get firstNinja(){
            report("Getting firstNinja");
            return this.ninjas[0];
        },
        set firstNinja(value){
            report("Setting firstNinja");
            this.ninjas[0] = value;
        }
      };

      function Ninja() {
        let _skillLevel = 0;
        Object.defineProperty(this, 'skillLevel', {
          get: () => {
            report("The get method is called");
            return _skillLevel;
          },
          set: value => {
            report("The set method is called");
            _skillLevel = value;
          } 
        });
      }

- P214. Many good examples of `Proxy`.

- P215.

      function makeLoggable(target){
        return new Proxy(target, {
          get: (target, property) => {
            report("Reading " + property);
            return target[property];
          },
          set: (target, property, value) => {
            report("Writing value " + value + " to " + property);
            target[property] = value;
          }
        });
        let ninja = { name: "Yoshi"};
        ninja = makeLoggable(ninja);
        assert(ninja.name === "Yoshi", "Our ninja Yoshi");
        ninja.weapon = "sword";

- P227. In JavaScript, arrays also exhibit a peculiar feature related to the `length` property: Nothing stops us from manually changing its value. Setting a value higher than the current length will expand the array with `undefined` items, whereas setting the value to a lower value will trim the array.

- P230.

      const ninjas = ["Yagyu", "Kuma", "Hattori", "Fuma"];
      delete ninjas[1];
      assert(ninjas.length === 4, "Length still reports that there are 4 items");
      assert(ninjas[0] === "Yagyu", "First item is Yagyu");
      assert(ninjas[1] === undefined, "We've simply created a hole");
      assert(ninjas[2] === "Hattori", "Hattori is still the third item");
      assert(ninjas[3] === "Fuma", "And Fuma is the last item");

- P247. For these two reasons — properties inherited through prototypes and support for string-only keys — plain objects generally aren’t useful as maps.

- P250. Iterating over collections such as `maps` and `set` with `for...of` loops guarantees that the values will be visited in the order in which they were inserted (something we can't rely on when iterating over objects using the `for...in` loop)

- P259. Regular Expression!!!

- P323. Get nondisplayed element's width/height

  1.  Change the `display` property to `block`.
  2.  Set `visibility` to `hidden`.
  3.  Set `position` to `absolute`. (In case a big hole where the element is positioned)
  4.  Grab the dimension values.
  5.  Restore the changed properties.

- P333. Good explanation of event loop, especially two event queues.

- P348. The browser won’t queue up more than one instance of a specific `interval` handler. If there is already an instance of an interval task queued and awaiting execution, this invocation will be dropped. A timer provides the capability to asynchronously delay the execution of a piece of code by **at least** a certain number of milliseconds. Because of the single-threaded nature of JavaScript, we can control only when the timer task is **added to the queue**, and **not when it’s finally executed**.

- P350.

      setTimeout(function repeatMe(){
        /* Some long block of code... */
          setTimeout(repeatMe, 10);
      }, 10);
      setInterval(() => {
        /* Some long block of code... */
      }, 10);
    The two pieces of code may appear to be functionally equivalent, but they aren’t. Notably, the `setTimeout` variant of the code will always have at least a 10 ms delay after the previous callback execution (depending on the state of the event queue, it may end up being more, but **never less**), whereas `setInterval` will attempt to execute a callback every 10 ms regardless of when the last callback was executed. And, intervals **can be fired immediately one after another**, regardless of the delay.
  
- P354. Within the event handler, the `this` keyword refers to the object on which the event has been **registered** while  `event.target` points to the element on which the event has occurred. In most cases the element on which the event handler has been registered is the element on which the element has occurred, but there are exceptions.

- P356. An event is handled in two phases:
    - Capturing phase — An event is first captured at the top element and trickled down to the target element.
    - Bubbling phase — After the target element has been reached in the capturing phase, the event handling switches to bubbling, and the event bubbles up again from the target element to the top element.
  
- P365. Why is it important that each iteration of the event loop doesn’t take much more than about 16 ms?

  A: To achieve smooth-running applications, the browser tries to perform rendering around 60 times per second. Because rendering is performed at the end of the event loop, each iteration shouldn’t last much longer than 16 ms, unless we want to create slow and jagged applications.

- P375. DOM Clobbering (greedy IDs).

      <form id="form" action="/conceal">
        <input type="text" id="action"/>
        <input type="submit" id="submit"/>
      </form>
    
  The browsers have added properties to the `<form>` element for each of the `input` elements within the form that reference the element. This might seem handy at first, until we realize that the name of the added property is taken from the id or name values of the input elements. And if that value just happens to be an already-used property of the form element, such as `action` or `submit`, those original properties are replaced by the new property. 

  So, before the `input#submit` element is created, the reference `form.action` points to the value of the `action` attribute for the `<form>`. Afterward, it points to the `input#submit` element. The same thing happens to `form.submit`.

- P390. Destructing.

      const ninja = { name:"Yoshi", action: "skulk", weapon: "shuriken"};
      const {name, action, weapon} = ninja;
      const {name: myName, action: myAction, weapon: myWeapon} = ninja;

      const ninjas = ["Yoshi", "Kuma", "Hattori"];
      const [firstNinja, secondNinja, thirdNinja] = ninjas;
      const [, , third] = ninjas;
      const [first, ...remaining] = ninjas;

- P391. Enhanced object literals

      const newNinja = {
        name,
        getName(){
          return this.name;
        },
        ["new" + name]: true
      };
