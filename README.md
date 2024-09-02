# raft-kv

## Build
### Compilation
Ensure `CMake` with version >= 3.5 is installed:
```
mkdir -p build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j8
```

### Dependencies
To build the executable, the following libraries are required:
- [glib-2.0](https://lazka.github.io/pgi-docs/GLib-2.0/index.html)
- [msgpack](https://github.com/msgpack/msgpack-c)
- [hireredis](https://github.com/redis/hiredis)
- [rocksdb](https://github.com/facebook/rocksdb)

## Running a cluster

First install [goreman](https://github.com/mattn/goreman), which manages Procfile-based applications.

    goreman start


## Usage
Install [redis-cli](https://github.com/antirez/redis), a redis console client.
```
redis-cli -p 63791
127.0.0.1:63791> set mykey myvalue
OK
127.0.0.1:63791> get mykey
"myvalue"
```

Remove a node and replace the myvalue with "new-value" to check cluster availability:
```
goreman run stop node2
redis-cli -p 63791
127.0.0.1:63791> set mykey new-value
OK
```

Bring the node back up and verify it recovers with the updated value "new-value":
```
redis-cli -p 63792
127.0.0.1:63792> KEYS *
1) "mykey"
127.0.0.1:63792> get mykey
"new-value"
```

## Benchmarking
```
> redis-benchmark -t set,get -n 100000 -p 63791

====== SET ======
  100000 requests completed in 2.72 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
  multi-thread: no

Latency by percentile distribution:
0.000% <= 0.327 milliseconds (cumulative count 3)
50.000% <= 0.999 milliseconds (cumulative count 53892)
75.000% <= 1.047 milliseconds (cumulative count 75047)
87.500% <= 1.079 milliseconds (cumulative count 89769)
93.750% <= 1.095 milliseconds (cumulative count 94003)
96.875% <= 1.127 milliseconds (cumulative count 96948)
98.438% <= 1.327 milliseconds (cumulative count 98452)
99.219% <= 40.927 milliseconds (cumulative count 99236)
99.609% <= 42.815 milliseconds (cumulative count 99614)
99.805% <= 43.167 milliseconds (cumulative count 99816)
99.902% <= 43.359 milliseconds (cumulative count 99903)
99.951% <= 43.775 milliseconds (cumulative count 99959)
99.976% <= 44.287 milliseconds (cumulative count 99981)
99.988% <= 44.351 milliseconds (cumulative count 99992)
99.994% <= 44.383 milliseconds (cumulative count 99998)
99.998% <= 44.447 milliseconds (cumulative count 100000)
100.000% <= 44.447 milliseconds (cumulative count 100000)

Cumulative distribution of latencies:
0.000% <= 0.103 milliseconds (cumulative count 0)
0.018% <= 0.407 milliseconds (cumulative count 18)
0.036% <= 0.503 milliseconds (cumulative count 36)
0.052% <= 0.607 milliseconds (cumulative count 52)
0.157% <= 0.703 milliseconds (cumulative count 157)
1.419% <= 0.807 milliseconds (cumulative count 1419)
12.950% <= 0.903 milliseconds (cumulative count 12950)
57.749% <= 1.007 milliseconds (cumulative count 57749)
95.276% <= 1.103 milliseconds (cumulative count 95276)
97.905% <= 1.207 milliseconds (cumulative count 97905)
98.387% <= 1.303 milliseconds (cumulative count 98387)
98.544% <= 1.407 milliseconds (cumulative count 98544)
98.655% <= 1.503 milliseconds (cumulative count 98655)
98.738% <= 1.607 milliseconds (cumulative count 98738)
98.846% <= 1.703 milliseconds (cumulative count 98846)
98.940% <= 1.807 milliseconds (cumulative count 98940)
98.973% <= 1.903 milliseconds (cumulative count 98973)
99.015% <= 2.007 milliseconds (cumulative count 99015)
99.044% <= 2.103 milliseconds (cumulative count 99044)
99.137% <= 3.103 milliseconds (cumulative count 99137)
99.139% <= 4.103 milliseconds (cumulative count 99139)
99.141% <= 5.103 milliseconds (cumulative count 99141)
99.144% <= 6.103 milliseconds (cumulative count 99144)
99.147% <= 7.103 milliseconds (cumulative count 99147)
99.148% <= 8.103 milliseconds (cumulative count 99148)
99.150% <= 19.103 milliseconds (cumulative count 99150)
99.265% <= 41.119 milliseconds (cumulative count 99265)
99.500% <= 42.111 milliseconds (cumulative count 99500)
99.751% <= 43.103 milliseconds (cumulative count 99751)
99.965% <= 44.127 milliseconds (cumulative count 99965)
100.000% <= 45.119 milliseconds (cumulative count 100000)

Summary:
  throughput summary: 36737.70 requests per second
  latency summary (msec):
          avg       min       p50       p95       p99       max
        1.348     0.320     0.999     1.103     1.983    44.447
====== GET ======
  100000 requests completed in 0.69 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
  multi-thread: no

Latency by percentile distribution:
0.000% <= 0.095 milliseconds (cumulative count 2)
50.000% <= 0.191 milliseconds (cumulative count 65510)
75.000% <= 0.199 milliseconds (cumulative count 77110)
87.500% <= 0.215 milliseconds (cumulative count 88309)
93.750% <= 0.239 milliseconds (cumulative count 94669)
96.875% <= 0.271 milliseconds (cumulative count 96997)
98.438% <= 0.343 milliseconds (cumulative count 98505)
99.219% <= 0.447 milliseconds (cumulative count 99262)
99.609% <= 0.535 milliseconds (cumulative count 99638)
99.805% <= 0.559 milliseconds (cumulative count 99839)
99.902% <= 0.575 milliseconds (cumulative count 99904)
99.951% <= 0.615 milliseconds (cumulative count 99954)
99.976% <= 0.631 milliseconds (cumulative count 99976)
99.988% <= 4.167 milliseconds (cumulative count 99988)
99.994% <= 7.191 milliseconds (cumulative count 99994)
99.997% <= 8.879 milliseconds (cumulative count 99997)
99.998% <= 9.527 milliseconds (cumulative count 99999)
99.999% <= 9.823 milliseconds (cumulative count 100000)
100.000% <= 9.823 milliseconds (cumulative count 100000)

Cumulative distribution of latencies:
0.003% <= 0.103 milliseconds (cumulative count 3)
83.743% <= 0.207 milliseconds (cumulative count 83743)
98.043% <= 0.303 milliseconds (cumulative count 98043)
99.027% <= 0.407 milliseconds (cumulative count 99027)
99.523% <= 0.503 milliseconds (cumulative count 99523)
99.936% <= 0.607 milliseconds (cumulative count 99936)
99.979% <= 0.703 milliseconds (cumulative count 99979)
99.980% <= 0.903 milliseconds (cumulative count 99980)
99.981% <= 1.207 milliseconds (cumulative count 99981)
99.982% <= 1.607 milliseconds (cumulative count 99982)
99.983% <= 2.007 milliseconds (cumulative count 99983)
99.985% <= 3.103 milliseconds (cumulative count 99985)
99.987% <= 4.103 milliseconds (cumulative count 99987)
99.989% <= 5.103 milliseconds (cumulative count 99989)
99.991% <= 6.103 milliseconds (cumulative count 99991)
99.993% <= 7.103 milliseconds (cumulative count 99993)
99.995% <= 8.103 milliseconds (cumulative count 99995)
99.997% <= 9.103 milliseconds (cumulative count 99997)
100.000% <= 10.103 milliseconds (cumulative count 100000)

Summary:
  throughput summary: 145137.88 requests per second
  latency summary (msec):
          avg       min       p50       p95       p99       max
        0.198     0.088     0.191     0.247     0.407     9.823
```
