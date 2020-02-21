# ton-client-web-js

TON Labs SDK Client Library for Web

## Run an example:
* Download and run an example:
```
$ git clone https://github.com/tonlabs/ton-client-web-js.git
$ cd ton-client-web-js
$ npm install
$ cd example
$ npm install
$ npm run web
```
* Open http://localhost:4000/ in the browser

This example connects to net.ton.dev and returns the top 10 accounts holding the most grams (filtered by balance, top to bottom).


## Using ton-client-web-js

### Requirements
You can run ton-client-web-js in a browser.

In this example, we'll use webpack to manage assets. Since it requires[Node.js](nodejs.org), you’ll need to install the latest version.

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

Next, you'll need to create  `dist/index.html` in your directory. It will act as the browser entry point:

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
npm install -g http-server
```

Now you can run your app:

```
npx http-server dist
```

Doing so will show you the links to the app within the browser.
Open your browser and take a look at your first app!
Hit CTRL-C to stop the server.

---

See [https://docs.ton.dev](https://docs.ton.dev) for documentation

---
Copyright 2018-2020 TON DEV SOLUTIONS LTD.

Licensed under the SOFTWARE EVALUATION License (the "License"); you may not use
this file except in compliance with the License.

You may obtain a copy of the License at: https://www.ton.dev/licenses

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific TON DEV software governing permissions and
limitations under the License.
