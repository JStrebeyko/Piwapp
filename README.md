# piwapp

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn start
```

### Compiles and minifies for production
```
yarn run build
```

### Lints and fixes files
```
yarn run lint
```
### TODO
overall:
- [ ] RWD;
- [ ] trickle down (add20) functionality as per spec;
- [ ] cleanup
- [ ] ensure dynamic routes do their jobs
- [ ] PWA

HTML/CSS
- [ ] Sass features beyond scoping - mixins, variables
- [ ] no semantic html tags (a lot of divs, poor accessibility);
- [x] styling the same thing twice on a single file (App.vue —> #app)
- [x] inline styling in some places

JS
- [x] naming convention could be better (eg. Boolean variables starting with `is` || `are` (allBearsRenderedAlready -> areAllBearsRendered)
- [x] `let self = this;` used instead of arrow function
- [ ] mix of ES5 and ES6:
- [x] variables (`var`, `let`, `const`)
- [ ] sometimes using arrow functions, sometimes not
- [ ] sometimes using string interpolation, sometimes not

Vue:
- [x] event modifiers sometimes unnecessary used (Details.vue -> .native on button tag)
- [ ] views could be divided into components to improve readability
- [ ] a lot of logic in the templates

Tests:
- [ ] tests
