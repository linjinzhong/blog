---
title: 博客搭建-hexo+next+github
urlname: 博客搭建-hexo+next+github
top: false
categories:
  - 工具
tags:
  - 博客搭建
author: jesonlin
date: 2022-11-22 12:24:56
update: 2022-11-22 12:24:56
---


记录下搭建个人博客流程，使用 hexo + next + github + 个人域名。

<!-- more -->

## 前期准备
0. 全局替换如下信息
    ```
    <USERNAME>: github个人账号
    <EMAIL>: github个人邮箱
    <CVM-IP>: CVM的IP
    <个人域名>: 腾讯云上申请的域名，https:开头
    <域名>: 腾讯云上申请的域名，不带https:开头
    <twitter账号>: twitter个人账号
    <facebook账号>: facebook个人账号
    ```
1. 腾讯云 cvm-centos:7  
    1. 购买初始化
        ```
        https://cloud.tencent.com/product/cvm?from=13950
        ```
    2. 替换腾讯云源
        ```
        mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
        wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.cloud.tencent.com/repo/centos7_base.repo
        yum clean all
        yum makecache
        yum update -y
        yum upgrade -y
        ```
    3. 磁盘映射-samba
        1. 安装
            ```
            yum -y install samba
            ```
        2. 选择共享文件夹
            ```
            mkdir -p /data/service
            chmod 777 /data/service
            ```
        3. 编辑samba配置文件
            ```
            vim /etc/samba/smb.conf
            <!-- global下新增一行，放开1315端口 -->
            smb ports = 1315

            <!-- 文件末尾添加如下共享文件夹 -->
            [service]
            comment = service
            path = /data/service
            public = yes
            browseable = yes
            writable = yes
            create mask = 0777
            directory mask = 0777
            ```
        4. 创建samba用户名和密码
            ```
            smbpasswd -a root
            ```
        5. 设置为开启启动
            ```
            chkconfig --level 3 smb on
            ```
        6. 重启sambda
            ```
            service smb start
            ```
    4. cvm -> 安全组 -> 自定义模板 -> 按模板格式放开端口
        ```
        0.0.0.0/0   TCP:1315
        0.0.0.0/0   TCP:4000
        ```
2. 腾讯云域名
    1. 注册
        ```
        https://dnspod.cloud.tencent.com/
        ```
    2. 添加A记录
        ```
        <!-- 找到github账号所在ip -->
        ping <USERNAME>.github.io
        @ A 默认 [IP]
        ```
    3. 添加CNAME
        ```
        www CNAME 默认 <USERNAME>.github.io.
        ```
3. github
    1. 注册和登录
        ```
        https://github.com/
        ```
    2. 新建仓库blog
        ```
        <!-- 修改默认分支名为master -->
        https://github.com/settings/repositories
        <!-- 新建仓库 -->
        https://github.com/<USERNAME>?tab=repositories
        <!-- 仓库地址-SSH -->
        git@github.com:<USERNAME>/blog.git
        <!-- 默认创建master分支 -->
        ```
    3. 创建分支
        ```
        <!-- 创建source分支 -->
        https://github.com/<USERNAME>/blog/branches
        <!-- 修改默认分支为source分支 -->
        Default branch -> 最右边左右箭头 -> master下拉选source -> Update
        ```
    3. 配置git
        ```
        git config --global user.name "<USERNAME>"
        git config --global user.email "<EMAIL>"
        ```
    4. 新建ssh keys
        ```
        <!-- cvm输入如下联系回车 -->
        ssh-keygen -t rsa -C "blog_<CVM-IP>"
        <!-- 查看公开密钥的方法 -->
        cat ~/.ssh/id_rsa.pub
        <!-- 将公开秘钥填入github -->
        https://github.com/settings/ssh/new
        ```
    5. 配置host链接git更快
        ```
        <!-- 从该网址找到IP -->
        https://www.ipaddress.com/site/github.com
        <!-- 或者如下 -->
        https://www.whatsmydns.net/#A/github.com
        <!-- 填入到hosts -->
        vim /etc/hosts
        [IP] https://github.com
        [IP] github.com
        ```


## hexo部署
1. 安装Node.js
    ```
    yum install -y nodejs
    <!-- 查询版本号 -->
    node -v
    npm -v
    ```
2. 安装git
    ```
    yum install -y git
    ```
3. 安装Hexo
    ```
    mkdir -p /data/service
    cd /data/service
    npm install hexo-cli -g
    hexo init blog
    cd blog
    npm install
    hexo server
    ```
