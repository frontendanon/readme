# gulpfile.js

Подключение плагинов gulp-* (gulp-autoprefixer, gulp-cssnano, gulp-htmlmin, gulp-imagemin, gulp-plumber, gulp-pug, gulp-rename, gulp-sourcemaps, gulp-stylus)

```
var plugins = require('gulp-load-plugins')();
```

Подключение browser-sync

```
var browserSync = require('browser-sync').create();
```

Подключение плагина удаления папок

```
var del = require('del');
```

Подключение gulp

```
var gulp = require('gulp');
```

Переменная перезагрузки страницы
```
var reload = browserSync.reload;
```

Подключение плагина созданий спрайта
```
var spritesmith = require('gulp.spritesmith');
```

Переменная кроссплатформенный разделитель директорий (используется в задаче копирования изображений ```images```)

```
var dirSep = require('path').sep;
```

## gulp develop

> Введя в консоли `gulp develop` запуститься сервер и можно приступать к
> работе. Для работы в адресную строку браузера ввести: localhost:3000

Задача запуска сервера разработки из папки dev

```
gulp.task('browserSync', function() {
  browserSync.init({
    server: 'dev' 
  });
});
```

Задача компиляции pug файла src/index.pug в dev/index.html

```
gulp.task('pug', function() {
  return gulp.src('src/index.pug')
    .pipe(plugins.plumber())
    .pipe(plugins.pug({
      pretty: true
    }))
    .pipe(gulp.dest('dev'));
});
```

> plumber — отлавливает ошибки и выводит их в консоль не прерывая задачи
> разработки `develop`, без него процесс в консоли рушился бы при ошибке
> в коде pug и пришлось бы в ручную запускать задачу `gulp develop`

Задача компиляции stylus файла src/main.styl в dev/css/main.css

```
gulp.task('stylus', function () {
  return gulp.src('src/main.styl')
    .pipe(plugins.plumber())
    .pipe(plugins.sourcemaps.init())
    .pipe(plugins.stylus())
    .pipe(plugins.autoprefixer())
    .pipe(plugins.sourcemaps.write('.'))
    .pipe(gulp.dest('dev/css'));
});
```

> plumber — отлавливает ошибки и выводит их в консоль не прерывая задачи
> разработки `develop`, без него процесс в консоли рушился бы при ошибке
> в коде stylus и пришлось бы в ручную запускать задачу `gulp develop`
> 
> sourcemaps — для вывода в инспекторе браузера styl файла к которому
> принадлежат стили 
> 
> autoprefixer — автопрефиксы

Задача копирования всех картинок расширений png и jpg, кроме картинок начинающихся на sprite

```
gulp.task('images', function () {
  return gulp.src(['src/**/*.{png,jpg}', '!src/**/sprite*.{png,jpg}'])
    .pipe(plugins.rename(function(path) {
      var dirs = path.dirname.split(dirSep);
      dirs.splice(0, 2);
      path.dirname = dirs.join(dirSep)
    }))
    .pipe(gulp.dest('dev/img'));
});
```

> rename — меняет директорию копирования
> > по умолчанию gulp переносит картинки копируя путь к картинке рекурсивно, например, если копировать  `src/blocks/header/logo.png` получится `dev/img/blocks/header/logo.png`  с  rename получится `dev/img/logo.png`

Задача сборки спрайта, ищет файлы png начинающиеся на sprite. Спрайт кладет в директорию `dev/img`, stylus файл в директорию `src/blocks/common`

```
gulp.task('sprite', ['clean:sprite'], function () {
  var spriteData = gulp.src('src/**/sprite*.png').pipe(spritesmith({
    imgName: 'sprite.png',
    cssName: 'sprite.styl',
    imgPath: '../img/sprite.png'
  }));
  spriteData.img.pipe(gulp.dest('dev/img'));
  spriteData.css.pipe(gulp.dest('src/blocks/common'));
});
```

Задача удаления спрайта при генерации нового

```
gulp.task('clean:sprite', function() {
  return del.sync('dev/img/sprite.png');
})
```

Задача разработки gulp develop. Собирает спрайт, копирует картинки, компилирует pug и stylus файлы во время разработки

```
gulp.task('develop', ['browserSync'], function () {
  gulp.watch('src/**/sprite*.png', ['sprite', reload]);
  gulp.watch(['src/**/*.{png,jpg}', '!src/**/sprite*.{png,jpg}'], ['images', reload]);
  gulp.watch('src/**/*.pug', ['pug', reload]);
  gulp.watch('src/**/*.styl', ['stylus', reload]);
});
```

## gulp build

> Введя в консоли `gulp build` :
> 
> - удалится папка `build`
> - скомпилируется минифицированный css из `src/main.styl` в `build/css/main.css`
> - скомпилируется минифицированный html из `src/index.pug` в `build/index.html`
> - сожмутся png, jpg картинки из `src/` и спрайт из `dev/img` в `build/img`

Удаление папки build

```
gulp.task('clean:build', function() {
  return del.sync('build');
});
```

Компиляция `src/main.styl` в `build/css/main.css` файл получается минифицированный

```
gulp.task('css', function() {
  return gulp.src('src/main.styl')
  .pipe(plugins.stylus())
  .pipe(plugins.autoprefixer())
  .pipe(plugins.cssnano())
  .pipe(gulp.dest('build/css'));
});
```

Компиляция `src/index.pug` в `build/index.html` файл получается минифицированный

```
gulp.task('html', function() {
  return gulp.src('src/index.pug')
  .pipe(plugins.pug())
  .pipe(plugins.htmlmin({collapseWhitespace: true}))
  .pipe(gulp.dest('build'));
});
```

Сжатие всех png, jpg изображений из `src/` и спрайта из `dev/img` в `build/img`

```
gulp.task('imagemin', function(){
  return gulp.src(['src/**/*.{png,jpg}', '!src/**/sprite*.{png,jpg}', 'dev/img/sprite.png'])
  .pipe(plugins.imagemin())
  .pipe(gulp.dest('build/img'));
});
```

Задача сборки gulp build в консоли выполнятся задачи clean:build, css, html, imagemin

```
gulp.task('build', [
  'clean:build',
  'css',
  'html',
  'imagemin'
  ], function () {
});
```

```
gulp.task('default', function () {
  console.log('Default task is null \n' +
    'gulp develop - develop project \n' +
    'gulp build - build project'
  );
})
```
