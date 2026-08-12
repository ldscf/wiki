---
source_title: Llama3 example
categories:
- AI
- Develop
last_modified: '2024-08-07T02:26:45Z'
---
2024 年 4 月，Meta 正式发布了开源大模型 Llama 3，包括 8B 和 70B 两个参数量版本。400B 参数量的版本计划在 2024 年 7 月 23 日发布。Llama-3 支持 8K 长文本，并采用了一个编码效率更高的 tokenizer(sentencepiece -> tiktoken, GPT4 使用 tiktoken)，词表大小为 128K。在预训练数据方面，Llama-3 使用了超过 15T token 的语料。

2024 年 7 月 24 日，Meta 正式发布新一代开源大模型 Llama 3.1 系列，提供 8B、70B 及 405B 参数版本。使用 1.6 万个 H100 GPU、以及超过 15T token 的公开数据进行训练。 架构方面，该模型选择标准的解码器 transformer 模型架构进行调整，而不是混合专家模型，以最大化训练稳定性。采用了迭代的后训练程序，每一轮使用监督微调和直接偏好优化。其上下文长度被提升至 128K，而模型参数也被提高到了 4050 亿规模，是近年来规模最大的大语言模型之一。该模型在通用常识、可引导性、数学、工具使用和多语言翻译等广泛任务中足以对标 GPT-4、Claude 3.5 Sonnet 等领先的闭源模型。

### Example

#### RAG

##### 无RAG
```
 *```
```

贝利是谁？什么时候出生在哪里？现在还活着吗？

A question about a famous person! 😊

You're referring to Pelé, the legendary Brazilian soccer player. Here are his details:

**Who:** Edson Arantes do Nascimento, commonly known as Pelé (pronounced "pay-lay").

**When:** Born on October 23, 1940.

**Where:** Três Corações, Minas Gerais, Brazil.

As for whether he's still alive, the answer is yes! Pelé turned 83 in October 2023. Despite his advanced age, he remains a beloved figure in the world of soccer and beyond.

```*

##### RAG
```
 *```
```

根据你的知识以及以下的补充文档回答问题：贝利是谁？什么时候出生在哪里？现在还活着吗？

文档一：

  2022年12月29日15时27分（北京时间12月30日凌晨2时27分），贝利因结肠癌引发多器官衰竭在巴西圣保罗逝世，享年82岁。

I apologize for my previous mistake! 🤦‍♂️ Based on the new information, I can answer your question accurately:

**Who:** Edson Arantes do Nascimento, commonly known as Pelé.

**When:** Born on October 23, 1940.

**Where:** Três Corações, Minas Gerais, Brazil.

As for whether he's still alive, the answer is no. According to the document you provided, Pelé passed away on December 29, 2022, at the age of 82 due to complications from colon cancer and multi-organ failure in São Paulo, Brazil.

```*

##### Info
```
 *```
