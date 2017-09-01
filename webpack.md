



## uglifyjs 压缩去版权信息
```shell
webpack --config webpack.dll.config.js
uglifyjs ./dist/libs/ngcore.dll.js -o ./dist/libs/ngcore.dll.min.js -m -c --comments '/meepo/'c
```

## webpack.dll.config.js 压缩第三方库配置

```js
const webpack = require('webpack')
const library = '[name]_lib'
const path = require('path')

module.exports = {
    entry: {
        ngcore: ['@angular/core']
    },
    output: {
        filename: '[name].dll.js',
        path: 'dist/libs/',
        library
    },
    plugins: [
        new webpack.DllPlugin({
            path: path.join(__dirname, 'dist/[name]-manifest.json'),
            name: library
        }),
        new webpack.optimize.UglifyJsPlugin({
            compress: {
                warnings: false
            }
        })
    ],
}
```

## webpack压缩打包配置
```js
const path = require('path');
const webpack = require('webpack');
const ProgressPlugin = require('webpack/lib/ProgressPlugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin');
const autoprefixer = require('autoprefixer');
const postcssUrl = require('postcss-url');

const { NoEmitOnErrorsPlugin, LoaderOptionsPlugin } = require('webpack');
const { GlobCopyWebpackPlugin, BaseHrefWebpackPlugin } = require('@angular/cli/plugins/webpack');
const { CommonsChunkPlugin } = require('webpack').optimize;
const { AotPlugin } = require('@ngtools/webpack');

const nodeModules = path.join(process.cwd(), 'node_modules');
const entryPoints = ["inline", "polyfills", "sw-register", "styles", "vendor", "main"];

const baseHref = "./"; //基础路径
const deployUrl = "./"; //相对路径
const { version } = require('./src/version');

module.exports = {
    "devtool": "source-map",
    "resolve": {
        "extensions": [
            ".ts",
            ".js"
        ],
        "modules": [
            "./node_modules"
        ]
    },
    "resolveLoader": {
        "modules": [
            "./node_modules"
        ]
    },
    "entry": {
        "main": [
            "./src/main.ts"
        ],
        "polyfills": [
            "./src/polyfills.ts"
        ],
        "styles": [
            "./src/styles.css"
        ],
        "webworker": [
            './src/webWorker/workerLoader.ts'
        ]
    },
    "output": {
        "path": path.join(process.cwd(), "dist"),
        "filename": "[name].bundle." + version + ".min.js",
        "chunkFilename": "[id].chunk." + version + ".min.js"
    },
    "module": {
        "rules": [{
                "enforce": "pre",
                "test": /\.js$/,
                "loader": "source-map-loader",
                "exclude": [
                    /\/node_modules\//
                ]
            },
            {
                "test": /\.json$/,
                "loader": "json-loader"
            },
            {
                "test": /\.html$/,
                "loader": "raw-loader"
            },
            {
                "test": /\.(eot|svg)$/,
                "loader": "file-loader?name=[name].[hash:20].[ext]"
            },
            {
                "test": /\.(jpg|png|gif|otf|ttf|woff|woff2|cur|ani)$/,
                "loader": "url-loader?name=[name].[hash:20].[ext]&limit=10000"
            },
            {
                "exclude": [
                    path.join(process.cwd(), "src/styles.css")
                ],
                "test": /\.css$/,
                "loaders": [
                    "exports-loader?module.exports.toString()",
                    "css-loader?{\"sourceMap\":false,\"importLoaders\":1}",
                    "postcss-loader"
                ]
            },
            {
                "exclude": [
                    path.join(process.cwd(), "src/styles.css")
                ],
                "test": /\.scss$|\.sass$/,
                "loaders": [
                    "exports-loader?module.exports.toString()",
                    "css-loader?{\"sourceMap\":false,\"importLoaders\":1}",
                    "postcss-loader",
                    "sass-loader"
                ]
            },
            {
                "exclude": [
                    path.join(process.cwd(), "src/styles.css")
                ],
                "test": /\.less$/,
                "loaders": [
                    "exports-loader?module.exports.toString()",
                    "css-loader?{\"sourceMap\":false,\"importLoaders\":1}",
                    "postcss-loader",
                    "less-loader"
                ]
            },
            {
                "exclude": [
                    path.join(process.cwd(), "src/styles.css")
                ],
                "test": /\.styl$/,
                "loaders": [
                    "exports-loader?module.exports.toString()",
                    "css-loader?{\"sourceMap\":false,\"importLoaders\":1}",
                    "postcss-loader",
                    "stylus-loader?{\"sourceMap\":false,\"paths\":[]}"
                ]
            },
            {
                "include": [
                    path.join(process.cwd(), "src/styles.css")
                ],
                "test": /\.css$/,
                "loaders": ExtractTextPlugin.extract({
                    "use": [
                        "css-loader?{\"sourceMap\":false,\"importLoaders\":1}",
                        "postcss-loader"
                    ],
                    "fallback": "style-loader",
                    "publicPath": ""
                })
            },
            {
                "include": [
                    path.join(process.cwd(), "src/styles.css")
                ],
                "test": /\.scss$|\.sass$/,
                "loaders": ExtractTextPlugin.extract({
                    "use": [
                        "css-loader?{\"sourceMap\":false,\"importLoaders\":1}",
                        "postcss-loader",
                        "sass-loader"
                    ],
                    "fallback": "style-loader",
                    "publicPath": ""
                })
            },
            {
                "include": [
                    path.join(process.cwd(), "src/styles.css")
                ],
                "test": /\.less$/,
                "loaders": ExtractTextPlugin.extract({
                    "use": [
                        "css-loader?{\"sourceMap\":false,\"importLoaders\":1}",
                        "postcss-loader",
                        "less-loader"
                    ],
                    "fallback": "style-loader",
                    "publicPath": ""
                })
            },
            {
                "include": [
                    path.join(process.cwd(), "src/styles.css")
                ],
                "test": /\.styl$/,
                "loaders": ExtractTextPlugin.extract({
                    "use": [
                        "css-loader?{\"sourceMap\":false,\"importLoaders\":1}",
                        "postcss-loader",
                        "stylus-loader?{\"sourceMap\":false,\"paths\":[]}"
                    ],
                    "fallback": "style-loader",
                    "publicPath": ""
                })
            },
            {
                "test": /\.ts$/,
                "loader": "@ngtools/webpack"
            }
        ]
    },
    "plugins": [
        new webpack.DllReferencePlugin({
            context: __dirname,
            manifest: require('./dist/ngcore-manifest.json')
        }),
        new NoEmitOnErrorsPlugin(),
        new GlobCopyWebpackPlugin({
            "patterns": [
                "assets",
                "favicon.ico"
            ],
            "globOptions": {
                "cwd": "/Users/eoriol/KaiK/webWorkersFactorial/src",
                "dot": true,
                "ignore": "**/.gitkeep"
            }
        }),
        new ProgressPlugin(),
        new HtmlWebpackPlugin({
            "template": "./src/index.html",
            "filename": "./index.html",
            "hash": false,
            "inject": true,
            "compile": true,
            "favicon": false,
            "minify": false,
            "cache": true,
            "showErrors": true,
            "chunks": "all",
            "excludeChunks": [
                'webworker'
            ],
            "title": "Webpack App",
            "xhtml": true,
            "chunksSortMode": function sort(left, right) {
                let leftIndex = entryPoints.indexOf(left.names[0]);
                let rightindex = entryPoints.indexOf(right.names[0]);
                if (leftIndex > rightindex) {
                    return 1;
                } else if (leftIndex < rightindex) {
                    return -1;
                } else {
                    return 0;
                }
            }
        }),
        new BaseHrefWebpackPlugin({}),
        new CommonsChunkPlugin({
            "name": "inline",
            "minChunks": null,
            "chunks": [
                "main",
                "polyfills",
                "styles"
            ]
        }),
        new CommonsChunkPlugin({
            "name": "vendor",
            "minChunks": (module) => module.resource && module.resource.startsWith(nodeModules),
            "chunks": [
                "main"
            ]
        }),
        new ExtractTextPlugin({
            "filename": "[name].bundle.css",
            "disable": true
        }),
        new LoaderOptionsPlugin({
            "sourceMap": false,
            "options": {
                "postcss": [
                    autoprefixer(),
                    postcssUrl({
                        "url": (URL) => {
                            // Only convert root relative URLs, which CSS-Loader won't process into require().
                            if (!URL.startsWith('/') || URL.startsWith('//')) {
                                return URL;
                            }
                            if (deployUrl.match(/:\/\//)) {
                                // If deployUrl contains a scheme, ignore baseHref use deployUrl as is.
                                return `${deployUrl.replace(/\/$/, '')}${URL}`;
                            } else if (baseHref.match(/:\/\//)) {
                                // If baseHref contains a scheme, include it as is.
                                return baseHref.replace(/\/$/, '') +
                                    `/${deployUrl}/${URL}`.replace(/\/\/+/g, '/');
                            } else {
                                // Join together base-href, deploy-url and the original URL.
                                // Also dedupe multiple slashes into single ones.
                                return `/${baseHref}/${deployUrl}/${URL}`.replace(/\/\/+/g, '/');
                            }
                        }
                    })
                ],
                "sassLoader": {
                    "sourceMap": false,
                    "includePaths": []
                },
                "lessLoader": {
                    "sourceMap": false
                },
                "context": ""
            }
        }),
        new AotPlugin({
            "mainPath": "main.ts",
            "entryModule": 'app/app.module#AppModule',
            "hostReplacementPaths": {
                "environments/environment.ts": "environments/environment.ts"
            },
            "exclude": [],
            "tsConfigPath": "src/tsconfig.app.json",
            "skipCodeGeneration": true
        }),
        new webpack.optimize.OccurrenceOrderPlugin(),
        new webpack.optimize.UglifyJsPlugin({
            compress: {
                warnings: false,
                drop_debugger: false,
                drop_console: false
            }
        })
    ],
    "node": {
        "fs": "empty",
        "global": true,
        "crypto": "empty",
        "tls": "empty",
        "net": "empty",
        "process": true,
        "module": false,
        "clearImmediate": false,
        "setImmediate": false
    }
};
```