# React & Redux in TypeScript - Static Typing Guide
**_"This guide is about to teach you how to leverage [Type Inference](https://www.typescriptlang.org/docs/handbook/type-inference.html), [Generics](https://www.typescriptlang.org/docs/handbook/generics.html) and other [Advanced Types](https://www.typescriptlang.org/docs/handbook/advanced-types.html) as much as possible to write the minimal amount of type annotations needed for your JavaScript code to be completely Type Safe"_** - this will make sure you get all the benefits of Static Typing and won't slow down your productivity by adding unnecessary typings.

> #### _Found it usefull? Want more updates?_ [**Give it a :star2:**](https://github.com/piotrwitek/react-redux-typescript-patterns/stargazers)  

### Goals
- Complete type safety with [`--strict`](https://www.typescriptlang.org/docs/handbook/compiler-options.html) flag without failing to `any` type for the best static-typing experience
- Minimize amount of manually writing type declarations by leveraging [Type Inference](https://www.typescriptlang.org/docs/handbook/type-inference.html)
- Reduce redux boilerplate and complexity of it's type annotations to a minimum with [simple utility functions](https://github.com/piotrwitek/react-redux-typescript) by extensive use of [Generics](https://www.typescriptlang.org/docs/handbook/generics.html) and [Advanced Types](https://www.typescriptlang.org/docs/handbook/advanced-types.html) features

### Playground Project
You should check Playground Project located in the `/playground` folder. It is a source of all the code examples found in the guide. They are all tested with the most recent version of TypeScript and 3rd party type definitions (like `@types/react` or `@types/react-redux`) to ensure the examples are up-to-date and not broken with updated definitions.
> Playground was created is such ą way, that you can easily clone repository locally and immediately play around on your own to learn all the examples from this guide in a real project environment without complicated setup.

---

## Table of Contents
- [Setup](#setup)
- [React Types Cheatsheet](#react-types-cheatsheet) 🌟 __NEW__
- [Component Typing Patterns](#component-typing-patterns)
  - [Stateless Components - SFC](#stateless-components---sfc)
  - [Stateful Components - Class](#stateful-components---class)
  - [Generic Components](#generic-components)
  - [Higher-Order Components](#higher-order-components) 📝 __UPDATED__
  - [Redux Connected Components](#redux-connected-components)
- [Redux](#redux)
  - [Action Creators](#action-creators)
  - [Reducers](#reducers)
  - [Store Configuration](#store-configuration)
  - [Async Flow](#async-flow) _("redux-observable")_
  - [Selectors](#selectors) _("reselect")_
- [Tools](#tools)
  - [Living Style Guide](#living-style-guide) _("react-styleguidist")_ 🌟 __NEW__
- [Extras](#extras)
  - [tsconfig.json](#tsconfigjson)
  - [tslint.json](#tslintjson)
  - [jest.config.json](#jestconfigjson)  
  - [Default and Named Module Exports](#default-and-named-module-exports)
  - [Vendor Types Augmentation](#vendor-types-augmentation)
  - [Npm Scripts](#npm-scripts)
- [FAQ](#faq)
- [Roadmap](#roadmap)
- [Contribution Guide](#contribution-guide)
- [Project Examples](#project-examples)

---

# Setup

### Installing types
```
npm i -D @types/react @types/react-dom @types/react-redux
```

"react" - `@types/react`  
"react-dom" - `@types/react-dom`  
"redux" - (types included with npm package)*  
"react-redux" - `@types/react-redux`  

> `redux` has improved types on a `next` branch in it's official github repo, use below instructions to add it to your project:
- in `package.json > devDependencies` add:  
  `"redux-next": "reactjs/redux#next"`  
- in `tsconfig.json > compilerOptions > paths` add:  
  `"redux": ["node_modules/redux-next"]`  

[⇧ back to top](#table-of-contents)

---

# React Types Cheatsheet

#### `React.StatelessComponent<P>` or alias `React.SFC<P>` 
Stateless functional components
```tsx
const MyComponent: React.SFC<MyComponentProps> = ...
```
[⇧ back to top](#table-of-contents)

#### `React.Component<P, S>`
Statefull class component
```tsx
class MyComponent extends React.Component<MyComponentProps, State> { ...
```
[⇧ back to top](#table-of-contents)

#### `React.ComponentType<P>`
Accepts sfc or class components with Generic Props Type
```tsx
const withState = <P extends WrappedComponentProps>(
  WrappedComponent: React.ComponentType<P>,
) => { ...
```
[⇧ back to top](#table-of-contents)

#### `React.ReactNode`
Accepts any react elements (component instances) and also primitive types
```tsx
const elementOrPrimitive: React.ReactNode = '' || 0 || false || null || <div /> || <MyComponent />;
```
[⇧ back to top](#table-of-contents)

#### `JSX.Element`
Similar in usage to ReactNode but limited to accept only react elements (and not primitive types)
```tsx
const elementOnly: JSX.Element =  <div /> || <MyComponent />;
```
[⇧ back to top](#table-of-contents)

#### `React.CSSProperties`
Type-safety for styles using css-in-js 
```tsx
const styles: React.CSSProperties = { flexDirection: 'row', ...
```
[⇧ back to top](#table-of-contents)

#### `React.ReactEventHandler<E>`
Type-safe event handlers for JSX
```tsx
const handleChange: React.ReactEventHandler<HTMLInputElement> = (ev) => { ...
```
[⇧ back to top](#table-of-contents)

---

# Component Typing Patterns

## Stateless Components - SFC

#### - stateless counter

```tsx
import * as React from 'react';

export interface SFCCounterProps {
  label: string,
  count: number,
  onIncrement: () => any,
}

export const SFCCounter: React.SFC<SFCCounterProps> = (props) => {
  const { label, count, onIncrement } = props;

  const handleIncrement = () => { onIncrement(); };

  return (
    <div>
      <span>{label}: {count} </span>
      <button type="button" onClick={handleIncrement}>
        {`Increment`}
      </button>
    </div>
  );
};

```

[⟩⟩⟩ demo](https://piotrwitek.github.io/react-redux-typescript-guide/#sfccounter)

[⇧ back to top](#table-of-contents)

#### - spreading attributes [link](https://facebook.github.io/react/docs/jsx-in-depth.html#spread-attributes)

```tsx
import * as React from 'react';

export interface SFCSpreadAttributesProps {
  className?: string,
  style?: React.CSSProperties,
}

export const SFCSpreadAttributes: React.SFC<SFCSpreadAttributesProps> = (props) => {
  const { children, ...restProps } = props;

  return (
    <div {...restProps}>
      {children}
    </div>
  );
};

```

[⟩⟩⟩ demo](https://piotrwitek.github.io/react-redux-typescript-guide/#sfcspreadattributes)

[⇧ back to top](#table-of-contents)

---

## Stateful Components - Class

#### - stateful counter

```tsx
import * as React from 'react';

export interface StatefulCounterProps {
  label: string,
}

type State = {
  count: number,
};

export class StatefulCounter extends React.Component<StatefulCounterProps, State> {
  state: State = {
    count: 0,
  };

  handleIncrement = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    const { handleIncrement } = this;
    const { label } = this.props;
    const { count } = this.state;

    return (
      <div>
        <span>{label}: {count} </span>
        <button type="button" onClick={handleIncrement}>
          {`Increment`}
        </button>
      </div>
    );
  }
}

```

[⟩⟩⟩ demo](https://piotrwitek.github.io/react-redux-typescript-guide/#statefulcounter)

[⇧ back to top](#table-of-contents)

#### - with default props

```tsx
import * as React from 'react';

export interface StatefulCounterWithInitialCountProps {
  label: string,
  initialCount?: number,
}

interface DefaultProps {
  initialCount: number,
}

type PropsWithDefaults = StatefulCounterWithInitialCountProps & DefaultProps;

interface State {
  count: number,
}

export const StatefulCounterWithInitialCount: React.ComponentClass<StatefulCounterWithInitialCountProps> =
  class extends React.Component<PropsWithDefaults, State> {
    // to make defaultProps strictly typed we need to explicitly declare their type
    // @see https://github.com/DefinitelyTyped/DefinitelyTyped/issues/11640
    static defaultProps: DefaultProps = {
      initialCount: 0,
    };

    state: State = {
      count: this.props.initialCount,
    };

    componentWillReceiveProps({ initialCount }: PropsWithDefaults) {
      if (initialCount != null && initialCount !== this.props.initialCount) {
        this.setState({ count: initialCount });
      }
    }

    handleIncrement = () => {
      this.setState({ count: this.state.count + 1 });
    };

    render() {
      const { handleIncrement } = this;
      const { label } = this.props;
      const { count } = this.state;

      return (
        <div>
          <span>{label}: {count} </span>
          <button type="button" onClick={handleIncrement}>
            {`Increment`}
          </button>
        </div>
      );
    }
  };

```

[⟩⟩⟩ demo](https://piotrwitek.github.io/react-redux-typescript-guide/#statefulcounterwithinitialcount)

[⇧ back to top](#table-of-contents)

---

## Generic Components
- easily create typed component variations and reuse common logic
- common use case is a generic list components

#### - generic list

```tsx
import * as React from 'react';

export interface GenericListProps<T> {
  items: T[],
  itemRenderer: (item: T) => JSX.Element,
}

export class GenericList<T> extends React.Component<GenericListProps<T>, {}> {
  render() {
    const { items, itemRenderer } = this.props;

    return (
      <div>
        {items.map(itemRenderer)}
      </div>
    );
  }
}

```

[⟩⟩⟩ demo](https://piotrwitek.github.io/react-redux-typescript-guide/##genericlist)

[⇧ back to top](#table-of-contents)

---

## Higher-Order Components
- function that takes a component and returns a new component
- a new component will infer Props interface from wrapped Component extended with Props of HOC
- will filter out props specific to HOC, and the rest will be passed through to wrapped component

#### - withState
Adds state to a stateless counter

```tsx
import * as React from 'react';
import { Diff as Subtract } from 'react-redux-typescript';

// These props will be subtracted from original component type
interface WrappedComponentProps {
  count: number,
  onIncrement: () => any,
}

export const withState = <P extends WrappedComponentProps>(
  WrappedComponent: React.ComponentType<P>,
) => {
  // These props will be added to original component type
  interface Props {
    initialCount?: number,
  }
  interface State {
    count: number,
  }

  return class WithState extends React.Component<Subtract<P, WrappedComponentProps> & Props, State> {
    // Enhance component name for debugging and React-Dev-Tools
    static displayName = `withState(${WrappedComponent.name})`;

    state: State = {
      count: (this.props.initialCount || 0)!,
    };

    handleIncrement = () => {
      this.setState({ count: this.state.count + 1 });
    };

    render() {
      const { ...remainingProps } = this.props;
      const { count } = this.state;

      return (
        <WrappedComponent
          {...remainingProps}
          count={count}
          onIncrement={this.handleIncrement}
        />
      );
    }
  };
};

```
<details><summary>show usage</summary><p>

```tsx
import * as React from 'react';

import { withState } from '@src/hoc';
import { SFCCounter } from '@src/components';

const SFCCounterWithState =
  withState(SFCCounter);

export default (() => (
  <SFCCounterWithState label={'SFCCounterWithState'} />
)) as React.SFC<{}>;

```
</p></details>

[⇧ back to top](#table-of-contents)

#### - withErrorBoundary
Adds error handling using componentDidCatch to any component

```tsx
import * as React from 'react';
import { Diff as Subtract } from 'react-redux-typescript';

const MISSING_ERROR = 'Error was swallowed during propagation.';

interface WrappedComponentProps {
  onReset?: () => any,
}

export const withErrorBoundary = <P extends WrappedComponentProps>(
  WrappedComponent: React.ComponentType<P>,
) => {
  interface Props { }
  interface State {
    error: Error | null | undefined,
  }

  return class WithErrorBoundary extends React.Component<Subtract<P, WrappedComponentProps> & Props, State> {
    static displayName = `withErrorBoundary(${WrappedComponent.name})`;

    state: State = {
      error: undefined,
    };

    componentDidCatch(error: Error | null, info: object) {
      this.setState({ error: error || new Error(MISSING_ERROR) });
      this.logErrorToCloud(error, info);
    }

    logErrorToCloud = (error: Error | null, info: object) => {
      // TODO: send error report to cloud
    }

    handleReset = () => {
      this.setState({ error: undefined });
    }

    render() {
      const { children, ...remainingProps } = this.props;
      const { error } = this.state;

      if (error) {
        return (
          <WrappedComponent
            {...remainingProps}
            onReset={this.handleReset}
          />
        );
      }

      return children;
    }
  };
};

```
<details><summary>show usage</summary><p>

```tsx
import * as React from 'react';

import { withErrorBoundary } from '@src/hoc';
import { ErrorMessage } from '@src/components';

const ErrorMessageWithErrorBoundary =
  withErrorBoundary(ErrorMessage);

const ErrorThrower = () => (
  <button type="button" onClick={() => { throw new Error(`Catch this!`); }}>
    {`Throw nasty error`}
  </button >
);

export default (() => (
  <ErrorMessageWithErrorBoundary>
    <ErrorThrower />
  </ErrorMessageWithErrorBoundary>
)) as React.SFC<{}>;

```
</p></details>

[⇧ back to top](#table-of-contents)

---

## Redux Connected Components

#### - redux connected counter

```tsx
import { connect } from 'react-redux';

import { RootState } from '@src/redux';
import { actionCreators } from '@src/redux/counters';
import { SFCCounter } from '@src/components';

const mapStateToProps = (state: RootState) => ({
  count: state.counters.sfcCounter,
});

export const SFCCounterConnected = connect(mapStateToProps, {
  onIncrement: actionCreators.incrementSfc,
})(SFCCounter);

```
<details><summary>show usage</summary><p>

```tsx
import * as React from 'react';

import { SFCCounterConnected } from '@src/connected';

export default () => (
  <SFCCounterConnected
    label={'SFCCounterConnected'}
  />
);

```
</p></details>

[⇧ back to top](#table-of-contents)

#### - redux connected counter (verbose)

```tsx
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';

import { RootState, Dispatch } from '@src/redux';
import { actionCreators } from '@src/redux/counters';
import { SFCCounter } from '@src/components';

const mapStateToProps = (state: RootState) => ({
  count: state.counters.sfcCounter,
});

const mapDispatchToProps = (dispatch: Dispatch) => bindActionCreators({
  onIncrement: actionCreators.incrementSfc,
}, dispatch);

export const SFCCounterConnectedVerbose =
  connect(mapStateToProps, mapDispatchToProps)(SFCCounter);

```
<details><summary>show usage</summary><p>

```tsx
import * as React from 'react';

import { SFCCounterConnectedVerbose } from '@src/connected';

export default () => (
  <SFCCounterConnectedVerbose
    label={'SFCCounterConnectedVerbose'}
  />
);

```
</p></details>

[⇧ back to top](#table-of-contents)

#### - with own props

```tsx
import { connect } from 'react-redux';

import { RootState } from '@src/redux';
import { actionCreators } from '@src/redux/counters';
import { SFCCounter } from '@src/components';

export interface SFCCounterConnectedExtended {
  initialCount: number,
}

const mapStateToProps = (state: RootState, ownProps: SFCCounterConnectedExtended) => ({
  count: state.counters.sfcCounter + ownProps.initialCount,
});

export const SFCCounterConnectedExtended = connect(mapStateToProps, {
  onIncrement: actionCreators.incrementSfc,
})(SFCCounter);

```
<details><summary>show usage</summary><p>

```tsx
import * as React from 'react';

import { SFCCounterConnectedExtended } from '@src/connected';

export default () => (
  <SFCCounterConnectedExtended
    label={'SFCCounterConnectedExtended'}
    initialCount={10}
  />
);

```
</p></details>

[⇧ back to top](#table-of-contents)

---

# Redux

## Action Creators

### KISS Style
This pattern is focused on a KISS principle - to stay clear of complex proprietary abstractions and follow simple and familiar JavaScript const based types:

Advantages:
- simple "const" based types
- familiar to standard JS usage
Disadvantages:
- significant amount of boilerplate and duplication
- necessary to export both action types and action creators to re-use in other places, e.g. `redux-saga` or `redux-observable`

```tsx
export const INCREMENT_SFC = 'INCREMENT_SFC';
export const DECREMENT_SFC = 'DECREMENT_SFC';

export type Actions = {
  INCREMENT_SFC: {
    type: typeof INCREMENT_SFC,
  },
  DECREMENT_SFC: {
    type: typeof DECREMENT_SFC,
  },
};

// Action Creators
export const actionCreators = {
  incrementSfc: (): Actions[typeof INCREMENT_SFC] => ({
    type: INCREMENT_SFC,
  }),
  decrementSfc: (): Actions[typeof DECREMENT_SFC] => ({
    type: DECREMENT_SFC,
  }),
};

```
<details><summary>show usage</summary><p>

```tsx
import store from '@src/store';
import { actionCreators } from '@src/redux/counters';

// store.dispatch(actionCreators.incrementSfc(1)); // Error: Expected 0 arguments, but got 1.
store.dispatch(actionCreators.incrementSfc()); // OK => { type: "INCREMENT_SFC" }

```
</p></details>

[⇧ back to top](#table-of-contents)

### DRY Style
In a DRY approach, we're introducing a simple factory function to automate the creation process of type-safe action creators. The advantage here is that we can reduce boilerplate and repetition significantly. It is also easier to re-use action creators in other layers thanks to `getType` helper function returning "type constant".

Advantages:
- using factory function to automate creation of type-safe action creators
- less boilerplate and code repetition than KISS Style
- getType helper to obtain action creator type (this makes using "type constants" unnecessary)

```ts
import { createAction, getType } from 'react-redux-typescript';

// Action Creators
export const actionCreators = {
  incrementCounter: createAction('INCREMENT_COUNTER'),
  showNotification: createAction('SHOW_NOTIFICATION', 
    (message: string, severity: Severity = 'default') => ({
      type: 'SHOW_NOTIFICATION', payload: { message, severity },
    })
  ),
};

// Usage
store.dispatch(actionCreators.incrementCounter(4)); // Error: Expected 0 arguments, but got 1.
store.dispatch(actionCreators.incrementCounter()); // OK: { type: "INCREMENT_COUNTER" }
getType(actionCreators.incrementCounter) === "INCREMENT_COUNTER" // true

store.dispatch(actionCreators.showNotification()); // Error: Supplied parameters do not match any signature of call target.
store.dispatch(actionCreators.showNotification('Hello!')); // OK: { type: "SHOW_NOTIFICATION", payload: { message: 'Hello!', severity: 'default' } }
store.dispatch(actionCreators.showNotification('Hello!', 'info')); // OK: { type: "SHOW_NOTIFICATION", payload: { message: 'Hello!', severity: 'info' } }
getType(actionCreators.showNotification) === "SHOW_NOTIFICATION" // true
```

[⇧ back to top](#table-of-contents)

---

## Reducers
Relevant TypeScript Docs references:  
- [Discriminated Union types](https://www.typescriptlang.org/docs/handbook/advanced-types.html)
- [Mapped types](https://www.typescriptlang.org/docs/handbook/advanced-types.html) e.g. `Readonly` & `Partial`  

### Tutorial
Declare reducer `State` type definition with readonly modifier for `type level` immutability
```ts
export type State = {
  readonly counter: number,
};
```

Readonly modifier allow initialization, but will not allow rassignment highlighting an error
```ts
export const initialState: State = {
  counter: 0,
}; // OK

initialState.counter = 3; // Error, cannot be mutated
```

#### Caveat: Readonly does not provide recursive immutability on objects
> This means that readonly modifier does not propagate immutability on nested properties of objects or arrays of objects. You'll need to set it explicitly on each nested property.

```ts
export type State = {
  readonly counterContainer: {
    readonly readonlyCounter: number,
    mutableCounter: number,
  }
};

state.counterContainer = { mutableCounter: 1 }; // Error, cannot be mutated
state.counterContainer.readonlyCounter = 1; // Error, cannot be mutated

state.counterContainer.mutableCounter = 1; // No error, can be mutated
```

> There are few utilities to help you achieve nested immutability. e.g. you can do it quite easily by using convenient `Readonly` or `ReadonlyArray` mapped types.

```ts
export type State = Readonly<{
  countersCollection: ReadonlyArray<Readonly<{
    readonlyCounter1: number,
    readonlyCounter2: number,
  }>>,
}>;

state.countersCollection[0] = { readonlyCounter1: 1, readonlyCounter2: 1 }; // Error, cannot be mutated
state.countersCollection[0].readonlyCounter1 = 1; // Error, cannot be mutated
state.countersCollection[0].readonlyCounter2 = 1; // Error, cannot be mutated
```

> _There are some experiments in the community to make a `ReadonlyRecursive` mapped type, but I'll need to investigate if they really works_

[⇧ back to top](#table-of-contents)

### Examples

#### Reducer with classic `const types`

```tsx
import { combineReducers } from 'redux';

import { RootAction } from '@src/redux';

import {
  INCREMENT_SFC,
  DECREMENT_SFC,
} from './';

export type State = {
  readonly sfcCounter: number,
};

export const reducer = combineReducers<State, RootAction>({
  sfcCounter: (state = 0, action) => {
    switch (action.type) {
      case INCREMENT_SFC:
        return state + 1;

      case DECREMENT_SFC:
        return state + 1;

      default:
        return state;
    }
  },
});

```

[⇧ back to top](#table-of-contents)

#### Reducer with getType helper from `react-redux-typescript`
```ts
import { getType } from 'react-redux-typescript';

export const reducer: Reducer<State> = (state = 0, action: RootAction) => {
  switch (action.type) {
    case getType(actionCreators.increment):
      return state + 1;
      
    case getType(actionCreators.decrement):
      return state - 1;

    default: return state;
  }
};
```

[⇧ back to top](#table-of-contents)

---

## Store Configuration

### Create Root State and Root Action Types

#### `RootState` - interface representing redux state tree
Can be imported in connected components to provide type-safety to Redux `connect` function

```tsx
import { combineReducers } from 'redux';
import { routerReducer as router, RouterState } from 'react-router-redux';

import { reducer as counters, State as CountersState } from '@src/redux/counters';
import { reducer as todos, State as TodosState } from '@src/redux/todos';

interface StoreEnhancerState { }

export interface RootState extends StoreEnhancerState {
  router: RouterState,
  counters: CountersState,
  todos: TodosState,
}

import { RootAction } from '@src/redux';
export const rootReducer = combineReducers<RootState, RootAction>({
  router,
  counters,
  todos,
});

```

[⇧ back to top](#table-of-contents)

#### `RootAction` - union type of all action objects
Can be imported in various layers receiving or sending redux actions like: reducers, sagas or redux-observables epics

```tsx
// RootActions
import { RouterAction, LocationChangeAction } from 'react-router-redux';

import { Actions as CountersActions } from '@src/redux/counters';
import { Actions as TodosActions } from '@src/redux/todos';
import { Actions as ToastsActions } from '@src/redux/toasts';

type ReactRouterAction = RouterAction | LocationChangeAction;

export type RootAction =
  | ReactRouterAction
  | CountersActions[keyof CountersActions]
  | TodosActions[keyof TodosActions]
  | ToastsActions[keyof ToastsActions];

```

[⇧ back to top](#table-of-contents)

### Create Store

When creating store use rootReducer instance, this alone will to set-up **strongly typed Store instance** with type inference.
> The resulting store instance methods like `getState` or `dispatch` will be typed checked and expose type errors

```tsx
import { createStore, applyMiddleware, compose } from 'redux';
import { createEpicMiddleware } from 'redux-observable';
import { rootReducer, rootEpic, RootState } from '@src/redux';

const composeEnhancers = (
  process.env.NODE_ENV === 'development' &&
  window && window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
) || compose;

function configureStore(initialState?: RootState) {
  // configure middlewares
  const middlewares = [
    createEpicMiddleware(rootEpic),
  ];
  // compose enhancers
  const enhancer = composeEnhancers(
    applyMiddleware(...middlewares),
  );
  // create store
  return createStore(
    rootReducer,
    initialState!,
    enhancer,
  );
}

// pass an optional param to rehydrate state on app start
const store = configureStore();

// export store singleton instance
export default store;

```

[⇧ back to top](#table-of-contents)

---

## Async Flow

### "redux-observable"

```ts
// import rxjs operators somewhere...
import { combineEpics, Epic } from 'redux-observable';

import { RootAction, RootState } from '@src/redux';
import { saveState } from '@src/services/local-storage-service';

const SAVING_DELAY = 1000;

// persist state in local storage every 1s
const saveStateInLocalStorage: Epic<RootAction, RootState> = (action$, store) => action$
  .debounceTime(SAVING_DELAY)
  .do((action: RootAction) => {
    // handle side-effects
    saveState(store.getState());
  })
  .ignoreElements();

export const epics = combineEpics(
  saveStateInLocalStorage,
);
```

[⇧ back to top](#table-of-contents)

---

## Selectors 

### "reselect"

```ts
import { createSelector } from 'reselect';

import { RootState } from '@src/redux';

export const getTodos =
  (state: RootState) => state.todos.todos;

export const getTodosFilter =
  (state: RootState) => state.todos.todosFilter;

export const getFilteredTodos = createSelector(
  getTodos, getTodosFilter,
  (todos, todosFilter) => {
    switch (todosFilter) {
      case 'completed':
        return todos.filter((t) => t.completed);
      case 'active':
        return todos.filter((t) => !t.completed);

      default:
        return todos;
    }
  },
);
```

[⇧ back to top](#table-of-contents)

---

# Tools

## Living Style Guide
### ["react-styleguidist"](https://github.com/styleguidist/react-styleguidist)

[⟩⟩⟩ styleguide.config.js](/playground/styleguide.config.js)  

[⟩⟩⟩ demo](https://piotrwitek.github.io/react-redux-typescript-guide/)

[⇧ back to top](#table-of-contents)

---

# Extras

### tsconfig.json
> - Recommended setup for best benefits from type-checking, with support for JSX and ES2016 features  
> - Add [`tslib`](https://www.npmjs.com/package/tslib) to minimize bundle size: `npm i tslib` -  this will externalize helper functions generated by transpiler and otherwise inlined in your modules  
> - Include absolute imports config working with Webpack  

```js
{
  "compilerOptions": {
    "baseUrl": "./", // enables absolute path imports
    "paths": { // define absolute path mappings
      "@src/*": ["src/*"] // will enable -> import { ... } from '@src/components'
      // in webpack you need to add -> resolve: { alias: { '@src': PATH_TO_SRC } }
    },
    "outDir": "dist/", // target for compiled files
    "allowSyntheticDefaultImports": true, // no errors on commonjs default import
    "allowJs": true, // include js files
    "checkJs": true, // typecheck js files
    "declaration": false, // don't emit declarations
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "forceConsistentCasingInFileNames": true,
    "importHelpers": true, // importing helper functions from tslib
    "noEmitHelpers": true, // disable emitting inline helper functions
    "jsx": "react", // process JSX
    "lib": [
      "dom",
      "es2016",
      "es2017.object"
    ],
    "target": "es5", // "es2015" for ES6+ engines
    "module": "commonjs", // "es2015" for tree-shaking
    "moduleResolution": "node",
    "noEmitOnError": true,
    "noFallthroughCasesInSwitch": true,
    "noImplicitAny": true,
    "noImplicitReturns": true,
    "noImplicitThis": true,
    "noUnusedLocals": true,
    "strictNullChecks": true,
    "pretty": true,
    "removeComments": true,
    "sourceMap": true
  },
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "node_modules",
    "src/**/*.spec.*"
  ]
}
```

[⇧ back to top](#table-of-contents)

### tslint.json
> - Recommended setup is to extend build-in preset `tslint:recommended` (for all rules use `tslint:all`)  
> - Add tslint react rules: `npm i -D tslint-react` https://github.com/palantir/tslint-react  
> - Amended some extended defaults for more flexibility  

```json
{
  "extends": ["tslint:recommended", "tslint-react"],
  "rules": {
    "arrow-parens": false,
    "arrow-return-shorthand": [false],
    "comment-format": [true, "check-space"],
    "import-blacklist": [true, "rxjs"],
    "interface-over-type-literal": false,
    "max-line-length": [true, 120],
    "member-access": false,
    "member-ordering": [true, {
      "order": "fields-first"
    }],
    "newline-before-return": false,
    "no-any": false,
    "no-empty-interface": false,
    "no-import-side-effect": [true],
    "no-inferrable-types": [true, "ignore-params", "ignore-properties"],
    "no-invalid-this": [true, "check-function-in-method"],
    "no-null-keyword": false,
    "no-require-imports": false,
    "no-switch-case-fall-through": true,
    "no-submodule-imports": [true, "rxjs", "@src"],
    "no-trailing-whitespace": true,
    "no-this-assignment": [true, {
      "allow-destructuring": true
    }],
    "no-unused-variable": [true, "react"],
    "object-literal-sort-keys": false,
    "object-literal-shorthand": false,
    "one-variable-per-declaration": [false],
    "only-arrow-functions": [true, "allow-declarations"],
    "ordered-imports": [false],
    "prefer-method-signature": false,
    "prefer-template": [true, "allow-single-concat"],
    "semicolon": [true, "ignore-interfaces"],
    "quotemark": [true, "single", "jsx-double"],
    "triple-equals": [true, "allow-null-check"],
    "typedef": [true,"parameter", "property-declaration"],
    "variable-name": [true, "ban-keywords", "check-format", "allow-pascal-case", "allow-leading-underscore"]
  }
}
```

[⇧ back to top](#table-of-contents)

### jest.config.json
> - Recommended setup for Jest with TypeScript  
> - Install with `npm i -D jest-cli ts-jest @types/jest`  

```json
{
  "verbose": true,
  "transform": {
    ".(ts|tsx)": "./node_modules/ts-jest/preprocessor.js"
  },
  "testRegex": "(/spec/.*|\\.(test|spec))\\.(ts|tsx|js)$",
  "moduleFileExtensions": [
    "ts",
    "tsx",
    "js"
  ],
  "globals": {
    "window": {},
    "ts-jest": {
      "tsConfigFile": "./tsconfig.json"
    }
  },
  "setupFiles": [
    "./jest.stubs.js",
    "./src/rxjs-imports.tsx"
  ]
}
```

[⇧ back to top](#table-of-contents)

### Default and Named Module Exports
> Most flexible solution is to use module folder pattern, because you can leverage both named and default import when you see fit.  
Using this solution you'll achieve better encapsulation for internal structure/naming refactoring without breaking your consumer code:  
```ts
// 1. in `components/` folder create component file (`select.tsx`) with default export:

// components/select.tsx
const Select: React.SFC<Props> = (props) => {
...
export default Select;

// 2. in `components/` folder create `index.ts` file handling named imports:

// components/index.ts
export { default as Select } from './select';
...

// 3. now you can import your components in both ways, with named export (better encapsulation) or using default export (internal access):

// containers/container.tsx
import { Select } from '@src/components';
or
import Select from '@src/components/select';
...
```

[⇧ back to top](#table-of-contents)

### Vendor Types Augmentation
> Strategies to fix issues coming from broken "vendor type declarations" files (*.d.ts)

#### Augmenting library internal type declarations - using relative import resolution 
```ts
// added missing autoFocus Prop on Input component in "antd@2.10.0" npm package
declare module '../node_modules/antd/lib/input/Input' {
  export interface InputProps {
    autoFocus?: boolean;
  }
}
```

[⇧ back to top](#table-of-contents)

#### Augmenting library public type declarations - using node module import resolution
```ts
// fixed broken public type declaration in "rxjs@5.4.1" npm package 
import { Operator } from 'rxjs/Operator';
import { Observable } from 'rxjs/Observable';

declare module 'rxjs/Subject' {
  interface Subject<T> {
    lift<R>(operator: Operator<T, R>): Observable<R>;
  }
}
```

[⇧ back to top](#table-of-contents)

#### To quick-fix missing type declarations for vendor modules you can "assert" a module type with `any` using [Shorthand Ambient Modules](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/Modules.md#shorthand-ambient-modules)

```tsx
// @src/types/modules.d.ts
declare module 'react-test-renderer';
declare module 'enzyme';

```

> More advanced scenarios for working with vendor module declarations can be found here [Official TypeScript Docs](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/Modules.md#working-with-other-javascript-libraries)

[⇧ back to top](#table-of-contents)

### Npm Scripts
> Common TS-related npm scripts shared across projects
```
"check": "npm run lint & npm run tsc",
"lint": "tslint --project './tsconfig.json'",
"tsc": "tsc -p . --noEmit",
"tsc:watch": "tsc -p . --noEmit -w",
"test": "jest --config jest.config.json",
"test:watch": "jest --config jest.config.json --watch",
```

[⇧ back to top](#table-of-contents)

---

# FAQ

### - should I still use React.PropTypes in TS?
> No. When using TypeScript it is an unnecessary overhead, when declaring IProps and IState interfaces, you will get complete intellisense and compile-time safety with static type checking, this way you'll be safe from runtime errors and you will save a lot of time on debugging. Additional benefit is an elegant and standarized method of documenting your component external API in the source code.  

[⇧ back to top](#table-of-contents)

### - when to use `interface` declarations and when `type` aliases?
> From practical side, using `interface` declaration will display identity (interface name) in compiler errors, on the contrary `type` aliases will be unwinded to show all the properties and nested types it consists of. This can be a bit noisy when reading compiler errors and I like to leverage this distinction to hide some of not so important type details in errors  
Related `ts-lint` rule: https://palantir.github.io/tslint/rules/interface-over-type-literal/  

[⇧ back to top](#table-of-contents)

### - how to best initialize class instance or static properties?
> Prefered modern style is to use class Property Initializers  
```tsx
class StatefulCounterWithInitialCount extends React.Component<Props, State> {
  // default props using Property Initializers
  static defaultProps: DefaultProps = {
    className: 'default-class',
    initialCount: 0,
  };
  
  // initial state using Property Initializers
  state: State = {
    count: this.props.initialCount,
  };
  ...
}
```

[⇧ back to top](#table-of-contents)

### - how to best declare component handler functions?
> Prefered modern style is to use Class Fields with arrow functions  
```tsx
class StatefulCounter extends React.Component<Props, State> {
// handlers using Class Fields with arrow functions
  handleIncrement = () => {
    this.setState({ count: this.state.count + 1 }); 
  };
  ...
}
```

[⇧ back to top](#table-of-contents)

---

# Roadmap
- extend HOC section with more advanced examples [#5](../../issues/5)  
- investigate typing patterns for generic component children [#7](../../issues/7)

[⇧ back to top](#table-of-contents)

# Contribution Guide
- Don't edit `README.md` - it is built with `generator` script from  separate `.md` files located in the `/docs/markdown` folder, edit them instead
- For code snippets, they are also injected by `generator` script from the source files located in the playground folder (this step make sure all examples are type-checked and linted), edit them instead
> look for include directives in `.md` files that look like this: `::[example|usage]='../../playground/src/components/sfc-counter.tsx'::`

Before opening PR please make sure to check:
```bash
# run linter in playground
yarn run lint

# run type-checking in playground
yarn run tsc
  
# re-generate `README.md` from repo root
sh ./generate.sh
# or 
node ./generator/bin/generate-readme.js
```

[⇧ back to top](#table-of-contents)

# Project Examples

https://github.com/piotrwitek/react-redux-typescript-starter-kit  
https://github.com/piotrwitek/react-redux-typescript-webpack-starter  

[⇧ back to top](#table-of-contents)
