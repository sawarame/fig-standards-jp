[RFC 2119]: http://tools.ietf.org/html/rfc2119
[オートローディング]: http://php.net/autoload
[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[実装例]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader-examples.md
[原文]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md


# オートローダー

この文書に記載されているキーワード「**しなければならない**（MUST）」、「**してはならない**（MUST NOT）」、「**必須**（REQUIRED）」、「**するものとする**（SHALL）」、「**しないものとする**（SHALL NOT）」、「**すべきである**（SHOULD）」、「**すべきでない**（SHOULD NOT）」、「**推奨する**（RECOMMENDED）」、「**することができる**（MAY）」、「**任意**（OPTIONAL）」は、[RFC 2119][]に記載されている内容で解釈されます。


## 1. 概要

このPSRは、ファイルパスからクラスを[オートローディング][]するための仕様を説明しています。
[PSR-0][]を含む、その他のオートローディングの仕様に追加して完全に相互運用可能なものです。
また、仕様に従ってオートロードされるファイルの場所についても説明しています。


## 2. 仕様

1. 「クラス」と言う用語は、クラス、インターフェース、トレイト、および他の同様の構造のことを指す。

2. 完全修飾クラス名は下記の形式になる。

        \<NamespaceName>(\<SubNamespaceNames>)*\<ClassName>
    
    1. 完全修飾クラス名には、トップレベル名前空間（ベンダー名前空間）を**持たなければならない**（MUST）。

    2. 完全修飾クラス名は一つ以上のサブ名前空間を**持つことができる**（MAY）。

    3. 完全修飾クラス名は終端クラス名を**持たなければならない**（MUST）。

    4. アンダースコアは完全修飾クラス名のどの部分にも特別な意味は持たない。

    5. 完全修飾クラス名は大文字・小文字の組み合わせを使用**することができる**（MAY）。
    
    6. 大文字小文字を区別してすべてのクラスを参照**しなければならない**（MUST）。

3. 完全修飾クラス名に対応するファイルをロードする場合

    1. 完全修飾クラス名の名前空間接頭語(先頭の区切り文字を含まない名前空間の連続)はベースディレクトリに対応する。

    2. 名前空間接頭語の後に続くサブ名前空間は、ベースディレクトリ内のサブディレクトリに対応し、名前空間の区切り文字はディレクトリの区切り文字を表す。また、サブディレクトリ名はサブ名前空間の大文字・小文字と一致**しなければならない**（MUST）。

    3. 終端クラス名は`.php`で終わるファイル名に対応する。ファイル名は終端クラス名の大文字・小文字と一致**しなければならない**（MUST）。

4. オートローダーの実装は、例外をスロー**してはならず**（MUST NOT）、どのレベルのエラーも発生**してはならない**（MUST NOT）。また値を返**すべきでない**（SHOULD NOT）。


## 3. 例

下記の表は、指定された完全修飾クラス名、名前空間接頭語、ベースディレクトリに対応するファイルパスを表す。

| 完全修飾クラス名 | 名前空有間接頭語 | ベースディレクトリ | ファイルパス |
|-|-|-|-|
| \Acme\Log\Writer\File_Writer  | Acme\Log\Writer    | ./acme-log-writer/lib/   | ./acme-log-writer/lib/File_Writer.php
| \Aura\Web\Response\Status     | Aura\Web           | /path/to/aura-web/src/   | /path/to/aura-web/src/Response/Status.php
| \Symfony\Core\Request         | Symfony\Core       | ./vendor/Symfony/Core/   | ./vendor/Symfony/Core/Request.php
| \Zend\Acl                     | Zend               | /usr/includes/Zend/      | /usr/includes/Zend/Acl.php

仕様に準拠したオートローダーの実装例については[実装例][]を参照。この実装例は仕様の一部とみな**してはならない**（MUST NOT）。そしていつでも変更**することができる**（MAY）。


[原文][]
