### 在Xcode 15 Build 出来的 assets的图片，使用4位png会失真，丢失alpha导致图片显示出问题
#### 用下面的脚步统一修改(MacOS)
```sh
#!/bin/bash

# 指定图片文件夹路径
input_folder="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"

# 遍历文件夹内的 PNG 图片
find "$input_folder" -type f -iname "*.png" | while read -r file; do
    # 使用 sips 命令获取图片信息
    bitsPerSample=$(sips -g bitsPerSample "$file" | awk '/bitsPerSample:/{print $2}')
    # 检查 bitsPerSample 是否小于等于 4
    if [ "$bitsPerSample" -le 4 ]; then
        # 输出 8bit 通道的图片（覆盖原始图片）
        sips -s format png --setProperty formatOptions 8 "$file" --out "$file"
        # 打印替换信息
        echo "Replaced: $file"
    fi
done

```
