---
layout: post
sidebar-align: left
title: "Using Webpack"
date:   2016-05-04 18:02:05
categories: javascript 
author: Pere Pages
---

* TOC
{:toc}

## Introduction

### Usual Problems

- Multiple Web Requests -> Combining Files
- Code Size -> Minifying Files
- File order Dependencies -> Maintaining File Order
- Transpilation
- Linting

### Other Solutions

- Server-side tools
- Task Runners : grunt, gulp

### Webpack Solution

Can also combine css with the js.

- Use NPM
- Use a Module System
  - AMD
  - CommonJS
  - ES6 modules

## Basic Builds

### CLI Basics

```bash
npm install -g webpack
```

```bash
# bundle.js is tye typical name
webpack ./app.js bundle.js
```

### Adding a config file

```webpack.config.js``` it is a *CommonJS* module.

```javascript
module.exports = {
    entry: "./app.js",
    output: {
        filename: "bundle.js"
    }
};
```

### Watch Mode and the Webpack Dev Server

```bash
webpack --watch
```

```javascript
module.exports = {
    entry: "./app.js",
    output: {
        filename: "bundle.js"
    },
    watch: true
};
```

#### Webpack Dev Server

```bash
npm install webpack-dev-server -g
webpack-dev-server --inline
```

### Building Multiple Files

```javascript
module.exports = {
    entry:  ["./app.js","./utils.js"],
    output: {
        filename: "bundle.js"
    },
    watch: true
};
```

### Using Loaders

e.g.

```bash
npm install --save-dev babel-core babel-loader
```

```json
// .babelrc
{
    "presets": ["es2015"]
}
```

#### ES6

```js
// ...
module: {
    loaders : [
        {
            test: /\.es6$/,
            exclude: /node_modules/,
            loader: "babel-loader"
        }
    ]
},

resolve: {
    extensions: ['', '.js', '.es6']
}
```

### Using Preloaders

#### JSHint

```js
// ...
module: {
    preLoaders: [
        {
            test: /\.js$/,
            exclude: 'node_modules',
            loader: 'jshint-loader'
        }
    ],
    loaders : [
        {
            test: /\.es6$/,
            exclude: /node_modules/,
            loader: "babel-loader"
        }
    ]
},

resolve: {
    extensions: ['', '.js', '.es6']
}
```

### Creating a Start Script

```js
// package.json
  "scripts": {
    "start": "webpack-dev-server",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

### Production vs. Dev Builds

```bash
webpack -p # minified
```

```bash
# this module removes statements
npm install --save-dev strip-loader
```

```js
var devConfig = require('./webpack.config.js');
var WebpackStrip = require('strip-loader');
var stripLoader = {
    test: [/\.js$/,/\.es6$/],
    exclude: '/node_modules/',
    // we are removing console.log statements
    loader: WebpackStrip.loader('console.log')    
};

devConfig.module.loaders.push(stripLoader);

module.exports = devConfig;
```

```bash
webpack --config webpack-production.config.js -p
```

## Advanced Builds

### Organizing Files and Folders

Using the ```webpack-dev-server``` the files are virtually created.

```js
var path = require('path'); // this module is part of node

module.exports = {
    context: path.resolve('js'), // the root
    entry: ["./app.js","./utils.js"],
    output: {
        path: path.resolve('build/js/'),
        publicPath: 'public/assets/js/', // <-- 
        filename: "bundle.js"
    },
    devServer: {
        contentBase: 'public' // <--
    },
   
   // ..
};
```

### Working with ES6 Modules

Check the babel-loader config section above, plus the .babelrc file.

### Adding Source Maps

```bash
webpack -d
webpack-dev-server -d
```

Then you can use the ```debugger;``` statement.

### Creating Multiple Bundles (e.g. laxy loading)

```js
var path = require('path'); // this module is part of node
var webpack = require('webpack'); // <--

var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('shared.js'); // <--

module.exports = {
    context: path.resolve('js'), // the root
    entry: { // <--
        about: './about_page.js',
        home: './home_page.js',
        contact: './contact_page.js'
    },
    output: {
        path: path.resolve('build/js/'),
        publicPath: '/public/assets/js/',
        filename: "[name].js" // <--
    },
    plugins: [commonsPlugin], // <--
    
    // ...
};
```

## Adding CSS

### CSS and Style Loaders

```bash
npm install css-loader style-loader url-loader file-loader --save-dev
```

```js
// require('./login');
import {login} from "./login";
import {} from "../css/bootstrap/css/bootstrap.css";
import {} from "../css/app.css";

login('admin','radical');

document.write("Hello World!!!");

console.log('App loaded');
```

```js
var path = require('path'); // this module is part of node

module.exports = {
    context: path.resolve('js'), // the root
    entry: ['./app'],
    output: {
        path: path.resolve('build/js/'),
        publicPath: '/public/assets/js/',
        filename: "bundle.js"
    },
    
    devServer: {
        contentBase: 'public'
    },
    watch: true,
    module: {
       
        loaders : [
            {
                test: /\.es6$/,
                exclude: /node_modules/,
                loader: "babel-loader"
            },
            { // <-- for the css
                test: /\.css$/,
                exclude: /node_modules/,
                loader: "style-loader!css-loader"
            },
             { // <-- for the fonts
                test: /\.woff($|\?)|\.woff2($|\?)|\.ttf($|\?)|\.eot($|\?)|\.svg($|\?)/,
                loader: 'url-loader'
             }
        ]
    },
    
    resolve: {
        extensions: ['', '.js', '.es6']
    }
};
```

### Using SCSS and SASS

```bash
npm install sass-loader node-sass --save-dev
```

```js
var path = require('path'); // this module is part of node

