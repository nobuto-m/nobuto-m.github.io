---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "n8nでRSSの新着アイテムをwebhookに投げる"
slug: pass-rss-new-item-to-webhook-with-n8n
subtitle: ""
summary: ""
authors: []

aliases:
- 1688885165

tags: []
categories: []
keywords: ["n8n"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-07-09T15:46:05+09:00
lastmod: 2023-07-09T15:46:05+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

昔は[SleipnirのRSSリーダー](https://internet.watch.impress.co.jp/cda/news/2006/03/27/11384.html)とかGoogle Readerとかを使っていて、Feedlyもちょっとは使っていた。けれどもいつのまにかTwitterで話題になったやつとか、[Google Discover](https://developers.google.com/search/docs/appearance/google-discover)で出てきたニュースをちょろっと読むだけになってしまった。もうちょっと能動的にキーワードで引っ掛けたりして自分にとって重要そうなニュースを早めに読みたいという気持ちはあり、[n8n](https://n8n.io/)のself-hostingコミュニティ版を使ってチャットに流すことにした。

{{< figure src="featured.png" caption="ピタゴラスイッチっぽさ" >}}

最初はすんなりいくかとおもって、特にメモを書くこともないなぁと思ってたけど、しっかりあったので書いておく。

まず、[RSSフィードを扱ってくれるRSS Readノード](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.rssfeedread/)は、RSSのURLを1個しか受け取ってくれない。なので複数サイトのフィードを一気に扱いたいときは、さっそく他のノードとの連携が必要になってくる。幸い[このトピックそのものの公式ドキュメントがあるので](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.splitinbatches/#example-usage-read-rss-feed-from-two-different-sources)、`Split In Batches`ノードでループを作ればいいことがわかるのだが、このループを作成するときにフィードのリストを渡すための「リストを定義する」みたいなノードが存在しない。

仕方がないので、先ほどの公式ドキュメントにあるように、別途任意のJavascriptコードを書ける[Codeノード](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.code/)を用意して、下記のようにして次に実行されるノードにリストが渡るようにする。

```javascript
return [
  {json: {url: 'https://news1.example.com/feed'}},
  {json: {url: 'https://news2.example.com/feed'}},
  {json: {url: 'https://news3.example.com/feed'}},
  {json: {url: 'https://news4.example.com/feed'}},
  {json: {url: 'https://news5.example.com/feed'}},
];
```

ここまでで、ワークフローが実行されたときに全部のサイトを巡回して、RSS内のアイテムを取ってきてくれるのだが、ここから「前回実行時からの新着アイテム」に絞り込むことがRSSノードではできない。このままWebhookへのPOSTにつなげてしまうと、ワークフローが実行されるたびに古い記事も含めて全部のアイテムがチャットに投げられてしまうので悲劇。そこでまずはワークフローの実行間隔を、cronに相当する[Schedule trigger](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.scheduletrigger/)ノードで設定する。ここでは例として157分間隔に設定。

{{< figure src="schedule_trigger.png" caption="実行間隔の設定" >}}

続いて、`Split In Batches`ノードの後ろに[Filterノード](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.filter/)をつなげて、次のようなフィルターを設定する。

{{< figure src="filter_by_date.png" caption="現在時刻から157分前を指定" >}}

ここで、RSS内のアイテムに紐付いている日時`{{$json.isoDate}}`と、現在時刻から157分前の日時`{{$now.minus({minutes:157}).toISO()}}`を比べて、記事が前回の実行時より新しいかどうかを無理やり判定する。

これ以外にいい方法がないか調べたけども、公式のノードだけではこれが限界っぽい。157分をハードコードすると、後々間隔を変更したときにこの部分を変更し忘れそうなので変数を使って  
`$('Schedule Trigger').params["rule"]["interval"][0]["minutesInterval"]`  
に変更しておくとよさそう。後は`$now`を使うとワークフロー全体が開始された日時ではなくて、[コード自体が呼び出された日時が入りそうなので](https://docs.n8n.io/code-examples/expressions/luxon/#variables)、フィードの取得に時間がかかったときなどは空白の時間が生まれて新着判定が上手くいかなそうなので、ここも変数を使ってワークフローが開始された日時そのものを使うために  
`$('Schedule Trigger').item.json.timestamp`  
としておく。

最終的には新着判定のために使う基準日時は
```javascript
{{
  DateTime.fromISO($("Schedule Trigger").item.json.timestamp)
    .minus({
      minutes:
        $("Schedule Trigger").params["rule"]["interval"][0][
          "minutesInterval"
        ],
    })
    .toISO();
}}
```
となる、けど読みにくいですね。

このあたりは、何分間隔という指定ではなくて、毎時決まった時間に、とした方がすっきりすると思う。だけど、毎時決まった時間にフィードを取りに行くのはなんかしっくりこないんですよね。

ここまでで新着判定はできているはずなので、後はWebhookに向かって情報を投げるだけ。[HTTP Requestノード](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.httprequest/)を使って、サービスに合わせて整形した上で投げる。例えば[Mattermostの場合は](https://developers.mattermost.com/integrate/webhooks/incoming/)、`text`にリンクを入れてあげれば自動でプレビューされるので十分。

{{< figure src="webhook.png" caption="リンクやタイトルを整形して投げる" >}}

新着アイテムを全部投稿するのではなくて、特定のキーワードに合致するものだけに絞りたければ、POSTする前にもう一つFilterノードを追加してRegexが書ける。

{{< figure src="filter_keywords.png" caption="タイトルか概要がキーワードを含むものだけに絞れる" >}}

「RSSの新着アイテムをキーワードで絞ってからPOSTしておいて」っていう簡単ぽく聞こえる作業が、ここまでの長文になってしまった。
