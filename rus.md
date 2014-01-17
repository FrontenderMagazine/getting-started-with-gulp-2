# Простое и удобное gulp‑окружение для разработки html5‑игры меньше, чем за 5 минут

**[Gulp][1]** [/gʌlp/ /галп/] — система потоковой сборки, подробнее о ней можно
прочесть в <del>**[вики][2]**</del> [документации][6] проекта.



### Начнём

Установим gulp глобально: 

    npm install -g gulp    

Вы можете найти все необходимые плагины по тегу [**gulpplugin**][3] на сайте npmjs. 
Для нашего примера понадобятся следующие плагины: *gulp-browserify*, *gulp-livereload*, 
*gulp-concat* и *gulp-styl*.

Выполните установку плагинов следующей коммандой:

    npm install --save gulp-browserify gulp-concat gulp-styl gulp-livereload tiny-lr

*Примечание* : `tiny-lr` понадобится для работы с `gulp-livereload`.


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

Создайте файл *gulpfile.js* в корне вашего проекта. 
Теперь запишем в этот файл простую задачу, которую будет выполнять gulp:

    gulp.task('default', function() {  
        gulp.run('lr-server', 'scripts', 'styles');
    });

Если вы выполните в терминале команду `gulp`, запустится задача по умолчанию, переданная коллбеком в
gulp.task('default'), которая, в свою очередь, запустит вложенные в нее подзадачи `lr-server`, 
`scripts` и `styles`.
Теперь нам нужно написать код для этих подзадач:

*   **scripts**: принимает все файлы по маске `src/**/*.js`,
    обрабатывает их с помощью `browserify`, и складывает в конечную папку;

        gulp.task('scripts', function() {  
            gulp.src(['src/**/*.js'])
                .pipe(browserify())
                .pipe(concat('dest.js'))
                .pipe(gulp.dest('build'))
                .pipe(refresh(server))
        });
    
*   **styles**: возможно вам понадобится препроцессор CSS, давайте 
    напишем задачу для этого;

        gulp.task('styles', function() {  
            gulp.src(['css/**/*.css'])
                .pipe(styl({compress : true}))
                .pipe(gulp.dest('build'))
                .pipe(refresh(server))
        });

*   **livereload**: наконец, нам нужен livereload-сервер :)

        gulp.task('lr-server', function() {  
            server.listen(35729, function(err) {
                if(err) return console.log(err);
            });
        });

*   **watch**: в gulp встроена система реагирования на изменения файлов. 
    Вы можете использовать эту задачу для запуска других необходимых вам задач
    при изменении файлов.

        gulp.watch('src/**', function(event) {  
            gulp.run('scripts');
        });

В этом случае мы запускаем задачу *scripts* каждый раз, когда изменяется любой 
файл внутри папки `src`.


### Завершение установки

По окончанию процесса настройки ваш *gulpfile.js* будет выглядеть так:

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
    

Использование команды gulp в терминале будет выглядеть примерно так:  
![Gulp][4]


### Совет напоследок

Если вы не обнаружили необходимый вам gulp-плагин, вы можете попробовать 
написать свой собственный, руководствуясь **[этой статьей][5]**.

[1]: https://github.com/wearefractal/gulp
[2]: https://github.com/wearefractal/gulp/wiki
[3]: https://npmjs.org/browse/keyword/gulpplugin
[4]: img/KjSSZ.jpg
[5]: https://github.com/gulpjs/gulp/tree/master/docs/writing-a-plugin
[6]: https://github.com/gulpjs/gulp/tree/master/docs
