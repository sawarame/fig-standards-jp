[RFC 2119]: http://tools.ietf.org/html/rfc2119
[RFC 5424]: http://tools.ietf.org/html/rfc5424

このページはPSR-3 Logger Interfaceを日本語訳したものです。  
原文：https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md

ロガーインターフェース
==================

このドキュメントはロギングライブラリの共通のインターフェースを説明します。

主な目的はライブラリが`Psr\Log\LoggerInterface`オブジェクトを受け入れ、シンプルかつ普遍的なログを書き込むことができるようにすることです。

カスタマイズを必要とするフレームワークとCMSは独自の方法でインターフェースを拡張することが出来ますが（MAY）、このドキュメントへの互換性は残すべきです（SHOULD）。
これはアプリケーションが使用するライブラリが、集中してログを書き込むことを確実にするためです。

この文書に記載されているキーワード「しなければならない（MUST）」、「してはならない（MUST NOT）」、「必須（REQUIRED）」、「するものとする（SHALL）」、「しないものとする（SHALL NOT）」、「すべきである（SHOULD）」、「すべきでない（SHOULD NOT）」、「推奨する（RECOMMENDED）」、「することができる（MAY）」、「任意（OPTIONAL）」は、[RFC 2119][]に記載されている内容で解釈されます。

このドキュメントでいう`実装者`はログ関連のライブラリもしくはフレームワークに`LoggerInterface`を実装している人を指します。
`ユーザー`はロガーを使用する人を指します。


## 1. 仕様

### 1.1 基本

- `LoggerInterface`は8つの[RFC 5424][]レベル(debug, info, notice, warning, error, critical, alert, emergency)でログを書き込むメソッドを公開します。

- 9つ目のメソッドの`log`は最初の引数にログレベルを受け取ります。
  このメソッドをログレベルの定数を使用して呼び出すと、各レベルのメソッドを呼び出すのと同じ結果を返さなければなりません(MUST) 。
  仕様で定義されていないレベルでこのメソッドを呼出したばあい、例外`Psr\Log\InvalidArgumentException`をスローしなければなりません(MUST) 。
  ユーザーは現在の実装がカスタムレベルをサポートしているかどうか確認なしに利用するべきではありません（SHOULD NOT）。


### 1.2 メッセージ

- すべてのメソッドはメッセージとして文字列を受け取るか、`__toString()`メソッドを持つオブジェクトを受け取ります。
  実装者は渡されたオブジェクトに対して特別な処理を行うことが出来ます（MAY）。
  そうでない場合、実装者は文字列にキャストしなければいけません（MUST）。

- メッセージは実装者がコンテキスト配列で置き換えることのできるプレースホルダを含むことができます（MAY）。

  プレースホルダの名前はコンテキスト配列のキーに対応していなければなりません（MUST）。

  プレースホルダ名は開き中括弧`{`と閉じ中括弧`}`で区切られていなければなりません（MUST）。
  区切り文字(中括弧)とプレースホルダ名との間にスペースが含まれていてはなりません（MUST NOT）。

  プレースホルダ名は`A-Z`、`a-z`、`0-9`、アンダースコア`_`、ピリオド`.`のみで構成されるべきです（SHOULD）。
  他の文字を使用することは、将来のプレースホルダの仕様変更のため予約されています。

  実装者はプレースホルダに様々なエスケープ処理を実装し、ログを表示用に変換することができます（MAY）。
  ユーザーはどのコンテキストでデータが表示されるか知ることが出来ないため、事前にプレースホルダ値をエスケープすべきではありません（SHOULD NOT）。

  以下は参照目的のみのプレースホルダの補間を行う実装例です。

  ~~~php
  <?php

  /**
   * メッセージのプレースホルダにコンテキストの値を補間します
   */
  function interpolate($message, array $context = array())
  {
      // コンテキストのキーを中括弧で囲った置換用の配列を作成する
      $replace = array();
      foreach ($context as $key => $val) {
          // 値が文字列にキャスト可能かチェックする
          if (!is_array($val) && (!is_object($val) || method_exists($val, '__toString'))) {
              $replace['{' . $key . '}'] = $val;
          }
      }

      // 置換する値をメッセージの中に保管して返す
      return strtr($message, $replace);
  }

  // 中括弧で区切ったプレースホルダ名を持つメッセージ
  $message = "User {username} created";

  // プレースホルダ名 => 置換値 の形式のコンテキスト配列
  $context = array('username' => 'bolivar');

  // "User bolivar created" が出力される
  echo interpolate($message, $context);
  ~~~

### 1.3 コンテキスト

- すべてのメソッドはコンテキストデータとして配列を受け取ります。
  これは文字列にうまく収まらない、外部の情報を保持するためのものです。
  配列には何でも含めることが出来ます。
  実装者はできるだけ柔軟にコンテキストデータを確実に扱わなければならない（MUST）。
  コンテキスト内に与えられた値は例外を投げたり、PHPエラーや警告や通知を上げてはならない（MUST NOT）。

