# Candle 汉化步骤总结

本文档概述了将 Candle 桌面应用程序界面翻译为简体中文的推荐流程，涵盖从环境准备到运行效果验证的全部步骤。

## 步骤 1：编译未汉化版本并验证
1. 克隆官方仓库：
   ```bash
   git clone https://github.com/Denvi/Candle.git
   ```
2. 参考 `readme.md` 中的说明搭建构建环境：
   - Windows 使用 vcpkg 安装 Qt 依赖并在 CMake 中启用。
   - Linux 使用发行版的软件包管理器安装 Qt5（如 `qtbase5-dev`）。
3. 运行常规构建：
   ```bash
   cmake -S . -B build
   cmake --build build
   ```
4. 启动生成的可执行程序，确认英文界面工作正常。

## 步骤 2：提取可翻译文本
Candle 是 Qt5 应用，所有可翻译字符串均包裹在 `tr("...")` 调用内。使用 Qt 提供的 `lupdate` 工具生成翻译模板：

```bash
lupdate ./src -ts translations/candle_zh_CN.ts
```

生成的 `translations/candle_zh_CN.ts` 列出了 UI 中的全部英文字符串，将作为翻译源文件。

## 步骤 3：翻译英文字符串
使用 Qt Linguist 打开 `translations/candle_zh_CN.ts`。将每条英文字符串翻译成简体中文，注意保持 CNC 术语的统一：

- Jog → 点动
- Feed rate → 进给速度
- Work coordinates → 工件坐标
- Probe / heightmap → 探高 / 高度图

Qt Linguist 会跟踪翻译完成度，确保所有条目都已确认。

## 步骤 4：生成运行时翻译文件
翻译完成后使用 `lrelease` 将 `.ts` 文件编译成运行时使用的 `.qm` 文件：

```bash
lrelease translations/candle_zh_CN.ts -qm translations/candle_zh_CN.qm
```

生成的 `translations/candle_zh_CN.qm` 即可被 Candle 在启动时加载。

## 步骤 5：在程序中安装中文翻译
在 `src/candle/main.cpp` 中的启动逻辑中加载 `.qm` 文件（项目已包含通用的 `loadTranslationsForLocale` 帮助函数）：

```cpp
QTranslator translator;
if (translator.load("candle_zh_CN.qm", "translations")) {
    app.installTranslator(&translator);
}
```

将 `.qm` 文件放在可执行文件同级目录下的 `translations/` 子目录中即可。

## 步骤 6：翻译内置帮助文档
仓库的 `help` 目录包含多语言帮助文档（如 `help/en`、`help/ru`）。完成界面翻译后：

1. 复制 `help/en` 目录为 `help/zh_CN`。
2. 将其中的 `.md` 或 `.html` 文件翻译成中文。
3. 如果需要，根据语言设置调整帮助加载逻辑，使中文环境优先读取 `help/zh_CN`。

完成以上步骤后，Candle 的界面和帮助文档即可提供完整的简体中文体验。
