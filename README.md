# 可续查电站湖表的并行整理与回收

这是本地演练用的合成来源资料。CSV 使用 UTF-8、有首行字段名，空字段表示未提供。除非字段语义明确为零，空值没有数值含义。

## 资料契约

source_files 给出来自场站落地文件的文件分组，一个 file_id 可以含多行，hour 与 at 均为 UTC 时间。record_id 在全湖标识采样记录，业务从未被撤回的版本取最高 rev。reader_requests 给出人工留档和查询工单。tick 指测试时钟秒，期限边界为 tick 到期，ttl 不含到期时刻。max_rows 是读页行数。练习中保留当前头和退位未满 20 秒的版本，另计留档与租约。维护工单的 compact 可在读完旧内容后延迟提交，读者和写入进度由调用时间控制。物理存储可选，须能核对真实对象是否存在。

以下文件位于 `data/`。

- `source_files.csv`（8 条）：`wave`、`file_id`、`record_id`、`rev`、`hour`、`at`、`meter`、`watts`
- `retractions.csv`（3 条）：`wave`、`retraction_id`、`record_id`、`rev`
- `maintenance_work.csv`（5 条）：`request`、`tick`、`verb`、`hour`、`target`
- `reader_requests.csv`（7 条）：`request`、`tick`、`verb`、`target`、`ttl`、`max_rows`

`manifest.json` 记录完整资料包的列名、行数、校验值和跨表引用。完整资料包可含业务冲突或同一来源的多次投递，校验器保留这些原始记录。

## 本地查看

适用 Python 3.11，无需安装第三方库或构建。

```sh
python3 inspect_inputs.py
python3 -m unittest -v test_input_pack
```

查看命令打印各原始表的记录数。单元测试校验资料结构、引用与文件完整性，可离线执行。
