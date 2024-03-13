---
title: "React concepts you must-know!"
last_modified_at: 2024-04-03
categories:
    - frontend
tags:
    - frontend
    - react
    - ui
    - modern
header: 
  teaser: /assets/images/collections/pages/terraform-articles.jpg
toc: true
excerpt: "Most important concepts to get started with react covered in this post."
---

[Checkout my first react project!](https://github.com/k-pulkit/react-store-app-demo){: .btn .btn--info}

My approach to learning react has been hands on. Watch some basic videos to familiarize yourself and just start. I recommend using Javascript mastery to practice the concepts.
{: .notice--warning}


## Introduction to React

React is a JavaScript library for building user interfaces. It allows developers to create interactive UI components that respond to user input. React uses a declarative approach, meaning you describe the desired outcome and let React handle the details of updating the UI when the underlying data changes.

## Components

Components are the building blocks of a React application. They are reusable, self-contained pieces of UI that can be composed together to create complex interfaces. There are two main types of components in React: functional components and class components.

### Functional Components

Functional components are JavaScript functions that return JSX (a syntax extension for JavaScript). 

```jsx
const Welcome = (props) => {
  return <h1>Hello, {props.name}</h1>;
};
```
They are simpler and easier to understand than class components.

### Class Components

Class components are ES6 classes that extend `React.Component`. They have additional features such as state and lifecycle methods.

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

## JSX

JSX allows you to write HTML-like code in your JavaScript files. It makes your React components more readable and expressive.

```jsx
const element = <h1>Hello, world!</h1>;
ReactDOM.render(element, document.getElementById('root'));
```

## Props

Props (short for properties) are used to pass data from parent components to child components in React. They are read-only and cannot be modified within the child component.

```jsx
const Welcome = (props) => {
  return <h1>Hello, {props.name}</h1>;
};

const element = <Welcome name="Alice" />;
ReactDOM.render(element, document.getElementById('root'));
```

## State

State is used to manage the internal state of a component in React. It allows components to change their output over time in response to user actions, network responses, and other events. State is declared using the `useState` hook in functional components.

```jsx
const Clock = () => {
  const [date, setDate] = useState(new Date());

  useEffect(() => {
    const timerID = setInterval(() => setDate(new Date()), 1000);
    return () => clearInterval(timerID);
  }, []);

  return <div>{date.toLocaleTimeString()}</div>;
};
```

## State Management

As your React applications grow in complexity, you may need a more robust solution for state management. Two common approaches are using Redux Toolkit and Context API.

### Redux Toolkit

Redux is a state management library for JavaScript applications, and Redux Toolkit is its official toolset for simplifying Redux development. It helps you write Redux logic more efficiently with fewer lines of code.

```jsx
import { configureStore } from '@reduxjs/toolkit';

const store = configureStore({
  reducer: {
    // Define your reducers here
  },
});

// Access the store's state
const state = store.getState();
```

### Context API

Context API is a feature in React that allows you to pass data through the component tree without having to pass props down manually at every level. It's useful for sharing data that needs to be accessible by many components.

```jsx
import React, { createContext, useContext } from 'react';

const MyContext = createContext();

const MyProvider = ({ children }) => {
  const sharedState = { /* Your shared state */ };

  return (
    <MyContext.Provider value={sharedState}>
      {children}
    </MyContext.Provider>
  );
};

const useSharedState = () => useContext(MyContext);

// Usage
const Component = () => {
  const sharedState = useSharedState();
  // Use sharedState
};
```

## Conclusion

These are some of the fundamental concepts every beginner should know when starting with React using functional components. Understanding state management using Redux Toolkit and Context API can help you build more complex and maintainable React applications. Happy coding!

