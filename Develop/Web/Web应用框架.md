---
source_title: Web应用框架
categories:
- Develop
- Web
last_modified: '2023-02-06T04:13:59Z'
---
Web应用框架（Web application framework）是一种开发框架，用来支持动态网站、网络应用程序及网络服务的开发。其类型有基于请求的和基于组件的两种框架。

Web应用框架有助于减轻网页开发时共通性活动的工作负荷，例如许多框架提供数据库访问接口、标准样板以及会话管理等，可提升代码的可再用性。

### 框架类型

现代web框架基本上都采用了模型、视图、控制器相分离的[MVC架构](#MVC架构)，基于请求和基于组件两种类型大都会有一个控制器将用户的请求分派给负责业务逻辑的模型，运算的结果再以某个视图表现出来，所以两大分类框架的区别主要在视图部分，基于请求的框架仍然把视图也就是网页看作是一个文档整体，程序员要用HTML、Javascript和CSS这些底层的代码来写“文档”，而基于组件的框架则把视图看作由积木一样的构件拼成，积木的显示不用程序员操心（当然它们也是由另一些程序员开发出来的），只要设置好它绑定的数据和调整它的属性，把他们大大从编写HTML、Javascript和CSS这些界面的工作中解放出来。

#### 请求框架

基于请求的框架较早出现，它用以描述一个web应用程序结构的概念和传统的静态Internet站点一样，是将其机制扩展到动态内容的延伸。对一个提供HTML和图片等静态内容的网站，网络另一端的浏览器发出以URI形式指定的资源的请求，Web服务器解读请求，检查该资源是否存在于本地，如果是则返回该静态内容，否则通知浏览器没有找到。Web应用升级到动态内容领域后，这个模型只需要做一点修改。那就是web服务器收到一个URL请求（相较于静态情况下的资源，动态情况下更接近于对一种服务的请求和调用）后，判断该请求的类型，如果是静态资源，则照上面所述处理；如果是动态内容，则通过某种机制（CGI、调用常驻内存的模块、递送给另一个进程如Java容器）运行该动态内容对应的程序，最后由程序给出响应，返回浏览器。在这样一个直接与web底层机制交流的模型中，服务器端程序要收集客户端籍get或post方式提交的数据，转换，校验，然后以这些数据作为输入运行业务逻辑后生成动态的内容（包括HTML、JavaScript、CSS、图片等）。

#### 组件框架

基于组件的框架采取了另一种思路，它把长久以来软件开发应用的组件思想引入到web开发。服务器返回的原本文档形式的网页被视为由一个个可独立工作、重复使用的组件构成。每个组件都能接受用户的输入，负责自己的显示。上面提到的服务器端程序所做的数据收集、转换、校验的工作都被下放给各个组件。

### Python

#### Django

Django是一个由Python写成的开源的Web应用框架。采用了MVC的软件设计模式。它开发最初是被用于管理劳伦斯出版集团旗下的一些以新闻内容为主的网站，并于2005年7月在BSD许可证下发布。这套框架是以比利时的吉普赛爵士吉他手Django Reinhardt来命名的。Django的主要目标是使开发复杂、数据库驱动的网站变得简单。Django注重组件的重用性和“可插拔性”，敏捷开发和DRY（Don't Repeat Yourself）法则。

代码托管地址：https://github.com/django/django

#### Flask

Flask是一个轻量级的、高扩展性的Web应用“微”框架，使用最简单的核心，并允许你通过Flask-extension扩展各种功能，以满足Web应用开发中的所有需求。Flask依赖于两个外部库：Jinja2 模板引擎和Werkzeug WSGI工具集。

代码托管地址：https://github.com/mitsuhiko/flask

### JS

#### Meteor

Meteor是一种新型JavaScript框架，用于WebApp应用程序开发。Meteor的基础构架是Node.JS+MongoDB，它把这个基础构架同时延伸到了浏览器端，如果App用纯JavaScript写成，JS APIs和DB APIs就可以同时在服务器端和客户端无差异地调用，本地和远程数据通过DDP（Distributed Data Protocol）协议传输。因此部分应用如TODO列表，网络在线和离线下使用功能完全没有差异，动作响应和数据延迟也完全感觉不出来。

代码托管地址：https://github.com/meteor/meteor

#### Express

Express是 Node.js 的一个MVC开发框架，支持jade等多种模板，是Node.js上最流行的Web开发框架。提供一系列强大特性帮助你创建各种Web应用。Express不对Node.js已有的特性进行二次抽象，只是在Node.js基础上扩展了Web应用所需的功能。

代码托管地址：https://github.com/strongloop/express

#### Sails

Sails是一个构建于Node.js基础之上的实时MVC框架，能够帮助开发人员轻松构建自定义、企业级的Node.js应用。它设计成类似于Ruby on Rails的MVC架构，但支持较为现代的风格，且是面向数据的Web应用程序开发。它特别适合实时功能开发，如聊天。得克萨斯州奥斯汀的Balderdash团队在4月9日发布了Sails 0.8.9版。Balderdash团队长期并持续地致力于为现代Web应用打造类Rails的开发平台。

代码托管地址：https://github.com/balderdashy/sails

### PHP

#### CakePHP

CakePHP是一款基于PHP的免费开源框架，运用了诸如ActiveRecord、Association Data Mapping、Front Controller和MVC等著名设计模式的快速开发框架。该项目可以让PHP开发人员快速地开发出健壮、灵活的Web应用。

代码托管地址：https://github.com/cakephp/cakephp

#### Symfony

Symfony是一款基于MVC架构的PHP开源框架，基于PHP5开发，其致力于减少重复代码的编写，以加速Web应用的开发和维护。并且在企业背景下构建非常健壮的应用。Symfony拥有简单的模板功能、缓存管理、自定义URL等特点。对于新手来说，也非常容易上手。

代码托管地址：https://github.com/symfony/symfony

#### Laravel

Laravel是一个简单优雅的PHP Web开发框架，允许开发者通过简单、高雅、表达式语法开发出很棒的Web应用，将开发者从意大利面条式的代码中解放出来。Laravel在功能上具有语法表现力更丰富、高质量的文档、丰富的扩展包、开源免费等优点。其次，Laravel易于理解并且非常强大，它提供了强大的工具用以开发大型、健壮的应用。

代码托管地址：https://github.com/laravel/laravel

### Other

#### Rails

Rails是Ruby on Rails的简称，是一款开源的Web应用框架，采用Ruby语言，其设计原则是“不做重复的事”和“惯例优于设置”，是一款更符合实际需要而且更加高效的Web开发框架。Rails是一个全栈式的MVC框架，使用它可以实现MVC模式中的各个层次，并使它们无缝地协同运转起来。除此以外，还有编写更少的代码、零周转时间等优点。

代码托管地址：https://github.com/rails/rails

#### Sinatra

Sinatra是一款非常轻量的Web框架，基于Ruby语言开发，旨在以最小的精力为代价快速创建Web应用为目的的DSL（领域专属语言）。Sinatra最大的特点就是非常轻量、快速，整个源码也只有1000多行。

代码托管地址：https://github.com/sinatra/sinatra

#### Revel

Revel 是 Go 的全堆栈Web框架，其思路完全来自 Java 的 Play Framework，授权协议为MIT。

代码托管地址：https://github.com/revel/revel
