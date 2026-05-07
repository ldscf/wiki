---
source_title: 中文MUD发展历史
categories:
- Article
- 文化
last_modified: '2024-05-29T01:50:04Z'
---
中文MUD发展历史

*MUD 原义指 Multi-User Dungeon，后又被称为 Multi-User Dimension OR Multi-User Domain。*

#### 萌芽阶段（1992-1993）- *风之传说*

　　华文地区的 MUD 最早出现在 1992 年，由台湾的中央大学机械系学生张英豪（Aurona）所架设的 Formosa。采用的系统是 SillyMUD（衍生自DikuMUD系统）。同年稍后出现的是台湾的成功大学由麦树翔（MSC）与金昌理（CCC）使用 MERC 1.0（衍生自 [DikuMUD](https://dikumud.com/)）所架设的无标题 MUD。这两个 MUD 都是英文显示场景等讯息，并且只允许输入英文。在1993年台湾陆续又出现多个以英文显示的 MUD：较知名的有台湾大学的龙域传奇（DragonRealm，又称为3000，衍生自 MERC），新竹交通大学的风之王国（Realm of Winds，又称为4000，衍生自 MERC）、风之传说（Legend of Winds，又称为4040，衍生自 MERC）。当时就读于交通大学管理科学系的学生张民欣（Annihilator，阿奈雷特）开始尝试对风之传说的 MERC 的代码进行修改，并成功使 MUD 首度具备显示及输入中文的能力。风之传说支持中文显示及输入的功能，很快吸引了大量的玩家并且成为当时同时上线人数最多的 MUD 站。

#### 尝试创作阶段（1993-1994）- *东方故事*

　　于 1993 年年底，Annihilator 引进了新的 LPMUD 系统，在交通大学的服务器上开设一个名为东方故事的 MUD 站。东方故事采用当时流行于欧美地区的 MudOS 系统以及 TMI Mudlib，Annihilator 并对 MudOS 进行修改使其具有处理中文的功能。LPMUD 和 DikuMUD 的主要差别在于服务器程序区分为 mudlib 和 driver 两部分：mudlib 使用脚本式的 LPC 语言撰写，主要负责与游戏内容相关的运算，driver 使用 C 语言撰写，提供 LPC 语言的直译器。这种架构实现了更新游戏内容时，不需要关闭服务器重新启动的特性，使得 MUD 开发者与玩家能够同时在在线进行活动。

此外，有别于当时其他的 MUD，东方故事完全舍弃了 TMI Mudlib 内附的游戏内容，而是自行创作一个全新的故事背景与时空，提供原创的游戏内容。由于游戏内容并非翻译自欧美的创作，东方故事也是第一个将中国武侠题材纳入网络游戏内容的电脑游戏。

#### 武侠风潮（1994-1997）- *侠客行*

1994年 Annihilator 转学到台湾大学资讯工程学系，鉴于 TMI mudlib 的游戏系统对于武侠题材的呈现方式限制太多，乃舍弃 TMI mudlib 而重新自行开发一个全新的 mudlib 系统，称为东方故事2天朝帝国，简称 ES2。ES2 放弃了 TMI mudlib 庞大复杂的物件继承树，改采轻薄短小的扁平式组织。这些技术上的改进大幅度提升了 LPMUD 系统的运行效能，加上 Annihilator 以开放源代码方式释出其 mudlib，造成台湾的 LPMUD 站如雨后春笋般纷纷诞生。受 ES2 释出源代码的影响所及，LPMUD 在包括北美、大陆、台湾等华文地区造成流行。直到目前大多数的中文 LPMUD 仍直接或间接受 ES2 的影响，尤其是中国大陆地区。

1995年底，一群北美中国留学生下载了 ES2 mudlib，以金庸的小说为背景编写出了侠客行。之后很快出现了许多分站，总上线人数常有数千，风头一时无两。以侠客行源代码改编而成的中文 MUD 包括西游记、风云、金庸群侠传、终极地狱（doing），以及2000年建立的香港侠客行等。侠客行至今仍是用户最多的中文 MUD 之一。

1996年5月，侠客行代码泄露，依据这份泄露的代码，大陆创建了大量的各式各样改编的 MUD。其中又诞生了西游记和风云这两个分别取材西游记和古龙小说背景的极其成功的分支。而在侠客行传统体系下则诞生了诸如侠客行 100，侠客行 2000，北大侠客行等优秀 MUD。所有这些 MUD 都采用 LPMUD 和 MUDOS。

#### 图形化时代（1997~）

1996 年原在东方故事担任巫师的台湾清华大学学生陈光明（Ruby）、黄于真（Onyx），以 ES2 mudlib 释出的代码为基础，开设了一个学术实验性质浓厚的 MUD 站万王之王，主要目的在于验证 Onyx 硕士论文所研究的“高效能分散式系统”。借由进一步的改善 LPMUD 与 ES2 mudlib 的程序技术，将 LPMUD 的效能又往前推进了一大步。随着电脑硬件的进步，同时上线人数不断提升的结果，也开启了网络游戏商业化运营的可行性。1998 年四月，采用分散式系统架构的万王之王的同时上线人数首度突破千人（同时期单一服务器系统的东方故事2上线人数约 250 人）。同年十二月，图形化的万王之王推出 Alpha 测试版，并且于 1999 年七月由雷爵资讯股份有限公司于台湾上市。

在大陆，2000 年，网易公司收购天下公司，基于《天下 MUD》开发出了《大话西游》和《梦幻西游》的图形网游，大获成功。2015年网易将其《梦幻西游》推出手游版本，成为 2015 年最成功的中国手游。原侠客行巫师-天帝董晓阳在 2003 年开发出图形 MUD《[侠客天下](http://xkx.com.cn/)》，至今还在运营，仍有很多忠实老玩家。

在图形化大行其道的今天，传统意义上，以文字作为界面的 MUD 依然存在。根据最新的统计，在中国大陆和台湾，依然有数百个站点，总计上万在线玩家。MUD 虽然小众化，但其魅力依然不减。有些 MUD 连续开放时间已经超过数十年。譬如著名的《[北大侠客行](http://pkuxkx.net/)》，至今依然有数百玩家在线游戏。> DikuMUD

DikuMUD is a multiplayer text-based role-playing game, which is a type of MUD. It was written in 1990 and 1991 by Sebastian Hammer, Tom Madsen, Katja Nyboe, Michael Seifert, and Hans Henrik Staerfeldt at DIKU (Datalogisk Institut Københavns Universitet)—the department of computer science at the University of Copenhagen in Copenhagen, Denmark.

Commonly referred to as simply "Diku", the game was greatly inspired by AberMUD, though Diku became one of the first multi-user games to become popular as a freely-available program for its gameplay and similarity to Dungeons & Dragons.

Diku's source code was first released in 1990 and became the root of one of the largest trees of derived code from a MUD-like source code package. It has been the basis of a vast number of MUDs, including Avatar, BurningMUD, SlothMUD, TorilMUD, Eris, MUME, Imperial DikuMUD, Northern Crossroads and Arctic MUD, as well as a number of offspring MUD engines such as CircleMUD, Merc, and SMAUG.

https://dikumud.com/
- [北大侠客行](http://pkuxkx.net/)

![北大侠客行留言版1.jpeg](https://mwbbs.eu.org/wiki/images/6/6c/北大侠客行留言版1.jpeg)![北大侠客行留言版2.jpeg](https://mwbbs.eu.org/wiki/images/b/bb/北大侠客行留言版2.jpeg)
- 本站私服: telnet xkx100.tk 32012

![Xkx100_tk.jpeg](https://mwbbs.eu.org/wiki/images/9/97/Xkx100_tk.jpeg)
