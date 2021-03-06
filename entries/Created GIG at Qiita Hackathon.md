Qiita Hackathonという2日間のイベントに参加し、[GIG](http://gig.herokuapp.com) というWebサービスを作って公開した。
他の参加者の方がつくったプロダクトは下記の記事で詳しく紹介されている。
http://blog.qiita.com/post/42345394076/qiita-2-day-hackathon-report


## 発表スライド
<script async class="speakerdeck-embed" data-id="428387e0500f013099e61231381d5324" data-ratio="1.33333333333333" src="//speakerdeck.com/assets/embed.js"></script>


## どうしてGIGをつくったか
今回のQiita Hackathonのテーマは、GitHub APIを使った開発+プログラマの問題を解決するサービス、とのことだった。随分前からブログに対して色々思う所がありながら結局吐き出す機会を掴めず悶々としていたということもあり、Webアプリ界においてブログを作るというのは言わばHello world、CG界における[ユタ・ポット](http://ja.wikipedia.org/wiki/Utah_teapot)みたいなものだと思ったので、ブログをつくることにした。テーマ的に少年少女の為のブログをつくろうという気は更々無くて、こういう要望を満たすものを作ろうとした。

* Web上から編集できる
* コマンドラインから編集できる
* バージョン管理される
* Gitのことを考えなくても使える
* データを預かるのは恐いので別の場所に置く
* 飽きたら記事データだけ取り出してすぐ潰せる


## ホスティング
途中までホスティング形式にしてサービス化する予定は全くなかったものの、Hackathonだし完成時には当然皆がすぐ試してみられる形にしないと駄目だろう、ということで急遽ホスティング出来るように実装を変えた。Hackathonが26時間しか無かった上に寝坊して12時間ほど寝ていたので、この変更には勇気が必要だった。[迷ったらワイルドな方を選べ](https://www.google.co.jp/search?q=%22%E8%BF%B7%E3%81%A3%E3%81%9F%E3%82%89%E3%83%AF%E3%82%A4%E3%83%AB%E3%83%89%E3%81%AA%E6%96%B9%E3%82%92%E9%81%B8%E3%81%B9%22)とのこと。


## 時間配分
* 13:30 テーマ聞く。ブログつくりはじめる
* 14:00 認証部分完成する
* 15:00 GitHub上のエントリがブログに表示される
* 16:00 黙々と作業
* 17:00 APIからcommit出来るようになる
* 18:00 twitter-bootstrap入れてCSS書く
* 19:00 エントリ削除できるようになる
* 20:00 エントリキャッシュ完成する
* 21:00 Post-Receive Hookでキャッシュ消せるようになる
* 22:00 琴浦さん見たいので帰宅
* ----
* 12:00 寝坊して起床
* 13:00 会場着
* 14:00 ホスティング形式に変更する実装終える
* 15:00 デザイン整える
* 15:30 スライド作り始める
* 16:00 終了/発表


## 難しかったところ
GitHubのAPIを通してgit-commitする、というのが意外と難しい。先ず以てこの付近のAPIの利用にはGitの内部構造の知識が前提になるのだけど、[Git Internals](http://git-scm.com/book/en/Git-Internals)を読んでもまだ少し足りない程度の知識が必要になる。ファイルを新規作成するのに比べ、ファイルを削除するcommitをつくる、というのはもっと難しい。ファイルを削除するというのはつまり、既存のtree構造から部分的にblobを抜き出し再構築したtreeをつくるということで、地獄である。例えば、entries/entry.mdというファイルを作ってGitHubに置く場合、下記のように5回APIを叩くことになる。

1. entry.mdのためのblobをつくる
2. masterブランチの指すcommitを取得する(以下、最新commitと呼ぶ)
3. entriesのために、1のblobを含み最新commitの指すtreeを継承したtreeをつくる
4. 最新commitを前commitに持つ、3で作ったtreeを含むcommitをつくる
5. masterの参照先を4で作ったcommitに切り替える


## 今後
自分用のブログとして使いながら、地道に改良を加えていきたい。例えばプレビュー機能が無かったり、キャッシュ機構が上手く動かない場合があったりするので、その辺の改良とか、CSSとJavaScriptを独自にカスタムできる程度の能力は付けたい。後は、せっかくホスティング形式にしているので、他の人のブログへの導線が欲しい。ソースコード自体GitHubで公開しているので、Pull Request大歓迎です。
https://github.com/r7kamura/gig
