# rjs-spot

node -v
v5.0.0
n
nvm use 6.2.2
Now using node v6.2.2 (npm v3.9.5)
node -v
v6.2.2
cd github/
git clone https://github.com/janusnic/rjs-spot
Клонирование в «rjs-spot»…
remote: Counting objects: 5, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (5/5), done.
Проверка соединения… готово.
cd rjs-spot/
git init
Реинициализация существующего репозиторий Git в ./rjs-spot/.git/


A Primer on the React Ecosystem: Part 1 of 3
posted in software development	on July 6, 2016 by Nitin Punjabi
http://patternhatch.com/2016/07/06/a-primer-on-the-react-ecosystem-part-1-of-3/
    Updates
    Introduction
    In This Installment
    Prerequisites
    The Code
    Project Description
    Project Creation
    Webpack
    Babel
    Hot Module Replacement
    Our First React Component

Updates
2016.07.12: Shortened NPM start and build scripts.

2016.07.06: First publication

Introduction
I’ve been using the React ecosystem extensively at work during the past eight months. We’ve built apps to help us monitor our trading positions, edit our pre-trade risk checks, and control our strategies.

It’s proven a fantastic ecosystem to work with given its composable nature and approachable learning curve. With React, it’s become almost fun to deliver a consumer-grade UI for Enterprise use.

I thought I’d share what I’ve learned so far through a primer. Over three parts, I’m going to show you how to:

    set up an environment for React app development,
    create React components and have them respond to data changes,
    manage app state with Redux.

If you’re approaching the React ecosystem for the first time, my hope is that by the end of this primer you’ll be comfortable enough to start building apps of your own.

In This Installment
In Part One, we’ll be setting up a basic environment for developing React-based apps. By the end of this post, you’ll have a working environment which builds your app and dynamically reloads whenever you make a change. We’ll also have created our first React component.

Prerequisites
I assume you’re familiar with Node and NPM and have them installed. You should also be familiar with Javascript ES6. You don’t need to be an expert in ES6, but know the major additions such as arrow functions and destructuring.

As of this writing, I am using Node 6.2.0 and NPM 3.8.9.

The Code
You can find all the code for Part One here https://github.com/nitinpunjabi/respotify/tree/part01. Each part will have its own branch with the master branch containing all the code written so far.

Project Description
We’re going to build an app which hooks up to the Spotify backend. Our app will allow a user to:

    retrieve the albums of an artist,
    retrieve the tracks for a given album,
    play the opening 30 seconds of a track.

Here’s a mockup:
Using Spotify’s backend will allow us to stay focused on frontend development.

Project Creation
Let’s start by creating our project.

mkdir respotify
cd respotify
npm init -y

This should create a package.json file in your project’s root directory.

Wrote to /home/janus/github/rjs-spot/package.json:

{
  "name": "rjs-spot",
  "version": "1.0.0",
  "description": "node -v v5.0.0 n nvm use 6.2.2 Now using node v6.2.2 (npm v3.9.5) node -v v6.2.2 cd github/ git clone https://github.com/janusnic/rjs-spot Клонирование в «rjs-spot»… remote: Counting objects: 5, done. remote: Compressing objects: 100% (4/4), done. remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0 Unpacking objects: 100% (5/5), done. Проверка соединения… готово. cd rjs-spot/ git init Реинициализация существующего репозиторий Git в ./rjs-spot/.git/",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/janusnic/rjs-spot.git"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/janusnic/rjs-spot/issues"
  },
  "homepage": "https://github.com/janusnic/rjs-spot#readme"
}

Next, create a src directory in your project’s root directory. In the src directory, create index.html:

<!DOCTYPE html>
<html>
  <head>
    <title>Respotify</title>
  </head>
  <body>
    <h1>Respotify</h1>
    <div id="container"></div>
    <script src="/bundle.js"></script>
  </body>
</html>

index.html is the entry point for our app. There are two lines of interest here:
    <div id="container"></div>
    <script src="/bundle.js"></script>

[Line 8] Our React components will be rendered inside the container div. This will become clear later in this post.

[Line 9] The build process we’re going to put together will combine all our JS files into a single bundle.js file, which is then pulled in by index.html and executed.

Next, create index.js:
console.log('Hello, world!');

index.js is the primary Javascript file for our app. For now, we’ll just have it log something to the console to ensure our build process works.

