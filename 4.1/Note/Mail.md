Mail
============

\Illuminate\Mail\Mailer.php

---


---


<a name="alwaysFrom"></a>
##### 设置全局地址和名称。

    @param  string  $address
    @param  string  $name
    @return void
    alwaysFrom($address, $name = null)

<a name="plain"></a>
##### Send a new message when only a plain part.

    @param  string  $view
    @param  array   $data
    @param  mixed   $callback
    @return int
    plain($view, array $data, $callback)

<a name="send"></a>
##### 使用视图发送新邮件。

    @param  string|array  $view
    @param  array  $data
    @param  Closure|string  $callback
    @return int
    send($view, array $data, $callback)

<a name="queue"></a>
##### 发送一封新邮件（默认队列）。

    @param  string|array  $view
    @param  array   $data
    @param  Closure|string  $callback
    @param  string  $queue
    @return void
    queue($view, array $data, $callback, $queue = null)

<a name="queueOn"></a>
##### 发送一封新邮件（指定队列）。

    @param  string  $queue
    @param  string|array  $view
    @param  array   $data
    @param  Closure|string  $callback
    @return void
    queueOn($queue, $view, array $data, $callback)

<a name="later"></a>
##### 延迟（N）秒发送电子邮件（默认队列）。

    @param  int  $delay
    @param  string|array  $view
    @param  array  $data
    @param  Closure|string  $callback
    @param  string  $queue
    @return void
    later($delay, $view, array $data, $callback, $queue = null)

<a name="laterOn"></a>
##### 延迟（N）秒发送电子邮件（指定队列）。

    @param  string  $queue
    @param  int  $delay
    @param  string|array  $view
    @param  array  $data
    @param  Closure|string  $callback
    @return void
    laterOn($queue, $delay, $view, array $data, $callback)

<a name="handleQueuedMessage"></a>
##### Handle a queued e-mail message job.

    @param  \Illuminate\Queue\Jobs\Job  $job
    @param  array  $data
    @return void
    handleQueuedMessage($job, $data)

<a name="pretend"></a>
##### Tell the mailer to not really send messages.

    @param  bool  $value
    @return void
    pretend($value = true)

<a name="getViewEnvironment"></a>
##### Get the view environment instance.

    @return \Illuminate\View\Environment
    getViewEnvironment()

<a name="failures"></a>
##### 获取发送失败的收件人数组

    @return array
    failures()

