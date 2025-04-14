---
theme: seriph
background: https://cover.sli.dev
class: text-center
drawings:
  persist: false
transition: slide-left
mdc: true

---

# 探索 Langflow：从入门到构建 AI 知识库

Langflow 是一个开源的低代码 AI 开发平台，旨在简化多智能体系统和检索增强生成（RAG）应用的构建过程。本文将带你从 Langflow 的基本介绍开始，逐步深入到安装部署、简单运行，以及通过文档 RAG 和完整知识库的 Demo 展示其实战能力。无论你是 AI 开发新手还是资深从业者，Langflow 都能让你快速上手，释放创造力。

---

## Langflow 介绍

Langflow 是一个基于 Python 的开源框架，专为构建多智能体和 RAG 应用设计。它提供直观的拖放式界面，让开发者无需编写大量代码即可快速构建复杂的 AI 工作流。以下是 Langflow 的核心特点：

- **可视化开发**：通过拖放组件（如语言模型、提示模板、向量存储等）构建工作流，降低开发门槛。
- **模块化设计**：支持多种大语言模型（LLM）、向量数据库和工具，灵活适配不同场景。
- **RAG 友好**：内置支持检索增强生成，适合构建知识密集型应用。
- **开放生态**：与 LangChain、OpenAI、Astra DB 等主流框架无缝集成，并支持 API 部署。
- **开源免费**：完全开源，社区活跃，适合个人和企业使用。

Langflow 的目标是通过简化开发流程，让开发者专注于 AI 应用的创意和逻辑，而非底层代码实现。接下来，我们将从安装开始，逐步探索它的功能。

---

## 2. 安装与部署

Langflow 支持本地安装和云端部署，安装过程简单，支持多种环境。以下是本地安装和 Docker 部署的步骤。

### 2.1 本地安装

**环境要求**：

