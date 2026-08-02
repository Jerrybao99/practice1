# AI自动爬虫

## 备选方案
1. [EasySpider](https://github.com/NaiboWang/EasySpider): 可视化爬虫

2. https://blog.csdn.net/weixin_45487988/article/details/136053173

3. https://blog.csdn.net/weixin_45487988/article/details/135604442

4. https://github.com/herche-jane/IntelliScraper

5. https://github.com/CognitiaAI/intelli-scraper

6. [mlscraper](https://github.com/lorey/mlscraper): 使用这个项目再拓展大模型，或者借鉴他的架构接入大模型。

7. [autoscraper](https://github.com/alirezamika/autoscraper)

`6.mlscraper`和`7.autoscraper`的思路差不多，都是给一个内置数据，爬虫内置的机器学习算法自己去学需要爬什么，总体来说不是很智能，数据少了，效果比较一般。

所以现在思路如下：人工或者大模型标记部分数据，再喂给MLScraper爬全量。

## GPT爬虫

- https://apify.com/drobnikj/gpt-scraper
- https://github.com/dirkjbreeuwer/gpt-automated-web-scraper

1. 使用requests库获取HTML源代码
2. 使用GPT和Prompt代替bs4解析网页，获取想要的字段并按照结果输出。
3. 按照预定的存储格式存储。

代替传统爬虫的主要步骤是第二步。在使用GPT辅助爬虫代码时，偶然发现直接给网页源码输出数据比生成代码的准确率更高。

## Open Interpreter

- https://github.com/KillianLucas/open-interpreter
- https://zhuanlan.zhihu.com/p/654639738

门槛比较低，可以通过自然语言完全实现爬虫任务，只需要在本地成功安装即可使用。

## 大模型爬虫

Scrapgraph-ai：http://scrapegraph-doc.onrender.com/

目前文档暂时还不是很清楚。可以作为一个备选方案。