# Firebaseにデプロイ

## VuePressのビルド

```
$ cd docs
$ yarn build
```

## デプロイ

あらかじめ画面でfirebaseのプロジェクトを作っておくこと

```
$ firebase login
  - 500エラーが出るなら firebase login --no-localhost で試す
  - 500エラーが出るならコンソールに出てくるURLを直接ブラウザに貼ってアクセス
  - 500エラーが出るなら何度かリロードしてみる

$ firebase init
  - Hostingを選択
  - Use an existing projectを選択
    - firebaseで作ったプロジェクトを指定
  - docs/src/.vuepress/distを指定
  - 以降は全部NO

$ firebase deploy
```

## カスタムドメインの追加 (Optional)

![test](./test.png)

