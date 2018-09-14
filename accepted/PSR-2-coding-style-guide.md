このページはPSR-2 Coding Style Guideを日本語訳したものです。  
原文：https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md

<!--
# Coding Style Guide
-->
# コーディングスタイルガイド

<!--
This guide extends and expands on [PSR-1], the basic coding standard.
-->
このガイドは、基本的なコーディング規約[PSR-1](https://github.com/sawarame/fig-standards-jp/blob/master/accepted/PSR-1-basic-coding-standard.md)を拡張したものです。

<!--
The intent of this guide is to reduce cognitive friction when scanning code from different authors.
It does so by enumerating a shared set of rules and expectations about how to format PHP code.
-->
このガイドの目的は、別の作成者がコードを見るときに認識のズレを減らすことです。
これはPHPコードの書式設定についての共有されたルールや可能性の一式を列挙することで実現します。

<!--
The style rules herein are derived from commonalities among the various member projects. 
When various authors collaborate across multiple projects, it helps to have one set of guidelines to be used among all those projects. 
Thus, the benefit of this guide is not in the rules themselves, but in the sharing of those rules.
-->
ここでのスタイルルールは、様々なメンバーのプロジェクトの共通点から得られたものです。
様々な作成者が複数のプロジェクトを横断して参画する場合、それらのプロジェクトで使用される一つのガイドラインを持つことは役に立ちます。
従ってこのガイドの利点はルールそのものではなく、ルールを共有することにあります。

<!--
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119].
-->
この文書に記載されているキーワード「しなければならない（MUST）」、「してはならない（MUST NOT）」、「必須（REQUIRED）」、「するものとする（SHALL）」、「しないものとする（SHALL NOT）」、「すべきである（SHOULD）」、「すべきでない（SHOULD NOT）」、「推奨する（RECOMMENDED）」、「することができる（MAY）」、「任意（OPTIONAL）」は、[RFC 2119](http://www.ietf.org/rfc/rfc2119.txt)に記載されている内容で解釈されます。

<!--
## 1. Overview
-->
## 1. 概要

<!--
- Code MUST follow a "coding style guide" PSR [[PSR-1]].

- Code MUST use 4 spaces for indenting, not tabs.

- There MUST NOT be a hard limit on line length; the soft limit MUST be 120 characters; lines SHOULD be 80 characters or less.

- There MUST be one blank line after the `namespace` declaration, and there MUST be one blank line after the block of `use` declarations.

- Opening braces for classes MUST go on the next line, and closing braces MUST go on the next line after the body.

- Opening braces for methods MUST go on the next line, and closing braces MUST go on the next line after the body.

- Visibility MUST be declared on all properties and methods; `abstract` and `final` MUST be declared before the visibility; `static` MUST be declared after the visibility.

- Control structure keywords MUST have one space after them; method and function calls MUST NOT.

- Opening braces for control structures MUST go on the same line, and closing braces MUST go on the next line after the body.

- Opening parentheses for control structures MUST NOT have a space after them, and closing parentheses for control structures MUST NOT have a space before.
-->
- PSR: [[PSR-1](https://github.com/sawarame/fig-standards-jp/blob/master/accepted/PSR-1-basic-coding-standard.md)]「コーディングスタイルガイド」に準拠しなければなりません（MUST）。

- インデントはタブではなく、4つのスペースを使用しなければなりません（MUST）。

- 行の長さはハードリミットがあってはなりません（MUST NOT）。ソフトリミットは120文字でなければなりませんが（MUST）、80文字以内にするべきです（SHOULD）。

- `namespace`宣言の後には空行を一行を入れなければならず（MUST）、`use`宣言の後にも空行を一行を入れなければなりません（MUST）。

- クラスの開き中括弧は次の行に記載しなければならず（MUST）、閉じ中括弧は本文の次の行に記載しなければなりません（MUST）。

- メソッドの開き中括弧は次の行に記載しなければならず（MUST）、閉じ中括弧は本文の次の行に記載しなければなりません（MUST）。

- アクセス修飾子はすべてのプロパティとメソッドに宣言しなければなりません（MUST）。`abstract`と`final`はアクセス修飾子の前に宣言しなければなりません（MUST）。`static`はアクセス修飾子の後ろに宣言しなければなりません（MUST）。

- [制御構造](http://php.net/manual/ja/language.control-structures.php)の[予約語](http://www.php.net/manual/ja/reserved.keywords.php)の後には1つのスペースを入れなければなりません（MUST）。メソッドや関数の呼び出しではスペースを入れてはなりません（MUST NOT）。

- 制御構造の開き中括弧は同じ行に記載しなければならず（MUST）、閉じ中括弧は本文の次の行に記載しなければなりません（MUST）。

- 制御構造の開き丸括弧の後ろにはスペースを入れてはならず（MUST NOT）、閉じ丸括弧の前にはスペースを入れてはなりません（MUST NOT）。

<!--
### 1.1. Example
-->
### 1.1. 例

<!--
This example encompasses some of the rules below as a quick overview:
-->
下記は概要の内容を表した例です。

<!--
~~~php
<?php
namespace Vendor\Package;

use FooInterface;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class Foo extends Bar implements FooInterface
{
    public function sampleMethod($a, $b = null)
    {
        if ($a === $b) {
            bar();
        } elseif ($a > $b) {
            $foo->bar($arg1);
        } else {
            BazClass::bar($arg2, $arg3);
        }
    }

    final public static function bar()
    {
        // method body
    }
}
~~~
-->
~~~php
<?php
namespace Vendor\Package;

use FooInterface;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class Foo extends Bar implements FooInterface
{
    public function sampleMethod($a, $b = null)
    {
        if ($a === $b) {
            bar();
        } elseif ($a > $b) {
            $foo->bar($arg1);
        } else {
            BazClass::bar($arg2, $arg3);
        }
    }

    final public static function bar()
    {
        // メソッド本文
    }
}
~~~

<!--
## 2. General
-->
## 2. 一般

<!--
### 2.1. Basic Coding Standard
-->
### 2.1. 基本的なコーディング規約

<!--
Code MUST follow all rules outlined in [PSR-1].
-->
[PSR-1](https://github.com/sawarame/fig-standards-jp/blob/master/accepted/PSR-1-basic-coding-standard.md)に準拠していなければなりません（MUST）。

<!--
### 2.2. Files
-->
### 2.2. ファイル

<!--
All PHP files MUST use the Unix LF (linefeed) line ending.

All PHP files MUST end with a single blank line.

The closing `?>` tag MUST be omitted from files containing only PHP.
-->
すべてのPHPファイルの改行コードはUnix LF(linefeed)を使用しなければなりません（MUST）。

すべてのPHPのファイルの末尾には空行を含まなければなりません（MUST）。

PHPコードのみのファイルでは、PHPの閉じタグ`?>`は省略しなければなりません（MUST）。

<!--
### 2.3. Lines
-->
### 2.3. 行

<!--
There MUST NOT be a hard limit on line length.

The soft limit on line length MUST be 120 characters; automated style checkers MUST warn but MUST NOT error at the soft limit.

Lines SHOULD NOT be longer than 80 characters; lines longer than that SHOULD be split into multiple subsequent lines of no more than 80 characters each.

There MUST NOT be trailing whitespace at the end of non-blank lines.

Blank lines MAY be added to improve readability and to indicate related blocks of code.

There MUST NOT be more than one statement per line.
-->
行に対してハードリミットがあってはなりません（MUST NOT）。

行のソフトリミットは120文字でなければなりません（MUST）。自動スタイルチェッカーは警告をしなければなりませんが（MUST）、エラーにしてはいけません（MUST NOT）。

行は80文字より長くあるべきではありません（SHOULD NOT）。それよりも長い行は、80文字以内のそれぞれの行に分割されるべきです（SHOULD）。

空行以外の行の末尾にスペースがあってはなりません（MUST NOT）。

可読性を向上させ、コードの関連ブロックを示すために空行を加えることが出来ます（MAY）。

一行に複数の命令文があってはなりません（MUST NOT）。

<!--
### 2.4. Indenting
-->
### 2.4. インデント

<!--
Code MUST use an indent of 4 spaces, and MUST NOT use tabs for indenting.
-->
インデントは4つのスペースを使用しなければならず（MUST）、タブを使用してはなりません（MUST NOT）。

<!--
> N.b.: Using only spaces, and not mixing spaces with tabs, helps to avoid
> problems with diffs, patches, history, and annotations. The use of spaces
> also makes it easy to insert fine-grained sub-indentation for inter-line
> alignment.
-->
> 注意：スペースのみを使用しタブと混在させないことは、差分やパッチ、履歴、注釈の表示のズレを回避するのに役に立ちます。
> また、スペースを利用すると、行間の位置合わせをするために細かいサブインデントを挿入するのを容易にします。

<!--
### 2.5. Keywords and True/False/Null
-->
### 2.5. 予約語とTrue/False/Null

<!--
PHP [keywords] MUST be in lower case.

The PHP constants `true`, `false`, and `null` MUST be in lower case.
-->
PHPの[予約語](http://www.php.net/manual/ja/reserved.keywords.php)は小文字で記載しなければなりません（MUST）。

PHPの定数である`true`、`false`、`null`は小文字で記載なければなりません（MUST）。

<!--
## 3. Namespace and Use Declarations
-->
## 3. NamespaceとUseの宣言

<!--
When present, there MUST be one blank line after the `namespace` declaration.

When present, all `use` declarations MUST go after the `namespace`
declaration.

There MUST be one `use` keyword per declaration.

There MUST be one blank line after the `use` block.
-->
`namespace`を宣言する場合は、その後に空行が一行なければなりません（MUST）。

`use`宣言をする場合は`namespace`宣言の後に記載しなければなりません（MUST）。

宣言ごとに`use`を使用しなければなりません（MUST）。

`use`宣言のブロックの後に空行が1一行なければなりません（MUST）。

<!--
For example:

~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

// ... additional PHP code ...

~~~
-->
例：

~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

// ... 何かしらのPHPコード ...

~~~

<!--
## 4. Classes, Properties, and Methods
-->
## 4. クラス・プロパティ・メソッド

<!--
The term "class" refers to all classes, interfaces, and traits.
-->
「クラス」という用語はすべてのクラス、インターフェース、トレイトのことを指します。

<!--
### 4.1. Extends and Implements
-->
### 4.1. ExtendsとImplements

<!--
The `extends` and `implements` keywords MUST be declared on the same line as the class name.

The opening brace for the class MUST go on its own line; the closing brace for the class MUST go on the next line after the body.
-->
`extends`と`implements`はクラスと同じ行で宣言されなければなりません（MUST）。

クラスの開き中括弧は単独の行でなければならず（MUST）、閉じ中括弧は本文最後の次の行になければなりません（MUST）。

<!--
~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements \ArrayAccess, \Countable
{
    // constants, properties, methods
}
~~~
-->
~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements \ArrayAccess, \Countable
{
    // 定数, プロパティ, メソッド
}
~~~

<!--
Lists of `implements` MAY be split across multiple lines, where each subsequent line is indented once. 
When doing so, the first item in the list MUST be on the next line, and there MUST be only one interface per line.
-->
`implements`のリストは複数の行に分割することができ（MAY）、それぞれ一つのインデントを入れなければなりません（MUST）。
分割した場合、リストの最初の項目は次の行に記載しなければならず（MUST）、一行ごとに一つのインターフェースを定義しなければなりません（MUST）。

<!--
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements
    \ArrayAccess,
    \Countable,
    \Serializable
{
    // constants, properties, methods
}
-->
~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements
    \ArrayAccess,
    \Countable,
    \Serializable
{
    // 定数, プロパティ, メソッド
}
~~~

<!--
### 4.2. Properties
-->
### 4.2. プロパティ

<!--
Visibility MUST be declared on all properties.

The `var` keyword MUST NOT be used to declare a property.

There MUST NOT be more than one property declared per statement.

Property names SHOULD NOT be prefixed with a single underscore to indicate protected or private visibility.

A property declaration looks like the following.
-->
すべてのプロパティにアクセス修飾子を宣言しなければなりません（MUST）。

プロパティは`var`をつけて宣言してはなりません（MUST NOT）。

一つの命令文で複数のプロパティを宣言してはなりません（MUST NOT）。

protectedやprivateのアクセス修飾子を示すために、一つのアンダースコアをプロパティ名の接頭語に使用するべきではありません（SHOULD NOT）。

プロパティの宣言は以下のようになります。

<!--
<?php
namespace Vendor\Package;

class ClassName
{
    public $foo = null;
}
-->
~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public $foo = null;
}
~~~

<!--
### 4.3. Methods
-->
### 4.3. メソッド

<!--
Visibility MUST be declared on all methods.

Method names SHOULD NOT be prefixed with a single underscore to indicate protected or private visibility.

Method names MUST NOT be declared with a space after the method name. 
The opening brace MUST go on its own line, and the closing brace MUST go on the next line following the body. 
There MUST NOT be a space after the opening parenthesis, and there MUST NOT be a space before the closing parenthesis.

A method declaration looks like the following. 
Note the placement of parentheses, commas, spaces, and braces:
-->
すべてのメソッドにアクセス修飾子を宣言しなければなりません（MUST）。

protectedやprivateのアクセス修飾子を示すために、一つのアンダースコアをメソッド名の接頭語に使用するべきではありません（SHOULD NOT）。

メソッド名の後ろにスペースを入れて宣言してはなりません（MUST NOT）。
メソッドの開き中括弧は単独の行でなければならず（MUST）、閉じ中括弧は本文最後の次の行になければなりません（MUST）。
メソッドの開き丸括弧の後にはスペースを入れてはならず（MUST NOT）、閉じ丸括弧の前にもスペースを入れてはなりません（MUST NOT）。

メソッドの宣言は下記のようになります。
丸括弧、カンマ、スペース、中括弧の配置に注意して下さい。

<!--
~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function fooBarBaz($arg1, &$arg2, $arg3 = [])
    {
        // method body
    }
}
~~~
-->
~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function fooBarBaz($arg1, &$arg2, $arg3 = [])
    {
        // メソッド本文
    }
}
~~~

<!--
### 4.4. Method Arguments
-->
### 4.4. メソッドの引数

<!--
In the argument list, there MUST NOT be a space before each comma, and there MUST be one space after each comma.

Method arguments with default values MUST go at the end of the argument list.
-->
引数のリストはそれぞれのカンマの前にスペースを入れてはならず（MUST NOT）、それぞれのカンマの後にスペースを入れなければなりません（MUST）。

初期設定値のある引数は、引数リストの最後でなければなりません（MUST）。

<!--
~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function foo($arg1, &$arg2, $arg3 = [])
    {
        // method body
    }
}
~~~
-->
~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function foo($arg1, &$arg2, $arg3 = [])
    {
        // メソッド本文
    }
}
~~~

<!--
Argument lists MAY be split across multiple lines, where each subsequent line is indented once. 
When doing so, the first item in the list MUST be on the next line, and there MUST be only one argument per line.

When the argument list is split across multiple lines, the closing parenthesis and opening brace MUST be placed together on their own line with one space between them.
-->

引数リストは複数の行に分割することができ（MAY）、それぞれ一つのインデントを入れなければなりません（MUST）。
分割した場合、リストの最初の項目は次の行に記載しなければならず（MUST）、一行ごとに一つの引数を記載しなければなりません（MUST）。

引数リストを複数行に分割した場合、閉じ丸括弧と開き中括弧はスペースを挟んで単独で同じ行に記載しなければなりません（MUST）。

<!--
~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function aVeryLongMethodName(
        ClassTypeHint $arg1,
        &$arg2,
        array $arg3 = []
    ) {
        // method body
    }
}
~~~
-->
~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function aVeryLongMethodName(
        ClassTypeHint $arg1,
        &$arg2,
        array $arg3 = []
    ) {
        // メソッド本文
    }
}
~~~

