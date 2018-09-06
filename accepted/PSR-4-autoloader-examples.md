このページはExample Implementations of PSR-4を日本語訳したものです。  
原文：https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader-examples.md


PSR-4 実装例
================================

PSR-4に準拠したコードの例：

クロージャの例
---------------

~~~php
<?php
/**
 * プロジェクト固有の実装例
 * 
 * このオートロード関数をSPLに登録すると、下記の行は\Foo\Bar\Baz\Quxクラスを
 * /path/to/project/src/Baz/Qux.phpファイルから読み込もうとします。
 *
 *      new \Foo\Bar\Baz\Qux;
 *
 * @param string $class The fully-qualified class name.
 * @return void
 */
spl_autoload_register(function ($class) {

    // プロジェクト固有の名前空間接頭語
    $prefix = 'Foo\\Bar\\';

    // 名前空間接頭語に対応するベースディレクトリ
    $base_dir = __DIR__ . '/src/';

    // クラスが名前空間接頭語を使用しているかどうか
    $len = strlen($prefix);
    if (strncmp($prefix, $class, $len) !== 0) {
        // 使用していなければ次のオートローダーに任せる
        return;
    }

    // 相対クラス名の取得
    $relative_class = substr($class, $len);

    // 名前空有間接頭語をベースディレクトリに置換して、相対クラス名の名前空間区切り文字を
    // ディレクトリ区切り文字に置換し、「.php」を追加
    $file = $base_dir . str_replace('\\', '/', $relative_class) . '.php';

    // ファイルが存在したら読み込む
    if (file_exists($file)) {
        require $file;
    }
});
~~~

クラスの例
-------------

下記は複数の名前空間を扱うためのクラスの実装例です

~~~php
<?php
namespace Example;

/**
 * 単一の名前空間接頭語で複数のベースディレクトリを許可する、オプション機能を含んだ汎用的な実装の例
 *
 * 次のパスにfoo-barパッケージクラスがある場合、
 *
 *     /path/to/packages/foo-bar/
 *         src/
 *             Baz.php             # Foo\Bar\Baz
 *             Qux/
 *                 Quux.php        # Foo\Bar\Qux\Quux
 *         tests/
 *             BazTest.php         # Foo\Bar\BazTest
 *             Qux/
 *                 QuuxTest.php    # Foo\Bar\Qux\QuuxTest
 *
 * 下記の様に\Foo\Bar名前空間接頭語のクラスファイルへのパスを追加します。
 *
 *      <?php
 *      // ローダーのインスタンス化
 *      $loader = new \Example\Psr4AutoloaderClass;
 *
 *      // オートローダーを登録
 *      $loader->register();
 *
 *      // 名前空間接頭語のベースディレクトリを登録
 *      $loader->addNamespace('Foo\Bar', '/path/to/packages/foo-bar/src');
 *      $loader->addNamespace('Foo\Bar', '/path/to/packages/foo-bar/tests');
 *
 * 下記の行は\Foo\Bar\Qux\Quuxクラスを/path/to/packages/foo-bar/src/Qux/Quux.php
 * ファイルから読み込もうとします。
 *
 *      <?php
 *      new \Foo\Bar\Qux\Quux;
 *
 * 下記の行は\Foo\Bar\Qux\QuuxTestクラスを/path/to/packages/foo-bar/tests/Qux/QuuxTest.php
 * ファイルから読み込もうとします。
 *
 *      <?php
 *      new \Foo\Bar\Qux\QuuxTest;
 */
class Psr4AutoloaderClass
{
    /**
     * キーが名前空間接頭語で値がそのベースディレクトリの配列になっている連想配列
     *
     * @var array
     */
    protected $prefixes = array();

    /**
     * クラスローダーをSPLオートローダースタックに登録します
     *
     * @return void
     */
    public function register()
    {
        spl_autoload_register(array($this, 'loadClass'));
    }

    /**
     * 名前空間接頭語のベースディレクトリを登録します
     *
     * @param string $prefix 名前空有間接頭語
     * @param string $base_dir 名前空間内のクラスファイルのベースディレクトリ
     * @param bool $prepend trueにすると、スタックの最後に追加するのではなく、最初に追加
     * します。これによって、最後に検索されるのではなく最初に検索される様になります。
     * @return void
     */
    public function addNamespace($prefix, $base_dir, $prepend = false)
    {
        // 名前空間接頭語の正規化
        $prefix = trim($prefix, '\\') . '\\';

        // 末尾に区切り文字をつけて、ベースディレクトリを正規化
        $base_dir = rtrim($base_dir, DIRECTORY_SEPARATOR) . '/';

        // 名前空間接頭語の配列を初期化
        if (isset($this->prefixes[$prefix]) === false) {
            $this->prefixes[$prefix] = array();
        }

        // 名前空間接頭語のベースディレクトリを保持する
        if ($prepend) {
            array_unshift($this->prefixes[$prefix], $base_dir);
        } else {
            array_push($this->prefixes[$prefix], $base_dir);
        }
    }

