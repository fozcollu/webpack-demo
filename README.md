Öncelikle webpack-demo adında bir dizin oluşturarak projemize başlayalım:

$ mkdir webpack-demo
$ cd webpack-demo
$ npm init -y
$ npm install webpack webpack-cli --save-dev

-- package.json dosyasında yer alan "main": "index.js" kısmını kaldıralım ve "private": true, özelliğini ekleyelim. Bu sayede yanlışlıkla proje paketinin publish edilmesi durumu önlenmiş olacaktır.

- src/index.html, src/index.js dosyalarını oluşturalım. 

-- index.html aşağıdaki gibi olmalıdır.
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Getting Started</title>
  </head>
  <body>
   <script src="main.js"></script>
  </body>
</html>

-- index.js de aşağıdaki gibi olmalı. Lodash kullanmak için $ npm install --save lodash

import _ from 'lodash';

function component() {
  const element = document.createElement('div');

  // Lodash, now imported by this script
  element.innerHTML = _.join(['Hello', 'webpack'], ' ');

  return element;
}

document.body.appendChild(component());
----------------------------------------------------

Gelelim webpack yapılandırmasına. Bazı bağımlılıklar yükleyelim.
$ npm install html-webpack-plugin --save-dev
$ npm install css-loader style-loader --save-dev
$ npm install --save-dev mini-css-extract-plugin

*html-webpack-plugin -> html dosyalarını paketler
*css-loader -> css dosyalarını paketler
*style-loader -> <style> etiketleri içindekileri paketler
*MiniCssExtractPlugin -> css'leri ayrı dosyalar halinde üretilmesini sağlar.

** Babel: Eski tarayıcılarla uyumlu javascript kodunun oluşturulmasını sağlar.
$ npm install @babel/core babel-loader @babel/preset-env --save-dev

*@babel/core: Babel'ın temel kod dönüştürme fonksiyonlarını barındırır.
*babel-loader: webpack'in babel'ı kullanması için olan adaptör bileşeni.
*@babel/preset-env: Modern JavaScript kodunun ES5 karşılıklarına dönüştürülebilmesi için gereklidir. 

-- webpack.config.js dosyamızın son hali aşağıdaki gibi olmalıdır.

-----------------------------------------------------------------------------
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
    mode: 'production',
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader'],
            },
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: ["babel-loader"]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "src", "index.html")
        }),
        new MiniCssExtractPlugin()
    ]
};

-------------------------------------------------------------------------------

** Webpack-dev-server ile local sunucu üzerinde geliştirim yapma

Bu metodun eklenmesiyle birlikte webpack,websocket kullanarak otomatik olarak değişikliklerin tarayıcıya yansıtılması sağlar.
npm start komutunu çalıştırdığınızda webpack, ilgili script'lerden bundle'ı oluşturacak ve bunu local server'da çalıştırarak Google Chrome'u açacak, böylece proje kodunu değiştirip kaydettiğinizde otomatik olarak web tarayıcısında da çıktısı değiştirilecektir.

$ npm install webpack-dev-server --save-dev

**Packege.json dosyasındaki scriptleri aşağıdaki gibi güncelleyelim 
--------------------------------------------------------------------------
"scripts": {
   "build:prod": "webpack --mode production",
   "build:dev": "webpack --mode development",
   "start": "webpack serve --open chrome",
},
---------------------------------------------------------------------------