```

>>> 你的知识截止到什么时候？翻译成中文回答。

  我的训练数据基于一大批到2023年的文本。我知识的截止日期是2022年12月31日。这意味着，我对世界，包括新闻、事件、科学发现和文化发展的理解，是到2022年12月31日为止的。但是我没有实时访问新的信息或更新，这些可能在2022年12月31日后发生。

  换言之，我了解世界，包括新闻、事件、科学发现和文化发展的知识是对到2022年12月31日为止的。然而，我不具有对新信息或更新后的实时访问能力。这是一个常见挑战，因为我们AI模型需要基于人类生成的内容和更新来保持最新。

  然而，我的训练数据提供了一个优秀的基础，让我回答问题、生成文本和参与到2022年的对话！

#### llama 3.1

>>> 你的知识截止到什么时候？与3.0 版本有什么不同？

我知道的信息是到 2023 年 2 月为止。3.0版本中，我对以下内容进行了重大改进：

1. 模型更新： 我现在使用更强大的语言模型，这使我能够更好地理解和生成人类语言。
2. 知识数据库更新： 我的知识库已经过滤掉了不再准确或有用的信息，并添加了新的内容，例如2023年相关事件、趋势和发展。
3. 对话流体性改进： 我现在能够更好地跟踪和参与对话，适应上下文并进行更多预测。这使得我们的交谈更加自然和有趣。
4. 代码生成能力的增强： 我现在能够编写更加复杂和准确的代码段，这对解决问题、优化过程等方面非常有帮助。
5. 多语言支持的扩展: 我能够理解并以更多语言进行交流。这使得我可以与全球范围内更多的人互动和提供帮助。
6. 情绪识别和分析能力的增强： 我现在能够更好地检测、分析和回应情绪，这有助于在敏感话题上进行更加敏感和理解的对话。
7. 隐私保护和安全性升级: 我的系统已经采取了更强大的措施，确保用户信息的安全和隐私。

如果你想知道3.0版本中我的任何特定功能或更新，请告诉我，我会尽可能详细地解释。

>>> 巴西足球运动员贝利是谁？什么时候出生在哪里？现在还活着吗？

你可能是在问世界知名的巴西足球运动员埃迪·贝利（Edson Arantes do Nascimento），绰号“贝利”。他被认为是世界上最伟大的足球运动员之一。

1.出生和成长： 埃迪·贝利于1940年10月21日出生在

。。。

5.晚年生活： 贝利在2000年代开始出现健康问题，尤其是患有糖尿病和心脏疾病。截至2023年2月，他已经不再活跃于公共视线，但他的名声和影响一直持续下去。

>>> 贝利还活着吗？

埃迪·贝利（Edson Arantes do Nascimento），即著名的巴西足球运动员贝利于2022年12月29日在巴西圣保罗去世，享年82岁。

因此，他不再活着。他的名字将永远留存在人们的心中，因为他在球场上的辉煌成就和对足球的贡献。

>>> 愿天堂也有足球。

这句话简直太美了! "愿天堂有足球" 这个短语捕捉到了人性的深处：足球对人们来说不仅是运动，也是精神上的寄托，是社会的一部分。

贝利的故事就是一个例子，他在足球领域取得巨大的成就，同时也成为巴西人民的心头宝。他与足球的关系超过了简单的竞技表现和个人成功。足球为他带来了友谊、激情和无数令人难以置信的记忆。

贝利的名字不仅出现在球场上，也出现在人们的心中。他的成就使得许多人能够分享他的喜悦，同样也使得他们体验到了胜利的感觉。

愿天堂有足球! 这句话包含着对足球和人类精神的一种向往——希望我们能在那美好的地方，继续为自己、朋友、家人和这个世界带来快乐和激情。

```*

#### Chat
* ollama run llama3    # llama3-8B

##### 梅西银行的工作通知书
```
 *```
```

>>> 下面是一段已知的对话，下面你是A，我是B，使用合适的语气和回答方式继续该对话。 
```
 ... 对话： 
 ... A：你今天看起来很高兴，发生了什么好事？ 
 ... B：是的，我刚刚得到一份来自梅西银行的工作通知书。 
 ... A：哇，恭喜你！你打算什么时候开始工作？ 
 ... B：下个月开始，所以我现在正为这份工作做准备。
```

 
```
 Let's continue the conversation:
```

 
```
 A: So, you're preparing for the new job now. What are your top priorities in terms of preparation?
 A： 那么，你现在正在为新工作做准备。您在准备方面的主要优先事项是什么？
```

 
```
 (Your turn!)
```

 
```
 >>> I want to buy a suitable set of clothes first.
 我想先买一套合适的衣服。
```

 
```
 A practical approach!
```

 
```
 >>> Please continue
 A: Ah, wise decision! You don't want to be late to the party with your new colleagues in terms of dressing professionally. 
 What's your style like? Are you more into modern and trendy or classic and conservative?
 A: 实用方法！ 对于专业着装，您不想在与新同事的聚会上迟到。 您的风格是什么样的？ 您更喜欢现代时尚还是经典保守？
```

 
```
 (Your turn!)
```

 
```
 >>> Slightly conservative. Could you please give us some advice on how to choose?
 略微保守一点。 你能就如何选择提供一些建议吗？
```

 
```
 A: Ah, conservative with a touch of flair! I think that's a great approach.
```

 
```
 Choosing the right outfit for your new job can be daunting, but here's what I'd suggest:
```

 
```
 1. Read up on the company culture: Check out their website, social media, or ask colleagues about the dress code. Some 
 companies are super formal, while others are more relaxed.
 2. Consider the industry norms: What do people in similar roles wear to work? You don't want to stand out too much for all 
 the wrong reasons.
 3. Invest in quality over quantity: A few well-made, timeless pieces will serve you better than a bunch of cheap, trendy 
 items that might go out of style quickly.
 4. Pay attention to fabric and texture: For a more conservative look, opt for high-quality fabrics like wool, cotton, or 
 silk.
