`sed`（Stream Editor）是 Linux 中一个强大的文本处理工具，常用于对输入流（文件或管道）进行基本的文本转换。以下是 `sed` 命令的一些**常用场景和示例**：

---

### 1. **替换文本（最常用）**
```bash
# 将每行第一个匹配的 old 替换为 new
sed 's/old/new/' file.txt

# 全局替换（每行所有匹配项）
sed 's/old/new/g' file.txt

# 忽略大小写替换
sed 's/old/new/gi' file.txt

# 替换第 n 次出现（例如第2次）
sed 's/old/new/2' file.txt
```

> 💡 默认不修改原文件，如需就地修改，加 `-i` 选项：
```bash
sed -i 's/old/new/g' file.txt
```

---

### 2. **删除行**
```bash
# 删除空行
sed '/^$/d' file.txt

# 删除包含特定字符串的行
sed '/pattern/d' file.txt

# 删除第 3 行
sed '3d' file.txt

# 删除第 2 到第 5 行
sed '2,5d' file.txt
```

---

### 3. **打印特定行（配合 `-n` 使用）**
```bash
# 打印第 5 行
sed -n '5p' file.txt

# 打印包含 "error" 的行
sed -n '/error/p' file.txt

# 打印第 10 到 20 行
sed -n '10,20p' file.txt
```

---

### 4. **在指定位置插入/追加文本**
```bash
# 在第 3 行前插入一行
sed '3i\New line before line 3' file.txt

# 在第 3 行后追加一行
sed '3a\New line after line 3' file.txt

# 在匹配行后追加
sed '/pattern/a\Inserted line' file.txt
```

---

### 5. **多命令操作（使用 `-e` 或 `{}`）**
```bash
# 同时执行多个替换
sed -e 's/foo/bar/g' -e 's/baz/qux/g' file.txt

# 或用分号
sed 's/foo/bar/g; s/baz/qux/g' file.txt
```

---

### 6. **使用正则表达式**
```bash
# 将所有数字替换为 [NUMBER]
sed 's/[0-9]\+/[NUMBER]/g' file.txt

# 删除以 # 开头的注释行（保留空行）
sed '/^#/d' file.txt
```

---

### 7. **从文件中读取脚本（-f）**
```bash
# script.sed 内容：
# s/foo/bar/g
# /debug/d

sed -f script.sed input.txt
```

---

### 8. **备份原文件（与 -i 配合）**
```bash
# 修改文件同时生成 .bak 备份
sed -i.bak 's/old/new/g' file.txt
```

---

### 9. **提取字段（类似 cut，但更灵活）**
```bash
# 提取冒号分隔的第一列（如 /etc/passwd）
sed 's/:.*$//' /etc/passwd
```

---

### 10. **组合使用（管道）**
```bash
# 查找日志中含 ERROR 的行，并替换时间戳为 [TIME]
grep "ERROR" app.log | sed 's/[0-9]\{2\}:[0-9]\{2\}:[0-9]\{2\}/[TIME]/'
```

---

### 注意事项：
- `sed` 默认使用 **基本正则表达式（BRE）**，如需扩展正则（ERE），加 `-r`（GNU sed）或 `-E`（macOS/BSD）。
- macOS 上的 `sed` 与 Linux（GNU sed）略有差异，跨平台脚本建议测试兼容性。

