

このページはPSR-0 Autoloading Standardを日本語訳したものです。  
原文：https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md

[PSR-4]: http://www.php-fig.org/psr/psr-4/

オートローディング規格
====================

> **廃止予定** - 2014年10月21日現在、PSR-0は廃止予定となりました、 代わりに現在は[PSR-4][]が推奨されいています。

次にオートローダーの相互運用について守らなければならない必須要件について説明します。

必須要件
---------

* 完全修飾名前空間とクラス名は次の構造を持たなければなりません。`\<Vendor Name>\(<Namespace>\)*<Class Name>`
* どの名前空間もトップレベルに「ベンダー名」を持たなければなりません。
* どの名前空間も好きな数だけサブ名前空間を持つことが出来ます。
* 名前空間の区切り文字はファイルシステムからロードする際に`DIRECTORY_SEPARATOR`(ディレクトリの区切り文字)に変換されます。
* クラス名に含まれる`_`(アンダースコア)は、`DIRECTORY_SEPARATOR`に変換されます。名前空間に含まれる`_`は特別な意味を持ちません。
* 完全修飾名前空間とクラス名はファイルシステムかロードする際に`.php`が後置されます。
* ベンダー名・名前空間・クラス名は、大文字・小文字の組み合わせのアルファベットが使用できます。


例
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`

名前空間とクラス名のアンダースコアの例
-----------------------------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

ここで定めた規格はオートローダー相互運用に影響の少ない最低限の共通項であるべきです。
PHP 5.3クラスをロードできるSplClassLoaderの実装のサンプルを利用して、これらの規格に準拠していることをテスト出来ます。

実装例
----------------------

下記は、上記で提案した規格がどの様にオートロードされているか、簡単に示す関数の例です。

~~~php
<?php

function autoload($className)
{
    $className = ltrim($className, '\\');
    $fileName  = '';
    $namespace = '';
    if ($lastNsPos = strrpos($className, '\\')) {
        $namespace = substr($className, 0, $lastNsPos);
        $className = substr($className, $lastNsPos + 1);
        $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
    }
    $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

    require $fileName;
}
spl_autoload_register('autoload');
~~~

SplClassLoaderの実装
-----------------------------

次のgistは上記提案されたオートローダーの相互運用の規格に準拠したクラスをロードできる、SplClassLoaderの実装例です。
これらの規格に準拠するＰＨＰ5.3のクラスをロードする現時点で推奨される方法です。

* [http://gist.github.com/221634](http://gist.github.com/221634)