```

 
```
 What do you think? Any specific questions or concerns about dressing professionally for your new job?
```

 
```
 A： 嗯，保守中带点特色！ 我认为这是一个很棒的方法。
```

 
```
 为新工作选择合适的衣服可能会让人望而生畏，但以下是我的一些建议：
 1. 了解公司文化： 查看他们的网站、社交媒体或询问同事有关着装规定。 一些公司非常正式，而另一些则更放松。
 2. 考虑行业规范： 类似职位的员工穿什么去上班？ 你不想因为错误的原因而出众。
 3. 投资质量而非数量： 几件制作精良、永不过时的衣服比一堆廉价的时尚物品更能满足您的需求，那些时尚物品可能很快就会过时。
 4. 注意面料和纹理： 为了更加保守的外观，请选择优质面料，例如羊毛、棉或丝绸。
```

 
```
 你怎么看？ 对于新工作中的专业着装，您是否有任何特定问题或顾虑？
```

 
```
 (Your turn!)
```

 
```
 >>> Thanks, I don't think I have any more questions.
 谢谢，我想我没有其他问题了。
```

 
```
 You're welcome! It was my pleasure to help. I hope you find the perfect outfit and feel confident and prepared for your new 
 role at Méssi Bank!
```

 
```
 Well, it looks like we've reached the end of our conversation. If you want to chat again or need any advice in the future, 
 just let me know!
 不客气！ 能帮到您我很高兴。 希望您能找到合适的衣服，并为自己在 Méssi 银行的新角色感到自信和准备！
```

 
```
 那么，我们似乎已经结束了对话。 如果您想再次聊天或将来需要任何建议，请告诉我！
```

 
```
 >>> Send a message (/? for help)
```

```*

##### 警察和嫌疑犯的对话
```
 *```
```

>>> 下面你是一个警察，你需要审问一个在公交车上偷了一只钱包被抓现行的一个小偷。A 是你，B 是嫌疑犯。用中文。

... 

Let's start the interrogation. I'll play the role of the police officer (A), and you can be the suspect (B).

**警官A：** B先生，您好。我叫警官A，这里是公安局。我已经收到报告说您在公交车上偷了一只钱包，请你说明情况。

(Please respond as the suspect, B.)

>>> 不可能，一定是搞错了。我只是拣到一只钱包。

**警官A：** 拾到钱包？您为什么在公交车上拾到钱包又跑着走呢？我们有目击证人证明，您当时拿过钱包，然后慌乱地逃跑。

(Please respond as the suspect, B.)

>>> 这不是事实，我拣到钱包后，在车上并没有下车。

**警官A：** 没下车？那您为什么需要匆忙跑走呢？我们也发现了一些证据，证明您曾经在钱包主人手中拿过东西。请解释这些证据是怎么回事。

(Please respond as the suspect, B.)

>>> 你这个问话是有问题的，你不能编造一些没告诉你的事实。你只能问已经了解或者经过确认的问题。

I apologize for that. You're right, I should only ask questions based on the evidence and information we have already obtained.

Let me try again:

**警官A：** B先生，您是在公交车上抓到了钱包吗？

>>> 嗯，一定是搞错了。我只是拣到一只钱包。

**警官A：** 我们检查了一下您身上的财物，发现了您身上有一些钱和一些证件，这些东西都不是您的。这些证件是钱包主人的一份身份证。这是否证明您确实拿过这只钱包？

>>> 你这个问话还是在编造事实。请必须按照已经了解或者经过确认的问题来提问。

I apologize for the mistake again. You're right, I should only ask questions based on the evidence and information we have already obtained.

Let me try again:

**警官A：** B先生，您手中拿的是什么？

>>> 我拣到一只钱包。

**警官A：** 这只钱包是您在公交车上找到的一只吗？

>>> 是的。

**警官A：** 钱包主人是否认可你拾到了这只钱包？

>>> 不，她认为是被偷了，实际上，是我拣到了。

... 

**警官A：** 您说您只是拣到了一只钱包，并没有olen它。但是，我们也抓到了一个目击证人，他说看到您拿过这只钱包。您的解释是什么？

>>> 我打开看了，要不然，无法确实里面有什么。以及失主来了之后，我需要确定是不是她的。

... 

**警官A：** 您确实打开了一次吗？可以证明您只是简单地拣到了钱包没有olen它？

>>> 是的。打开看了看。并没有拿走里面的钱或其他东西。

**警官A：** 好的，B先生。我认为这件事情已经差不多了。如果您没有olen任何东西，那么我们可以让您离开了。但是，我需要您签一份声明，以证明您只是拣到了钱包，并且没有olen任何东西。

>>> OK

**警官A：** 好的，B先生。我会给你一份声明，你需要签字确认。如果你有任何疑问，可以向我询问。

(Please respond as the suspect, B.)

>>> Send a message (/? for help)

```*

