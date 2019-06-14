```
docker run -it --entrypoint bash debian:stretch
apt-get update
apt-get install wget gcc lbzip2 make
wget https://github.com/jemalloc/jemalloc/releases/download/5.2.0/jemalloc-5.2.0.tar.bz2
tar -xvf jemalloc-5.2.0.tar.bz2
cd jemalloc-5.2.0
./configure
make
make install
```
