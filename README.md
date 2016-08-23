# InstallCosmoSIS
简介一下安装CosmoSIS的辛酸历程，可能可以帮助跟我一样的菜鸟
安装机器在一台没有sudo权限的小服务器，系统centus_7，python用的是Anaconda2
https://bitbucket.org/joezuntz/cosmosis/wiki/Manual%20Install，按照指南安装dependencies，系统里确定有gcc,git，其他4个不确定就分别下载

下载gsl-latest.tar.gz
tar gsl-latest.tar.gz
cd gsl-2.1
./configure --prefix=$HOME/lib/gsl
make
make install

cfitsio3390.tar.gz
tar xvf cfitsio3390.tar.gz
cd cfitsio
./configure --prefix=$HOME/lib/cfitsio （最后对CosmoSIS进行make的时候失败了多次，回到这里多次尝试，加了CFLAGS=-fPIC CPPFLAGS=-fPIC，也不知道是不是这个的原因……）
make shared
make install

fftw-3.3.5.tar.gz

lapack-3.6.1.tgz