<!--
### 4.5. `abstract`, `final`, and `static`
-->
### 4.5. `abstract`・`final`・`static`

<!--
When present, the `abstract` and `final` declarations MUST precede the visibility declaration.

When present, the `static` declaration MUST come after the visibility declaration.
-->
`abstract`と`final`を宣言する場合はアクセス修飾子より前に宣言しなければなりません（MUST）。

`static`を宣言する場合はアクセス修飾子より後に宣言しなければなりません。

<!--
~~~php
<?php
namespace Vendor\Package;

abstract class ClassName
{
    protected static $foo;

    abstract protected function zim();

    final public static function bar()
    {
        // method body
    }
}
~~~
-->
~~~php
<?php
namespace Vendor\Package;

abstract class ClassName
{
    protected static $foo;

    abstract protected function zim();

    final public static function bar()
    {
        // メソッド本文
    }
}
~~~

<!--
### 4.6. Method and Function Calls
-->
### 4.6. メソッドと関数の呼び出し

<!--
When making a method or function call, there MUST NOT be a space between the method or function name and the opening parenthesis, there MUST NOT be a space after the opening parenthesis, and there MUST NOT be a space before the closing parenthesis.
In the argument list, there MUST NOT be a space before each comma, and there MUST be one space after each comma.
-->
メソッドもしくは関数を呼び出す場合、メソッド名・関数名と開き丸括弧の間にスペースを入れてはならず（MUST NOT）、開き丸括弧の後にもスペースを入れてはならず（MUST NOT）、閉じ丸括弧の前にもスペースを入れてはなりません（MUST NOT）。
引数リストのそれぞれのカンマの前にスペースを入れてはならず（MUST NOT）、それぞれのカンマの後にスペースを入れなければなりません（MUST）。