At this point, your project should look like this:
respotify
 -src
  --index.html
  --index.js
 --package.json
 Webpack
 We’ll now start putting together our build environment from scratch. You may have heard of React boilerplate projects such as this which provide complete development setups. While they’re useful, I feel it’s instructive to know how to create at least a basic setup yourself so that you know the major things that are happening under the hood.

 Our build environment starts with Webpack. At its simplest, Webpack bundles your assets for you. And while there are a number of bundlers out there, Webpack has gained traction in the React community for other features it offers; a few of which we’ll be using. https://webpack.github.io/
 https://github.com/kriasoft/react-starter-kit

 Let’s start by installing Webpack:
 npm install webpack --save-dev

 Once the installation is complete, check your package.json file for whether Webpack is under your devDependencies.

 We want Webpack to do two things:

     Take our application code and generate static assets from the code.
     Start a development server to serve up the assets.

 Since Webpack is driven by configuration, we’ll start there. Create a file called webpack.config.js in the project’s root folder:
 const webpack = require('webpack');
 const path = require('path');

 const PATHS = {
   app: './src/index.js',
   dist: path.join(__dirname, 'dist')
 };

 module.exports = {
   entry: {
     javascript: PATHS.app
   },
   output: {
     path: PATHS.dist,
     publicPath: '/',
     filename: 'bundle.js'
   },
   devServer: {
     contentBase: PATHS.dist
   }
 };

 Let’s go through this.

At the top, we pull in the Webpack and Path modules, then define a few path constants for our app.

[Line 9] This is where our Webpack configuration begins.

[Line 10] This is the entry point for what will eventually become our bundle.js file. In our case, we have a single JS file (index.js) serving as our entry point. Webpack will process index.js as well as any other JS file index.js declares as a dependency, and the dependencies of those dependencies and so on, eventually combining them into a single bundle.js file. Imagine a graph of dependencies collapsing into a single node.

[Line 13] Here’s where we set our output configuration. In this case, we’re telling Webpack to:

    output the results of its compilation to a dist folder,
    set the URL of the output files to the home path,
    call the result of its compilation bundle.js.

[Line 18] Here we’re specifying that Webpack’s dev server should serve assets from the dist folder.

Now try running this command in your terminal (make sure you’re in your project’s root directory and node is on your PATH):
node node_modules/webpack/bin/webpack.js
1

node node_modules/webpack/bin/webpack.js

You should see a dist folder created in your project’s root directory with a bundle.js file in it.

Hash: 4ffc0e024a5f924c546f
Version: webpack 1.13.1
Time: 58ms
    Asset     Size  Chunks             Chunk Names
bundle.js  1.42 kB       0  [emitted]  javascript
   [0] ./src/index.js 26 bytes {0} [built]

   If you look inside bundle.js, you’ll see some Webpack-specific functionality along with our console.log statement near the bottom.

   At this point, your project directory structure should look like this:
   Respotify
-dist
 --bundle.js
-node_modules
-src
 --index.html
 --index.js
--package.json
--webpack.config.js

Now what we also want is to serve this bundle.js file, referenced by index.html, which is currently not in the dist folder. So what we need is to have index.html copied to the dist folder.

To do that, we’ll use the file-loader package. Install it like so:
npm install file-loader --save-dev

We’ll modify our webpack.config.js file to include a reference to our index.html file. We’ll also include a module object to specify our first loader. In essence, loaders are preprocessors which run on files as you require or load them. In this case, we’re using the file-loader loader to copy index.html to our output (dist) directory. Add the following highlighted lines to your webpack.config.js file:

const webpack = require('webpack');
const path = require('path');

const PATHS = {
  app: './src/index.js',
  html: './src/index.html',
  dist: path.join(__dirname, 'dist')
};

module.exports = {
  entry: {
    javascript: PATHS.app,
    html: PATHS.html
  },
  output: {
    path: PATHS.dist,
    publicPath: '/',
    filename: 'bundle.js'
  },
  devServer: {
    contentBase: PATHS.dist
  },
  module: {
    loaders: [
      {
        test: /\.html$/,
        loader: "file?name=[name].[ext]"
      }
    ]
  }
};

Now if we run the previous command again:
node node_modules/webpack/bin/webpack.js
1

node node_modules/webpack/bin/webpack.js

You should see index.html copied over into the dist directory. The dist directory should now look like this:
dist
--bundle.js
--index.html
1
2
3

dist
--bundle.js
--index.html

As we create our app, we’ll need to keep re-compiling bundle.js. It’s tedious to run the Webpack command each time. What if we could just have the build process run automatically on each change and our browser refresh to reflect them?

That’s where Webpack Dev Server comes into play. Let’s start by installing it:
npm install webpack-dev-server --save-dev
1

npm install webpack-dev-server --save-dev

Once installed, start the server with this command:
node node_modules/webpack-dev-server/bin/webpack-dev-server.js
1

node node_modules/webpack-dev-server/bin/webpack-dev-server.js

That’ll launch a dev server at http://localhost:8080/webpack-dev-server/.

If you visit that URL, you should see our web app:

