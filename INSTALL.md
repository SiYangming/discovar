# DISCOVAR / DISCOVAR de novo (build 52488) 安装备忘

两个发行包均来自 Broad Institute，许可为 MIT（见各自目录下的 `LICENSE`）。

原始压缩包不在代码树中，改由本仓库 Release `52488` 提供：

| 文件 | 大小 | SHA-256 |
|---|---|---|
| `discovar.tar.gz` | 1,728,853 B | `c46e8f5727b3c8116d715c02e20a83e6261c762e8964d00709abfb322a501d4e` |
| `discovardenovo.tar.gz` | 2,052,519 B | `445445a3b75e17e276a6119434f13784a5a661a9c7277f5e10f3b6b3b8ac5771` |

## 1. 下载

本仓库地址：https://github.com/SiYangming/discovar

```bash
# 从本仓库 Release 下载两个发行包
wget https://github.com/SiYangming/discovar/releases/download/52488/discovar.tar.gz
wget https://github.com/SiYangming/discovar/releases/download/52488/discovardenovo.tar.gz

# 或
curl -L -O https://github.com/SiYangming/discovar/releases/download/52488/discovar.tar.gz
curl -L -O https://github.com/SiYangming/discovar/releases/download/52488/discovardenovo.tar.gz

# 或克隆完整源码树（含 discovar-52488/ 与 discovardenovo-52488/）
git clone https://github.com/SiYangming/discovar.git
```

## 2. 前置条件

- 编译需要支持 C++11 的较新 GCC（建议 4.8 及以上）。系统自带 GCC 过旧时（如 CentOS 6 的 4.4）需先安装/切换新版 GCC，例如先 `source` 一份配置好新版 GCC 的环境脚本
- DISCOVAR 运行需要 samtools
- DISCOVAR de novo 另需 jemalloc

## 3. 安装依赖

### samtools 1.2

```bash
wget https://github.com/samtools/samtools/releases/download/1.2/samtools-1.2.tar.bz2
tar jxf samtools-1.2.tar.bz2 -C /path/to/install/
cd /path/to/install/samtools-1.2/
make -j 4

# 添加环境变量
echo 'PATH=$PATH:/path/to/install/samtools-1.2/' >> ~/.bashrc
source ~/.bashrc
```

### jemalloc 3.6.0

```bash
wget https://github.com/jemalloc/jemalloc/releases/download/3.6.0/jemalloc-3.6.0.tar.bz2
tar jxf jemalloc-3.6.0.tar.bz2
cd jemalloc-3.6.0/
./configure && make -j 4 && sudo make install
cd ..
rm -rf jemalloc-3.6.0/
```

## 4. 安装 DISCOVAR

```bash
tar zxf discovar.tar.gz
cd discovar-52488/
./configure --prefix=/path/to/install/discovar && make -j 4 && make install
cd ..

# 添加环境变量
echo 'PATH=$PATH:/path/to/install/discovar/bin/' >> ~/.bashrc
source ~/.bashrc

rm -rf discovar-52488/
```

## 5. 安装 DISCOVAR de novo

```bash
tar zxf discovardenovo.tar.gz
cd discovardenovo-52488/
./configure --prefix=/path/to/install/discovardenovo && make -j 4 all && make install
cd ..

# 添加环境变量
echo 'PATH=$PATH:/path/to/install/discovardenovo/bin/' >> ~/.bashrc
source ~/.bashrc

rm -rf discovardenovo-52488/
```

## 说明

- `/path/to/install` 为自选安装目录（如 `/opt/biosoft`），`path/to/xxx.tar.gz` 与 `path/to/xxx.tar.bz2` 为各发行包所在路径
- `discovar-52488/`、`discovardenovo-52488/` 为两个项目的源码目录，`configure` + `make` + `make install` 完成后可删除解压目录
- 许可：MIT License（Copyright (c) 2015 Broad Institute）
