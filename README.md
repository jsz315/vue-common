# vue-common
vue研究

'use strict'
require('./check-versions')()

process.env.NODE_ENV = 'production'

const ora = require('ora')
const rm = require('rimraf')
const path = require('path')
const chalk = require('chalk')
const webpack = require('webpack')
const config = require('../config')
const webpackConfig = require('./webpack.prod.conf')
const fs = require('fs')

const spinner = ora('building for production...')
spinner.start()

rm(path.join(config.build.assetsRoot, config.build.assetsSubDirectory), err => {
  if (err) throw err
  webpack(webpackConfig, function (err, stats) {
    spinner.stop()
    if (err) throw err
    process.stdout.write(stats.toString({
      colors: true,
      modules: false,
      children: false,
      chunks: false,
      chunkModules: false
    }) + '\n\n')

    if (stats.hasErrors()) {
      console.log(chalk.red('  Build failed with errors.\n'))
      process.exit(1)
    }

    var files = fs.readdirSync(path.join(__dirname, '../dist/static/css'));
    files.forEach(function(item, index){
      if(/.css$/.test(item)){
        var cssFile = path.join(__dirname, '../dist/static/css/' + item);
        var cssStr = fs.readFileSync(cssFile, "utf-8");
        cssStr = cssStr.replace(/url\(\/static/ig, "url(..");
        cssStr = cssStr.replace(/url\(static/ig, "url(../");
        fs.writeFileSync(cssFile, cssStr);
        console.log(chalk.cyan('  replace css path success.\n'))
      }
    })

    console.log(chalk.cyan('  Build complete.\n'))
    console.log(chalk.yellow(
      '  Tip: built files are meant to be served over an HTTP server.\n' +
      '  Opening index.html over file:// won\'t work.\n'
    ))
  })
})
