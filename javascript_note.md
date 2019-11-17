## データ型   
### プリミティブとオブジェクト   

primitive   
- primitive型の値   
- primitive型は６つある
- 値としてのコピー
：string,number,boolean,symbol,null.undefined   


オブジェクト   
 - プロパティとして複数の値をもてる   
 - {...}で作ることができる.この宣言をオブジェクトリテラルという   
 - プリミティブの方がオブジェクトより'軽い'  
 - オブジェクトは”参照”による格納

プリミティブのメソッドを実行した時の内部の動き   
 1. プロパティにアクセスするや否や，一時的にオブジェクトが作られ，それはメソッドを持っている   

 1. メソッドは実行される   

 1. 一時的なオブジェクトは破棄され，プリミティブの値だけが残る   

要約   
 - プリミティブは、null と undefined の例外を除いて、多くの役立つメソッドを提供します。次のチャプターで詳細を学んでいきます。   
 - 形式的には、それらのメソッドは一時的なオブジェクトを通して行われます。しかしJavaScriptエンジンは内部的に最適化するようチューニングされているため、呼び出しのコストはかかりません。   

### resources   
 https://ja.javascript.info/object   
https://ja.javascript.info/object   

### this
メソッドはオブジェクトを this で参照することができます。
関数が “メソッド” 構文で呼び出されたとき: object.method(), 呼び出し中の this の値は、object です。
https://ja.javascript.info/object-methods

### コンストラクタ
通常 {...} 構文で1つのオブジェクトを作ることができます。しかし、しばしば多くの似たようなオブジェクトを作る必要があります。例えば複数のユーザやメニューアイテムなどです。

それは、コンストラクタ関数と "new" 演算子を使うことで実現できます。

https://ja.javascript.info/constructor-new

JSON
JSONの一般的な使い方は、Webサーバとの間でデータを交換すること

jsonオブジェクトはjavascriptのオブジェクトに似た構文でかくが，
プロパティ名を""で囲む．JavaScriptはそうしない

jsonテキストをJavaScriptオブジェクトに変換するには、JavaScript関数JSON.parse()を使用します：


`JavaScript`
data={ "name":"John", "age":30, "city":"New York"}

var obj = JSON.parse('{ "name":"John", "age":30, "city":"New York"}');

http://memopad.bitter.jp/w3c/js/js_json_syntax.html
