# recluster

Clustering library with support for zero-downtime reloading

# usage

If server.js is your regular http server (e.g. express), create
cluster.js and add:

```js
var recluster = require('recluster'),
    path = require('path');
 
var cluster = recluster(path.join(__dirname, 'server.js'));
cluster.run();

process.on('SIGUSR2', function() {
    console.log('Got SIGUSR2, reloading cluster...');
    cluster.reload();
});

console.log("spawned cluster, kill -s SIGUSR2", process.pid, "to reload");
```

then run it

    node cluster.js

To hot-reload the server, simply run
    
    kill -s SIGUSR2 <cluster_pid>

To find out which of the N (= number of cores by default) worker
instances you're running from inside server.js, you can use 

    process.env.WORKER_ID

which is zero-based i.e. 0 <= WORKER_ID < N

# options

    var cluster = recluster(file, opt)

where 

### file        

Absolute path to the module that defines the server

### opt.workers

Number of active workers (default = cores)

### opt.timeout

Timeout to kill old workers after reload (seconds). 

Defaults to 1 second in development, 1 hour in production.

### opt.respawn 

Minimum time between worker respawns when workers die (seconds)

### opt.backoff

Maximum respawn time (reached via exponential backoff). Set to
0 or undefined to disable exponential backoff.

# cluster

The returned object has the following methods:

### cluster.run

Starts the cluster by running child processes

### cluster.reload

Hot-reloads new code. some of the children will remain active 
for `opt.timeout` seconds after reload

### cluster.terminate

Terminates the entire cluster and removes all listeners.

![Bitdeli ba](https://d2weczhvl823v0.cloudfront.net/spion/recluster/trend.png)


