# Javascript

[Return to Table of Contents](/README.md)

## **JS Run Time**

Currently AWS supports up to Node 12, all javascript code running on AWS must run in Node 12.  
For development purposes both `LTS` and `Current` versions of Node are fine.  
**Note:** React Native code must be able to run on JsCore because Apple is restrictive and doesn't allow other javascript engines on iOS

## **Comments**

Use JsDoc to comment functions whenever possible, not necessary for functions that aren't exported, but still highly preferred. All backend endpoints/websockets must be documented with ApiDoc.

Comments describing design decisions and explaining more complicated code are useful and encouraged.

## **NPM and Yarn**

All Projects should be using NPM to manage dependencies. React and React Native projects are exempt and should use yarn instead.

## **Modules**

### **where to import**

Imports should for the most part be located at the very top of the file. Notable exceptions include Express.js routers on the backend.

### **import order**

There is no set import order across files, but within a file imports should typically be separated by `node_modules` and local files.

### **default export**

When creating modules don't use default export. Notable exceptions include React / ReactNative components.

## **Don't Trust Data!**

Assume that data being sent can be corrupted/tampered with/wrong etc.

### **For Backend**

1. Always validate users on the backend before allowing them to make changes
    1. server should return relevant `4xx` error
    2. websocket server should kick the client off
2. Always validate information that the user/client sends to the server
    1. server should return relevant `4xx` error
    2. websocket server should kick the client off

### **For Frontend**

Data being sent from the server to the user/client doesn't need to be validated but the request should be wrapped in a `try/catch` block.  
Data fields from server response should be checked for existence before they are used.

## **Ternary Statements**

Ternary statements are good for reducing code size while maintaining readability. It is up to the author to determine when ternarys will make code more readable and when they won't.  

### **use:**

```javascript
return number % 2 === 0 ? true : false;
```

### **instead of:**

```javascript
if(number % 2 === 0) {
    return true;
} else {
    return false;
}
```

## **JS Destructuring**

Destructuring of both objects and arrays is preferred whenever possible.

### **use:**

```javascript
const x = [10, 20];
const y = {o: 10, t:20};
const [a, b] = x;
const {o} = y;
```

### **instead of:**

```javascript
const x = [10, 20];
const y = {o: 10, t:20};
const a = x[0];
const b = x[1];
const o = y.o;
```

## **Asynchronous Code**

### **Async/Await vs. Promises vs. Callbacks**

Whenever possible use `async/await` over Promises and callbacks.

If 2 or more async functions both need to be awaited, but aren't reliant on each other use `Promise.all` like so:

```javascript
await Promise.all([
    asyncFunctionA(),
    asyncFunctionV(),
]);
```

**NOTE:** `async/await` is really just a wrapper around Promises. a Promise can be awaited to be resolved. and an async function just returns a Promise that resolves to the return value. The use of `async/await` is preferred purely for readability and to reduce confusion.

**NOTE:** `Promise.all` is *technically* parallel the vast majority of the time the functions will be blocking and will be executed concurrently. This will rarely matter, but it is something to be aware of if expecting true parallelization.

### **Error Catching**

All `async/await` functions should be wrapped in a `try/catch` block  
All `Promises` should have error catching using `.catch(e)`


