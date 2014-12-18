# Woo

Woo is a fast non-blocking HTTP server built on top of [libev](http://software.schmorp.de/pkg/libev.html). The goal is to be the fastest web server in Common Lisp.

## Warning

This library is still under development and considered ALPHA quality.

## Usage

### Start a server

```common-lisp
(ql:quickload :woo)

(woo:run
  (lambda (env)
    (declare (ignore env))
    '(200 (:content-type "text/plain") ("Hello, World"))))
```

### Start with Clack

```common-lisp
(ql:quickload :clack)

(clack:clackup
  (lambda (env)
    (declare (ignore env))
    '(200 (:content-type "text/plain") ("Hello, World")))
  :server :woo
  :use-default-middlewares nil)
```

### Cluster

```common-lisp
(woo:run
  (lambda (env)
    (declare (ignore env))
    '(200 (:content-type "text/plain") ("Hello, World")))
  :worker-num 4)
```

## Requirements

* UNIX (GNU Linux, Mac, *BSD)
* [libfixposix](https://github.com/sionescu/libfixposix) (for [IOLib](https://github.com/sionescu/iolib))
* [libev](http://libev.schmorp.de)

## Benchmark

Comparison of the server performance to return "Hello, World" for every requests. Here's the results of requests/sec scores.

![Benchmark Results](images/benchmark.png)

I used the below command of [wrk](https://github.com/wg/wrk).

```
wrk -c [10 or 100] -t 4 -d 10 http://127.0.0.1:5000
```

The benchmarking environment is:

* MacBook Pro Retina, 13-inch, Early 2013 (CPU: 3GHz Intel Core i7 / Memory: 8GB 1600 MHz)
* wrk 3.1.1
* SBCL 1.2.6
* Python 2.7.8
* PyPy 2.4.0
* Tornado 4.0.2
* Node.js 0.10.21
* Quicklisp 2014-11-06
* libevent 2.1.4-alpha (HEAD)
* libev 4.15

NOTE: Though the machine has multiple CPUs, this benchmark assumes single core environment. Some web servers has features to run on multiple CPU cores with better performance, like Hunchentoot's threaded taskmanager or Node.js's cluster.

### Tornado (Python)

```
$ python benchmark/hello.py
```

```
$ wrk -c 10 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.83ms  218.41us   6.63ms   96.11%
    Req/Sec   735.93     83.69     0.89k    80.07%
  28223 requests in 10.00s, 5.57MB read
Requests/sec:   2822.09
Transfer/sec:    570.48KB
```

```
$ wrk -c 100 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    39.14ms    1.02ms  45.78ms   73.21%
    Req/Sec   641.91     37.37   825.00     82.48%
  25504 requests in 10.01s, 5.03MB read
Requests/sec:   2548.72
Transfer/sec:    515.22KB
```

### Hunchentoot (Common Lisp)

```
$ sbcl --load benchmark/hunchentoot.lisp
```

```
$ wrk -c 10 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   144.00us   97.95us   4.76ms   99.85%
    Req/Sec     6.81k   509.00     7.22k    97.05%
  63978 requests in 10.01s, 9.58MB read
  Socket errors: connect 0, read 0, write 0, timeout 34
Requests/sec:   6390.08
Transfer/sec:      0.96MB
```

```
wrk -c 100 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     3.24ms    2.45ms   5.19ms   61.52%
    Req/Sec     6.83k   556.53     7.89k    93.36%
  64140 requests in 10.01s, 9.60MB read
  Socket errors: connect 0, read 161, write 0, timeout 387
Requests/sec:   6406.92
Transfer/sec:      0.96MB
```

### Tornado (PyPy)

```
$ pypy benchmark/hello.py
```

```
$ wrk -c 10 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     4.21ms    8.64ms  30.26ms   90.41%
    Req/Sec     1.80k     1.11k    3.67k    56.02%
  67132 requests in 10.00s, 13.25MB read
Requests/sec:   6712.27
Transfer/sec:      1.33MB
```

```
$ wrk -c 100 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    16.08ms    8.16ms  40.17ms   89.97%
    Req/Sec     1.60k   311.97     2.16k    63.06%
  62962 requests in 10.01s, 12.43MB read
Requests/sec:   6292.83
Transfer/sec:      1.24MB
```

### Wookie (Common Lisp)

```
$ sbcl --load benchmark/wookie.lisp
```

```
$ wrk -c 10 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.25ms    3.35ms  52.53ms   99.16%
    Req/Sec     1.99k   227.28     2.33k    78.41%
  75538 requests in 10.00s, 8.28MB read
Requests/sec:   7554.20
Transfer/sec:    848.37KB
```

```
$ wrk -c 100 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    13.15ms    1.89ms  25.52ms   85.12%
    Req/Sec     1.94k   349.30     2.59k    64.35%
  76227 requests in 10.01s, 8.36MB read
Requests/sec:   7618.58
Transfer/sec:    855.60KB
```

### Node.js http module

```
$ node benchmark/node.js
```

```
$ wrk -c 10 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   558.17us   57.01us   4.02ms   95.83%
    Req/Sec     3.73k   213.97     4.11k    63.56%
  141101 requests in 10.00s, 17.49MB read
Requests/sec:  14110.16
Transfer/sec:      1.75MB
```

```
$ wrk -c 100 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     7.71ms    0.91ms  26.78ms   93.24%
    Req/Sec     3.37k   366.65     4.53k    74.85%
  130057 requests in 10.00s, 16.12MB read
Requests/sec:  13003.58
Transfer/sec:      1.61MB
```

### Woo (Common Lisp)

```
$ sbcl --load benchmark/woo.lisp
```

```
$ wrk -c 10 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   304.78us  435.89us  10.34ms   99.56%
    Req/Sec     7.06k     1.33k    9.89k    74.84%
  267269 requests in 10.00s, 33.65MB read
Requests/sec:  26728.29
Transfer/sec:      3.36MB
```

```
$ wrk -c 100 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     3.83ms    1.35ms  23.13ms   95.43%
    Req/Sec     7.02k     1.03k    8.33k    82.96%
  266506 requests in 10.00s, 33.55MB read
Requests/sec:  26649.52
Transfer/sec:      3.35MB
```

### Go

```
$ go build benchmark/hello.go
$ ./hello
```

```
$ wrk -c 10 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   274.14us  142.62us   0.92ms   57.60%
    Req/Sec     7.47k     0.96k   10.60k    64.57%
  282965 requests in 10.00s, 34.81MB read
Requests/sec:  28298.26
Transfer/sec:      3.48MB
```

```
wrk -c 100 -t 4 -d 10 http://127.0.0.1:5000
Running 10s test @ http://127.0.0.1:5000
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     3.72ms  742.47us   6.22ms   62.44%
    Req/Sec     7.09k   568.00     9.00k    65.49%
  269210 requests in 10.00s, 33.12MB read
Requests/sec:  26922.81
Transfer/sec:      3.31MB
```

## TODO

* SSL
* Make it faster, more scalable

## See Also

* [libev](http://software.schmorp.de/pkg/libev.html)
* [Wookie](http://wookie.beeets.com)
* [Clack](http://clacklisp.org/)

## Author

* Eitaro Fukamachi (e.arrows@gmail.com)

## Copyright

Copyright (c) 2014 Eitaro Fukamachi (e.arrows@gmail.com)

## License

Licensed under the MIT License.
