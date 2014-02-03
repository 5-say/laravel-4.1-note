# Laravel 4 调试工具栏

> 项目地址：[github.com/barryvdh/laravel-debugbar](https://github.com/barryvdh/laravel-debugbar)

## 简介

这个资源包为 Laravel 整合了 [PHP Debug Bar](https://github.com/maximebf/php-debugbar) 。它引入一个服务提供者来注册调试工具栏，并将其附加到你的页面输出中。你可以通过 Laravel 来发布它的资源和配置文件。它引导一些收集器为 Laravel 工作，并针对 Laravel 实现了特殊的自定义 DataCollecters。它被配置为能够显示重定向和 AJAX 请求（若存在，则在右上方的下拉框中显示）。

![屏幕截图](/img/laravel-4-debugbar/GVc6C9g.png)

> **注意：** 仅在开发环境中使用 DebugBar。它会减缓应用程序的速度（因为它会进行数据搜集）。因此当遇到运行缓慢的情况，请尝试禁用一部分收集器。

这个资源包包含了一些定制的收集器：

收集器|作用
-|-
RouteCollector|显示当前路由的信息。
ViewCollector|显示当前加载的视图（可选：显示视图共享数据）。
EventsCollector|显示所有的事件。
LaravelCollector|显示 Laravel 的版本和运行环境（默认情况下禁用）。
SymfonyRequestCollector|取代 RequestCollector 以获取更多请求与响应信息。
LogsCollector|显示日志文件中最新的日志信息（默认情况下禁用）。
FilesCollector|显示 PHP 中 included/required 的文件（默认情况下禁用）。
ConfigCollector|显示配置文件的返回值（默认情况下禁用）。

Bootstraps the following collectors for Laravel:

收集器|作用
-|-
LogCollector|显示所有的日志信息。
PdoCollector|显示数据库的查询与绑定信息。
TwigCollector|For extra Twig info with barryvdh/laravel-twigbridge
SwiftMailCollector|for Mail
SwiftLogCollector|for Mail

默认收集器：

- PhpInfoCollector
- MessagesCollector
- TimeDataCollector (With Booting and Application timing)
- MemoryCollector
- ExceptionsCollector

它还提供了一个 Facade 接口，使你能够更容易的记录信息、异常、运行时间。

## 安装

在你的 composer.json 中 Require 这个资源包，并运行 `composer update` 命令（或者直接执行 `composer require barryvdh/laravel-debugbar:dev-master` 命令）：

    "barryvdh/laravel-debugbar": "1.*"

添加 ServiceProvider 到 `app/config/app.php` 文件的 providers 数组中。

    'Barryvdh\Debugbar\ServiceProvider',

你需要发布这个包的静态资源。

    $ php artisan debugbar:publish

注意：发布的静态资源有可能过时（因为上游项目的变更），建议每次更新后都重新执行发布命令。你也可以在 composer.json 文件中添加“自动重新发布”的命令。

    "post-update-cmd": [
        "php artisan debugbar:publish"
    ],

如果你已经设置 `app.debug=true` 那么它将默认启动。你可以在配置文件中覆盖它。You can also set in your config if you want to include the vendor files also (FontAwesome and jQuery). If you already use them in your site, set it to false.
You can also only display the js of css vendors, by setting it to 'js' or 'css'.

    $ php artisan config:publish barryvdh/laravel-debugbar

你可以禁用或启用你想要的收集器，你也可以通过 IOC 容器来添加额外的收集器（`$app['debugbar']->addCollector(new MyDataCollector)`）。

如果你想要通过 Facade 来记录消息，请在 `app.php` 文件的 aliases 数组中添加下面的信息：

     'Debugbar' => 'Barryvdh\Debugbar\Facade',

现在你就可以使用 Facade 来添加消息了，它遵循 PSR-3 levels (debug, info, notice, warning, error, critical, alert, emergency)：

    Debugbar::info($object);
    Debugbar::error("Error!");
    Debugbar::warning('Watch out..');
    Debugbar::addMessage('Another message', 'mylabel');

并且记录 开始/结束 耗时：

    Debugbar::startMeasure('render','Time for rendering');
    Debugbar::stopMeasure('render');
    Debugbar::addMeasure('now', LARAVEL_START, microtime(true));
    Debugbar::measure('My long operation', function() {
        //Do something..
    });

或者记录异常：

    try {
        throw new Exception('foobar');
    } catch (Exception $e) {
        Debugbar::addException($e);
    }

如果你想添加自定义的 DataCollectors，可以通过 Facade 或 IOC 容器 来完成：

    Debugbar::addCollector(new DebugBar\DataCollector\MessagesCollector('my_messages'));
    //Or via the App container:
    $debugbar = App::make('debugbar');
    $debugbar->addCollector(new DebugBar\DataCollector\MessagesCollector('my_messages'));

默认情况下 Debugbar 只会在 `</body>` 标签前注入。If you want to inject the Debugbar yourself,
set the config option 'inject' to false and use the renderer yourself and follow http://phpdebugbar.com/docs/rendering.html

    $renderer = Debugbar::getJavascriptRenderer();

Note: Not using the auto-inject, will disable the Request information, because that is added After the response.
You can add the default_request datacollector in the config as alternative.

## 在运行时临时 启用/禁用 调试工具栏

你可以在运行时临时 启用 或 禁用 调试工具栏

    \Debugbar::enable();
    \Debugbar::disable();

注意：一旦启用，收集器就将被加载（并且可能产生额外的开销），因此如果你要在生产环境下使用，请在配置中将其禁用，仅在需要的时候临时启用它。
