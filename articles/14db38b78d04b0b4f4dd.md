---
title: "NativeScript-VueとVueのウェブアプリでコードシェアしようと思ったができなかった"
emoji: "☠️"
type: "tech"
topics:
  - "nativescript"
  - "vue"
  - "yarn"
  - "workspaces"
published: true
published_at: "2020-11-18 19:43"
---

# NativeScript-Vueのコードシェアリング

NativeScript-Vueでのコードシェアリングは[公式のガイドにも掲載されている](https://nativescript-vue.org/en/docs/getting-started/code-sharing/)。それのための[vue-cli-plugin-nativescript-vue](https://www.npmjs.com/package/vue-cli-plugin-nativescript-vue)というパッケージも開発されているのだが、2020年にはいってからは更新が[止まって](https://github.com/nativescript-vue/vue-cli-plugin-nativescript-vue)いる（2020年10月時点で）。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/152050/cdd5e347-0391-cbd4-bf2a-64245e39212f.png)
そのためNativeScript-Vueの開発者のrigor789は次のような方法でコードシェアするのを推奨している：
![](https://storage.googleapis.com/zenn-user-upload/8uq7t0hv2t7pyxlb7mn048naa2ry)
`web`, `mobile`, `common`にわけ、`node_modules`や共通のコード、画像などは`common`に入れ、プラットフォームspecificなコードやロジックはそれぞれ`web`か`mobile`で開発するということらしい。

## workspaceをわける

まずはyarnが入ってなかったので入れる。

### yarn

```
$ brew install yarn
```

### yarn workspace

[yarn workspace](https://classic.yarnpkg.com/en/docs/workspaces/)を使うといいという作者のツイートをもとに、workspaceをつくってみる。`packages/`というフォルダをつくって、その直下のフォルダすべてを登録するのが常套手段のようなので([example 1](https://forum.vuejs.org/t/vue-cli-and-yarn-workspaces/93230), [example 2](https://qiita.com/ktkn304/items/ee4e51f762cf66baf272))、そうしてみよう。


```json: /package.json
{
  "private": true,
  "workspaces": ["packages/*"],
}
```
`/packages/common`フォルダは今のところカラなので、`package.json`だけつくって次のようにする。`"version"`はなんでもいいが、あとで使うことになる。
```json: /packages/common/package.json
{
  "name": "common",
  "version": "1.0.0",
  "dependencies": {
    "cross-env": "5.0.5",
  },
  "license": "MIT"
}
```

この`common`を独立したパッケージとみなして、Vueプロジェクトで読み込めば良さそうだ。

### Vue.jsプロジェクトをつくる

`/packages`に移動して、Vue-CLIで`/packages/web`に新しいVueプロジェクトを作る。

```
$ vue create web
```

生成された`/packages/web/package.json`の`dependencies`に以下を追加する。

```json: /packages/web/package.json
    "cross-env": "5.0.5",
    "common": "1.0.0"
```

さらにrootフォルダに移動して、`yarn install`すると、rootに`node_modules`フォルダができる。

#### Webアプリでコードシェアできるかテスト

最終的にこの形で使うことはないだろうけれど、まずはちゃんと`common`ディレクトリから何かを読み出せるということをテストしたい。全然わからんが、[YouTubeビデオ](https://youtu.be/G8KXFWftCg0?t=260)などを参考に、`/packages/common/`に`src/`というフォルダを作り、そこに`main.js`をつくる：

```js: /packages/common/src/main.js
module.exports = () => {
  console.log('hello')
}
```

さらにこの`src/main.js`を最初に呼ばれるスクリプトファイルに指定するための行を追加しよう：

```json: /packages/common/package.json
{
  "name": "common",
  "version": "1.0.0",
  "main": "src/main.js",
  "dependencies": {
    "cross-env": "5.0.5"
  },
  "license": "MIT"
}
```

`/packages/web/src/main.js`で呼び出す：

```js: /packages/web/src/main.js
import Vue from "vue";
import App from "./App.vue";
import router from "./router";
import store from "./store";

Vue.config.productionTip = false;

// testing importing and running common function
const CommonFunction = require("common");
CommonFunction();

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount("#app");

```

`npm run serve`すると立ち上がりそう・・・になるのだが、ESLintが文句を言う：

```zsh
% npm run serve

> web@0.1.0 serve /Users/mm/Documents/new-code-sharing-ns7/packages/web
> vue-cli-service serve

 INFO  Starting development server...
98% after emitting CopyPlugin

 ERROR  Failed to compile with 1 errors                                       14:49:25

Module build failed (from /Users/mm/Documents/new-code-sharing-ns7/node_modules/eslint-loader/index.js):
Error: No ESLint configuration found in /Users/mm/Documents/new-code-sharing-ns7/packages/common/src.
    at CascadingConfigArrayFactory._finalizeConfigArray (/Users/mm/Documents/new-code-sharing-ns7/node_modules/eslint/lib/cli-engine/cascading-config-array-factory.js:432:19)
    at CascadingConfigArrayFactory.getConfigArrayForFile (/Users/mm/Documents/new-code-sharing-ns7/node_modules/eslint/lib/cli-engine/cascading-config-array-factory.js:271:21)
    at CLIEngine.isPathIgnored (/Users/mm/Documents/new-code-sharing-ns7/node_modules/eslint/lib/cli-engine/cli-engine.js:951:18)
    at CLIEngine.executeOnText (/Users/mm/Documents/new-code-sharing-ns7/node_modules/eslint/lib/cli-engine/cli-engine.js:868:38)
    at lint (/Users/mm/Documents/new-code-sharing-ns7/node_modules/eslint-loader/index.js:278:17)
    at transform (/Users/mm/Documents/new-code-sharing-ns7/node_modules/eslint-loader/index.js:252:18)
    at /Users/mm/Documents/new-code-sharing-ns7/node_modules/loader-fs-cache/index.js:127:18
    at ReadFileContext.callback (/Users/mm/Documents/new-code-sharing-ns7/node_modules/loader-fs-cache/index.js:31:14)
    at FSReqCallback.readFileAfterOpen [as oncomplete] (fs.js:257:13)

You may use special comments to disable some warnings.
Use // eslint-disable-next-line to ignore the next line.
Use /* eslint-disable */ to ignore all warnings in a file.
```

この[Vue CLIのIssue](https://github.com/vuejs/vue-cli/issues/2948)を参考に、`vue.config.js`を`/packages/web`直下に作成し次のようにする：

```js: /packages/web/vue.config.js
module.exports = {
  chainWebpack: config => config.resolve.symlinks(false)
};
```
すると`npm run serve`するとちゃんとconsoleに`hello`と出る。
![](https://storage.googleapis.com/zenn-user-upload/ltzkxwb4ilhsk3uwmdbcoks2ndsb)

### NativeScript-Vueプロジェクトをつくる

次に`/packages`フォルダに移動して`native`という名前のNativeScript-Vueプロジェクトをつくろう。

```
$ npm install -g @vue/cli @vue/cli-init
$ vue init nativescript-vue/vue-cli-template native
```

本来ならここで`cd native`として`npm i`するのだが、その前に`/packages/native/package.json`に、Webと同様次のように`cross-env`と`common`を書き加える。

```json: /packages/native/package.json
{
  "name": "native",
  "main": "main.js",
  "version": "1.0.0",
  "description": "A native application built with NativeScript-Vue",
  "author": "xerroxcopy <xerroxcopy@gmail.com>",
  "license": "MIT",
  "dependencies": { 
    "cross-env": "5.0.5",
    "common": "1.0.0",
    "vuex": "^3.5.1",
    "@vue/devtools": "^5.3.3",
    "nativescript-socketio": "^3.3.1",
    "nativescript-vue-devtools": "^1.4.0",
    "nativescript-toasty": "^3.0.0-alpha.2",
    "@nativescript/theme": "^2.3.3",
    "nativescript-vue": "^2.8.1",
    "@nativescript/core": "^7.0.3"
  },
  "devDependencies": {
    "@babel/core": "^7.11.6",
    "@babel/preset-env": "^7.11.5",
    "babel-loader": "^8.1.0",
    "@nativescript/webpack": "^3.0.4",
    "nativescript-vue-template-compiler": "^2.8.1",
    "nativescript-worker-loader": "~0.12.1",
    "sass": "^1.26.10",
    "vue-loader": "^15.9.3"
  }
}

```

`ns run ios`すると次のようなエラーが出る：

```zsh
% ns run ios
Error while loading nativescript-cloud is: Default commands should be required before child commands
Searching for devices...
npm ERR! code ETARGET
npm ERR! notarget No matching version found for common@1.0.0.
npm ERR! notarget In most cases you or one of your dependencies are requesting
npm ERR! notarget a package version that doesn't exist.
npm ERR! notarget 
npm ERR! notarget It was specified as a dependency of 'native'
npm ERR! notarget 

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/mm/.npm/_logs/2020-10-16T06_12_06_780Z-debug.log
Unable to install dependencies. Make sure your package.json is valid and all dependencies are correct. Error is: Command npm failed with exit code 1
```
最初の`Error while loading nativescript-cloud is: Default commands should be required before child commands`は2020-10-16現在あらゆる設定で出てくるエラーなのでおそらく気にしないでよい。問題はsymlinkされているはずの`common@1.0.0`がないよと言われることだ。ここで試しに`/packages/web/package.json`の`"common": "1.0.0"`を消してみるとちゃんと立ち上がる：

```zsh
% ns run ios
Error while loading nativescript-cloud is: Default commands should be required before child commands
Searching for devices...

> fsevents@1.2.13 install /Users/mm/Documents/new-code-sharing-ns7/packages/native/node_modules/watchpack-chokidar2/node_modules/fsevents
> node install.js

  SOLINK_MODULE(target) Release/.node
  CXX(target) Release/obj.target/fse/fsevents.o
  SOLINK_MODULE(target) Release/fse.node

> core-js@3.6.5 postinstall /Users/mm/Documents/new-code-sharing-ns7/packages/native/node_modules/core-js
> node -e "try{require('./postinstall')}catch(e){}"

Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!

The project needs your help! Please consider supporting of core-js on Open Collective or Patreon: 
> https://opencollective.com/core-js 
> https://www.patreon.com/zloirock 

Also, the author of core-js ( https://github.com/zloirock ) is looking for a good job -)


> ejs@2.7.4 postinstall /Users/mm/Documents/new-code-sharing-ns7/packages/native/node_modules/ejs
> node ./postinstall.js

Thank you for installing EJS: built with the Jake JavaScript build tool (https://jakejs.com/)


> electron@7.3.3 postinstall /Users/mm/Documents/new-code-sharing-ns7/packages/native/node_modules/electron
> node install.js


> @nativescript/core@7.0.11 postinstall /Users/mm/Documents/new-code-sharing-ns7/packages/native/node_modules/@nativescript/core
> node cli-hooks/postinstall.js


> @nativescript/webpack@3.0.7 postinstall /Users/mm/Documents/new-code-sharing-ns7/packages/native/node_modules/@nativescript/webpack
> node postinstall.js


> nativescript-vue@2.8.1 postinstall /Users/mm/Documents/new-code-sharing-ns7/packages/native/node_modules/nativescript-vue
> node postinstall.js

Hook already installed: nativescript-vue at location: /Users/mm/Documents/new-code-sharing-ns7/packages/native/hooks/before-checkForChanges
Hook already installed: nativescript-vue at location: /Users/mm/Documents/new-code-sharing-ns7/packages/native/hooks/before-watch
added 995 packages from 486 contributors and audited 996 packages in 87.56s

48 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

Preparing project...
Bundling application for entryPath ./main...
File change detected. Starting incremental webpack compilation...

webpack is watching the files…

[BABEL] Note: The code generator has deoptimised the styling of /Users/mm/Documents/new-code-sharing-ns7/packages/native/node_modules/@vue/devtools/build/backend.js as it exceeds the max of 500KB.
Hash: 14a54a14d283b94c41b8
Version: webpack 4.44.2
Time: 14121ms
Built at: 10/16/2020 15:15:43
                                              Asset       Size                                            Chunks             Chunk Names
                 assets/images/NativeScript-Vue.png   8.22 KiB                                                    [emitted]  
                                          bundle.js    356 KiB                                            bundle  [emitted]  bundle
                                       package.json  998 bytes                                                    [emitted]  
                                         runtime.js   77.1 KiB                                           runtime  [emitted]  runtime
tns_modules/@nativescript/core/inspector_modules.js  797 bytes  tns_modules/@nativescript/core/inspector_modules  [emitted]  tns_modules/@nativescript/core/inspector_modules
                                          vendor.js   10.4 MiB                                            vendor  [emitted]  vendor
Entrypoint bundle = runtime.js vendor.js bundle.js
Entrypoint tns_modules/@nativescript/core/inspector_modules = runtime.js vendor.js tns_modules/@nativescript/core/inspector_modules.js
[./ sync ^\.\/app\.(css|scss|less|sass)$] . sync nonrecursive ^\.\/app\.(css|scss|less|sass)$ 175 bytes {bundle} [built]
[./ sync recursive (?<!\bApp_Resources\b.*)(?<!\.\/\btests\b\/.*?)\.(xml|css|js|(?<!\.d\.)ts|(?<!\b_[\w-]*\.)scss)$] . sync (?<!\bApp_Resources\b.*)(?<!\.\/\btests\b\/.*?)\.(xml|css|js|(?<!\.d\.)ts|(?<!\b_[\w-]*\.)scss)$ 204 bytes {bundle} [built]
[./app.scss] 119 KiB {bundle} [optional] [built]
[./main.js] 1.7 KiB {bundle} [built]
[./store.js] 142 bytes {bundle} [built]
[~/package.json] external "~/package.json" 42 bytes {bundle} {tns_modules/@nativescript/core/inspector_modules} [optional] [built]
    + 351 hidden modules
Webpack compilation complete. Watching for file changes.
Webpack build done!
Installing pods...
Analyzing dependencies
Downloading dependencies
Generating Pods project
Integrating client project
Pod installation complete! There are 3 dependencies from the Podfile and 5 total pods installed.
Updating runtime package.json with configuration values...
Project successfully prepared (ios)
Building project...
Xcode build...
/* com.apple.actool.document.warnings */
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/native/Resources/Assets.xcassets:./LaunchImage.launchimage: warning: Launch images are deprecated in iOS 13.0. Use a launch storyboard or XIB instead.
/* com.apple.actool.document.notices */
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/native/Resources/Assets.xcassets:./AppIcon.appiconset/[][ipad][76x76][][][1x][][]: notice: 76x76@1x app icons only apply to iPad apps targeting releases of iOS prior to 10.0.
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/native/Resources/Assets.xcassets:./LaunchImage.launchimage/[iphone][retina4][2x][portrait][full-screen][]: notice: This launch image only applies to iOS 6.x and prior but the minimum deployment is 7.0 or later.
/* com.apple.actool.compilation-results */
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon20x20@2x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon20x20@2x~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon20x20@3x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon20x20~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon29x29.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon29x29@2x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon29x29@2x~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon29x29@3x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon29x29~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon40x40@2x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon40x40@2x~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon40x40@3x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon40x40~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon60x60@2x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon60x60@3x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon76x76@2x~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon76x76~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/AppIcon83.5x83.5@2x~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/Assets.car
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-1100-Landscape-2436h@3x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-1100-Portrait-2436h@3x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-1200-Landscape-1792h@2x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-1200-Landscape-2688h@3x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-1200-Portrait-1792h@2x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-1200-Portrait-2688h@3x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-568h@2x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-700-568h@2x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-700-Landscape@2x~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-700-Landscape~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-700-Portrait@2x~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-700-Portrait~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-700@2x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-800-667h@2x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-800-Landscape-736h@3x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-800-Portrait-736h@3x.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-Landscape@2x~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-Landscape~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-Portrait@2x~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage-Portrait~ipad.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage.png
/Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app/LaunchImage@2x.png
/Users/mm/Library/Developer/Xcode/DerivedData/native-herliyjplpawzqfrfhjeobnqdyws/Build/Intermediates.noindex/native.build/Debug-iphonesimulator/native.build/assetcatalog_generated_info.plist

warning: 'UILaunchImages' has been deprecated, use launch storyboards instead. (in target 'native' from project 'native')
note: Using new build system
note: Building targets in parallel
note: Using codesigning identity override: 
note: Planning build
note: Constructing build description
warning: The iOS Simulator deployment target 'IPHONEOS_DEPLOYMENT_TARGET' is set to 8.0, but the range of supported deployment target versions is 9.0 to 14.0.99. (in target 'Toast-Swift' from project 'Pods')
warning: The iOS Simulator deployment target 'IPHONEOS_DEPLOYMENT_TARGET' is set to 8.0, but the range of supported deployment target versions is 9.0 to 14.0.99. (in target 'Starscream' from project 'Pods')
warning: The iOS Simulator deployment target 'IPHONEOS_DEPLOYMENT_TARGET' is set to 8.0, but the range of supported deployment target versions is 9.0 to 14.0.99. (in target 'MDFInternationalization' from project 'Pods')
warning: The iOS Simulator deployment target 'IPHONEOS_DEPLOYMENT_TARGET' is set to 8.0, but the range of supported deployment target versions is 9.0 to 14.0.99. (in target 'Socket.IO-Client-Swift' from project 'Pods')
Project successfully built.
The build result is located at: /Users/mm/Documents/new-code-sharing-ns7/packages/native/platforms/ios/build/Debug-iphonesimulator/native.app
Installing on device 3C8DDF23-0C35-4BF0-82DA-BFFA667227AA...
Successfully installed on device with identifier '3C8DDF23-0C35-4BF0-82DA-BFFA667227AA'.
Successfully transferred all files on device 3C8DDF23-0C35-4BF0-82DA-BFFA667227AA.
Restarting application on device 3C8DDF23-0C35-4BF0-82DA-BFFA667227AA...
(RunningBoardServices) [com.apple.runningboard:connection] Identity resolved as application<org.nativescript.application>
CONSOLE INFO: HMR: Hot Module Replacement Enabled. Waiting for signal.
NativeScript debugger has opened inspector socket on port 18183 for org.nativescript.application.
CONSOLE LOG: {NSVue (Vue: 2.6.12 | NSVue: 2.8.1)} -> CreateElement(Frame)
CONSOLE LOG: {NSVue (Vue: 2.6.12 | NSVue: 2.8.1)} -> CreateElement(Page)
CONSOLE LOG: {NSVue (Vue: 2.6.12 | NSVue: 2.8.1)} -> CreateElement(ActionBar)
CONSOLE LOG: {NSVue (Vue: 2.6.12 | NSVue: 2.8.1)} -> AppendChild(Page(3), ActionBar(4))
CONSOLE LOG: {NSVue (Vue: 2.6.12 | NSVue: 2.8.1)} -> CreateElement(gridlayout)
CONSOLE LOG: {NSVue (Vue: 2.6.12 | NSVue: 2.8.1)} -> CreateElement(label)
CONSOLE LOG: {NSVue (Vue: 2.6.12 | NSVue: 2.8.1)} -> AppendChild(GridLayout(5), Label(6))
CONSOLE LOG: {NSVue (Vue: 2.6.12 | NSVue: 2.8.1)} -> AppendChild(Page(3), GridLayout(5))
CONSOLE LOG: {NSVue (Vue: 2.6.12 | NSVue: 2.8.1)} -> AppendChild(Frame(2), Page(3))
Successfully synced application org.nativescript.application on device 3C8DDF23-0C35-4BF0-82DA-BFFA667227AA.
```

この問題はSlackで聞いてみたが返信がつかなかった。stack overflowでも[聞いてみた](https://stackoverflow.com/questions/64383376/nativescript-vue-build-fails-when-using-yarn-workspaces)が・・・ダメッ・・・！

解決法が見つかり次第更新しますが失敗した記録として残しておく。rest in peace