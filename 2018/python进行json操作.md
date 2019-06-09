---
title: " python进行json操作\t\t"
tags:
  - json
  - python
url: 1055.html
id: 1055
categories:
  - python
date: 2018-04-02 11:14:02
---

介绍
--

python有json模块，直接import json即可使用。主要有四个方法: dumps将python类型转换为json字符串 loads将json字符串转换成python类型 dump将存有python类型的文件直接转为json字符串 load将存有json格式的文件转为python类型 -s方法是直接进行数据类型转换，而-方法则是包含了文件操作，可以通过import json;help(json.XXX)获得相关帮助，部分帮助内容如下：

帮助文档
----

1.  dump(obj, fp, *, skipkeys=False, ensure\_ascii=True, check\_circular=True, allow\_nan=True, cls=None, indent=None, separators=None, default=None, sort\_keys=False, **kw) Serialize \`\`obj\`\` as a JSON formatted stream to \`\`fp\`\` (a ``.write()``-supporting file-like object).
2.  dumps(obj, *, skipkeys=False, ensure\_ascii=True, check\_circular=True, allow\_nan=True, cls=None, indent=None, separators=None, default=None, sort\_keys=False, **kw) Serialize \`\`obj\`\` to a JSON formatted \`\`str\`\`.
3.  load(fp, *, cls=None, object\_hook=None, parse\_float=None, parse\_int=None, parse\_constant=None, object\_pairs\_hook=None, **kw) Deserialize \`\`fp\`\` (a ``.read()``-supporting file-like object containing a JSON document) to a Python object.
4.  loads(s, *, encoding=None, cls=None, object\_hook=None, parse\_float=None, parse\_int=None, parse\_constant=None, object\_pairs\_hook=None, **kw) Deserialize \`\`s\`\` (a \`\`str\`\`, \`\`bytes\`\` or \`\`bytearray\`\` instance containing a JSON document) to a Python object.

python-json类型对应关系
-----------------

python类型→json类型 dict→object list, tuple→array str, unicode→string int, long, float→number True,False→true,false None→null