title: Truthy and Falsy
date: 2016-02-12 10:30:54
desc: javascript knowledge of truthy value and falsy value
---

Truty value and falsy value is a little bit different from Boolean value. In JavaScript, the truthy value is strict equal to `true` when evaluated in a Boolean context, and the falsy value is evaluted to `false`.

The following value is common falsy value:

- false
- null
- undefined
- 0
- NaN
- ''
- document.all

<!-- more -->

All value are truthy value except falsy value:

```js
!!{} === true
// => true

!!document.all === false
// => true
```

###### Reference

- [MDN - Truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)
- [MDN - Falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)