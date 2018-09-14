
[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: https://github.com/sawarame/fig-standards-jp/blob/master/accepted/PSR-0.md
[PSR-4]: https://github.com/sawarame/fig-standards-jp/blob/master/accepted/PSR-4-autoloader.md

このページはPSR-1 Basic Coding Standardを日本語訳したものです。  
原文：https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md

<!--
# Basic Coding Standard
-->
# 基本的なコーディング規約

<!--
This section of the standard comprises what should be considered the standard coding elements that are required to ensure a high level of technical interoperability between shared PHP code.
-->
このセクションの規約は、共有されるPHPのコードの相互運用性を保つために必要とされる標準的なコーディング要素を考慮し、構成されています。

<!--
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119].
-->
この文書に記載されているキーワード「しなければならない（MUST）」、「してはならない（MUST NOT）」、「必須（REQUIRED）」、「するものとする（SHALL）」、「しないものとする（SHALL NOT）」、「すべきである（SHOULD）」、「すべきでない（SHOULD NOT）」、「推奨する（RECOMMENDED）」、「することができる（MAY）」、「任意（OPTIONAL）」は、[RFC 2119][]に記載されている内容で解釈されます。

<!--
## 1. Overview
-->
## 1. 概要

<!--
- Files MUST use only `<?php` and `<?=` tags.

- Files MUST use only UTF-8 without BOM for PHP code.

- Files SHOULD *either* declare symbols (classes, functions, constants, etc.) *or* cause side-effects (e.g. generate output, change .ini settings, etc.) but SHOULD NOT do both.

- Namespaces and classes MUST follow an "autoloading" PSR: [[PSR-0], [PSR-4]].

- Class names MUST be declared in `StudlyCaps`.

- Class constants MUST be declared in all upper case with underscore separators.

- Method names MUST be declared in `camelCase`.
-->
- ファイルは`<?php`タグと`<?=`タグのみ使用しなければなりません（MUST）。

- ファイルはBOMなしUTF−8のみ使用しなければなりません（MUST）。

- ファイルはシンボルの宣言(クラス・関数・定数など)または、副作用のある処理（出力の生成やiniの設定等）のどちらかであるべきです（SHOULD）。両方を含むべきではありません（SHOULD NOT）。

- 名前空間とクラスはPSR: [[PSR-0][], [PSR-4][]]「オートローディング」に準拠しなければなりません（MUST）。

- クラス名は`StudlyCaps`(単語の先頭文字を大文字で表記する記法)で宣言しなければなりません（MUST）。

- クラス定数はすべて大文字で、アンダースコアを区切り文字にしたもので宣言しなければなりません（MUST）。

- 関数名は`camelCase`(単語の先頭文字を大文字で表記するが、最初の1文字目は小文字とする記法)で宣言しなければなりません（MUST）。

<!--
## 2. Files
-->
## 2. ファイル

<!--
### 2.1. PHP Tags
-->
### 2.1. PHPタグ

<!--
PHP code MUST use the long `<?php ?>` tags or the short-echo `<?= ?>` tags; it MUST NOT use the other tag variations.
-->
PHPコードは`<?php ?>`タグか、出力の省略形式`<?= ?>`を使用しなければなりません（MUST）。
他のタグのバリエーションを使用してはなりません（MUST NOT）。

<!--
### 2.2. Character Encoding
-->
### 2.2. 文字エンコーディング

<!--
PHP code MUST use only UTF-8 without BOM.
-->
PHPコードはBOMなしUTF−8のみ使用しなければなりません（MUST）。

<!--
### 2.3. Side Effects
-->
### 2.3. 副作用

<!--
A file SHOULD declare new symbols (classes, functions, constants, etc.) and cause no other side effects, or it SHOULD execute logic with side effects, but SHOULD NOT do both.
-->
一つのファイルには、新しいシンボル（クラス・関数・定数等）の宣言と他の副作用を起こさないものか、副作用のあるロジックを行うか、どちらかであるべきで（SHOULD）、両方行うべきではありません（SHOULD NOT）。

<!--
The phrase "side effects" means execution of logic not directly related to declaring classes, functions, constants, etc., *merely from including the file*.
-->
「副作用」が意味するのは、クラス、関数、定数などの宣言に直接関係するものではなく、単にファイルをインクルードすることでロジックが実行されるものを指します。

