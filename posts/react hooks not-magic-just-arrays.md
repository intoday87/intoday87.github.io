---
title: react hooks는 그저 arrays다
---
이전에 이해하고 넘어갔던 내용인데 공부하다가 고취시키는 좋은 자료가 있어 내용을 가져와 정리해본다.
react hooks는 렌더링시마다 closer를 기반으로 한 singleton 배열이다. 컴포넌트 내에서 순서대로 호출되는 각각의 모든 훅은 순서(index)를 갖게 되며 그 순서에 따라 결정된 값을 가지고 있을 뿐이다.
- source: https://www.swyx.io/hooks#not-magic-just-arrays
- source example by [codesandbox](https://codesandbox.io/p/sandbox/react-hooks-not-magic-just-arrays-7n96dg)


```js
const MyReact = (function () {
  let hooks = [], // 각각 순서대로 호출되는 훅의 deps를 가진다. singleton으로 관리됨
    currentHook = 0; // array of hooks, and an iterator!
  return {
    render(Component) {
      const Comp = Component(); // run effects
      Comp.render();
      currentHook = 0; // reset for next render
      return Comp;
    },
    useEffect(callback, depArray) {
      const hasNoDeps = !depArray;
      const deps = hooks[currentHook]; // type: array | undefined
      const hasChangedDeps = deps
        ? !depArray.every((el, i) => el === deps[i])
        : true;
      if (hasNoDeps || hasChangedDeps) {
        callback();
        hooks[currentHook] = depArray;
      }
      currentHook++; // done with this hook
    },
    useState(initialValue) {
      hooks[currentHook] = hooks[currentHook] || initialValue; // type: any
      const setStateHookIndex = currentHook; // for setState's closure!
      const setState = (newState) => (hooks[setStateHookIndex] = newState);
      return [hooks[currentHook++], setState];
    },
  };
})();

export default MyReact;


```
