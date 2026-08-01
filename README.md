# 办公自动化实战：5 个即用 Python 脚本（数据清洗 / 批量处理）

每天处理 Excel 到吐？批量改文件到手软？跨系统导出的数据全是乱码和重复？这几个脚本拿去就能用，帮你把重复劳动压缩到几分钟。

## 1. 自动编码检测 + 乱码修复（最刚需）
同事发的 CSV 常是 GBK 编码，pandas 直接读就乱码。自动检测编码再读：

```python
import chardet, pandas as pd

def smart_read_csv(path):
    with open(path, 'rb') as f:
        enc = chardet.detect(f.read(10000))['encoding']
    return pd.read_csv(path, encoding=enc)
```

## 2. 一键去重（按指定列）
几千行数据里有重复手机号/订单号，一行搞定：

```python
df = df.drop_duplicates(subset=['手机号'], keep='first')
```

## 3. 手机号清洗（批量修复格式）
导出数据里手机号格式五花八门：`138 1234 5678`、`+86-13812345678`、`138-1234-5678`……统一成标准格式：

```python
import re
def clean_phone(p):
    p = re.sub(r'[^\d]', '', str(p))
    if p.startswith('86') and len(p) == 13:
        p = p[2:]
    return p if len(p) == 11 else None

df['手机号'] = df['手机号'].apply(clean_phone)
```

## 4. 批量重命名文件（按规则）
文件夹里几百个文件要按日期/序号重命名：

```python
import os, glob
for i, f in enumerate(glob.glob('/data/*.pdf'), 1):
    os.rename(f, f'/data/发票_{i:03d}.pdf')
```

## 5. 自动生成汇总日报
把 12 个分公司的 Excel 合并成一张总表：

```python
import pandas as pd, glob
frames = [pd.read_excel(f) for f in glob.glob('/data/分公司*.xlsx')]
pd.concat(frames).to_excel('/data/汇总日报.xlsx', index=False)
```

---

## 需要更复杂的定制？
我提供 **Python 脚本定制开发服务**：
- 数据清洗 / 表格合并拆分 / 报表自动生成：¥200-800
- 定向爬虫（公开数据抓取）：¥500-1500
- 办公自动化流程（批量处理、定时任务、邮件自动收发）：¥800-2000

**交付物**：完整可运行代码 + 使用说明 + 本地测试通过截图，先付 50% 定金，验收后付尾款。
有任何数据处理需求，欢迎在 GitHub 上联系我（issue/讨论区留言），说明你的场景，我 24 小时内回复报价。