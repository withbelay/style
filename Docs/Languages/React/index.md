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
