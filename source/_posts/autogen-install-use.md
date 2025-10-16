---
title: autogen安装与使用
date: 2025-10-02 14:43:41
categories: AI
---
## 背景
之前搭建过crewAI，但是因为文档和自定义的地方比较少，所以尝试使用autogen
## 安装
### 创建虚拟环境
```bash
conda create -n autogen python=3.12
```
### pip安装
```bash
pip install -U "autogen-agentchat"
```
<!-- more -->
### 安装openai扩展
我使用deepseek，作为兼容openai api的服务商，需要在autogen中安装openai的插件
```bash
pip install "autogen-ext[openai]"
```
## 使用
### 使用env管理api_key
安装dotenv
```bash
pip install python-dotenv
```
### 配置日志
```python

import asyncio
import logging
import os
import sys
from autogen_core import EVENT_LOGGER_NAME
from autogen_ext.models.openai import OpenAIChatCompletionClient
from dotenv import load_dotenv
load_dotenv()
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(EVENT_LOGGER_NAME)
logger.setLevel(logging.INFO)
```
### 创建模型测试消息
```python

async def main(api_key: str) -> None:
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
    # 测试 DeepSeek 模型
    from autogen_core.models import (  # pylint: disable=import-outside-toplevel
        UserMessage,
    )
    result = await openai_model_client.create(
        [UserMessage(content="What is the capital of France?", source="user")]
    )
    logger.info(result)
    await openai_model_client.close()
```
### 完整示例
```python

# -*- coding: utf-8 -*-
"""
A autogen using DeepSeek model.
"""
import asyncio
import logging
import os
import sys
from autogen_core import EVENT_LOGGER_NAME
from autogen_ext.models.openai import OpenAIChatCompletionClient
from dotenv import load_dotenv
load_dotenv()
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(EVENT_LOGGER_NAME)
logger.setLevel(logging.INFO)
async def main(api_key: str) -> None:
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
    # 测试 DeepSeek 模型
    from autogen_core.models import (  # pylint: disable=import-outside-toplevel
        UserMessage,
    )
    result = await openai_model_client.create(
        [UserMessage(content="What is the capital of France?", source="user")]
    )
    logger.info(result)
    await openai_model_client.close()
if __name__ == "__main__":
    open_api_key = os.getenv("OPENAI_API_KEY")
    if not open_api_key:
        logger.error("OPENAI_API_KEY environment variable is not set.")
        sys.exit(1)
    asyncio.run(main(open_api_key))
```

