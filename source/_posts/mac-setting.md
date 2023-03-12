---
title: mac-设置
urlname: mac-设置
top: false
categories:
  - 工具   
tags:
  - mac设置
author: jesonlin
date: 2022-12-08 16:52:56
update: 2022-12-08 16:52:56
---

记录下windows多年老用户切换到mac系统后更趁手的设置。

<!-- more -->

## 基本设置
1. 触控板选项-轻点省力  
    1. 【系统偏好设置】-》【触控板】-》勾选【轻点来点按】  
    2. 触控板有很多好用的手势，可以实时学习
2. 三指拖移-拖拽超级好用  
    【系统偏好设置】-》【辅助功能】-》【指针控制】-》【触控板选项】-》【启用拖移】三指拖移
3. 访达偏好设置
    ```
        【桌面】-》左上角【访达】-》【偏好设置】-》 【边栏】-》勾上所需
     ```
4. 底部程序坞设置
    ```
    鼠标置于程序坞-》双指按压-》程序坞偏好设置-》置于屏幕底部/自动隐藏
    ```
5. 顶部图标栏
    ```
    按住【cmd】键，三指拖拽顶部图标下拉，有出现删除标记可以删除
    ```
6. 文件显示
    ```
    程序坞打开【访达】-》按住【cmd】+[up]键直到最顶层 -》点击左上角显示-》
    选择【为列表】
    选择【显示标签页栏】
    选择【显示路径栏】
    选择【显示状态栏】
    选择【查看选项】-》勾选【始终以列表】-》排序方式【修改日期】-》选中【用作默认】
    ```
7. 触发角
    ```
    【系统偏好设置】-》【桌面与屏幕保护程序】-》【触发角】
    【左上角】-》【将显示器置于休眠状态】
    【左下角】-》【启动屏幕保护程序】
    【右上角】-》【调度中心】
    【右下角】-》【桌面】
    ```
8. 终端设置
    1. 窗口设置
        ```
        打开终端偏好设置-》样式选中【homebrew】-》字体大小选中【18】-》窗口选中列数100行数36
        ```
    2. brew
        ```
        /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
        echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/XXXX/.zshrc
        echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/jesonlin/.zshrc
        ```
    3. zsh  
        1. 安装  
            ```
            sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
            ```
        2. 显示当前目录绝对路径  
            ```
            vim ~/.oh-my-zsh/themes/robbyrussell.zsh-theme
            PROMPT+=' %{$fg[cyan]%}%d%{$reset_color%} $(git_prompt_info)'
            ```
    4. 别名的快捷操作
        ```
        vim ~/.zshrc
        export PATH=$HOME/bin:/usr/local/bin:/usr/bin:$PATH
        alias ll='ls -alhF'
        alias subl="/Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl"
        alias -s py=subl
        eval "$(/opt/homebrew/bin/brew shellenv)"
        ```
9. Python环境
    1. 安装pyenv  
        ```
        curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
        vim ~/.zshrc
        export PATH="$HOME/.pyenv/bin:$PATH"
        eval "$(pyenv init -)"
        eval "$(pyenv virtualenv-init -)"   
        ```
    2. 查看看着Python版本
        ```
        pyenv install -l
        ```
    3. 安装python指定版本（m1 pro有问题）
        ```
        pyenv install 3.6.8
        ```
10. SMB共享的网络磁盘上DS_Store文件的阻止生成方案
    ```
    【终端】-》defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE
    ```


## 快捷键
0. 复制==拷贝+粘贴  
    ```
    【cmd】+ 【d】
    ```
1. 拷贝
    ```
    【cmd】 + 【c】
    ```
2. 粘贴 
    ``` 
    【cmd】 + 【v】
    ```
3. 剪切（文本）  
    ```
    【cmd】 + 【x】
    ```
4. 剪切（文件）  
    ```
    【cmd】 + 【c】, 【cmd】 + 【opt】 + 【v】
    ```
5. 关闭软件  
    ```
    【cmd】 + 【q】
    ```
6. 任务管理器  
    ```
    【cmd】 + 【opt】 + 【esc】
    ```
7. 预览 
    ``` 
    单击中 + 空格  / 三指单击
    ```
8. 数据查询和英文翻译  
    ```
    三指单击
    ```
9. 截图 
    ``` 
    全屏：【shift】 +【cmd】 + 【3】  
    指定区域：【shift】 + 【cmd】 + 【4】  
    特定窗口：【shift】 + 【cmd】 + 【4】 + 【space】  
    ```
10. 录屏
    ```
    【shift】 + 【cmd】 + 【5】
    ```
11. 聚焦搜索(升级为Alfred)
    ```
    【cmd】 + 【space】
    ```
12. 调出访达搜索
    ```
    【cmd】+【opt】+【space】
    ```
