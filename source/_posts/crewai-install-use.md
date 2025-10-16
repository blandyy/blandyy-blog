---
title: crewAI安装与使用
date: 2025-10-01 23:57:17
categories: AI
---
## 背景

crewAI通过配置角色让智能体来共同协作，这次使用crewAI来创建一个产品经理+软件架构师+PHP全栈开发工程师的团队

## 安装

### uv安装

CrewAI 使用 UV 进行依赖管理和包处理，所以在使用crewAI之前先安装uv

<!-- more -->

首先我们创建一个专门用于crewAI的虚拟环境

```bash
conda create -n blog_by_crewAI python=3.11
```

在这个虚拟环境下安装uv

```bash
pip install uv
```

安装完查看版本输出：

```bash
(blog_by_crewAI) C:\Users\穆琪>uv -V
uv 0.8.15 (8473ecba1 2025-09-03)
```

### 安装crewAI

```bash
pip install crewai
```

查看版本输出：

```bash
(blog_by_crewAI) C:\Users\穆琪>crewai version
crewai version: 0.177.0
```

至此crewAI已经安装完成

## 使用

### 创建项目

首先我们创建一个项目，模型选deepseek

```bash
crewai create crew crewai-project
```

apikey等我们后面要使用的时候再进行配置

### 配置文件

#### agents

在 CrewAI 中，agents.yaml 是用于配置智能代理（Agent）核心属性的文件，这些配置决定了每个 Agent 的角色定位、任务目标和行为风格。

位置在`src/my_project/config/agents.yaml`

下面是一个产品经理的例子：

```yaml
# src/my_project/config/agents.yaml
agent的名称:  # 代表一个独立的 Agent 的名称（标识符），用于在团队（Crew）中区分不同的 Agent
  role: >
    {topic} 领域内的xxx  # 定义 Agent 的角色身份，相当于它在团队中的 “职业头衔”，决定了它的专业领域和行为基调，角色会影响 Agent 的思考方式和输出风格。比如 软件开发领域内的资深产品经理
  goal: >  # 明确 Agent 的核心任务目标，即它在团队中需要完成的具体使命
    在 {topic} 领域内做出美观好用的产品
  backstory: >  # 为 Agent 添加背景故事，描述它的经验、特长、性格等，用于塑造更贴合角色的 “行为个性”。
    你是一个资深的产品经理
```

这是我设计的一个产品经理+架构师+PHP开发工程师的配置：

```yaml

product_manager:
  role: >
    软件开发领域资深产品经理
  goal: >
    负责产品全生命周期管理，从需求挖掘到产品落地，确保产品满足用户需求并实现商业价值
  backstory: >
    你拥有10年以上软件开发领域产品经理经验，擅长从用户痛点出发挖掘产品需求，
    精通敏捷开发流程，能有效协调设计、开发和测试团队。曾主导多个核心产品从0到1的
    成功上线，善于在商业目标和技术可行性之间找到平衡，以数据驱动产品决策。
software_architect:
  role: >
    软件开发领域资深软件架构师
  goal: >
    设计健壮、可扩展且符合业务需求的系统架构，解决复杂技术挑战并确保技术方案的可行性
  backstory: >
    你拥有10年以上软件开发与架构设计经验，精通微服务、分布式系统等主流架构模式，
    熟悉多种技术栈与云原生解决方案。曾主导多个大型系统从0到1的架构设计与演进，
    擅长在业务目标与技术实现间建立桥梁，能精准识别技术风险并提出优化方案，
    推动跨团队技术标准统一与最佳实践落地。
php_fullstack_engineer:
  role: >
    软件开发领域资深PHP全栈开发工程师
  goal: >
    负责从前端到后端的全流程开发，实现高性能、可维护的PHP应用，解决跨领域技术难题
  backstory: >
    你拥有10年以上PHP开发经验，精通PHP全栈开发。
    擅长数据库优化、API设计与微服务集成，曾主导多个大型电商与企业级应用的全栈开发。
    能独立完成从需求分析到部署上线的全流程工作，注重代码质量与系统性能，善于解决复杂业务场景下的技术挑战，
    并乐于分享经验指导团队成长。
```

#### tasks


在 CrewAI 中，tasks.yaml 是用于定义任务（Task）的配置文件，每个任务配置会明确任务的目标、预期输出、执行主体（代理）等信息，让代理（Agent）清楚知道需要完成什么工作。

