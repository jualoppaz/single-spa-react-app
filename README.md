<p float="left">
  <img src="https://single-spa.js.org/img/logo-white-bgblue.svg" width="50" height="50">
  <img src="https://cdn.auth0.com/blog/react-js/react.png" width="50" height="50">
</p>

[![npm version](https://img.shields.io/npm/v/single-spa-react-app.svg?style=flat-square)](https://www.npmjs.org/package/single-spa-react-app)
[![](https://data.jsdelivr.com/v1/package/npm/single-spa-react-app/badge)](https://www.jsdelivr.com/package/npm/single-spa-react-app)

# single-spa-react-app

This is a React application example used as NPM package in [single-spa-login-example-with-npm-packages](https://github.com/jualoppaz/single-spa-login-example-with-npm-packages) in order to register an application. See the installation instructions there.

## ✍🏻 Motivation

This is a React application which contains two routed pages for embbed the app inside a root single-spa application.

## How it works ❓

There are several files for the right working of this application and they are:

- src/index.js
- src/singleSpaEntry.js
- package.json
- webpack.config.js

### src/index.js

```javascript
import React from 'react';
import './index.css';
import { BrowserRouter } from 'react-router-dom';

import App from './App';
import * as serviceWorker from './serviceWorker';


export default class Root extends React.Component {
  render() {
    return (
      <BrowserRouter basename="/react">
        <App />
      </BrowserRouter>
    );
  }
}

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister();
```

As this application will be mounted when browser url starts with **/react**, we need to set **basename** attribute with **/react** value in our router component. In this case the used router compononent is **BrowserRouter**.

### src/singleSpaEntry.js

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import singleSpaReact from 'single-spa-react';

import rootComponent from './index';

const reactLifecycles = singleSpaReact({
  React,
  ReactDOM,
  rootComponent,
  domElementGetter: () => document.getElementById('react-app'),
});
export const { bootstrap } = reactLifecycles;
export const { mount } = reactLifecycles;
export const { unmount } = reactLifecycles;
```

The **reactLifecycles** object contains all **single-spa-react** methods for the **single-spa** lifecycle of this app. All used config is default one but the custom config of the **domElementGetter** option. It's assumed that an element with **react-app** id is defined in the **index.html** where this application will be mounted.

### package.json

```json
{
  "name": "single-spa-react-app",
  "version": "0.1.3",
  "description": "React application with two example pages for be included in a single-spa application as registered app.",
  "main": "dist/single-spa-react-app.js",
  "dependencies": {
    "react": "16.12.0",
    "react-dom": "16.12.0",
    "react-router-dom": "5.1.2",
    "react-scripts": "3.3.1",
    "single-spa-react": "2.11.0"
  },
  "scripts": {
    "build": "webpack --config webpack.config.js -p",
    "lint": "eslint . --ext .js --fix"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/jualoppaz/single-spa-react-app.git"
  },
  "author": "Juan Manuel López Pazos",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/jualoppaz/single-spa-react-app/issues"
  },
  "homepage": "https://github.com/jualoppaz/single-spa-react-app#readme",
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
  "devDependencies": {
    "@babel/core": "7.8.4",
    "@babel/preset-react": "7.8.3",
    "@testing-library/jest-dom": "4.2.4",
    "@testing-library/react": "9.4.0",
    "@testing-library/user-event": "7.2.1",
    "babel-loader": "8.0.6",
    "clean-webpack-plugin": "3.0.0",
    "babel-eslint": "10.0.3",
    "css-loader": "3.4.2",
    "eslint": "6.8.0",
    "eslint-config-airbnb-base": "14.0.0",
    "eslint-plugin-flowtype": "4.6.0",
    "eslint-plugin-import": "2.20.1",
    "eslint-plugin-react": "7.18.3",
    "html-loader": "0.5.5",
    "node-sass": "4.13.1",
    "path": "0.12.7",
    "sass-loader": "8.0.2",
    "style-loader": "1.1.3",
    "typescript": "3.7.5",
    "webpack": "4.41.5",
    "webpack-cli": "3.3.10"
  },
  "keywords": [
    "single-spa",
    "react",
    "react-router-dom",
    "npm",
    "webpack"
  ]
}
```

There are only two scripts in this project:

- **build**: for compile the application and build it as a **libray** in **umd** format
- **lint**: for run **eslint** in all project

### webpack.config.js

```javascript
const path = require('path');
const webpack = require('webpack');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
  entry: ['src/singleSpaEntry.js'],
  output: {
    library: 'single-spa-react-app',
    libraryTarget: 'umd',
    filename: 'single-spa-react-app.js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.js?$/,
        exclude: [path.resolve(__dirname, 'node_modules')],
        loader: ['babel-loader', 'eslint-loader'],
      },
      {
        test: /\.html$/i,
        loader: 'html-loader',
      },
      {
        test: /\.css|\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader'],
      },
    ],
  },
  node: {
    fs: 'empty',
  },
  resolve: {
    modules: [__dirname, 'node_modules'],
  },
  plugins: [
    new CleanWebpackPlugin({
      cleanAfterEveryBuildPatterns: ['dist'],
    }),
    new webpack.optimize.LimitChunkCountPlugin({
      maxChunks: 1,
    }),
  ],
  devtool: 'source-map',
  externals: [],
  devServer: {
    historyApiFallback: true,
    writeToDisk: true,
  },
};
```

The needed options for the right build of the application as library are defined in the **output** field.\
The **LimitChunkCountPlugin** is used for disable chunks for build process. It's not necessary but I prefer keep whole application in one chunk as it will be embedded in another one.
