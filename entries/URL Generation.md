RailsのURL生成には、url_forまたはActionController::Resourcesで自動生成されたメソッド(*_path, *_url)を利用するか、モデルのインスタンスを渡して良い感じのURLに変換してもらったりする。
ActionController::Baseのurl_forとActionView::Helpers::UrlHelperで定義されたurl_forは少し挙動が違っていて、
Helperの方は:only_pathオプションがデフォルトでtrueになっている。
つまり、Helperの方はデフォルトでは相対パスを生成するようになっている。

## _path, _url
大体の場合はurl_forを明示的に使う機会は少なくて、例えばBlogモデルを扱うようなアプリケーションヌの場合、
blog_pathとか、blog_urlとかを使うことになる。
blog_pathは相対パス、blog_urlは絶対パスを返す。
Hakologは以前RSSのlink要素に間違えて_pathを使っていて、
RSSリーダーに相対パスが表示されていたことがあって、
_urlに書き直したら上手くいった。

## RESTful
明示的なパスの指定もレーッルズウェイに乗ってRESTfulに従っていればそこまで使う機会はなくて、
link_to等であればBlogモデルのインスタンスを渡せば、
適当にコンテキストで判断してurl_forを使った結果のURLを返してくれる。
form_forにBlog.newを渡せばPOST /blogsにリンクされるようになるとか、
既に保存済みのblogを渡せばPUT /blogs.:idにリンクされたりとかする。

## to_param
```link_to @blog.title, @blog```とかのリンク生成にはActiveRecord::Base#to_paramが呼ばれるはずなので、
独自のURLを生成したい場合はこれを上書きしてあげれば良い。
Hakologだと/:usernameというリンクを生成したいので、これを書いている。

```ruby
class Blog < ActiveRecord::Base
  def to_param
    username
  end
end
```

* [Ruby on Rails Guides: Rails Routing from the Outside In](http://guides.rubyonrails.org/routing.html)
* [url_for (ActionController::Base) - APIdock](http://apidock.com/rails/ActionController/Base/url_for)
* [url_for (ActionView::Helpers::UrlHelper) - APIdock](http://apidock.com/rails/ActionView/Helpers/UrlHelper/url_for)