<!--
<?php
bar();
$foo->bar($arg1);
Foo::bar($arg2, $arg3);
-->
~~~php
<?php
bar();
$foo->bar($arg1);
Foo::bar($arg2, $arg3);
~~~

<!--
Argument lists MAY be split across multiple lines, where each subsequent line is indented once.
When doing so, the first item in the list MUST be on the next line, and there MUST be only one argument per line.
-->
引数リストは複数の行に分割することができ（MAY）、それぞれ一つのインデントを入れなければなりません（MUST）。
分割した場合、リストの最初の項目は次の行に記載しなければならず（MUST）、一行ごとに一つの引数を記載しなければなりません（MUST）。

<!--
~~~php
<?php
$foo->bar(
    $longArgument,
    $longerArgument,
    $muchLongerArgument
);
~~~
-->
~~~php
<?php
$foo->bar(
    $longArgument,
    $longerArgument,
    $muchLongerArgument
);
~~~

<!--
## 5. Control Structures
-->
## 5. 制御構造

<!--
The general style rules for control structures are as follows:

- There MUST be one space after the control structure keyword
- There MUST NOT be a space after the opening parenthesis
- There MUST NOT be a space before the closing parenthesis
- There MUST be one space between the closing parenthesis and the opening brace
- The structure body MUST be indented once
- The closing brace MUST be on the next line after the body

