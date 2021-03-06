---
title: "相关文档"
description: "链接到面向 macOS 开发人员的其他文档。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0a282c58-1c37-4f73-8440-85de2daf454a
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/02/2016
ms.openlocfilehash: 000061d8ed884f1eb248517c6131ec49134cb8a1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="related-documentation"></a>相关文档

除了 [developer.xamarin.com](~/mac/get-started/index.md) 的 Mac 部分，还有三个很好的文档来源，也可对 Xamarin.Mac 相关问题有所帮助：

- [**Xamarin.iOS 文档**](~/ios/get-started/index.md) -对于许多 API（主要来自 AppKit/UIKit 外）而言，iOS 和 macOS 版本之间只存在微小的差异。 在某些情况下，如果给定的 iOS API 的名称为 `UIFoo`，则可在 macOS 中找到名为 `NSFoo` 的类似 API。 这些示例通常出现在 C# 中。

- **Apple 的 [Mac 开发人员中心](https://developer.apple.com/devcenter/mac/)** - 其中一个示例：在 Objective-C 中调用的 API 可直接转换成 C#。 请参阅[了解 Mac API](~/mac/app-fundamentals/mac-apis.md)了解有关如何实现此操作的详细信息。

- [**Stack Overflow**](http://stackoverflow.com/)（堆栈溢出） - 一个很有用的资源源，用于解答简单的一次性问题，例如[“How do I auto expand all nodes in an NSOutlineView”](http://stackoverflow.com/questions/519751/nsoutlineview-auto-expand-all-nodes)（如何在 NSOutlineView 中自动展开所有节点）。 这些示例通常是 Objective-C 的，并且需要转换成 C#，但 C# 中存在答案的子集。

## <a name="user-interface"></a>用户界面

在 Xamarin.Mac 应用程序中使用 C# 和 .NET 时，开发人员可以访问的用户界面控件与使用 Objective-C 和 Xcode 的开发人员相同。 由于 Xamarin.Mac 与 Xcode 直接集成，开发人员可以使用 Xcode 的 Interface Builder 来创建和维护应用的用户界面（或选择直接使用 C# 代码创建）。

下面列出的指南详细介绍了如何在 Xamarin.Mac 应用程序中使用 macOS 元素：

- [Windows](~/mac/user-interface/window.md)
- [对话框](~/mac/user-interface/dialog.md)
- [警报](~/mac/user-interface/alert.md)
- [菜单](~/mac/user-interface/menu.md)
- [工具栏](~/mac/user-interface/toolbar.md)
- [表视图](~/mac/user-interface/table-view.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [源列表](~/mac/user-interface/source-list.md)
