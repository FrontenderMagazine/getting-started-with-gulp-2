<section class="post-content">
**[Gulp][1]** is a streaming build system. You can read more about it **
[here][2]**.

### Getting Started {#gettingstarted}

Install gulp globally 

    npm install -g gulp  
    

Get the required gulp plugins from **[here][3]**. For our example we are gonna
need gulp-browserify, gulp-livereload, gulp-concat and gulp-styl.

    npm install --save gulp-browserify gulp-concat gulp-styl gulp-livereload tiny-lr  
    

*Note* : `tiny-lr` is required by `gulp-livereload`

### Project Structure {#projectstructure}

    |-src/
        |-index.js
        |-lib/
    |-css/
        |-index.css
    |-index.html
    |-package.json
    |-README.md
    

### Configuring Gulp {#configuringgulp}

Create a file named `gulpfile.js` in your project root. Now lets write the
tasks that`gulp` should take care of

    gulp.task('default', function() {  
        gulp.run('lr-server', 'scripts', 'styles');
    });
    

When you run `gulp` in your CLI the default task is run which in this case
triggers`lr-server, scripts and styles` task. Lets expand these task

*   **Scripts** : This task takes all the script files specified in the `src`
    and bundles them to the destination with
   `browserify` 

    gulp.task('scripts', function() {  
        gulp.src(['src/**/*.js'])
            .pipe(browserify())
            .pipe(concat('dest.js'))
            .pipe(gulp.dest('build'))
            .pipe(refresh(server))
    })
    

*   **Styles** : You might need to preprocess your css, So lets get a task
    ready for that
   

    gulp.task('styles', function() {  
        gulp.src(['css/**/*.css'])
            .pipe(styl({compress : true}))
            .pipe(gulp.dest('build'))
            .pipe(refresh(server))
    })
    

*   **Livereload** : Finally we need a livereload server :)

    gulp.task('lr-server', function() {  
        server.listen(35729, function(err) {
            if(err) return console.log(err);
        });
    })
    

*   **Watch** : good thing is gulp comes with built-in watch system. You could
    use this to run specific task when certain file[s] changes.
   

    gulp.watch('src/**', function(event) {  
        gulp.run('scripts');
    })
    

Here were are running scripts task whenever any changes to files under `src`
directory takes place.

### Complete Setup {#completesetup}

So in the end your `gulpfile.js` should look something like this. 

    var gulp = require('gulp');  
    var browserify = require('gulp-browserify');  
    var concat = require('gulp-concat');  
    var styl = require('gulp-styl');  
    var refresh = require('gulp-livereload');  
    var lr = require('tiny-lr');  
    var server = lr();
    
    gulp.task('scripts', function() {  
        gulp.src(['src/**/*.js'])
            .pipe(browserify())
            .pipe(concat('dest.js'))
            .pipe(gulp.dest('build'))
            .pipe(refresh(server))
    })
    
    gulp.task('styles', function() {  
        gulp.src(['css/**/*.css'])
            .pipe(styl({compress : true}))
            .pipe(gulp.dest('build'))
            .pipe(refresh(server))
    })
    
    gulp.task('lr-server', function() {  
        server.listen(35729, function(err) {
            if(err) return console.log(err);
        });
    })
    
    gulp.task('default', function() {  
        gulp.run('lr-server', 'scripts', 'styles');
    
        gulp.watch('src/**', function(event) {
            gulp.run('scripts');
        })
    
        gulp.watch('css/**', function(event) {
            gulp.run('styles');
        })
    })
    

running gulp in the cli should produce output closer to this.   
![Gulp][4]

### Reference {#reference}

If you dont find the required gulp plugin ,you could also write your **[own][5]
**.</section>

 [1]: https://github.com/wearefractal/gulp
 [2]: https://github.com/wearefractal/gulp/wiki
 [3]: https://npmjs.org/browse/keyword/gulpplugin
 [4]: img/KjSSZ.jpg
 [5]: https://github.com/wearefractal/gulp/wiki/Writing-a-gulp-plugin