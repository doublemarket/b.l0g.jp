---
title: 『入門Kubernetes』を翻訳しました
author: doublemarket
layout: post
categories:
  - Books
---

コンテナを管理するオーケーストレータとしてデファクトスタンダードになりつつあるKubernetesに関する入門書『[入門Kubernetes](https://www.oreilly.co.jp/books/9784873118406/)』を翻訳しました。2018年3月22日に、オライリー・ジャパンから発売されます。原著は『[Kubernetes: Up and Running](http://shop.oreilly.com/product/0636920043874.do)』です。

<center><img border="1" src="/images/2018-03-12-kubernetes-up-and-running/kubernetes-up-and-running.jpg"></center>

- [目次](https://www.oreilly.co.jp/books/9784873118406/#toc)
- [Amazonの書籍詳細ページ](https://www.amazon.co.jp/dp/4873118409/)

Kubernetesの開発者たちが直接Kubernetesを解説した本で、Kubernetesでアプリケーションを動かすために必要な色々な概念を、実際にKubernetesクラスタ上で動かしてみながら学んでいくという構成になっています。

また、チュートリアルだけでなく「なぜコンテナを使うべきなのか」「どういう経緯でKubernetesが生まれたのか」「マイクロサービスの何がいいのか」といった、コンテナやマイクロサービスが広まって来た背景についても書かれています。そのため、コンテナはほとんど触ったことがないという人でも読み始められるようになっています。

入門書なのでKubernetesの機能のすべてが網羅されているわけではありませんが、Kubernetesを理解するための基本的な事項については一通り書かれています。Kubernetesの公式ドキュメントはとても丁寧に書かれているので(ただし英語)、まずこの『入門Kubernetes』を読んでKubernetesの仕組みと基本的なリソースを学び、さらにその先を知りたい場合には公式ドキュメントを読み込んでいくというのにぴったりだと思います。

## 日本語版独自の改善点

原著はKubernetes 1.5あるいは1.6をベースに書かれています。日本語版には、翻訳完了時の最新版であるKubernetes 1.9までに加えられた変更などを、注釈などの形でできるだけ反映しました。チュートリアルを簡単に進められるように、原著の内容に合わせて[サンプルデータのリポジトリ](https://github.com/kubernetes-up-and-running/examples)が用意されているのですが、これも日本語版に合わせて[Kubernetes 1.9対応データを含むリポジトリ](https://github.com/doublemarket/kuar-examples-1-9)を用意しました。

また、原著への「内容はいいのに誤植が多すぎる」といった指摘に対しては、翻訳の際にできる限り修正を反映させるようにしています。

## 翻訳の経緯と謝辞

英語を日本語に翻訳することで日本語の情報量を増やしたいという思いで、[人力翻訳コミュニティYakst](https://yakst.com/ja)というサイトを作って翻訳したり、[『SQLパフォーマンス詳解』というデータベースに関する本の翻訳](http://b.l0g.jp/uncategorized/sql-performance-explained-ja/)などをしてきました。その先に、いつかは書店で売られる本の翻訳がしたいと思っていたところ、機会をいただいて実現することになりました。

私自身Kubernetesに関してはまだまだ経験が浅いこともあり、Kubernetesの運用経験豊富なレビュアのみなさまからの指摘無しにこの本は完成しなかったといっても過言ではありません。レビューしていただいた[@mahataさん](https://twitter.com/mahata)、[@Ladicleさん](https://twitter.com/ladicle)、[@superbrothersさん](https://twitter.com/superbrothers)、[@strskさん](https://twitter.com/strsk)、[@deeeetさん](https://twitter.com/deeeet)(順不同)には本当に感謝するばかりです。

「私は『入門Kubernetes』でKubernetesを始めました」という人がたくさん現れて、日本のKubernetesコミュニティがさらに盛り上がっていくことにつながれば、翻訳者としてこの上ない喜びです。みなさんぜひ読んでみてください！
