---
title: 配置环境及NEMO源代码下载
date: 2024-03-21 23:32:53
tags: Model
---

# 0. 了解超算及编译环境

- 硬件信息
  ```bash
  lscpu
  ```

  

- 系统信息
  ```bash
  cat /etc/os-release
  ```

- 查看自带的软件

  ```bash
  module avail
  ```

- 提交作业的系统----LSF作业调度系统

  ```bash
  bsub -q ocean_530 -o %J.out mpirun -n 8 ./nemo # 提交命令
  bjobs -l JobID # 查看作业运行状况
  ```

    [基本操作参考](https://scc.ustc.edu.cn/zlsc/user_doc/html/lsf/lsf.html)

- 师姐之前装好的
  conda、cdo-1.9.9、ncl（在conda的ncl_stable环境里）、mpich3.0.4 、NETCDF4.1.3、Cmake、ECCODES

# 一、安装XIOS的依赖项

## 初始的`.bashrc`
``` bash
# .bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
#module load

module load python/3.7.3
module load mkl/2019.1.144
module load gcc/4.9.0

# complier
export CC="gcc"
export CXX="g++"
export FC="gfortran"
export COMPILER="$FC"
export FCFLAGS="-m64"
export F77="gfortran"


# Uncomment the following line if you don't like systemctl's auto-paging feature:
# export SYSTEMD_PAGER=
export HOME="/data/gpfs01/dir1"
export MR_DIR="/data/gpfs01/dir1/Build_WRF/LIBRARIES"
```
## 文件树结构
```bash
/  $HOME 
	|--dir_name2 # directory of linux 
		|--Build_NEMO # main bulid NEMO directory. 
			|--LIBRARIES # NEMO library directory. 
			|--src # source code directory & all tar.gz files. 
			|--nemo_4.2.0 # NEMO directory.
```
## 下载安装文件
 下载所需要的依赖包文件，编译和安装
```bash
wget http://www.zlib.net/fossils/zlib-1.2.11.tar.gz
wget http:........etc.
```
所有需要的依赖包见下图

![](https://note.youdao.com/yws/public/resource/61443f14facef299c91e9dd9000e2b11/xmlnote/WEBRESOURCEaa8271f1c91e8248b4f27f89eecfbaf9/18)

## 编译包
每安装一个包，要将它的所处路径写进`.bashrc`中。
1. 安装zlib
```bash
  tar xvzf zlib-1.2.11.tar.gz
  cd zlib-1.2.11
  
  ./configure --prefix=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/zlib
  
  make -j8 |& tee make_log.txt
  make install |& tee make_install.txt
```
2. 安装curl
```shell
  tar xvzf curl-7.77.0.tar.gz
  cd curl-7.77.0
  
  ./configure --prefix=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/curl --with-zlib=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/zlib --without-ssl
  
  make -j8 |& tee make_log.txt
  make install |& tee make_install.txt
```
3. 安装hdf5
```bash
  tar xzvf hdf5-1.12.1.tar.gz 
  cd hdf5-1.12.1 
  
  ./configure --prefix=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5 --with-zlib=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/zlib --enable-fortran --enable-static=yes --enable-parallel --enable-shared   --enable-fortran2003  --enable-unsupported
  
  make -j8 |& tee make_log.txt
  
  make install |& tee make_install.txt
```
4. 安装parallel-NetCDF
```bash
  tar vxzf pnetcdf-1.12.3.tar.gz
  cd pnetcdf-1.12.3
  
  export CPPFLAGS="-I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/include -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/zlib/include " 
  export LDFLAGS="-L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/zlib/lib " 
  
  ./configure --prefix=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/PNETCDF-1.12.3  --enable-shared --enable-large-file-test --enable-null-byte-header-padding --enable-burst-buffering --enable-profiling --enable-static
  
  make -j8 |& tee make_log.txt
  
  make install |& tee make_install.txt
```
5. 安装netcdf-c-4.9.2
	(因为4.8版本有一个和HDF51.12 通不过的测试) 要逐行粘贴
	
	
```bash
  tar xvzf netcdf-c-4.9.2.tar.gz
  cd netcdf-c-4.9.2
  
  export CPPFLAGS="-I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/curl/include -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/include -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/PNETCDF-1.12.3/include"
  export LDFLAGS="-L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/curl/lib -L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib -Wl,-rpath=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib -L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/PNETCDF-1.12.3/lib" 
  
  ./configure --prefix=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf --with-hdf5=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5 --enable-shared --enable-netcdf-4 --enable-netcdf4 --enable-dap --with-pic --disable-doxygen --enable-static --enable-largefile --disable-libxml2  --enable-pnetcdf --enable-parallel-tests 
  
  make -j8 |& tee make_log.txt
  
  make install |& tee make_install.txt
```
- 一定要添上这两个选项`--enable-netcdf-4 --enable-netcdf4`，否则在XIOS安装时会报错
	检查是否打开的命令：`nc-config --all`
	
	![](https://note.youdao.com/yws/public/resource/61443f14facef299c91e9dd9000e2b11/xmlnote/WEBRESOURCEfafa9db45124243e38f8874d8a6431f6/21)
	
	6. 安装netcdf-fortran 
		在这步之前提前安装了mpich，并将环境变量改了，见8
	```bash
	  tar xvzf netcdf-fortran-4.6.0.tar.gz
	  cd netcdf-fortran-4.6.0
	  
	export CC=mpicc -lstdc++
	#  告知库文件位置
	export LD_LIBRARY_PATH="/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/lib: LD_LIBRARY_PATH"
	export CPPFLAGS="-I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/include -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/include"
	export LDFLAGS="-L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib -L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/lib -Wl,-rpath=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib -Wl,-rpath=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/lib "
	export CFLAGS="-L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib -L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/lib -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/include -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/include"
	export CXXFLAGS="-L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib -L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/lib -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/include -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/include"
	export FCFLAGS="-L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib -L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/lib -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/include -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/include"
	
	#设置参数
	 CPPFLAGS="-I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/include  -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/include" \ 
	  LDFLAGS="-L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/lib -L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib"  \
	 ./configure --prefix=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf --enable-shared --with-pic --disable-doxygen  --enable-largefile --enable-static
	# 编译
	make -j8 |& tee make_log.txt
	
	#安装
	make install |& tee make_install.txt
	```
	7. 安装netcdf-cxx
	```bash
	tar xzvf netcdf-cxx4-4.3.1.tar.gz
	cd netcdf-cxx4-4.3.1
	# configure
	CPPFLAGS="-I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/include -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/include"  
	LDFLAGS="-L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/lib -L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib"  
	 LIBS="-L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib -L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/lib -lhdf5_hl -lhdf5 -lz  -lnetcdf -lnetcdff"
	./configure --prefix=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf --enable-shared --with-pic --disable-doxygen  --enable-largefile --enable-static
	
	#编译
	make -j8 |& tee make_log.txt
	#安装
	make install |& tee make_install.txt
	```
	8. 安装mpich(--with-device=ch4:ofi,加上这个选项编译)
	```bash
	tar xzvf mpich-4.1.1.tar.gz
	cd mpich-4.1.1
	
	./configure --prefix=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/mpich-4.1.1 --with-device=ch4:ofi
	
	#编译
	make -j8 |& tee make_log.txt
	#安装
	make install |& tee make_install.txt
	
	```
	9. 修改环境变量，最后的`.bashrc`文件如下：
	```bash
	# .bashrc
	
	# Source global definitions
	if [ -f /etc/bashrc ]; then
	        . /etc/bashrc
	fi
	#module load
	
	module load python/3.7.3
	module load mkl/2019.1.144
	module load gcc/4.9.0
	
	
	
	# Uncomment the following line if you don't like systemctl's auto-paging feature:
	# export SYSTEMD_PAGER=
	export HOME="/data/gpfs01/dir1"
	export MR_DIR="/data/gpfs01/dir1/Build_WRF/LIBRARIES"
	
	# complier
	export CC=mpicc
	export CXX=mpicxx
	export FC=mpif90
	export F77=mpif77
	export COMPILER="$FC"
	
	
	#export CC="gcc"
	#export CXX="g++"
	#export FC="gfortran"
	#export COMPILER="$FC"
	#export FCFLAGS="-m64"
	#export F77="gfortran"
	
	# for PnetCDF
	export PATH="$HOME/dir_name2/Build_NEMO/LIBRARIES/PNETCDF-1.12.3/bin:$PATH"
	export LD_LIBRARY_PATH="$HOME/dir_name2/Build_NEMO/LIBRARIES/PNETCDF-1.12.3/lib:$LD_LIBRARY_PATH"
	
	# for NETCDF
	export NETCDF="$HOME/dir_name2/Build_NEMO/LIBRARIES/netcdf"
	export PATH="$HOME/dir_name2/Build_NEMO/LIBRARIES/netcdf/bin:$PATH"
	export LD_LIBRARY_PATH="$NETCDF/lib:$LD_LIBRARY_PATH"
	
	# for curl
	export PATH="$HOME/dir_name2/Build_NEMO/LIBRARIES/curl/bin:$PATH"
	export LD_LIBRARY_PATH="$HOME/dir_name2/Build_NEMO/LIBRARIES/curl/lib:$LD_LIBRARY_PATH"
	
	### HDF5
	export PATH="$HOME/dir_name2/Build_NEMO/LIBRARIES/hdf5/bin:$PATH"
	export LD_LIBRARY_PATH="$HOME/dir_name2/Build_NEMO/LIBRARIES/hdf5/lib:$LD_LIBRARY_PATH"
	
	# for MY MPICH
	export MPICH="/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/mpich-4.1.1"
	export PATH="$MPICH/bin:$PATH"
	export LD_LIBRARY_PATH="$MPICH/lib:$LD_LIBRARY_PATH"
	```
	# 二、安装XIOS
	1. 下载XIOS仓库
	```bash
	   cd ~/dir_name2/Build_NEMO/LIBRARIES/
	   svn co http://forge.ipsl.jussieu.fr/ioserver/svn/XIOS/trunk xios-t
	```
	2. 复制并修改`arch`文件夹里的配置（根据学校的超算信息和所用的编译器，这里是用的gcc/4.9.0。用intel的试了几遍老是报错）
	   参考信息：
	   [XIOS-Trunk+nemo4.2.0+gcc8.3.0](https://nemo-related.readthedocs.io/en/latest/compilation_notes/nemo42.html )
	   [XIOS-2+nemo+Intel](http://christopherbull.com.au/nemo/nemo-wed12-01/)
	   [NEMO4.2.0-User-guide](https://sites.nemo-ocean.io/user-guide/install.html#)
	   - `arch-XMU_HPC.fcm`的内容
	```bash
	##############################################################################
	###################                Projet XIOS               ###################
	################################################################################
	
	%CCOMPILER	mpicc
	%FCOMPILER	mpif90
	%LINKER     mpif90
	
	%BASE_CFLAGS    -ansi -w -D_GLIBCXX_USE_CXX11_ABI=0  -std=c++11
	#%BASE_CFLAGS    -ansi -w
	%PROD_CFLAGS    -O3 -DBOOST_DISABLE_ASSERTS
	%DEV_CFLAGS     -g -O2
	%DEBUG_CFLAGS   -g
	
	%BASE_FFLAGS    -D__NONE__
	%PROD_FFLAGS    -O3
	%DEV_FFLAGS     -g -O2
	%DEBUG_FFLAGS   -g
	
	%BASE_INC	      -D__NONE__
	%BASE_LD        -lstdc++
	#-mkl=parallel
	#%BASE_LD        -mkl=parallel
	
	%CPP            cpp
	%FPP            cpp -P
	%MAKE           make
	```
	   - `arch-XMU_HPC.env`的内容
	```bash
	export HDF5_INC_DIR=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/include
	export HDF5_LIB_DIR=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/lib
	
	export NETCDF_INC_DIR=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/include
	export NETCDF_LIB_DIR=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/lib
	
	#export BLITZ_INC_DIR="/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/blitz/include"
	#export BLITZ_LIB_DIR="/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/blitz/lib"
	
	#export ZLIB_INC_DIR=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/zlib/include
	#export ZLIB_LIB_DIR=/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/zlib/lib
	```
	   - `arch-XMU_HPC.path`的内容
	```bash
	NETCDF_INCDIR="-I$NETCDF_INC_DIR"
	NETCDF_LIBDIR="-Wl,'--allow-multiple-definition' -L$NETCDF_LIB_DIR"
	#NETCDF_LIBDIR="-L$NETCDF_LIB_DIR"
	NETCDF_LIB="-lnetcdf -lnetcdff"
	
	#MPI_INCDIR=""
	#MPI_LIBDIR=""
	#MPI_LIB=""
	
	HDF5_INCDIR="-I$HDF5_INC_DIR"
	HDF5_LIBDIR="-L$HDF5_LIB_DIR"
	HDF5_LIB="-lhdf5_hl -lhdf5 -lhdf5 -lz"
	
	#ZLIB_INCDIR="-I $ZLIB_INC_DIR"
	#ZLIB_LIBCDIR="-I $ZLIB_LIB_DIR"
	#ZLIB_LIB="-lz"
	
	#BOOST_INCDIR="-I $BOOST_INC_DIR"
	#BOOST_LIBDIR="-L $BOOST_LIB_DIR"
	#BOOST_LIB=""
	
	#这个耦合器是后来装的可写可不写
	OASIS_INCDIR="-I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/OASIS3-MCT_5.0/include"
	OASIS_LIBDIR="-L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/OASIS3-MCT_5.0/lib"
	OASIS_LIB="-lpsmile.MPI1 -lscrip -lmct -lmpeu"
	
	#BLITZ_INCDIR="-I /YOUR_HOME_DIR//Build_NEMO/LIBRARIES/blitz/include"
	#BLITZ_LIBDIR="-L /YOUR_HOME_DIR//Build_NEMO/LIBRARIES/blitz/lib"
	#BLITZ_LIB="-lblitz"
	```
	3. 修改根目录下的`bld.cfg`文件其中的一行
	```bash
	cd ../
	
	nano bld.cfg
	#修改这行
	bld::tool::cflags    %CFLAGS %CBASE_INC -I${PWD}/extern/src_netcdf -I${PWD}/extern/boost/include -I${PWD}/extern/rapidxml/include -I${PWD}/extern/blitz/include
	```
	   其中的`src_netcdf`改为`src_netcdf4`
	
	4. 回到xios的目录下，编译(这个时间巨长)
	```
	./make_xios --full --prod --arch XMU_HPC -j8 |& tee compile_log.txt
	```
	ps：xios-2.5版本可以用在nemo-4.0.7版本，但是nemo4.2不能用2.5版本，需要用到中间版本（就是我们下载的这个）；（nemo4.2用XIOS3的时候会报错显示perl5的错误，具体要怎么修改没有去看，因为中间版本可以用了，实在是懒得折腾了）
	
	# 三、下载NEMO源代码并配置
	- 下载代码
	因为nemo4.2不再支持svn了，需要用git，可以先在本地下载好，用ftp传送到服务器上。本地下载的命令是：
	`git clone https://forge.nemo-ocean.eu/nemo/nemo.git nemo_4.2.0`
	- 在`arch`文件夹下，创建并修改`arch-XMU_HPC.fcm`
	```bash
	
	%NCDF_HOME           /YOUR_HOME_DIR//Build_NEMO/LIBRARIES/netcdf/
	%HDF5_HOME           /YOUR_HOME_DIR//Build_NEMO/LIBRARIES/hdf5/
	%XIOS_HOME           /YOUR_HOME_DIR//Build_NEMO/LIBRARIES/xios-t/
	
	
	%OASIS_INC           -I/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/OASIS3-MCT_5.0/build-static/lib/mct -I/dat$
	%OASIS_LIB           -L/YOUR_HOME_DIR//Build_NEMO/LIBRARIES/OASIS3-MCT_5.0/lib -lpsmile.MPI1 -lmct -lm$
	
	%NCDF_INC            -I%NCDF_HOME/include -I%NCDF_HOME/include
	%NCDF_LIB            -L%NCDF_HOME/lib -L%NCDF_HOME/lib -lnetcdff -lnetcdf -lstdc++
	%XIOS_INC            -I%XIOS_HOME/inc
	%XIOS_LIB            -L%XIOS_HOME/lib -lxios
	
	%CPP                 cpp -Dkey_nosignedzero
	%CPPFLAGS            -P -traditional
	
	%FC                  mpif90
	%FCFLAGS             -fdefault-real-8 -O3 -funroll-all-loops -fcray-pointer -cpp -ffree-line-length-none
	%FFLAGS              %FCFLAGS
	%LD                  mpif90
	%FPPFLAGS           -P -C  -traditional
	%LDFLAGS
	#%LDFLAGS             -kml=parallel
	%AR                  ar
	%ARFLAGS             -rs
	%MK                  make
	%USER_INC            %XIOS_INC %OASIS_INC  %NCDF_INC
	%USER_LIB            %XIOS_LIB %OASIS_LIB  %NCDF_LIB
	
	#%CC                  mpicc
	#%CFLAGS              -O3 -march=native -mtune=native
	```
	最后的文件夹树的样子：
	
	![](https://note.youdao.com/yws/public/resource/61443f14facef299c91e9dd9000e2b11/xmlnote/WEBRESOURCEd2da1d052817ef60031b8134959959b5/23)
	
- 