```yaml

任务名称:  # 作为任务的唯一标识符，用于在 Crew（团队）中定义任务的执行顺序或依赖关系
  description: >  # 描述任务的具体内容、目标和要求，告诉代理 “需要做什么”
    任务内容
  expected_output: >  # 明确任务的预期输出格式和内容，告诉代理 “需要产出什么样的结果”
    输出结果
  agent: xxx  # 指定负责执行该任务的代理名称（需与 agents.yaml 中定义的代理名称对应），即 “谁来执行这个任务”
  output_file: xxx.md  # 指定任务输出结果的保存路径，让代理将生成的内容直接写入文件
```

```yaml

requirement_analysis_task:
  description: >
    分析用户提出的功能需求，梳理核心诉求与场景。需评估需求的必要性、可行性与优先级，
    识别潜在的用户痛点与商业价值，同时考虑技术实现难度与资源投入。
    需结合市场同类产品情况，提出差异化建议，并形成结构化的需求分析报告。
  expected_output: >
    一份详细的需求分析报告，包含需求概述、用户场景分析、功能拆解、优先级评估、
    技术可行性分析、商业价值评估及建议方案。所有内容需用中文撰写，
    格式为markdown且不包含'```'标记。
  agent: product_manager
  output_file: requirement_analysis.md
architecture_design_task:
  description: >
    根据需求文档进行系统架构设计，确定技术栈选型、架构模式与整体框架。
    需进行组件划分、模块接口设计、数据流转规划，同时考虑系统可扩展性、
    安全性、性能与可维护性。需评估技术方案的可行性，提供架构图与关键技术说明。
  expected_output: >
    一份完整的架构设计报告，包含架构概述、技术栈选型说明、系统组件结构图、
    模块接口定义、数据流程图、非功能需求实现方案及技术风险评估。
    所有内容需用中文撰写，格式为markdown且不包含'```'标记。
  agent: software_architect
  output_file: architecture_design.md
php_fullstack_development_task:
  description: >
    根据架构设计文档与需求规格，完成PHP全栈开发工作。需实现前端页面与交互逻辑、
    后端API接口开发、数据库表结构与查询优化，
    确保前后端数据交互顺畅。需遵循代码规范进行单元测试与集成测试，修复开发中发现的问题，
    并编写开发说明文档。
  expected_output: >
    一份包含完整开发成果的交付报告，内容包括：功能实现清单、核心代码说明（关键模块逻辑）、
    数据库脚本、测试用例与结果、部署步骤。所有内容需用中文撰写，
    格式为markdown且不包含'```'标记。
  agent: php_fullstack_developer
  output_file: php_development_deliverable.md
```

剩下的根据官方的github配置实例化对应的agent就可以了。

[crewai官方仓库](href="https://github.com/crewAIInc/crewAI")

### crew.py

```python

from crewai import Agent, Crew, Process, Task
from crewai.project import CrewBase, agent, crew, task
from crewai.agents.agent_builder.base_agent import BaseAgent
from typing import List
@CrewBase
class Crewai():
    """Crewai crew"""
    agents: List[BaseAgent]
    tasks: List[Task]
    @agent
    def product_manager(self) -> Agent:
        return Agent(
            config=self.agents_config['product_manager'], # type: ignore[index]
            verbose=True
        )
    @agent
    def software_architect(self) -> Agent:
        return Agent(
            config=self.agents_config['software_architect'], # type: ignore[index]
            verbose=True
        )
    @task
    def requirement_analysis_task(self) -> Task:
        return Task(
            config=self.tasks_config['requirement_analysis_task'], # type: ignore[index]
            output_file='requirement_analysis.md'
        )
    @task
    def architecture_design_task(self) -> Task:
        return Task(
            config=self.tasks_config['architecture_design_task'], # type: ignore[index]
            output_file='architecture_design.md'
        )
    @crew
    def crew(self) -> Crew:
        """Creates the Crewai crew"""
        return Crew(
            agents=self.agents, # Automatically created by the @agent decorator
            tasks=self.tasks, # Automatically created by the @task decorator
            process=Process.sequential,
            verbose=True,
            # process=Process.hierarchical, # In case you wanna use that instead https://docs.crewai.com/how-to/Hierarchical/
        )
```

在这里，我只创建了产品经理和架构师。