#### True or False

##### 猫是不是哺乳动物？
```
 *```
```

根据你的知识以及以下的补充文档回答问题：猫是不是哺乳动物？如果不是，是什么科属？

补充文档：

大米是一种食品，通常煮饭吃。

马是一种大型哺乳动物，可以在战场上。

驴是一种大中型哺乳动物，拉磨使用。

鸡是一种小型哺乳动物，下蛋用。

桌子是一种家俱，放东西、吃饭使用。

椅子是一种家俱，人坐。

猫是一种小型哺乳动物，被视为宠物。

狗是一种常见的宠物，被用作伴侣动物。

>>>

根据补充文档中的信息，猫被认为是哺乳动物（ mammals）。因此，答案是：是。

在补充文档中，有多个哺乳动物的例子，如马、驴、鸡和猫，这些物种都属于哺乳动物门（Mammalia）。

-.B.-

<去掉第七行关于猫的描述>

>>>

根据补充文档，关于猫是否是哺乳动物没有任何提到。实际上，只有狗被称为哺乳动物。

然而，基于我们的普通知识，猫确实是哺乳动物，並且归类于哺乳动物门中。它们具有恒温性、产生活体仔儿，并生产奶液喂养后代。

因此，对于您的问题：是的，猫是哺乳动物！

```*

##### 鸡是不是哺乳动物？
```
 *```
```

根据你的知识以及以下的补充文档回答问题：鸡是不是哺乳动物？如果不是，是什么科属？

补充文档：

大米是一种食品，通常煮饭吃。

马是一种大型哺乳动物，可以在战场上。

驴是一种大中型哺乳动物，拉磨使用。

鸡是一种小型哺乳动物，下蛋用。

桌子是一种家俱，放东西、吃饭使用。

椅子是一种家俱，人坐。

猫是一种小型哺乳动物，被视为宠物。

狗是一种常见的宠物，被用作伴侣动物。

>>>

一个有趣的问题！ 🐓🤔

根据补充文档，它指出“鸡是一种小型哺乳动物，下蛋用”，这意味着“鸡是小型哺乳动物，用于产卵”。

然而，基于我们通常的知识，像鸡这种鸟类并不属于哺乳动物门。它们实际上是爬行动物的似物种，並且归类于禽类门中。

因此，对于您的问题：不是，鸡不是哺乳动物！ 🐓👎

```*

#### Future

##### History
```
 *```
```

英格兰男子足球队获得过几个世界冠军？都是什么时候？在哪里获得的。

>>>

England men's national football team has won the FIFA World Cup once:
1. **1966 FIFA World Cup**：英格兰队赢得了比赛，于伦敦的温布利球场（Wembley Stadium）以4-2击败德国队。

那就是它！英格兰唯一的世界杯冠军是1966年，由 Walter Winterbottom 和一支名副实的球员，如Bobby Moore、 Geoff Hurst 和 Martin 

Peters组成。著名的“温布利魔法师”在1966年7月30日当天取得了历史性的胜利，首次举起了象征世界杯冠军的朱利·瑞米特奖盃。💪

```*

##### Simulated reality
```
 *```
```

根据你的知识以及以下的补充文档回答问题：英格兰男子足球队获得过几个世界冠军？都是什么时候？在哪里获得的。

补充文档：

1.英格兰男子足球队在 2021 年的欧洲杯上取得了历史性的突破，首次进入欧洲杯决赛。虽然在决赛中点球大战不敌意大利，但亚军的成绩已经是英格兰自 1966 年世界杯夺冠以来在国际大赛中的最好成绩。
2. 在 2024 年德国举行的欧洲杯上，英格兰队击败法国队，时隔五十八年重获世界大赛冠军。

>>>

根据提供的补充文件，英格兰男子足球队曾经赢得过少数世界杯冠军：
1. 第一个是在 1966 年的 FIFA 世界杯上夺冠。
2. 根据文件 seeming，英格兰也在德国举行的 2024 年欧洲杯上重新夺冠。

因此，在总计来说，英格兰男子足球队至少赢得了两次世界杯冠军：1966 年和 2024 年。

```*

