# My Agent Skills (`my-agent-skills`)

> 🚀 个人精选的 Agent Skills / Prompts 集合，专为 Cursor、Claude、Gemini、Copilot 等 AI 辅助开发工具打造。

这个仓库用于沉淀和管理在日常开发（如 **C++ 跨平台开发**、**Android NDK**、**Git 工作流**、**GIS / 农业数据处理** 等）中总结的标准化 AI 交互模板。通过提供清晰的工程规范与架构约定，帮助 AI 更加准确地生成符合生产标准的代码。

---

## 📦 Skills 列表

| 领域 / 分类 | Skill 名称 | 描述 | 快速链接 |
| :--- | :--- | :--- | :--- |
| **C++ / NDK** | `cpp-library-template` | 标准化 C++ 跨平台库与 JNI 桥接工程模板。包含 CMake 层次化构建、符号导出、`nativeHandle` 内存生命周期管理及编码规范。 | [查看 Skill](./cxx-ndk/cpp-library-template/) |
| **Git 工作流** | `git-commit-message` | 自动分析 Git 变更（Staged/Unstaged）并生成符合团队定制规范（`AAAAA：【BBBB】CCCCCC`）的 Commit Message。 | [查看 Skill](./git/git-commit-message/) |

---

## 🛠️ 如何使用 (How to Use)

你可以根据你使用的 AI 工具，选择以下任意一种方式引入此仓库中的 Skill：

### 方式 1：在 Cursor / Claude Projects 中直接引入
1. 打开对应的 Skill 目录（例如 `cxx-ndk/cpp-library-template`）。
2. 将 `SKILL.md` 以及 `references/coding-conventions.md` 的内容复制到你的：
   * **Cursor Rules** (`.cursorrules` 或 System Prompt)
   * **Claude Project Knowledge** (项目知识库)
   * **Custom GPTs / Gemini System Instructions**

### 方式 2：在对话中直接唤醒
在与 AI 对话时，只需复制 `SKILL.md` 内容或在 Prompt 中使用触发词：
> *"请按照 `cpp-library-template` 规范，帮我创建一个全新的 C++ 算法库/JNI 桥接模块。"*  
> *"请帮我生成当前改动的 Git commit message。"*

---

## 📂 仓库结构

```text
my-agent-skills/
├── README.md
├── cxx-ndk/                             # C++ 与 NDK 领域
│   └── cpp-library-template/            # C++ 库与 JNI 桥接模板
│       ├── SKILL.md                     # Skill 主定义文件
│       └── references/
│           └── coding-conventions.md    # 详细的 C++ & JNI 编码规范
└── git/                                 # Git 工作流领域
    └── git-commit-message/              # Commit Message 自动生成规范
        └── SKILL.md                     # Skill 主定义文件
```

---

## 🗺️ 未来规划 (Roadmap)

随着日常工程实践的沉淀，本仓库将陆续补充以下领域的 Skill：

- [x] **C++ & Android JNI 跨平台库骨架** (`cpp-library-template`)
- [x] **Git Commit Message 规范化自动生成** (`git-commit-message`)
- [ ] **3D 地图 / 渲染引擎 (OSG / OpenGL ES) 交互与 Surface 绑定**
- [ ] **农业 GIS 数据格式处理 (ISOXML / Shapefile / FODM)**

---

## 📄 License

[MIT License](LICENSE)