4. 启动
    ```
    hexo clean && hexo generate && hexo server
    ```
5. 本地调试-浏览器输入
    ```
    http://<CVM-IP>:4000/
    ```


## github部署
1. 编辑_config.yml
    ```
    cd /data/service/blog
    vim _config.yml
    deploy:
      type: git
      repository: git@github.com:<USERNAME>/blog.git
      branch: master  # 这个根据github实际填master还是main，建议将github默认分支改为master
    url: https://<USERNAME>.github.io/blog/
    ```
2. 部署推送git的master分支
    ```
    cd /data/service/blog
    npm install hexo-deployer-git --save
    hexo clean && hexo generate && hexo d
    ```
3. github配置
    ```
    <!-- 进入blog仓库的pages页面 -->
    https://github.com/<USERNAME>/blog/settings/pages
    Branch -> 选择master -> Save
    <!-- 等待几分钟，重新刷新页面，会出现如下 -->
    Your site is live at https://<USERNAME>.github.io/blog/
    <!-- 通过该网址可以访问hexo首页 -->
    ```
4. 配置自定义域名
    1. 编辑_config.yml
        ```
        url: <个人域名>
        ```
    2. github配置腾讯云域名
        ```
        https://github.com/<USERNAME>/blog/settings/pages
        Pages -> Custom domain -> <域名> -> Save
        <!-- 会在master分支下生成CNAME文件 -->
        ```
    3. 重新部署推送
        ```
        hexo clean && hexo generate && hexo d
        ```
    4. 问题
        1. 发现master分支下的CNAME文件不见了
        2. 同时Pages -> Custom domain下自定义的域名也被重置为一开始默认的
    5. 解决方法
        1. 将生成的CNAME文件放在/data/service/blog/source下
        2. 再次部署推送发现本地.deploy_git目录下就有CNAME文件
        3. 同时master分支上的内容就是该.deploy_git目录下内容
    6. 最终可以通过个人域名访问hexo博客
5. 源代码保存在默认的source分支
    1. 目的是可以再任意地方重新部署写文章
    2. 关联远程的默认source分支
        ```
        hexo clean
        git init
        git remote add origin git@github.com:<USERNAME>/blog.git
        git add .
        git commit -m "初始化blog"
        git branch -M source
        git pull
        git branch --set-upstream-to=origin/source  source
        git pull
        git push
        ```
6. 迁移到其他CVM
    ```
    mkdir -p /data/service/
    cd /data/service
    yum install -y nodejs
    yum install -y git
    git clone git@github.com:<USERNAME>/blog.git
    cd blog
    npm install hexo-cli -g
    hexo init .
    npm install
    hexo server
    ```


## 主题配置
1. 下载next主题
    ```
    cd /data/service/blog
    <!-- v8.13.2 -->
    git clone https://github.com/theme-next/hexo-theme-next themes/next
    ```
2. 配置文件
    ```
    <!-- 站点配置文件 -->
    /data/service/blog/_config.yml
    <!-- 主题配置文件 -->
    /data/service/blog/themes/next/_config.yml
    ```
3. 修改站点配置文件_config.yml
    ```
    # Site
    title: jesonlin
    subtitle: ''
    description: 'choice is better than effort'
    keywords:
    author: jesonlin
    language: zh-CN
    timezone: Asia/Shanghai
    
    permalink: :category/:urlname/  # 永久链接
    permalink_defaults: 
      urlname: undefined  # 永久链接中变量默认值

    default_layout: draft  # 使用hexo new [layout] <title> 默认布局改为草稿

    post_asset_folder: true # 将每篇博客对应生成一个文件夹目录（方便插入图片等附件）
    # 插入图片相对路径![](/xx.jpg)需要安装包
    # npm install hexo-image-link --save

    default_category: uncategory

    ## Themes: https://hexo.io/themes/
    theme: next
    ```
4. 隐藏网页底部的由 Hexo & NexT.Gemini 强力驱动
    ```
    vim /data/service/blog/themes/next/layout/_partials/footer.swig
    <!-- 搜索powered所在几行，前后分别加上 -->
    <!--
        包含powered的行
    -->
    ```
