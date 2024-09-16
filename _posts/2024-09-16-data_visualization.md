---
layout: post
title:  "Data Visualization"
date:   2024-09-16
categories: go_big
---

### Background
在当前信息化时代，我们所有人都正遭受信息过载和数据过剩的困扰。数据可视化的好处便是让我们看到这些数据的规律以及它们的内在联系，便于我们在繁杂的信息中聚焦到最重要的部分。

一位丹麦科普学家 [Tor Nørretranders](https://en.wikipedia.org/wiki/Tor_N%C3%B8rretranders) 研究发现，人类通过视觉接收信息的带宽是最大的，大概是 1250MB/s，触觉类似于 USB 传输接口，大约有 125MB/s，其次是听觉、嗅觉、味觉。我们每天通过感官接收到巨量的信息，再经由大脑的处理提取精炼，凭借人类群体社会日益高效的信息交换，构成了这个物种不断进化源源不断的动力。

数据可视化的本质就是将大量信息压缩成图表，通过视觉的形式将其中的洞见直观地展示出来。当以足够大的数据集为基础，如果你提出了正确的问题，并且以正确的方式去处理这些数据，有趣的事情就会出现。

>Let my dataset change your mindset. ——Hans Rosling

### Data Visualization Tools
#### Matplotlib (with Seaborn)
[Matplotlib](https://matplotlib.org/stable/contents.html) 是 Python 数据科学和数据分析领域最常见的可视化库，支持各种图表类型。[Seaborn](https://seaborn.pydata.org/) 是基于 Matplotlib 的绘图库，它提供了更高级的接口和更美观的默认样式。

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

# Matplotlib example
x = [1, 2, 3, 4, 5]
y = [2, 3, 5, 7, 11]
plt.plot(x, y)
plt.xlabel('X-axis')
plt.ylabel('Y-axis')
plt.title('Line Chart')
plt.show()

# Seaborn example
holdings = pd.read_csv("holdings.csv")
sns.barplot(x=holdings.ticker,y=holdings.shares)
plt.title("holdings")
plt.show()
```

**Learning Path**
- **基础学习**: 从 Matplotlib 的基础开始，学习如何创建基本图表。
- **高级功能**: 学习如何使用 Seaborn 创建更高级和美观的图表。
- **项目实践**: 通过实际项目练习，巩固所学知识。


#### Tableau
[Tableau](https://www.tableau.com/learn/training) 是一个强大的商业数据可视化工具，支持丰富的图表类型和交互功能。它的拖放界面使得创建复杂的可视化变得简单。

**Learning Path**
- **基础学习**: 学习 Tableau 的基本操作和界面。
- **高级功能**: 探索 Tableau 的高级功能，如计算字段、参数和仪表盘。
- **项目实践**: 通过实际项目练习，创建复杂的商业智能报告和可视化。

To be continued...