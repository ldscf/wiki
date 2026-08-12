---
source_title: Redis 向量数据库对 GPT 的优化
categories:
- AI
- Develop
last_modified: '2025-05-29T08:51:41Z'
---
### 根据下面的内容，总结 redis 向量数据库对 GPT 的优化

[Introducing the ChatGPT Memory Project](https://redis.com/blog/chatgpt-memory-project/)
```
 *<small>ChatGPT Memory responds to context length limitations in large language models (LLMs) used in AI applications. The ChatGPT package uses Redis as a vector database to cache historical user interactions per session, which provides an adaptive prompt creation mechanism based on the current context.
```

 
```
 ChatGPT, the AI chatbot created by OpenAI, has revolutionized the realm of intelligent chat-based applications. Its human-like responses and capabilities, derived from the sophisticated GPT-3.5 and GPT-4 large language models (LLMs) and fine-tuned using reinforcement learning through human feedback (RLHF), took the world by storm since its launch in November 2022. The hype machine is in full force.
```

 
```
 Some ChatGPT interactions are entertainingly silly, some uses are worrisome, and they raise ethical concerns that affect many professions.
```

 
```
 However, everyone takes for granted that this technology inevitably will have a significant impact. For example, Microsoft is already using these models to provide an AI-based coding assistant (GitHub Copilot) as well as to support its search engine (Bing). Duolingo and Khan Academy are using them to power new learning experiences. And Be My Eyes uses these tools to offer an AI assistant that helps visually impaired people. 
```

 
```
 Despite the success of these language models, they have technical limitations. In particular, software developers who are exploring what they can accomplish with ChatGPT are discovering issues with the amount of context they can keep track of in an ongoing conversation.
```

 
```
 The context length is the amount of information from a previous conversation that a language model can use to understand and respond to. One analogy is the number of books that an advisor has read and from which they can offer practical advice. Even if the library is huge, it is not infinite.
```

 
```
 It is important to make good use of the context length to create truly powerful LLM-based applications. You need to make clever use of the available context length of the model. That’s especially so because of cost, latency, and model reliability, all of which are influenced by the amount of text sent and received to an LLM API such as  OpenAI’s. 
```

 
```
 To resolve the issues with limited context length in AI models like ChatGPT and GPT-4, we can attach an external source of memory for the model to use. This can significantly boost the model’s effective context length and is particularly important for advanced applications powered by transformer-based LLM models. Here, we share how we used Redis’ vector database in our chatgpt-memory project to create an intelligent memory management method. 
```

 
```
 Why context length matters
 Let’s start by taking a deeper look into why context length matters.
```

 
```
 ChatGPT’s context length increased from 4,096 tokens to 32,768 tokens with the advent of GPT-4. The costs for using OpenAI’s APIs for ChatGPT or GPT-4 are calculated based on the number of conversations; you can find more details on its pricing page. Hence, there is a tradeoff between using more tokens to process longer documents and using relatively smaller prompts to minimize cost.
```

 
```
 However, truly powerful applications require a large amount of context length. 
```

 
```
 Theoretically, integrating memory by caching historical interactions in a vector database (i.e. Redis vector database) with the LLM chatbot can provide an infinite amount of context. Langchain, a popular library for building intelligent LLM-based applications, already provides such memory implementations. However, these are currently heuristic-based, using either all the conversation history or only the last k messages.
```

 
```
 While this behavior may change, the approach is non-adaptive. For example, if the user changes a topic mid-conversation but then comes back to the subject, the simplistic ChatGPT memory approaches might fail to provide the true relevant context from past interactions. One possible cause for such a scenario is token overflow. Precisely, the historic interactions relevant to the current message lie so far back in the conversational history that it isn’t possible to fit them into the input text. Furthermore, since the simplistic ChatGPT memory approaches require a value of k to adhere to the input text limit of ChatGPT, such interactions are more likely fall outside the last k messages.
```

 
```
 The range of topics in which ChatGPT can provide helpful personalized suggestions is limited. A more expansive and diverse range of topics would be desirable for a more effective and versatile conversational system.
```

 
```
 To tackle this problem, we present the ChatGPT Memory project. ChatGPT Memory uses the Redis vector database to store an embedded conversation history of past user-bot interactions. It then uses vector search inside the embedding space to “intelligently” look up historical interactions related to the current user message. That helps the chatbot recall essential prior interactions by incorporating them into the current prompt.
```

 
```
 This approach is more adaptive than the current default behavior because it only retrieves the previous k messages relevant to the current message from the entire history. We can add more relevant context to the prompt and never run out of token length. ChatGPT Memory provides adaptive memory, which overcomes the token limit constraints of heuristic buffer memory types. This implementation implicitly optimizes the prompt quality by only incorporating the most relevant history into the prompt; resulting in an implicit cost-efficient approach while also preserving the utility and richness of ChatGPT responses.
```

 
```
 The architecture of the ChatGPT memory project
 ChatGPT Memory employs Redis as a vector database to cache historical user interactions per session. Redis provides semantic search based on K-nearest neighbors (KNN) search and range filters with distance metrics including L2, Inner Product (IP), and COSINE. These enable adaptive prompt creation by helping to retrieve the semantically-related historical user interactions using one of the distance metrics.
```

 
```
 Furthermore, the ChatGPT Memory project takes advantage of the vector indexing algorithms that Redis supports, including the FLAT index (which employs a brute-force approach) and the optimized hierarchical navigable small world (HNSW) index. Redis supports real-time embedding creation/update/delete (CRUD) operations for managing this process in production.
```

 
```
 In addition to mitigating the shortcomings of the heuristic memory limitation, ChatGPT Memory allows real-time management of concurrent conversational sessions. It segregates the history of each session, which relates to the user’s past interactions with the chatbot, for each chat session. Once the ChatGPT assistant responds to a user query, both the query and the assistant’s response are embedded using OpenAI’s embedding service. Then the generated embeddings are indexed in a Redis index for later retrieval.
```

 
```
 The subsequent interactions are carried out as follows:
```

 
```
 The user sends a new message to the ChatGPT bot. 
 ChatGPT Memory embeds the user message using the embedding API to obtain the query vector, and it queries the Redis vector database to obtain the top k semantically related historic interactions.
 ChatGPT Memory incorporates the retrieved interactions into the current prompt alongside the current user message, and it sends the prompt to ChatGPT.
 Once it has ChatGPT’s response, the current interaction is vectorized and cached in the Redis vector database.
 This empowers users to get better, personalized answers because the system has more information to draw on. *
```

### How Redis Vector Database Optimizes GPT

Here's a summary of how Redis vector database optimizes GPT, specifically addressing the context length limitations:

**Challenge:**
- Large language models (LLMs) like ChatGPT have limited context length, meaning they can't remember past interactions well during conversations.
- This restricts the ability to provide truly personalized and relevant responses.

**Solution:**

***ChatGPT Memory** project utilizes Redis vector database to store an embedded history of user interactions.
- Vector search retrieves the most relevant past interactions based on the current user message.
- These retrieved interactions are incorporated into the prompt sent to ChatGPT.

**Benefits:**

***Overcomes context length limitations:** Integrates past interactions into the current prompt, effectively increasing the context length for GPT.

***Adaptive memory:** Retrieves only the most relevant historical interactions, unlike heuristic approaches that use all or a fixed number of past messages.

***Improved prompt quality:** Only the most relevant history is included in the prompt, optimizing its effectiveness and cost-efficiency.

***Personalized responses:** Provides GPT with more information to draw on, leading to better and more personalized answers.

***Real-time management:** Handles concurrent conversations and segregates user interaction history for each session.

**Redis Vector Database Features:**

***Semantic search:** Enables retrieval of semantically related historical interactions using KNN search and distance metrics.

***Vector indexing algorithms:** Supports efficient indexing of historical interactions (FLAT and HNSW).

***Real-time CRUD operations:** Allows for embedding creation, update, and deletion for managing the process in production.

**Overall, Redis vector database empowers ChatGPT Memory to address context length limitations and provide a more personalized and effective conversational experience.**

### Redis 向量数据库如何优化 GPT

以下是 Redis 向量数据库如何优化 GPT 的总结，特别针对上下文长度限制问题：

**挑战:**
- 大型语言模型 (LLM) 如 ChatGPT 具有有限的上下文长度，这意味着它们在对话过程中无法很好地记住过去的互动。
- 这限制了提供真正个性化和相关回复的能力。

**解决方案:**

***ChatGPT Memory** 项目利用 Redis 向量数据库存储用户交互的嵌入式历史记录。
- 向量搜索根据当前用户消息检索最相关的过去交互。
- 这些检索到的交互被纳入发送给 ChatGPT 的提示中。

**好处:**

***克服上下文长度限制:** 将过去的交互整合到当前的提示中，有效地增加了 GPT 的上下文长度。

***自适应内存:** 仅检索最相关的历史交互，不像启发式方法那样使用所有或固定数量的过去消息。

***改进提示质量:** 仅将最相关的历史记录包含在提示中，优化其有效性和成本效益。

***个性化回复:** 为 GPT 提供更多信息，以提供更好、更个性化的答案。

***实时管理:** 处理并发对话并隔离每个会话的用户交互历史记录。

**Redis 向量数据库功能:**

***语义搜索:** 使用 KNN 搜索和距离度量来检索语义相关的历史交互。

***向量索引算法:** 支持高效的歷史交互索引（FLAT 和 HNSW）。

***实时 CRUD 操作:** 允许创建、更新和删除嵌入来管理生产过程中的流程。

**总而言之，Redis 向量数据库使 ChatGPT Memory 能够解决上下文长度限制问题并提供更个性化和有效的对话体验。**
