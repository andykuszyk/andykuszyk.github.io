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

```
java ...
cd /
jeprof --show_bytes --gif $(which java) jeprof*.heap > app-profiling.gif
```

```
docker exec -it ... bash
kill <pid>
docker cp ...:/app-profiling.gif ./
```
