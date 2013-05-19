# grunt-este-watch [![Build Status](https://secure.travis-ci.org/Steida/grunt-este-watch.png?branch=master)](http://travis-ci.org/Steida/grunt-este-watch)
> Run predefined tasks whenever watched file changes. LiveReload included.

## Getting Started
This plugin requires Grunt `~0.4.0`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-contrib-watch --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-contrib-watch');
```




## Watch task
_Run this task with the `grunt esteWatch` command._


### Settings

#### options.dirs
Type: `Array.<string>`
Default: [
  'bower_components/closure-library/**/',
  'bower_components/este-library/**/',
  '!bower_components/este-library/node_modules/**/',
  'client/**/{js,css}/**/'
]

List of watched directories.

#### options.livereload
Type: `Object`
Default: {
  enabled: true,
  port: 35729,
  extensions: ['js', 'css']
}

### Example
From [github.com/Steida/este](http://github.com/Steida/este) Gruntfile.coffee.

```js
grunt.initConfig({
  esteWatch: {
    options: {
      dirs: ['bower_components/closure-library/**/',
      'bower_components/este-library/**/',
      '!bower_components/este-library/node_modules/**/',
      'client/**/{js,css}/**/']
    },
    coffee: function(filepath) {
      var files = [{
        expand: true,
        src: filepath,
        ext: '.js'
      }];
      grunt.config(['coffee', 'app', 'files'], files);
      grunt.config(['coffee2closure', 'app', 'files'], files);
      return ['coffee:app', 'coffee2closure:app'];
    },
    soy: function(filepath) {
      grunt.config(['esteTemplates', 'app'], filepath);
      return ['esteTemplates:app'];
    },
    js: function(filepath) {
      grunt.config(['esteUnitTests', 'app', 'src'], filepath);
      var tasks = ['esteDeps:all', 'esteUnitTests:app'];
      if (grunt.option('stage')) {
        tasks.push('esteBuilder:app');
      }
      return tasks;
    },
    styl: function(filepath) {
      grunt.config(['stylus', 'all', 'files'], [{
        expand: true,
        src: filepath,
        ext: '.css'
      }]);
      return ['stylus:all', 'stylus:app'];
    },
    css: function(filepath) {
      if (grunt.option('stage')) {
        return 'cssmin:app';
      }
    }
  }
});
```

#### Live Reloading
Live reloading is built into the watch task and enabled by default.

##### Enabling Live Reload in Your HTML
Once you've started a live reload server you'll be able to access the live reload script. To enable live reload on your page, add a script tag before your closing `</body>` tag pointing to the `livereload.js` script:

```html
<script src="http://localhost:35729/livereload.js"></script>
```

Feel free to add this script to your template situation and toggle with some sort of `dev` flag.

### FAQs

#### What's wrong with grunt-contrib-watch?
It's slow and buggy, because it uses combination fs.fileWatch and fs.watch, for
historical reason. From Node 0.9.2+, fs.watch is ok.

[github.com/Steida/este](http://github.com/Steida/este) Needs maximum performance and
stability, so that's why I had to create yet another Node.js file watcher.
This watcher is continuously tested on Mac, Linux, Win platforms.

#### What are grunt-contrib-watch bugs?
Strange "Abort trap: 6" exceptions. File added in new directory isn't detected.
LiveReload console.log mess during livereloading. Polling to much files. Etc.