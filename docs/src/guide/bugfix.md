# バグFIX

そのままだとうまく動かないところがあるので直す。

※もしかすると今は直っているかもしれないし、直し方も自己流なのでご了承を。

## 検索窓にMac限定のバグがある

### 事象

検索窓に日本語を入力して変換を完了すると勝手に「検索にヒットした1件目の記事」に飛んでしまう。

### 解決方法

パッチを作ってデプロイ時に自動に当てるような仕組みを導入する。

#### パッチの作り方

[https://github.com/ds300/patch-package](https://github.com/ds300/patch-package) に従う。

`package.json` に下を追記
```
 "scripts": {
   "docs:dev": "vuepress dev docs",
   "docs:build": "vuepress build docs",
   + "postinstall": "patch-package"
 },
```

インストール
```
yarn add patch-package postinstall-postinstall
```

バグが発生するファイル (`node_modules/@vuepress/plugin-search/SearchBox.vue`) を直接編集
```
diff --git a/node_modules/@vuepress/plugin-search/SearchBox.vue b/node_modules/@vuepress/plugin-search/SearchBox.vue
index b47f18e..f9baa5d 100644
--- a/node_modules/@vuepress/plugin-search/SearchBox.vue
+++ b/node_modules/@vuepress/plugin-search/SearchBox.vue
@@ -11,9 +11,10 @@
       @input="query = $event.target.value"
       @focus="focused = true"
       @blur="focused = false"
-      @keyup.enter="go(focusIndex)"
+      @keyup.enter="goEnter(focusIndex)"
       @keyup.up="onUp"
       @keyup.down="onDown"
+      @compositionstart="composing = true"
     >
     <ul
       v-if="showSuggestions"
@@ -56,7 +57,8 @@ export default {
       query: '',
       focused: false,
       focusIndex: 0,
-      placeholder: undefined
+      placeholder: undefined,
+      composing: false
     }
   },
 
@@ -177,6 +179,15 @@ export default {
       }
     },
 
+    goEnter(i) {
+      var ua = window.navigator.userAgent.toLowerCase();
+      if (ua.indexOf("mac os x") !== -1 && this.composing) {
+        this.composing = false
+        return
+      }
+      this.go(i)
+    },
+
     go (i) {
       if (!this.showSuggestions) {
         return

```

パッチファイルの作成

```
yarn patch-package @vuepress/plugin-search
```

これで完了。buildする際にパッチを当ててくれる。

## VuePress Plugin SEOでエラーになるブラウザがある

### 事象

[https://vuepress.vuejs.org/plugin/official/plugin-google-analytics.html#install](https://vuepress.vuejs.org/plugin/official/plugin-google-analytics.html#install)を導入するとSafariとMobileなどでJavaScriptエラーになる。

### 解決策

直接Google Analyticsのタグをheadに埋め込むこともできるのでこちらで対応する

`docs/.vuepress/config.js`
```
  head: [
    ['script', {
      async: true,
      src: "https://www.googletagmanager.com/gtag/js?id=xxxxx"
    }],
    ['script', {}, `
      window.dataLayer = window.dataLayer || [];
      function gtag(){dataLayer.push(arguments);}
      gtag('js', new Date());
      
      gtag('config', 'xxxxx');
    `],
  ],
```
