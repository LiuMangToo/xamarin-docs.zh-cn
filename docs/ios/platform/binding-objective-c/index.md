---
title: "绑定 iOS 库"
description: "如何使 iOS 本机库 （和 CocoaPods） 中的 Xamarin 应用可访问。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 3afe1a03299e600502d49b1db039af4c6642e131
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="binding-ios-libraries"></a>绑定 iOS 库

_如何使 iOS 本机库 （和 CocoaPods） 中的 Xamarin 应用可访问。_

请按照以下链接以了解有关 Xamarin.iOS 和 Xamarin.Mac 绑定 Objective C 库和 CocoaPods 操作：

- [**概述**](~/cross-platform/macios/binding/overview.md) -
  描述绑定的工作方式。
- [**绑定 Objective C 库**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  如何将绑定在 Xamarin 项目中使用的 Objective C 库的说明。
- [**键入定义参考指南**](~/cross-platform/macios/binding/binding-types-reference.md) -
  描述所有绑定作者驱动器绑定生成过程到可用的属性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[目标 Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目标 Sharpie 是有助于引导绑定的第一个传递的命令行工具。
它分析本机库映射到的公共 API 的标头文件的工作原理[绑定定义](~/cross-platform/macios/binding/objective-c-libraries.md)（否则手动完成该过程）。 目标 Sharpie 不会创建一个绑定本身，但它可以帮助你开始 ！

目标 Sharpie 3.0 引入了直接绑定 Cocoapods 的能力 ！

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[演练-绑定 iOS Objective C 库](walkthrough.md)

此页提供了创建 iOS 绑定项目使用的开放源代码的分步演练[ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) Objective C 项目作为示例。 **InfColorPicker**库提供了允许用户选择一种颜色基于其 HSB 表示形式，使颜色选择更加友好的用户的可重用的视图控制器。
目标 Sharpie 将用于协助绑定过程中。



## <a name="related-links"></a>相关链接

- [绑定 Objective-C](~/cross-platform/macios/binding/index.md)
- [Mac 绑定](~/mac/platform/binding.md)