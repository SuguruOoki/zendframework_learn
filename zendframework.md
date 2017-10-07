# Zendframework

## 概要
基本的にはMVCフレームワークであるのでRailsと構想は変わらないし、Djangoとも似ている。そのため、あまり違和感なくできそうではある。QueryBuilderも用意されているのでそこをうまく使ってクエリの方も作成していく所存。
smartyを使っているため、基本的には、これから必要。

## 歴史(ざっくり)
#### ZF1

FrameworkでMVC！MVC！と言っていたあの頃。PHPエンジンを開発したZendのFrameworkということで今もPHP界隈では政治の中心的な立ち位置ですね。

composerが出る前の時代でしたので、これさえいれておけば他にいらないライブラリ集というオールインワンが圧倒的で便利でありました。
はじめは、Zend_DbのためにZF1を入れたのですが、いつの間にか他のコンポーネントを使い出していろいろ学ばせてもらってたのも良い思い出です。

#### ZF2

リリースされた当初、コンポーネントが分割できるようになったことに喜んでいたものですが、Zend\Dbの使い方がものすごく変わっていたのを見て唖然としました。

Adapterで手軽に使っていたメソッドが奥のクラス階層に移動していたり・・・
fetchするときも返り値がデータそのものではない時があったり・・・
普通に使うだけでも手順が増えてしまっていたのです。
これですぐ移行するという選択肢はなくなり、なんとか折り合いをつけてからにしようとしていたらZF1はサポートが延長され、折り合いをつける理由がなくなって今に至ります。

#### そしてZF3の絶望

PHP7や5.6が登場してライブラリも新しい世代へ変わることに動き出していた時期でしたので、ZendFrameworkの次期バージョンのZF3発表。
とりあえず作っているよ。というアナウンスがありZF2が生まれ変わるのか！と期待していたら、2系での延長上での開発でした。

ここでZend\Dbの使い勝手が変わることはなくなったのです。

#### ZF1がEOLに

