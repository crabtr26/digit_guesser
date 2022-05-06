# ML in the browser, on-chain demo

This is a demo website using the Pyodide package - https://pyodide.org/en/stable/.

The site uses a slightly modified version of the SVM classifier from the sklearn docs to predict a digit drawn by a site visitor -https://scikit-learn.org/stable/auto_examples/classification/plot_digits_classification.html#sphx-glr-auto-examples-classification-plot-digits-classification-py.

The model is deployed in the browser directly, so the data does not leave the client's browser. The site is also served from the Internet Computer blockchain - https://nhrm2-5aaaa-aaaad-qau4a-cai.ic.fleek.co/

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
