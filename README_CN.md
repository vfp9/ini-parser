# INI File Parser

[English](README.md) | [简体中文](README_CN.md)

这是一个与 .NET、Mono 和 Unity3d 兼容(*)的库，用于从 IO 流、文件流以及用 C# 编写的字符串中读取/写入 INI 数据。

该库还实现了合并操作，既可合并完整的 INI 文件，也可合并部分文件，甚至只合并文件中包含的键的子集。


(*) 该库是 100% .NET 代码，不依赖于 Windows API 调用，因此具有可移植性。

[![Build Status](https://travis-ci.org/rickyah/ini-parser.png?branch=master)](https://travis-ci.org/rickyah/ini-parser)


获取最新版本: https://github.com/rickyah/ini-parser/releases/latest
使用 NuGet 安装: https://www.nuget.org/packages/ini-parser/

## Version 2.0
因为 INI 格式并非真正的 “标准”，第 2 版引入了一种更简单的方法来定制 INI 文件的解析：

 * 向 `IniParser` 传递配置对象，指定解析器的行为。如果没有提供配置对象，则使用默认实现。
 
 * 从 `IniDataParser` 派生并覆盖细粒度解析方法。


## 安装

该库已发布到 [NuGet](https://www.nuget.org/packages/ini-parser/)，可通过命令行从包含解决方案的目录中安装。

```bat
> nuget install ini-parser
```

或从 Visual Studio 中的[包管理器控制台](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)

```powershell
PM> Install-Package ini-parser
```

如果您使用的是 Visual Studio，可以下载 [NuGet 包管理器](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 扩展，以便为您的项目添加 NuGet 依赖关系。

如果您使用的是 MonoDevelop / Xamarin Studio，则可以安装 [MonoDevelop NuGet AddIn](https://github.com/mrward/monodevelop-nuget-addin) 以在集成开发环境中将此库添加为依赖库。

## 入门

所有代码示例都需要使用以下 using 语句：

```csharp
using IniParser;
using IniParser.Model;
```

INI 数据存储在嵌套字典中，因此访问与部分键相关的值非常简单。使用提供的方法之一加载数据。

```csharp
var parser = new FileIniDataParser();
IniData data = parser.ReadFile("Configuration.ini");
```

读取指定部分中某个键的值。值总是以字符串形式获取。

```csharp
string useFullScreenStr = data["UI"]["fullscreen"];
// useFullScreenStr 包含 “true”
bool useFullScreen = bool.Parse(useFullScreenStr);
```

修改字典中的值，而不是检索到的值，并保存或覆盖文件。

```csharp
data["UI"]["fullscreen"] = "true";
parser.WriteFile("Configuration.ini", data);
```

请访问 [维基](https://github.com/rickyah/ini-parser/wiki) 了解更多使用示例，或 [查看示例项目的代码](https://github.com/rickyah/ini-parser/blob/development/src/IniFileParser.Example/Program.cs)


## 合并 ini 文件
合并 ini 文件仅需执行以下代码：

```csharp

   var parser = new IniParser.Parser.IniDataParser();

   IniData config = parser.Parse(File.ReadAllText("global_config.ini"));
   IniData user_config = parser.Parse(File.ReadAllText("user_config.ini"));
   config.Merge(user_config);

   // 现在，config 包含了两个 ini 文件中的数据，
   // 键值和部分的值将被用户配置文件中的键值和部分的值覆盖
```

谨记，如果您愿意，可以合并各个部分：

```csharp
config["user_settings"].Merge(user_config["user_settings"]);
```

## 注释

该库允许修改 ini 文件中的注释。
不过，与将文件写回磁盘相比，注释将被重新排列，因此注释被写入其所引用的元素之前。

要查询、添加或删除注释，可访问 `SectionData` 和 `KeyData` 模型中的属性 `Comments` 。

```csharp
var listOfCommentsForSection = config.["user_settings"].Comments;
var listOfCommentsForKey = config["user_settings"].GetKeyData("resolution").Comments;
```

## Unity3D
您可以在 Unity3D 项目中轻松使用该库。只需将代码或 DLL 放到项目的 Assets 文件夹中，就可以使用了！

ini-parser 实际上在 [ProjectPrefs](http://u3d.as/content/garrafote/project-prefs/5so) 中使用，这是 Unity Assets Store 中的一个免费插件，允许您为项目设置自定义首选项。我与该项目没有任何关系： 感谢 Garrafote 制作此附加组件。

## 贡献
您是否有改进本库的想法，或者碰巧遇到了一个错误？请在 [issues page](https://github.com/rickyah/ini-parser/issues) 中分享您的想法或您发现的错误，或者更好的方法是：随意 fork 并 [贡献](https://github.com/rickyah/ini-parser/wiki/Contributing) 到此项目中，并提交一个拉取请求。