The body of each structure MUST be enclosed by braces.
This standardizes how the structures look, and reduces the likelihood of introducing errors as new lines get added to the body.
-->
制御構造の一般的なスタイルルールは下記の通りです。

- 制御構造の予約語の後ろにはスペースを入れなければなりません（MUST）
- 開き丸括弧の後ろにはスペースを入れてはなりません（MUST NOT）
- 閉じ丸括弧の前にはスペースを入れてはなりません（MUST NOT）
- 閉じ丸括弧と開き中括弧の間は一つのスペースでなければなりません（MUST）
- 制御本文はインデントを一つ入れなければなりません（MUST）
- 閉じ中括弧は本文最後の次の行になければなりません（MUST）

制御本文は中括弧で囲わなければなりません（MUST）。
この制御の見た目を揃えることで、新しい行が追加した際にエラーを引き起こす可能性を減らします。

<!--
### 5.1. `if`, `elseif`, `else`
-->
### 5.1. `if`・`elseif`・`else`

<!--
An `if` structure looks like the following.
Note the placement of parentheses,Bspaces, and braces; and that `else` and `elseif` are on the same line as the closing brace from the earlier body.
-->
`if`文は下記の様になります。
丸括弧・スペース・中括弧の位置、`else`と`elseif`は前の制御本文の閉じ中括弧と同じ行にあることに注意して下さい。

