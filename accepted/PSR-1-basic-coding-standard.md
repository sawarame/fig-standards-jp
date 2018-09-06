
[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: https://github.com/sawarame/fig-standards-jp/blob/master/accepted/PSR-0.md
[PSR-4]: https://github.com/sawarame/fig-standards-jp/blob/master/accepted/PSR-4-autoloader.md

このページはPSR-1 Basic Coding Standardを日本語訳したものです。  
原文：https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md

# 基本的なコーディング規約

この規約のセクションでは共有のPHPコード間で、技術的なレベルの高い相互運用性を確実にするために必要な、標準でコーディング要素が考慮すべき内容を含みます。


この文書に記載されているキーワード「しなければならない（MUST）」、「してはならない（MUST NOT）」、「必須（REQUIRED）」、「するものとする（SHALL）」、「しないものとする（SHALL NOT）」、「すべきである（SHOULD）」、「すべきでない（SHOULD NOT）」、「推奨する（RECOMMENDED）」、「することができる（MAY）」、「任意（OPTIONAL）」は、[RFC 2119][]に記載されている内容で解釈されます。


## 1. 概要

- ファイルは`<?php`タグと`<?=`タグのみ使用しなければなりません（MUST）。

- ファイルはBOMなしUTF−8のみ使用しなければなりません（MUST）。

- ファイルはシンボルの宣言(クラス・関数・定数など)または、副作用のある処理（出力の生成やiniの設定等）のどちらかであるべきです（SHOULD）。両方を含むべきではありません（SHOULD NOT）。

- 名前空間とクラスはPSR: [[PSR-0][], [PSR-4][]]「オートローディング」に準拠しなければなりません（MUST）。

- クラス名は`StudlyCaps`(単語の先頭文字を大文字で表記する記法)で宣言しなければなりません（MUST）。

- クラス定数はすべて大文字で、アンダースコアを区切り文字にしたもので宣言しなければなりません（MUST）。

- 関数名は`camelCase`(単語の先頭文字を大文字で表記するが、最初の1文字目は小文字とする記法)で宣言しなければなりません（MUST）。

## 2. ファイル

### 2.1. PHPタグ

PHPコードは`<?php ?>`タグか、出力の省略形式`<?= ?>`を使用しなければなりません（MUST）。
他のタグのバリエーションを使用してはなりません（MUST NOT）。

### 2.2. 文字エンコーディング

PHPコードはBOMなしUTF−8のみ使用しなければなりません（MUST）。

### 2.3. 副作用

一つのファイルには新しいシンボル（クラス・関数・定数等）と他の副作用を引き起こさないものが宣言されるか、副作用のあるロジックが実行されるべきで（SHOULD）、両方行うべきではありません（SHOULD NOT）。

「副作用」というフレーズが意味するものは、クラス・関数・定数などの宣言に直接関係しないロジックの実行をファイルを含めることを意味します。

「副作用」には、出力の生成・明示的な`require`もしくは`include`の使用・外部サービスへの接続・iniの設定の変更・エラーまたは例外の発行・グローバル変数または静的変数の変更・ファイルへの読み書き等々が含まれますが、限定はされません。

下記は宣言と副作用の両方を持つファイルの例です。（つまり悪い例）

~~~php
<?php
// 副作用: iniの設定変更
ini_set('error_reporting', E_ALL);

// 副作用: ファイルの読み込み
include "file.php";

// 副作用: 出力の生成
echo "<html>\n";

// 宣言
function foo()
{
    // 関数の処理
}
~~~


下記は副作用のない、宣言が含まれるファイルの例です。（つまり良い例）

~~~php
<?php
// 宣言
function foo()
{
    // 関数の処理
}

// 条件付き宣言は副作用ではありません
if (! function_exists('bar')) {
    function bar()
    {
        // 関数の処理
    }
}
~~~

## 3. 名前空間・クラス名

名前空間とクラスはPSR: [[PSR-0][], [PSR-4][]]「オートローディング」に準拠しなければなりません（MUST）。

これは各クラスが単独でファイルに存在し、トップレベルがベンダー名の少なくとも1つ以上の名前空間にあることを意味します。

クラス名は`StudlyCaps`(単語の先頭文字を大文字で表記する記法)で宣言しなければなりません（MUST）。

PHP5.3以降で書かれたコードは、正式な名前空間を使用しなければなりません（MUST）。

~~~php
<?php
// PHP5.3以降:
namespace Vendor\Model;

class Foo
{
}
~~~

PHP5.2.x以前に書かれたコードは、クラス名に`Vendor_`接頭語の疑似名前空間規則を使用するべきです（SHOULD）。

~~~php
<?php
// PHP 5.2.x and earlier:
class Vendor_Model_Foo
{
}
~~~

## 4. クラス定数・プロパティ・メソッド

「クラス」という用語はすべてのクラス、インターフェース、そしてトレイトを指します。

### 4.1. 定数

クラス定数はすべて大文字で、アンダースコアを区切り文字にしたもので宣言しなければならなりません（MUST）。

~~~php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
~~~

### 4.2. プロパティ

このガイドはプロパティ名の`$StudlyCaps`、`$camelCase`、`$under_score`を使用することに関しての推奨を、意図的に避けています（つまりどれを使用してもよい）。

しかし、命名規則は適正なスコープで一貫しているべきです（SHOULD）。そのスコープは、ベンダーレベル、パッケージレベル、クラスレベル、またはメソッドレベルのいずれかです。

### 4.3. Methods

関数名は`camelCase()`で宣言しなければなりません（MUST）。
