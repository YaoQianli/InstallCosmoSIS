# InstallCosmoSIS
简介一下安装CosmoSIS的辛酸历程，可能可以帮助跟我一样的菜鸟
安装机器在一台没有sudo权限的小服务器，系统centus_7，python用的是Anaconda2

https://bitbucket.org/joezuntz/cosmosis/wiki/Manual%20Install，按照指南安装dependencies，系统里确定有gcc,git，其他4个不确定就分别下载，研究安装方法和调试错误花了好多天……

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
make shared（不加shared的话只生成libcfitsio.a，没有libcfitsio.so）
make install

fftw-3.3.5.tar.gz
tar xvf fftw-3.3.5.tar.gz
cd fftw-3.3.5
./configure CFLAGS=-fPIC LDFLAGS=-fPIC CPPFLAGS=-fPIC FFLAGS=-fPIC --prefix=$HOME/lib/fftw
（死马当活马医加了一堆-fPIC成功了……可以./configure --help查查）
make
make install

lapack-3.6.1.tgz
唯一一个按照网上攻略能装成功的，http://maslino.website/post/compile-installation-blas-and-lapack.html
tar zxf lapack-3.6.1.tgz
cd lapack-3.6.1
cp INSTALL/make.inc.gfortran make.inc   # On Linux with lapack-3.2.1 or newer
make lapacklib
vi make.inc
修改选项，给OPTS和NOOPT这两个设置都加上-fPIC选项，结果如下：
FORTRAN  = gfortran 
OPTS     = -O2 -frecursive -fPIC -m64
DRVOPTS  = $(OPTS)
NOOPT    = -O0 -frecursive -fPIC -m64
LOADER   = gfortran
:x
make


