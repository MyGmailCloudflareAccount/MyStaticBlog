---
title: shell脚本的一种混淆方式
date: 2024-12-09 11:30:55
tags: []
categories: 默认
---

灵感来源于：[rwkgyg/sing-box-yg](https://gitlab.com/rwkgyg/sing-box-yg)

目前各种 PaaS、SaaS、BaaS 等等平台，在用来托管一些常用的应用——如 Xray、Alist 等等应用时，经常会提示违规，不允许部署。在经过大量的测试后，我发现此类平台多以关键字检测作为封禁的因素，所以只要做到日志、文件名、文本文件内容不含此类关键字，就能继续部署，正常使用。

多数情况下，部署此类应用都是通过一个功能完善的 Shell 程序自动化进行，在其中把文件名和日志做好处理即可，但是最后 shell 脚本本身的内容又不可避免的会出现 Xray、Alist 等应用的下载链接，导致 Shell 脚本本身被平台查处封禁。当然通过多个变量拆分构造下载链接避开关键字也是一种处理方法，但是无疑这增加了脚本的编写复杂度，所以在通过查看 rwkgyg/sing-box-yg 时我获取了灵感，编写了一个 Python 脚本专门用于混淆 Shell 脚本：

**PYTHON**

```
import os
import uuid
import base64

# 读取你的脚本
with open('script.sh', 'r') as f:
    script = f.read()

# 使用 base64 对你的脚本进行编码
encoded_script = base64.b64encode(script.encode()).decode()

# 定义每个字符串段的大小
chunk_size = 4

# 将编码后的脚本分割成多个小段
chunks = [encoded_script[i:i+chunk_size]
          for i in range(0, len(encoded_script), chunk_size)]

# 创建一个新的文件来存储最终的脚本
with open('final_script.sh', 'w') as f:
    # 输出每个变量赋值语句
    variable_names = []
    for chunk in chunks:
        while True:
            var_name = str(uuid.uuid4()).replace('-', '')  # 生成一个 UUID 作为变量名
            if not var_name.startswith('0') and not var_name.startswith('1') and not var_name.startswith('2') and not var_name.startswith('3') and not var_name.startswith('4') and not var_name.startswith('5') and not var_name.startswith('6') and not var_name.startswith('7') and not var_name.startswith('8') and not var_name.startswith('9'):
                break
        variable_names.append(var_name)
        f.write(f'{var_name}=\'{chunk}\'\n')

    # 输出连接所有变量的语句
    f.write('eval "$(echo -n "')
    for var_name in variable_names:
        f.write(f'${var_name}')
    f.write('" | base64 --decode)"')

# 修改文件权限，使其可执行
os.chmod('final_script.sh', 0o755)
```

这个 Python 脚本的原理很简单，就是先把原 Shell 脚本使用 Base64 编码，然后分割成很多个字符串，再使用 UUID 作为变量名，把每个字符串都存储在一个变量中，最后把 UUID 变量全都连起来，使用 `eval` 命令执行。

简单粗暴，效果很好，用了的都说好。
