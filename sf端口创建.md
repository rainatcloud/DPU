
#创建bond之后，SF端口都会丢失，需要重建SF端口

#创建一个88的sf端口，mlxdevm在这个路径
/opt/mellanox/iproute2/sbin/mlxdevm port add pci/0000:03:00.0 flavour pcisf pfnum 0 sfnum 88

/opt/mellanox/iproute2/sbin/mlxdevm port show en3f0pf0sf88

#设一下Mac地址
mlxdevm port function set pci/0000:03:00.0/229377 hw_addr 00:00:00:00:88:88
