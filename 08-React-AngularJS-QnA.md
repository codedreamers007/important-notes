# React & AngularJS - Interview Q&A Guide

## Table of Contents
1. [React Fundamentals](#react-fundamentals)
2. [React Advanced Concepts](#react-advanced-concepts)
3. [React Hooks](#react-hooks)
4. [React Performance Optimization](#react-performance-optimization)
5. [AngularJS Fundamentals](#angularjs-fundamentals)
6. [AngularJS Advanced Concepts](#angularjs-advanced-concepts)
7. [Comparison: React vs AngularJS](#comparison-react-vs-angularjs)
8. [Common Interview Questions](#common-interview-questions)

---

## React Fundamentals

### What is React?

**Definition**: React is a JavaScript library for building user interfaces, particularly web applications.

**Key Characteristics**:
- **Component-Based**: Build UIs using reusable components
- **Declarative**: Describe what UI should look like
- **Virtual DOM**: Efficient updates using virtual representation
- **Unidirectional Data Flow**: Data flows down, events flow up
- **JSX**: JavaScript syntax extension for writing HTML-like code

### Core Concepts

**1. Components**:
- Building blocks of React applications
- Can be functional or class-based
- Reusable and composable
- Encapsulate UI and logic

**2. JSX (JavaScript XML)**:
- Syntax extension for JavaScript
- Write HTML-like code in JavaScript
- Transpiled to JavaScript
- Must return single element (or Fragment)

**3. Props (Properties)**:
- Pass data from parent to child
- Read-only (immutable)
- One-way data flow
- Can be any JavaScript type

**4. State**:
- Component's internal data
- Can change over time
- Triggers re-renders when updated
- Managed with `useState` hook or `this.state`

**5. Virtual DOM**:
- Lightweight copy of real DOM
- React updates virtual DOM first
- Compares with previous version (diffing)
- Updates only changed parts (reconciliation)

### Basic React Example

```jsx
// Functional Component
function Welcome(props) {
  return <h1>Hello, {props.name}!</h1>;
}

// Using the component
<Welcome name="John" />

// With Hooks
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

---

## React Advanced Concepts

### Component Lifecycle

**Class Components Lifecycle**:
1. **Mounting**: `constructor` → `render` → `componentDidMount`
2. **Updating**: `shouldComponentUpdate` → `render` → `componentDidUpdate`
3. **Unmounting**: `componentWillUnmount`

**Functional Components (Hooks)**:
- `useEffect` replaces lifecycle methods
- `useEffect(() => {}, [])` - componentDidMount
- `useEffect(() => { return () => {} }, [deps])` - componentDidUpdate + cleanup
- `useEffect(() => { return () => {} }, [])` - componentWillUnmount

### State Management

**Local State**:
- `useState` hook for functional components
- `this.state` for class components
- Component-specific data

**Lifting State Up**:
- Move state to common ancestor
- Pass down as props
- Share state between siblings

**Context API**:
- Share data without prop drilling
- Create context with `createContext`
- Provide with `Context.Provider`
- Consume with `useContext`

**Redux**:
- Global state management
- Single source of truth
- Predictable state updates
- Time-travel debugging

### Event Handling

```jsx
// Inline handler
<button onClick={() => console.log('clicked')}>
  Click me
</button>

// Named function
function handleClick() {
  console.log('clicked');
}
<button onClick={handleClick}>Click me</button>

// With parameters
<button onClick={(e) => handleClick(id, e)}>
  Click me
</button>

// Prevent default
function handleSubmit(e) {
  e.preventDefault();
  // form logic
}
```

### Conditional Rendering

```jsx
// If statement
{isLoggedIn && <UserMenu />}

// Ternary operator
{isLoading ? <Spinner /> : <Content />}

// Multiple conditions
{status === 'loading' && <Spinner />}
{status === 'error' && <Error />}
{status === 'success' && <Data />}
```

### Lists and Keys

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <TodoItem key={todo.id} todo={todo} />
      ))}
    </ul>
  );
}
```

**Key Points**:
- Keys help React identify changed items
- Use stable, unique identifiers
- Index as key only if list is static
- Keys must be unique among siblings

---

## React Hooks

### useState Hook

**Purpose**: Add state to functional components

```jsx
const [state, setState] = useState(initialValue);

// Example
const [count, setCount] = useState(0);
const [name, setName] = useState('');

// Update state
setCount(count + 1);
setCount(prevCount => prevCount + 1); // Functional update
```

**Key Points**:
- Returns array: [state, setter]
- State updates are asynchronous
- Use functional updates for dependent updates
- State is preserved between re-renders

### useEffect Hook

**Purpose**: Perform side effects in functional components

```jsx
// Run on every render
useEffect(() => {
  // side effect
});

// Run once on mount
useEffect(() => {
  // side effect
}, []);

// Run when dependencies change
useEffect(() => {
  // side effect
}, [dependency1, dependency2]);

// Cleanup
useEffect(() => {
  const subscription = subscribe();
  return () => {
    subscription.unsubscribe(); // cleanup
  };
}, []);
```

**Use Cases**:
- Data fetching
- Subscriptions
- DOM manipulation
- Timers
- Cleanup operations

### useContext Hook

**Purpose**: Access React context

```jsx
const ThemeContext = createContext('light');

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  const theme = useContext(ThemeContext);
  return <div className={theme}>Content</div>;
}
```

### useReducer Hook

**Purpose**: Manage complex state with reducer pattern

```jsx
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });
  
  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>
        +
      </button>
    </div>
  );
}
```

### useMemo Hook

**Purpose**: Memoize expensive calculations

```jsx
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(a, b);
}, [a, b]); // Only recompute if a or b changes
```

### useCallback Hook

**Purpose**: Memoize functions

```jsx
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]); // Only recreate if a or b changes
```

### Custom Hooks

**Purpose**: Extract reusable logic

```jsx
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);
  
  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  const reset = () => setCount(initialValue);
  
  return { count, increment, decrement, reset };
}

// Usage
function Counter() {
  const { count, increment, decrement } = useCounter(0);
  return (
    <div>
      <p>{count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </div>
  );
}
```

---

## React Performance Optimization

### React.memo

**Purpose**: Prevent unnecessary re-renders

```jsx
const MemoizedComponent = React.memo(function MyComponent({ name }) {
  return <div>{name}</div>;
}, (prevProps, nextProps) => {
  // Custom comparison
  return prevProps.name === nextProps.name;
});
```

### useMemo and useCallback

- `useMemo`: Memoize values
- `useCallback`: Memoize functions
- Prevent unnecessary recalculations
- Optimize child component re-renders

### Code Splitting

```jsx
// Lazy loading
const LazyComponent = React.lazy(() => import('./LazyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </Suspense>
  );
}
```

### Virtualization

- Render only visible items
- Use libraries like `react-window`
- Handle large lists efficiently

### Best Practices

1. **Avoid inline object/function creation in render**
2. **Use keys properly**
3. **Split large components**
4. **Memoize expensive calculations**
5. **Code split routes**
6. **Use production build**

---

## AngularJS Fundamentals

### What is AngularJS?

**Definition**: AngularJS (Angular 1.x) is a JavaScript framework for building dynamic web applications.

**Key Characteristics**:
- **MVC Architecture**: Model-View-Controller pattern
- **Two-Way Data Binding**: Automatic synchronization
- **Dependency Injection**: Built-in DI system
- **Directives**: Extend HTML with custom attributes
- **Services**: Reusable business logic

### Core Concepts

**1. Modules**:
```javascript
var app = angular.module('myApp', []);
```

**2. Controllers**:
```javascript
app.controller('MyController', function($scope) {
  $scope.message = 'Hello AngularJS';
});
```

**3. Directives**:
- Built-in: `ng-app`, `ng-controller`, `ng-model`, `ng-repeat`
- Custom directives for reusable components

**4. Two-Way Data Binding**:
```html
<input ng-model="name" />
<p>Hello, {{name}}!</p>
<!-- Changes in input automatically update paragraph -->
```

**5. Services**:
- Singleton objects
- Share data and logic
- Built-in: `$http`, `$location`, `$timeout`
- Custom services

### Basic AngularJS Example

```html
<!DOCTYPE html>
<html ng-app="myApp">
<head>
  <script src="angular.js"></script>
</head>
<body ng-controller="MyController">
  <input ng-model="name" placeholder="Enter name" />
  <h1>Hello, {{name}}!</h1>
  
  <script>
    var app = angular.module('myApp', []);
    app.controller('MyController', function($scope) {
      $scope.name = 'World';
    });
  </script>
</body>
</html>
```

### Directives

**Built-in Directives**:
- `ng-app`: Bootstrap AngularJS app
- `ng-controller`: Attach controller
- `ng-model`: Two-way data binding
- `ng-repeat`: Loop through array
- `ng-if`: Conditional rendering
- `ng-show/ng-hide`: Toggle visibility
- `ng-click`: Click handler
- `ng-bind`: One-way binding

**Custom Directives**:
```javascript
app.directive('myDirective', function() {
  return {
    restrict: 'E', // E = element, A = attribute
    template: '<div>Custom directive</div>',
    scope: {
      data: '='
    },
    link: function(scope, element, attrs) {
      // DOM manipulation
    }
  };
});
```

### Services

**Built-in Services**:
- `$http`: HTTP requests
- `$location`: URL manipulation
- `$timeout`: setTimeout wrapper
- `$interval`: setInterval wrapper
- `$rootScope`: Root scope
- `$q`: Promises

**Custom Services**:
```javascript
// Factory
app.factory('UserService', function($http) {
  return {
    getUsers: function() {
      return $http.get('/api/users');
    }
  };
});

// Service
app.service('UserService', function($http) {
  this.getUsers = function() {
    return $http.get('/api/users');
  };
});
```

### Dependency Injection

```javascript
app.controller('MyController', function($scope, $http, UserService) {
  // Dependencies injected automatically
  $scope.users = [];
  
  UserService.getUsers().then(function(response) {
    $scope.users = response.data;
  });
});
```

---

## AngularJS Advanced Concepts

### Scope

**$scope**:
- Glue between controller and view
- Contains model data
- Watches for changes
- Two-way data binding

**$rootScope**:
- Parent of all scopes
- Global scope
- Shared across app

**Scope Inheritance**:
- Child scopes inherit from parent
- Isolated scopes in directives

### Digest Cycle

**How It Works**:
1. User interaction triggers event
2. Event handler updates model
3. AngularJS checks for changes (digest cycle)
4. Updates DOM if changes detected
5. Repeats until stable

**$apply()**:
- Manually trigger digest cycle
- Needed for external events
- `$timeout` and `$http` call automatically

### Filters

**Built-in Filters**:
```html
{{ name | uppercase }}
{{ price | currency }}
{{ date | date:'short' }}
{{ items | filter:searchText }}
{{ items | orderBy:'name' }}
{{ items | limitTo:10 }}
```

**Custom Filters**:
```javascript
app.filter('reverse', function() {
  return function(text) {
    return text.split('').reverse().join('');
  };
});
```

### Routing

**ngRoute**:
```javascript
app.config(function($routeProvider) {
  $routeProvider
    .when('/', {
      templateUrl: 'home.html',
      controller: 'HomeController'
    })
    .when('/about', {
      templateUrl: 'about.html',
      controller: 'AboutController'
    })
    .otherwise({
      redirectTo: '/'
    });
});
```

**ui-router** (Third-party):
- More powerful routing
- Nested views
- Multiple named views
- State-based routing

### Promises

```javascript
$http.get('/api/data')
  .then(function(response) {
    // Success
    $scope.data = response.data;
  })
  .catch(function(error) {
    // Error
    console.error(error);
  });
```

---

## Comparison: React vs AngularJS

### Architecture

**React**:
- Library (not framework)
- Component-based
- Unidirectional data flow
- Virtual DOM
- JSX syntax

**AngularJS**:
- Full framework
- MVC architecture
- Two-way data binding
- Real DOM manipulation
- HTML templates

### Data Binding

**React**:
- One-way data flow
- Props down, events up
- Explicit state management
- Predictable updates

**AngularJS**:
- Two-way data binding
- Automatic synchronization
- $scope watches
- Can be less predictable

### Performance

**React**:
- Virtual DOM for efficiency
- Optimized re-renders
- Better for large apps
- Manual optimization needed

**AngularJS**:
- Direct DOM manipulation
- Digest cycle overhead
- Can be slower with many watchers
- Performance issues with large apps

### Learning Curve

**React**:
- Simpler core concepts
- More ecosystem choices
- JSX learning curve
- Flexible but requires decisions

**AngularJS**:
- More concepts to learn
- Opinionated framework
- Steeper learning curve
- More built-in features

### Use Cases

**React**:
- Modern SPAs
- Component libraries
- Mobile apps (React Native)
- Large-scale applications
- When flexibility is needed

**AngularJS**:
- Legacy applications
- Rapid prototyping
- When framework structure needed
- Enterprise applications (older)
- Note: AngularJS is deprecated, use Angular (2+) for new projects

---

## Common Interview Questions

### React Questions

**Q1: What is the Virtual DOM and how does it work?**

**Answer**:
- Virtual DOM is a lightweight JavaScript representation of the real DOM
- React creates virtual DOM tree in memory
- When state changes, React creates new virtual DOM tree
- React compares (diffs) new tree with previous tree
- React updates only changed parts in real DOM (reconciliation)
- Benefits: Faster updates, batched changes, efficient rendering

**Q2: Explain the difference between props and state.**

**Answer**:
- **Props**: Passed from parent, read-only, immutable, external data
- **State**: Internal to component, can change, mutable, local data
- Props flow down, state managed within component
- State changes trigger re-renders, props changes trigger re-renders in child

**Q3: What are React Hooks and why were they introduced?**

**Answer**:
- Hooks allow using state and lifecycle in functional components
- Introduced to solve: class complexity, wrapper hell, reuse logic
- Common hooks: useState, useEffect, useContext, useReducer
- Rules: Only call at top level, only in React functions
- Benefits: Simpler code, better code reuse, easier testing

**Q4: Explain the component lifecycle in React.**

**Answer**:
**Class Components**:
- Mounting: constructor → render → componentDidMount
- Updating: shouldComponentUpdate → render → componentDidUpdate
- Unmounting: componentWillUnmount

**Functional Components (Hooks)**:
- useEffect with empty deps = componentDidMount
- useEffect with deps = componentDidUpdate
- useEffect cleanup = componentWillUnmount

**Q5: How do you optimize React performance?**

**Answer**:
- Use React.memo for component memoization
- useMemo for expensive calculations
- useCallback for function memoization
- Code splitting with React.lazy
- Virtualization for long lists
- Avoid inline object/function creation
- Use production build
- Profile with React DevTools

**Q6: What is the difference between controlled and uncontrolled components?**

**Answer**:
**Controlled**:
- Form data handled by React state
- Value controlled by state
- onChange updates state
- Single source of truth

**Uncontrolled**:
- Form data handled by DOM
- Use refs to access values
- Less React code
- Can use for simple forms

**Q7: Explain Redux and when to use it.**

**Answer**:
- Redux is state management library
- Single source of truth (store)
- State is read-only (immutable)
- Changes via pure functions (reducers)
- Predictable state updates
- Use when: Complex state, shared state, time-travel debugging needed

**Q8: What is JSX and how does it work?**

**Answer**:
- JSX is JavaScript syntax extension
- Write HTML-like code in JavaScript
- Transpiled to React.createElement calls
- Must return single element (or Fragment)
- Can embed JavaScript expressions with {}
- Not required but recommended

### AngularJS Questions

**Q1: Explain two-way data binding in AngularJS.**

**Answer**:
- Automatic synchronization between model and view
- Changes in model update view
- Changes in view update model
- Uses $watch and $apply
- Digest cycle checks for changes
- Can have performance issues with many watchers

**Q2: What is the digest cycle?**

**Answer**:
- AngularJS checks for model changes
- Runs $watchers to detect changes
- Updates DOM if changes found
- Continues until no more changes (dirty checking)
- Can be triggered manually with $apply()
- Performance can degrade with many watchers

**Q3: Explain dependency injection in AngularJS.**

**Answer**:
- Built-in DI system
- Dependencies injected automatically
- Services registered with module
- Injected via function parameters
- Benefits: Testability, modularity, loose coupling
- Example: Controller receives $scope, $http automatically

**Q4: What are directives in AngularJS?**

**Answer**:
- Extend HTML with custom attributes/elements
- Built-in: ng-app, ng-controller, ng-model, ng-repeat
- Custom directives for reusable components
- Types: Element (E), Attribute (A), Class (C), Comment (M)
- Can have isolated scope
- Link function for DOM manipulation

**Q5: Explain the difference between factory, service, and provider.**

**Answer**:
**Factory**:
- Returns object/function
- Can return anything
- Most flexible

**Service**:
- Constructor function
- New instance created
- Returns service object

**Provider**:
- Configurable before app starts
- Can be configured in config phase
- Most complex, most flexible

**Q6: How does AngularJS handle routing?**

**Answer**:
- ngRoute module for basic routing
- $routeProvider configures routes
- Maps URLs to templates/controllers
- $location service for URL manipulation
- ui-router for advanced routing (nested views, states)

**Q7: What is $scope and $rootScope?**

**Answer**:
**$scope**:
- Glue between controller and view
- Contains model data
- Child scopes inherit from parent
- Isolated scopes in directives

**$rootScope**:
- Parent of all scopes
- Global scope
- Shared across entire app
- Use sparingly

**Q8: How do you optimize AngularJS performance?**

**Answer**:
- Limit watchers (use one-time binding {{::value}})
- Use track by in ng-repeat
- Avoid deep watching
- Use $apply() carefully
- Debounce ng-model
- Use filters sparingly
- Consider pagination/virtualization
- Profile with Batarang

---

## Key Takeaways

### React
1. **Component-Based**: Build with reusable components
2. **Virtual DOM**: Efficient updates via virtual representation
3. **Unidirectional Flow**: Props down, events up
4. **Hooks**: Modern way to use state and lifecycle
5. **Performance**: Optimize with memoization and code splitting
6. **Ecosystem**: Large ecosystem, many choices

### AngularJS
1. **Two-Way Binding**: Automatic model-view synchronization
2. **MVC**: Model-View-Controller architecture
3. **Dependency Injection**: Built-in DI system
4. **Directives**: Extend HTML functionality
5. **Digest Cycle**: Change detection mechanism
6. **Note**: AngularJS is deprecated, use Angular (2+) for new projects

### When to Use What
- **React**: Modern apps, flexibility needed, large ecosystem
- **AngularJS**: Legacy apps, rapid prototyping (but deprecated)
- **Angular (2+)**: Enterprise apps, full framework needed, TypeScript

---

## Advanced Topics

### React Advanced

**1. Error Boundaries**:
```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }
  
  componentDidCatch(error, errorInfo) {
    console.error(error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

**2. Context API**:
- Share data without prop drilling
- Create context, provide value, consume with useContext
- Use for theme, user data, language

**3. Higher-Order Components (HOC)**:
- Function that takes component, returns enhanced component
- Reuse component logic
- Example: withAuth, withRouter

**4. Render Props**:
- Component with render prop function
- Share code between components
- Alternative to HOCs

### AngularJS Advanced

**1. Custom Directives**:
- Create reusable components
- Isolated scope for encapsulation
- Link function for DOM manipulation
- Compile function for template manipulation

**2. Services Best Practices**:
- Use factories for most cases
- Services for constructor-like behavior
- Providers for configuration
- Keep services focused and testable

**3. Testing**:
- Unit tests with Jasmine/Karma
- E2E tests with Protractor
- Mock services and dependencies
- Test controllers, services, directives

---

## Best Practices

### React
1. Keep components small and focused
2. Use functional components with hooks
3. Extract reusable logic to custom hooks
4. Use TypeScript for type safety
5. Follow component composition over inheritance
6. Optimize re-renders with memoization
7. Handle errors with error boundaries
8. Use proper key props in lists

### AngularJS
1. Limit watchers for performance
2. Use one-time binding when possible
3. Avoid deep watching
4. Use track by in ng-repeat
5. Keep controllers thin
6. Move logic to services
7. Use directives for reusable components
8. Test your code

---

## Resources

- React Official Documentation
- AngularJS Official Documentation (legacy)
- React Hooks Documentation
- Redux Documentation
- React Router Documentation
- AngularJS Style Guide

