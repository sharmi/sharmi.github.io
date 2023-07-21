---
 
title: RabbitMQ vs Redis as Message Brokers
publishDate: 2013-10-16 12:50:07.000000000 +05:30

category:  Python External Library
tags:
- Message Brokers
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _wpas_done_all: '1'
  dsq_thread_id: '2892679934'
author:  sharmi
 
canonical: "https://www.minvolai.com/rabbitmq-vs-redis-as-message-brokers/"
slug: rabbitmq-vs-redis-as-message-brokers
---
<p>I have been looking into job queues for one of my personal projects.  This excellent post by Muriel Salvan <a href="http://x-aeon.com/wp/2013/04/10/a-quick-message-queue-benchmark-activemq-rabbitmq-hornetq-qpid-apollo/">A quick message queue benchmark: ActiveMQ, RabbitMQ, HornetQ, QPID, Apollo</a> gives a good comparison of popular message brokers.  The consensus in on RabbitMQ, which is well established but one of the upcoming options not covered is Redis.  With it's recent support for PubSub, it is shaping up a strong contender.</p>
<h2>Advantages of RabbitMQ</h2>
<ul>
<li>Highly customizable routing</li>
<li>Persistent queues</li>
</ul>
<h2>Advantages of Redis</h2>
<ul>
<li>high speed due to in memory datastore</li>
<li>can double up as both key-value datastore and job queue</li>
</ul>
<p>Since I'm working in python, I decided to go with Celery. I tried testing both RabbitMQ and Redis by adding 100000 messages to the queue and using a worker to process the queued messages.  The test was run thrice and averaged.  In the case of the celery worker, there doesn't seem to be a burst mode, i.e the worker cannot not exit when all the messages in the queue are processed.  So I had to use the next best approximation, the timestamps in the log messages.</p>
<p>tasks.py has the task definition and the message broker to use.</p>
<pre><code class="python">    from celery import Celery
    celery = Celery('tasks', broker='amqp://guest@localhost//') 
    #celery = Celery('tasks', broker='redis://localhost//') 

    @celery.task
    def newtask(somestr, dt, value):
        pass

</code></pre>
<p>test.py does the actual adding of the tasks to the queue</p>
<pre><code class="python"><br />    from tasks import newtask
    from datetime import datetime
    import time


    dt = datetime.utcnow()
    st_time = time.time()
    for i in xrange(100000):
        newtask.delay('shortstring', dt, 67.8)
    print time.time() - st_time

</code></pre>
<p>The celery worker retrieves the messages by running the command</p>
<pre><code class="bash">    time celery -A tasks worker --loglevel=info  -f tasks.log --concurrency 1

</code></pre>
<p>--concurrency indicates how many simultaneous workers to run.  -f indicates the logfile to use. We can infer the time taken for the run from the log timestamp to process the last message.  Next we need to estimate the time taken for the INFO level logging the worker does and deduct it from the total time taken.</p>
<pre><code class="python">    import logging
    import sys
    import time
    logger = logging.getLogger('MainProcess')
    hdlr = logging.FileHandler('/tmp/myapp.log')
    formatter = logging.Formatter('%(asctime)s %(levelname)s %(message)s')
    hdlr.setFormatter(formatter)
    logger.addHandler(hdlr) 
    logger.setLevel(logging.INFO)


    def main():
        inputf = sys.argv[1]
        for inputf in sys.argv[1:]:
            loglines = file(inputf).readlines()
            loglines = [line.split(']', 1)[1].strip() for line in loglines]
            st_time = time.time()
            for line in loglines:
                logger.info(line)
            print inputf, time.time() - st_time


    if __name__ == "__main__":
        main()

</code></pre>
<p>Here is the tabulation of the results for each trial consisting of 100,000 messages.  It is apparent that RabbitMQ takes  75% of Redis' time to add a message and 86% of the time to process a message.  Since the message processing capacity is almost equal, the decision would be solely based on the features.  i.e if you want sophisticated routing capabilities go with RabbitMQ. If you need an in memory key-value store go with Redis.</p>
<table>
<thead>
<tr>
<th>Activity</th>
<th>Trial 1</th>
<th>Trial 2</th>
<th>Trial 3</th>
<th>Average</th>
<th>Per Message</th>
</tr>
</thead>
<tbody>
<tr>
<td>RabbitMQ - Adding Message to Queue</td>
<td>56.96</td>
<td>54.18</td>
<td>57.13</td>
<td>56.09</td>
<td>0.0005609</td>
</tr>
<tr>
<td>Redis - Adding Message to Queue</td>
<td>68.81</td>
<td>76.52</td>
<td>76.95</td>
<td>74.09</td>
<td>0.0007409</td>
</tr>
<tr>
<td>RabbitMQ - Processing Messages off the Queue</td>
<td>122.406</td>
<td>132.55</td>
<td>195.885</td>
<td>150.28</td>
<td>0.0015028</td>
</tr>
<tr>
<td>Redis - Processing Messages off the Queue</td>
<td>157.59</td>
<td>177.774</td>
<td>186.332</td>
<td>173.9</td>
<td>0.001739</td>
</tr>
</tbody>
</table>
