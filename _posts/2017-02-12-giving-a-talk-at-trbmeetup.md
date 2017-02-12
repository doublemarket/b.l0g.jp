---
title: Tokyo Rubyist Meetupでデータベースのインデックスについて英語で話した
author: doublemarket
layout: post
categories:
  - Databases
---

The English version is at the end of this post.

[2月10(金)に開催されたTokyo Rubyist Meetup](https://trbmeetup.doorkeeper.jp/events/56308)というイベントで、自分が翻訳した[SQLパフォーマンス詳解](http://sql-performance-explained.jp/)という本をベースにして「How indexes work in relational database」という題で発表した。題名からわかるように、英語での発表。

<script async class="speakerdeck-embed" data-id="a54c372ef22749ff9af1d235dcaa7fe8" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

[Tokyo Rubyist Meetup](https://trbmeetup.doorkeeper.jp/)は、Doorkeeperの社長[Paul McMahon](https://www.doorkeeper.jp/team/paul)さんが主催している、Rubyのことを中心にしつつも色々なトピックを取り上げている英語話者向けの勉強会。Paulさんからお声がけがあって、DBエンジニアからの知見はRubyistにもうけると思うよということで、英語の勉強と本の宣伝とデータベース知識の整理の意味で発表することにした。Paulさん貴重な機会をくれて本当にありがとうございました！

内容的には、[SQLパフォーマンス詳解](http://sql-performance-explained.jp/)(の[英語版](http://sql-performance-explained.com/))の第1章の要約みたいな感じで、インデックスがなぜ必要なのか、Bツリーインデックスはどうやってデータを探すのに役立つのかといった基本的なところを書いた。Active Recordばっかり使ってて、SQLがどう動いているのかあまり意識していなかったり、高速に応答するクエリをどう書いたらいいのかわからない人に向けて、インデックスコワクナイヨ、ということを言ったつもり。しかし特定のDB(MySQLとかPostgreSQLとか)についてあえて触れなかったので、内容的に抽象的すぎたかもしれない。こういう発表って、どの程度の人をターゲットにするかブレないようにすること、そして抽象的すぎず突っ込みすぎずな内容にもっていくのが難しい。

今年は意識的に英語でアウトプットしていこうと思っているので、できる限り機会を見つけてこういう場で発表するつもり。

---

I gave a talk "How index work in relational databases" based on the book [SQL Performance Explanined](http://sql-performance-explained.jp/) at [Tokyo Rubyist Meetup held on Feb. 10](https://trbmeetup.doorkeeper.jp/events/56308).

(See the material above.)

[Tokyo Rubyist Meetup](https://trbmeetup.doorkeeper.jp/) is a tech meetup for English speaking Ruby engineers organized by the CEO of Doorkeeper Inc. [Paul McMahon](https://www.doorkeeper.jp/team/paul). One day, he gave me an email and suggested me to talk about a topic related to databases. I decided to talk something to practice my English, to promote the book, and to reorganize my thought on databases. Thank you very much Paul, this was my precious experience!

The talk was basically the summary of the chapter one of [SQL Performance Explanined](http://sql-performance-explained.com/), including basic topics like why indexes are needed and how B-tree indexes search a record. The talk was for developers who use Active Record and don't have enough knowledge about indexes and what I wanted to say for them is that don't be afraid of indexes. I intentionally avoid to write about a specific database management system (MySQL, PostgreSQL, etc.) to focus on how indexes work in general but because of that, the talk might be too abstract. Hopefully, the talk was a good introduction to dive into the internal of databases for attendees.

I'm going to talk at some more events in this year to improve my English skill.
