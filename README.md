# DISCOVAR / DISCOVAR de novo (build 52488) 备份仓库

Broad Institute 的 **DISCOVAR** 与 **DISCOVAR de novo**（build 52488）源码与示例数据的镜像归档。

- 许可：MIT License（Copyright (c) 2015 Broad Institute），见各源码目录下的 `LICENSE`
- 安装方法（含依赖安装）：见 [INSTALL.md](INSTALL.md)

## 仓库内容

| 路径 | 说明 |
|---|---|
| `discovar-52488/` | DISCOVAR 源码树（小区域组装 + 变异检测） |
| `discovardenovo-52488/` | DISCOVAR de novo 源码树（无参考组装） |
| `INSTALL.md` | 依赖（samtools 1.2、jemalloc 3.6.0）与编译安装步骤 |
| `README.md` | 本文件 |

## 发行包与示例数据（Release `52488`）

所有压缩包均不在代码树中（原始包与示例数据单个超过 GitHub 单文件 100 MiB 上限），统一由 Release `52488` 提供：

| 附件 | 大小 | SHA-256 |
|---|---|---|
| `discovar.tar.gz` | 1,728,853 B | `c46e8f5727b3c8116d715c02e20a83e6261c762e8964d00709abfb322a501d4e` |
| `discovardenovo.tar.gz` | 2,052,519 B | `445445a3b75e17e276a6119434f13784a5a661a9c7277f5e10f3b6b3b8ac5771` |
| `discovar-examples.tar.gz` | 107,070,843 B | `bb182c050dd7978a824e93f9d6b43664dd264a1c6ab7cf5a97fca32d2e2e205b` |
| `discovardenovo-examples.tar.gz` | 106,946,593 B | `e4cb0d3712bcaa0215087b737f3c139b65852fce2cb2b2ea1c191d2ca7970df4` |

```bash
# 源码包
wget https://github.com/SiYangming/discovar/releases/download/52488/discovar.tar.gz
wget https://github.com/SiYangming/discovar/releases/download/52488/discovardenovo.tar.gz

# 示例数据包
wget https://github.com/SiYangming/discovar/releases/download/52488/discovar-examples.tar.gz
wget https://github.com/SiYangming/discovar/releases/download/52488/discovardenovo-examples.tar.gz

# 克隆源码树
git clone https://github.com/SiYangming/discovar.git
```

示例数据说明：250 bp Illumina reads，比对到人类第 10 号染色体上与已完成 Fosmid clone 序列重合的区域（Fosmid clone 21，chr10:30,892,106-30,933,760），并附整条 10 号染色体参考序列。两个示例包内容一致，`discovar-examples` 另含 `sample-genome.fasta.m100`（由 `PrepareDiscovarGenome` 生成）。

## 使用示例

以下步骤假设已按 [INSTALL.md](INSTALL.md) 安装好 DISCOVAR / DISCOVAR de novo，并把示例包解压到工作目录（`/path/to/work` 为自选路径）。

### 1. DISCOVAR de novo 组装

```bash
mkdir -p /path/to/work/DISCOVAR
cd /path/to/work/DISCOVAR

# DISCOVAR de novo 组装（无参考基因组）
tar zxf path/to/discovardenovo-examples.tar.gz
cd discovardenovo-examples/
mkdir -p discovardenovo-assembly

# 运行 DISCOVAR de novo
DiscovarDeNovo READS=sample-reads.bam OUT_DIR=discovardenovo-assembly NUM_THREADS=4

# DISCOVAR de novo 组装（有参考基因组）
mkdir discovardenovo-assembly-aligned
grep '>' sample-genome.fasta | perl -pe 's/>(\S+).*/$1/' > sample-genome.names
DiscovarDeNovo READS=sample-reads.bam OUT_DIR=discovardenovo-assembly-aligned REFHEAD=sample-genome NUM_THREADS=4
cd ..
```

### 2. DISCOVAR 组装与变异检测

DISCOVAR 支持指定参考基因组区域的组装，以及变异检测（variant calling）。

```bash
cd /path/to/work/DISCOVAR

# 使用 DISCOVAR 进行小基因组组装（有参考基因组区域）
tar zxf path/to/discovar-examples.tar.gz
cd discovar-examples
mkdir -p discovar-assembly/tmp

# 组装指定区域的基因组
Discovar READS=sample-reads.bam OUT_HEAD=discovar-assembly/genome REGIONS='10:30892106-30933760' TMP=discovar-assembly/tmp

# 使用 DISCOVAR 进行 variant calling
mkdir -p discovar-variants/tmp
PrepareDiscovarGenome REF=sample-genome.fasta
# 生成 sample-genome.fasta.m100 文件非常消耗时间

Discovar READS=sample-reads.bam REFERENCE=sample-genome.fasta REGIONS='10:30892106-30933760' OUT_HEAD=discovar-variants/genome TMP=discovar-variants/tmp
cd ..
```

### 3. 使用 NhoodInfo 查看组装信息

```bash
cd /path/to/work/DISCOVAR/discovardenovo-examples

# 使用 NhoodInfo 查看组装邻域信息
NhoodInfo OUT=out DIR_IN=discovardenovo-assembly-aligned/a.final/ SEEDS=10:30.5M COUNT=True SHOW_INV=True

# 转换为可视化格式
dot -Tsvg out.dot -o out.svg
convert out.svg out.png
```

结果解读示例：

```
2850<2851>[1.04x](+10:30,434,920-51,125)C=3346(16K)
```

- `2850`：edge id；`<2851>` 为其反向互补的 edge id
- `1.04x`：该 edge 在双倍体中的拷贝数（0.5 表示存在于单倍型中，大于 2x 表示该 edge 是重复序列）
- `+`：序列在参考序列正义链上
- `10:30,434,920-51,125`：该 edge 比对到参考序列 10:30,434,920-30,451,125 区间
- `C=3346`：测序覆盖度
- `(16K)`：该段 edge 长度