<!--
~~~php
<?php
if ($expr1) {
    // if body
} elseif ($expr2) {
    // elseif body
} else {
    // else body;
}
~~~
-->
~~~php
<?php
if ($expr1) {
    // if 本文
} elseif ($expr2) {
    // elseif 本文
} else {
    // else 本文
}
~~~

<!--
The keyword `elseif` SHOULD be used instead of `else if` so that all control keywords look like single words.
-->
一つの制御単語の様に見せるため、`else if`の代わりに`elseif`を使用するべきです（SHOULD）。

<!--
### 5.2. `switch`, `case`
-->
### 5.2. `switch`・`case`

<!--
A `switch` structure looks like the following. Note the placement of parentheses, spaces, and braces.
The `case` statement MUST be indented once from `switch`, and the `break` keyword (or other terminating keyword) MUST be indented at the same level as the `case` body.
There MUST be a comment such as `// no break` when fall-through is intentional in a non-empty `case` body.
-->
`switch`文は下記の様になります。
丸括弧、スペース、中括弧の配置に注意して下さい。
`case`文は`switch`よりも一つインデントを下げなければならず（MUST）、`break`（もしくはその他の終了キーワード）は`case`内本文と同じインデントでなければなりません（MUST）。
空ではない`case`内本文で意図的にフォールスルーする場合、`// no break`のようにコメントを記載しなければなりません（MUST）。

<!--
~~~php
<?php
switch ($expr) {
    case 0:
        echo 'First case, with a break';
        break;
    case 1:
        echo 'Second case, which falls through';
        // no break
    case 2:
    case 3:
    case 4:
        echo 'Third case, return instead of break';
        return;
    default:
        echo 'Default case';
        break;
}
~~~
-->
~~~php
<?php
switch ($expr) {
    case 0:
        echo 'First case, with a break';
        break;
    case 1:
        echo 'Second case, which falls through';
        // no break
    case 2:
    case 3:
    case 4:
        echo 'Third case, return instead of break';
        return;
    default:
        echo 'Default case';
        break;
}
~~~

<!--
### 5.3. `while`, `do while`
-->
### 5.3. `while`・`do while`

<!--
A `while` statement looks like the following.
Note the placement of parentheses, spaces, and braces.
-->
`while`文は下記の様になります。
丸括弧、スペース、中括弧の配置に注意して下さい。

<!--
~~~php
<?php
while ($expr) {
    // structure body
}
~~~
-->
~~~php
<?php
while ($expr) {
    // while 本文
}
~~~

<!--
Similarly, a `do while` statement looks like the following.
Note the placement of parentheses, spaces, and braces.
-->
同じ様に、`do while`文は下記の様になります。
丸括弧、スペース、中括弧の配置に注意して下さい。

<!--
~~~php
<?php
do {
    // structure body;
} while ($expr);
~~~
-->
~~~php
<?php
do {
    // do while 本文
} while ($expr);
~~~

<!--
### 5.4. `for`
-->
### 5.4. `for`

<!--
A `for` statement looks like the following.
Note the placement of parentheses, spaces, and braces.
-->
`for`文は下記の様になります。
丸括弧、スペース、中括弧の配置に注意して下さい。

<!--
~~~php
<?php
for ($i = 0; $i < 10; $i++) {
    // for body
}
~~~
-->
~~~php
<?php
for ($i = 0; $i < 10; $i++) {
    // for 本文
}
~~~

<!--
### 5.5. `foreach`
-->
### 5.5. `foreach`

<!--
A `foreach` statement looks like the following.
Note the placement of parentheses, spaces, and braces.
-->
`foreach`文は下記の様になります。
丸括弧、スペース、中括弧の配置に注意して下さい。