- Python 3.10 - 3.13
- pip 包管理器 或者 uv(https://github.com/astral-sh/uv)
- OpenAI（OpenAI Compatible） API 密钥（用于 Chat 和 Embedding ）

---

**安装步骤**：

- 安装
  1. 打开终端，创建虚拟环境（可选但推荐）：

     ```bash
     python -m venv langflow_env
     source langflow_env/bin/activate  # Windows: langflow_env\Scripts\activate
     ```

  2. 使用 pip 安装 Langflow：

    ```bash
    pip install langflow
    ```

    ```bash
    uv pip install langflow # 使用 uv 安装
    ```

- 启动 Langflow 服务：
  
    ```bash
    python -m langflow run
    ```

    ```bash
    uv run langflow run # 使用 uv 安装可以使用 uv 启动
    ```

    打开浏览器，访问 http://localhost:7860，即可进入 Langflow 的可视化界面。

---

### 2.2 Docker 部署

- 确保已安装 Docker 和 Docker Compose。
    创建 docker-compose.yml 文件 (参考 docker/dev.docker-compose.yml)：

    ```yaml
    version: '3'
    services:
    langflow:
        image: langflowai/langflow:latest
        ports:
        - "7860:7860"
        environment:
        - LANGFLOW_DATABASE_URL=postgresql://postgres:postgres@postgres:5432/langflow
        depends_on:
        - postgres
    postgres:
        image: postgres:latest
        environment:
        - POSTGRES_USER=postgres
        - POSTGRES_PASSWORD=postgres
        - POSTGRES_DB=langflow
    ```

- 运行 Docker Compose：

    ```bash
    docker-compose up
    ```

- 访问 http://localhost:7860，即可使用 Langflow。
- 提示：
    本地安装适合快速体验，容器化 部署更适合生产环境。
    确保网络畅通，首次运行可能需要下载依赖。

---

### 2.3 Desktop 部署(当前仅支持 MacOS)

---

### 简单运行：Hello World 示例

通过一个简单的聊天机器人示例，快速体验 Langflow 的可视化开发。

- 目标：构建一个基于 OpenAI 的基础聊天机器人。
- 步骤：
  - 打开 Langflow 界面，点击 New Flow > Blank Flow。
  - 在组件面板中：
    - 拖放 Chat Input 组件到画布，用于接收用户输入。
    - 拖放 Prompt 组件，设置提示模板，例如：
      - 你是一个友好的助手，请根据用户输入回答问题：{user_input}
    - 拖放 OpenAI 组件，配置 API 密钥（需提前获取 OpenAI API Key）。
  - 连接组件：
    - 将 Chat Input 的输出连接到 Prompt 的 user_input 输入。
    - 将 Prompt 的输出连接到 OpenAI 的输入。
    - 将 OpenAI 的输出连接到 Chat Output 组件。
    - 点击右上角 Run 按钮，进入 Playground 模式。
    - 在聊天窗口输入“Hello, Langflow!”，机器人将返回类似“Hi! I'm happy to chat with you about Langflow!”的回复。
- 效果：
  - 这个简单工作流展示了 Langflow 的核心能力：通过拖放和连接组件，快速实现一个功能完整的聊天机器人。无需编写一行代码！

---

### 文档 RAG Demo：让机器人“读懂”你的文档

RAG（检索增强生成）是 Langflow 的核心优势之一，它能让 AI 从外部文档中检索信息，生成更准确的回答。以下是一个基于文档的 RAG Demo。

- 目标：构建一个能回答 PDF 文档内容的聊天机器人。
- 准备：
  - 一份 PDF 文件（例如公司手册或技术文档）。
  - OpenAI API 密钥。
  - Astra DB 或其他向量数据库（本例使用 Astra DB）。
- 步骤：
  - 创建新流程：
    - 在 Langflow 中选择 New Flow > Blank Flow。
  - 添加组件：
    - File Loader：上传你的 PDF 文件。
    - Split Text：将文档拆分为小块，设置 chunk 大小为 1000 字符。
    - OpenAI  Embedding s：生成文本 Embedding 向量，需配置 OpenAI API 密钥。
    - Astra DB Vector Store：存储 Embedding 向量，需提供 Astra DB 的连接信息（可在 DataStax 注册免费账户）。
    - Vector Search：用于检索相关文档片段。
    - Prompt：设置提示模板，例如：

        ```txt
        根据以下上下文回答用户问题：
        上下文：{context}
        用户问题：{user_question}
        ```

    - OpenAI：生成最终回答。
    - Chat Input 和 Chat Output：用于用户交互。
  - 连接组件：
    - File Loader → Split Text → OpenAI  Embedding s → Astra DB Vector Store。
    - Chat Input → Vector Search（user_question 输入）。
    - Astra DB Vector Store → Vector Search（context 输入）。
    - Vector Search → Prompt（context 输入）。
    - Chat Input → Prompt（user_question 输入）。
    - Prompt → OpenAI → Chat Output。
  - 运行流程：
    - 点击 Run，上传 PDF 后，等待文档处理完成。
    - 在 Playground 中提问，例如“文档中提到公司的使命是什么？”，机器人将基于 PDF 内容回答。
    - 效果：
      - 通过这个 RAG 流程，机器人能够从 PDF 中提取关键信息并生成准确回答。例如，如果 PDF 是公司手册，机器人可以回答关于公司历史、产品或政策的问题。
    - 提示：
      - 确保向量数据库配置正确，Astra DB 提供了便捷的免费试用。
      - 调整 chunk 大小和 Embedding 模型可优化检索效果。

---

### RAG 简图

### 流程图1：离线将数据源转换为向量

```mermaid
graph LR
    A[数据源] --> B[数据预处理]
    B --> C[文本分块]
    C --> D[计算 Embedding ]
    D --> E[生成向量存储至向量数据库]
```

### 流程图2：用户提问处理流程

```mermaid
graph LR
    A[用户提问] --> B[提问 Embedding ]
    B --> C[向量检索]
    C --> D{相似性计算}
    D --> E[检索相关信息]
    E --> F[LLM处理]
    F --> G[返回响应]
```

---

### 构建完整知识库 Demo：企业级应用

在实际场景中，我们往往需要处理多个数据源，构建一个完整的知识库。以下是一个更复杂的 Demo，展示如何用 Langflow 构建企业级知识库。

- 目标：构建一个支持多文档（PDF、TXT、网页）的知识库，回答跨文档的复杂问题。
- 准备：
  - 多个数据源：PDF 文件（公司手册）、TXT 文件（FAQ）、网页（公司官网）。
  - OpenAI API 密钥和 Astra DB 账户。
  - 可选：Langfuse 用于观测和调试。
- 步骤：
  - 创建新流程：
    - 选择 New Flow > Blank Flow。
  - 添加数据加载组件：
    - File Loader：上传 PDF 和 TXT 文件。
    - Web Loader：输入公司官网 URL，抓取网页内容。
    - Split Text：将所有数据拆分为 chunk（chunk 大小 1000，overlap 200）。
    - OpenAI  Embedding s：生成 Embedding 向量。
    - Astra DB Vector Store：存储所有 Embedding 向量。
  - 构建检索与生成：
    - Chat Input：接收用户问题。
    - Vector Search：从 Astra DB 中检索相关 chunk，设置 top-k=5。
  - Prompt：设计复杂提示模板，例如：
      你是一个企业知识助手，根据以下上下文回答问题。如果问题涉及多个文档，请综合信息生成连贯回答：
      上下文：{context}
      用户问题：{user_question}
  - OpenAI：生成回答。
  - Chat Output：显示结果。
- 集成观测工具：
  - 添加 Langfuse 组件，配置环境变量（参考 Langfuse 文档）。
  - 连接 Langfuse 到 OpenAI 组件，记录每步推理过程。
  - 连接与运行：
    - File Loader 和 Web Loader → Split Text → OpenAI  Embedding s → Astra DB Vector Store。
    - Chat Input → Vector Search → Prompt（user_question）。
    - Astra DB Vector Store → Vector Search → Prompt（context）。
    - Prompt → OpenAI → Langfuse → Chat Output。
    - 点击 Run，上传数据并等待处理完成。
- 测试知识库：
    在 Playground 中提问，例如：
    “公司最近的产品更新是什么？”（来自网页）
    “员工福利政策有哪些？”（来自 PDF）
    “如何联系客服？”（来自 TXT）
    机器人将综合多源信息生成准确回答。
- 效果：
    知识库能够处理多种格式的数据，回答跨文档的复杂问题。例如，当问及“公司产品与福利的关系”时，机器人可以结合网页的产品介绍和 PDF 的福利政策，生成连贯的回答。
- 进阶优化：
  - 多智能体：引入 Langflow 的 Agent 组件，分配任务给不同智能体（如检索、总结、生成）。
  - 提示优化：调整提示模板，减少幻觉（hallucination）。
- 部署：将流程导出为 JSON 或 API，集成到企业应用中。

---

### 总结与展望

- 未来展望：
  - 生态扩展：Langflow 正在快速迭代，支持更多 LLM 和数据库。
  - 企业应用：通过 API 和 Kubernetes 部署，Langflow 可无缝嵌入企业流程。
  - 社区贡献：作为开源项目，欢迎开发者参与，共同完善工具。
- 建议：
  - 初学者可从简单流程开始，熟悉组件逻辑。
  - 进阶用户可尝试多智能体和复杂 RAG 优化。
  - 关注 Langflow GitHub（https://github.com/langflow-ai/langflow）获取最新更新。
  - 希望这篇文章能为你的 Langflow 之旅提供启发！快去动手实践，构建属于你的 AI 应用吧！
- 参考资料：
  - Langflow 官方文档：https://docs.langflow.org
  - Langflow GitHub：https://github.com/langflow-ai/langflow
  - DataStax Astra DB：https://www.datastax.com
  - Langfuse 集成指南：https://langfuse.com

---

## 附 pip-mirror

```conf
; pip.conf
[global]
timeout = 6000
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
extra-index-url = https://mirrors.aliyun.com/pypi/simple
trusted-host = pypi.tuna.tsinghua.edu.cn
[tool.uv]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

---

## pgvector

- pull image

```sh
docker pull pgvector/pgvector:pg17
```

- run container

```sh
docker run --name pgvector-db -e POSTGRES_PASSWORD=pgvector --shm-size=1g -p 5433:5432 -d pgvector
```

- 进入 container

```sh
docker exec -it pgvector-db psql -U postgres
```

- 启动 vector extension

```sql
CREATE EXTENSION vector;
```

- 测试

```sql
CREATE TABLE items (id serial PRIMARY KEY, embedding vector(3));

INSERT INTO items (embedding) VALUES ('[1, 1, 1]'), ('[2, 2, 2]');

SELECT * FROM items;
```
