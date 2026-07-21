# My Agent Skills (`my-agent-skills`)

> A curated collection of modular, standardized Agent Skills and prompts designed for LLM-assisted software development (Cursor, Claude, Gemini, etc.).

这个仓库用于沉淀和管理个人在实际工程（如 Android NDK、C++ 跨平台开发、GIS/农业数据处理等）中积累的标准化 Agent Skill 模板。通过结构化的上下文与编码规范，帮助 AI 更加精准地生成符合工程标准的代码。

---

## 目录结构 (Repository Structure)

```text
my-agent-skills/
├── README.md                          # 仓库主页索引
├── .gitignore                         # Git 忽略文件配置
├── LICENSE                            # 开源许可证 (MIT)
└── cxx-ndk/                           # C++ 与 Android NDK 领域 Skills
    └── cpp-library-template/          # 跨平台 C++ 库与 JNI 桥接工程模板
        ├── SKILL.md                   # Skill 主定义文件
        └── references/
            └── coding-conventions.md  # C++ / JNI 详细编码与架构规范
