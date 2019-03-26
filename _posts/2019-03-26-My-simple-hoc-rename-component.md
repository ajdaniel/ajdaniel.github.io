---
layout: post
title: My simple HoC rename component
---

I'm currently working on a front-end project that uses both React JS and Angular JS. Yes, that sounds a bit crazy but the project is evolving from Angular to React, and this is the difficult transition phase.

We are using `$ngRedux` to manage our Redux application state, which presented an issue to me today when I created a new React component in this AngularJS dominated codebase.

### react2angular

We use [react2angular](https://github.com/coatue-oss/react2angular) (full disclosure, I am a contributor) to embed React components in AngularJS. One of the interesting parts about this is dealing with Angular's dependency injections. `$ngRedux` is served to components as a dependency and injected at runtime. We can access it with `react2angular` like so:


```js
import angular from 'angular';
import { react2angular } from 'react2angular';
import MyReactComponent from './MyReactComponent';

export const MyReactComponentModule = angular
  .module('my-project.components.myReactComponent', [])
  .component('myReactComponent', react2angular(MyReactComponent, [], ['$translate', '$ngRedux']))
  .name;
```

We can discuss `$translate` another day.

So my React component uses Redux, and thus I use the very useful `connect()` function to transform some useful stuff from the store to my component, like actions and props.

```js
export default connect(mapStateToProps, mapDispatchToProps)(MyReactComponent);
```

### The problem

Here's what happened with my code above:

`Invariant Violation: Could not find "store" in either the context or props of "Connect(MyReactComponent)". Either wrap the root component in a <Provider>, or explicitly pass "store" as a prop to "Connect(MyReactComponent)".`

ugh.

`connect()` _expects a prop called `store` in the component_. This prop is the redux store which provides the data and the dispatcher to the component. However, we're using `$ngRedux` via dependency injection.

I searched around the redux codebase, issues, and docs to see if I can change the expected prop name. I was unsuccessful. It doesn't mean it's not possible, it's probably my lack of ability to find it (which you could argue means a lack of concise documentation).

So here's how I solved it, with a very heavy-handed higher-order component:

```js
// This HOC simply renames $ngRedux to store (because this is used with react2angular)
const mapNgReduxToStore = WrappedComponent => {
  const MappedComponent = ({ $ngRedux, ...rest }) => {
    return (<WrappedComponent {...rest} store={$ngRedux} />);
  };
  MappedComponent.displayName = `WithStore(${WrappedComponent.displayName})`;
  MappedComponent.propTypes = {
    $ngRedux : PropTypes.object,
  };
  return MappedComponent;
};

export default lodash.flowRight(
  mapNgReduxToStore,
  connect(mapStateToProps, mapDispatchToProps)
)(MyReactComponent);
```

(If you're new to compose/flowRight, it's just a simple way to layer HoCs for a component).

There we have it! It feels heavy handed, and perhaps I should try and raise an issue with `react-redux` to allow a prop name change. But this gets the job done and I hope it serves someone well.