- コンテキストデータに`Exception`を入れる場合は、'`exeption`'キーを使用しなければいけません（MUST）。
  ログの例外は共通のパターンで、ログのバックエンドがサポートする場合は、実装者が例外からスタックトレースを抽出できるようにします。
  コンテキストデータには何でも含むことができるので（MAY）、実装者は`'exception'`キーが実際に`Exception`であることを使用する前に確認しなければなりません（MUST）。

### 1.4 ヘルパークラスとインターフェース

- `Psr\Log\AbstractLogger`を継承して汎用の`log`メソッドを実装することで、`LoggerInterface`を非常に簡単に実装できるようになります。
  `Psr\Log\AbstractLogger`は8つのレベルのメソッドのメッセージとコンテキストを`log`メソッドに転送するようになっています。
  
- 同様に`Psr\Log\LoggerTrait`を使用する場合も汎用の`log`メソッドを実装する必要があります。
  なお、トレイトはインターフェースを実装出来ないので、この場合は更に別途`LoggerInterface`を実装しなければなりません。

- `Psr\Log\NullLogger`はインターフェースとともに提供されます。
  ロガーが与えられていない場合にフォールバック「ブラックホール」の実装をインターフェースのユーザーに提供し、ユーザーはそれを使用することができます（MAY）。
  しかしながら、コンテキストデータの生成が重要な場合は、条件付きのロギングを使用することが、より良いアプローチかもしれません。

- `Psr\Log\LoggerAwareInterface`は`setLogger(LoggerInterface $logger)`メソッドのみを含み、任意ロガーのインスタンスを自動設定するフレームワークで使用することができます。

- `Psr\Log\LoggerAwareTrait`を使用して、どのクラスでも同等のインターフェースを簡単に実装することが出来ます。
  `$this->logger`でロガーにアクセスできます。

- `Psr\Log\LogLevel`クラスは8つのログレベル定義を持っています。
  

## 2. パッケージ

説明したインターフェースとクラス、関連性する例外クラス、実装を検証するためのテストスイートは[psr/log](https://packagist.org/packages/psr/log)パッケージの一部として提供されています。

## 3. `Psr\Log\LoggerInterface`

~~~php
<?php

namespace Psr\Log;

/**
 * loggerインターフェース
 *
 * メッセージは文字列か__toString()メソッドを実装しているオブジェクトでなければなりません（MUST）
 *
 * メッセージは{foo}形式のプレースホルダを持つことができ（MAY）、キーが"foo"のコンテキスト
 * データに置換されます。
 *
 * コンテキスト配列はどんなデータを含むことができますが、唯一想定できることは実装者が与えられた
 * 例外からスタックトレースを取り出す場合に、コンテキストデータのキーの名称を"exception"に
 * しなければならない（MUST）ということです。
 * 
 * すべての仕様は下記を確認して下さい
 * https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md
 */
interface LoggerInterface
{
    /**
     * システムが使用できない状態。
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function emergency($message, array $context = array());

    /**
     * 即時対応を起こさなければ行けない状態
     *
     * 例：ウェブサイト全体がダウンしている、データベースが使用できない等
     * SMSアラートで目をさますべき。
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function alert($message, array $context = array());

    /**
     * 深刻な状態
     *
     * 例：アプリケーションのコンポーネントが使用できない、予想しない例外が発生した場合等
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function critical($message, array $context = array());

    /**
     * 即時対応は必要ないが、ログに記録し監視するべきランタイムエラー
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function error($message, array $context = array());

    /**
     * エラーでは無い例外が発生
     *
     * 例：廃止予定のAPIの使用や、APIの誤用、間違っているわけではないが望ましくないもの
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function warning($message, array $context = array());

    /**
     * 正常だが重要なイベント
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function notice($message, array $context = array());

    /**
     * 確認するイベント
     *
     * 例：ユーザーのログインやSQLのログなど
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function info($message, array $context = array());

    /**
     * デバッグの詳細な情報
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function debug($message, array $context = array());

    /**
     * 任意のレベルのログを出力
     *
     * @param mixed $level
     * @param string $message
     * @param array $context
     * @return void
     */
    public function log($level, $message, array $context = array());
}
~~~

## 4. `Psr\Log\LoggerAwareInterface`

~~~php
<?php

namespace Psr\Log;

/**
 * logger-awareインターフェース
 */
interface LoggerAwareInterface
{
    /**
     * オブジェクトにロガーを設定する
     *
     * @param LoggerInterface $logger
     * @return void
     */
    public function setLogger(LoggerInterface $logger);
}
~~~

## 5. `Psr\Log\LogLevel`

~~~php
<?php

namespace Psr\Log;

/**
 * ログレベル定数
 */
class LogLevel
{
    const EMERGENCY = 'emergency';
    const ALERT     = 'alert';
    const CRITICAL  = 'critical';
    const ERROR     = 'error';
    const WARNING   = 'warning';
    const NOTICE    = 'notice';
    const INFO      = 'info';
    const DEBUG     = 'debug';
}
~~~
