# progressive-teaching

一个用于Codex的技术讲解技能：从已经理解的例子出发，让当前问题引出新概念，通过小幅改动、执行过程和可观察结果逐步理解。

它适合解释代码、配置、系统机制和技术资料。重点是控制每一层引入的信息，追踪真实的数据流与调用关系。简单问题保持简短，完整工作流按用户要求一次讲清。

## 使用

安装后可以直接提出问题：

```text
用 $progressive-teaching 讲一下这段代码。我已经理解parametrize，想知道YAML的数据怎么传进测试函数。
```

```text
用 $progressive-teaching 解释Redis的写事件。从缓冲区和一次write调用开始讲。
```

技能也允许在适合的技术讲解请求中自动调用。具体调用与发现行为由宿主支持决定；用户和项目指令优先。

## 安装到Codex

将`skills/progressive-teaching`整个目录放入Codex的skills目录。默认目录是`~/.codex/skills`；配置了`CODEX_HOME`时使用其`skills`子目录。

```bash
git clone https://github.com/Yukinoshita03/progressive-teaching-skill.git
mkdir -p ~/.codex/skills
cp -R progressive-teaching-skill/skills/progressive-teaching ~/.codex/skills/
```

以上命令适用于尚未安装同名技能的默认配置。已有同名目录时，先检查差异并保留自己的改动。安装后在新的Codex会话中确认能发现`progressive-teaching`。

## 文件

- [SKILL.md](skills/progressive-teaching/SKILL.md)：触发范围、递进讲解方式、节奏调整和证据要求。
- [examples.md](skills/progressive-teaching/references/examples.md)：fixture、YAML、切片及事件机制的讲解示例。
- [openai.yaml](skills/progressive-teaching/agents/openai.yaml)：Codex技能列表中的名称、说明和示例提示词。

## 方法来源

受“隔壁的程序员老王”的视频[如何使用pytest进行单元测试](https://www.bilibili.com/video/BV1KZWKz3Ek5/)启发，结合学习者反馈提炼而成。对视频的观察是：沿用同一个计算器例子，用重复准备、资源清理、新增输入等需求逐步引出fixture、yield、scope和参数化。

本仓库提供的是独立编写的技能说明与补充示例，不是作者发布的官方方法或视频逐字稿。采用来源的教学方式，同时独立核对技术内容和勘误；不固定复用视频章节顺序。

技能不会强制模式选择、测验、掌握度跟踪或复习计划，也不会默认创建学习档案。文件操作与外部服务调用遵循用户授权及宿主规则。

## 许可

本仓库原创内容采用[MIT许可证](LICENSE)。参考视频及其内容的权利属于原作者，不纳入本许可。