<!--
~~~php
<?php
foreach ($iterable as $key => $value) {
    // foreach body
}
~~~
-->
~~~php
<?php
foreach ($iterable as $key => $value) {
    // foreach 本文
}
~~~

<!--
### 5.6. `try`, `catch`
-->
### 5.6. `try`・`catch`

<!--
A `try catch` block looks like the following.
Note the placement of parentheses, spaces, and braces.
-->
`try catch`節は下記の様になります。
丸括弧、スペース、中括弧の配置に注意して下さい。

<!--
~~~php
<?php
try {
    // try body
} catch (FirstExceptionType $e) {
    // catch body
} catch (OtherExceptionType $e) {
    // catch body
}
~~~
-->
~~~php
<?php
try {
    // try 本文
} catch (FirstExceptionType $e) {
    // catch 本文
} catch (OtherExceptionType $e) {
    // catch 本文
}
~~~

<!--
## 6. Closures
-->
## 6. 無名関数

<!--
Closures MUST be declared with a space after the `function` keyword, and a space before and after the `use` keyword.

The opening brace MUST go on the same line, and the closing brace MUST go on the next line following the body.

There MUST NOT be a space after the opening parenthesis of the argument list or variable list, and there MUST NOT be a space before the closing parenthesis of the argument list or variable list.

In the argument list and variable list, there MUST NOT be a space before each comma, and there MUST be one space after each comma.

Closure arguments with default values MUST go at the end of the argument list.

A closure declaration looks like the following. Note the placement of parentheses, commas, spaces, and braces:
-->
無名関数の宣言は`function`の後にスペースを入れなければならず（MUST）、`use`の前後にもスペースを入れなければなりません（MUST）。

開き中括弧は同じ行に記載しなければならず（MUST）、閉じ中括弧は本文最後の次の行に記載しなければなりません（MUST）。

引数リストまたは変数リストの開き丸括弧の後にスペースを入れてはならず（MUST NOT）、引数リストまたは変数リストの閉じ丸括弧の前にもスペースを入れてはなりません（MUST NOT）。

引数リストもしくは変数リストのそれぞれのカンマの前にスペースを入れてはならず（MUST NOT）、それぞれのカンマの後にスペースを入れなければなりません（MUST）。

初期設定値のある引数は、引数リストの最後でなければなりません（MUST）。

クロージャの宣言は下記の様になります。
丸括弧、カンマ、スペース、中括弧の位置に注意して下さい。

<!--
<?php
$closureWithArgs = function ($arg1, $arg2) {
    // body
};

$closureWithArgsAndVars = function ($arg1, $arg2) use ($var1, $var2) {
    // body
};
-->
~~~php
<?php
$closureWithArgs = function ($arg1, $arg2) {
    // 本文
};

$closureWithArgsAndVars = function ($arg1, $arg2) use ($var1, $var2) {
    // 本文
};
~~~

<!--
Argument lists and variable lists MAY be split across multiple lines, where each subsequent line is indented once.
When doing so, the first item in the list MUST be on the next line, and there MUST be only one argument or variable per line.

When the ending list (whether of arguments or variables) is split across multiple lines, the closing parenthesis and opening brace MUST be placed together on their own line with one space between them.

The following are examples of closures with and without argument lists and variable lists split across multiple lines.
-->
引数リストと変数リストは複数の行に分割することができ（MAY）、それぞれ一つのインデントを入れなければなりません（MUST）。
分割した場合、リストの最初の項目は次の行に記載しなければならず（MUST）、一行ごとに一つの引数を記載しなければなりません（MUST）。

引数リストもしくは変数リストが複数行に分割された場合、閉じ丸括弧と開き中括弧はスペースを挟んで単独で同じ行に記載しなければなりません（MUST）。

下記は、引数リストと変数リストの有無や、複数行に分割された場合の、無名関数の例になります。

<!--
~~~php
<?php
$longArgs_noVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) {
    // body
};

$noArgs_longVars = function () use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // body
};

$longArgs_longVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // body
};

$longArgs_shortVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use ($var1) {
    // body
};

$shortArgs_longVars = function ($arg) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // body
};
~~~
-->
~~~php
<?php
$longArgs_noVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) {
    // 本文
};

$noArgs_longVars = function () use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // 本文
};

$longArgs_longVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // 本文
};

$longArgs_shortVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use ($var1) {
    // 本文
};

$shortArgs_longVars = function ($arg) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // 本文
};
~~~

<!--
Note that the formatting rules also apply when the closure is used directly in a function or method call as an argument.
-->
このルールは無名関数がメソッドの呼び出しの引数として直接使用される場合にも適用されることに注意して下さい。