[Zend Framework 1 End-of-Life Announcement](https://framework.zend.com/blog/2016-06-28-zf1-eol.html)

ZF1の延長されてきたサポートが、ようやくといいますか、やっと9月にEOLを迎えるというアナウンスがされました。
SQL関係でセキュリティ上好ましくないことをしているなどありますので、ZF2や3にしろ他のライブラリにしろなにかしら移行する理由ができました。

## 折り合いをつけるには残念なところを変えてゆく

絶望を感じるところですが、メジャーな上、徳丸さんがとりあえずソース見てくれるところなので、切り捨てるというのはとりあえずなしで考えたら、どうやったら受け入れられるのか？使ってみようと思えるのか？考えました。

### ZF2でよかった点

* オブジェクト指向やイベントハンドラ、DI意識されててZF1よりは設計良くなってたよ
* そのおかげでそれぞれの受け持ち範囲がわかりやすくなったよ
* select結果がIterator(ResultSet)で返されるので、データが大量の時に扱いやすくなったよ

### ZF2でわるかった点

* 手順が全然変わったよ
* 公式マニュアルがひどいよ。ソース見ながら使った方が早いよ
* 今まですぐに届いていた関数が分散してたよ(例：beginTransaction)

### 基本的な使い方

* 主にSelect系SQL文を実行する場合→ Adapter
* Insert,Updateする場合→ AbstractTableで各DBテーブルのサブクラスを作る
* プログラマブルなSQLの組み立て→ Sql `Sql->prepareStatementForSqlObject($select)->execute()`
* TableのFeatureは、Eventで処理を追加できるよ

以上の方針にすると流れがわかりやすく、手順も最短ルートで呼び出してゆけるかと思います。が、メソッド名の長さなどイけてない部分はまだまだあります。
けどそれらをクリアするにはラッパークラスを作るしかないのか･･･
という残念なモチベーションでラッパークラスを作りました。

[github zfdb-compat](https://github.com/reioto/zfdb-compat)

compatとついていますが、完全にZF1のインターフェースではないので互換とはいえないのです。
といいますのも、作ってる途中で過去仕様に捕らわれるのではなくZF2の良いところは本当に良くなっているので、そこは取り入れていくべきだと思ったからです。
具体的にAdapterでは全結果のarrayを返す`fetchAll`よりもイテレータを返す`fetchResult`を使うべきです。
移行するまでのつなぎとか、よく使っていたメソッドがどうなったのかソースを見ていただけるとわかっていただけるかと。ご参考までにどうぞ



## バージョンによる特徴

現在は1, 2, 3がリリースされている。１に比べ、２は持味であった高速性などが失われており、２よりも１の方が好まれる傾向にある。

## 開発環境の構築

#### ざっくり

##### MAMPにZendFramework1をインストールする
```php:index.php
Warning: require_once(Zend/Application.php): failed to open stream: No such file or directory in /Applications/MAMP/htdocs/...
```

MAMPでZendFramework1をインストールする必要があったので、以下の手順でインストールした。

1. [MAMP](https://www.mamp.info/en/)をインストールする
2. MAMPで使用しているPHPのバージョンを確認する
2. [GitHubのReleaseページからフレームワークを入手](https://github.com/zendframework/zf1/releases)
3. 解凍し、`Library/Zend/` ディレクトリを取り出し、他は削除
4. `Zend/`ディレクトリを`/Applications/MAMP/bin/php/php5.x.x/lib/php`へ移動
  * x.xには先程調べたバージョン番号が入る
4. `/Applications/MAMP/bin/php/phpx.x.x/lib/php`

`php.ini`の`include_path`を編集するのであれば、`Zend/`ディレクトリは他の場所でも良い模様



## Controllerについて

####  [ZF3] Zend Framework 3 コントローラを追加する

以下に例を記載します。

```
Application/
	src/
		Controller/
			IndexController.php
			TestController.php // 今回追加するコントローラ
```

routes にコントローラ設定を追加する。

```:Application/config/module.config.php
'test' => [
    'type'    => Segment::class,
    'options' => [
        'route'    => '/test[/:action]',
        'defaults' => [
            'controller' => Controller\TestController::class,
            'action'     => 'index',
        ],
    ],
],
```

#### 補足

ページ別、機能が大きい場合などはモジュールを追加して分けたりする方がスマートに開発できる。

[14.2. How to Create a New Module?](https://olegkrivtsov.github.io/using-zend-framework-3-book/html/en/Creating_a_New_Module/How_to_Create_a_New_Module_.html)


## View

## Model

## やり方まとめ
### [ZF3] AjaxリクエストをZend Framework 3でレスポンスする

1. module.config.phpを設定する
2. JsonModelを使用して返却する

### module.config.phpを設定する

```
'view_manager' =>
	'strategies' => 'ViewJsonStrategy' // この部分
```

JSONペイロードをレスポンスできるように設定に追加します。
この設定がないと下記の処理でJSONがレスポンスされません。

### JsonModelを使用して返却する

```:IndexController.php

use Zend\View\Model\JsonModel; // namespace

public function indexAction()
{
	return new ViewModel();
}

public function ajaxAction()
{
	// $data = $this->getRequest->getQuery(); // GET
	// $data = $this->getRequest->getPost(); // POST
	$data = array(
		'message' => 'Hello World!!',
	);
	return new JsonModel($data);
	// namespaceを使用しない場合は
	// return new \Zend\View\Model\JsonModel($data)
}
```

[URL Path] /application/ajax

- /{controller}/{action}
- IndexControllerはapplicationとしてコントローラ設定されています

#### 備考

ログ出力

```
private function __log ()
{
    $stream = @fopen('/var/www/html/zend/data/logs/debug_log.txt', 'a', false);
    if (!$stream) {
        throw new Exception("Can't open stream.");
    }
//        $stream = 'php://output';
	// 適宜 namespace を使用
    $writer = new \Zend\Log\Writer\Stream($stream);
    $logger = new \Zend\Log\Logger();
    $logger->addWriter($writer);
    
    $param = $this->getRequest()->getPost();
    
    $logger->debug(var_export($param, true));
}
```

Ajax sample
[[jQuery] AjaxでPHPにPOST送信する](https://spelunker2.wordpress.com/2014/10/02/jquery-ajax%E3%81%A7php%E3%81%ABpost%E9%80%81%E4%BF%A1%E3%81%99%E3%82%8B/)


## その他

How to Japanize Validation of Zend Framework 2.3
エラーメッセージの翻訳を行うには、以下の様にします。
#### ロケールを日本語とする
```php:module/Application/config/module.config.php
// 'locale' => 'en_US',
'locale' => 'ja_JP'
```
####  翻訳ファイルを設定する
```php:module/Application/Module.php
public function onBootstrap(MvcEvent $e)
{
    $eventManager        = $e->getApplication()->getEventManager();
    $moduleRouteListener = new ModuleRouteListener();
    $moduleRouteListener->attach($eventManager);

    // 以下を追加
    $sm = $e->getApplication()->getServiceManager();
    $translator = $sm->get('MvcTranslator');
    $translator->addTranslationFile(
        'phpArray',
        'vendor/zendframework/zendframework/resources/languages/ja/' .
        'Zend_Validate.php',
        'default',
        'ja_JP'
    );

    \Zend\Validator\AbstractValidator::setDefaultTranslator(
        new \Zend\Mvc\I18n\Translator($translator)
    );
}
```
#### 実行結果
![名称未設定.png](https://qiita-image-store.s3.amazonaws.com/0/28376/fa117b5f-d313-a55c-f24d-1902819f738b.png)

`Zend_Validate.php` の指定がダサいです。なんか `_DIR_` とか使って格好良くしたいんですけど、力不足ですすいません。

以下を参考にしました。
http://stackoverflow.com/questions/23151917/zf2-3-translate-validation-message

## ZendFramework1 で setcookie
ZF1 で `Set-Cookie` ヘッダを吐きたかったんですが、ググると `Zend_Http_Cookie` ばっかり引っかかります。
`Zend_Http_Cookie` は `Zend_Http_Client` で利用するもので、フロントコントローラでの利用は想定されていません(多分)。

さらにググると、ネイティブの `setcookie` 関数を使用している例も結構見つかります。
「まさかできないわけないだろー」と調べた所、無事`Zend_Http_Header_SetCookie` クラスを発見出来ました。
このクラスを使用するとクッキーの属性などをオブジェクティブに扱えるので便利です。

…が、実は公式ドキュメントに書いてあったりします。
[レスポンスオブジェクト - Zend_Controller - Zend Framework](http://framework.zend.com/manual/1.12/ja/zend.controller.response.html#zend.controller.response.headers.setcookie)

上記から引用：

```php
$cookie = new Zend_Http_Header_SetCookie();
$cookie->setName('foo')
       ->setValue('bar')
       ->setDomain('example.com')
       ->setPath('/')
       ->setHttponly(true);
$this->getResponse()->setRawHeader($cookie);
```

`Zend_Http_Header_SetCookie` でググっても 49 件しかヒットしない

## 重複登録予防
* ブラウザの戻るボタンでの重複登録防止(クッキー使用) 
* submitボタンのダブルクリックでの重複登録防止（アラート使用）


**MainController側**
　トークンを発行し、ビューに挿入する。

```php:MainController.php＞indexAction
//トークン発行
$tokenId = substr(md5(uniqid().mt_rand()),-13,10);
$this -> view -> tokenId = $tokenId;
```

**入力欄**
　↑で発行し送られてきたトークンをhiddenでさらに送信。
　このときダブルクリックでの重複登録をさせないためにアラートを表示させる。

```html:index.tpl
<form action="[SampleController.php＞insertActionへのURL]" method="POST">
　<input type="text" name="title">
  <input type="hidden" name="ticket" value="($this->tokenId)">
　<input type="submit" value="登録" onClick="javascript:doubleClick(this)">
</form>


<script type="text/javascript">
    //登録ボタンのダブルクリック防止
    function doubleClick(btn){
        alert("登録しました！！");
    }
</script>

```

**入力情報が送られてくる側**
　入力情報と共に送られてきたトークンを取得→クッキーに登録。
　  issetがtrueならクッキーデータがある(戻るボタンや更新ボタンを押した)ということで、入力ページにジャンプさせる。

```php:SampleController.php＞insertAction
//トークン取得
$req = $this -> getRequest();
$ticket='';
$ticket = trim($req -> getPost('ticket'));
//クッキー発行
setcookie('tickettemp', $ticket, time()+(3600*24*30));
//戻るボタンとか押してたら入力ページに戻す
if(isset($_COOKIE['tickettemp'])){
  　if($ticket == $_COOKIE['tickettemp']){
  　　//入力ページにジャンプ
  　　$this -> _redirect('/モジュール名/main/index');
　　}
}

//以下で入力情報の取得とDB登録処理をする。
```

## パラメータ取得
**パラメータの取得の比較**

通常時とZendでの取得方法を比較。
両方使ったことあると、なんか迷う。

#### GETパラメータの取得
```php:通常
$_GET['key'];
```
```php:Zend
$this->getRequest()->getQuery('key');
```

#### POSTパラメータの取得
```php:通常
$_POST['key'];
```
```php:Zend
$this->getRequest()->getPost('key');
```

#### Cookieの取得
```php:通常
$_COOKIE['cookie'];
```
```php:Zend
$this->getRequest()->getCookie('cookie');
```


## 参考

[zendから外部APIを呼び出すサンプル](https://github.com/zendframework/ZendService_Api)

[[ZF3] AjaxリクエストをZend Framework 3でレスポンスする](https://qiita.com/nixe-takeyama/items/f391e6ad4f6b6c33c202)

[[jQuery] AjaxでPHPにPOST送信する](https://spelunker2.wordpress.com/2014/10/02/jquery-ajax%E3%81%A7php%E3%81%ABpost%E9%80%81%E4%BF%A1%E3%81%99%E3%82%8B/)