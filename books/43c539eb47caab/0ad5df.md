---
title: "Firebaseプロジェクトを作成する"
free: false
---

Firebaseプロジェクトを作成した上で以下の対応を行ないます。

# Firestoreの初期化

Firebaseコンソールから[データベースの作成](https://console.firebase.google.com/u/0/project/_/firestore)を行います。

1. 本番環境モードを選択
2. **適切なロケーション**を選択

という手順で行います。この際、**一度設定したロケーションは変更できない**ので気をつけましょう。

ロケーション名|概要
---|---
asia-northeast1|東京
asia-northeast2|大阪

ロケーションはデータベースの場所を指すのでユーザーが最も多く存在するロケーションを選ぶべきです。西日本在住者向けのローカルサービスなら `asia-northeast2` を選びますが、基本的には `asia-northeast1` で良いでしょう。もちろん海外向けのサービスであれば[海外のロケーション](https://firebase.google.com/docs/firestore/locations?hl=ja#location-r)を選択してください。

# アプリの登録

[公式ドキュメント](https://firebase.google.com/docs/web/setup?hl=ja#register-app)に沿ってアプリの登録を行います。以下抜粋です。

> 1. Firebase コンソールの [[プロジェクトの概要](https://console.firebase.google.com/u/0/project/_/overview?hl=ja)] ページの中央にあるウェブアイコンをクリックして設定ワークフローを起動します。
> 2. すでに Firebase プロジェクトにアプリを追加している場合は、[アプリを追加] をクリックするとプラットフォームのオプションが表示されます。
> 3. アプリのニックネームを入力します。（このニックネームは内部用の簡易 ID であり、Firebase コンソールでのみ表示されます。）
> 4. [アプリを登録] をクリックします。

ニックネームはなんでも構いません。「このアプリの Firebase Hosting も設定します。」はチェック不要です。Firebase SDKの追加ステップは無視してコンソールに進みましょう。

# Blazeプランへのアップグレード

AlgoliaなどのSaaSとの連携はFirebase Cloud Functionsを使いますが、無料プランでは外部サービスとの連携ができません。Blazeプランにアップグレードしてください。Firebaseの無料枠はかなり多いので、よほどのトラフィックがない限り実際に課金が発生することはありません。

とはいえ開発中に誤って無限ループを発生させてしまいトラフィックが集中した結果課金が発生する可能性はあります。そういった事態を防ぐためにもアップグレード時に指示に従い予算アラートの設定を行い、事故にいち早く気づけるようにしましょう。

:::message
予算アラートは単なる通知であり、一定金額以上の課金を制限してくれる機能ではありません。課金を制限する機能は[現状存在しない](https://firebase.google.com/docs/firestore/quotas?hl=ja#set_spending_limits)のでアラートの見落としには十分気をつけましょう。
:::

## アップグレード方法

Firebase管理コンソール左下より「アップグレード」を選択してフローに従ってください。