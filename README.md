# react-native-accessibility-actions
Simplify handling accessibility actions in React Native apps.

## useAccessibilityActions
Helper hook to provide both `onAccessibilityAction` and `accessibilityActions` to an element.
```
const { accessibilityActions, onAccessibilityAction } = useAccessibilityActions(getAccessibiltyActions, deps)
```

### Parameters
1. getAccessibiltyActions
    - callback returning [AccessibilityActions](#accessibilityactions)
2. deps
    - array of dependencies
    - to make use of ESlint `react-hooks/exhaustive-deps` rule add `useAccessibilityActions` as it's `additionalHooks`
        ```
        'react-hooks/exhaustive-deps': [
            'error',
            {
                additionalHooks: '(useAccessibilityActions)',
            },
        ],
        ```

### Return value
- [accessibilityActions](https://reactnative.dev/docs/accessibility#accessibility-actions)
- [onAccessibilityAction](https://reactnative.dev/docs/accessibility#accessibility-actions)



- a11name - any action name that would be handled internally in `onAccessibilityAction` - might be one of the [system action](https://reactnative.dev/docs/accessibility#accessibility-actions) or any custom name
- label - Action label to be read / provided via actions list
- onAction - action to be performed


### Types
#### AccessibilityActions
Object, where key is the action name and value is [AccessibilityAction](#accessibilityaction)
- action name - can be one of [standard action name](https://reactnative.dev/docs/accessibility#accessibility-actions) or any custom one, must be unique
#### AccessibilityAction
| key      | value type        | description                                                                                                                              |
|----------|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| label    | string            | used as an a11y label for the `accessibilityAction`                                                                                      |
| onAction | () => void        | callback to be invoked as `onAccessibilityAction` for this action name                                                                   |
| disabled | boolean optional | Whether to not expose the accessibility action to the user. If true, disables this action in both `accessibilityActions` & `onAccessibilityAction` |
### Example
Standard way
```
<View
  accessible
  accessibilityActions={(() => {
    const result = [
      { name: 'increaseValue', label: 'Decrease value' },
      { name: 'more_info', label: 'More Info' },
      { name: 'get_directions', label: 'Get Directions' },
    ];
    if (value > 0) result.push({ name: 'decreaseValue', label: 'Decrease value' });
    return result;
  })()}
  onAccessibilityAction={(event: AccessibilityActionEvent) => {
    switch (event.nativeEvent.actionName) {
      case 'increaseValue':
        increaseValue();
      case 'decreaseValue':
        decreaseValue();
      case 'more_info':
        onPressMore();
      case 'get_directions':
        onPressDirections();
    }
  }}
/>;
```
Using the helper hook
```
<View
  accessible
  {...useAccessibilityActions(
    () => ({
      more_info: { label: 'More Info', onAction: onPressMore },
      get_directions: { label: 'Get Directions', onAction: onPressDirections },
      increaseValue: { label: 'Increase value', onAction: increaseValue },
      decreaseValue: { label: 'Decrease value', onAction: decreaseValue, disabled: value === 0 },
    }),
    []
  )}
/>;
```