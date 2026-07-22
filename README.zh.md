# Karpathy 启发的 Claude Code 指南

> 查看我的新项目 [Multica](https://github.com/multica-ai/multica) —— 一个用于运行和管理编码智能体的开源平台，支持可复用的技能。
>
> 在 X 上关注我： [https://x.com/jiayuan_jy](https://x.com/jiayuan_jy)

这是一个单一的 `CLAUDE.md` 文件，用于改善 Claude Code 的行为，源自 [Andrej Karpathy 的观察](https://x.com/karpathy/status/2015883857489522876) 关于 LLM 编码陷阱的总结。

[English](./README.md) | 简体中文

## 问题所在

来自 Andrej 的推文：

> "模型会替你做错误假设，然后不假思索地执行。它们不管理自己的困惑，不寻求澄清，不呈现权衡，在应该提出异议时也不会反驳。"

> "它们真的很喜欢把代码和 API 搞复杂，堆砌抽象概念，不清理死代码……明明 100 行能搞定的事情，非要实现成 1000 行的臃肿架构。"

> "它们有时仍会修改或删除自己理解不足的代码和注释，即使这些内容与任务本身无关。"

## 解决方案

四个原则，集中在一个文件中，直接解决这些问题：

| 原则 | 解决什么问题 |
|-----------|-----------|
| **编码前思考** | 错误假设、隐藏困惑、缺少权衡 |
| **简洁优先** | 过度复杂、臃肿抽象 |
| **精准修改** | 无关编辑、触碰不该改的代码 |
| **目标驱动执行** | 通过测试优先、可验证的成功标准来推进 |

## 四个原则详解

### 1. 编码前思考

**不要假设。不要隐藏困惑。呈现权衡。**

LLM 经常默认选择一种解释，然后直接执行。这个原则要求明确推理：

- **明确说明假设** —— 如果不确定，先问，不要猜
- **给出多个解释** —— 存在歧义时，不要悄悄选一个
- **必要时提出反对意见** —— 如果有更简单的方案，就说明出来
- **困惑时先停下** —— 说清楚哪里不明确，再提问

### 2. 简洁优先

**只写解决问题所需的最少代码。不要预设未来需求。**

避免过度设计：

- 不添加超出需求的功能
- 不为单次使用的代码抽象出层次
- 不做没有要求的“灵活性”或“可配置性”
- 不为不可能发生的场景写复杂的错误处理
- 如果 200 行就能解决，别写成 1000 行

**检验标准：** 一个资深工程师会不会觉得这太复杂？如果会，就简化。

### 3. 精准修改

**只改必须改的部分。只清理自己制造的垃圾。**

修改已有代码时：

- 不要“顺手优化”相邻代码、注释或格式
- 不要重构没有问题的东西
- 保持现有风格，即使你会用别的写法
- 如果发现无关的死代码，提出来，不要擅自删除

当你的改动产生孤儿时：

- 删除因你自己的改动而变得未使用的导入、变量、函数
- 不要删除原本就存在的死代码，除非被要求

**检验标准：** 每一处修改都应该能直接对应到用户请求。

### 4. 目标驱动执行

**先定义成功标准，再循环验证。**

把命令式任务改写成可验证的目标：

| 不是... | 而是... |
|--------------|-----------------|
| "加验证" | "先为非法输入写测试，再让它们通过" |
| "修 bug" | "先写一个能复现 bug 的测试，再让它通过" |
| "重构 X" | "确保重构前后测试都通过" |

对于多步骤任务，先写一个简短计划：

```
1. [步骤] → 验证：[检查项]
2. [步骤] → 验证：[检查项]
3. [步骤] → 验证：[检查项]
```

清晰的成功标准能让 LLM 独立循环。含糊的目标（“先让它能用”）通常意味着反复返工。

## 安装

**选项 A：Claude Code Plugin（推荐）**

在 Claude Code 中，先添加 marketplace：
```
/plugin marketplace add forrestchang/andrej-karpathy-skills
```

然后安装插件：
```
/plugin install andrej-karpathy-skills@karpathy-skills
```

这会把指南安装为 Claude Code 插件，使 skill 可以在所有项目中使用。

**选项 B：CLAUDE.md（按项目）**

新项目：
```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

已有项目（追加）：
```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

## 在 Cursor 中使用

这个仓库包含一个已提交的 Cursor 项目规则（[`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc)），因此在 Cursor 中打开这个项目时会自动应用同样的指南。更多设置、在其他项目中使用该规则，以及它和 Claude Code 的关系，请看 **[CURSOR.md](CURSOR.md)**。

## 给贡献者

如果你修改了四个原则，请保持 **[`CLAUDE.md`](CLAUDE.md)** 和 **[`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc)** 同步。如果发布的 skill/plugin 文案也需要一致，请同时更新 **[`skills/karpathy-guidelines/SKILL.md`](skills/karpathy-guidelines/SKILL.md)**。
