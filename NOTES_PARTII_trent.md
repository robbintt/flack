## Part 2 - Scaling Flask for Production.

Notes from Miguel Grinberg's talk on making a production flask app.

PyCon 2016 - 20160528 0900 PST

Presentation URL:


### Scaling Web Servers

Problem - one request at a time.  Not great.

#### Multiple Threads

Limited use of multiple threads because of GIL.  Not really great.


#### Multiple processes

Obviously multiple apps sounds nice. Each process has its own set of things.

What about SQLAlchemy?


#### Green threads / coroutines - asyncio, gevent, eventlet.

Flask doesn't support asyncio.

Flask does support gevent, eventlet.

IO and standard library `threading` functions are incompatible.

How about manually triggering a switch? - `sleep` function to take a break and let other threads have a turn.

Note on gevent and eventlet -- A lot of people get this wrong on stack overflow. This stuff isn't for cpu intensive work. All green threads need a chance to run and CPU must be available or sleeps must run very often to let the scheduler switch.

Miguel's IO library is for this. Read more about this.  I have it in other notes but a google search will suffice.

Both eventlet and gevent allow monkeypatching to coroutine friendly functions (overwrite standard library function with the coroutine friendly function).


#### Using Production Web Servers (tag: v0.12) 

##### gunicorn

Limited load balancing - Why limited? I will have to look this up.

Written in Python - robust but not lightning fast

Supports multiple processes, and eventlet or gevent green threads.


##### uwsgi




##### nginx

Written in C.  

Ideal for serving static files in production. You expose the static file in the nginx config.


#### Bottlenecks: I/O Bound and CPU Bound

##### I/O Bottlenecks

Flack example: scraping links included in posts

Solution: concurrent request handlers through multiple threads, processes, or green threads.

Make I/O heavy requests asynchronous.

##### CPU Bottlenecks

Flack example: rendering posts from markdown to HTML

Solution: Offload CPU intensive requests to auxiliary threads or processes to keep the server unblocked.  Rephrased: make CPU intensive requests asynchronous.


#### Asynchronous HTTP Requests:

1. Request starts background work, gives a status 202.
2. Goes back to listening requests.
3. Location header has a status URL where the client can ask for status for the asynchronous task.
4. Request to status URL returns 202 while the request is still in progress.
5. When complete, (see presentation for details)

There is a decorator for this in flask.... @async decorator.

That's all that is necessary!!!  This is almost too easy.


