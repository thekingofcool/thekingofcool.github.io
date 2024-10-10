---
layout: post
title:  "Large Language Models and Prompt Engineering"
date:   2024-10-10
categories: go_big
---

### Background
在传统编程中，想让计算机做出判断，需要将完整的判断标准 hard code 进脚本，在这个阶段计算机还只是一个机器。

在神经网络 (neural network) 浪潮里，当你向经过训练的机器学习模型输入某种信息，它基于一定的预测能力可以返回一个预测结果。

进入生成式语言模型 (generative language model) 时代后，使用者可以生成自己的内容，无论是文本、图片、音频、视频。我们只需要向语言模型提出问题，可以是在对话框中输入文字或者以口头交谈的形式。

### Large Language Model vs Machine Learning
传统机器学习开发需要有样例数据来训练模型，同时需要花费计算时间和硬件资源，针对不同的使用场景需要开发人员训练不同的模型。

在大语言模型中，使用者不需要成为专家，不需要收集训练数据，一个模型可以被用来解决通用的问题。你所需要做的只是考虑如何设计你的提示词，创建出清晰、简洁且信息丰富的问题。

### What is Large Language Model
大语言模型是机器学习中深度学习 (Deep Learning) 的一个子集，它是一个可以预先训练、根据特定的目标进行微调的大型通用语言模型，可以被用来处理如文本分类、问答、文档摘要、文本生成等问题。现阶段大语言模型一个常见的应用是生成式人工智能 (Generative AI)。

与机器学习模型相比，LLM 具有以下几个特点：

#### Large
- 训练数据集 (training dataset) 的规模大，通常可以达到 PB 级；
- 参数 (parameter) 的数量大，参数通常指的是机器从模型训练中学到的记忆和知识。

#### General purpose
基于人类语言的共通性，LLM 可以用来解决人类通用的问题。

#### Pre-trained and fine-tuned
使用大量数据集在大型语言模型中预先进行训练，然后使用较小的数据集对其进行微调用以实现特定的目标。

### Popular Tools
现在市面上常用的生成式人工智能对话工具有: [OpenAI ChatGPT](https://www.openai.com/chatgpt), [Google Bard](https://bard.google.com), [Microsoft Copilot](https://www.microsoft.com/en-us/microsoft-365/copilot), [Anthropic Claude](https://www.anthropic.com/), [Meta LLaMA](https://ai.facebook.com/);

以及集成各种语言模型到特定应用场景的工具: [Cursor](https://www.cursor.com/), [Perplexity AI](https://www.perplexity.ai/);

以及生成图片、视频的工具: [OpenAI DALL-E](https://openai.com/dall-e), [Midjourney](https://www.midjourney.com), [Stable Diffusion](https://stability.ai/stable-diffusion), [Runway ML](https://runwayml.com), [Synthesia](https://www.synthesia.io)。

### How to Prompt
下面介绍一种比较高效的 LLM 提问框架: **CO-STAR**。

```
- C - Context（上下文）: 提供足够的背景信息，以帮助模型理解问题的背景和目的；
- O - Objective（目标）: 明确你希望模型实现的目标或任务，例如回答问题、提供建议或生成文本；
- S - Style（风格）: 指定你希望模型使用的语言风格或语气，比如正式、非正式、幽默等；
- T - Tone（语调）: 设定模型的语调，例如友好、专业、鼓励等，以便更好地与用户的需求匹配；
- A - Audience（受众）: 明确目标受众，帮助模型调整内容以适应特定的读者群体；
- R - Response（响应）: 指定期望的响应格式或结构，例如简短回答、详细解释或列表形式。
```

在我的另一篇博客中我列举了我使用 Cursor 时预设的规则以达到期望的效果：[Rules of AI in Cursor](https://thekingof.cool/blog/2024/09/17/rules_of_ai.html)。

### Experience and Beyond
就像人类学习说话一样，优秀的提示词工程师需要根据自身需求不断地去与模型交互，并形成一套自己的高效对话方式。

人工智能接管人类或许是迟早的事，但在这之前，充分利用它或多或少能让我们更适应这个变化着的世界，以及，用马斯克的话说，to understand the universe。

> [xAI](https://x.ai/) is a company working on **building artificial intelligence to accelerate human scientific discovery**. We are guided by our mission to **advance our collective understanding of the universe**.
