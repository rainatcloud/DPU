
# MLNX SNAP基于NVIDIA内部的SPDK分支编译，但可以与其他SPDK版本一起工作，前提是mlnx-snap源码必须基于新的SPDK代码重新编译
# 并且新的SPDK版本变化并不改变外部的SPDK API逻辑
# 以下命令都在BF-2 SOC里执行

# 下载Spdk代码，选择相应的版本，
git clone https://github.com/spdk/spdk
cd spdk
git tag -l

# 当前选择21.07版本
git checkout tags/v21.07
git submodule update --init

# 也可以忽略之前两步，直接下载相应版本代码到Bluefield SOC

# 安装librados和librbd
yum install -y librbd-devel.aarch64
# 配置spdk选项，这里选择加上rbd，需要先安装librados和librbd, 最后一个选项不能落下~~
./configure --prefix=/opt/mellanox/spdk-custom --disable-tests --disable-unit-tests --without-crypto --without-fio --with-vhost --without-pmdk --with-rbd --with-rdma --with-shared --with-iscsi-initiator --without-vtune --without-isal

# 编译
make && sudo make install

# 拷贝lib库和头文件
cp -r dpdk/build/lib/* /opt/mellanox/spdk-custom/lib/
cp -r dpdk/build/include/* /opt/mellanox/spdk-custom/include/

# 编译snap，首先要有mlnx_snap的源码
# 本文档用的源码是：mlnx-snap-3.5.0-12.mlnx.el8.src.rpm
./configure --with-snap --with-spdk=/opt/mellanox/spdk-custom --without-gtest --prefix=/usr
make -j8 && sudo make install

# Append additional custom libraries to the mlnx-snap application. 
Set LD_PRELOAD="/opt/mellanox/spdk/lib/libspdk_custom_library.so"

# Run application.
