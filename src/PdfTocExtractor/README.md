# PdfTocExtractor

![License](https://img.shields.io/badge/license-MIT-blue)
![.NET](https://img.shields.io/badge/.NET-8.0-purple)
![NuGet](https://img.shields.io/nuget/v/PdfTocExtractor)

PdfTocExtractor 是一个纯 C# 实现的轻量级PDF目录提取库，用于从PDF文件中提取目录（TOC）并导出为多种格式。支持Markdown、JSON、XML、纯文本等格式，完全摆脱命令行依赖，无需额外的PDF处理工具，适合在 .NET 项目中内嵌、分发或集成自动化流程中使用。

通过PdfTocExtractor，您可以轻松从PDF文档中提取书签和目录结构，生成清晰的导航文档，完美适用于文档处理、内容分析和自动化工作流。

🚀 跨平台、零依赖、极速提取，一切尽在 PdfTocExtractor！

## ✨ 功能特点

- 📖 从PDF文件提取书签/目录信息
- 📄 支持多种输出格式：Markdown、JSON、XML、纯文本
- 🎯 可配置的导出选项（层级深度、页码格式等）
- 🔧 可扩展的导出器架构，支持自定义格式
- ⚡ 异步操作支持，高性能处理
- 🌐 跨平台支持：Windows、Linux、macOS

## 📦 安装

### 通过 NuGet 安装

```bash
dotnet add package PdfTocExtractor
```

或在 Package Manager Console 中：

```powershell
Install-Package PdfTocExtractor
```

## 🚀 快速开始

### 基本用法

```csharp
using PdfTocExtractor;
using PdfTocExtractor.Exporters;

// 创建提取器实例
var extractor = new PdfTocExtractor();

// 提取目录
var tocItems = await extractor.ExtractTocAsync("document.pdf");

// 导出为Markdown
await extractor.ExportToFileAsync(tocItems, "output.md", "markdown");

// 导出为JSON
await extractor.ExportToFileAsync(tocItems, "output.json", "json");
```

### 高级用法

```csharp
// 使用自定义导出选项
var exportOptions = new ExportOptions
{
    MaxDepth = 3,
    IncludePageNumbers = true,
    CustomTitle = "文档目录",
    IndentString = "  ",
    PageNumberFormat = "第 {0} 页"
};

await extractor.ExportToFileAsync(tocItems, "output.md", "markdown", exportOptions);

// 直接从PDF提取并导出
await extractor.ExtractAndExportAsync("document.pdf", "output.xml");
```

## 📄 支持的输出格式

- **Markdown** (`md`, `markdown`) - 适合文档和网页显示，支持层级结构
- **JSON** (`json`) - 适合程序处理和API集成，包含完整元数据
- **XML** (`xml`) - 结构化数据交换，标准化格式
- **Text** (`txt`, `text`) - 纯文本格式，简洁易读

## ⚙️ 导出选项

- `MaxDepth` - 最大层级深度（0表示无限制）
- `IncludePageNumbers` - 是否包含页码信息
- `IncludeLinks` - 是否包含链接（如果格式支持）
- `CustomTitle` - 自定义文档标题
- `IndentString` - 缩进字符串
- `PageNumberFormat` - 页码格式化字符串
- `Encoding` - 文件编码格式

## 🔧 扩展性

您可以通过实现 `IExporter` 接口来创建自定义导出器：

```csharp
public class CustomExporter : IExporter
{
    public string FormatName => "Custom";
    public string FileExtension => "custom";

    public string Export(IEnumerable<TocItem> tocItems, ExportOptions? options = null)
    {
        // 实现自定义导出逻辑
        return "custom format content";
    }

    public async Task ExportToFileAsync(IEnumerable<TocItem> tocItems, string filePath, ExportOptions? options = null)
    {
        var content = Export(tocItems, options);
        await File.WriteAllTextAsync(filePath, content);
    }
}

// 注册自定义导出器
extractor.RegisterExporter("custom", new CustomExporter());
```

## 📝 示例输出

### Markdown 输出示例

```markdown
# 文档目录

- [第1章 概述](#第1章-概述) (第 1 页)
  - [1.1 背景](#11-背景) (第 2 页)
  - [1.2 目标](#12-目标) (第 3 页)
- [第2章 技术架构](#第2章-技术架构) (第 5 页)
  - [2.1 系统设计](#21-系统设计) (第 6 页)
    - [2.1.1 核心组件](#211-核心组件) (第 7 页)
    - [2.1.2 数据流](#212-数据流) (第 8 页)
```

### JSON 输出示例

```json
{
  "title": "文档目录",
  "generatedAt": "2024-01-15T10:30:00",
  "items": [
    {
      "title": "第1章 概述",
      "level": 1,
      "page": "第 1 页",
      "pageNumber": 1,
      "children": [
        {
          "title": "1.1 背景",
          "level": 2,
          "page": "第 2 页",
          "pageNumber": 2
        }
      ]
    }
  ]
}
```

## 🛠️ 技术实现

PdfTocExtractor 使用以下技术：

- **.NET 8.0** - 现代化的.NET平台
- **[iText 9.2.0](https://github.com/itext/itext7-dotnet)** - 强大的PDF处理库
- **[iText7.bouncy-castle-adapter 9.2.0](https://www.nuget.org/packages/itext7.bouncy-castle-adapter)** - 加密PDF支持（必需）
- **[Newtonsoft.Json 13.0.3](https://github.com/JamesNK/Newtonsoft.Json)** - JSON序列化

## 🔧 故障排除

### 常见问题

#### "PdfEncryption exception" 错误

如果遇到此错误，通常是因为PDF文件使用了加密或权限保护。请确保已安装 `itext7.bouncy-castle-adapter` 包：

```bash
dotnet add package itext7.bouncy-castle-adapter
```

#### "此PDF文件没有目录（书签）信息" 错误

这表示PDF文件确实没有嵌入的书签/目录信息。可以：
- 检查PDF是否在其他阅读器中显示目录面板
- 考虑使用其他工具为PDF添加书签

## 📄 许可证

MIT License

## 🤝 相关项目

- [PdfTocExtractor.Cli](https://www.nuget.org/packages/PdfTocExtractor.Cli) - 命令行工具版本
- [项目主页](https://github.com/iugamlabs/pdf-toc-extractor) - 完整的项目文档和示例

## 📞 支持

如果您遇到问题或有建议，请：

- 📋 [提交Issue](https://github.com/iugamlabs/pdf-toc-extractor/issues)
- 💬 [参与讨论](https://github.com/iugamlabs/pdf-toc-extractor/discussions)
- ⭐ 如果这个项目对您有帮助，请给个Star！