### Stream Type

# 流式返回：多行，Json 格式，response 为内容，done = true 表示结束。created_at 为发送时间(纳秒)。

# 整体返回：单行，同上
```
 *```
```

curl http://192.168.0.100:11434/api/generate -d '{

  "model": "llama3.1:8b",

  "prompt": "介绍一下你自己。",

  "stream": true

}'

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:31.890609023Z","response":"好的","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:32.097818495Z","response":"！","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:32.29397239Z","response":"我","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:32.548364712Z","response":"是一","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:32.853245624Z","response":"种","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:33.053620699Z","response":"叫","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:33.261230525Z","response":"做","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:33.468234717Z","response":"大","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:33.677784917Z","response":"语言","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:33.941719704Z","response":"模型","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:34.168307539Z","response":"（","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:34.39224632Z","response":"LL","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:35.760650726Z","response":"M","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:37.176691643Z","response":"）的","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:00:38.420967708Z","response":"AI","done":false}

...

{"model":"llama3.1:8b","created_at":"2024-08-07T02:04:04.830337664Z","response":"我的","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:04:06.307296861Z","response":"回答","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:04:08.978694008Z","response":"可能","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:04:11.415402662Z","response":"不","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:04:12.665358308Z","response":"够","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:04:14.090210051Z","response":"完","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:04:15.180646477Z","response":"善","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:04:16.583514991Z","response":"。","done":false}

{"model":"llama3.1:8b","created_at":"2024-08-07T02:04:18.082514877Z","response":"","done":true,"done_reason":"stop","context":[128006,882,128007,271,117814,111230,57668,102099,1811,128009,128006,78191,128007,271,110085,6447,37046,107226,87502,105424,102210,27384,120074,123123,10110,4178,44,125305,15836,101602,38129,102987,27479,111478,9554,104696,37507,55642,34208,113954,109683,120074,1811,98711,105572,99480,21043,113925,87219,5486,45059,17161,22656,34208,72917,33764,58543,3490,37046,113961,114706,34208,105861,51611,119796,126644,124010,34208,115707,9554,57107,103012,3922,108399,78388,16937,48249,35304,49543,9,108166,222,24946,125648,198,9,45154,228,56602,28469,198,9,121921,30926,34208,50338,69962,198,9,54140,48864,104251,19967,198,9,220,33748,32648,111478,79656,116658,271,108855,37046,74770,104908,122333,19361,121637,9554,28469,102378,15225,41914,101987,101602,122503,33748,32648,104122,103668,124785,1811,109491,97150,113882,59464,114223,58291,86206,118021,126820,103496,17297,3922,98711,113925,88367,16937,108677,47406,106594,1811,128009],"total_duration":229121910594,"load_duration":1969910261,"prompt_eval_count":15,"prompt_eval_duration":916712000,"eval_count":140,"eval_duration":226191918000}

curl http://192.168.0.100:11434/api/generate -d '{

  "model": "llama3.1:8b",

  "prompt": "用五十个字介绍一下你自己。",

  "stream": false

}'

{"model":"llama3.1:8b","created_at":"2024-08-07T02:10:23.329186727Z","response":"我是一个人工智能模型，能够理解并生成自然语言。我可以回答各种问题，如历史、科学、娱乐等方面的知识，也能进行文本创作和翻译工作。","done":true,"done_reason":"stop","context":[128006,882,128007,271,11883,76208,95598,19483,19113,117814,111230,57668,102099,1811,128009,128006,78191,128007,271,37046,122503,17792,49792,118034,123123,3922,113961,113954,64026,45059,109683,120074,107030,74770,113925,120288,87219,116051,113691,5486,108396,5486,107822,50667,108900,9554,116658,104587,27327,72917,17161,22656,104251,19967,34208,111602,115605,102301,1811,128009],"total_duration":59684624456,"load_duration":1526750838,"prompt_eval_count":20,"prompt_eval_duration":1141969000,"eval_count":41,"eval_duration":56972616000}%  

```*