<!--
"Side effects" include but are not limited to: generating output, explicit use of `require` or `include`, connecting to external services, modifying ini settings, emitting errors or exceptions, modifying global or static variables, reading from or writing to a file, and so on.
-->
「副作用」には、出力の生成・明示的な`require`もしくは`include`の使用・外部サービスへの接続・iniの設定の変更・エラーまたは例外の発行・グローバル変数または静的変数の変更・ファイルへの読み書き等々が含まれますが、これだけというわけではありません。

<!--
The following is an example of a file with both declarations and side effects; i.e, an example of what to avoid:
-->
下記は宣言と副作用の両方を持つファイルの例です。（つまり悪い例）

<!--
~~~php
<?php
// side effect: change ini settings
ini_set('error_reporting', E_ALL);

// side effect: loads a file
include "file.php";

// side effect: generates output
echo "<html>\n";

// declaration
function foo()
{
    // function body
}
~~~
-->
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

<!--
The following example is of a file that contains declarations without side effects; i.e., an example of what to emulate:
-->
下記は副作用のない、宣言が含まれるファイルの例です。（つまり良い例）

<!--
~~~php
<?php
// declaration
function foo()
{
    // function body
}

// conditional declaration is *not* a side effect
if (! function_exists('bar')) {
    function bar()
    {
        // function body
    }
}
~~~
-->
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

<!--
## 3. Namespace and Class Names
-->
## 3. 名前空間・クラス名

<!--
Namespaces and classes MUST follow an "autoloading" PSR: [[PSR-0], [PSR-4]].
-->
名前空間とクラスはPSR: [[PSR-0][], [PSR-4][]]「オートローディング」に準拠しなければなりません（MUST）。

<!--
This means each class is in a file by itself, and is in a namespace of at least one level: a top-level vendor name.
-->
これは各クラスが単独でファイルに存在し、トップレベルがベンダー名の少なくとも1つ以上の名前空間にあることを意味します。

<!--
Class names MUST be declared in `StudlyCaps`.
-->
クラス名は`StudlyCaps`で宣言しなければなりません（MUST）。

<!--
Code written for PHP 5.3 and after MUST use formal namespaces.
-->
PHP5.3以降で書かれたコードは、正式な名前空間を使用しなければなりません（MUST）。

<!--
~~~php
<?php
// PHP 5.3 and later:
namespace Vendor\Model;

class Foo
{
}
~~~
-->
~~~php
<?php
// PHP5.3以降:
namespace Vendor\Model;

class Foo
{
}
~~~

<!--
Code written for 5.2.x and before SHOULD use the pseudo-namespacing convention of `Vendor_` prefixes on class names.
-->
PHP5.2.x以前に書かれたコードは、クラス名に`Vendor_`接頭語の疑似名前空間規則を使用するべきです（SHOULD）。

<!--
~~~php
<?php
// PHP 5.2.x and earlier:
class Vendor_Model_Foo
{
}
~~~
-->
~~~php
<?php
// PHP 5.2.x and earlier:
class Vendor_Model_Foo
{
}
~~~

<!--
## 4. Class Constants, Properties, and Methods
-->
## 4. クラス定数・プロパティ・メソッド

<!--
The term "class" refers to all classes, interfaces, and traits.
-->
「クラス」という用語はすべてのクラス、インターフェース、そしてトレイトを指します。

<!--
### 4.1. Constants
-->
### 4.1. 定数

<!--
Class constants MUST be declared in all upper case with underscore separators. 
For example:
-->
クラス定数はすべて大文字で、アンダースコアを区切り文字にしたもので宣言しなければならなりません（MUST）。

<!--
~~~php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
~~~
-->
~~~php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
~~~

<!--
### 4.2. Properties
-->
### 4.2. プロパティ

<!--
This guide intentionally avoids any recommendation regarding the use of `$StudlyCaps`, `$camelCase`, or `$under_score` property names.
-->
本規約はプロパティ名に関しての命名規則は特に推奨するものはなく、`$StudlyCaps`、`$camelCase`、`$under_score`のいずれを使用しても構いません。

<!--
Whatever naming convention is used SHOULD be applied consistently within a reasonable scope. That scope may be vendor-level, package-level, class-level, or method-level.
-->
しかし、命名規則は適正な範囲で一貫して使用されるべきです（SHOULD）。その範囲は、ベンダーレベル、パッケージレベル、クラスレベル、またはメソッドレベルのいずれかです。

<!--
### 4.3. Methods
-->
### 4.3. メソッド

<!--
Method names MUST be declared in `camelCase()`.
-->
メソッド名は`camelCase()`で宣言しなければなりません（MUST）。
