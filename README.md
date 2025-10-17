# 能够通过编译的方法

## 修改 /etc/systemd/zram-generator.conf 文件
增大虚拟内存 是本机内存的 2 倍

```
# zram-size = min(ram, 8192)
zram-size = ram * 2
compression-algorithm = zstd
```