# TON Javascript Web SDK

[![npm publish](https://github.com/tonlabs/ton-client-web-js/workflows/npm%20publish/badge.svg)](https://www.npmjs.com/package/ton-client-web-js)

## Community links:

[![Chat on Telegram](https://img.shields.io/badge/chat-on%20telegram-9cf.svg)](https://t.me/ton_sdk)  [![Gitter](https://badges.gitter.im/ton-sdk/community.svg)](https://gitter.im/ton-sdk/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

## Documentation

[GraphQL API and SDK documentation](https://docs.ton.dev/86757ecb2/p/92b041-overview)

The JavaScript SDK implements the client-side libraries used by applications working with TON OS GraphQL API.  
This package supports web (browser), mobile-web, and server (Node.js) clients. 

[Common Javascript SDK](https://github.com/tonlabs/ton-client-js) is distributed via [npm package](https://www.npmjs.com/package/ton-client-js).


**Attention!** Because the JS library uses pre-compiled [core sdk rust library](https://github.com/tonlabs/TON-SDK), you need to 
install it via platform-dependable [web package](https://www.npmjs.com/package/ton-client-web-js)  that will automatically 
install common js package +  download and link pre-compiled rust core to your project:


```
npm install ton-client-web-js
```

To get started using TON Javascript SDK, see [Add SDK to your Application](https://docs.ton.dev/86757ecb2/p/622af4-web).


# Using `ton-client-web-js`

## Requirements

You can run `ton-client-web-js` in a browser.

In this example, we'll use webpack to manage assets. Since it requires [Node.js](nodejs.org), you’ll need to install the latest version.

Let's start with a clean  [webpack](https://webpack.js.org/) project:

```bash
mkdir HelloTonClient
cd HelloTonClient
npm init -y
npm install webpack webpack-cli --save-dev
```

Now create the starting files in the root directory of the project:

```
 |- /src
   |- index.js
 |- /dist
   |- index.html
```

Here, the `src` file contains our source code for further webpack usage, and `dist` contains files that are directly served to the browser.

Next, create `dist/index.html` in your directory. It will act as the browser entry point:

```html
<!doctype html>
<html>
  <head>
    <title>Example</title>
  </head>
  <body>
    <script src="main.js"></script>
  </body>
</html>
```

Webpack will later use `src` to generate `main.js`.

Use npm to install `ton-client-web-js` package:

```
npm install ton-client-web-js
```

To copy `tonclient.wasm` from `node_modules\ton-client-web-js` use `copy-webpack-plugin` in `webpack.config.js`.

Install copy-webpack-plugin
```
npm install copy-webpack-plugin --save-dev
```
Next, create `webpack.config.js` in your directory 
```js
const CopyPlugin = require('copy-webpack-plugin');

module.exports = {
  mode: 'development',
  plugins: [
    new CopyPlugin({
      patterns: [
        { from: './node_modules/ton-client-web-js/tonclient.wasm' }
      ],
    }),
  ],
};
```

Finally, add the code below to `src/index.js`:

```js
import { TONClient, setWasmOptions } from 'ton-client-web-js';

function addHTML(message) {
    document.body.insertAdjacentHTML("beforeend", `<p>${message}</p>`);
}

window.addEventListener('load', () => {
    (async () => {
        // Adding an HTML update function
        setWasmOptions({
            addHTML,
        });
        let createStart = Date.now();
        // creating a TONClient wit a net.ton.dev connection
        const client = await TONClient.create({
            servers: ['net.ton.dev']
        });
        // requesting TONClient creation time stamp
        addHTML(`Client creation time: ${(Date.now() - createStart)}`);
        // displaying the current client version
        addHTML(`Client version: ${await client.config.getVersion()}`);
        addHTML(`Client connected to: ${await client.config.data.servers}`);
        const queryStart = Date.now();
        // requesting top 10 accounts sorted by balance from net.ton.dev/graphql
        const accounts = await client.queries.accounts.query({}, 'id balance', [{path:'balance', direction:'DESC'}], 10);
        addHTML(`Query time: ${(Date.now() - queryStart)}`);
        // displaying the data
        addHTML(`<table>${accounts.map(x => `<tr><td>${x.id}</td><td>${BigInt(x.balance)}</td></tr>`).join('')}</table>`);
        // displaying the data received time stamp
        addHTML(`Now is: ${new Date()}`);
    })();
});
```

To finish the example, let's build a `dist/main.js` from our source:

```
npx webpack
```

To check if it works we'll need a simple HTTP server:

```
npm install light-server
```

Now you can run your app:

```
npx light-server -s dist
```

Doing so will show you the links to the app within the browser.
Open your browser and take a look at your first app!
Hit CTRL-C to stop the server.

---
See [https://docs.ton.dev/86757ecb2/p/92b041-overview](https://docs.ton.dev/86757ecb2/p/92b041-overview) for documentation


---
Copyright 2018-2020 TON DEV SOLUTIONS LTD.
