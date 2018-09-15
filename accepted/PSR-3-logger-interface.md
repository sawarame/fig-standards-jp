このページはPSR-3 Logger Interfaceを日本語訳したものです。  
原文：https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md

<!--
Logger Interface
================
-->
ロガーインターフェース
==================

<!--
This document describes a common interface for logging libraries.
-->
このドキュメントはロギングライブラリの共通のインターフェースを説明します。

<!--
The main goal is to allow libraries to receive a `Psr\Log\LoggerInterface` object and write logs to it in a simple and universal way.
Frameworks and CMSs that have custom needs MAY extend the interface for their own purpose, but SHOULD remain compatible with this document.
This ensures that the third-party libraries an application uses can write to the centralized application logs.
-->
主な目的はライブラリが`Psr\Log\LoggerInterface`オブジェクトを受け入れ、シンプルかつ普遍的な方法でログを書き込むことができるようにすることです。
カスタマイズを必要とするフレームワークとCMSは独自の方法でインターフェースを拡張することが出来ますが（MAY）、このドキュメントへの互換性は残すべきです（SHOULD）。
これによりアプリケーションが使用するライブラリが、ログを一元管理できるようになります。

<!--
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][].
-->
この文書に記載されているキーワード「しなければならない（MUST）」、「してはならない（MUST NOT）」、「必須（REQUIRED）」、「するものとする（SHALL）」、「しないものとする（SHALL NOT）」、「すべきである（SHOULD）」、「すべきでない（SHOULD NOT）」、「推奨する（RECOMMENDED）」、「することができる（MAY）」、「任意（OPTIONAL）」は、[RFC 2119](http://tools.ietf.org/html/rfc2119)に記載されている内容で解釈されます。

<!--
The word `implementor` in this document is to be interpreted as someone implementing the `LoggerInterface` in a log-related library or framework.
Users of loggers are referred to as `user`.
-->
このドキュメントでいう`実装者`はログ関連のライブラリもしくはフレームワークに`LoggerInterface`を実装している人を指します。
`ユーザー`はロガーを使用する人を指します。

<!--
## 1. Specification
-->
## 1. 仕様

<!--
### 1.1 Basics
-->
### 1.1 基本

<!--
- The `LoggerInterface` exposes eight methods to write logs to the eight [RFC 5424][] levels (debug, info, notice, warning, error, critical, alert, emergency).

- A ninth method, `log`, accepts a log level as the first argument. 
  Calling this method with one of the log level constants MUST have the same result as calling the level-specific method.
  Calling this method with a level not defined by this specification MUST throw a `Psr\Log\InvalidArgumentException` if the implementation does not know about the level.
  Users SHOULD NOT use a custom level without knowing for sure the current implementation supports it.
-->

- `LoggerInterface`は8つの[RFC 5424](http://tools.ietf.org/html/rfc5424)レベル(debug, info, notice, warning, error, critical, alert, emergency)でログを書き込むメソッドを公開します。

- 9つ目のメソッドの`log`は最初の引数にログレベルを受け取ります。
  このメソッドをログレベルの定数を使用して呼び出すと、各レベルのメソッドを呼び出すのと同じ結果を返さなければなりません(MUST) 。
  仕様で定義されていないレベルでこのメソッドが呼出された場合、例外`Psr\Log\InvalidArgumentException`をスローしなければなりません(MUST) 。
  ユーザーは現在の実装がカスタムレベルをサポートしているかどうか確認なしに利用するべきではありません（SHOULD NOT）。

<!--
### 1.2 Message
-->
### 1.2 メッセージ

<!--
- Every method accepts a string as the message, or an object with a `__toString()` method.
  Implementors MAY have special handling for the passed objects. If that is not the case, implementors MUST cast it to a string.

- The message MAY contain placeholders which implementors MAY replace with values from the context array.

  Placeholder names MUST correspond to keys in the context array.

  Placeholder names MUST be delimited with a single opening brace `{` and a single closing brace `}`.
  There MUST NOT be any whitespace between the delimiters and the placeholder name.

  Placeholder names SHOULD be composed only of the characters `A-Z`, `a-z`, `0-9`, underscore `_`, and period `.`.
  The use of other characters is reserved for future modifications of the placeholders specification.

  Implementors MAY use placeholders to implement various escaping strategies and translate logs for display.
  Users SHOULD NOT pre-escape placeholder values since they can not know in which context the data will be displayed.

  The following is an example implementation of placeholder interpolation provided for reference purposes only:

  ~~~php
  <?php

  /**
   * Interpolates context values into the message placeholders.
   */
  function interpolate($message, array $context = array())
  {
      // build a replacement array with braces around the context keys
      $replace = array();
      foreach ($context as $key => $val) {
          // check that the value can be casted to string
          if (!is_array($val) && (!is_object($val) || method_exists($val, '__toString'))) {
              $replace['{' . $key . '}'] = $val;
          }
      }

      // interpolate replacement values into the message and return
      return strtr($message, $replace);
  }

  // a message with brace-delimited placeholder names
  $message = "User {username} created";

  // a context array of placeholder names => replacement values
  $context = array('username' => 'bolivar');

  // echoes "User bolivar created"
  echo interpolate($message, $context);
  ~~~
-->
- すべてのメソッドはメッセージとして文字列を受け取るか、`__toString()`メソッドを持つオブジェクトを受け取ります。
  実装者は渡されたオブジェクトに対して特別な処理を行うことが出来ます（MAY）。
  そうでない場合、実装者は文字列にキャストしなければいけません（MUST）。

- メッセージは実装者がコンテキスト配列で置き換えることのできるプレースホルダを含むことができます（MAY）。

  プレースホルダの名前はコンテキスト配列のキーに対応していなければなりません（MUST）。

  プレースホルダ名は開き中括弧`{`と閉じ中括弧`}`で区切られていなければなりません（MUST）。
  区切り文字(中括弧)とプレースホルダ名との間にスペースが含まれていてはなりません（MUST NOT）。

  プレースホルダ名は`A-Z`、`a-z`、`0-9`、アンダースコア`_`、ピリオド`.`のみで構成されるべきです（SHOULD）。
  他の文字を使用することは、将来のプレースホルダの仕様変更のため予約されています。

  実装者はプレースホルダを使用して様々なエスケープ処理を実装し、ログを表示用に変換することができます（MAY）。
  ユーザーはどの状況でデータが表示されるか知ることが出来ないため、事前にプレースホルダ値をエスケープすべきではありません（SHOULD NOT）。

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

<!--
### 1.3 Context
-->
### 1.3 コンテキスト

<!--
- Every method accepts an array as context data.
  This is meant to hold any extraneous information that does not fit well in a string.
  The array can contain anything. 
  Implementors MUST ensure they treat context data with as much lenience as possible.
  A given value in the context MUST NOT throw an exception nor raise any php error, warning or notice.

- If an `Exception` object is passed in the context data, it MUST be in the `'exception'` key.
  Logging exceptions is a common pattern and this allows implementors to extract a stack trace from the exception when the log backend supports it.
  Implementors MUST still verify that the `'exception'` key is actually an `Exception` before using it as such, as it MAY contain anything.
-->
- すべてのメソッドはコンテキストデータとして配列を受け取ります。
  これは文字列にうまく収まらない、外部の情報を保持するためのものです。
  配列には何でも含めることが出来ます。
  実装者はできるだけ柔軟にコンテキストデータを確実に扱わなければなりません（MUST）。
  コンテキスト内に与えられた値によって例外を投げたり、PHPエラーや警告や通知を上げてはなりません（MUST NOT）。

- コンテキストデータに`Exception`を入れる場合は、'`exeption`'キーを使用しなければいけません（MUST）。
  ログの例外は共通のパターンで、ログのバックエンドがサポートする場合は、実装者が例外からスタックトレースを抽出できるようにします。
  コンテキストデータには何でも含むことができるので（MAY）、実装者は`'exception'`キーが実際に`Exception`であることを使用する前に確認しなければなりません（MUST）。

<!--
### 1.4 Helper classes and interfaces
-->
### 1.4 ヘルパークラスとインターフェース

<!--
- The `Psr\Log\AbstractLogger` class lets you implement the `LoggerInterface` very easily by extending it and implementing the generic `log` method.
  The other eight methods are forwarding the message and context to it.

- Similarly, using the `Psr\Log\LoggerTrait` only requires you to implement the generic `log` method.
  Note that since traits can not implement interfaces, in this case you still have to implement `LoggerInterface`.

- The `Psr\Log\NullLogger` is provided together with the interface.
  It MAY be used by users of the interface to provide a fall-back "black hole" implementation if no logger is given to them.
  However, conditional logging may be a better approach if context data creation is expensive.

- The `Psr\Log\LoggerAwareInterface` only contains a `setLogger(LoggerInterface $logger)` method and can be used by frameworks to auto-wire arbitrary instances with a logger.

- The `Psr\Log\LoggerAwareTrait` trait can be used to implement the equivalent interface easily in any class.
  It gives you access to `$this->logger`.

- The `Psr\Log\LogLevel` class holds constants for the eight log levels.
-->
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

<!--
## 2. Package
-->
## 2. パッケージ

<!--
The interfaces and classes described as well as relevant exception classes and a test suite to verify your implementation are provided as part of the [psr/log](https://packagist.org/packages/psr/log) package.
-->
説明したインターフェースとクラス、関連性する例外クラス、実装を検証するためのテストスイートは[psr/log](https://packagist.org/packages/psr/log)パッケージの一部として提供されています。

<!--
## 3. `Psr\Log\LoggerInterface`
-->
## 3. `Psr\Log\LoggerInterface`

<!--
<?php

namespace Psr\Log;

/**
 * Describes a logger instance
 *
 * The message MUST be a string or object implementing __toString().
 *
 * The message MAY contain placeholders in the form: {foo} where foo
 * will be replaced by the context data in key "foo".
 *
 * The context array can contain arbitrary data, the only assumption that
 * can be made by implementors is that if an Exception instance is given
 * to produce a stack trace, it MUST be in a key named "exception".
 *
 * See https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md
 * for the full interface specification.
 */
interface LoggerInterface
{
    /**
     * System is unusable.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function emergency($message, array $context = array());

    /**
     * Action must be taken immediately.
     *
     * Example: Entire website down, database unavailable, etc. This should
     * trigger the SMS alerts and wake you up.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function alert($message, array $context = array());

    /**
     * Critical conditions.
     *
     * Example: Application component unavailable, unexpected exception.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function critical($message, array $context = array());

    /**
     * Runtime errors that do not require immediate action but should typically
     * be logged and monitored.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function error($message, array $context = array());

    /**
     * Exceptional occurrences that are not errors.
     *
     * Example: Use of deprecated APIs, poor use of an API, undesirable things
     * that are not necessarily wrong.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function warning($message, array $context = array());

    /**
     * Normal but significant events.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function notice($message, array $context = array());

    /**
     * Interesting events.
     *
     * Example: User logs in, SQL logs.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function info($message, array $context = array());

    /**
     * Detailed debug information.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function debug($message, array $context = array());

    /**
     * Logs with an arbitrary level.
     *
     * @param mixed $level
     * @param string $message
     * @param array $context
     * @return void
     */
    public function log($level, $message, array $context = array());
}
-->

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

<!--
## 4. `Psr\Log\LoggerAwareInterface`
-->
## 4. `Psr\Log\LoggerAwareInterface`

<!--
~~~php
<?php

namespace Psr\Log;

/**
 * Describes a logger-aware instance
 */
interface LoggerAwareInterface
{
    /**
     * Sets a logger instance on the object
     *
     * @param LoggerInterface $logger
     * @return void
     */
    public function setLogger(LoggerInterface $logger);
}
~~~
-->
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

<!--
## 5. `Psr\Log\LogLevel`
-->
## 5. `Psr\Log\LogLevel`

<!--
~~~php
<?php

namespace Psr\Log;

/**
 * Describes log levels
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
-->
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
