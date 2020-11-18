# React

[Return to Table of Contents](/README.md)

## **General**

### **Use Pure Component or Stateless Component if possible**

```javascript
export const User = (props) => <User {...props} />;
```

### **Use only functional components**

#### **use:**

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

#### **instead of:**

```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

## **PropTypes**

PropTypes should be used whenever possible. If you see any code that doesn't have proptypes it would be appreciated to fix it, open a github issue, or inform the author.
For more information on using proptypes see [**here**](https://github.com/facebook/prop-types).

## **Code Structure**

When determining where in the file tree a component should go, reffer to the following list:
- Screen stay in screens folder
- Logical Hooks seperated out of a component should be in the Hooks folder
- If a screen/set of screens gets split into too many single-use components (e.g. Brefings.js, ChangePassWordModal.js) it can get its own folder: eg. finance, social, news, etc. in components. These can get more specific if need be
- Any general UI components such as SlidingSelectButton should be in the UI folder.
- Any single-use component that can be turned into a general UI component should be, even if its only used in once place (how SlidingSelectButton was for originally only for payments)
