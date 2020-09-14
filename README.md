Project "t" is a lib built with ncc 0.24, dist file is committed. It
has one external lib "lodash", that's where it triggered the error.

Project "t2" is a webpack built with webpack, linking to "t".


To reproduce the error:
```bash
cd t2/
npm install
npm start
```

Navigate browser to http://127.0.0.1:8080 to see the error in console.
To display better error, can add webpack.config.js

```
module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist'),
  },
  mode: 'development',
  devtool: 'cheap-module-eval-source-map';
};
```


The cause: somehow the `__webpack_require__` in `t/dist/index.js` conflicts
with global var in webpack app t2.
I conform this error went away by manually rename `__webpack_require__` in
`t/dist/index.js` to something else.

In my understanding of js syntax, I don't know how this is possible, because
the `__webpack_require__` in `t/dist/index.js` was inside a function scope.

Unless webpack is doing some code transforming without proper scope analysis
(I guess it assumes var `__webpack_require__` is the global var).