13. delete
    ```
    【fn】+【backpace】
    ```


## 其他操作
1. 连接服务器
    1. 连接服务器
        ```
        【桌面】-》【前往】-》【连接服务器】（【cmd】+【k】）
        smb://XXX.XXX.XXX.XXX/文件夹路径
        ```
    2. 连接远程服务器
        1. 安装smb服务
            ```
            yum -y install samba
            ```
        2. 选择要共享的文件夹，添加权限
            ```
            chmod 777 /root
            ```
        3. 打开smb配置文件
            ```
            vim /etc/samba/smb.conf
            // 在global下面添加一行访问端口，因为运营商常把445端口拦截了
            [global]
                    smb ports = 1315 1314
            // 文件末尾添加访问名及其对应共享文件夹
            [share]
                comment = Share Directories
                path=/root
                public = yes
                browseable = yes
                writable = yes
                create mask = 0777
                directory mask = 0777
            ```
        4. 创建smb登录用户名
            ```
            touch /etc/samba/smbpasswd #新建文件
            smbpasswd -a root
            ```
        5. 并且将smb设置为开机自启动
            ```
            chkconfig --level 3 smb on
            ```
        6. 重新启动smb
            ```
            service smb start
            ```
        7. 腾讯云服务器-》防火墙-》开启1314 1315端口

2. 键位映射-karabiner-elements-神器-模拟filco键盘键位  
    1. 【系统偏好设置】-》【键盘】-》【按下地球仪fn键时】-》选中不做任何操作 
    2. mac键位映射-》打开karabiner-elements
        1. 【Profiles】  
            1. 删除所有，保留一个默认的'default'
            2. 新建一个'mac'，这些配置会出现在【菜单栏karabiner-elements的下拉列表】
            3. 将配置文件mac.json放在【\~/.config/karabiner/assets/complex_modifications】
            4. 文件路径也可在【Misc】-》【Export & Import】双击打开
        2. 【Device】选择【Apple Internal Keyboard】
        3. 【Simple modifications】-》【Apple Internal Keyboard】: 简单的键位映射
            ```
            fn -> caps lock
            caps lock -> left_command
            left_command -> fn
            ```
        4. 【Complex modifications】-》【Apple Internal Keyboard】：复杂的组合映射
            1. 【Add rule】
            2. 选择刚才mac.json中的四条映射规则
                1. fn + aesdfg = head/上/左/下/右/tail
                2. right_command + k/< = home/end
                3. right_command + l/> = page_up/page_down
                4. 绑定fn + tab = left_command + tab
     3. filco-minila-air键位映射-》打开karabiner-elements
         1. 新建一个'filco',会生成对应filco.json文件放在指定路径。
         2. 【Device】选中连接的filco蓝牙键盘
         3. 【Simple modifications】-》【FILCO XXX】: 简单的键位映射
            ```
            left_control -> caps lock
            caps lock -> left_command
            left_command -> left_control
            ```
         4. 【Function keys】-》【FILCO XXX】：功能键映射，将Fx键映射本身而不是mac上的功能。
         4. 【Complex modifications】-》【FILCO XXX】：复杂的组合映射
             1. 【Add rule】
             2. 选择刚才filco.json中的两条映射规则
                 1. left_option + esdfag = head/上/左/下/右/tail
                 2. left_option + tab = left_command + tab


## 必备软件
1. 屏保
    ```
    http://fliqlo.com/
    ```
2. 搜狗输入法
    ```
    https://pinyin.sogou.com/mac/
    ``` 
3. 谷歌浏览器
    ```
    https://www.google.com/intl/zh-CN/chrome/
    # 插件
    https://chrome.google.com/webstore/category/extensions?hl=zh-CN
    ```
    ```
    1. iTab新标签页（丰富的标签页）
    2. DJSON. JSON Viewer & Formatter（json格式化）
    3. Markdown Preview Plus（可以用sublime打开md文件编辑，同时用chrome打开该文件实时刷新预览）
    4. 有道词典Chrome划词插件（翻译插件）
    5. Talend API Tester - Free Edition（发包工具）
    ```
4. sublime
    ```
    https://www.sublimetext.com
    ```
5. xmind
    ```
    https://www.xmind.cn/
    ```
6. Alfred
    ```
    # 通过一个输入框快速完成文件搜索、自定义动作
    https://www.alfredapp.com/
    # 先取消原有聚焦搜索快捷键
    【系统偏好设置】-》【键盘】-》【快捷键】-》【聚焦搜索】
    # 设置Alfred快捷键
    【cmd】+【space】
    # 其他设置待摸索
    ```
  

## REFERENCE
[filco.json](https://github.com/linjinzhong/Software/blob/master/filco.json)
[mac.json](https://github.com/linjinzhong/Software/blob/master/mac.json) 
