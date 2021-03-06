# cal-rule

[![LICENSE](https://img.shields.io/npm/l/cal-rule.svg?sanitize=true)](https://github.com/darkXmo/cal-rule/blob/main/LICENSE)
[![NPM version](https://img.shields.io/npm/v/cal-rule.svg?style=flat)](https://npmjs.com/package/cal-rule)
[![CircleCI](https://circleci.com/gh/darkXmo/cal-rule.svg?style=shield)](https://circleci.com/gh/darkXmo/cal-rule)
[![Codecov](https://badgen.net/codecov/c/github/darkXmo/cal-rule)](https://app.codecov.io/gh/darkXmo/cal-rule)
[![install size](https://badgen.net/packagephobia/install/cal-rule)](https://packagephobia.now.sh/result?p=cal-rule)

A simple javascript/typescript calculating rule solution.

> `1A&2B|!(3C&4D)`

operator `&` `|` `!` `()` are supported.

`select` and `input` are supported.

> - `1A` means choose the first item of first choice;
> - `1` means first value is valid;
> - You can customize your own strategy as well;

## install

```bash
npm install cal-rule
```

## usage

### init

```javascript
import { init } from 'cal-rule';

const ruleStr = '1A&2B';

const rule = init(ruleStr);
```

while using typescript, you can also appoint the value type of `rule`.

```typescript
const rule = init<string>(ruleStr);
```

if `ruleStr` is invalid, `init` will return `undefined`;

### choices & values & parse

pass the value to rule, parse the

```typescript
const rule = init(ruleStr);

const choices = [
  ['option1A', 'option1B', 'option1C'],
  ['option2A', 'option2B', 'option2C', 'option2D'],
  ['option3A', 'option3B']
];

const values = ['option1A', 'option2B', undefined];

// since the ruleStr only infer to the value of position [0] and [1], choices[2] and values[2] will be passed but ignored.
rule.choices = choices;
rule.values = values;

// true
const ans: boolean = rule.parse();
```

If there is a `input` value instead of `select`, which means no choices can be provided, `undefined` is ok to be passed as an item of `choices`.

```typescript
const ruleStr = '1A&2';

const rule = init(ruleStr);

const choices = [['option1A', 'option1B', 'option1C'], undefined, ['option3A', 'option3B']];

const values = ['option1A; option1B; option1C', 'input value', undefined];

rule.choices = choices;
rule.values = values;

// true
const ans: boolean = rule.parse();
```

`parse` provide a functional param to cover the default calculator;

```typescript
const choices = [
  ['option1A', 'option1B', 'option1C'],
  ['option2A', 'option2B', 'option2C', 'option2D'],
  ['option3A', 'option3B']
];

const values = ['option1A; option1B; option1C', 'option2B; option2D', undefined];

rule.choices = choices;
rule.values = values;

// true
const ans: boolean = rule.parse((value, choice): boolean => {
  if (value) {
    return value.includes(choice);
  }
  return false;
});
```

The second param `choice` is defined by ruleStr and choices. `1A` means `choices[0][0]`, while `2B` means `choices[1][1]`

In `1A&2B` case:

| serial | value                            | choice       | rule    | ans             |
| ------ | -------------------------------- | ------------ | ------- | --------------- |
| 0      | `'option1A; option1B; option1C'` | `'option1A'` | `1A`    | true            |
| 1      | `'option2B; option2D'`           | `'option2B'` | `2B`    | true            |
| 2      | values                           | choices      | `1A&2B` | ans[0] & ans[1] |

Default function pass to parse ???

```typescript
(value, choice) => {
  if (choice) {
    return value === choice;
  } else {
    return value.trim().length > 0;
  }
};
```

### calculator

`cal-rule` allows developer to customize the default calculating function on parse.

```typescript
rule.calculator = (value, choice): boolean => {
  if (value) {
    return value.includes(choice);
  }
  return false;
};
```

## Project Actives

![Alt](https://repobeats.axiom.co/api/embed/070ab5736ffbb0afce1a6c9ebe598032c60730a8.svg 'Repobeats analytics image')
