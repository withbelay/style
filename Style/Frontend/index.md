# WOLF Frontend Dev Style Guide

Welcome to the WOLF Dev Style Guide for the Frontend. This is an opinionated guide of the style, conventions, and best practices that code should adhere to in pull requests.

ESLint will automatically take care of a lot for you, it is a very valuable plugin to get set up and running. We have an `.eslintrc` that will automatically allow ESLint to start checking the code.

A large portion of this guide was informed and uses modified examples from the [AirBnB style guide](https://github.com/airbnb/javascript#amendments) to fit WOLF's needs.

## Engineering Philosophy

There are 3 driving concepts behind this style guide and our engineering philosophy in general:

1. Keep it Simple - Code should be straightforward and understandable by other engineers with as little effort as possible. Avoid sprawling conditionals, make the states components can be in as obvious as possible, and break things up into multiple functions/components when complexity adds up
2. Keep it Terse - Code should be as compact as it needs to be without any excess fluff. Prefer implicit over explicit. JSX should be as optimal as possible without any unnecessary components or styles.
3. Keep it Clean - Use ESLint to automatically format your code to our standards. Use whitespace to break up constant declarations from function declarations and the JSX. Don't leave any unused variables or imports. 

## Avoid These

Javascript as a language was developed in just 10 days, and as you might expect from that description, there are some fairly weird and dangerous things that are historically baked into it if you go looking hard enough.

- **Never `eval()`**. Just ***don't use it***. `eval()` is:

  1. Slow (no opportunity to compile or cache evaluated code)
  2. Makes debugging a challenge (no line numbers for example)
  3. Opens up code for injection attacks

  If you are unaware, `eval()` evaluates JavaScript code represented as a string, which can be very dangerous. You really should never run into a situation where you are forced to use it.

- Don't use the `void` operator. The `void` operator takes the expression you give it, evaluates it, and returns `undefined`. It's antiquated and if you need to use it because it evaluates whatever expression you give it `undefined`… just use the global variable `undefined`. 

- Try to avoid coercion. Make your type casts as obvious as possible because it will make the code more readable. Consider the below example

  ```jsx
  // bad
  // is c an int or a string?
  const a = 10;
  const b = "10";
  const c = a + b; // seems like an int, but c = "1010"
  
  // good
  // clear that c is intended to be a concatenated string
  const a = 10;
  const b = "10";
  const c = `${String(a)}${b}`; // very clear that c = "1010" just from looking
  
  // an exception
  // !! technically coerces the expression to a bool, but since everything in js is truthy or falsy its simpler to think of it as a bool typecast shorthand so it's fine to use
  const str = "hello";
  const hasText = !!str; // true
  ```

### Mutation

- ***Avoid mutation at all costs***

  > When code changes, it is more likely to break, and the code changing is inevitable. When we mutate (or change) a variable directly, it becomes harder to trace what that variable is supposed to be at at any given time during execution and therefore becomes harder to reason about - Mutation leads to confusion and that creates bugs. Redux toolkit manages a mutable pointer to our immutable state and React Native directly manages the rendering. Our components and screens only need to be functions that translate data from our state into rendered elements.

- Don't operate directly, operate on copies

  ```jsx
  // make sure to properly copy things, js can be a little weird

  const user = {name: 'Michael Grant Warshowsky'};
  // let's try to copy it
  const user2 = user;
  user2.name = 'Heewon Ahn';

  // we didn't do a copy though, we just clobbered user.name:
  console.log(user.name === 'Heewon Ahn') // true

  // use Object.assign to do shallow copies
  const user2 = Object.assign({}, user);
  user2.name = 'Heewon Ahn';
  console.log(user.name === 'Heewon Ahn') // false
  console.log(user2.name === 'Heewon Ahn') // true

  // similar for arrays, use spread syntax to copy
  const array = [1, 2, 3, 4];
  const arrayCopy = [...array];
  ```

- Use functional components instead of class components

  > There is one singular difference between React class components and functional components - class components mutate themselves, functional components are immutable. A class component will simply mutate it's own internal references when props change where as an entirely new functional component will be created if it's props change. You can imagine the completed application as a series of stateful snapshots over time that can each be traced back to the exact set of props that were given to it.

#### A note about Immer

So now this is going to sound like it completely contradicts the above statements, but there is one specific scenario where writing mutating code is actually fine, and that is in redux toolkit reducers. Redux toolkit makes use of the [Immer](https://immerjs.github.io/immer/) library, which means that redux reducers receive a *copy* of the current state called a draft which can be modified with the usual methods, then that draft is used to create the new state.

```jsx
.addCase(createAlpacaACHRelationship.fulfilled, (state, action) => {
  state.achList.push(action.payload.relationship);
})
```

This is a real redux reducer we use, as you can see, we directly manipulate the state by pushing to it. Normally this is not a good idea, but Immer is working behind the scenes to make this possible. As you can see, we don't need to spread the state or do any redux weirdness, Immer just handles it for us, and the code is much cleaner, compact, and easier to understand.

## Naming Conventions

- Global constants must be `SCREAMING_SNAKE_CASE`

  ```jsx
  // bad
  const globalConst = 5;

  // good
  const GLOBAL_CONST = 5;
  ```

- React components must be in `PascalCase` (Each word capitalized)

  ```jsx
  // bad
  const reactComponent = () => <View />;

  // good 
  const ReactComponent = () => <View />;
  ```

- Functions and hooks regardless of their location must be `camelCase`

  ```jsx
  // bad
  const myfunc = (x) => x**2;
  const MyFunc = (x) => x**2;

  // good
  const myFunc = (x) => x**2;

  const Component = ({}) => {
    // bad
    const myfunc = (x) => x**2;
    const MyFunc = (x) => x**2;

    // good
    const myFunc = (x) => x**2;
  }
  ```

- Local constants and testIDs must be `camelCase`

  ```jsx
  const Component = ({}) => {
    // bad
    const LocalConst = 5;
    const localobj = {a: 1};
    
    // good
    const localConst = 5;
    const localObj = {a: 1};
    
    // bad
    const SubComponent = () => <View testID="SubComponent.View" />;
    	
    // good
    const SubComponent = () => <View testID="subComponent.view" />;
  }
  ```

- A base filename should exactly match the name of its default export

  ```jsx
  // hypothetical file contents
  const Component = () => null;
  export default Component;

  // bad
  import Component from '@/path/component';

  // good
  import Component from '@/path/Component';
  ```

- Acronyms and initialisms should always be all uppercase, or all lowercase

  ```jsx
  // bad
  import SmsContainer from '@/containers/SmsContainer';
  
  // bad
  const HttpRequests = [];
  
  // good
  import SMSContainer from '@/containers/SMSContainer';
  
  // good
  const HTTPRequests = [];
  
  // good
  const httpRequests = [];
  
  // best
  const requests = [];
  ```

## References and Variables

- Use `const` for all of your references, don't use `var`

  ```jsx
  // bad
  var a = 1;
  let a = 1;
  a = 1; // makes `a` global and able to leak out

  // good
  const a = 1;
  ```
  > The reason you shouldn't use vars is because `let` and `const` are block scoped whereas `var`s are function scoped.

  ```jsx
  // const and let only exist in the blocks they are defined in
  {
    let a = 1;
    const b = 1;
    var c = 1;
  }

  console.log(a); // ReferenceError
  console.log(b); // ReferenceError
  console.log(c); // prints 1, the var leaks out to the rest of the code. not good.
  ```

- Don't chain variable assignments

  ```jsx
  // bad
  (() => {
    // js interprets this as
    // let a = ( b = ( c = 1 ) );
    // The let keyword only applies to variable a; variables b and c become
    // global variables. not good.
    const a = b = c = 1;
  }());
  
  console.log(a); // throws ReferenceError
  console.log(b); // 1
  console.log(c); // 1
  
  // good
  (() => {
    const a = 1;
    const b = a;
    const c = a;
  }());
  
  console.log(a); // throws ReferenceError
  console.log(b); // throws ReferenceError
  console.log(c); // throws ReferenceError
  ```


- In the case you do need to reassign, use `let`, and try to encapsulate the variable that changes in a function/hook/IIFE. 

  ```jsx
  // bad
  var count = 1;
  count += 1;

  // good
  let count = 1;
  count += 1;

  // best, use the let and don't expose the mutable reference to the rest of the code
  const counter = () => {
    let count = 1;
    count += 1;
  }

  // same as above, but with an IIFE
  (() => {
    let count = 1;
    count += 1;
  }());
  ```

- Don't leave any unused variables around

  ```jsx
  // bad
  const some_unused_var = 42;

  // bad, unused function arguments
  const getX(x, y) => x;

  // good
  const getXPlusY = (x, y) => x + y;

  // an exception
  // 'symbol' is ignored even if unused because it has a rest property sibling.
  // this is a form of extracting an object that omits the specified keys.
  const {symbol, ...analysis} = data;
  // 'analysis' is now the 'data' object without its 'symbol' property.
  ```

- Don't use or save references to `this`

  > We don't use classes or class components so there is no reason to ever use `this` normally. Consider the below code on how to manage references to certain scopes

### Naming

- Name things accurately.

  > Ultimately we prefer accurate, clear names over shorthand that doesn't match up with what information/function the reference is actually referring to. Terse code with long names is better than terse code with terse names.

  ```jsx
  // a real example from WOLF code
  // wordy, but 100% accurate to what the function does and easily distinguishable from similar alerts
  export function showNoInvestmentAccountsAlert() {
    Alert.alert(
      'No investment accounts',
      "Sorry, we couldn't find any investment accounts related to the credentials you entered. Please try linking a different account.",
    );
  }
  ```

- Booleans must be prefixed with `is` or `has` for readability

  ```jsx
  // bad
  const loaded = true;
  const error = false;
  
  // good
  const isLoaded = true;
  const hasError = false;
  ```

## Objects

- Use the literal syntax for object creation

  ```jsx
  // bad
  const obj = new Object();

  // good
  const obj = {};
  ```

- Use the property value shorthand

  ```jsx
  const property = 'check out this cool shorthand method';

  // bad
  const obj = {
    property: property,
  };

  // good
  const obj = {
    property,
  };
  ```

- Group shorthand properties together for readability

  ```jsx
  const apple = 'apple';
  const orange = 'orange';
  
  // bad
  const obj = {
    apple,
    pear: 'pear',
    plum: 'plum',
    orange,
    grape: 'grape',
  }
  
  // good
  const obj = {
    apple,
    orange,
    grape: 'grape',
    pear: 'pear',
    plum: 'plum',
  }
  ```

### Properties

- Use dot notation when accessing properties

  ```jsx
  const theme = useTheme();

  // bad
  theme['type'];

  // good
  theme.type;

  // an exception
  // sometimes you will just need to use bracket notation though
  theme['type-2'];
  ```

- Use bracket notation when accessing properties with a variable

  ```jsx
  const theme = useTheme();
  const color = 'border';
  
  theme[color];
  ```

## Arrays

- Use the literal syntax for array creation

  ```jsx
  // bad
  const array = new Array();

  // good
  const array = [];
  ```

- Use `.map()`, `.filter()`, and `.reduce()` over `for` loops. 

  > Functionally manipulating arrays is often more compact and allows the various array operations to be chained together. Learn more about these functions [here](https://medium.com/poka-techblog/simplify-your-javascript-use-map-reduce-and-filter-bd02c593cc2d).

  ```jsx
  // -- .map() --
  // bad
  for (i = 0; i < len; i += 1) {
    console.log(array[i]);
  }

  // good
  array.map((e) => console.log(e));

  // -- .filter() --
  // we want to keep all instances of 'keep me'

  // bad
  let filteredArray = []
  for (i = 0; i < len; i += 1) {
    if (array[i] === 'keep me') {
      filteredArray.push(array[i]);
    }
  }

  // good
  // note how we also remove a temp variable for a const
  const filteredArray = array.filter((e) => e === 'keep me');

  // -- .reduce() --
  // we want to sum the following array
  const reduceArray = [1, 2, 3];

  // bad
  const reduceValue = 0;
  for (i = 0; i < len; i += 1) {
    reduceValue += reduceArray[i];
  }

  // good
  const reduceValue = reduceArray.reduce((acc, e) => acc + e, 0);
  ```

- Use the spread syntax to copy and combine arrays

  ```jsx
  // -- copy arrays --
  // bad
  const len = array.length;
  const arrayCopy = array.map((e) => array[e]);
  
  // good
  const arrayCopy = [...array];
  
  // -- combine arrays --
  const a = [1, 2];
  const b = [3, 4];
  
  // bad
  // also mutates a
  b.map((e) => a.push(e));
  
  // good
  const c = Array.concat(a, b);
  
  // best
  const c = [...a, ...b];
  ```

## Destructuring

- Use object destructuring

  > Destructuring saves you from creating temporary references for those properties, and from repetitive access of the object. Repeating object access creates more repetitive code, requires more reading, and creates more opportunities for mistakes. Destructuring objects also provides a single site of definition of the object structure that is used in the block, rather than requiring reading the entire block to determine what is used.

  ```jsx
  // bad
  const getOrder = (order) => {
    const symbol = order.symbol;
    const qty = order.qty;
    const filled_avg_price = order.filled_avg_price;

    return `You have ${qty} shares of ${symbol} valued at ${qty * filled_avg_price}`;
  }

  // good
  const getOrder = (order) => {
    const {symbol, qty, filled_avg_price} = order;
    return `You have ${qty} shares of ${symbol} valued at ${qty * filled_avg_price}`;
  }

  // best
  const getOrder = ({symbol, qty, filled_avg_price}) => (
    `You have ${qty} shares of ${symbol} valued at ${qty * filled_avg_price}`
  );
  ```

- Use array destructuring

  ```jsx
  const array = [1, 2, 3, 4];

  // bad
  const first = array[0];
  const second = array[1];

  // good
  const [first, second] = array;
  ```

- Use object destructuring for multiple return values, not array destructuring

  > This is ideal because you can add new properties over time or change the order of things without breaking call sites

  ```jsx
  // bad
  const getSides = ({left, right, top, bottom}) => (
    [left, right, top, bottom]
  );
  
  // the caller needs to think about the order of return data
  const [left, _, top] = getSides(input);
  
  // good
  const getSides = ({left, right, top, bottom}) => (
    {left, right, top, bottom}
  );
  
  // the caller selects only the data they need
  const { left, top } = getSides(shape);
  ```

## Strings

- Use single quotes for string literals

  ```jsx
  // bad
  const stock = "TSLA";

  // good
  const stock = 'TSLA';
  ```

- Use double quotes for component properties and for when you have apostrophes

  ```jsx
  // bad
  const Header = ({text}) => <Text category='h1' />{text}</Text>;
  const Header = ({text}) => <Text category={'h1'} />{text}</Text>;
  const Header = ({text}) => <Text category={"h1"} />{text}</Text>;

  // good
  const Header = ({text}) => <Text category="h1" >{text}</Text>;

  // bad
  const retryMessage = 'Let\'s try that again';

  // good
  const retryMessage = "Let's try that again";
  ```

- Don't write strings over multiple lines

  ```jsx
  // awful
  const error = 'This is an egregiously long error message' +
                'created by the best financial data provider in the world, HubFin.' +
                'Have you tried turning the backend off and on again?';

  // bad
  const error = 'This is an egregiously long error message \
  created by the best financial data provider in the world, HubFin. \
  Have you tried turning the backend off and on again?';

  // good
  const error = 'This is an egregiously long error message created by the best financial data provider in the world, HubFin. Have you tried turning the backend off and on again?';
  ```

- Use template literals over string concatenation

  ```jsx
  // awful
  const position = ['Your $AAPL position is valued at', value, '.'].join();

  // bad
  const position = 'Your $AAPL position is valued at ' + value + '.';

  // good
  const position = `Your $AAPL position is valued at ${value}.`;
  ```

- Don't unnecessarily escape strings

  ```jsx
  // bad
  const str = '\'this\' \i\s \"quoted\"';
  
  // good
  const str = '\'this\' is "quoted"';
  ```

## Typecasting

- Typecase to number with `Number`, avoid `parseInt`

  ```jsx
  parseInt("23") // 23
  parseInt("023") // 19 (octal)
  parseInt("023", 10) // 23

  Number('23') // 23
  Number('023') // 23
  ```

- Typecast to string with `String`

  ```jsx
  const stock = {
    price: 9,
  };

  // bad
  const priceText = new String(stock.price); // typeof priceText is "object" not "string"
  const priceText = stock.price + ''; // invokes this.reviewScore.valueOf()
  const priceText = stock.price.toString(); // isn’t guaranteed to return a string

  // good
  const totalScore = String(stock.price);
  ```

- Typecast to bool with `!!`

  > What this does is the first ! logically negates what was given which coerces the value to a bool, then it is negated again to get the original value, but how it would be if it were just a bool

  ```jsx
  const unsafe = null;
  
  // bad
  const isSafe = new Boolean(unsafe);
  const isSafe = Boolean(unsafe);
  
  // good
  const isSafe = !!unsafe;
  ```

## Comparison Operators

- Use `===` and `!==` over `==` and `!=` when not comparing to `null`

  ```jsx
  // bad
  '1' == 1; // true

  // good
  '1' === 1; // false

  // acceptable
  1 != null;
  ```

- Conditional statements such as the `if` statement evaluate their expression using coercion with the `ToBoolean` abstract method and always follow these simple rules:

  - **Objects** evaluate to **true**
  - **Undefined** evaluates to **false**
  - **Null** evaluates to **false**
  - **Booleans** evaluate to **the value of the boolean**
  - **Numbers** evaluate to **false** if **+0, -0, or NaN**, otherwise **true**
  - **Strings** evaluate to **false** if an empty string `''`, otherwise **true**
  - Read more from this [guide on equality in js](https://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108) by Angus Croll

- Use shortcuts for comparisons where it makes sense
  > Reminder to avoid using control flow statements in the JSX, it's better to just use ternaries and the logical AND because of the compactness and it avoids issues with 'The text node needs to be wrapped in <Text>' error
  >
  > See the Control Statements section for more

  ```jsx
  // -- bool --
  // bad
  if (isValid === true) { ... }
  
  // good
  if (isValid) { ... }
  
  // -- string --
  // acceptable
  if (name !== '') { ... }
  
  // good
  if (name) { ... }
  
  // -- number --
  // acceptable
  if (collection.length > 0) { ... }
  
  // good
  if (collection.length) { ... }
  ```

## Iterators

- Don't

  > Ok but seriously, not using iterators like `for` loops enforces immutability which makes it easier to reason about code and also makes it easier to tell what operation we are performing on an array. A for loop at a glance doesn't give you the same initial info that seeing `.map()` or `.filter()` or `forEach()` does until you dig deeper into it
  >
  > Use `map()` / `every()` / `filter()` / `find()` / `findIndex()` / `reduce()` / `some()` / ... to iterate over arrays, and `Object.keys()` / `Object.values()` / `Object.entries()` to produce arrays so you can iterate over objects.

  ```jsx
  const numbers = [1, 2, 3, 4, 5];
  
  // bad
  let sum = 0;
  for (let num of numbers) {
    sum += num;
  }
  
  // good
  let sum = 0;
  numbers.forEach((num) => {
    sum += num;
  });
  
  // best (use the functional force)
  const sum = numbers.reduce((acc, num) => acc + num, 0);
  
  // bad
  const increasedByOne = [];
  for (let i = 0; i < numbers.length; i++) {
    increasedByOne.push(numbers[i] + 1);
  }
  
  // bad, still mutating with push
  const increasedByOne = [];
  numbers.forEach((num) => {
    increasedByOne.push(num + 1);
  });
  
  // best (keeping it functional)
  const increasedByOne = numbers.map((num) => num + 1);
  ```

## Functions

- Use arrow notation instead of `function` where possible. If you are trying to export a hook/function/component, then it is perfectly acceptable to use the `function` since you can't define and export a const on the same line.

  ```jsx
  // bad
  function square(x) {
    return x**2;
  }

  // acceptable only when exporting
  export function square(x) {
    return x**2;
  }

  // good, but consider the next point
  const square = (x) => {
    return x**2;
  }
  ```

- Implicitly return where possible

  ```jsx
  // best
  const square = (x) => x**2;
  ```

- Always include parentheses around arguments for clarity and consistency.

  > This minimizes diff churn when adding or removing arguments

  ```jsx
  // bad
  const square = x => x**2;

  // good
  const square = (x) => x**2;
  ```

- Don't provide default arguments

  > Consider the following real WOLF example. This function takes in a number and an optional object that contains the specific configuration of how that number should be formatted. It would be incredibly unwieldy if every time you called it you gave it every single option, even if you don't actually need it.

  ```jsx
  export function formatNumber(
    number,
    {
      decimalPlace = 2,
      abbreviateK = false,
      smartAbbreviate = true,
      includeDollar = true,
      includeMinus = true,
      includePlus = false,
      includePercent = false,
      addCommas = true,
      min = null,
      toFixed = true,
    } = {},
  ) { ... }
  
  // bad
  formatNumber("1000.00", {decimalPlace: 2, abbreviateK: false});
     
  // good
  // default arguments are automatically applied
  formatNumber("1000.00");
  
  // only provide the configurations where it changes
  formatNumber("1000.00", {includePlus: true});
  ```

## Blocks

- Use braces with all multiline blocks

  ```jsx
  // bad
  if (test)
    return false;

  // good
  if (test) return false;

  // good
  if (test) {
    return false;
  }
  ```

- If an `if` block always executes a `return` statement, the subsequent `else` block is unnecessary. A `return` in an `else if` block following an `if` block that contains a `return` can be separated into multiple `if` blocks.

  ```jsx
  // bad
  const foo = () => {
    if (x) {
      return x;
    } else {
      return y;
    }
  }
  
  // bad
  const foo = () => {
    if (x) {
      return x;
    } else if (y) {
      return y;
    }
  }
  
  // bad
  const foo = () => {
    if (x) {
      return x;
    } else {
      if (y) {
        return y;
      }
    }
  }
  
  // good
  const foo = () => {
    if (x) {
      return x;
    }
  
    return y;
  }
  
  // good
  const foo = () => {
    if (x) {
      return x;
    }
  
    if (y) {
      return y;
    }
  }
  
  // best
  // this is called the 'nullish coalescing operator', if x is null it returns y, otherwise it returns x
  const foo = () => {
    return x ?? y;
  }
  ```

## Control Statements

- Don't use selection operators in place of control statements for running functions

  ```jsx
  // bad
  !isRunning && startRunning();
  
  // good
  if (!isRunning) {
    startRunning();
  }
  
  // we do this a lot in JSX, fine to do for literals and variables
  <View>
    {hasProperty && <Text>'Property detected'</Text>}
  </View>
  ```

- Use ternaries for short control statements and if else blocks for longer, more complex logic

  ```jsx
  // bad
  const foo = () => {
    if (thing) {
      return doThis();
    } else {
      return doThat();
    }
  };
  
  // good
  const foo = () => {
    return thing ? doThis() : doThat();
  };

  // best
  const foo = () => thing ? doThis() : doThat();
  ```

- Use ternaries & the logical AND in the JSX to conditionally render elements.

  ```jsx
  // a common 'archetype' is to render an error if it exists, or replace something that would normally display with an error 
  // Note that error will start as null
  
  // will render the content unless an error is present
  <View>
    {error ? <Text>{error}</Text> : <Content />}
  </View>
  
  // will render the error text below the content if one is present
  <View>
    <Content />
    {error && <Text>{error}</Text>}
  </View>
  ```

## Components & CSS

- Keep JSX as simple and as optimized as possible. There should be no unnecessary views or unused styles. It is often worth refactoring or at least re-analyzing your JSX and CSS styles before making a PR.

  ```jsx
  // bad
  <View style={styles.container}>
  	<View style={styles.content}>
    	<Text>This is some content</Text>
    </View>
  </View>
  
  const styles = StyleService.create({
  	container: {
      flex: 1,
    },
    content: {
      alignSelf: 'center',
  	},
  });
  
  // good
  <View style={[styles.container, styles.content]}>
   <Text>This is some content</Text>
  </View>
  
  const styles = StyleService.create({
  	container: {
      flex: 1,
    },
    content: {
      alignSelf: 'center',
  	},
  });
  
  // but do we really need two styles?
  // sometimes it depends on the component if you choose to merge styles, generally it will be more clear if your styles are as atomic as possible, and if you name them as literally as you can
  // (if a style just applies flex: 1, call it `flex1`, if a style centers a component, call it `center`, etc)
  <View style={styles.container}>
   <Text>This is some content</Text>
  </View>
  
  const styles = StyleService.create({
  	container: {
      flex: 1,
      alignSelf: 'center',
    },
  });
  
  // you can break out layout styles into their own style to apply as needed
  <View style={styles.flex1}>
    <Text style={styles.center}>Header</Text>
   	<View style={styles.row}>
      <AppIcon></AppIcon>
    	<Text>This is some content in a line</Text>
    </View>
  </View>
  
  const styles = StyleService.create({
  	container: {
      flex: 1,
      alignSelf: 'center',
    },
  });
  ```

- Boolean properties can be applied by just including their name

  ```jsx
  // acceptable
  <NavBar backButton={true} />

  // good
  // this is just shorthand for the above
  <NavBar backButton />
  ```

- Make sure to read up on UIKitten for how to automatically apply specific styles to common elements like `<Text />` and `<Button />`

- Use UIKitten StyleSheets for CSS, don't inline styles unless you need them to be programmatically defined

## UIKitten

- Learn about [UIKitten](https://akveo.github.io/react-native-ui-kitten/docs/getting-started/what-is-ui-kitten#what-is-ui-kitten), the UI library we use on the WOLF FE. All of it's configurations are stored as readable json in `custom-mapping.js`, and you should familiarize yourself with it.

  > We use UIKitten for 3 things:
  >
  > 1. Theme provider (allows us to switch between light and dark mode i.e. independently set colors as well as a shared general theme)
  > 2. Themed colors in CSS styling (allows us to reference theme colors in our CSS)
  > 3. Component Library (commonly used components like `<Text />` and `<Button />` that use our design language)

- Don't use React Native `<Text />`, use UIKitten `<Text />`

  > This allows all `<Text />` components to use the `category` prop to quickly change between font sizes and weights in`custom-mapping.js`

- Don't use `<TouchableOpacity />` unless you absolutely need to. Use a UIKitten `<Button />`

  > UIKitten `<Button />`s wherever possible. By doing this, it allows all of our buttons to be changed at once from the `custom-mapping.js` file because they all read their base style information from there. 

- Don't unnecessarily color text

  > UIKitten provides an `appearance` and `status` prop to almost every component and those allow us to quickly get a component inline with their designs. `<Text />` components from  You can also add new statuses and appearances too from the `custom-mapping.js` file.

## Hooks

From the React Native Docs: "**What is a Hook?** A Hook is a special function that lets you “hook into” React features. For example, `useState` is a Hook that lets you add React state to function components."

### [useState](https://reactjs.org/docs/hooks-state.html)

You may be thinking, 'if all of our components are functions, how is information preserved between function calls?'. The answer is state. Redux Toolkit allows us to manage state independently of our components, and lets us interact with the state via a single mutable reference (which is `state`, that thing passed to every reducer and selector), and that seems all fine but what if we need something outside of that system, say to store a unique error message or loading state in only one specific component? It doesn't make sense to add something to redux unless multiple parts of the application depend on that state, so if we want a component to be stateful without Redux Toolkit, we need `useState`.

```jsx
// The state example from the docs
const Widget = ({}) => {
  // useState returns the reference to the state variable and the function to manipulate it safely
  // You can rename these to whatever, but since we are making a counter we have `count` and `setCount`
  const [count, setCount] = useState(0);
  // Use the given function to manipulate the state
  const increment = () => setCount(count + 1);

	return (
    // Now every time we press this button, our state changes which triggers a rerender that then shows the new changes
    <Button onClick={increment}>
    	I count things
    </Button>
  );
}
```

### [useCallback](https://reactjs.org/docs/hooks-reference.html#usecallback)

When you define something in a component, it is created on each render. What `useCallback` does is it lets you define something, and it only gets redefined if the dependencies of that thing changes. What this means is it lets you memoize some arbitrary callback, so very useful if you have a sub-component defined in the main component or if you have a very heavy function that shouldn't be ran on each render.

```jsx
const foo = (data) => myExpensiveOperation(data); // gets created every render

// callback-ized version
const foo = useCallback(
  () => myExpensiveOperation(data),
  [data], // dependency array, means this only runs again if data changes
);

// note, some things are NOT worth memoizing!
// this needs even more time to calculate because of the overhead of memoizing, small things are not worth memoizing
const doubler = useCallback(
  () => x * 2,
  [x], 
);
```

### [useMemo](https://reactjs.org/docs/hooks-reference.html#usememo)

`useMemo` is the same as `useCallback` but instead of returning a memoized callback, it returns a memoized value. Similarly to `useCallback`, you should only use this for things that are expensive. They may seem like they do the same thing because they both take a function and an array of dependencies, but the difference is that `useCallback` *returns* its function and `useMemo` *runs* its function and returns the result.

```jsx
// an illustrated example of the differences between useMemo and useCallback

const foo = useCallback(() => {
  console.log('foo');
}, []);

foo(); // foo printed
console.log(foo); // function

const foo = useMemo(() => {
  console.log('foo');
}, []); // foo printed

foo(); // ReferenceError
console.log(foo); // null, the func returns nothing, just gets ran immediately

// this is the same as the useMemo code
// the difference is the () at the end
const foo = useCallback(() => {
  console.log('foo');
}, [])();
```

```jsx
// some examples of useMemo
const memo = useMemo(() => myExpensiveOperation(data), [data]);

// an example from WOLF code
// this will generate a list of colors to use only once since data.length never changes
const barColors = useMemo(() => getRandomColors(data.length), [data.length]);
```

### [useEffect](https://reactjs.org/docs/hooks-reference.html#useeffect)

"Mutations, subscriptions, timers, logging, and other side effects are  not allowed inside the main body of a function component (referred to as React’s *render phase*). Doing so will lead to confusing bugs and inconsistencies in the UI." In the event we need to perform one of these *side effects*, we have the `useEffect` hook. The function passed to `useEffect` will run after the render is committed to the screen. Similarly to `useMemo` and `useCallback`, `useEffect` accepts a dependency array, which means it will only fire if the dependencies change. You can also give it an empty one to fire it after every rerender.

```jsx
// an example from WOLF code
// useEffect is most commonly used to fetch data
useEffect(() => {
  fetchProfileSubmissionsPage(user._id);
}, [fetchProfileSubmissionsPage, user._id]);
```

The most common use case for this hook is to fetch data after a screen loads to populate it, this will cause another rerender though so try to pre-load your data if it makes sense to do so and prefer to pass things in through route.params if possible.

### [useRef](https://reactjs.org/docs/hooks-reference.html#useref)

`useRef` allows you to create a mutable reference to an object that's `.current` property is whatever you give to it. The reference will exist for the full lifetime of the component. This might seem similar to state but updating a ref does not trigger a rerender. A common use case is keeping a ref to a specific component (typically an input) to manipulate it.

```jsx
// a simplified example from WOLF code
const usernameInputRef = useRef(null);

// shake the input if the username already exists
const saveUser = () => {
  if (!usernameValidation.isValid) {
    usernameInputRef.current?.shake();
  }
}

return (
  <AppValidatedInput ref={usernameInputRef} ... />
  <Button onPress={saveUser}>Save changes</Button>
);
```

### [useSelector & useDispatch](https://react-redux.js.org/api/hooks)

`useSelector` and `useDispatch` are hooks provided by Redux Toolkit. Typically you would use `mapStateToProps` and `mapDispatchToProps` to connect a screen with the state and dispatch. You can also get state values and dispatch thunks with these hooks instead for components. Use `mapToProps` for screens and hooks for components.

`useSelector` can be used to get information about the state

```jsx
// some examples from WOLF code
const {phone_number} = useSelector(
  (state) => state.brokerageApp.application.contact,
);

const alpacaAccountEquity = useSelector(
  (state) => state.alpaca?.tradingAccount?.equity,
);
```

Whereas `useDispatch` lets us dispatch redux actions by giving us a reference to the dispatch function

```jsx
const dispatch = useDispatch();

const handleStreetAddressChange = (value) => {
  dispatch(updateContact({street_address: [value]}));
};
```

### useStyleSheet & useTheme

`useStyleSheet` and `useTheme` are provided by UIKitten to get CSS styles and theming respectively. You can create a stylesheet with a UIKitten `StyleService` and then use it in your component:

```jsx
import {
  useStyleSheet,
  StyleService,
} from '@ui-kitten/components';

const ColorBlock = () => {
  const styles = useStyleSheet(themedStyles);

	return <View style={styles.container} />
}

const themedStyles = StyleService.create({
  container: {
    flex: 1,
    backgroundColor: 'background' // pulls in from theme regardless
  }
});
```

To get theme colors in your CSS, `useTheme`.

```jsx
import {
  useTheme,
} from '@ui-kitten/components';

const PositionIndicator = ({price}) => {
  const theme = useTheme();
  const color = price > 0 ? theme.positive : theme.negative;
	
  // this will be green if price is positive, red if price is negative
	return <View style={{backgroundColor: color}} />
}
```



## Performance

- Avoid anonymous functions in the JSX

  > Two simple rule to remember about rerenders is that React Native will render your component 1) whenever its props change and 2) whenever the state changes, and the problem with anonymous functions is that *identical anonymous functions are not logically equivalent*.
  >
  > ```jsx
  > () => null === () => null // false
  > ```
  >
  > JavaScript is comparing these functions *by reference*, which means it's just comparing the positions of these two funcs in memory, and since anonymous funcs are created on the spot, every anonymous function will be effectively unique as far as javascript is concerned.
  >
  > What this means is if you pass an anonymous func as a prop, you are going to cause that component to rerender at every possible opportunity, because React Native will make a new anonymous function, compare it to the old one, and interpret the 'new' anonymous function as a prop update. Consider the below example where we want to create a component that only needs to render once.

  ```jsx
  // bad
  <Widget
    color="red" // this prop never changes so it will not cause Widget to rerender if the screen with the pure component updates "red" === "red"
    onPress={() => console.log('hello world')} // seems innocuous but will always cause this component to update because anonymous fn !== anonymous fn
  />

  // good
  const onPress = () => console.log('pure again');
  <Widget 
    color="red"
    onPress={onPress} // press === press because we bind the function to the name press, and that always refers to the same spot in memory, this component will render once and never need to rerender now
  />
  ```

- If something does not need to be defined in the component, move it out

  > Otherwise it will be recreated on each rerender

  ```jsx
  // bad
  const Widget = ({}) => {
    const onPress = () => console.log('i get made every rerender');
    return <Button onPress={onPress} />;
  }

  // good
  const onPress = () => console.log('i get made once');
  const Widget = ({}) => {
    return <Button onPress={onPress} />;
  }
  ```

- Use [WDYR](https://github.com/welldone-software/why-did-you-render) to investigate exactly what causes a component to rerender for performance testing

  > We already have this library in our dependencies, it's just a matter of adding `SHOW_WDYR=true` to your `.env`

- Make use of `useMemo` and `useCallback`

  > See the Hooks section for more

## Robustness

- You will often be writing components that receive data from API endpoints, and sometimes this data can be erroneous or even missing. A good rule of thumb is if you get data from the backend or some source that isn't being made by you, assume it will be null at some point.

  ```jsx
  // an example from WOLF code
  // this takes the user's holdings, and immediately destructures the object into all of the properties it needs
  // and assigns them a backup default argument. in the event one of these properties is missing from the holding,
  // it becomes 'N/A' instead of null, which lets the component gracefully handle the missing content.
  export default function Position({holdings = [], symbol = ''}) {
  
    ...
  
    const {
      price = 'N/A',
      previousClose = 'N/A',
      dailyPctChange = 'N/A',
      purchasePrice = 'N/A',
      quantity = 'N/A',
      totalValue = 'N/A',
      totalChange = 'N/A',
      purchaseValue = 'N/A',
    } = holdings.find((p) => p.symbol === symbol) ?? {};
  
    // Now we can create more information about the position provided it exists, otherwise we have defaults
    const change =
      price !== 'N/A' && previousClose !== 'N/A' ? price - previousClose : 'N/A';
  
    const totalReturnPct =
      totalChange !== 'N/A' && purchaseValue !== 'N/A'
        ? (totalChange / purchaseValue) * 100
        : 'N/A';
    const portfolioValue = holdings
      .filter((p) => p.totalValue)
      .reduce((a, b) => a + b.totalValue, 0);
    const portfolioPercentage =
      totalValue !== 'N/A' ? (totalValue / portfolioValue) * 100 : 'N/A';
  
    ...
  
  }
  ```

## Testing

- New components should always be accompanied by a corresponding jest test. Learn about [jest](https://jestjs.io/) and read through our tests to get an idea of how we utilize it

- A new component should always be accompanied with a snapshot test of it's major working states (example: do not make a snapshot for every resolution in a chart, more snapshots just slows down the tests) and one of it's error states if it has one or more

  ```jsx
  // A snapshot test looks something like this
  describe('@/components/finance/AnalysisWidgets/EarningsPreview', () => {
   
    ...
    
    test('it renders with mock data', () => {
      const tree = render(<EarningsPreview analysis={MOCK_ANALYSIS} />);
      expect(tree).toMatchSnapshot();
    });
  });
  ```

- Bug fixes should always be accompanied by a corresponding regression test

  > Usually a regression is going to be some form of UI regression that wasn't noticed. In that case, simply making sure that the component that broke has a good snapshot test is all that is needed.

- Be cautious about stubs and mocks - they can make your tests more brittle

- TestIDs are named in a specific way. They must be in the form `<component name>.<purpose>.<index if applicable>`. Let's say we have a `LikedList` that shows all of the user's liked posts. The first item in the list would have the testId `likedList.post.0`.

- Strive to write many small pure functions, and minimize where mutations occur

- 100% test coverage is a good goal to strive for, even if it’s not always practical to reach it

## Comments & Docstrings

- Make use of `TODO`s, `FIXME`s and `NOTE`s where it makes sense

  ```jsx
  // TODO for things that need to be done
  // FIXME for things that don't work as they should
  // NOTE for implementation details, edge cases, etc

  const calculateEPS = () => {
    // TODO: finish implementing eps calculation
    return 0;
  }

  // FIXME: not returning expected values
  const square(x) => x * 2;

  // NOTE: this component expects xyz to be present in order to work properly
  const ComplicatedComponent = ({props}) => { ... } 
  ```

- If you comment something out because you don't need it, delete it instead

  ```jsx
  // bad
  const styles = StyleService.create({
    featureContainer: {
      flex: 1,
      // justifyContent: 'space-between'
    }
  });

  // an exception
  // it is acceptable to comment code that will be uncommented in the future
  // just make sure to note it with a FIXME or TODO
  const styles = StyleService.create
    // TODO: Style for a currently disabled component that needs some work
    // featureContainer: {
    //  flex: 1,
    // }
  });
  ```

- New components and screens should always have a good docstring with a general description, list of params and what they are, and any important info you want to communicate to other devs who will come across it

  ```jsx
  /**
   * Widget component for showing data with a particular color and style
   * @param data {Object} data object to be rendered, should be in xyz format otherwise it will not render properly
   * @param [color='highlight'] {String} optional color string to color data with
   * @param [style={}] {Style} optional style to apply to container
   */
  const Widget = ({data, color = 'highlight', style = {}}) => { ... }
  ```

## Imports & Exports

- Use modules

  ```jsx
  // bad
  const MyLib = require('@/path/MyLib');
  module.exports = MyLib.doThing;
  
  // getting there
  import doThing from '@/path/MyLib';
  export default MyLib.doThing;
  
  // best
  import {doThing} from '@/path/MyLib';
  export default doThing;
  ```


- Use `@` imports over relative imports

  ```jsx
  // bad
  import {MyLib} from './MyLib';
  
  // good
  import {MyLib} from '@/path/MyLib';
  ```

- Avoid wildcard imports
  > Why? This makes sure you have a single default export.

  ```jsx
  // bad
  import * as MyLib from '@/path/MyLib';
  
  // good
  // but note this will fail if you don't have a default export
  import MyLib from '@/path/MyLib';
  ```

- Only import from a path in one place

  ```jsx
  // bad
  import MyLib from '@/path/MyLib';
  import {doThing, doOtherThing} from '@/path/MyLib';
  
  // good
  import MyLib, {doThing, doOtherThing} from '@/path/MyLib';
  ```

- Don't export mutable references

  ```jsx
  // bad
  let a = 1;
  export {a};
  
  // good
  const a = 1;
  export {a};
  ```

## Whitespace

- ESLint will automatically take care of most whitespace problems automatically

- Sometimes a bit of extra whitespace is a good idea to improve readability. If code sections have little to do with each other, inserting whitespace between them is a good idea

  ```jsx
  // variable declarations should be split from function declarations for instance, and individual functions/components/returns should have whitespace between them
  const [error, setError] = useState(null);
  const theme = useTheme();
  const color = value > 0 ? theme.positive : theme.negative;
  
  const loadOnce = useEffect(() => {
    ...
  }, []);
  
  const Header = useCallback(() => (
    <View>
      <AppIcon name="style-guide-icon" />
    	<Text category="h3">yo</Text>
    </View>
  ), [data]);
  
  return {
     <View>
      <Header />
      {error && 
        <View>
      	<Text>{error}</Text>
        </View>
      }
    </View>
  }
  ```

## Semicolons

- Add them (ESLint will do it for you, but you might be interested in why)

  > When JavaScript encounters a line break without a semicolon, it uses a set of rules called [Automatic Semicolon Insertion](https://tc39.github.io/ecma262/#sec-automatic-semicolon-insertion) to determine whether it should regard that line break as the end of a statement, and (as the name implies) place a semicolon into your code before the line break if it thinks so. ASI contains a few eccentric behaviors, though, and your code will break if JavaScript misinterprets your line break. These rules will become more complicated as new features become a part of JavaScript. Explicitly terminating your statements and configuring your linter to catch missing semicolons will help prevent you from encountering issues.