# PentAGI —— 中文导读

<div align="center" style="font-size: 1.5em; margin: 20px 0;">
    <strong>P</strong>enetration testing <strong>A</strong>rtificial <strong>G</strong>eneral <strong>I</strong>ntelligence
</div>

> 📄 **本文档性质说明**
> 本文件是 [PentAGI](https://github.com/vxcontrol/pentagi) 的中文导读，**不是逐条全文翻译**。
> 原始 README 约 23.8 万字符、4100+ 行，主体是 10 余家 LLM 提供商的逐一配置样例与测试工具的长篇用法，逐条翻译既不现实也无必要。
> 本文档覆盖：项目定位、核心特性、能力边界、架构、快速开始、使用流程、开发者工具与合规提醒。
> 具体环境变量、配置文件样例请查阅[英文原版 README](README.md)。若两者有出入，以英文原版为准。

## 这是什么

PentAGI 是一个面向**自动化安全测试**的工具，借助前沿人工智能技术实现。它面向信息安全专业人士、研究人员和爱好者，为他们提供一个强大而灵活的渗透测试解决方案。

一句话概括：**把你对某个目标（需已获授权）的渗透测试意图用自然语言描述出来，PentAGI 会自动规划任务、调用专业工具执行、记录过程，并产出漏洞报告。**

> ⚠️ **合规与法律提醒（务必先读）**
> PentAGI 是攻击性安全工具。**只对你拥有、或已获得明确书面授权的系统进行测试。**
> 未经授权对他人系统实施扫描、探测、入侵，在中国及多数国家和地区均属违法行为，可能触犯《刑法》第 285/286 条等条款并承担刑事责任。
> 请在使用前阅读项目的 [EULA.md](EULA.md)（最终用户许可协议）中的可接受使用要求。

## 核心特性

- **安全隔离**：所有操作都在沙箱化的 Docker 环境中执行，完全隔离。
- **完全自主**：AI 驱动的 agent 会自动判定并执行渗透测试步骤；可选开启执行监控与智能任务规划以提升可靠性。
- **专业渗透工具**：内置 20+ 专业安全工具套件，包括 nmap、metasploit、sqlmap 等。
- **智能记忆系统**：长期存储研究结果与成功经验，供后续复用。
- **可选知识图谱**：基于 Graphiti + Neo4j 的知识图谱，用于语义关系追踪和高级上下文理解。
- **Web 情报**：通过 [scraper](https://hub.docker.com/r/vxcontrol/scraper) 内置浏览器，从网络来源收集最新信息。
- **外部搜索系统**：集成 [Tavily](https://tavily.com)、[Firecrawl](https://www.firecrawl.dev)、[Traversaal](https://traversaal.ai)、[Perplexity](https://www.perplexity.ai)、[DuckDuckGo](https://duckduckgo.com/)、[Google Custom Search](https://programmablesearchengine.google.com/)、[Sploitus](https://sploitus.com) 和 [Searxng](https://searxng.org)。
- **专家团队**：带分工的委派系统，为研究、开发、基础设施任务分配专门的 AI 子 agent；配合可选的执行监控与智能任务规划，让较小的模型也能有不错的表现。
- **全面监控**：详细日志记录，并集成 Grafana/Prometheus 做实时系统观测。
- **详细报告**：生成完整的漏洞报告，附带利用（exploitation）指南。
- **智能容器管理**：根据具体任务需求自动选择 Docker 镜像。
- **现代界面**：简洁直观的 Web UI，用于系统管理与监控。
- **完整 API**：功能完备的 REST 与 GraphQL API，支持 Bearer token 认证，便于自动化与集成。
- **持久化存储**：所有命令与输出都存入 PostgreSQL（带 [pgvector](https://hub.docker.com/r/vxcontrol/pgvector) 扩展）。
- **可扩展架构**：基于微服务的设计，支持水平扩展。
- **自托管方案**：对部署与数据拥有完全控制权。
- **灵活的 LLM 接入**：支持 10+ 家 LLM 提供商，含 [OpenAI](https://platform.openai.com/)、[Anthropic](https://www.anthropic.com/)、[Google AI/Gemini](https://ai.google.dev/)、[AWS Bedrock](https://aws.amazon.com/bedrock/)、[Ollama](https://ollama.com/)、[DeepSeek](https://www.deepseek.com/en/)、[GLM](https://z.ai/)、[Kimi](https://platform.moonshot.ai/)、[Qwen](https://www.alibabacloud.com/en/)、[MiniMax](https://www.minimax.io/) 及自定义接口，另支持聚合平台（[OpenRouter](https://openrouter.ai/)、[DeepInfra](https://deepinfra.com/)、[Atlas Cloud](https://www.atlascloud.ai/)、[OpenCode Go plan](https://opencode.ai/en/go)）。本地生产部署可参考其 [vLLM + Qwen3.5-27B-FP8 指南](examples/guides/vllm-qwen35-27b-fp8.md)。
- **快速部署**：通过 [Docker Compose](https://docs.docker.com/compose/) 配合完整的环境配置即可轻松搭建。

### 当前能力边界（官方明确说明）

- PentAGI 目前是**自主 + 助手引导的渗透测试平台**，不是 CALDERA 那类带预定义战役/攻击计划的 BAS（Breach and Attack Simulation，入侵与攻击模拟）或对手仿真产品。
- 类 BAS 的「由 agent 编写攻击脚本」应视作概念性/未来工作，**不是当前已实现的功能**。
- 当前的流程报告 UI 支持网页查看、复制到剪贴板、下载 Markdown、下载 PDF；**JSON 格式的流程报告导出目前不在文档承诺的输出格式之列**。
- 提供商灵活性通过内置提供商和自定义/OpenAI 兼容端点实现。

## 架构概览

PentAGI 采用微服务架构，主要组件：

| 层 | 组件 | 作用 |
|---|---|---|
| 界面 | Web UI | 流程管理、执行监控、报告查看 |
| 核心 | API | 对 UI 提供 HTTP/WebSocket 接口 |
| 核心 | Agent | 实际的 AI 执行体，负责决策与调用工具 |
| 核心 | PostgreSQL | 主数据库，存储流程、任务、动作、产物、记忆 |
| 核心 | Message Queue | 任务队列（RabbitMQ），解耦 API 与 Agent |
| 知识 | Graphiti + Neo4j | 可选知识图谱，做语义关系与上下文理解 |
| 工具 | Web Scraper | 隔离浏览器，负责网页抓取 |
| 工具 | Security Tools | 20+ 专业渗透工具，沙箱执行 |
| 监控 | OpenTelemetry + VictoriaMetrics / Jaeger / Loki + Grafana | 指标、链路追踪、日志 |
| 分析 | Langfuse + ClickHouse + Redis + MinIO | LLM 调用分析与缓存 |

数据模型是层层包含的结构，理解它有助于看懂界面上「流程」的进展：

```
Flow（流程：一次完整的测试任务）
 └── Task（任务：流程下的大阶段）
      └── SubTask（子任务：由特定类型 agent 执行，如 researcher/developer/executor）
           └── Action（动作：一条命令/搜索/分析，有成功失败状态）
                ├── Artifact（产物：文件/报告/日志）
                └── Memory（记忆：写入长期记忆系统的经验）
```

## 快速开始

分步图文教程见 [Installing and Configuring PentAGI](examples/guides/installation_configuration.md)，下文是各步骤的详细参考。

### 系统要求

- Docker 与 Docker Compose（或 Podman）
- 至少 2 个 vCPU
- 至少 4 GB 内存
- 20 GB 可用磁盘空间
- 能访问互联网以下载镜像与更新

### 使用安装器（推荐）

PentAGI 提供带终端 UI 的交互式安装器，引导你完成系统检查、LLM 提供商设置、搜索引擎配置与安全加固。

**支持平台：**
- **Linux**：amd64 [下载](https://pentagi.com/downloads/linux/amd64/installer-latest.zip) | arm64 [下载](https://pentagi.com/downloads/linux/arm64/installer-latest.zip)
- **Windows**：amd64 [下载](https://pentagi.com/downloads/windows/amd64/installer-latest.zip)
- **macOS**：amd64（Intel）[下载](https://pentagi.com/downloads/darwin/amd64/installer-latest.zip) | arm64（M 系列）[下载](https://pentagi.com/downloads/darwin/arm64/installer-latest.zip)

**快速安装（Linux amd64）：**

```bash
# 创建安装目录
mkdir -p pentagi && cd pentagi

# 下载安装器
wget -O installer.zip https://pentagi.com/downloads/linux/amd64/installer-latest.zip

# 解压
unzip installer.zip

# 运行交互式安装器
./installer
```

**权限说明：**

安装器需要相应权限来调用 Docker API：

- **方式一（生产环境推荐）**：以 root 运行 `sudo ./installer`
- **方式二（开发环境）**：把用户加入 `docker` 组
  ```bash
  sudo usermod -aG docker $USER
  newgrp docker
  docker ps    # 验证无需 sudo 即可访问
  ```

  ⚠️ **安全提示**：把用户加入 `docker` 组等同于授予 root 级别权限，请仅在可控环境、仅对受信任用户这样做。生产部署建议使用 rootless Docker 模式，或用 sudo 运行安装器。

**安装器会做这些事：**
1. **系统检查**：验证 Docker、网络连通性与系统要求
2. **环境准备**：创建并配置 `.env` 文件，填入优化后的默认值
3. **提供商配置**：设置 LLM 提供商（OpenAI、Anthropic、Gemini、Bedrock、Ollama、DeepSeek、GLM、Kimi、Qwen、MiniMax、自定义）
4. **搜索引擎**：配置 DuckDuckGo、Google、Tavily、Firecrawl、Traversaal、Perplexity、Sploitus、Searxng，以及可选的内部浏览器分析兜底引擎
5. **安全加固**：生成安全凭证并配置 SSL 证书
6. **部署**：用 docker-compose 启动 PentAGI

### Web 控制台能管什么 / 只能在服务端配什么

**服务启动后，Web 控制台已可管理（无需改配置文件）：**

- **Settings → Providers**：创建、编辑、删除、测试用户自定义的提供商配置，控制每个 agent 的模型选择、运行时参数、推理选项与价格元数据。
- **Settings → Prompts**：管理系统提示词、人类提示词与工具提示词模板。
- **Settings → PentAGI API**：创建与管理用于 REST 和 GraphQL 访问的 Bearer token。
- **其他界面偏好**：收藏的流程以用户偏好存储；主题在侧边栏/个人资料控件中切换，而非 Settings 页。

**仍只能通过服务端配置（环境变量 / compose 文件 / 挂载配置文件）：**

- **LLM 凭证与连接参数**：各家 API Key、端点、认证方式等；仅部分提供商支持配置文件路径，如 `OLLAMA_SERVER_CONFIG_PATH`、`LLM_SERVER_CONFIG_PATH`。
- **搜索提供商凭证与选项**：如 `DUCKDUCKGO_*`、`GOOGLE_*`、`TAVILY_API_KEY`、`FIRECRAWL_API_*`、`TRAVERSAAL_API_KEY`、`PERPLEXITY_*`、`SEARXNG_*`、`SPLOITUS_ENABLED`，以及可选的 `WEB_SEARCH_INTERNAL_*` 浏览器分析兜底设置。
- **第三方集成**：Langfuse、Graphiti 等外部服务仍是服务端配置。
- **MCP 服务器管理**：MCP 设置页目前还不作为实时 Web 控制台功能开放。

## 登录后如何使用

技术栈跑起来、能登录 Web UI 之后，最快的上手路径是 **Flows（流程）** 工作流。

### 1. 创建你的第一个流程

1. 在侧边栏打开 **Flows**。
2. 点击 **New Flow**。
3. 选择符合你目标的模式：
   - **Automation（自动化）**：让 PentAGI 端到端自主执行整个测试目标
   - **Assistant（助手）**：交互式往复协作，你一步步引导调查。此模式下还可开启 **Use Agents** 开关，让 PentAGI 把子任务委派给专门的子 agent，适合更复杂的调查
4. 选择本次流程要使用的 LLM 提供商。
5. 在消息框中用自然语言描述目标与目的。

一个好的首个提示词通常包含：

- 目标系统或 URL
- 你想要的评估类型
- 任何范围限制或交战规则（rules of engagement）
- 你期望的结果，例如漏洞报告或某个假设的验证

示例：

```text
评估 https://target.example 的常见 Web 应用漏洞。重点关注认证、文件处理和注入类问题。仅限所提供的目标范围，并用复现步骤总结已确认的发现。
```

**只测试你拥有或已获明确授权评估的系统。** 可接受使用要求见 [EULA.md](EULA.md)。

### 2. 用模板做可重复的工作流

新建流程表单带模板选择器，可用已保存的流程模板预填消息框。

- 如果 **Templates** 中已有保存的模板，直接使用
- 需要 Web 测试的实用基线时，可从 [`examples/prompts/base_web_pentest.md`](examples/prompts/base_web_pentest.md) 的示例提示词开始
- 启动流程前，先调整目标、范围与约束

模板只是起点。使用 PentAGI 不需要特殊语法：只要目标和目的清晰，平实的自然语言指令就足够好用。

### 3. 监控执行并查看输出

提交流程后，PentAGI 会自动打开流程页。

- 在主视图跟随消息、agent 活动与任务进度
- 检查工具活动和终端输出
- 查看生成的任务与子任务，理解 PentAGI 正在做什么

结果足够多之后，用流程页的 **Report** 菜单：

- 在网页视图中打开报告
- 复制报告到剪贴板
- 下载 Markdown 报告
- 下载 PDF 报告

### 4. 用 Assistant 视图引导进行中的流程

每个流程还有一个 **Assistant** 视图用于交互式引导。当自主运行遇到需要人工判断的情况时，它比直接重启更合适。

- 先打开该流程的 **Assistant** 视图查看当前状态，再做改动
- 可用助手查看流程状态、停止当前任务、提交后续指令，或在下一步执行前修正剩余计划中的子任务
- 请把它当作当前流程的显式控制通道，而非不可见的后台队列。若要改变方向，明确说出来，并让新指令仍处于当前交战范围内
- 最适合的场景：澄清范围、在中间发现后重排优先级，或在不丢失其余流程上下文的前提下回应自动化检查点

### 5. 管理流程内的文件

每个流程在流程页都有独立的 **Files** 标签页。文件以父流程为界：存放在宿主机的 `{dataDir}/flow-{id}-data/`，绝不会泄漏到其他流程。

该标签页展示三类文件来源：

- **Uploads（上传，`uploads/`）**：你从 Web UI 提供的文件。使用 **Upload files** 操作，或直接拖拽到 Files 标签页。agent 容器运行期间，上传的文件也会被推送到容器内的 `/work/uploads/`，使 agent 能用普通 shell 工具读取。
- **Resources（资源，`resources/`）**：通过 **Attach resources from library** 从你保存的资源库中附加的文件。附加后会被复制进流程，并推送到运行中的容器内 `/work/resources/`。
- **Container（容器快照，`container/`）**：通过 **Pull file or directory from container** 从运行中的 agent 容器拉取的快照。在流程侧为只读，且绝不会回传到容器。

Files 标签页的单个文件操作包括 **Download（下载）**、**Copy path（复制路径）**、**Save as resource（另存为资源，把流程文件提升到可复用资源库）** 与 **Delete（删除）**。容器未运行时 Pull 操作会被禁用，提示「Container is not running」。

上传的文件与附加的资源会通过 `{{.UserFiles}}` 模板变量自动列在 agent 的系统提示词中，渲染为一个紧凑的 `<task_files>` XML 块（内含 `<uploads>` 与 `<resources>` 两节），因此助手与自动化 agent 能直接按路径引用，无需你把内容粘贴进对话。容器快照仅在 UI 中可见，不会自动注入回提示词。

当前限制：

- 单文件上传上限 300 MB；单次上传请求最多 1000 个文件、总计 2 GB。文件名上限 255 字节（约 255 个 ASCII 字符；非 ASCII 名称每字符占多字节）。
- 上传与资源会镜像到运行中容器的固定路径 `/work/uploads/` 和 `/work/resources/`；写入其他容器路径的文件不会自动镜像回流程文件模型。容器快照可来自你拉取的任意容器路径（例如 `/etc/...`），缓存在流程侧的 `container/` 下，且不会推回容器。
- 容器快照是时间点拉取。在 UI 中编辑快照不会写回运行中的容器。
- 目前删除流程会删除流程记录及其长期记忆条目，但**尚不会**归档或删除磁盘上该流程的 `flow-{id}-data/` 目录。若想回收空间，仍需运维人员手动清理数据目录。

建议：早期测试时从一个很窄的目标和单一明确目的开始。这样输出更容易审查，也便于在跑更大规模评估前先打磨好提示词。

## API 访问

PentAGI 通过 REST 和 GraphQL 两套 API 提供完整的程序化访问能力，可把渗透测试工作流接入你的自动化流水线、CI/CD 流程和自定义应用。

- **REST API**：适合常规集成与脚本调用
- **GraphQL API**：适合需要灵活查询字段的集成
- 认证方式：Bearer token（在 `Settings → PentAGI API` 中创建）
- 可用的 API 探索与测试界面、客户端代码生成说明，见英文原版 README 的「API Access」章节

## 开发者工具

PentAGI 附带三个测试/调试工具，用于验证与优化 agent 行为：

| 工具 | 用途 |
|---|---|
| **ctester** | 测试 LLM agent——验证各 agent 类型在不同提供商配置下的表现，支持按 agent 类型、测试组筛选，可生成报告，并提供大量预置提供商配置（含 DeepSeek、GLM、Kimi、Qwen 等中国厂商） |
| **etester** | 测试嵌入（embedding）提供商与向量数据库连接、查看统计、重算嵌入、搜索文档；更换嵌入提供商后需用它重算 |
| **ftester** | 函数级测试——在 mock 模式或真实流程上下文中测试终端命令、搜索、向量库、AI agent 等功能，可交互式填充工具参数；也是调试「流程卡住不推进」的首选工具 |

## 构建

英文原版 README 的「Building」章节给出 Docker 镜像构建与多平台构建的完整命令；开发环境搭建、Postgres 资源限制查看等见「Development」章节。

## 合规提醒（再次强调）

- 仅测试你拥有或已获**明确书面授权**的系统。
- 遵守 [EULA.md](EULA.md) 中的可接受使用要求。
- 使用前请确认你所在司法辖区的法律法规。在中国，未经授权的渗透测试可能触犯《网络安全法》《刑法》相关条款。

## 社区与鸣谢

- Discord：[加入社区](https://discord.gg/2xrMh7qX6m)
- Telegram：[加入频道](https://t.me/+Ka9i6CNwe71hMWQy)
- 鸣谢与第三方依赖、VXControl 云服务信息见英文原版 README 的「Credits」章节。

## 许可证

见英文原版 README 的 License 章节及 [LICENSE](LICENSE) 文件。