Try changing the h1 tag in index.html to something else and saving it. The Webpack dev server should automatically detect the change and reload. On each reload, you should also see the console log output from index.js. So what we have now is a dev server which serves our assets and reloads when it detects a change.

Let’s round out this section by creating a few NPM scripts in our package.json file to make things more convenient.

Add the highlighted lines to the scripts object in package.json:

"scripts": {
  "start": "webpack-dev-server",
  "build": "webpack",
  "test": "echo \"Error: no test specified\" && exit 1"
},

If you haven’t stopped the webpack dev server, stop it. Then try launching the Webpack dev server with:

npm run start

To run the build process manually, use:

npm run build

Why does having the start script simply resolve to webpack-dev-server work? Try executing this in your project’s folder:

npm bin

What you should see is the absolute path of the bin folder containing the executables of your project’s installed node modules. If you investigate the folder, you should a list of executables corresponding to the packages you’ve installed, including webpack and webpack-dev-server. Each time you install a node module locally for your project, its executable then gets added to this bin folder.

When npm runs a script, the .bin folder is added to the PATH, which allows us to simply refer to the executable directly in package.json.

At this point, we have a basic build process going. Next, we’ll augment our build process to allow us to use the latest ES6 features.

2016.07.12 Update
Previously, the start and build scripts looked like this:

"start": "node node_modules/webpack-dev-server/bin/webpack-dev-server.js",
"build": "node node_modules/webpack/bin/webpack.js",

Cory House let me know that including the whole path was unnecessary and could be shortened to what we have now.

Babel
ES6 support varies amongst browsers. So how can you use all the ES6 features without worrying about compatibility issues? One solution is to transpile your ES6 code to ES5, and that’s exactly what we’ll be doing.

We’ll be using Babel for the job. We want Babel to do two things:

    Transpile our ES6 to ES5.
    Transpile our JSX to Javascript. JSX is a React DSL which resembles HTML. You’ll learn all about it when we get to React.

Start by installing these Babel packages:

npm install babel-core babel-loader babel-preset-es2015 babel-preset-react --save-dev

Let’s go through what each package does:

    babel-core: Core Babel compiler
    babel-loader: Webpack loader for Babel
    babel-preset-es2015: A set of Babel plugins for ES6 -> ES5 transpilation
    babel-preset-react: A set of Babel plugins for JSX -> JS transpilation

Before we get to incorporating Babel, let’s make a change to index.js to include some ES6 code. Modify index.js to look like this:

const greeting = (name) => {
  console.log(`Hello, ${name}!`);
};

greeting('world');

Now execute npm run build, and look at the bottom of bundle.js. You should see the preceding function there in its original ES6 form. So no transpilation is taking place. With that in mind, let’s incorporate Babel.

First, we’ll add the babel-loader to our webpack.config.js file. Recall how we already use the file-loader to copy our index.html file to the dist folder. We’ll add a second loader object now for Babel. Add the highlighted lines to your module object
module: {
  loaders: [
    {
      test: /\.html$/,
      loader: "file?name=[name].[ext]"
    },
    {
      test: /\.js$/,
      exclude: /node_modules/,
      loaders: ["babel-loader"]
    }
  ]
}

Here, we’re asking Webpack to apply the babel-loader to all files with a .js extension which aren’t in node_modules.

Next, create a .babelrc file in your project’s root directory:

{
  "presets": ["react", "es2015"]
}

Here we’re asking Babel to use the presets we installed earlier.

That’s it.

Execute npm run build again and check out bundle.js. Our greeting function should now be in its ES5 equivalent.

'use strict';

var greeting = function greeting(name) {
  console.log('Hello, ' + name + '!');
};

greeting('world');


Ok, we’re almost done. What we want next is the ability to reload React components whenever we make a change, without losing state information.

Hot Module Replacement
Let’s start by installing React and ReactDOM, and then the React Hot Loader. Note that we’re installing React and ReactDOM as regular dependencies, and React Hot Loader as a dev dependency.

npm install react react-dom --save

npm install react-hot-loader --save-dev

Next, have Webpack use the hot loader. Open webpack.config.js and modify the babel-loader like so:

loaders: ["react-hot", "babel-loader"]

It’s important that react-hot gets prepended as webpack processes from right to left.

Next, open up package.json and edit the start npm script to look like this:

"start": "webpack-dev-server --hot --inline",

At this point, we’ve further augmented our dev setup to reload React components without losing state. But what does that actually mean? To answer that, we’ll need to test drive our setup by creating a React component.

Our First React Component
Create a file in your src directory called greeting.js:

import React from "react";

export default React.createClass({
  render: function() {
    return (
      <div className="greeting">
        Hello, {this.props.name}!
      </div>
    );
  }
});