5. 修改主题配置文件
    1. 主题-Scheme Settings
        ```
        custom_file_path:
          style: source/_data/styles.styl  # 在对应位置创建文件
        # Schemes
        # scheme: Muse
        #scheme: Mist
        #scheme: Pisces
        scheme: Gemini  # 使用这个

        # Dark Mode
        darkmode: true  # 主题暗黑模式
        ```
    2. 暗黑模式切换开关
        ```
        <!-- 依赖 -->
        npm install hexo-next-darkmode --save
        # Dark Mode
        darkmode: false  # 主题暗黑模式
        # Darkmode JS
        # For more information: https://github.com/rqh656418510/hexo-next-darkmode, https://github.com/sandoche/Darkmode.js
        darkmode_js:  # 黑白模式开关，npm install hexo-next-darkmode --save
          enable: true
          bottom: '64px' # default: '32px'
          right: 'unset' # default: '32px'
          left: '32px' # default: 'unset'
          time: '0.5s' # default: '0.3s'
          mixColor: 'transparent' # default: '#fff'
          backgroundColor: 'transparent' # default: '#fff'
          buttonColorDark: '#100f2c' # default: '#100f2c'
          buttonColorLight: '#fff' # default: '#fff'
          isActivated: false # default false
          saveInCookies: true # default: true
          label: '🌓' # default: ''
          autoMatchOsTheme: true # default: true
          libUrl: # Set custom library cdn url for Darkmode.js
        ```
    3. 站点信息-定制logo-custom_logo
        ```
        custom_logo: /images/qq-image.jpeg  # 定制logo
        creative_commons:
          post: true  # 文末版权声明
        ```
    4. 站点信息-文末版权声明-creative_commons
        ```
        post: true
        <!-- 额外增加一行本文结束*感谢您的阅读 -->
        vim themes/next/layout/_macro/passage-end-tag.njk
        <!-- 粘贴如下内容 -->
        <div>
            {% if not is_index %}
                <div style="text-align:center;color: #ccc;font-size:24px;">------------->本文结束<i class="fa fa-paw"></i>感谢您的阅读-------------</div>
            {% endif %}
        </div>
        <!-- 在post.njk加上刚才新建的文件 -->
        vim themes/next/layout/_macro/post.njk 
        <!-- 在END POST BODY后加上如下 -->
        {% if not is_index and theme.passage_end_tag.enabled %}
          <div>
            {% include 'passage-end-tag.njk' %}
          </div>
        {% endif %}
        <!-- 打开主题配置文件 -->
        vim themes/next/_config.yml
        <!-- 在末尾加上 -->
        # 文章末尾添加“本文结束”标记
        passage_end_tag:
          enabled: true
        ```
    5. 菜单-菜单项-menu
        ```
        menu:
        home: / || fa fa-home
        about: /about/ || fa fa-user
        tags: /tags/ || fa fa-tags
        categories: /categories/ || fa fa-th
        archives: /archives/ || fa fa-archive
        resources: /resources/ || fa fa-download
        #schedule: /schedule/ || fa fa-calendar
        #sitemap: /sitemap.xml || fa fa-sitemap
        #commonweal: /404/ || fa fa-heartbeat
        ```
    6. 侧边栏-sidebar
        ```
        <!-- 头像-avatar -->
        url: /images/handsome.jpg  # 侧边栏头像
        rounded: true  # 侧边栏头像圆形包围
        rotated: true  # 侧边栏头像旋转动画
        <!-- 社交链接-social -->
        GitHub: https://github.com/<USERNAME> || fab fa-github
        E-Mail: mailto:<EMAIL> || fa fa-envelope
        Twitter: https://twitter.com/<twitter账号> || fab fa-twitter
        FB Page: https://www.facebook.com/<facebook账号> || fab fa-facebook
        ```
    7. 页脚和爱心动画-footer
        ```
        since: 2022  # 建站时间
        animated: true  # 爱心动画
        powered: false  # 关掉powered
        ```
    8. 博客设置-字数统计
        ```
        <!-- 依赖包 -->
        cd /data/service/blog
        npm install hexo-symbols-count-time
        <!-- 主题配置文件 -->
        symbols_count_time:
          separated_meta: true  # 是否另起一行（true的话不和发表时间等同一行）
          item_text_post: true  # 首页文章统计数量前是否显示文字描述（本文字数、阅读时长）
          item_text_total: true  # 页面底部统计数量前是否显示文字描述（站点总字数、站点阅读时长）
          awl: 4  # 平均字长
          wpm: 275  # 每分钟阅读字数
          suffix: "mins."  # 单位后缀
        <!-- 需要重新部署 -->
        hexo clean && hexo generate && hexo s
        ```
    9. 博客设置-打赏奖励
        ```
        reward_settings:
          # If true, reward will be displayed in every article by default.
          enable: true  # 打赏开关
          animation: false
          # 没有comment选项，修改其他 # 提示修改
          # vim themes/next/languages/en.yml
          # Buy me a coffee -> 原创技术分享，您的支持将鼓励我继续创作
        reward:
          wechatpay: /images/wechatpay.png  # 微信收款码
          alipay: /images/alipay.png  # 支付宝收款码
        ```
    10. 进度条-pace
        ```
        pace:
          enable: true
        ```
    11. 评论-comments
        ```
        comments:
          active: gitalk  # 评论
        gitalk:  #注册应用: https://github.com/settings/applications/new "Homepage URL"和"Authorization callback URL"填个人域名
          enable: true  # 开启评论
          github_id: <USERNAME>  # github账号
          repo: blog  # github上博客仓库
          client_id: [CLIENT_ID]
          client_secret: [CLIENT_SECRET]
          admin_user: <USERNAME>
          distraction_free_mode: true # Facebook-like distraction free mode
          language: zh-CN
        <!-- 由于gitalk被墙没法获取token，需要代理 -->
        proxy: https://strong-caramel-969805.netlify.app/github_access_token  # 替换网上找的代理
        ```
    12. 人数浏览数统计-busuanzi_count
        ```
        busuanzi_count:
          enable: true  # 启用不蒜子
          total_visitors: true  # 网站总访客数
          total_visitors_icon: fa fa-user
          total_views: true  # 网站总浏览数
          total_views_icon: fa fa-eye
          post_views: true # 文章浏览数
          post_views_icon: fa fa-eye
        ```
    13. 本地搜索-local_search
        ```
       local_search:
          enable: true  # 激活
        <!-- 依赖包 -->
        cd /data/service/blog
        npm install hexo-generator-searchdb
        ```
    14. github贡献日历-只在关于页面出现
        ```
        vim themes/next/layout/_layout.njk
        {%- if page.type == 'about' %}
        <div class="post-block animated fadeIn">
            <h5 class="post-title" itemprop="name headline">
                <a href="https://github.com/<USERNAME>" class="post-title-link" itemprop="url">Github Contribution Calendar</a>
            </h5>
            <div class="post-body animated fadeInDown" itemprop="articleBody">
                <img style="width: 100%; margin-top: 30px;" src="https://ghchart.rshah.org/00ff00/<USERNAME>" alt="jesonlin's Blue Github Chart" />
            </div>
        </div>
        {%- endif %}
        ```
    15. 代码块拷贝
        ```
        codeblock:
          copy_button:  # 代码块拷贝
            enable: true
        ```