    /**
     * 指定されたクラス名からクラスファイルを読み込む
     *
     * @param string $class 完全修飾クラス名
     * @return mixed 成功した場合は割り当てられたファイル名、 失敗した場合はfalse
     */
    public function loadClass($class)
    {
        // 現在の名前空間接頭語を保持する
        $prefix = $class;

        // 割り当てられたファイル名を探すため、完全修飾クラス名の名前空間を後方から処理する
        while (false !== $pos = strrpos($prefix, '\\')) {

            // 名前空間接頭語の最後の区切り文字までを保持
            $prefix = substr($class, 0, $pos + 1);

            // 残りは相対クラス名とする
            $relative_class = substr($class, $pos + 1);

            // 名前空間接頭語と相対クラス名から、割り当てられたファイルの読み込みを試みます
            $mapped_file = $this->loadMappedFile($prefix, $relative_class);
            if ($mapped_file) {
                return $mapped_file;
            }

            // 次の反復のstrrpos()のため、末尾の区切り文字を削除する。
            $prefix = rtrim($prefix, '\\');
        }

        // 割り当てられたファイルが見つからなかった場合
        return false;
    }

    /**
     * 名前空間接頭語と相対クラス名から、割り当てられたファイルをを読み込む
     *
     * @param string $prefix 名前空間接頭語
     * @param string $relative_class 相対クラス名
     * @return mixed 割り当てられてファイルの読み込みが出来なければfalse、読み込まれた場合は
     * 読み込んだファイル名
     */
    protected function loadMappedFile($prefix, $relative_class)
    {
        // 名前空間接頭語に対するベースディレクトリが存在するかどうか
        if (isset($this->prefixes[$prefix]) === false) {
            return false;
        }

        // 名前空間接頭語のベースディレクトリを調べる
        foreach ($this->prefixes[$prefix] as $base_dir) {

            // 名前空間接頭語をベースディレクトリに置き換え、相対クラス名の名前空間の区切り文字
            // をディレクトリの区切り文字に置換し、末尾に.phpをつける
            $file = $base_dir
                  . str_replace('\\', '/', $relative_class)
                  . '.php';

            // 割り当てられたファイルが見つかった場合、読み込む
            if ($this->requireFile($file)) {
                // 読み込み完了
                return $file;
            }
        }

        // 割り当てられたファイルが見つからなかった場合
        return false;
    }

    /**
     * ファイルが存在した場合は、ファイルシステムからファイルを読み込みます。
     *
     * @param string $file 読み込むファイル
     * @return bool ファイルが存在した場合はtrue、そうでなければfalse
     */
    protected function requireFile($file)
    {
        if (file_exists($file)) {
            require $file;
            return true;
        }
        return false;
    }
}
~~~

### 単体テスト

次の例は上記クラスローダーの単体テストを行う一つの方法です

~~~php
<?php
namespace Example\Tests;

class MockPsr4AutoloaderClass extends Psr4AutoloaderClass
{
    protected $files = array();

    public function setFiles(array $files)
    {
        $this->files = $files;
    }

    protected function requireFile($file)
    {
        return in_array($file, $this->files);
    }
}

class Psr4AutoloaderClassTest extends \PHPUnit_Framework_TestCase
{
    protected $loader;

    protected function setUp()
    {
        $this->loader = new MockPsr4AutoloaderClass;

        $this->loader->setFiles(array(
            '/vendor/foo.bar/src/ClassName.php',
            '/vendor/foo.bar/src/DoomClassName.php',
            '/vendor/foo.bar/tests/ClassNameTest.php',
            '/vendor/foo.bardoom/src/ClassName.php',
            '/vendor/foo.bar.baz.dib/src/ClassName.php',
            '/vendor/foo.bar.baz.dib.zim.gir/src/ClassName.php',
        ));

        $this->loader->addNamespace(
            'Foo\Bar',
            '/vendor/foo.bar/src'
        );

        $this->loader->addNamespace(
            'Foo\Bar',
            '/vendor/foo.bar/tests'
        );

        $this->loader->addNamespace(
            'Foo\BarDoom',
            '/vendor/foo.bardoom/src'
        );

        $this->loader->addNamespace(
            'Foo\Bar\Baz\Dib',
            '/vendor/foo.bar.baz.dib/src'
        );

        $this->loader->addNamespace(
            'Foo\Bar\Baz\Dib\Zim\Gir',
            '/vendor/foo.bar.baz.dib.zim.gir/src'
        );
    }

    public function testExistingFile()
    {
        $actual = $this->loader->loadClass('Foo\Bar\ClassName');
        $expect = '/vendor/foo.bar/src/ClassName.php';
        $this->assertSame($expect, $actual);

        $actual = $this->loader->loadClass('Foo\Bar\ClassNameTest');
        $expect = '/vendor/foo.bar/tests/ClassNameTest.php';
        $this->assertSame($expect, $actual);
    }

    public function testMissingFile()
    {
        $actual = $this->loader->loadClass('No_Vendor\No_Package\NoClass');
        $this->assertFalse($actual);
    }

    public function testDeepFile()
    {
        $actual = $this->loader->loadClass('Foo\Bar\Baz\Dib\Zim\Gir\ClassName');
        $expect = '/vendor/foo.bar.baz.dib.zim.gir/src/ClassName.php';
        $this->assertSame($expect, $actual);
    }

    public function testConfusion()
    {
        $actual = $this->loader->loadClass('Foo\Bar\DoomClassName');
        $expect = '/vendor/foo.bar/src/DoomClassName.php';
        $this->assertSame($expect, $actual);

        $actual = $this->loader->loadClass('Foo\BarDoom\ClassName');
        $expect = '/vendor/foo.bardoom/src/ClassName.php';
        $this->assertSame($expect, $actual);
    }
}
~~~