<!--
<?php
$foo->bar(
    $arg1,
    function ($arg2) use ($var1) {
        // body
    },
    $arg3
);
-->
~~~php
<?php
$foo->bar(
    $arg1,
    function ($arg2) use ($var1) {
        // 本文
    },
    $arg3
);
~~~

<!--
## 7. Conclusion
-->
## 7. 最後に

<!--
There are many elements of style and practice intentionally omitted by this guide.
These include but are not limited to:

- Declaration of global variables and global constants

- Declaration of functions

- Operators and assignment

- Inter-line alignment

- Comments and documentation blocks

- Class name prefixes and suffixes

- Best practices

Future recommendations MAY revise and extend this guide to address those or
other elements of style and practice.
-->
このガイドでは意図的に省略された多くのスタイルや慣習があります。
これらが含むものは、次のものについては制限していません。

- グローバル変数と、グローバル定数の宣言について

- 関数の宣言について

- 演算子と代入について

- 行間の配置について

- コメントと、ドキュメントブロックに付いて

- クラス名の接頭語と接尾語について

- ベスト・プラクティスについて

将来他の推奨されるスタイルや慣習によって、このガイドは修正・拡張することができます（MAY）。


<!--
## Appendix A. Survey
-->
## 付録A. 調査

<!--
In writing this style guide, the group took a survey of member projects to determine common practices.
The survey is retained herein for posterity.
-->
このスタイルガイドを書く際に、プロジェクトのメンバーを調査し、一般的な慣習を定めました。
その調査をここに残します。

### A.1. 調査データ

    url,http://www.horde.org/apps/horde/docs/CODING_STANDARDS,http://pear.php.net/manual/en/standards.php,http://solarphp.com/manual/appendix-standards.style,http://framework.zend.com/manual/en/coding-standard.html,https://symfony.com/doc/2.0/contributing/code/standards.html,http://www.ppi.io/docs/coding-standards.html,https://github.com/ezsystems/ezp-next/wiki/codingstandards,http://book.cakephp.org/2.0/en/contributing/cakephp-coding-conventions.html,https://github.com/UnionOfRAD/lithium/wiki/Spec%3A-Coding,http://drupal.org/coding-standards,http://code.google.com/p/sabredav/,http://area51.phpbb.com/docs/31x/coding-guidelines.html,https://docs.google.com/a/zikula.org/document/edit?authkey=CPCU0Us&hgd=1&id=1fcqb93Sn-hR9c0mkN6m_tyWnmEvoswKBtSc0tKkZmJA,http://www.chisimba.com,n/a,https://github.com/Respect/project-info/blob/master/coding-standards-sample.php,n/a,Object Calisthenics for PHP,http://doc.nette.org/en/coding-standard,http://flow3.typo3.org,https://github.com/propelorm/Propel2/wiki/Coding-Standards,http://developer.joomla.org/coding-standards.html
    voting,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,no,no,no,?,yes,no,yes
    indent_type,4,4,4,4,4,tab,4,tab,tab,2,4,tab,4,4,4,4,4,4,tab,tab,4,tab
    line_length_limit_soft,75,75,75,75,no,85,120,120,80,80,80,no,100,80,80,?,?,120,80,120,no,150
    line_length_limit_hard,85,85,85,85,no,no,no,no,100,?,no,no,no,100,100,?,120,120,no,no,no,no
    class_names,studly,studly,studly,studly,studly,studly,studly,studly,studly,studly,studly,lower_under,studly,lower,studly,studly,studly,studly,?,studly,studly,studly
    class_brace_line,next,next,next,next,next,same,next,same,same,same,same,next,next,next,next,next,next,next,next,same,next,next
    constant_names,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper
    true_false_null,lower,lower,lower,lower,lower,lower,lower,lower,lower,upper,lower,lower,lower,upper,lower,lower,lower,lower,lower,upper,lower,lower
    method_names,camel,camel,camel,camel,camel,camel,camel,camel,camel,camel,camel,lower_under,camel,camel,camel,camel,camel,camel,camel,camel,camel,camel
    method_brace_line,next,next,next,next,next,same,next,same,same,same,same,next,next,same,next,next,next,next,next,same,next,next
    control_brace_line,same,same,same,same,same,same,next,same,same,same,same,next,same,same,next,same,same,same,same,same,same,next
    control_space_after,yes,yes,yes,yes,yes,no,yes,yes,yes,yes,no,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes
    always_use_control_braces,yes,yes,yes,yes,yes,yes,no,yes,yes,yes,no,yes,yes,yes,yes,no,yes,yes,yes,yes,yes,yes
    else_elseif_line,same,same,same,same,same,same,next,same,same,next,same,next,same,next,next,same,same,same,same,same,same,next
    case_break_indent_from_switch,0/1,0/1,0/1,1/2,1/2,1/2,1/2,1/1,1/1,1/2,1/2,1/1,1/2,1/2,1/2,1/2,1/2,1/2,0/1,1/1,1/2,1/2
    function_space_after,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no
    closing_php_tag_required,no,no,no,no,no,no,no,no,yes,no,no,no,no,yes,no,no,no,no,no,yes,no,no
    line_endings,LF,LF,LF,LF,LF,LF,LF,LF,?,LF,?,LF,LF,LF,LF,?,,LF,?,LF,LF,LF
    static_or_visibility_first,static,?,static,either,either,either,visibility,visibility,visibility,either,static,either,?,visibility,?,?,either,either,visibility,visibility,static,?
    control_space_parens,no,no,no,no,no,no,yes,no,no,no,no,no,no,yes,?,no,no,no,no,no,no,no
    blank_line_after_php,no,no,no,no,yes,no,no,no,no,yes,yes,no,no,yes,?,yes,yes,no,yes,no,yes,no
    class_method_control_brace,next/next/same,next/next/same,next/next/same,next/next/same,next/next/same,same/same/same,next/next/next,same/same/same,same/same/same,same/same/same,same/same/same,next/next/next,next/next/same,next/same/same,next/next/next,next/next/same,next/next/same,next/next/same,next/next/same,same/same/same,next/next/same,next/next/next

