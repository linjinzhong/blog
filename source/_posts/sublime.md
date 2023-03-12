---
title: sublime-配置和使用
urlname: sublime-配置和使用
top: false
categories:
  - 软件
tags:
  - sublime
author: jesonlin
date: 2022-12-08 19:04:07
update: 2022-12-08 19:04:07
---

Sublime Text 是一款流行的代码编辑器软件，也是HTML和散文先进的文本编辑器，可运行在Linux，Windows和Mac OS X。也是许多程序员喜欢使用的一款文本编辑器软件。
[sublime英文官网](https://www.sublimetext.com/)
[sublime中文官网](https://www.sublimetextcn.com/)

<!-- more -->

## 快捷键
1. 打开/关闭左侧目录
    ```
    【cmd】+【k】，【cmd】+【b】
    ```
2.  展开/折叠代码
    ```
    【cmd】+【k】+【0】/【1】/【2】
    ```
3. 打开/关闭标签页
    ```
    【cmd】+【w】
    ```
4. 全屏
    ```
    【cmd】+【ctrl】+【f】
    ```
5. 选中中到行首/选中到行尾
    ```
     <!-- 自定义快捷键 -->
    【shift】+【home】/【end】
    ```
6. 打开终端terminus
    ```
    【cmd】+【fn】|【option】+【t】
    ```
7. json 格式化/反格式化
    ```
    <!-- 自定义快捷键 -->
    【cmd】+【fn】|【opt】+【j】/【m】
    ```


## 安装
根据[官方地址](https://www.sublimetext.com/docs/linux_repositories.html)下载安装


## 基础配置
1. 安装 Package Control 插件包管理器
    1. 旧版本安装
        1. 快捷键打开控制台
            ```
            ctrl + `
            ```
        2. 输入代码
            ```
            import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by) 
            ```
    2. 新版本安装
        1. 快捷键打开查找栏
            ```
            【cmd】 +【shift】+【p】
            ```
        2. 输入如下回车安装
            ```
            Install Package Control
            ```

2. 其他常规配置
    1. 打开侧边栏
        ```
        【cmd】+【shift】+【p】-》view: toggle side bar  or (【cmd】+【k】/【b】)
        ```
    2. 打开上边栏 
        ```
        【cmd】+【shift】+【p】-》view: toggle tab
        ```
    3. 设置主题、字体大小及tab等
        ```
        Preferences -> Settings -> User or 【cmd】+【,】
        "font_size": 21,
        "tab_size": 4,
        "translate_tabs_to_spaces": true,
        "theme": "Monokai Classic.sublime-theme",
        "color_scheme": "Monokai Classic.sublime-color-scheme",
        "show_full_path": true,
        "save_on_focus_lost": false,
        ```


## 插件安装-步骤
1. 所有插件安装都是通过插件包管理器
2. 【cmd】+【shift】+【p】
3. Install Package
4. 输入对应插件名称


## monikai 主题
1. 打开包管理器输入
    ```
    Theme-monokai pro
    ```
2. 配置主题
    ```
    「Preference」-> 「Package Settings」-> 「Theme-Monokai Pro」-> 选择「Monikai Classic」
    ```


## sidebar强化
1. 打开包管理器输入
    ```
    SideBarEnhancements
    ```
2. 配置主题
    ```
    「Preference」-> 「Package Settings」-> 「Theme-Monokai Pro」-> 选择「Side Bar」
    ```


## terminal 终端
1. 打开包管理器输入
    ```
    terminus
    ```
2. 配置快捷键
    ```
    「Preference」 -> 「Key Bindings」 -> 「User」
    {"keys": ["command+fn+t"], "command": "toggle_terminus_panel"},
    ```


## json 格式化
1. 打开包管理器输入
    ```
    pretty_json
    ```
2. 配置快捷键
    ```
    「Preference」 -> 「Key Bindings」 -> 「User」
    { "keys": ["command+fn+m"], "command": "un_pretty_json" },
    { "keys": ["command+option+m"], "command": "un_pretty_json" },
    { "keys": ["command+fn+j"], "command": "pretty_json" },
    { "keys": ["command+option+j"], "command": "pretty_json" },
    ```


## black-Python代码格式化
1. 打开包管理器输入
    ```
    sublack
    ```
2. 配置快捷键
    ```
    「Preference」-> 「Package Settings」-> 「sublack」->「Settings」
    {
        "black_on_save": false,
        "black_line_length": 80,
        "black_command": "/Users/jesonlin/Library/Python/3.8/bin/black",
    }
    「Preference」 -> 「Key Bindings」 -> 「User」
    {"keys": ["command+fn+b"], "command": "black_file"}, 
    {"keys": ["command+option+b"], "command": "black_file"}, 
    {"keys": ["command+fn+shift+b"], "command": "black_diff"}, 
    {"keys": ["command+option+shift+b"], "command": "black_diff"}, 
    ```
3. 依赖python环境  
    ```
    pip3 intall black
    ```


# SublimeLinter-pylint和flake8检查python代码
1. 打开包管理器输入
    ```
    SublimeLinter
    ```
2. 配置快捷键
    ```
    「Preference」-> 「Package Settings」-> 「SublimeLinter」->「Settings」
    // SublimeLinter Settings - User
    {
        "linters": {
            "pylint": {
                "disable": false,
                "args": ["--max-line-length=120"],
                "executable": "/Users/jesonlin/Library/Python/3.9/bin/pylint",
                "env": {},

                // Exclude files that match the given pattern(s).
                "excludes": ["!*.py"],
                // E.g. ["warning: ", "W3\d\d: ", "missing <!DOCTYPE> declaration"]
                "filter_errors": ["R0903","E0401","C0111","I0011","I0012","W0704","W0142","W0212","W0232","W0613","W0702","R0201","W0614","R0914","R0912","R0915","R0913","R0904","R0801","C0209"],
                "lint_mode": "background",
                "selector": "",
                "styles": [
                    {
                        "codes": [""]
                    }
                ],
                // The current working dir the lint job will run in.
                "working_dir": "",
                "python": 3,
                "disable_if_not_dependency": false
            },
            "flake8": {
                "disable": false,
                "args": ["--max-line-length=120"],
                "executable": "/Users/jesonlin/Library/Python/3.9/bin/flake8",
                "env": {},
                // Exclude files that match the given pattern(s).
                "excludes": ["!*.py"],
                "filter_errors": ["E203"],
                "lint_mode": "background",
                "selector": "",
                "styles": [
                    {
                        "codes": [""]
                    }
                ],
                "working_dir": "",
                "python": 3,
                "disable_if_not_dependency": false
            }
        }
    }
    ```
3. 依赖pylint和flake8
    ```
    pip3 install pylint
    pip3 install flake8
    ```


## auto refresh
1. 打开包管理器输入
    ```
    auto refresh
    ```

## SFTP
1. 打开包管理器输入
    ```
    sftp
    ```
2. 配置
    ```
    「File」-> 「SFT|FTP」->「Setup Server」->打开文件编辑保存即可
    "host": "XXX.XXX.XXX.XXX",
    "user": "root",
    "password": "XXX",
    "remote_path": "XXX",
    ```
3. 使用
    1. 同步到本地
        1. 新建一个待同步的空白文件夹
        2. 在文件名右键「SFTP|FTP」->「Map to Remote」-> 弹出文件配置远程信息保存
            ```
        "host": "XXX.XXX.XXX.XXX",
            "user": "root",
            "password": "XXX",
        "remote_path": "XXX",
            "keepalive": 3600,
        ```
     3. 鼠标文件名右键选则「Download Folder」
    

# REFERENCE
[Sublime Text 3 超详细插件安装](https://zhuanlan.zhihu.com/p/91942738)

