# Using Grunt with a front-end project for the first time

This is beginner's guide to setting up a project using the popular task runner and a simple directory setup.

## 	Before we start

Grunt is a command line tool that uses a only small amount of commands. If you're unfamiliar using the command line, getting to grips with Grunt is a nice introduction for beginners. For this guide you'll need a code editor and a command line application. I'll be using Terminal (Mac) during this guide.

Having a working knowledge of front-end development is a must. It is also assumed that you’re familiar with Git and have used Github before.

## What is Grunt?

Grunt is a 'task runner' which has thousands of plugins that can be used to automate almost any task. There are other task runners available that may serve your needs better but I won't be getting into the pro's and con's of the alternatives in this guide. 

> The fact is: using any task runner will dramatically increase the speed of your development.

### Setting up

If you've never used Grunt before you will need to:

- Install [Node.js](https://nodejs.org/en/)
- Install [Grunt's command line interface](http://gruntjs.com/getting-started)

Once you're all setup, we can [fork the boilerplate](https://github.com/abbasarezoo/grunt-boilerplate/) and start getting to know Grunt.

## Getting some plugins

All of the plugins that we're going to use will be stored in a directory named *node_modules*. But, you may have noticed there is no such directory in the repo you forked. In all Grunt projects a *node_modules* will include thousands of files, I don't see the point in pushing these files to a remote repo.

### npm-install

You will also see a *package.json* file which contains the name of the project and a list of plugins we will be using. If you're *package.json* file has a pre-populated list of plugins (like in this guide) you can simply run the command `$ npm install` to download them to your local environment. Running this command creates the *node_modules* directory locally and the directories for each plugin within.

> Any additional plugins you add to your project will update this file so you can leave this file alone from now on.

Once we have the plugins we’re ready to look at our Gruntfile and configure some tasks.

## Getting to know your Gruntfile

Each Grunt project has a Gruntfile. This is a configuration file where our tasks are setup. The file should and will always be named *Gruntfile.js* otherwise Grunt will error when your try to run a task. 

Open up the file and take a look at the basic setup. In our file plugins are loaded at the top, tasks configured in the middle and registered at the bottom.

```js
module.exports = function(grunt) {

// LOAD EACH PLUGIN

grunt.loadNpmTasks('grunt-sass');

// TASK CONFIGURATION

grunt.initConfig({
  pkg: grunt.file.readJSON('package.son'),

    sass: {

      options: {
        sourceMap: false
      },

      dist: {
        files: {
          ‘build/style.css': '_css-src/style.scss'
        }
      }

    },

});

// REGISTER EACH TASK

grunt.registerTask('css', [sass]);

};
```

### Interpreting a task

Most tasks share similar characteristics with slight variations depending on how the code was written by the developer who built the plugin. 

Each task tends to have it's own Github repo where you can explore how the task works and setup, [here's one](https://github.com/gruntjs/grunt-contrib-uglify). I would recommend taking a look at the *README.md* of each plugin to get a feel for each one should be used.

> Running tasks, editing tasks, breaking tasks and fixing them will serve you well in your Grunt-based future. 

#### Here's an example image optimisation task:

``` js
imagemin: {
    optimise: {
        files: [{
            expand: true,
            cwd: '_img-src',
            src: ['*.{png,jpg,gif,svg}'],
            dest: 'build/images'
        }]
     }
},
```
- This task as one target: `optimise`
- This looks through the current working directory (`cwd`), where our uncompressed images are saved.
- It then looks for source files (`src`) to optimise. Note in this example we're using a wildcard (`*`) to tell the plugin to target all files in our current working directory with the file extensions of *.png*, *.jpg*, *.gif* and *.svg*.
- It then makes new versions of all the optimised images in the *build/images* directory.

#### Another task example with a different structure, this time compiling Sass:

```js
sass: {
    options: {
        sourceMap: false
    },
    dist: {
        files: {
            ‘build/style.css': '_css-src/style.scss'
        }
    }
},
```
- This task is setup a little differently, with two targets `options` and `dist`.
- The `options` target creates a source map for the compiled CSS file (really handy). This option is currently set to `false` however, which means no source map will be generated.
- The second target of this task dictates where the compiled CSS will be generated to and from directory it is created from.
- The first part shows which directory the final CSS goes. The second part shows the directory where the separate Sass files are located.

## Running tasks

Now you've got to know Grunt it's time to head back to Terminal and start writing some commands.

### Tasks included with this boilerplate

#### Compile CSS

Task name: *[grunt-sass](https://github.com/sindresorhus/grunt-sass)*

Command: `$ grunt css`

- Compiles Sass files inside the *_css-src* directory to CSS files
- Adds in vendor prefixes and minify CSS using posts
- Compiles the resulting CSS files to a specific directory

#### Compile HTML

Task name: *[grunt-codekit](https://github.com/fatso83/grunt-codekit)*

Command: `$ grunt html`

- Compiles multiple HTML files inside the *_html-src* directory into *.kit* files
- Converts *.kit* files to *.html* files and moves them to a specific directory

#### Concatenate Javascript

Task name: *[grunt-contrib-uglify](https://github.com/gruntjs/grunt-contrib-uglify)*

Command: `$ grunt js`

- Concatenates multiple Javascript files inside the *_js-src* directory into a single minified file
- Moves the resulting JS file to a specific directory

#### Optimise images

Task name: *[grunt-contrib-imagemin](https://github.com/gruntjs/grunt-contrib-imagemin)*

Command `$ grunt img`

- Optimises all images inside the *_img-src* directory
- Moves the optimised images to a specific directory

Now you have the ability to run individual tasks your development time will be noticeably quicker. But we can take our newly found knowledge of Grunt a step further.

## Automate the automation

Using Terminal to run individual tasks is a great way to learn Grunt, but this isn't an efficient way of working on a live project. We can take our automation to the next level and we can do this using the *grunt-watch* task.

This task watches a directory and runs specified sub-tasks when additions and changes are made to files within that directory.

The great thing about about *grunt-watch* is you run the command once and leave it to work its magic in the background while you go about your work.

##### Here's an example grunt-watch task:

```js
watch: {
    css: {
        files: ['_css-src/**/*.scss'],
        tasks: ['css'],
            options: {
                spawn: false,
                livereload:35729,
                event: ['added', 'changed']
            },
    },
},
```

- In this example there is a sub-task called `css`
- This sub-task watches the *_css-src* directory and all files within with the file extension *.scss*
- If you refer to the area in the Gruntfile where tasks are registered you'll see 

You will notice in our Gruntfile a number of sub-tasks within our *watch* task. Take some time to familiarise yourself with how sub-tasks are written and how the link with other tasks in the same Gruntfile.

To run the *watch* task simply run `$ grunt watch` and you're good to go.

## Finishing up

While this guide is a super-simple introduction to Grunt, it is a great jumping off point for bigger and better things. My advice would be to get to grips with some basic tasks first, then start experimenting with some of the more complex tasks once you're comfortable. 

But using the simpler tasks that we have used in this guide should make your life a whole lot better.

## Handy links

- [The Grunt boilerplate](https://github.com/abbasarezoo/grunt-boilerplate/)
- [Official Grunt website](http://gruntjs.com/)
- [Node.js website](https://nodejs.org/en/)
- [Chris Coyier’s article on Grunt is excellent](https://24ways.org/2013/grunt-is-not-weird-and-hard/). This is where I started learning Grunt, recommended reading.