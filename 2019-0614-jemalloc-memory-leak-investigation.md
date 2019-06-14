# Investigating native memory leaks using `jemalloc`
I was recently investigating a memory leak of a Java application, which didn't seem to exhibit an increase in heap utilisation, despite having a constantly growing memory footprint.

Reading [this]() and [this]() article led me to suspect that my problem might have been due to unmanaged memory allocation taking place through native Java calls. Perhaps this was a case of some resources that utilise native memory not correctly freeing up the memory they had used.

It transpires that this was not the case, but the process of discovering this was rather torturous, so I thought I'd post up the steps I followed to get `jemalloc` running and profiling my application.

## Adding `jemalloc` to my container's runtime
Installing `jemalloc` is not the most well documented process. In the end, I cloned the repo and built it from source, with the following line added to my `Dockerfile`:

```
RUN apt-get update && \
    apt-get install -y wget gcc lbzip2 make graphviz && \
    wget https://github.com/jemalloc/jemalloc/releases/download/5.2.0/jemalloc-5.2.0.tar.bz2 && \
    tar -xvf jemalloc-5.2.0.tar.bz2 && \
    cd jemalloc-5.2.0 && \
    ./configure --enable-prof && \
    make && \
    make install

ENV MALLOC_CONF prof_leak:true,lg_prof_sample:0,prof_final:true
ENV LD_PRELOAD /usr/local/lib/libjemalloc.so.2
```

## Generating a visualisation of memory usage
A `jeprof` command needs to be run after your process terminates in order to generate a visualisation of memory usage. Since my application was runnnig in a container, and terminating it would terminate the container, I wrapped the application's execution in a shell script and added the relevant `jeprof` command to make sure that it would always execute once the application did:

```
java ...
cd /
jeprof --show_bytes --gif $(which java) jeprof*.heap > app-profiling.gif
```

## Collecting the visualisation for later inspection
Having run my application in a container and exposed it to the scenario in which it exhibited high memory usage, I then used `docker exec` to kill the Java process:

```
docker exec -it <container_id> kill <pid>
```

This caused the script to finish executing and save the visualisation. I then ran `docker ps -a` to find the terminated Docker process and used the following to copy out the visualisation:

```
docker cp <container_id>:/app-profiling.gif ./
```
