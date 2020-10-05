# whoolso-word-filter

A flexible, smart word filter to prevent profanity or whatever whatever suits your taste.

## Installing

`npm i whoolso-word-filter`

```js
const { filterWords } = require('whoolso-word-filter');
```

## Using the filter

This package gives you access to a function called `filterWords()` which takes a config object as its sole argument. To keep it simple, the function returns an array with all
the words/phrases found inside a given string.

### Building the config object

The config object allows you to configure the filter as much as possible. Take the following example:

```javascript
const configObj = {
  wordsToFilter,
  stringToCheck,
  lengthThreshold: 3,
  leetAlphabet1: textToLeetAlphabet1,
  leetAlphabet2: textToLeetAlphabet2,
  shortWordLength: 3,
  shortWordExceptions
};

const foundWords = filterWords(configObj);
```

Argument Definitions:

- **wordsToFilter:** An array containing all the words that you want to filter as strings. If you want to filter phrases, for example `'bad person'`, you'll have to
  write it without spaces `'badperson'`.  
   There's no need to add the plural version of a word unless the grammar varies, for instance, if one of your words is `'idiot'`, it
  is not necessary to add `'idiots'` because it contains the root of the word, which is what interests us. It's not necessary to add the leet versions
  of your word either (ex. `'1d1ot'`). If you want to be really strict, it's advisable to add misspelled versions of the word (ex. `'stupid'` could be
  intentionally misspelled as `'stupd'`).

```javascript
// Suppose we want to filter some political terms. Our wordsToFilter array could be something like this:

const wordsToFilter = [
  'gop',
  'gerrymander',
  'republican',
  'republikan',
  'rpublican',
  'rpblican',
  'rpublicn',
  'repblicn',
  'lefty',
  'lfty',
  'lftwing'
];
```

Also, make sure all the words you add are lowercase. The filter converts the string you want to check to lowercase, so array of wordsToFilter must be all lowercase too.

- **stringToCheck:** The string that you want to check.

```js
const stringToCheck = `I am a political comment whose unique goal is to say the word republican.`;
```

- **lengthThreshold**: The length of syllabes in which you want to find words separated by spaces. `'I am here to say the word r e p u b l i c a n'` will catch
  `'republican'` if the value of `lengthThreshold` is at least 1. If it's 2, `'re pu bl ic an'` would be caught as well and so on. The larger you set this option to, the more
  prone you will be to false positives, so I wouldn't suggest using a number larger than 3, but that depends on your needs.

- **leetAlphabet1 and leetAlphabet2**: The function will perform two leet translations in the text. Given the wordsToFilter array shown above, take these
  sentences `'I am here to say the word republic@n'` and `'I am here to say the word republ1c4n'`, both of them will be caught as 'republican'.

leetAlphabet1 and leetAlphabet2 must have the following format:

```js
const textToLeetAlphabet1 = {
  A: '@',
  B: '8',
  C: '(',
  D: 'D',
  E: '3',
  F: 'F',
  G: '6',
  H: '#',
  I: '!',
  J: 'J',
  K: 'K',
  L: '1',
  M: 'M',
  N: 'N',
  O: '0',
  P: 'P',
  Q: 'Q',
  R: 'R',
  S: '$',
  T: '6',
  U: 'U',
  V: 'V',
  W: 'W',
  X: 'X',
  Y: 'Y',
  Z: '2'
};

const textToLeetAlphabet2 = {
  A: '4',
  B: '8',
  C: '(',
  D: '<|',
  E: '€',
  F: 'PH',
  G: '9',
  H: '|-|',
  I: '1',
  J: 'J',
  K: 'K',
  L: '|',
  M: '|\\/|',
  N: '|\\|',
  O: '0',
  P: '|2',
  Q: 'Q',
  R: 'R',
  S: '5',
  T: '+',
  U: '|_|',
  V: '/',
  W: '//',
  X: '><',
  Y: `'/`,
  Z: '2'
};
```

If you require a more advanced leet filter, translate the string's leet before passing it inside the filter's config object.

- **shortWordLength:** A number indicating the length from which you consider a word to be 'short'. This property helps to reduce the number of false positves, as some
  short words might be contained inside longer words. I recommend you to leave this option as 3.

`// Setting this option to 3 means 'In my case, short words are those with a length of 3 or less characters.'`

- **shortWordExceptions:**: Words that even though exceed the defined `shortWordLength`, they must be treated as short words. 'meth' is a good example, say you want to
  filter drug names, but a string containing the word 'something' returns 'meth'. You could set your shortWordLength to 4 to solve this, but that could cause some other
  false positives. `shortWordExceptions` is the solution for these cases:

```js
const shortWordExceptions = ['meth'];
```

## Examples

Consider the following `configObject`, where the leet alphabets are the same as the ones shown above.

```js
const wordsToFilter = ['uneducated', 'republican', 'meth'];
const shortWordExceptions = ['meth'];

const configObj = {
  wordsToFilter,
  stringToCheck,
  lengthThreshold: 3,
  leetAlphabet1: textToLeetAlphabet1,
  leetAlphabet2: textToLeetAlphabet2,
  shortWordLength: 3,
  shortWordExceptions
};
```

- **Normal sentence**

```js
configObj.stringToCheck = `They are something else, uneducated republicans`;

console.log(filterWords(configObj)); // [ 'uneducated', 'republican' ]
```

- **Putting signs between each letter**

```js
configObj.stringToCheck = `They are something else, un$edu'ca[]\`te()d" republicans`;

console.log(filterWords(configObj)); // [ 'uneducated', 'republican' ]
```

- **Separating the letters with spaces**

It doesn't matter how many spaces you use, the word will be detected anyways.

```js
configObj.stringToCheck = `They are something else, u n e d u c a t e d republicans`;

console.log(filterWords(configObj)); // [ 'uneducated', 'republican' ]
```

- **Writing the word in all caps**

```js
configObj.stringToCheck = `They are something else, UNEDUCATED republicans`;

console.log(filterWords(configObj)); // [ 'uneducated', 'republican' ]
```

Alternating between upper and lowercase gives the same result, ex. `'uNeDuCatEd'`.

- **Replacing some letters with numbers and signs / using leet**

```js
configObj.stringToCheck = `They are something else, un3duc@t3d republicans`;

console.log(filterWords(configObj)); // [ 'uneducated', 'republican' ]
```

- **Duplicating letters**

```js
configObj.stringToCheck = `They are something else, uneeduuuucaa@t333ed republicans`;

console.log(filterWords(configObj)); // [ 'uneducated', 'republican' ]
```

- **Writing without spaces**

```js
configObj.stringToCheck = `They are something elseuneeeduucatedrepublicans`;

console.log(filterWords(configObj)); // [ 'uneducated', 'republican' ]
```

- **What if the sentence doesn't have any of the words to filter?**

An empty array will be returned.

```js
configObj.stringToCheck = `They are good people and I love them`;

console.log(filterWords(configObj)); // []
```

**Note:** If you want to filter words that have two consecutive letters (ex. 'dumbass'), it's advisable to add the
versions with only one letter to `wordsToFilter` to make sure the filter is able to catch them (ex. 'dumbas').
This is because when checking for leet we remove the duplicated letters, so if someone writes something like
`'hello dumba$$hoe'`, combining leet in a word with two consecutive letters and then adding another word without spaces
will be able to pass if you haven't added the version of the word with only one letter, 'dumbas' in this case.
