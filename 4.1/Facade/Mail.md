Mail
============

\Illuminate\Mail\Mailer.php

---


---


<a name="alwaysFrom"></a>
##### Set the global from address and name.

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
##### Send a new message using a view.

    @param  string|array  $view
    @param  array  $data
    @param  Closure|string  $callback
    @return int
    send($view, array $data, $callback)

<a name="queue"></a>
##### Queue a new e-mail message for sending.

    @param  string|array  $view
    @param  array   $data
    @param  Closure|string  $callback
    @param  string  $queue
    @return void
    queue($view, array $data, $callback, $queue = null)

<a name="queueOn"></a>
##### Queue a new e-mail message for sending on the given queue.

    @param  string  $queue
    @param  string|array  $view
    @param  array   $data
    @param  Closure|string  $callback
    @return void
    queueOn($queue, $view, array $data, $callback)

<a name="later"></a>
##### Queue a new e-mail message for sending after (n) seconds.

    @param  int  $delay
    @param  string|array  $view
    @param  array  $data
    @param  Closure|string  $callback
    @param  string  $queue
    @return void
    later($delay, $view, array $data, $callback, $queue = null)

<a name="laterOn"></a>
##### Queue a new e-mail message for sending after (n) seconds on the given queue.

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
##### Get the array of failed recipients.

    @return array
    failures()

<a name="setLogger"></a>
##### Set the log writer instance.

    @param  \Illuminate\Log\Writer  $logger
    @return \Illuminate\Mail\Mailer
    setLogger(Writer $logger)

<a name="setQueue"></a>
##### Set the queue manager instance.

    @param  \Illuminate\Queue\QueueManager  $queue
    @return \Illuminate\Mail\Mailer
    setQueue(QueueManager $queue)

<a name="setContainer"></a>
##### Set the IoC container instance.

    @param  \Illuminate\Container\Container  $container
    @return void
    setContainer(Container $container)