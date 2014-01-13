邮件操作
============



---


<a name="alwaysFrom"></a>
##### 设置全局地址和名称。

    @param  string  $address
    @param  string  $name
    @return void
    Mail::alwaysFrom($address, $name = null)

<a name="plain"></a>
##### Send a new message when only a plain part.

    @param  string  $view
    @param  array   $data
    @param  mixed   $callback
    @return int
    Mail::plain($view, array $data, $callback)

<a name="send"></a>
##### 使用视图发送新邮件。

    @param  string|array  $view
    @param  array  $data
    @param  Closure|string  $callback
    @return int
    Mail::send($view, array $data, $callback)

案例：

    Mail::send('mail.demo', array('name'=>'testUser'), function($message)
    {
        $message
            ->to('目标邮箱')
            ->subject('邮件标题')
            ->attach('本地附件路径');
    });

<a name="queue"></a>
##### 发送一封新邮件（默认队列）。

    @param  string|array  $view
    @param  array   $data
    @param  Closure|string  $callback
    @param  string  $queue
    @return void
    Mail::queue($view, array $data, $callback, $queue = null)

<a name="queueOn"></a>
##### 发送一封新邮件（指定队列）。

    @param  string  $queue
    @param  string|array  $view
    @param  array   $data
    @param  Closure|string  $callback
    @return void
    Mail::queueOn($queue, $view, array $data, $callback)

<a name="later"></a>
##### 延迟（N）秒发送电子邮件（默认队列）。

    @param  int  $delay
    @param  string|array  $view
    @param  array  $data
    @param  Closure|string  $callback
    @param  string  $queue
    @return void
    Mail::later($delay, $view, array $data, $callback, $queue = null)

<a name="laterOn"></a>
##### 延迟（N）秒发送电子邮件（指定队列）。

    @param  string  $queue
    @param  int  $delay
    @param  string|array  $view
    @param  array  $data
    @param  Closure|string  $callback
    @return void
    Mail::laterOn($queue, $delay, $view, array $data, $callback)

<a name="handleQueuedMessage"></a>
##### Handle a queued e-mail message job.

    @param  \Illuminate\Queue\Jobs\Job  $job
    @param  array  $data
    @return void
    Mail::handleQueuedMessage($job, $data)

<a name="pretend"></a>
##### Tell the mailer to not really send messages.

    @param  bool  $value
    @return void
    Mail::pretend($value = true)

<a name="getViewEnvironment"></a>
##### Get the view environment instance.

    @return \Illuminate\View\Environment
    Mail::getViewEnvironment()

<a name="failures"></a>
##### 获取发送失败的收件人数组

    @return array
    Mail::failures()

