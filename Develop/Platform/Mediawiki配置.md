---
source_title: Mediawiki配置
categories:
- Develop
- Platform
- Web
last_modified: '2025-05-13T07:48:50Z'
---
MediaWiki是一套基于网络的Wiki引擎，维基媒体基金会的所有项目乃至众多wiki网站都使用了这软件。MediaWiki软件最初是为自由内容百科全书维基百科开发，今日已被一些公司机构用作内部知识管理和内容管理系统。Novell甚而还在多个高流量的网站中使用了该软件。

MediaWiki采用PHP编程语言写成，并可使用MySQL、MariaDB、PostgreSQL或SQLite之一作为其关系数据库管理系统。

### 镜像
 ```

# mc1
HN=10.0.0.141
同步：

# DB
 rsync -arvu --progress ${HN}:/u01/web/db/mediawiki /u01/web/db/

# Image, etc...
 rsync -arvu --progress ${HN}:/u01/web/wiki/images /u01/web/wiki
```

### LocalSettings.php

#### 服务器地址
 ```

# 换域名需要变更（如同步至测试环境、镜像环境等）
$wgServer = "https://mwbbs.eu.org";
$wgServer = "";        # 不需要绑定域名
```

#### 未注册用户不可编辑
```
 $wgGroupPermissions['*']['edit'] = false;
```

#### 不可注册
```
 $wgGroupPermissions['*']['createaccount'] = false;
```

#### 已注册用户可注册新用户
```
 $wgGroupPermissions['user']['createaccount'] = true;
```

#### 发送邮件设置
 ```
$wgEnableEmail = true;
$wgEnableUserEmail = true; # UPO
$wgSMTP = array(
'host'     => "smtp-mail.outlook.com",    //邮箱要求加密连接，前面加：ssl://
'IDHost'   => "outlook.com",
'port'     => 587,
'auth'     => true,                       // 需要登录
'username' => "nmwbbs2022@hotmail.com",   // 提供 SMTP 服务的邮箱账号
'password' => "MWw.d.123"              // SMTP 认证的密码
);
$wgEmergencyContact = "mwbbs2022@hotmail.com";
$wgPasswordSender = "MWw.d.123";
$wgPasswordSenderName = 'mwbbs2022@hotmail.com';
$wgEnotifUserTalk = true; # UPO
$wgEnotifWatchlist = false; # UPO
$wgEmailAuthentication = true;
按以上配置，未见发邮件，只是不报错了。
```

##### Error

牛奶河Wiki不能发送确认邮件。 请检查您的邮箱地址是否包含无效字符。
- $wgSMTP:host，配置成 ssl://，报错：邮件发送器的返回信息：Failed to connect to ssl://smtp-mail.outlook.com:587 [SMTP: Failed to connect socket: fsockopen(): unable to connect to ssl://smtp-mail.outlook.com:587 (Unknown error) (code: -1, response: )]
- 邮件发送器的返回信息： authentication failure [SMTP: Invalid response code received from server (code: 535, response: 5.7.3 Authentication unsuccessful [LO4P123CA0531.GBRP123.PROD.OUTLOOK.COM])]
- 邮件发送器的返回信息： authentication failure [SMTP: Invalid response code received from server (code: 535, response: 5.7.3 Authentication unsuccessful [LO2P265CA0145.GBRP265.PROD.OUTLOOK.COM])]

----原配置

### $wgEmergencyContact = "";

### $wgPasswordSender = "";

### $wgEnotifUserTalk = false; # UPO

### $wgEnotifWatchlist = false; # UPO

### $wgEmailAuthentication = true;

#### 授权方式

##### 无

## For attaching licensing metadata to pages, and displaying an

## appropriate copyright notice / icon. GNU Free Documentation

## License and Creative Commons licenses are supported so far.

$wgRightsPage = ""; # Set to the title of a wiki page that describes your license/copyright

$wgRightsUrl = "";

$wgRightsText = "";

$wgRightsIcon = "";

##### 知识共享署名-相同方式共享

$wgRightsPage = "";

$wgRightsUrl = "https://creativecommons.org/licenses/by-sa/4.0/";

$wgRightsText = "知识共享署名-相同方式共享";

$wgRightsIcon = "$wgResourceBasePath/resources/assets/licenses/cc-by-sa.png";

