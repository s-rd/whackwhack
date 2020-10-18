![Screenshot](/screenshot.jpg)

[![Netlify Status](https://api.netlify.com/api/v1/badges/92d97559-dcc5-44ae-80b8-030aa060652e/deploy-status)](https://app.netlify.com/sites/whackwhack/deploys)

# Whack Whack — Revolution!

> A simple Whack-a-Mole type game built as a Single Page Application with HTML/SCSS and Vue/JS.

## Structure

[App.vue](/src/App.vue) is the main file. This keeps track of state (instead of a store) and pulls in child components. Data flows to child components through props.

Styles are located separately in [assets/styles](/src/assets/styles). Each Vue component has its own `block`, following the BEM methodology.

## Project setup
```
npm install
```

#### Compiles and hot-reloads for development
```
npm run serve
```

#### Compiles and minifies for production
```
npm run build
```
