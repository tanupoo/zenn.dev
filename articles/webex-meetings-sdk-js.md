---
title: "Webex Meetings: webex-js-sdk"
emoji: "ð¦"
type: "tech"
topics: [Cisco, Webex]
published: false
created: "2022-02-25"
---

[README](https://github.com/webex/webex-js-sdk#samples)ã«æ¸ãã¦ããéãã https://js.samples.s4d.io/ ã§ãµã³ãã«ãè©¦ãããã©ãåä½ãèª¿ã¹ãã«ã¯æåã§è©¦ãããã

https://github.com/webex/webex-js-sdk/blob/master/CONTRIBUTING.md#running-samples-locally ãåèã« [Webex JS SDK](https://github.com/webex/webex-js-sdk) ã®ãµã³ãã«ãæåã§èµ·åãã¦ã¿ãã

## ãµã³ãã«ã®èµ·å

ã¾ãã¯ Running Samples Locallyã«å¾ã£ã¦ã¿ãã

```
git clone git@github.com:webex/webex-js-sdk.git
cd webex-js-sdk
```

```
% npm install
npm WARN old lockfile 
npm WARN old lockfile The package-lock.json file was created with an old version of npm,
npm WARN old lockfile so supplemental metadata must be fetched from the registry.
npm WARN old lockfile 
npm WARN old lockfile This is a one-time fix-up, please be patient...
npm WARN old lockfile 
npm WARN EBADENGINE Unsupported engine {
npm WARN EBADENGINE   package: 'webex-js-sdk@1.154.2',
npm WARN EBADENGINE   required: { node: '^14.x', npm: '6.x' },
npm WARN EBADENGINE   current: { node: 'v17.3.0', npm: '8.3.0' }
npm WARN EBADENGINE }

> webex-js-sdk@1.154.2 preinstall
> [ -f ./package-lock.json ] && [ "$npm_config_refer" != "ci" ] && npx npm-force-resolutions || exit 0


> webex-js-sdk@1.154.2 postinstall
> patch-package

patch-package 6.4.6
Applying patches...
documentation@13.1.1 â
karma-edgium-launcher@4.0.0-0 â
karma-safari-launcher@1.0.0 â
karma-sauce-launcher@4.3.6 â
node-jose@2.0.0 â

> webex-js-sdk@1.154.2 prepare
> husky install

husky - Git hooks installed

up to date, audited 2909 packages in 54s

187 packages are looking for funding
  run `npm fund` for details

49 vulnerabilities (32 moderate, 15 high, 2 critical)

To address issues that do not require attention, run:
  npm audit fix

To address all issues possible (including breaking changes), run:
  npm audit fix --force

Some issues need review, and may require choosing
a different dependency.

Run `npm audit` for details.
```

```
% npm run build

> webex-js-sdk@1.154.2 prebuild
> npm run clean


> webex-js-sdk@1.154.2 clean
> rimraf packages/node_modules/**/.coverage packages/node_modules/**/dist


> webex-js-sdk@1.154.2 build
> node ./tooling/index.js build

(node:83729) Warning: Accessing non-existent property 'VERSION' of module exports inside circular dependency
(Use `node --trace-warnings ...` to show where the warning was created)
webpack log:
node:internal/crypto/hash:67
  this[kHandle] = new _Hash(algorithm, xofLen);
                  ^

Error: error:0308010C:digital envelope routines::unsupported
    at new Hash (node:internal/crypto/hash:67:19)
    at Object.createHash (node:crypto:130:10)
    at module.exports (/Users/tanupoo/webex-js-sdk/node_modules/webpack/lib/util/createHash.js:135:53)
    at NormalModule._initBuildHash (/Users/tanupoo/webex-js-sdk/node_modules/webpack/lib/NormalModule.js:417:16)
    at handleParseError (/Users/tanupoo/webex-js-sdk/node_modules/webpack/lib/NormalModule.js:471:10)
    at /Users/tanupoo/webex-js-sdk/node_modules/webpack/lib/NormalModule.js:503:5
    at /Users/tanupoo/webex-js-sdk/node_modules/webpack/lib/NormalModule.js:358:12
    at /Users/tanupoo/webex-js-sdk/node_modules/loader-runner/lib/LoaderRunner.js:373:3
    at iterateNormalLoaders (/Users/tanupoo/webex-js-sdk/node_modules/loader-runner/lib/LoaderRunner.js:214:10)
    at iterateNormalLoaders (/Users/tanupoo/webex-js-sdk/node_modules/loader-runner/lib/LoaderRunner.js:221:10)
    at /Users/tanupoo/webex-js-sdk/node_modules/loader-runner/lib/LoaderRunner.js:236:3
    at context.callback (/Users/tanupoo/webex-js-sdk/node_modules/loader-runner/lib/LoaderRunner.js:111:13)
    at /Users/tanupoo/webex-js-sdk/node_modules/babel-loader/lib/index.js:59:71 {
  opensslErrorStack: [ 'error:03000086:digital envelope routines::initialization error' ],
  library: 'digital envelope routines',
  reason: 'unsupported',
  code: 'ERR_OSSL_EVP_UNSUPPORTED'
}

Node.js v17.3.0
```

å¤±æã

`ERR_OSSL_EVP_UNSUPPORTED`ã§ã°ã°ãã¨ã[Node.js 17 is here!](https://medium.com/the-node-js-collection/node-js-17-is-here-8dba1e14e382)ã®OpenSSL 3.0ã« `--openssl-legacy-provider`ãä½¿ãã¨ããã

ç°å¢å¤æ°ã«ã»ãããã¦ååº¦buildãã¦ã¿ãã

```
% export NODE_OPTIONS=--openssl-legacy-provider
% npm run build

> webex-js-sdk@1.154.2 prebuild
> npm run clean


> webex-js-sdk@1.154.2 clean
> rimraf packages/node_modules/**/.coverage packages/node_modules/**/dist


> webex-js-sdk@1.154.2 build
> node ./tooling/index.js build

(node:84777) Warning: Accessing non-existent property 'VERSION' of module exports inside circular dependency
(Use `node --trace-warnings ...` to show where the warning was created)
webpack log:
Hash: 8fdc641f440e8b332eab
Version: webpack 4.46.0
Time: 32459ms
Built at: 02/26/2022 6:38:19 PM
           Asset      Size  Chunks                          Chunk Names
    webex.min.js  2.45 MiB       0  [emitted]        [big]  main
webex.min.js.map  9.52 MiB       0  [emitted] [dev]         main
Entrypoint main [big] = webex.min.js webex.min.js.map
   [9] ./packages/node_modules/@webex/webex-core/src/index.js + 21 modules 160 KiB {0} [built]
       | ./packages/node_modules/@webex/webex-core/src/index.js 2.5 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/webex-plugin.js 5.79 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/plugins/logger.js 1.59 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/credentials/index.js 462 bytes [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/services/index.js 1.01 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/stateless-webex-plugin.js 3.65 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/batcher.js 8.59 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/page.js 4.02 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/credentials/grant-errors.js 5.36 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/credentials/scope.js 600 bytes [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/credentials/token.js 16.5 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/credentials/credentials.js 17.9 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/services/constants.js 311 bytes [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/services/service-url.js 3.39 KiB [built]
       | ./packages/node_modules/@webex/webex-core/src/lib/services/service-catalog.js 17 KiB [built]
       |     + 7 hidden modules
  [63] (webpack)/buildin/global.js 472 bytes {0} [built]
  [98] ./node_modules/lodash/merge.js 1.19 KiB {0} [built]
 [108] ./packages/node_modules/@webex/internal-plugin-device/src/index.js + 7 modules 39.9 KiB {0} [built]
       | ./packages/node_modules/@webex/internal-plugin-device/src/index.js 849 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-device/src/constants.js 607 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-device/src/device.js 23.7 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-device/src/features/index.js 202 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-device/src/interceptors/device-url.js 3.73 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-device/src/features/feature-model.js 7.51 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-device/src/features/feature-collection.js 600 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-device/src/features/features-model.js 2.62 KiB [built]
 [706] ./packages/node_modules/webex/src/index.js 461 bytes {0} [built]
 [707] ./node_modules/@babel/polyfill/lib/index.js 686 bytes {0} [built]
 [708] ./node_modules/@babel/polyfill/lib/noConflict.js 567 bytes {0} [built]
 [879] ./node_modules/@babel/polyfill/node_modules/core-js/library/fn/global.js 87 bytes {0} [built]
 [892] ./packages/node_modules/webex/src/webex.js 2.38 KiB {0} [built]
[1357] ./packages/node_modules/@webex/plugin-meetings/src/index.js + 107 modules 1 MiB {0} [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/index.js 356 bytes [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/constants.js 40.4 KiB [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/config.js 3.03 KiB [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/meetings/index.js 34.4 KiB [built]
       | ./node_modules/webrtc-adapter/src/js/adapter_core.js 435 bytes [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/common/logs/logger-config.js 190 bytes [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/common/logs/logger-proxy.js 1.06 KiB [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/metrics/config.js 14.7 KiB [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/metrics/index.js 21.2 KiB [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/common/config.js 249 bytes [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/common/logs/request.js 3.2 KiB [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/common/events/trigger-proxy.js 685 bytes [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/media/index.js 20.8 KiB [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/analyzer/analyzer.js 3.02 KiB [built]
       | ./packages/node_modules/@webex/plugin-meetings/src/common/errors/password-error.js 2.24 KiB [built]
       |     + 93 hidden modules
[1358] ./packages/node_modules/@webex/plugin-device-manager/src/index.js + 12 modules 44.5 KiB {0} [built]
       | ./packages/node_modules/@webex/plugin-device-manager/src/index.js 416 bytes [built]
       | ./packages/node_modules/@webex/plugin-device-manager/src/device-manager.js 20.1 KiB [built]
       | ./packages/node_modules/@webex/plugin-device-manager/src/config.js 18 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-lyra/src/index.js 475 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-search/src/index.js 2.35 KiB [built]
       | ./packages/node_modules/@webex/plugin-device-manager/src/constants.js 116 bytes [built]
       | ./packages/node_modules/@webex/plugin-device-manager/src/collection.js 802 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-lyra/src/
webpack log:
lyra.js 1.34 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-lyra/src/config.js 103 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-search/src/search.js 4.03 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-search/src/config.js 105 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-lyra/src/space.js 11.2 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-lyra/src/device.js 3.41 KiB [built]
[1359] ./packages/node_modules/@webex/internal-plugin-calendar/src/index.js + 5 modules 20 KiB {0} [built]
       | ./packages/node_modules/@webex/internal-plugin-calendar/src/index.js 5.46 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-calendar/src/calendar.js 9.23 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-calendar/src/config.js 247 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-calendar/src/constants.js 294 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-calendar/src/collection.js 2.38 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-calendar/src/util.js 2.34 KiB [built]
[1360] ./packages/node_modules/@webex/internal-plugin-presence/src/index.js + 5 modules 20.3 KiB {0} [built]
       | ./packages/node_modules/@webex/internal-plugin-presence/src/index.js 1.04 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-presence/src/presence.js 7.85 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-presence/src/config.js 180 bytes [built]
       | ./packages/node_modules/@webex/internal-plugin-presence/src/presence-batcher.js 2.25 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-presence/src/presence-worker.js 8.29 KiB [built]
       | ./packages/node_modules/@webex/internal-plugin-presence/src/constants.js 687 bytes [built]
[1361] ./packages/node_modules/@webex/plugin-people/src/index.js + 3 modules 8.94 KiB {0} [built]
       | ./packages/node_modules/@webex/plugin-people/src/index.js 278 bytes [built]
       | ./packages/node_modules/@webex/plugin-people/src/people.js 5.95 KiB [built]
       | ./packages/node_modules/@webex/plugin-people/src/config.js 385 bytes [built]
       | ./packages/node_modules/@webex/plugin-people/src/people-batcher.js 2.31 KiB [built]
[1362] ./packages/node_modules/@webex/plugin-authorization/src/index.browser.js + 2 modules 995 bytes {0} [built]
       | ./packages/node_modules/@webex/plugin-authorization/src/index.browser.js 143 bytes [built]
       | ./packages/node_modules/@webex/plugin-authorization-browser/src/index.js 472 bytes [built]
       | ./packages/node_modules/@webex/plugin-authorization-browser/src/config.js 365 bytes [built]
    + 1358 hidden modules

WARNING in asset size limit: The following asset(s) exceed the recommended size limit (244 KiB).
This can impact web performance.
Assets: 
  webex.min.js (2.45 MiB)

WARNING in entrypoint size limit: The following entrypoint(s) combined asset size exceeds the recommended limit (244 KiB). This can impact web performance.
Entrypoints:
  main (2.45 MiB)
      webex.min.js


WARNING in webpack performance recommendations: 
You can limit the size of your bundles by using import() or require.ensure to lazy load some parts of your application.
For more info visit https://webpack.js.org/guides/code-splitting/
```

ä»åº¦ã¯ãã¾ããã«ãã§ããæãã

```
% npm run samples:serve

> webex-js-sdk@1.154.2 samples:serve
> webpack serve --color --env NODE_ENV=development

<i> [webpack-dev-server] SSL certificate: /Users/tanupoo/webex-js-sdk/node_modules/.cache/webpack-dev-server/server.pem
<i> [webpack-dev-server] Project is running at:
<i> [webpack-dev-server] Loopback: https://localhost:8000/
<i> [webpack-dev-server] On Your Network (IPv4): https://1.1.1.179:8000/
<i> [webpack-dev-server] On Your Network (IPv6): https://[fe80::1]:8000/
<i> [webpack-dev-server] Content not from webpack is served from './packages/node_modules/samples' directory
```

ãã©ã¦ã¶ãã https://localhost:8000 ã«ã¤ãªãã§ããªã¬ãªã¬è¨¼ææ¸ãOKããã¨ãµã³ãã«ãèµ·åã§ããã

## app.js: ã¡ãã£ã¢ã®è¿½å 

ã¡ãã£ã¢ã¯5ã¤ã

### localStream

getMediaStreams()ã§ meeting.getMediaStreams()å¼ã³åºãã®æ»ããã localStreamãã»ãããã¦ããã

```
      if (localStream && mediaSettings.sendVideo) {
        meetingStreamsLocalVideo.srcObject = localStream;
      }
```
### ä»4ã¤

addMedia()ã§ãmeeting.addMedia()å¼ã³åºãã®ãã¨ãmeetingãªãã¸ã§ã¯ãã® media:readyãããªã¬ã«ãªã£ã¦ã¡ãã£ã¢ãè¿½å ãã¦ããã

        meetingStreamsRemotelVideo
        meetingStreamsRemoteAudio
        meetingStreamsRemoteShare
        meetingStreamsLocalShare

```
  meeting.on('media:ready', (media) => {
    // eslint-disable-next-line default-case
    switch (media.type) {
      case 'remoteVideo':
        meetingStreamsRemotelVideo.srcObject = media.stream;
    :  (çç¥)
```

## app.js: ãã¼ã¯ã³ã®åå¾

browser-plugin-meetings/index.htmlã«Credentialsãå¥åãã¦ãã webex.init()ãã¿ã³ãæ¼ãã
ãã¿ã³ã«ã¯ webex.init()ã¨æ¸ãã¦ãããã©å®éã¯app.jsã§Eventãªã¹ãã¼ãä»è¾¼ãã§ãã£ã¦ initWebex()ãåã«å¼ã°ããã

```
credentialsFormElm.addEventListener('submit', initWebex);
```

initWebex()ã®ä¸­ã§ãWebex.init()ããã

```
  webex = window.webex = Webex.init({
    config: {
      logger: {
        level: 'debug'
      },
      meetings: {
        reconnection: {
          enabled: true
        },
        enableRtx: true
      }
      // Any other sdk config we need
    },
    credentials: {
      access_token: tokenElm.value
    }
  });
```

Webex.init()ã¯ webex/src/webex.jsã§å®ç¾©ããã¦ãã¦ãnew Webex(attrs)ãã¦ããã
Webex()ã®æ¬ä½ã¯ WebexCore.extend()ã§ã@webex/webex-coreã§å®ç¾©ããã¦ããã

## js-sdkã®åä½ãèª¿ã¹ãã

samples/browser-plugin-meetings/index.html ãèµ·ç¹ã«ä½ã¬ãã«é¢æ°ãèª¿ã¹ãã
fetch()ã§ã¯ãªããhttps://github.com/naugtur/xhr ãä½¿ã£ã¦ããã

@webex/plugin-meetings/src/meeting/request.js ã Webex meetingsã®ã¯ã©ã¹ã¨ã¢ã¿ãª>ãä»ãã¦ã
@webex/http-core/src/request/index.js ã« console.log()ãä»è¾¼ãã

```
export default function request(options) {
    console.log("XXX", options);
  if (options.url) {
```
