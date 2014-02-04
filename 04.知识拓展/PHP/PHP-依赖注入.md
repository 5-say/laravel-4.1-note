# PHP 依赖注入

> **来源：** [blog.sina.com.cn/s/blog_7141dace0100lopb.html](http://blog.sina.com.cn/s/blog_7141dace0100lopb.html)

## 简介

PHP Dependency Injection 简称 DI。
依赖注入是对于要求更易维护，更易测试，更加模块化的代码的解决方案。

## 什么是依赖

每个项目都有依赖（外界提供的输入）, 项目越复杂，越需要更多的依赖。在现今的网络应用程序中，最常见的依赖是数据库，其风险在于，一旦数据库暂停运行，那么整个程序也因此而停止运行。这是因为代码依赖数据库服务器……这已足够。因为数据库服务器有时会崩溃，而弃用它是荒谬的。尽管依赖有其自身的瑕疵，却仍然存在代码中，因为它使程序开发人员的工作更加轻松。

## 不使用依赖注入的弊端

对于大多依赖而言，问题在于代码如何处理它们及与它们交互。也就是说，问题是代码本身而非依赖。如果你没有使用依赖注入，将有机会遇到下列代码：

    class Book {
        public function __construct()
        {
            $registry = RegistrySingleton::getInstance();
            $this->_databaseConnection = $registry->databaseConnection;
            // or
            global $databaseConnection;
            $this->_databaseConnection = $databaseConnection;
        }
    }

现在对象 book 一旦建立，就已经完全连到数据库。这没什么不好，book 需要跟数据库交互并取得数据。问题在于 book 取得其接入的方法。要使 book 能够与数据库交互，代码必须有一个外来的变量 $databaseConnect，更糟糕的是，它必须有个 singleton 类型类（registry）的对象，其包含一个 databaseConnection 的记录。如果这些不存在的话，book 会无法运行，这就使得这些代码远远不够模块化。

这就提出一个问题，到底 book 如何接入数据库？这就是 IoC 出现的原因了。

## 依赖注入的解决方案 IoC

在好莱坞，一个濒临绝境的演员不会打电话给 Martin Scoresese 要求在他的下个影片中扮演一个角色。绝不会这样，实际正相反。Martin Scorese 会打电话给这个困顿的演员并邀请他在下一部影片中扮演重要的角色。对象是挣扎中的演员，他们不会去捡自己所扮演的角色，导演需要告诉他们去做什么。对象不会到它所接触的外界挑选系统，相反，外界系统会被赋予对象。记住这就是 IoC（Inversion of Control）控制反转。

如下是开发人员告诉他的对象如何与外界的依赖打交道：

    class Book {
        publicfunction __construct(){}
        publicfunction setDatabaseConnection($databaseConnection)
        {
              $this->_databaseConnection = $databaseConnection;
        }
    }
    $book = new Book();
    $book->setDatabase($databaseConnection);

这代码允许这个 book 类能在任何的网页程序中使用。此 book 不再依赖任何事项，除了程序开发者要在对象创建后立即要提供一个数据库。

这就是最佳方法——依赖注入。

## 两种常用的依赖注入的方式

### constructor 注入

注：构造函数。这种译法似乎并不恰当，类中此方法更多是用来对某些属性进行初始化

Constructor 注入涉及到将所有依赖作为参数，传递给新创建的对象。看起来像这样：

    $book = new Book($databaseConnection, $configFile);

### setter 注入

对象的依赖越多，construction 就会变得越杂乱。还有一些其他的原因使得它成为一个不好的方法，包括代码重用和 constructors 要做的工作。

这就使我们不得不采用其他的方法进行依赖注入，我们称其为设置注入。在需要注入依赖的对象里创建一个公共方法：

    $book = new Book();
    $book->setDatabase($databaseConnection);
    $book->setConfigFile($configFile);

这很容易实现，但是它会导致为您的程序写大量代码。当创建一个book对象时，需要三行代码。如果我们必须注入另外的依赖时，必须增加第四行代码。这很快也变得一团糟。

### 工厂类

解决以上问题的对策是使用一个工厂 factory，它是一个类，用来创建然后注入一个对象的所有依赖。举例如下：

    class Factory {
        public static $_database;
        public static function makeBook()
        {
            $book = new Book();
            $book->setDatabase(self::$_database);
            // more injection...（更多注入）
            return $book;
        }
     }

然后：

    $book = Factory::makeBook();

所有的依赖在运行期间都应在此 factory 对象中注册。它现在是大门，在依赖与任何对象打交道前，都必经此门。

makeBook 作为一个静态方法的原因是易用并且可以在全局得到。在本文开头，我提出使用 singleton 类型和全局变量对代码而言是一个糟糕的选择。多数情形下，确实如此！当他们控制接入时，是一个差设计。但是在他们控制创建时，是一个不错的选择。makeBook 方法仅仅是创建的一个捷径。在 book 类和 factory 类之间，没有任何依赖。由于 factory 类的存在，我们的依赖可以存放在一个地点，并且用一行创建代码就可自动注入这些依赖。

此处的 factory 或者容器类移走了依赖注入的所有额外工作。

以前的注入：

    $book = new Book();

目前的方法：

    $book= Factory::makeBook();

几乎没有任何的额外工作，但是有大量益处。

当运行测试代码时，尤其是单元测试，其目标是检查类中的方法是否正确地工作。由于 book 类要求接入数据库并且读取图书数据，它增加了一层复杂度。此测试不得不进行数据库连接，取得数据，然后测试它。瞬间，测试不再是测试 book 类中的单个方法，它目前同时也测试数据库。如果数据库离线，测试就会失败。这已经远离了单元测试的目的。

解决上述问题的方法是用不同的数据库依赖来作单元测试。当测试套件开始时，虚拟的数据库被注入 book。虚拟数据库将永远拥有开发人员所期待的数据。如果使用一个真实数据库，数据的潜在变化会导致测试不必要的失败。当数据库的记录改变了，不需因此重新进行单元测试。

此代码现在更加模块化，因为它可以被放到任何其他的网页程序。创建 book 对象并且用 $book->setDatabase() 注入数据库连接。此时，数据库在 Registery::Database、$database 或者 $someRandomDatabaseVarible 都无关大局。只要有一个数据库连接，book就可以在任何系统内工作。

代码更加易维护，因为每个对象都被赋予了它的所需。如果同一类的实例需要不同的数据库连接，在类内部一点儿也不需要额外的代码。例如 book1 连接到 数据库1，book2 连接到 数据库2
 
    Factory::$_database=$ourDatabaseVarForDB1;
    $book1= Factory::makeBook();
    $book2= Factory::makeBook();
    $book2->setDatabase($database2);

依赖注入真的是更易维护，更易测试和更加模块化的最佳解决方案。