#### LOGO

左上角 LOGO 在 $wgLogos 中定义。

#### 疑问/尚不清楚

##### 数据库复制

下列配置出现在数据库配置区域，感觉像是复制了一份数据库，但无地址，无法判断，删除后不影响使用。
 ```
$wgObjectCaches['db-replicated'] = [
'factory' => 'Wikimedia\ObjectFactory\ObjectFactory::getObjectFromSpec',
'args' => [ [ 'factory' => 'ObjectCache::getInstance', 'args' => [ CACHE_DB ] ] ]
];
```

##### 扩展:AbuseFilter

增加后，查看历史报错。
 ```
数据库错误
出现数据库查询错误。这可能表示软件中存在漏洞。
[de469599c3e96adad328cf25] 2022-12-29 12:42:34: 类型“Wikimedia\Rdbms\DBQueryError”的致命异常
```

##### 扩展:OATHAuth

增加后，登录报错。
 ```
数据库错误
出现数据库查询错误。这可能表示软件中存在漏洞。
[8b7f27bdbb64400300154b26] 2022-12-29 12:46:24: 类型“Wikimedia\Rdbms\DBQueryError”的致命异常
```

##### 扩展:VisualEditor
 ```
使用一段时间后（两年），使用可视编辑功能报错。
[1e8bb4e427e477bad1672d63] Caught exception of type RuntimeException
有说可能是 php7-zlib 的问题，需要安装/重安装；
实际情况是重启 Apache2 正常。
```

### 其他修改

#### /includes/WebStart.php

不起作用
```
 ''<!DOCTYPE html>
 
 ''
 *'*'
 ''''
```

### 扩展(extensions)
- 安装依赖组件。
- 下载扩展文件，放置在 extensions/XXX

在 LocalSettings.php 末尾增加
```
 wfLoadExtension( 'XXX' );
 如：wfLoadExtension( 'VisualEditor' );
```

导航至 [验证已成功安装](https://<!-- template: SERVERNAME -->/wiki/index.php/Special:Version)

#### 扩展:Popups

当用户将鼠标悬停在一个页面和分别的引用时，[弹窗（Popups）扩展](https://www.mediawiki.org/wiki/Extension:Popups)会预览一篇文章的内容，对于引用，还会显示引用的完整内容。

This extension has a hard dependency on **[Extension:TextExtracts](#Mediawiki帮助)** and **Extension:PageImages** when used with the default mwApiPlain gateway. 

[Popups 1.39 Download](https://extdist.wmflabs.org/dist/extensions/Popups-REL1_39-fedc2c6.tar.gz)

#### 1.39 已内置扩展

##### 扩展:TextExtracts
```
 git clone https://gerrit.wikimedia.org/r/mediawiki/extensions/TextExtracts.git
```

##### 扩展:页面图像
```
 https://www.mediawiki.org/wiki/Special:ExtensionDistributor/PageImages
 https://extdist.wmflabs.org/dist/extensions/PageImages-REL1_39-6f03b4a.tar.gz
```

#### wgHooks

$wgHooks 是一个全局变量，用于存储 MediaWiki 中的各种钩子，允许在不修改核心代码的情况下，在特定事件发生时执行自定义代码。如：'BeforePageDisplay' 是一个在页面最终渲染前触发的钩子。

#### LaTeX

让页面具备 LaTeX 解析能力（通过 [KaTeX](https://github.com/KaTeX/KaTeX)）
 ```

# 在 MediaWiki 页面显示之前，自动加载 KaTeX 库及其 auto-render 扩展，并配置 auto-render 扩展使用 $$ 作为数学公式的分隔符。

# 1. 将 https://github.com/KaTeX/KaTeX 下载并解压的目录 katex 放入 /js/ 中

# 2. 在 LocalSettings.php 增加下列代码（需清缓存，无需重启）：
$wgHooks['BeforePageDisplay'][] = function( OutputPage &$out, Skin &$skin ) {
  $out->addScript('
    
    
    
    
      document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
          delimiters: [
            {left: \'$$\', right: \'$$\', display: true}, 
            {left: \'$\', right: \'$\', display: false}
          ], 
          throwOnError : false
        });
      });
    
  ');
};
```
