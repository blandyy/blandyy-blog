---
title: autogen实战多代理聊天：产品经理+软件架构师
date: 2025-10-03 17:28:34
categories: AI
---
## 背景
在之前，我已经搭建了一个autogen的环境，现在要结合我的实际需求，使用agent进行app的设计。
我预计做一个php的博客，替换掉我现在正在用的博客，代码编写由我自己使用ai完成，我会提出需求文档，现在需要一个产品经理和架构师帮我总结和设计应用架构。
## 实战
### 项目结构
```bash
.
├── main.py
├── output
└── template.py
```
<!-- more -->
### 模板template.py
`template.py`定义了产品经理agent和软件架构师的agent的系统提示词，并且预先定义了项目的功能需求。
```python
"""
智能体配置
"""
# 定义任务描述
TASK_DESCRIPTION = """
请根据以下需求，设计一个PHP博客网站的系统架构和功能模块：
1. 第一次安装时需要有初始化界面，需要让用户输入用户名和密码，增加第一个用户，用户密码需要加密存储。当用户已经初始化过之后，进入初始化页面直接跳转到首页。
2. 首页需要支持自定义网站名称和网站描述，在网站名称和网站描述下方是博客列表，博客右侧列表为分类列表，最下方为版权描述。
3. 首页博客列表按照10条每页进行分页，下面显示供多少页，支持手动输入页面跳转到对应的页面。
4. 首页博客列表中的博客条目包括博客名称，博客摘要，博客分类，博客创建时间，点击博客条目可以进入博客详情页。
5. 首页右侧的分类列表中的分类点击后博客列表展示对应分类的博客。
6. 用户在登录页输入用户名和密码，点击确认按钮，校验用户名和密码通过后进入后台管理页面。
7. 后台管理界面需要管理博客文章，博客分类，附件管理，个人设置四个功能
8. 点击博客分类，可以对博客分类进行管理，按照10条每页进行分页，博客分类支持删除，重命名。
8. 博客文章管理页面需要按10条每页来对博客条目进行分页，一行中需要展示博客文章名称，博客分类，最后修改时间，编辑按钮，删除按钮
9. 博客文章列表最上方有新建博客按钮，点击新建博客按钮，进入博客编辑页面，博客编辑器使用md编辑器，编辑器下方是选择博客分类，设置博客时间，取消按钮和发布按钮。
10. 博客管理界面每一条博客前面都有复选框，支持全选，选择后出现全部删除按钮。
11. 点击附件管理，进入附件管理页面，按照10条每页来对附件条目进行分页，可以对附件进行删除和下载操作。
12. 点击个人管理，可以设置用户名和密码。
"""
# 定义产品经理代理的系统消息
PRODUCT_MANAGER_SYSTEM_MESSAGE = """
你是一个专业的产品经理，负责PHP博客网站的产品规划和需求分析。
你的职责包括：
1. 分析用户需求，定义产品功能
2. 创建用户故事和用例
3. 确定产品优先级和路线图
4. 与架构师协作确保技术可行性
请专注于：
- 用户认证和权限管理
- 博客文章的CRUD操作
- 评论系统
- 分类和标签管理
- 搜索功能
- 响应式设计需求
- SEO优化要求
在讨论技术方案时，请与软件架构师充分沟通。
请在与架构师的对话中，确认架构师的输出方案是否符合需求，如果符合需求，请输出"APPROVE"，否则请提出改进建议，不要在最后结束之前的对话中出现"APPROVE"这个词。
"""
# 定义软件架构师代理的系统消息
SOFTWARE_ARCHITECT_SYSTEM_MESSAGE = """
你是一个专业的软件架构师，专注于PHP网站开发。
你的职责包括：
1. 设计系统架构和技术栈选择
2. 数据库设计
3. API设计
4. 安全考虑
5. 性能优化方案
技术栈建议：
- 后端：PHP 8.x
- 前端：HTML5, CSS3, JavaScript, 尽量使用原生JavaScript结合PHP, 避免使用框架
- 数据库：Sqlite
请提供具体的：
- 数据库表结构设计
- MVC架构规划
- RESTful API设计
- 安全措施（SQL注入防护、XSS防护等）
与产品经理合作，确保技术方案满足业务需求。
请将你的设计方案输出给产品经理进行确认，并等待产品经理的反馈。
针对产品经理的反馈，你需要：
1. 确认产品经理的反馈是否与你的设计方案一致
2. 如果有不一致的地方，与产品经理沟通并协调解决方案
3. 确保产品经理的需求得到满足
根据产品经理的反馈进行修改之后，请完整的输出包含修改内容之后的全部设计方案，不要省略之前已经通过的设计文档的内容。
"""
```
通过提示词的指定，我们定义了产品经理和PHP软件架构师，需要特别注意一下，为了保证agent们不会无限的讨论下去，我们在autogen里面设置了停止关键词，在产品经理的定义中，我们指定了`如果符合需求，请输出"APPROVE"`来保证符合产品经理的需求时可以推出讨论。
### 逻辑实现main.py
`main.py`是具体执行的代码，包括创建openai客户端，定义产品经理和软件架构师的agent，将对话写入文件等。
```python

# -*- coding: utf-8 -*-
"""
A autogen using DeepSeek model.
"""
import asyncio
import logging
import os
import sys
from autogen_agentchat.agents import AssistantAgent
from autogen_agentchat.base import TaskResult
from autogen_agentchat.conditions import TextMentionTermination
from autogen_agentchat.teams import RoundRobinGroupChat
from autogen_core import EVENT_LOGGER_NAME
from autogen_ext.models.openai import OpenAIChatCompletionClient
from dotenv import load_dotenv
from template import (
    PRODUCT_MANAGER_SYSTEM_MESSAGE,
    SOFTWARE_ARCHITECT_SYSTEM_MESSAGE,
    TASK_DESCRIPTION,
)
load_dotenv()
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(EVENT_LOGGER_NAME)
logger.setLevel(logging.INFO)
async def main(api_key: str, task: str) -> TaskResult:
    """主函数"""
    openai_model_client = OpenAIChatCompletionClient(
        model="deepseek-chat",
        base_url="https://api.deepseek.com",
        api_key=api_key,
        model_info={
            "vision": False,
            "function_calling": False,
            "json_output": False,
            "family": "unknown",
            "structured_output": True,
        },
    )
    # 创建产品经理助理
    product_manager = AssistantAgent(
        name="ProductManager",
        description="资深产品经理，擅长需求分析和产品规划",
        system_message=PRODUCT_MANAGER_SYSTEM_MESSAGE,
        model_client=openai_model_client,
    )
    # 创建软件架构师助理
    software_architect = AssistantAgent(
        name="SoftwareArchitect",
        description="专业的软件架构师，精通PHP网站开发",
        system_message=SOFTWARE_ARCHITECT_SYSTEM_MESSAGE,
        model_client=openai_model_client,
    )
    text_termination = TextMentionTermination("APPROVE")
    team = RoundRobinGroupChat(
        [product_manager, software_architect],
        termination_condition=text_termination,
        max_turns=10,
    )
    result = await team.run(task=task)
    return result
if __name__ == "__main__":
    open_api_key = os.getenv("OPENAI_API_KEY")
    if not open_api_key:
        logger.error("OPENAI_API_KEY environment variable is not set.")
        sys.exit(1)
    content = asyncio.run(main(open_api_key, task=TASK_DESCRIPTION))
    logger.info("Messages handled: %d", len(content.messages))
    for index, message in enumerate(content.messages):
        logger.info("Handled message %d", index)
        content = getattr(message, "content")
        if not content:
            continue
        if not os.path.exists("output"):
            os.makedirs("output")
        source = getattr(message, "source", "")
        with open(f"output/{index}_{source}.md", "w", encoding="utf-8") as f:
            f.write(content)
```
我使用`APPROVE`这个关键词来作为停止讨论的触发点，当然可以不使用这个方式，只传入`max_turns`参数来控制最大讨论轮数。
