---
title: AWS, GCP, Azureの障害情報の提供方法とTwitterボット
author: doublemarket
layout: post
categories:
  - Cloud
---

[Twitterボットを作るのが趣味という記事](http://b.l0g.jp/misc/my-twitter-bots/)を以前書いたが、そのからみでAWS(Amazon Web Service)、GCP(Google Cloud Platform)、Microsoft Azureの主要3クラウドの障害情報Twitterボットを作った。面白いもので3サービスとも障害情報の提供方法やポリシーが違っていて、ボットのような障害情報を取ってきて通知する仕組みをつくるのはちょっと面倒。せっかくなのでボットの宣伝も兼ねて障害報告に関するサービス間の違いを挙げておく。自分で通知の仕組みを作ったり、サービスを選ぶときの参考に。

ちなみに、これらのサービスを既に使っている人は、単純にこれらのボットをフォローするか、Slack([英語の公式ドキュメント](https://get.slack.help/hc/en-us/articles/205346227-Add-Twitter-to-Slack)、[日本語記事](http://qiita.com/YuukiOgino/items/54f427544fe2fa304a80))やHipchat([Zapierを使った方法の日本語記事](http://www.e-agency.co.jp/column/zapier_twittertohipchat.html))などにボットのツイートを通知するようにしておくと、障害検知の一環としても使えますのでぜひどうぞ。どのボットも障害発生時間などを日本時間に置き換えてツイートする仕組みです。

# AWS

### Twitterボット
- 全リージョン対象 [@awsstatusjp_all](https://twitter.com/awsstatusjp_all)
  - 下のボットのツイートも包含されるので片方フォローすればOK
- 東京リージョン関連のみ [@awsstatusjp](https://twitter.com/awsstatusjp)
  - 東京リージョンのサービスと、S3やManagement consoleなどリージョンなしのサービスの障害が対象

### 障害情報サイト
- [http://status.aws.amazon.com/](http://status.aws.amazon.com/)
- 大まかな地域ごとにタブが並んでおり、その中に地域内の全リージョン、全サービスが一列に並ぶ
- 障害があると左端の緑の丸がオレンジになり、障害の詳細がDetails列に簡潔に書かれる
- 他のサービスと比べると障害内容の記述はシンプルであまり詳細は書かれない
- 過去の障害履歴は下半分に1週間分(それ以前にもさかのぼれる)表示されるが、サービス数が多すぎてやや見にくい
- 比較的定型的な表現が多い
- タイムゾーンはPST/PDT

### 機械的に障害情報を取る方法
- 全リージョンの全サービスの情報がそれぞれ1つずつRSSフィードで提供される
- その他に[全障害情報が含まれるRSSフィード](http://status.aws.amazon.com/rss/all.rss)もある
- 基本的には上記サイトに表示される内容がフィードにも書かれる

### 所感
- いいところ
  - リージョン及びサービスごとにフィードがあるので特定サービスごとに監視したい時は楽
  - 定型的な表現が多いので訳したり表現を拾って処理を分岐したりしやすい
- 悪いところ
  - 過去の障害情報が見にくいので他サービスのように時系列の一覧などが欲しい
  - フィードの数が多すぎる(all.rssをうまく使えばいいが)
  - 他サービスと比べて障害情報の詳細さが足りない

# GCP

### Twitterボット
- 全リージョン対象 [@gcpstatusjp](https://twitter.com/gcpstatusjp)
  - 日本リージョンはまだない([開設予定](http://googlecloudplatform-japan.blogspot.jp/2016/03/geo-tokyo-region.html))
  - 現状、障害がリージョンごとにうまく分けて通知されないので日本リージョンができても別ボットにしづらそう

### 障害情報サイト
- [https://status.cloud.google.com/](https://status.cloud.google.com/)
- 地域ごとやリージョンごとの区分けはなく、横軸が日時、縦軸がサービスのグリッドのみ
- 障害があるとグリッド状に赤(致命的障害)やオレンジ(一部障害)の丸が表示され、障害の詳細へのリンクが貼られる
- 障害内容の記述は詳細で、次の報告をいつまでにするかが必ず併記される
  - ただし次の報告時刻は守られない時もある様子
- 過去の障害は[時系列に並んで表示](https://status.cloud.google.com/summary)される
- タイムゾーンはPST/PDT

### 機械的に障害情報を取る方法
- サイトに掲載されている詳細情報や障害のレベル(Severity)、開始・収束時刻なども載ったJSONと、少し情報が少ないRSSの2つ

### 所感
- いいところ
  - フィードの他に詳しい情報を含む[JSONフィード](https://status.cloud.google.com/incidents.json)が提供されている。しかもちゃんと[スキーマ情報](https://status.cloud.google.com/incidents.schema.json)も別にある
  - Severityと障害の開始・収束時間があるのが非常にいい
- 悪いところ
  - リージョンが障害情報の詳細にしか書かれないので、特定リージョンの障害のみを検出したりするのが困難

# Azure

### Twitterボット
- 全リージョン対象 [@azurestatusjp_a](https://twitter.com/azurestatusjp_a)
  - アカウント名の文字数制限の影響で末尾がallじゃなくて悲しかった
  - 下のボットのツイートも包含されるので片方フォローすればOK
- 日本リージョン関連のみ [@azurestatusjp](https://twitter.com/azurestatusjp)
  - 日本のリージョンとグローバルサービスのみ

### 障害情報サイト
- [https://azure.microsoft.com/ja-jp/status/](https://azure.microsoft.com/ja-jp/status/) ([https://status.azure.com/](https://status.azure.com/)でもアクセス可)
- 大まかな地域ごとにタブが並んでおり、その中に横軸リージョン、縦軸サービスのグリッドがある
- 障害があるとグリッドに赤丸や黄色三角が表示され、障害の詳細がページ上部に書かれる
- 障害内容の記述はGCPに近い詳細さで、今後の対応予定なども書かれる
- 過去の障害は[時系列に並んで表示](https://azure.microsoft.com/ja-jp/status/history/)される
- URLからすると障害内容は日本語で表示されそうに見えるが、リージョン名などのみ日本語であとは英語のまま
- タイムゾーンはUTC

### 機械的に障害情報を取る方法
- 全リージョンの全サービスの情報が含まれるRSSフィードが1つのみ
- 障害内容の記述はサイトに表示されるものと同じだが、Severityなどの情報は含まれない

### 所感
- いいところ
  - かなり詳しい障害状況が報告される
- 悪いところ
  - RSSフィードのスキーマが微妙(リージョンとサービスが同じCategoryに列挙されている)
  - 障害がない時はRSSフィードの内容が空になる
    - どんな情報がどのように配信されるか分からない
    - 障害が終わった時の情報がフィードから得られない
    - 他サービスだと、障害収束後に「こんなのありました」という報告パターンがあるがそういうのをどう出すのか謎
  - サイト上には表示されるがRSSフィードに載らない情報が多い

# 全体的に
- 利用者から見てもっとも丁寧な感じなのはAzureだが、フィードがいけてないので情報の加工は一番やりにくい
- AWSは情報の詳細さが他サービスと比べて全然足りない
- 障害情報を加工して通知などに使いたい時には圧倒的にGCP。Severityや障害開始・収束時間など詳しい情報をパースしやすい形式で提供しているのはGCPのみ