### A.2. 調査の凡例

`indent_type`:
The type of indenting. `tab` = "Use a tab", `2` or `4` = "number of spaces"

`line_length_limit_soft`:
The "soft" line length limit, in characters. `?` = not discernible or no response, `no` means no limit.

`line_length_limit_hard`:
The "hard" line length limit, in characters. `?` = not discernible or no response, `no` means no limit.

`class_names`:
How classes are named. `lower` = lowercase only, `lower_under` = lowercase with underscore separators, `studly` = StudlyCase.

`class_brace_line`:
Does the opening brace for a class go on the `same` line as the class keyword, or on the `next` line after it?

`constant_names`:
How are class constants named? `upper` = Uppercase with underscore separators.

`true_false_null`:
Are the `true`, `false`, and `null` keywords spelled as all `lower` case, or all `upper` case?

`method_names`:
How are methods named? `camel` = `camelCase`, `lower_under` = lowercase with underscore separators.

`method_brace_line`:
Does the opening brace for a method go on the `same` line as the method name, or on the `next` line?

`control_brace_line`:
Does the opening brace for a control structure go on the `same` line, or on the `next` line?

`control_space_after`:
Is there a space after the control structure keyword?

`always_use_control_braces`:
Do control structures always use braces?

`else_elseif_line`:
When using `else` or `elseif`, does it go on the `same` line as the previous closing brace, or does it go on the `next` line?

`case_break_indent_from_switch`:
How many times are `case` and `break` indented from an opening `switch` statement?

`function_space_after`:
Do function calls have a space after the function name and before the opening parenthesis?

`closing_php_tag_required`:
In files containing only PHP, is the closing `?>` tag required?

`line_endings`:
What type of line ending is used?

`static_or_visibility_first`:
When declaring a method, does `static` come first, or does the visibility come first?

`control_space_parens`:
In a control structure expression, is there a space after the opening parenthesis and a space before the closing parenthesis? `yes` = `if ( $expr )`, `no` = `if ($expr)`.

`blank_line_after_php`:
Is there a blank line after the opening PHP tag?

`class_method_control_brace`:
A summary of what line the opening braces go on for classes, methods, and control structures.

### A.3. 調査結果

    indent_type:
        tab: 7
        2: 1
        4: 14
    line_length_limit_soft:
        ?: 2
        no: 3
        75: 4
        80: 6
        85: 1
        100: 1
        120: 4
        150: 1
    line_length_limit_hard:
        ?: 2
        no: 11
        85: 4
        100: 3
        120: 2
    class_names:
        ?: 1
        lower: 1
        lower_under: 1
        studly: 19
    class_brace_line:
        next: 16
        same: 6
    constant_names:
        upper: 22
    true_false_null:
        lower: 19
        upper: 3
    method_names:
        camel: 21
        lower_under: 1
    method_brace_line:
        next: 15
        same: 7
    control_brace_line:
        next: 4
        same: 18
    control_space_after:
        no: 2
        yes: 20
    always_use_control_braces:
        no: 3
        yes: 19
    else_elseif_line:
        next: 6
        same: 16
    case_break_indent_from_switch:
        0/1: 4
        1/1: 4
        1/2: 14
    function_space_after:
        no: 22
    closing_php_tag_required:
        no: 19
        yes: 3
    line_endings:
        ?: 5
        LF: 17
    static_or_visibility_first:
        ?: 5
        either: 7
        static: 4
        visibility: 6
    control_space_parens:
        ?: 1
        no: 19
        yes: 2
    blank_line_after_php:
        ?: 1
        no: 13
        yes: 8
    class_method_control_brace:
        next/next/next: 4
        next/next/same: 11
        next/same/same: 1
        same/same/same: 6
