---
source_title: Dokuwiki配置
categories:
- Develop
- Platform
- Web
last_modified: '2023-01-19T14:24:54Z'
---
![Dokuwiki.png](https://mwbbs.eu.org/wiki/images/8/88/Dokuwiki.png)

DokuWiki is a simple to use and highly versatile Open Source wiki software that doesn't require a database. It is loved by users for its clean and readable syntax. The ease of maintenance, backup and integration makes it an administrator's favorite. Built in access controls and authentication connectors make DokuWiki especially useful in the enterprise context and the large number of plugins contributed by its vibrant community allow for a broad range of use cases beyond a traditional wiki.

Read the [DokuWiki Manual](https://www.dokuwiki.org/manual) to unleash the full power of DokuWiki.

### ENV

Apache/…

PHP Version 7.4.3: OK

### Install

https://www.dokuwiki.org/dokuwiki

http://www.dokuwiki.com.cn
1. Extract the tarball somewhere on your hard disk.
1. Upload all the files within the dokuwiki-YYYY-MM-DD directory to your webspace.
1. Give proper permissions to certain folders and files.
1. Open http://yourserver/dokuwiki/install.php to use the graphical configuration tool.

### plugin

#### 增加新页

plugin: Add New Page –>

{{NEWPAGE}}

←- 注：多目录格式：dev:db:oracle:oracle_abc.txt

#### 侧边栏 & 增加新页

plugin: Indexmenu Plugin

Welcome → Create → sidebar –>

所有文章

{{indexmenu>.#1|navbar nopg skipns=/media/}}

发表文章

{{NEWPAGE}}

←- 注：N表示展开到第几层，nopg不显示页面，skipns跳过目录

#### 移动页面

plugin: Move plugin

每个页面右侧边栏增加“页面重命名”
