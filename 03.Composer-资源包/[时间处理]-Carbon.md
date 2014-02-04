# Carbon

> 项目地址 [github.com/briannesbitt/Carbon](https://github.com/briannesbitt/Carbon)

**注意：**Laravel 默认已经引入了此依赖，具体调用方法是直接使用 `Carbon/Carbon`。此外，Eloquent 中的 timestamp 类型字段默认返回的也是 Carbon 实例。

PHP 5.3+ DateTime 的一个简单 API 拓展

    printf("Right now is %s", Carbon::now()->toDateTimeString());
    printf("Right now in Vancouver is %s", Carbon::now('America/Vancouver'));  //implicit __toString()
    $tomorrow = Carbon::now()->addDay();
    $lastWeek = Carbon::now()->subWeek();
    $nextSummerOlympics = Carbon::createFromDate(2012)->addYears(4);
    
    $officialDate = Carbon::now()->toRFC2822String();
    
    $howOldAmI = Carbon::createFromDate(1975, 5, 21)->age;
    
    $noonTodayLondonTime = Carbon::createFromTime(12, 0, 0, 'Europe/London');
    
    $worldWillEnd = Carbon::createFromDate(2012, 12, 21, 'GMT');
    
    // Don't really want to die so mock now
    Carbon::setTestNow(Carbon::createFromDate(2000, 1, 1));
    
    // comparisons are always done in UTC
    if (Carbon::now()->gte($worldWillEnd)) {
       die();
    }
    
    // Phew! Return to normal behaviour
    Carbon::setTestNow();
    
    if (Carbon::now()->isWeekend()) {
       echo 'Party!';
    }
    echo Carbon::now()->subMinutes(2)->diffForHumans(); // '2 minutes ago'
    
    // ... but also does 'from now', 'after' and 'before'
    // rolling up to seconds, minutes, hours, days, months, years
    
    $daysSinceEpoch = Carbon::createFromTimeStamp(0)->diffInDays();

## 安装

### 环境要求

- PHP 版本 5.3+ 
- [可选] PHPUnit 测试套件

### 在 Composer 中使用

Carbon 最简单的安装方法就是通过 [composer](http://getcomposer.org/) 来进行安装。创建如下 `composer.json` 文件并运行 `php composer.phar install` 命令来完成安装。

    {
        "require": {
            "nesbot/Carbon": "*"
        }
    }

在您的项目中引入 Composer 提供的 `autoload.php` 进行类文件的自动载入，而后按照如下方法进行调用。

    <?php
    require 'vendor/autoload.php';
    use Carbon\Carbon;
    printf("Now: %s", Carbon::now());

### 非 Composer 环境下的使用方法

下载 [Carbon.php](https://github.com/briannesbitt/Carbon/blob/master/Carbon/Carbon.php) 将其保存到您的项目中，进行常规的类文件调用。

    <?php
    require 'path/to/Carbon.php';
    use Carbon\Carbon;
    printf("Now: %s", Carbon::now());

## API

Carbon 类 [继承](http://php.net/manual/zh/keyword.extends.php) 了 PHP 原生 [DateTime](http://www.php.net/manual/zh/class.datetime.php) 类。

    <?php
    class Carbon extends \DateTime
    {
        // code here
    }

Carbon 继承了基础 DateTime 类的所有方法。这种方式允许你访问基类的方法，如果您在任何用例中看到 Carbon 中并未定义的方法，那么它就存在于 DateTime 中。

采取特别谨慎的措施，以确保时区的正确处理，并在适当的时候基于 DateTime 的相关方法执行。例如所有的比较操作都是基于 UTC 或正在使用的 datetime 时区进行处理。

    $dtToronto = Carbon::createFromDate(2012, 1, 1, 'America/Toronto');
    $dtVancouver = Carbon::createFromDate(2012, 1, 1, 'America/Vancouver');
    
    echo $dtVancouver->diffInHours($dtToronto); // 3

另外 `is` 系列方法，是在 Carbon 实例所提供的时区内进行比较。比如我目前所在的时区相对 Tokyo 有 -13 小时的时差，若按照常规的思路，我所在的位置任何早于下午1点的时间执行 `Carbon::now('Asia/Tokyo')->isToday()` 都将返回 false，但这是没有意义的，因为 `now()` 在 Tokyo 执行时应该总是返回 Tokyo 当天的时间。因此将采用与当前实例相同的时区来进行比较操作，而这个例子正确的返回值为 true。

### 实例化

There are several different methods available to create a new instance of Carbon.  First there is a constructor.  It overrides the [parent constructor](http://www.php.net/manual/en/datetime.construct.php) and you are best to read about the first parameter from the PHP manual and understand the date/time string formats it accepts.  You'll hopefully find yourself rarely using the constructor but rather relying on the explicit static methods for improved readability.

    $carbon = new Carbon();                  // equivalent to Carbon::now()
    $carbon = new Carbon('first day of January 2008', 'America/Vancouver');
    echo get_class($carbon);                 // 'Carbon\Carbon'

You'll notice above that the timezone (2nd) parameter was passed as a string rather than a `\DateTimeZone` instance. All DateTimeZone parameters have been augmented so you can pass a DateTimeZone instance or a string and the timezone will be created for you.  This is again shown in the next example which also introduces the `now()` function.

    $now = Carbon::now();
    $nowInLondonTz = Carbon::now(new DateTimeZone('Europe/London'));
    // or just pass the timezone as a string
    $nowInLondonTz = Carbon::now('Europe/London');

If you really love your fluid method calls and get frustrated by the extra line or ugly pair of brackets necessary when using the constructor you'll enjoy the `parse` method.

    echo (new Carbon('first day of December 2008'))->addWeeks(2);     // 2008-12-15 00:00:00
    echo Carbon::parse('first day of December 2008')->addWeeks(2);    // 2008-12-15 00:00:00

To accompany `now()`, a few other static instantiation helpers exist to create widely known instances.  The only thing to really notice here is that `today()`, `tomorrow()` and `yesterday()`, besides behaving as expected, all accept a timezone parameter and each has their time value set to `00:00:00`.

    $now = Carbon::now();
    echo $now;                               // 2014-01-06 22:52:03
    $today = Carbon::today();
    echo $today;                             // 2014-01-06 00:00:00
    $tomorrow = Carbon::tomorrow('Europe/London');
    echo $tomorrow;                          // 2014-01-08 00:00:00
    $yesterday = Carbon::yesterday();
    echo $yesterday;                         // 2014-01-05 00:00:00

The next group of static helpers are the `createXXX()` helpers. Most of the static `create` functions allow you to provide as many or as few arguments as you want and will provide default values for all others.  Generally default values are the current date, time or timezone.  Higher values will wrap appropriately but invalid values will throw an `InvalidArgumentException` with an informative message.  The message is obtained from an [DateTime::getLastErrors()](http://php.net/manual/en/datetime.getlasterrors.php) call.

    Carbon::createFromDate($year, $month, $day, $tz);
    Carbon::createFromTime($hour, $minute, $second, $tz);
    Carbon::create($year, $month, $day, $hour, $minute, $second, $tz);

`createFromDate()` will default the time to now.  `createFromTime()` will default the date to today. `create()` will default any null parameter to the current respective value. As before, the `$tz` defaults to the current timezone and otherwise can be a DateTimeZone instance or simply a string timezone value.  The only special case for default values (mimicking the underlying PHP library) occurs when an hour value is specified but no minutes or seconds, they will get defaulted to 0.

    $xmasThisYear = Carbon::createFromDate(null, 12, 25);  // Year defaults to current year
    $Y2K = Carbon::create(2000, 1, 1, 0, 0, 0);
    $alsoY2K = Carbon::create(1999, 12, 31, 24);
    $noonLondonTz = Carbon::createFromTime(12, 0, 0, 'Europe/London');
    
    // A two digit minute could not be found
    try { Carbon::create(1975, 5, 21, 22, -2, 0); } catch(InvalidArgumentException $x) { echo $x->getMessage(); }

    Carbon::createFromFormat($format, $time, $tz);

`createFromFormat()` is mostly a wrapper for the base php function [DateTime::createFromFormat](http://php.net/manual/en/datetime.createfromformat.php).  The difference being again the `$tz` argument can be a DateTimeZone instance or a string timezone value.  Also, if there are errors with the format this function will call the `DateTime::getLastErrors()` method and then throw a `InvalidArgumentException` with the errors as the message.  If you look at the source for the `createXX()` functions above, they all make a call to `createFromFormat()`.

    echo Carbon::createFromFormat('Y-m-d H', '1975-05-21 22')->toDateTimeString(); // 1975-05-21 22:00:00

The final two create functions are for working with [unix timestamps](http://en.wikipedia.org/wiki/Unix_time).  The first will create a Carbon instance equal to the given timestamp and will set the timezone as well or default it to the current timezone.  The second, `createFromTimestampUTC()`, is different in that the timezone will remain UTC (GMT).  The second acts the same as `Carbon::createFromFormat('@'.$timestamp)` but I have just made it a little more explicit.  Negative timestamps are also allowed.

    echo Carbon::createFromTimeStamp(-1)->toDateTimeString();                        // 1969-12-31 18:59:59
    echo Carbon::createFromTimeStamp(-1, 'Europe/London')->toDateTimeString();       // 1970-01-01 00:59:59
    echo Carbon::createFromTimeStampUTC(-1)->toDateTimeString();                     // 1969-12-31 23:59:59

You can also create a `copy()` of an existing Carbon instance.  As expected the date, time and timezone values are all copied to the new instance.

    $dt = Carbon::now();
    echo $dt->diffInYears($dt->copy()->addYear());  // 1
    
    // $dt was unchanged and still holds the value of Carbon:now()

Finally, if you find yourself inheriting a `\DateTime` instance from another library, fear not!  You can create a `Carbon` instance via a friendly `instance()` function.

    $dt = new \DateTime('first day of January 2008'); // <== instance from another API
    $carbon = Carbon::instance($dt);
    echo get_class($carbon);                               // 'Carbon\Carbon'
    echo $carbon->toDateTimeString();                      // 2008-01-01 00:00:00

### 测试助手

The testing methods allow you to set a Carbon instance (real or mock) to be returned when a "now" instance is created.  The provided instance will be returned specifically under the following conditions:
- A call to the static now() method, ex. Carbon::now()
- When a null (or blank string) is passed to the constructor or parse(), ex. new Carbon(null)
- When the string "now" is passed to the constructor or parse(), ex. new Carbon('now')

    $knownDate = Carbon::create(2001, 5, 21, 12);          // create testing date
    Carbon::setTestNow($knownDate);                        // set the mock (of course this could be a real mock object)
    echo Carbon::now();                                    // 2001-05-21 12:00:00
    echo new Carbon();                                     // 2001-05-21 12:00:00
    echo Carbon::parse();                                  // 2001-05-21 12:00:00
    echo new Carbon('now');                                // 2001-05-21 12:00:00
    echo Carbon::parse('now');                             // 2001-05-21 12:00:00
    var_dump(Carbon::hasTestNow());                        // bool(true)
    Carbon::setTestNow();                                  // clear the mock
    var_dump(Carbon::hasTestNow());                        // bool(false)
    echo Carbon::now();                                    // 2014-01-06 22:52:03

A more meaning full example:

    class SeasonalProduct
    {
        protected $price;
    
        public function __construct($price)
        {
            $this->price = $price;
        }
    
        public function getPrice() {
            $multiplier = 1;
            if (Carbon::now()->month == 12) {
                $multiplier = 2;
            }
    
            return $this->price * $multiplier;
        }
    }
    
    $product = new SeasonalProduct(100);
    Carbon::setTestNow(Carbon::parse('first day of March 2000'));
    echo $product->getPrice();                                             // 100
    Carbon::setTestNow(Carbon::parse('first day of December 2000'));
    echo $product->getPrice();                                             // 200
    Carbon::setTestNow(Carbon::parse('first day of May 2000'));
    echo $product->getPrice();                                             // 100
    Carbon::setTestNow();

Relative phrases are also mocked according to the given "now" instance.

    $knownDate = Carbon::create(2001, 5, 21, 12);          // create testing date
    Carbon::setTestNow($knownDate);                        // set the mock
    echo new Carbon('tomorrow');                           // 2001-05-22 00:00:00
    echo new Carbon('yesterday');                          // 2001-05-20 00:00:00
    echo new Carbon('next wednesday');                     // 2001-05-23 00:00:00
    echo new Carbon('last friday');                        // 2001-05-18 00:00:00
    echo new Carbon('this thursday');                      // 2001-05-24 00:00:00
    Carbon::setTestNow();                                  // always clear it !

The list of words that are considered to be relative modifiers are:
- this
- next
- last
- tomorrow
- yesterday
- +
- -
- first
- last
- ago

Be aware that similar to the next(), previous() and modify() methods some of these relative modifiers will set the time to 00:00:00.

### 属性获取

The getters are implemented via PHP's `__get()` method.  This enables you to access the value as if it was a property rather than a function call.

    $dt = Carbon::create(2012, 9, 5, 23, 26, 11);
    
    // These getters specifically return integers, ie intval()
    var_dump($dt->year);                                         // int(2012)
    var_dump($dt->month);                                        // int(9)
    var_dump($dt->day);                                          // int(5)
    var_dump($dt->hour);                                         // int(23)
    var_dump($dt->minute);                                       // int(26)
    var_dump($dt->second);                                       // int(11)
    var_dump($dt->dayOfWeek);                                    // int(3)
    var_dump($dt->dayOfYear);                                    // int(248)
    var_dump($dt->weekOfYear);                                   // int(36)
    var_dump($dt->daysInMonth);                                  // int(30)
    var_dump($dt->timestamp);                                    // int(1346901971)
    var_dump(Carbon::createFromDate(1975, 5, 21)->age);          // int(38) calculated vs now in the same tz
    var_dump($dt->quarter);                                      // int(3)
    
    // Returns an int of seconds difference from UTC (+/- sign included)
    var_dump(Carbon::createFromTimestampUTC(0)->offset);         // int(0)
    var_dump(Carbon::createFromTimestamp(0)->offset);            // int(-18000)
    
    // Returns an int of hours difference from UTC (+/- sign included)
    var_dump(Carbon::createFromTimestamp(0)->offsetHours);       // int(-5)
    
    // Indicates if day light savings time is on
    var_dump(Carbon::createFromDate(2012, 1, 1)->dst);           // bool(false)
    var_dump(Carbon::createFromDate(2012, 9, 1)->dst);           // bool(true)
    
    // Indicates if the instance is in the same timezone as the local timzezone
    var_dump(Carbon::now()->local);                              // bool(true)
    var_dump(Carbon::now('America/Vancouver')->local);           // bool(false)
    
    // Indicates if the instance is in the UTC timezone
    var_dump(Carbon::now()->utc);                                // bool(false)
    var_dump(Carbon::now('Europe/London')->utc);                 // bool(true)
    var_dump(Carbon::createFromTimestampUTC(0)->utc);            // bool(true)
    
    // Gets the DateTimeZone instance
    echo get_class(Carbon::now()->timezone);                     // DateTimeZone
    echo get_class(Carbon::now()->tz);                           // DateTimeZone
    
    // Gets the DateTimeZone instance name, shortcut for ->timezone->getName()
    echo Carbon::now()->timezoneName;                            // America/Toronto
    echo Carbon::now()->tzName;                                  // America/Toronto

### 属性赋值

The following setters are implemented via PHP's `__set()` method.  Its good to take note here that none of the setters, with the obvious exception of explicitly setting the timezone, will change the timezone of the instance.  Specifically, setting the timestamp will not set the corresponding timezone to UTC.

    $dt = Carbon::now();
    
    $dt->year = 1975;
    $dt->month = 13;             // would force year++ and month = 1
    $dt->month = 5;
    $dt->day = 21;
    $dt->hour = 22;
    $dt->minute = 32;
    $dt->second = 5;
    
    $dt->timestamp = 169957925;  // This will not change the timezone
    
    // Set the timezone via DateTimeZone instance or string
    $dt->timezone = new DateTimeZone('Europe/London');
    $dt->timezone = 'Europe/London';
    $dt->tz = 'Europe/London';

### 链式调用

No arguments are optional for the setters, but there are enough variety in the function definitions that you shouldn't need them anyway.  Its good to take note here that none of the setters, with the obvious exception of explicitly setting the timezone, will change the timezone of the instance.  Specifically, setting the timestamp will not set the corresponding timezone to UTC.

    $dt = Carbon::now();
    
    $dt->year(1975)->month(5)->day(21)->hour(22)->minute(32)->second(5)->toDateTimeString();
    $dt->setDate(1975, 5, 21)->setTime(22, 32, 5)->toDateTimeString();
    $dt->setDateTime(1975, 5, 21, 22, 32, 5)->toDateTimeString();
    
    $dt->timestamp(169957925)->timezone('Europe/London');
    
    $dt->tz('America/Toronto')->setTimezone('America/Vancouver');

### IsSet

The PHP function `__isset()` is implemented.  This was done as some external systems (ex. [Twig](http://twig.sensiolabs.org/doc/recipes.html#using-dynamic-object-properties)) validate the existence of a property before using it.  This is done using the `isset()` or `empty()` method.  You can read more about these on the PHP site: [__isset()](http://www.php.net/manual/en/language.oop5.overloading.php#object.isset), [isset()](http://www.php.net/manual/en/function.isset.php), [empty()](http://www.php.net/manual/en/function.empty.php).

    var_dump(isset(Carbon::now()->iDoNotExist));       // bool(false)
    var_dump(isset(Carbon::now()->hour));              // bool(true)
    var_dump(empty(Carbon::now()->iDoNotExist));       // bool(true)
    var_dump(empty(Carbon::now()->year));              // bool(false)

### 字符串格式 和 本地化

All of the available `toXXXString()` methods rely on the base class method [DateTime::format()](http://php.net/manual/en/datetime.format.php).  You'll notice the `__toString()` method is defined which allows a Carbon instance to be printed as a pretty date time string when used in a string context.

    $dt = Carbon::create(1975, 12, 25, 14, 15, 16);
    
    var_dump($dt->toDateTimeString() == $dt);          // bool(true) => uses __toString()
    echo $dt->toDateString();                          // 1975-12-25
    echo $dt->toFormattedDateString();                 // Dec 25, 1975
    echo $dt->toTimeString();                          // 14:15:16
    echo $dt->toDateTimeString();                      // 1975-12-25 14:15:16
    echo $dt->toDayDateTimeString();                   // Thu, Dec 25, 1975 2:15 PM
    
    // ... of course format() is still available
    echo $dt->format('l jS \\of F Y h:i:s A');         // Thursday 25th of December 1975 02:15:16 PM

You can also set the default __toString() format (which defaults to `Y-m-d H:i:s`) thats used when [type juggling](http://php.net/manual/en/language.types.type-juggling.php) occurs.

    Carbon::setToStringFormat('jS \o\f F, Y g:i:s a');
    echo $dt;                                          // 25th of December, 1975 2:15:16 pm
    Carbon::resetToStringFormat();
    echo $dt;                                          // 1975-12-25 14:15:16

Unfortunately the base class DateTime does not have any localization support.  To begin localization support a `formatLocalized($format)` method has been added.  The implementation makes a call to [strftime](http://www.php.net/strftime) using the current instance timestamp.  If you first set the current locale with [setlocale()](http://www.php.net/setlocale) then the string returned will be formatted in the correct locale.

    setlocale(LC_TIME, 'German');                     
    echo $dt->formatLocalized('%A %d %B %Y');          // Donnerstag 25 Dezember 1975
    setlocale(LC_TIME, '');                           
    echo $dt->formatLocalized('%A %d %B %Y');          // Thursday 25 December 1975

## 公共格式转化

The following are wrappers for the common formats provided in the [DateTime class](http://www.php.net/manual/en/class.datetime.php).

    $dt = Carbon::now();
    
    echo $dt->toATOMString();        // same as $dt->format(DateTime::ATOM);
    echo $dt->toCOOKIEString();
    echo $dt->toISO8601String();
    echo $dt->toRFC822String();
    echo $dt->toRFC850String();
    echo $dt->toRFC1036String();
    echo $dt->toRFC1123String();
    echo $dt->toRFC2822String();
    echo $dt->toRFC3339String();
    echo $dt->toRSSString();
    echo $dt->toW3CString();

### 比较

Simple comparison is offered up via the following functions.  Remember that the comparison is done in the UTC timezone so things aren't always as they seem.

    echo Carbon::now()->tzName;                        // America/Toronto
    $first = Carbon::create(2012, 9, 5, 23, 26, 11);
    $second = Carbon::create(2012, 9, 5, 20, 26, 11, 'America/Vancouver');
    
    echo $first->toDateTimeString();                   // 2012-09-05 23:26:11
    echo $first->tzName;                               // America/Toronto
    echo $second->toDateTimeString();                  // 2012-09-05 20:26:11
    echo $second->tzName;                              // America/Vancouver
    
    var_dump($first->eq($second));                     // bool(true)
    var_dump($first->ne($second));                     // bool(false)
    var_dump($first->gt($second));                     // bool(false)
    var_dump($first->gte($second));                    // bool(true)
    var_dump($first->lt($second));                     // bool(false)
    var_dump($first->lte($second));                    // bool(true)
    
    $first->setDateTime(2012, 1, 1, 0, 0, 0);
    $second->setDateTime(2012, 1, 1, 0, 0, 0);         // Remember tz is 'America/Vancouver'
    
    var_dump($first->eq($second));                     // bool(false)
    var_dump($first->ne($second));                     // bool(true)
    var_dump($first->gt($second));                     // bool(false)
    var_dump($first->gte($second));                    // bool(false)
    var_dump($first->lt($second));                     // bool(true)
    var_dump($first->lte($second));                    // bool(true)

To determine if the current instance is between two other instances you can use the aptly named `between()` method.  The third parameter indicates if an equal to comparison should be done.  The default is true which determines if its between or equal to the boundaries.

    $first = Carbon::create(2012, 9, 5, 1);
    $second = Carbon::create(2012, 9, 5, 5);
    var_dump(Carbon::create(2012, 9, 5, 3)->between($first, $second));          // bool(true)
    var_dump(Carbon::create(2012, 9, 5, 5)->between($first, $second));          // bool(true)
    var_dump(Carbon::create(2012, 9, 5, 5)->between($first, $second, false));   // bool(false)

Woah! Did you forget min() and max() ? Nope. That is covered as well by the suitably named `min()` and `max()` methods.  As usual the default parameter is now if null is specified.

    $dt1 = Carbon::create(2012, 1, 1, 0, 0, 0);
    $dt2 = Carbon::create(2014, 1, 30, 0, 0, 0);
    echo $dt1->min($dt2);                              // 2012-01-01 00:00:00
    
    $dt1 = Carbon::create(2012, 1, 1, 0, 0, 0);
    $dt2 = Carbon::create(2014, 1, 30, 0, 0, 0);
    echo $dt1->max($dt2);                              // 2014-01-30 00:00:00
    
    // now is the default param
    $dt1 = Carbon::create(2000, 1, 1, 0, 0, 0);
    echo $dt1->max();                                  // 2014-01-06 22:52:03

To handle the most used cases there are some simple helper functions that hopefully are obvious from their names.  For the methods that compare to `now()` (ex. isToday()) in some manner the `now()` is created in the same timezone as the instance.

    $dt = Carbon::now();
    
    $dt->isWeekday();
    $dt->isWeekend();
    $dt->isYesterday();
    $dt->isToday();
    $dt->isTomorrow();
    $dt->isFuture();
    $dt->isPast();
    $dt->isLeapYear();

### 加法 和 减法

The default DateTime provides a couple of different methods for easily adding and subtracting time.  There is `modify()`, `add()` and `sub()`.  `modify()` takes a *magical* date/time format string, 'last day of next month', that it parses and applies the modification while `add()` and `sub()` use a `DateInterval` class thats not so obvious, `new \DateInterval('P6YT5M')`.  Hopefully using these fluent functions will be more clear and easier to read after not seeing your code for a few weeks.  But of course I don't make you choose since the base class functions are still available.

    $dt = Carbon::create(2012, 1, 31, 0);
    
    echo $dt->toDateTimeString();            // 2012-01-31 00:00:00
    
    echo $dt->addYears(5);                   // 2017-01-31 00:00:00
    echo $dt->addYear();                     // 2018-01-31 00:00:00
    echo $dt->subYear();                     // 2017-01-31 00:00:00
    echo $dt->subYears(5);                   // 2012-01-31 00:00:00
    
    echo $dt->addMonths(60);                 // 2017-01-31 00:00:00
    echo $dt->addMonth();                    // 2017-03-03 00:00:00 equivalent of $dt->month($dt->month + 1); so it wraps
    echo $dt->subMonth();                    // 2017-02-03 00:00:00
    echo $dt->subMonths(60);                 // 2012-02-03 00:00:00
    
    echo $dt->addDays(29);                   // 2012-03-03 00:00:00
    echo $dt->addDay();                      // 2012-03-04 00:00:00
    echo $dt->subDay();                      // 2012-03-03 00:00:00
    echo $dt->subDays(29);                   // 2012-02-03 00:00:00
    
    echo $dt->addWeekdays(4);                // 2012-02-09 00:00:00
    echo $dt->addWeekday();                  // 2012-02-10 00:00:00
    echo $dt->subWeekday();                  // 2012-02-09 00:00:00
    echo $dt->subWeekdays(4);                // 2012-02-03 00:00:00
    
    echo $dt->addWeeks(3);                   // 2012-02-24 00:00:00
    echo $dt->addWeek();                     // 2012-03-02 00:00:00
    echo $dt->subWeek();                     // 2012-02-24 00:00:00
    echo $dt->subWeeks(3);                   // 2012-02-03 00:00:00
    
    echo $dt->addHours(24);                  // 2012-02-04 00:00:00
    echo $dt->addHour();                     // 2012-02-04 01:00:00
    echo $dt->subHour();                     // 2012-02-04 00:00:00
    echo $dt->subHours(24);                  // 2012-02-03 00:00:00
    
    echo $dt->addMinutes(61);                // 2012-02-03 01:01:00
    echo $dt->addMinute();                   // 2012-02-03 01:02:00
    echo $dt->subMinute();                   // 2012-02-03 01:01:00
    echo $dt->subMinutes(61);                // 2012-02-03 00:00:00
    
    echo $dt->addSeconds(61);                // 2012-02-03 00:01:01
    echo $dt->addSecond();                   // 2012-02-03 00:01:02
    echo $dt->subSecond();                   // 2012-02-03 00:01:01
    echo $dt->subSeconds(61);                // 2012-02-03 00:00:00

For fun you can also pass negative values to `addXXX()`, in fact that's how `subXXX()` is implemented.

### 差异时间

These functions always return the **total difference** expressed in the specified time requested.  This differs from the base class `diff()` function where an interval of 61 seconds would be returned as 1 minute and 1 second via a `DateInterval` instance.  The `diffInMinutes()` function would simply return 1.  All values are truncated and not rounded.  Each function below has a default first parameter which is the Carbon instance to compare to, or null if you want to use `now()`.  The 2nd parameter again is optional and indicates if you want the return value to be the absolute value or a relative value that might have a `-` (negative) sign if the passed in date is less than the current instance.  This will default to true, return the absolute value.  The comparisons are done in UTC.

    // Carbon::diffInYears(Carbon $dt = null, $abs = true)
    
    echo Carbon::now('America/Vancouver')->diffInSeconds(Carbon::now('Europe/London')); // 0
    
    $dtOttawa = Carbon::createFromDate(2000, 1, 1, 'America/Toronto');
    $dtVancouver = Carbon::createFromDate(2000, 1, 1, 'America/Vancouver');
    echo $dtOttawa->diffInHours($dtVancouver);                             // 3
    
    echo $dtOttawa->diffInHours($dtVancouver, false);                      // 3
    echo $dtVancouver->diffInHours($dtOttawa, false);                      // -3
    
    $dt = Carbon::create(2012, 1, 31, 0);
    echo $dt->diffInDays($dt->copy()->addMonth());                         // 31
    echo $dt->diffInDays($dt->copy()->subMonth(), false);                  // -31
    
    $dt = Carbon::create(2012, 4, 30, 0);
    echo $dt->diffInDays($dt->copy()->addMonth());                         // 30
    echo $dt->diffInDays($dt->copy()->addWeek());                          // 7
    
    $dt = Carbon::create(2012, 1, 1, 0);
    echo $dt->diffInMinutes($dt->copy()->addSeconds(59));                  // 0
    echo $dt->diffInMinutes($dt->copy()->addSeconds(60));                  // 1
    echo $dt->diffInMinutes($dt->copy()->addSeconds(119));                 // 1
    echo $dt->diffInMinutes($dt->copy()->addSeconds(120));                 // 2
    
    // others that are defined
    // diffInYears(), diffInMonths(), diffInDays()
    // diffInHours(), diffInMinutes(), diffInSeconds()
    // Carbon::average(Carbon $dt = null)

### 友好的差异时间输出

It is easier for humans to read `1 month ago` compared to 30 days ago.  This is a common function seen in most date libraries so I thought I would add it here as well.  It uses approximations for a month being 4 weeks. The lone argument for the function is the other Carbon instance to diff against, and of course it defaults to `now()` if not specified.

This method will add a phrase after the difference value relative to the instance and the passed in instance.  There are 4 possibilities:

* When comparing a value in the past to default now:
    * 1 hour ago
    * 5 months ago

* When comparing a value in the future to default now:
    * 1 hour from now
    * 5 months from now

* When comparing a value in the past to another value:
    * 1 hour before
    * 5 months before

* When comparing a value in the future to another value:
    * 1 hour after
    * 5 months after

--

    // The most typical usage is for comments
    // The instance is the date the comment was created and its being compared to default now()
    echo Carbon::now()->subDays(5)->diffForHumans();               // 5 days ago
    
    echo Carbon::now()->diffForHumans(Carbon::now()->subYear());   // 1 year after
    
    $dt = Carbon::createFromDate(2011, 2, 1);
    
    echo $dt->diffForHumans($dt->copy()->addMonth());              // 1 month before
    echo $dt->diffForHumans($dt->copy()->subMonth());              // 1 month after
    
    echo Carbon::now()->addSeconds(5)->diffForHumans();            // 5 seconds from now
    
    echo Carbon::now()->subDays(24)->diffForHumans();              // 3 weeks ago

### 调节器

These group of methods perform helpful modifications to the current instance.  Most of them are self explanatory from their names... or at least should be.  You'll also notice that the startOfXXX(), next() and previous() methods set the time to 00:00:00 and the endOfXXX() methods set the time to 23:59:59.

The only one slightly different is the `average()` function.  It moves your instance to the middle date between itself and the provided Carbon argument.

    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->startOfDay();                            // 2012-01-31 00:00:00
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->endOfDay();                              // 2012-01-31 23:59:59
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->startOfMonth();                          // 2012-01-01 00:00:00
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->endOfMonth();                            // 2012-01-31 23:59:59
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->startOfYear();                           // 2012-01-01 00:00:00
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->endOfYear();                             // 2012-12-31 23:59:59
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->startOfDecade();                         // 2010-01-01 00:00:00
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->endOfDecade();                           // 2019-12-31 23:59:59
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->startOfCentury();                        // 2000-01-01 00:00:00
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->endOfCentury();                          // 2099-12-31 23:59:59
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->startOfWeek();                           // 2012-01-30 00:00:00
    var_dump($dt->dayOfWeek == Carbon::MONDAY);        // bool(true) : ISO8601 week starts on Monday
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->endOfWeek();                             // 2012-02-05 23:59:59
    var_dump($dt->dayOfWeek == Carbon::SUNDAY);        // bool(true) : ISO8601 week ends on Sunday
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->next(Carbon::WEDNESDAY);                 // 2012-02-01 00:00:00
    var_dump($dt->dayOfWeek == Carbon::WEDNESDAY);     // bool(true)
    
    $dt = Carbon::create(2012, 1, 1, 12, 0, 0);
    echo $dt->next();                                  // 2012-01-08 00:00:00
    
    $dt = Carbon::create(2012, 1, 31, 12, 0, 0);
    echo $dt->previous(Carbon::WEDNESDAY);             // 2012-01-25 00:00:00
    var_dump($dt->dayOfWeek == Carbon::WEDNESDAY);     // bool(true)
    
    $dt = Carbon::create(2012, 1, 1, 12, 0, 0);
    echo $dt->previous();                              // 2011-12-25 00:00:00
    
    $start = Carbon::create(2014, 1, 1, 0, 0, 0);
    $end = Carbon::create(2014, 1, 30, 0, 0, 0);
    echo $start->average($end);                        // 2014-01-15 12:00:00
    
    // others that are defined that are similar
    //   firstOfMonth(), lastOfMonth(), nthOfMonth()
    //   firstOfQuarter(), lastOfQuarter(), nthOfQuarter()
    //   firstOfYear(), lastOfYear(), nthOfYear()

### 常量

Carbon 类中预定义了下列常量：

- SUNDAY = 0
- MONDAY = 1
- TUESDAY = 2
- WEDNESDAY = 3
- THURSDAY = 4
- FRIDAY = 5
- SATURDAY = 6
- MONTHS_PER_YEAR = 12
- HOURS_PER_DAY = 24
- MINUTES_PER_HOUR = 60
- SECONDS_PER_MINUTE = 60

用例：

    $dt = Carbon::createFromDate(2012, 10, 6);
    if ($dt->dayOfWeek === Carbon::SATURDAY) {
        echo 'Place bets on Ottawa Senators Winning!';
    }

