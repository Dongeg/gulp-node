# gulp-node

使用gulp构建项目流程

```bash
//全局安装gulp，
 npm install gulp -g
//初始化项目,生成packjson.js
npm init
//安装项目依赖
npm install gulp --save-dev
//还有其他依赖如   
//"colors": "^1.0.3",
// "gulp": "^3.8.11",
//"gulp-autoprefixer": "^2.1.0",
//"gulp-imagemin": "^2.2.1",
//"gulp-less": "^3.0.2",
//"gulp-minify-css": "^1.0.0",
//"gulp-ruby-sass": "^1.0.1",
//"gulp-sourcemaps": "^1.5.1",
//"gulp-uglify": "^1.1.0",
//"gulp-watch-path": "^0.0.7",
//"stream-combiner2": "^1.0.2"
```
创建gulpfile.js文件

内容如下

```js

//引入项目依赖
var gulp = require('gulp');
var uglify = require('gulp-uglify');
var minifyCSS = require('gulp-minify-css');
var imagemin = require('gulp-imagemin');
var less = require('gulp-less');
//js处理
gulp.task('script', function() {
    gulp.src('src/js/*.js')
    // 2. 压缩文件
        .pipe(uglify())
    // 3. 另存压缩后的文件
        .pipe(gulp.dest('dist/js'))
});
gulp.task('jsauto', function () {
    // 监听文件修改，当文件被修改则执行 script 任务
    gulp.watch('src/js/*.js', ['script'])
})

//css处理
gulp.task('css', function () {
    // 1. 找到文件
    gulp.src('src/css/*.css')
    // 2. 压缩文件
        .pipe(minifyCSS())
    // 3. 另存为压缩文件
        .pipe(gulp.dest('dist/css'))
})
gulp.task('cssauto', function () {
    // 监听文件修改，当文件被修改则执行 css 任务
    gulp.watch('src/css/*.css', ['css'])
});

//img处理
gulp.task('images', function () {
    // 1. 找到图片
    gulp.src('src/images/*.*')
    // 2. 压缩图片
        .pipe(imagemin({
            progressive: true
        }))
    // 3. 另存图片
        .pipe(gulp.dest('dist/images'))
});
gulp.ask('imgauto',function(){
  gulp.watch('images/*.*)', ['images'])
})

//less处理
gulp.task('less',function(){
  //1.找到less文件
  gulp.src('less/**.less')
  //2.编译为css
      .pipe(less())
  //3.另存文件
      .pipe(gulp.dest(‘dist/css))
});
//在命令行使用 gulp auto 启动此任务
gulp.task('lessauto', function () {
    // 监听文件修改，当文件被修改则执行 less 任务
    gulp.watch('less/**.less', ['less']);
})
```
