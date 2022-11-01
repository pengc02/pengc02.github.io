---
layout:     post
title:      "Pack a Python Project into EXE"
date:       2022-10-12 
author:     "Pengc"
catalog: false
published: true
header-style: text
excerpt_separator: <!--more-->
tags:
  - python
---

弄了一晚上才把大作业封装起来<!--more-->

> 网上的博客写的比较乱，这里整理一下并说一下我成功的完整操作

1. 安装pyinstaller，pip install即可

2. 在工作目录下生成一个spec后缀文件

   命令是：`pyi-makespec -F -w -i favicon.ico main.py`

   其中ico文件是一张图片，作为你的exe文件图标，main.py是你的主接口文件名

3. 在spec文件中添加附属数据（图片音频等），重点要修改一下几处：

   ```shell
   a = Analysis(
       ['UI.py'], # 这里无需再加其它的py文件名 
       pathex=['D:\Desktop\人工智能基础大作业一\code'], # 这里需要放这个文件夹的绝对路径
       binaries=[],
       datas=[('basic-pics','basic-pics'),('jsonLevels','jsonLevels'),('newpics','newpics'),('font','font')], # 这里是附属数据，每一个元祖前面是本地文件夹名称，后面是你希望它在exe中文件夹名称 
       hiddenimports=[],
       hookspath=[],
       hooksconfig={},
       runtime_hooks=[],
       excludes=[],
       win_no_prefer_redirects=False,
       win_private_assemblies=False,
       cipher=block_cipher,
       noarchive=False,
   )
   ```

4. 在主接口py文件的所有代码之前插入以下代码，来维护路径的正确性;

   ```python
   import os
   import sys
   
   # 资源文件目录访问
   def source_path(relative_path):
       # 是否Bundle Resource
       if getattr(sys, 'frozen', False):
           base_path = sys._MEIPASS
       else:
           base_path = os.path.abspath(".")
       return os.path.join(base_path, relative_path)
   
   # 修改当前工作目录，使得资源文件可以被正确访问
   cd = source_path('')
   os.chdir(cd)
   ```

5. 完成上述所有操作后，最后一步输入以下命令生成exe：

   ```shell
   pyinstaller main.spec
   ```

6. 在工作目录下的dist文件夹中应当有一个exe文件。