module.exports = {
    context: path.resolve('js'), // the root
    entry: ['./app'],
    output: {
        path: path.resolve('build/js/'),
        publicPath: '/public/assets/js/',
        filename: "bundle.js"
    },
    
    devServer: {
        contentBase: 'public'
    },
    watch: true,
    module: {
        
        loaders : [
            {
                test: /\.es6$/,
                exclude: /node_modules/,
                loader: "babel-loader"
            },
            {
                test: /\.css$/,
                exclude: /node_modules/,
                loader: "style-loader!css-loader"
            },
            {
                test: /\.woff($|\?)|\.woff2($|\?)|\.ttf($|\?)|\.eot($|\?)|\.svg($|\?)/,
                loader: 'url-loader'
            },
            {
                test: /\.scss$/,
                exclude: /node_modules/,
                loader: "style-loader!css-loader!sass-loader"
            }
        ]
    },
    
    resolve: {
        extensions: ['', '.js', '.es6']
    }
};
```

### Using LESS

```js
// in loaders section
{
    test: /\.less$/,
    exclude: /node_modules/,
    loader: "style-loader!css-loader!less-loader"
}
```

### Creating Separate CSS Bundle

```bash
npm install extract-text-webpack-plugin --save-dev
```

```js
var path = require('path'); // this module is part of node

var ExtractTextPlugin = require('extract-text-webpack-plugin'); // <--

module.exports = {
    context: path.resolve('js'), // the root
    entry: ['./app'],
    output: {
        path: path.resolve('build/'), // <--
        publicPath: '/public/assets/', // <--
        filename: "bundle.js"
    },
    plugins: [
        new ExtractTextPlugin("styles.css") // <--
    ],
    devServer: {
        contentBase: 'public'
    },
    watch: true,
    module: {
        
        loaders : [
            {
                test: /\.es6$/,
                exclude: /node_modules/,
                loader: "babel-loader"
            },
            {
                test: /\.css$/,
                exclude: /node_modules/,
                loader: ExtractTextPlugin.extract("style-loader","css-loader") // <--
            },
            {
                test: /\.woff($|\?)|\.woff2($|\?)|\.ttf($|\?)|\.eot($|\?)|\.svg($|\?)/,
                loader: 'url-loader'
            },
            {
                test: /\.scss$/,
                exclude: /node_modules/,
                loader: ExtractTextPlugin.extract("style-loader","css-loader!sass-loader")
            }
        ]
    },
    
    resolve: {
        extensions: ['', '.js', '.es6']
    }
};
```

### Auto Prefixer

```bash
npm install autoprefixer-loader --save-dev
```

```js
var path = require('path'); // this module is part of node

module.exports = {
    context: path.resolve('js'), // the root
    entry: ['./app'],
    output: {
        path: path.resolve('build/js'),
        publicPath: '/public/assets/js',
        filename: "bundle.js"
    },
    devServer: {
        contentBase: 'public'
    },
    watch: true,
    module: {
        
        loaders : [
            {
                test: /\.es6$/,
                exclude: /node_modules/,
                loader: "babel-loader"
            },
            {
                test: /\.css$/,
                exclude: /node_modules/,
                loader: "style-loader!css-loader!autoprefixer-loader" 
            },
            {
                test: /\.woff($|\?)|\.woff2($|\?)|\.ttf($|\?)|\.eot($|\?)|\.svg($|\?)/,
                loader: 'url-loader'
            },
            {
                test: /\.scss$/,
                exclude: /node_modules/,
                loader: "style-loader!css-loader!autoprefixer-loader!less-loader"
            }
        ]
    },
    
    resolve: {
        extensions: ['', '.js', '.es6']
    }
};
```

## Adding Images & Fonts to Your Build

We use the url-loader and play the limit parameter.

### Adding Images

We require the image in the js or css.

```bash
npm install url-loader --save-dev
```

```js
{
    test: /.(png|jpg)$/,
    exclude: '/node_modules/,
    loader: 'url-loader?limit=10000' 
    // bundles images below this size otherwize does a request for the image
}
```

### Adding Fonts

```bash
npm install url-loader --save-dev
```

```js
{
    test: /.(png|jpg|ttf|eot)$/,
    exclude: '/node_modules/,
    loader: 'url-loader?limit=10000' 
    // bundles images below this size otherwize does a request for the image
}
```

## Webpack Tools

### Connect Middleware

#### Node

- ejs : templating system (one fo them)
- express: web framework
- morgan: logger facility

```js
var webpackMiddleware = require('webpack-dev-middleware');
var webpack = require('webpack');

var config = require('./webpack.config');

app.use(webpackMiddleware(webpack(config), {
    publicPath: '/build',
    headers: {'X-Custom-Webpack-Header': 'yes'},
    stats: {
        color: true
    }
}));
```

### Creating a custom loader

The loader it's just a js module. Loaders deal with files.

```js
loaders: [
    {
        test: /\.json$/,
        exclude: /node_modules/,
        loader: "path.resolve('loaders/strip')" // being strip the module we've created
    }
]
```

### Using plugins

Plugins work on the bundle basis. Loaders with files seperately.

```js
var webpack = require('webpack');
var TimestampWebpackPlugin = require('timestamp-webpack-plugin');

// ... 
plugins: [
    new webpack.ProvidePlugin({
        $: "jquery",
        jQuery: "jquery",
        "window.jQuery": "jquery"
    }),
    new TimestampWebpackPlugin({
        path: __dirname,
        filename: 'timestamp.json'
    }),
    new webpack.BannerPlugin("**************\nGenerated by webpack\n***************\n");
]
```