We’ve just created our first React component. Let’s examine this line-by-line.

[Line 1] At the top, we import React.

[Line 3] We create a React component class with the React.createClass function, passing it a specification object describing our component.

[Line 4] Every React component must have a render method, which returns the component’s markup.

[Lines 5-9] Inside the render method, we return a block of what looks like HTML–A div element of class greeting wrapping a string. The string itself concatenates ‘Hello, ‘ with a reference to some value.

We’re going to dive into the details of Lines 5-9, but first, let’s get this component on the screen.

Open index.js and modify it like so:
import React from "react";
import ReactDOM from "react-dom";
import Greeting from './greeting';

ReactDOM.render(
  <Greeting name="World"/>,
  document.getElementById('container')
);

We once again start by importing React. We then import ReactDOM which is the API for rendering to the DOM. We also import our greeting component.

We then call ReactDOM’s render method, passing it two arguments:

    The component we want to render.
    Where we wish to render the component.

In our case, we want to render our greeting component in a div with id container. Recall that this container div is in index.html.

Note what the first argument looks like. We pass our greeting component as if it’s an HTML element. We also include a key value pair where the key is name and the value is “World”. name then becomes a property of this particular greeting component.

In essence, what we’re stating in index.js is this:

Render a greeting component, assigning it a name property with value “World”, and render this component inside a div with id container.

Now when the greeting component renders, it should retrieve the name property and include its value in the string. Restart the dev server and verify the component renders as expected:
To test the hot reload, try wrapping the string from greeting.js in h1 tags like so:
        <h1>Hello, {this.props.name}!</h1>
7

        <h1>Hello, {this.props.name}!</h1>

Save it and you should see the component update in place without requiring a full reload.

Now let’s take a step back and revisit this fragment from greeting.js:
    return (
      <div className="greeting">
        Hello, {this.props.name}!
      </div>
    );
5
6
7
8
9

    return (
      <div className="greeting">
        Hello, {this.props.name}!
      </div>
    );

The markup we’re returning is actually JSX, a React-specific DSL that’s convenient for describing tree structures, much like HTML. Our Babel setup then takes this JSX and transpiles it to its equivalent plain Javascript. This leads to two important things to keep in mind:

    JSX is not an extension to Javascript.
    JSX is not necessary. You can use plain Javascript method calls instead.

To demonstrate the second point, let’s use the Babel REPL. On the REPL, make sure the ES2015 and React preset boxes are checked. Copy the entire contents of greeting.js into the editor box on the left, and note the transpiled output of the render method on the right. A fragment such as this:
<div className="greeting" />
1

<div className="greeting" />

gets transpiled to:
_react2.default.createElement("div", { className: "greeting" });
1

_react2.default.createElement("div", { className: "greeting" });

The preceding plain Javascript code is something you can use directly. However, I recommend sticking with JSX as it makes React components easier to both read and write. The fact that it looks like HTML also helps lessen the learning curve. Of course, since JSX is really Javascript, there are a few differences such as JSX’s className vs HTML’s class. We’ll be diving into more JSX in future installments. For now, here’s Facebook’s guide for more information.

Let’s turn our attention now to this line inside the div element:
        Hello, {this.props.name}!
7

        Hello, {this.props.name}!

In JSX, anything enclosed in curly braces is a Javascript expression. So here, we are accessing this component’s props object, and in turn, accessing the name property on the props.

Recall this line from index.js:
  <Greeting name="World"/>,
6

  <Greeting name="World"/>,

When you instantiate a component, you can pass a number of key-value pairs representing that component’s state. So when we instantiated our greeting component, we also specified that it should have a piece of state called name, and the value of that state should be “World”.

Inside the actual greeting component, that state is then available through the component’s props object, using the same name label. When the expression inside the curly braces then gets evaluated, it results in “Hello, World” getting rendered to the browser.

One key thing to keep in mind is that props are immutable. In other words, once state is passed down to a component, the receiving component can no longer change it. This can help with making your component easier to reason about and debug, as well as isolating state management to only a few modules.

Try changing the value of name to your own name in index.js. You should see Webpack detect the change and automatically reload, resulting in the greeting component receiving the new state.

If you’ve followed along and got everything working, then congratulations. You just created a build process and development environment for React-based apps which bundles, transpiles, and hot-reloads. In addition, you created a React component. Keep in mind that you can add a lot more to an environment such as minification, selective bundling, and enabling and disabling various tool features based on where you’re deploying to. But for our purposes, this suffices.

In the next installment, we’ll dive further into React by creating more components and building out our Spotify client. We’ll also add linting to help keep our code tidy.

Expect the next installment to come in about two weeks from the date of this publication. Until then, if you have any comments, suggestions, or corrections, please feel free to post them in the comments or shoot me an email.
