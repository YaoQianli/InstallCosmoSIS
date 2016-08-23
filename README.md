# InstallCosmoSIS

简介一下安装CosmoSIS的辛酸历程，可能可以帮助跟我一样的菜鸟

安装机器在一台没有sudo权限的小服务器，系统centus_7，python用的是Anaconda2

https://bitbucket.org/joezuntz/cosmosis/wiki/Manual%20Install

按照指南安装dependencies，系统里确定有gcc,git，其他4个不确定就分别下载，研究安装方法和调试错误花了好多天……

-----------------------------

下载gsl-latest.tar.gz

tar gsl-latest.tar.gz

cd gsl-2.1

./configure --prefix=$HOME/lib/gsl

make

make install

-------------------------------

cfitsio3390.tar.gz

tar xvf cfitsio3390.tar.gz

cd cfitsio

./configure --prefix=$HOME/lib/cfitsio （最后对CosmoSIS进行make的时候失败了多次，回到这里多次尝试，加了CFLAGS=-fPIC CPPFLAGS=-fPIC，也不知道是不是这个的原因……）

make shared（不加shared的话只生成libcfitsio.a，没有libcfitsio.so）

make install

---------------------------------

fftw-3.3.5.tar.gz

tar xvf fftw-3.3.5.tar.gz

cd fftw-3.3.5

./configure CFLAGS=-fPIC LDFLAGS=-fPIC CPPFLAGS=-fPIC FFLAGS=-fPIC --prefix=$HOME/lib/fftw
（死马当活马医加了一堆-fPIC成功了……可以./configure --help查查）

make

make install

------------------------------------------

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

------------------------------------------

python的东西anaconda里面都有，再按照cosmosis网站上介绍的安装emcee，下载cosmosis主体

3 setup script里面，Edit the new file setup-my-cosmosis也蛮麻烦的，附上我的setup-my-cosmosis

%#This script is only used if you did a manual installation of CosmoSIS;

%#i.e. if you did not use the "bootstrap" script and have no "ups" directory


%#See the wiki https://bitbucket.org/joezuntz/cosmosis/wiki/edit/Manual%20Install

%#on how to change and use this file



export COSMOSIS_SRC_DIR=$HOME/cosmosis


%#The gnu science library

export GSL_INC=$HOME/lib/gsl/include

export GSL_LIB=$HOME/lib/gsl/lib


%#The cfitsio FITS library

export CFITSIO_INC=$HOME/lib/cfitsio/include

export CFITSIO_LIB=$HOME/lib/cfitsio/lib


export FFTW_LIBRARY=$HOME/lib/fftw/lib

export FFTW_INC_DIR=$HOME/lib/fftw/include



%#The lapack linear algebra package

%#On a mac this should just say "-framework Accelerate" or "-framework veclib" if that doesn't work

export LAPACK_LINK="-L ~/InstallCosmosis/lapack-3.6.1/liblapack.a -llapack -lblas"



%#You may need to add :${DYLD_LIBRARY_PATH} to the end of this

export DYLD_LIBRARY_PATH=/usr/lib64/libgfortran.so.3.0.0:${COSMOSIS_SRC_DIR}/cosmosis/datablock/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/planck/plc-1.0/lib/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/planck2015/plc-2.0/lib/


%#You may need to add :${LD_LIBRARY_PATH} to the end of this

export LD_LIBRARY_PATH=/usr/lib64/libgfortran.so.3.0.0:${COSMOSIS_SRC_DIR}/cosmosis/datablock/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/planck/plc-1.0/lib/:${COSMOSIS_SRC_DIR}/cosmosis-standard-library/likelihood/planck2015/plc-2.0/lib/



export PYTHONPATH=${COSMOSIS_SRC_DIR}:${PYTHONPATH}

export PATH=${COSMOSIS_SRC_DIR}/bin:/usr/bin/gcc:~/anaconda2/bin/python:${PATH}


------------------------------------------

其中那几个软件包都是在安装时用--prefix选择的位置

gcc/python的位置用which gcc/python得到

libgfortran的位置用rpm -ql libgfortran

之后在对cosmosis进行make时，libgfortran出错，使用conda update libgfortran进行修复

网上另一修复办法为

conda remove libgfortran

conda install libgcc --force

------------------------------------

回到cosmosis网页完成安装，make成功，cosmosis demos/demo1.ini成功
