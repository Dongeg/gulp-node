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
有时候项目过大只需要编译有改动的文件

可以做如下配置

注意必须先执行正常的编译然后再执行按需编译，否则最后只生成有有改动的文件

gulpfile.js

```
//引入依赖
var gulp = require('gulp')
var gutil = require('gulp-util')

var uglify = require('gulp-uglify')
var watchPath = require('gulp-watch-path')

//创建任务
gulp.task('watchjs', function () {
    gulp.watch('src/js/**/*.js', function (event) {
    	//监听路径
        var paths = watchPath(event, 'src/', 'dist/')
        /*
        paths
            { srcPath: 'src/js/log.js',
              srcDir: 'src/js/',
              distPath: 'dist/js/log.js',
              distDir: 'dist/js/',
              srcFilename: 'log.js',
              distFilename: 'log.js' }
        */
        //控制台输出色彩（非必需）
        gutil.log(gutil.colors.green(event.type) + ' ' + paths.srcPath)
        gutil.log('Dist ' + paths.distPath)

        gulp.src(paths.srcPath)
            .pipe(uglify())
            .pipe(gulp.dest(paths.distDir))
    })
})

gulp.task('default', ['watchjs'])
```

//控制台报错输出

```js
var gulp = require('gulp')
var gutil = require('gulp-util')
var uglify = require('gulp-uglify')
var watchPath = require('gulp-watch-path')
var combiner = require('stream-combiner2')

var handleError = function (err) {
    var colors = gutil.colors;
    console.log('\n')
    gutil.log(colors.red('Error!'))
    gutil.log('fileName: ' + colors.red(err.fileName))
    gutil.log('lineNumber: ' + colors.red(err.lineNumber))
    gutil.log('message: ' + err.message)
    gutil.log('plugin: ' + colors.yellow(err.plugin))
}
gulp.task('watchjs', function () {
    gulp.watch('src/js/**/*.js', function (event) {
        var paths = watchPath(event, 'src/', 'dist/')
        /*
        paths
            { srcPath: 'src/js/log.js',
              srcDir: 'src/js/',
              distPath: 'dist/js/log.js',
              distDir: 'dist/js/',
              srcFilename: 'log.js',
              distFilename: 'log.js' }
        */
        gutil.log(gutil.colors.green(event.type) + ' ' + paths.srcPath)
        gutil.log('Dist ' + paths.distPath)

        var combined = combiner.obj([
            gulp.src(paths.srcPath),
            uglify(),
            gulp.dest(paths.distDir)
        ])

        combined.on('error', handleError)
    })
})

gulp.task('default', ['watchjs'])
```


CSS autoprefixer

```js
gulp.task('watchcss', function () {
    gulp.watch('src/css/**/*.css', function (event) {
        var paths = watchPath(event, 'src/', 'dist/')

		gutil.log(gutil.colors.green(event.type) + ' ' + paths.srcPath)
        gutil.log('Dist ' + paths.distPath)

        gulp.src(paths.srcPath)
            .pipe(sourcemaps.init())
            .pipe(autoprefixer({
              browsers: 'last 2 versions'
            }))
            .pipe(minifycss())
            .pipe(sourcemaps.write('./'))
            .pipe(gulp.dest(paths.distDir))
    })
})

```
