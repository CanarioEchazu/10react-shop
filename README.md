# React paso a paso

En la carpeta clonada, realizamos code .
npm install react react-dom -S (es una dependencia que vamos a usar y no de desarrollo
npm init -y (para inicializar) y me fijo en el archivo package.json
Desde el editor de código, crear una carpeta src y dentro de ella un archivo idex.js. Crear también otra carpeta components y dentro de ella un archivo App.jsx.
En index.js hacer:
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App.jsx';

ReactDOM.render(<App />, document.getElementById('app'));

Creamos otra carpeta public y le agregamos un archivo index.html y le llenamos con http5. Y le agregamos
<body>
    <div id="app"></div>
</body>

Y en App.jsx
import React from 'react';

const App = () => <h1> Canario con React, vamos Argentina Campeon</h1>

export default App;

Hasta aquí esta estructura base de React.
## Configuración Webpack 5 para React.js
Vamos a instalar babel:
npm install @babel/core @babel/preset-env @babel/preset-react babel-loader -D
Despues creamos un archivo .babelrc y le ponemos:
{
    "presets": [
        "@babel/preset-env",
        "@babel/preset-react"
    ]
}

Ahora colocamos
npm install webpack webpack-cli webpack-dev-server -D
Luego, agregamos el archive webpack.config.js donde va a contener toda la configuración de la aplicación.
En ese archivo escribir lo siguiente:
const path = require('path');

module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    },
    mode: 'development',
    resolve: {
        extensions: ['.js', '.jsx'],
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                }
            }
        ]
    }
}

## Configuración de Plugins y Loader para React.JS
Lo primero que tenemos que hacer es instalar los plugin y loaders para usarlas con HTML y CSS
npm install html-loader html-webpack-plugin -D
Y preparamos nuestro archivo de configuración. Colocamos lo siguiente:
const HtmlWebpackPlugin = require('html-webpack-plugin');
debajo de rules del babel loader, ponemos la “,” y escribimos:
            {
                test: /\.html$/,
                use: [
                    {
                    loader: 'html-loader',
                    }
                ]
            },

Despues creamos una nueva debajo de module, un plugin y ponemos:
    plugins: [
        new HtmlWebpackPlugin({
            template: './public/index.html',
            filename: './index.html'
        }),
    ],


OJO EXISTE ESTA CONFIGURACION PERO NO ME FUNCIONABA

    devServer: {
        contentBase: path.join(__dirname, 'dist'),
        compress: true,
        port: 3006,
    }
}
PREFIERO OMITIRLA
Ahora abrimos nuestro package.json y  buscamos en script y agregamos la siguiente configuración:
    "start": "webpack serve --open",
    "build": "webpack --mode production"

Y ahora veamos que esté funcionando nuestra aplicación. Escribimos en la terminal
npm run start
Como está funcionando, vamos a crearle unos cambios para ver que esta funcionando. Cambamos algo del texto del archivo App.jsx dentro de <h1> Hola, aprendo React <h1> y vemos que fue modificada.

## Configuración de Webpack para CSS en React
Hay que instalar las dependencias y plugin para CSS:
npm install mini-css-extract-plugin css-loader style-loader sass-loader -D
Abrimos el archive de configuración webpack.config.js y agregamos en la 3ra línea:
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

También agregamos en ese archivo una nueva regla:
            {
                test: /\.s[ac]ss$/i,
                use: [
                    "style-loader",
                    "css-loader",
                    "sass-loader",
                ],
            }
Y nos faltaría solo agregar otro plugin:
        new MiniCssExtractPlugin({
            filename: '[name].css'
        }),
Ahora dentro de la carpeta src, vamos a crear una carpeta styles. Y en la misma creamos un archivo global.scss. Ahí le vamos a escribir
$base-color: green;
$color: rgba(white, 0.88);

body {
    background-color: $base-color;
    color: $color;
}

Ahora este archivo lo integramos. Abrimos el archivo index.js y colocamos en la 4ta línea
import './styles/global.scss';

Ya que temenos todos nuestros recursos, vamos a ejecutar con:
npm run start

## Optimización de WebPack para React
Para enviarlo a producción, lo primero es instalar los siguiente:
npm install css-minimizer-webpack-plugin terser-webpack-plugin clean-webpack-plugin -D
Una vez instalado, vamos a crear un achivo para configurar el nuevo desarrollo. Copiamos todo lo que esta en la vieja configuración y vamos a modificar en esta DEV en lo siguiente en línea 15:
    mode: 'development',

Ahora en webpack.config.js vamos agregar desde la línea 4:
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
const TerserPlugin = require('terser-webpack-plugin');
const{ CleanWebpackPlugin } = require('clean-webpack-plugin');

Ahora ingresamos en module.exports desde la línea 13 lo siguiente:
        publicPath: '/',

y crear alias desde la línea 18(debajo de extensions):
        alias: {
            '@components': path.resolve(__dirname, 'src/components/'),
            '@styles': path.resolve(__dirname, 'src/styles/'),
        }


Y también agregar en plugin:
        new CleanWebpackPlugin(),
    ],
    optimization: {
        minimize: true,
        minimizer: [
            new CssMinimizerPlugin(),
            new TerserPlugin(),
        ]
    }

Y en module.exports le agregamos en línea 22 debajo de las llaves de resolve

Y ahora tenemos esta configuración lista para modo produccions
    mode: 'production',

Ahora solo nos falta ver en package.json corregir estas configuraciones de start y build. 
    "start": "webpack serve --config webpack.config.dev.js",
    "build": "webpack --config webpack.config.js"
  },

## Deploy del proyecto con React.js
Lo que nos falta es subir nuestra aplicación en netlify.

