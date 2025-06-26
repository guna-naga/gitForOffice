Component is a reusable self-contained piece of UI.
Components are independent and reusable bits of code. They serve the same purpose as JavaScript functions, but return HTML. 
Components come in two types, Class components and Function components.


Props stands for properties and is used for passing data from one component to another.
React Props are like function arguments in JavaScript and attributes in HTML.
props are passed in a unidirectional flow.props are read-only which means child component cannot modify them directly.
Props are passed to components via HTML attributes.


React Router is used to create an application with multiple page routes.
To add React Router in your application, run this in the terminal  ->[npm i -D react-router-dom].
import { BrowserRouter, Routes, Route } from "react-router-dom";




Hooks allow function components to have access to state and other React features.You must import Hooks from react.
There are 3 rules for hooks:
Hooks can only be called inside React function components.
Hooks can only be called at the top level of a component.
Hooks cannot be conditional


What is the useState?
useState is a React Hook that allows the functional component to manage its state. 
it returns an array with two values(current state , Function to update state).
const [state, setState] = useState(initialState)


The useEffect Hook allows the functional component to perform side effects in your components.
Some examples of side effects are: fetching data, directly updating the DOM, and timers.
useEffect accepts two arguments. The second argument is optional.
useEffect(<function>, <dependency>)


React Context is a way to manage state globally.
To create context, you must Import createContext, useContext and initialize it:
It can be used together with the useState Hook to share state between deeply nested components more easily than with useState alone.
In order to use the Context in a child component, we need to access it using the useContext Hook.


The useRef Hook allows you to persist values between renders.
It can be used to access a DOM element directly.
useRef() only returns one item. It returns an Object called current.
When we initialize useRef we set the initial value: useRef(0).


The React useCallback Hook returns a memoized callback function.
One reason to use useCallback is to prevent a component from re-rendering unless its props have changed.
The useCallback Hook only runs when one of its dependencies update.
The useCallback and useMehmo Hooks are similar. The main difference is that useMemo returns a memoized value and useCallback returns a memoized function.


The React useMemo Hook returns a memoized value.
The useMemo Hook only runs when one of its dependencies update.

