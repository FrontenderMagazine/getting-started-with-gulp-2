# Простое и удобное gulp-окружение для разработки игры меньше, чем за 5 минут

**[Gulp][1]** [/gʌlp/ /галп/] потоковая система сборки, подробнее
в **[вики][2]** проекта.


### Начнём

Установим gulp глобально

    npm install -g gulp    

Необходимые плагины вы найдёте по тегу [**gulpplugin**][3] на сайте npmjs. Для этой
статьи понадобятся *gulp-browserify*, *gulp-livereload*, *gulp-concat*
и *gulp-styl*.

    npm install --save gulp-browserify gulp-concat gulp-styl gulp-livereload tiny-lr

*Примечание* : `tiny-lr` понадобится для `gulp-livereload`.


### Структура проекта

    |-src/
        |-index.js
        |-lib/
    |-css/
        |-index.css
    |-index.html
    |-package.json
    |-README.md


### Настройка Gulp

Создайте файл *gulpfile.js* в корне вашего проекта. Теперь напишем простой таск для
gulp:

    gulp.task('default', function() {  
        gulp.run('lr-server', 'scripts', 'styles');
    });

Если вы выполните в терминале `gulp`, то запустится таск по умолчанию, который в
нашем случае и в свою очередь запустит вложенные `lr-server`, `scripts` и `styles`
таски. Теперь напишем код и для этих тасков.

*   **scripts**: этот таск, принимает все файлы по маске `src/**/*.js`
    обрабатывает их с помощью `browserify` и складывает в конечную папку.

        gulp.task('scripts', function() {  
            gulp.src(['src/**/*.js'])
                .pipe(browserify())
                .pipe(concat('dest.js'))
                .pipe(gulp.dest('build'))
                .pipe(refresh(server))
        });
    
*   **styles**: возможно вам понадобится препроцессор, поэтому давайте напишем
    таск для этого.

        gulp.task('styles', function() {  
            gulp.src(['css/**/*.css'])
                .pipe(styl({compress : true}))
                .pipe(gulp.dest('build'))
                .pipe(refresh(server))
        });

*   **livereload**: наконец, нам нужен livereload-сервер =)

        gulp.task('lr-server', function() {  
            server.listen(35729, function(err) {
                if(err) return console.log(err);
            });
        });

*   **watch**: gulp несёт с собой встроенную систему реагирования на изменения
    файлов. Вы можете использовать этот таск, для запуска необходимых тасков
    при изменении файлов.

        gulp.watch('src/**', function(event) {  
            gulp.run('scripts');
        });

В этом случае мы запускаем таск *scripts* каждый раз, когда изменяется любой файл
внутри папки `src`.


### Итоговое решение

В конце концов, ваш *gulpfile.js* будет таким:

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
    

Использование gulp в терминале будет выглядеть примерно так:  
![Gulp][4]


### Справка

Если вы не обнаружили необходимый gulp-плагин, вы можете **[написать свой
собственный][5]**.

[1]: https://github.com/wearefractal/gulp
[2]: https://github.com/wearefractal/gulp/wiki
[3]: https://npmjs.org/browse/keyword/gulpplugin
[4]: img/KjSSZ.jpg
[5]: https://github.com/wearefractal/gulp/wiki/Writing-a-gulp-plugin