## 基本使用
1. 布局文件
    ```
    布局名   存放位置
    post    source/_posts   # 文章
    page    source          # 页面
    draft   source/_drafts  # 草稿
    ```
2. 草稿布局文件设置
    ```
    vim scaffolds/draft.md
    <!-- 填入如下内容 -->
    ---
    title: {{ title }}
    urlname: {{title}}
    top: false
    categories:
    - uncategory
    tags:
    - untag
    date: {{ date }}
    updated: {{ date }}
    author: {{ author }}
    ---

    这里是摘要

    <!-- more -->

    # 内容

    这里是内容
    ```
3. 文章布局文件设置
    ```
    vim scaffolds/post.md
    <!-- 填入如下内容 -->
    ---
    title: {{ title }}
    urlname: {{title}}
    top:
    categories:
    tags:
    date: {{ date }}
    updated: {{ date }}
    author: {{ author }}
    ---
    ```
4. 菜单栏页面创建
    ```
    <!-- 创建关于页面 -->
    hexo new page about
    <!-- 创建分类页面 -->
    hexo new page categories
    <!-- 创建标签页面 -->
    hexo new page tags
    <!-- 创建资源页面 -->
    hexo new page resources
    ```
5. 创建草稿
    ```
    hexp new [layout] <title>
    hexo new draft xxx
    <!-- 草稿默认不显示，通过参数--draft强制显示 -->
    hexo clean && hexo s --draft
    <!-- 发布草稿到文章 -->
    hexo publish <title>
    ```
6. 创建文章
    ```
    hexo new post xxx
    ```
7. 本地调试
    ```
    hexo clean && hexo s
    ```
8. 推送到github部署
    ```
    hexo clean && hexo generate && hexo deploy
    ```
## REFERENCE
[官方文档](https://hexo.io/zh-cn/) 
[hexo-next主题配置](https://blog.csdn.net/as480133937/article/details/100138838)
