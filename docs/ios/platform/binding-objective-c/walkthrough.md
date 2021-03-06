---
title: 演练： 将绑定 iOS Objective C 库
description: 本文提供了实际创建的现有 Objective C 库 InfColorPicker Xamarin.iOS 绑定的演练。 它涵盖主题，如编译静态 Objective C 库、 将绑定，以及在 Xamarin.iOS 应用程序中使用绑定。
ms.topic: article
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 44ed651413d66866f131a294158525440278b291
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>演练： 将绑定 iOS Objective C 库

_本文提供了实际创建的现有 Objective C 库 InfColorPicker Xamarin.iOS 绑定的演练。它涵盖主题，如编译静态 Objective C 库、 将绑定，以及在 Xamarin.iOS 应用程序中使用绑定。_

当运行 iOS，你可能会遇到你想要使用第三方 Objective C 库的情况。 在这些情况下，你可以使用 Xamarin.iOS_绑定项目_创建[C# 绑定](~/cross-platform/macios/binding/overview.md)，便可以使用你的 Xamarin.iOS 应用程序中的库。

通常，iOS 生态系统中你可以在 3 风格中找到库：

* 为使用预编译静态库文件`.a`以及其头 （.h 文件） 的扩展。 例如， [Google 分析库](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* 作为预编译的框架。 这是仅包含静态库、 标头和有时其他相关资源的文件夹`.framework`扩展。 例如， [Google AdMob 库](https://developers.google.com/admob/ios/download)。
* 正如刚才源代码文件。 例如，仅包含一个库`.m`和`.h`Objective C 文件。

中的第一个和第二个方案将已经有一个预编译的 CocoaTouch 静态库以便在本文中我们将重点放在第三个方案。 请记住，然后再开始创建一个绑定，请始终检查与以下库，以确保你可以自由地将其绑定提供了许可证。

本文提供了创建使用开放源代码绑定项目的分步演练[InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective C 项目作为示例，但是本指南中的所有信息都可以与任何调整，以使用第三方 OBJECTIVE-C 的库。 InfColorPicker 库提供了允许用户选择一种颜色基于其 HSB 表示形式，使颜色选择更加友好的用户的可重用的视图控制器。

[![](walkthrough-images/run01.png "在 iOS 上运行的 InfColorPicker 库的示例")](walkthrough-images/run01.png#lightbox)

我们将介绍所有必需的步骤来使用此特定的 OBJECTIVE-C 的 API，在 Xamarin.iOS 中：

- 首先，我们将创建使用 Xcode OBJECTIVE-C 的静态库。
- 然后，我们将绑定与 Xamarin.iOS 此静态库。
- 接下来，显示可以目标 Sharpie 如何通过自动生成某些 （但不是全部） 来减少工作负荷的 Xamarin.iOS 绑定所需的必要 API 定义。
- 最后，我们将创建一个 Xamarin.iOS 应用程序使用的绑定。

示例应用程序将演示如何使用强委托 InfColorPicker API 与 C# 代码之间的通信。 我们已了解如何使用强委托后，我们将介绍如何使用弱委托来执行相同的任务。

## <a name="requirements"></a>要求

本文假定你已具备一定安装了 Xcode 和 Objective C 语言，并且你已阅读我们[绑定 OBJECTIVE-C](~/cross-platform/macios/binding/index.md)文档。 此外，以下是完成提供的步骤是必需的：

-  **Xcode 和 iOS SDK** -Apple 的 Xcode 和最新的 iOS API 需要安装并配置开发人员计算机上。
-  **[Xcode 命令行工具](#Installing_the_Xcode_Command_Line_Tools)** -Xcode 命令行工具必须安装 Xcode （请参见以下的安装详细信息） 的当前安装的版本。
-  **用于 Mac 或 Visual Studio 的 visual Studio** -应该安装并配置开发计算机上用于 Mac 或 Visual Studio 的 Visual Studio 的最新版本。 Apple Mac 是必要条件开发 Xamarin.iOS 应用程序，并使用 Visual Studio 时你必须连接到[Xamarin.iOS 生成主机](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **最新版本的目标 Sharpie** -从目标 Sharpie 工具的当前副本下载[此处](~/cross-platform/macios/binding/objective-sharpie/get-started.md)。 如果你已安装的目标 Sharpie，你可通过更新它的最新版本使用 `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>安装 Xcode 命令行工具

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


如上面所述，我们将使用 Xcode 命令行工具 (专门`make`和`lipo`) 在本演练中。 `make`命令是一个非常常见的 Unix 实用工具，将通过使用自动化的可执行程序和库编译_生成文件_，它指定应如何生成程序。 `lipo`命令是一个 OS X 命令行实用程序，用于创建多体系结构文件; 它将组合多个`.a`到一个文件，它可由所有硬件体系结构使用的文件。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


如上面所述，我们将使用 Xcode 命令行工具上**Mac 生成主机**(专门`make`和`lipo`) 在本演练中。 `make`命令是一个非常常见的 Unix 实用工具，将通过使用自动化的可执行程序和库编译_生成文件_以指定如何生成来生成程序。 `lipo`命令是一个 OS X 命令行实用程序，用于创建多体系结构文件; 它将组合多个`.a`到一个文件，它可由所有硬件体系结构使用的文件。


-----

根据 Apple 的[生成从命令行中使用 Xcode 常见问题](https://developer.apple.com/library/ios/technotes/tn2339/_index.html)文档，为 OS X 10.9 和更高版本，则**下载**窗格 Xcode**首选项**对话框不不再支持支持下载命令行工具。

你将需要使用以下方法之一来安装这些工具：

- **安装 Xcode** -当你安装 Xcode，它与你的所有命令行工具捆绑。 在 OS X 10.9 填充 (安装在`/usr/bin`)，可以映射中包含任何工具`/usr/bin`到在 Xcode 内相应的工具。 例如，`xcrun`命令，从而允许您查找或从命令行运行在 Xcode 内的任何工具。
- **终端应用**-从终端应用程序，你可以通过运行中安装的命令行工具`xcode-select --install`命令：
    - 启动终端应用程序。
    - 类型`xcode-select --install`按**Enter**，例如：

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - 将要求你安装的命令行工具，请单击**安装**按钮： [ ![ ](walkthrough-images/xcode01.png "安装的命令行工具")](walkthrough-images/xcode01.png#lightbox)

    - 将下载这些工具，并将其从 Apple 的服务器安装： [ ![ ](walkthrough-images/xcode02.png "下载工具")](walkthrough-images/xcode02.png#lightbox)

- **下载的 Apple 开发人员**-命令行工具包形式提供[的 Apple 开发人员的下载]()网页。 登录你的 Apple ID，然后搜索，然后下载命令行工具： [ ![ ](walkthrough-images/xcode03.png "查找命令行工具")](walkthrough-images/xcode03.png#lightbox)

安装了命令行工具，我们已准备好继续演练。

## <a name="walkthrough"></a>演练

在此演练中，我们将介绍以下步骤：

- **[创建静态库](#Creating_A_Static_Library)** -此步骤包括创建静态库的**InfColorPicker** Objective C 代码。 静态库将具有`.a`文件扩展名，并且将嵌入到类库项目的.NET 程序集。
- **[创建 Xamarin.iOS 绑定项目](#Create_a_Xamarin.iOS_Binding_Project)** -一旦我们有一个静态库，我们将使用它创建 Xamarin.iOS 绑定项目。 绑定项目中包含的我们刚刚创建的静态库和 C# 代码，以解释如何使用 OBJECTIVE-C 的 API 的形式的元数据。 此元数据通常称为 API 定义。 我们将使用**[目标 Sharpie](#Using_Objective_Sharpie)** 以帮助我们与创建 API 定义。
- **[规范化 API 定义](#Normalize_the_API_Definitions)** -目标 Sharpie 能够很好地帮助我们，成功，但它不能执行的所有内容。 我们将讨论我们需要后，才可以使用对 API 定义进行一些更改。
- **[使用绑定库](#Using_the_Binding)** -最后，我们将创建一个 Xamarin.iOS 应用程序来演示如何使用我们新创建的绑定的项目。

现在，我们了解涉及哪些步骤，让我们继续本演练的其余部分。

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>创建静态库

如果我们在 Github 中的 InfColorPicker 的检查的代码：

[![](walkthrough-images/image02.png "检查 InfColorPicker 在 Github 中的代码")](walkthrough-images/image02.png#lightbox)

我们可以看到项目中的以下三个目录：

- **InfColorPicker** -此目录包含项目的 OBJECTIVE-C 的代码。
- **PickerSamplePad** -此目录包含示例 iPad 项目。
- **PickerSamplePhone** -此目录包含示例 iPhone 项目。

让我们下载 InfColorPicker 项目从[GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip)并将其解压缩我们选择的目录中。 打开 Xcode 目标`PickerSamplePhone`项目中，我们看到 Xcode 导航器中的以下项目结构：

[![](walkthrough-images/image03.png "Xcode 导航器中的项目结构")](walkthrough-images/image03.png#lightbox)

此项目通过直接将 InfColorPicker 源代码 （以红色框） 添加到每个示例项目来实现代码重用。 示例项目的代码是在蓝色框中。 由于此特定项目不提供我们与静态库，因此有必要为我们创建一个 Xcode 项目以编译此静态库。

第一步是我们将 InfoColorPicker 源代码添加到静态库。 让我们实现此执行以下操作：

1. 启动 Xcode。
2. 从**文件**菜单中选择**新建** > **项目...**:

    [![](walkthrough-images/image04.png "开始一个新项目")](walkthrough-images/image04.png#lightbox)
3. 选择**框架和库**、 **Cocoa Touch 静态库**模板，然后单击**下一步**按钮：

    [![](walkthrough-images/image05.png "选择 Cocoa Touch 静态库模板")](walkthrough-images/image05.png#lightbox)
4. 输入`InfColorPicker`为**项目名称**单击**下一步**按钮：

    [![](walkthrough-images/image06.png "InfColorPicker 输入项目名称")](walkthrough-images/image06.png#lightbox)
5. 选择一个位置来保存项目并单击**确定**按钮。
6. 现在，我们需要将源从 InfColorPicker 项目添加到我们的静态库项目。 因为**InfColorPicker.h**文件已存在在我们的静态库中 （默认），Xcode 将不允许我们要覆盖它。 从**Finder**、 导航到在原始项目，我们从 GitHub 解压缩的 InfColorPicker 源代码、 复制所有 InfColorPicker 文件和将其粘贴到我们的新静态库项目：

    [![](walkthrough-images/image12.png "复制所有 InfColorPicker 文件")](walkthrough-images/image12.png#lightbox)

7. 返回到 Xcode 中，右键单击**InfColorPicker**文件夹，然后选择**将文件添加到"InfColorPicker..."**:

    [![](walkthrough-images/image08.png "将文件添加")](walkthrough-images/image08.png#lightbox)

8. 从添加文件对话框中，导航到我们刚刚复制的 InfColorPicker 源代码文件，它们全部选中，然后单击**添加**按钮：

    [![](walkthrough-images/image09.png "选择所有，然后单击添加按钮")](walkthrough-images/image09.png#lightbox)

9. 源代码将复制到我们的项目：

    [![](walkthrough-images/image10.png "源代码将复制到项目")](walkthrough-images/image10.png#lightbox)

10. 从 Xcode 项目导航器中，选择**InfColorPicker.m**文件，并注释掉的最后两行 （由于写入此库，不使用此文件的方式）：

    [![](walkthrough-images/image14.png "编辑 InfColorPicker.m 文件")](walkthrough-images/image14.png#lightbox)

11. 现在，我们需要检查是否有任何库所需的框架。 在自述文件，或通过打开提供的示例项目之一，您可以找到此信息。 此示例使用`Foundation.framework`， `UIKit.framework`，和`CoreGraphics.framework`因此让我们添加它们。

12. 选择**InfColorPicker 目标 > 生成阶段**展开**二进制文件链接与库**部分：

    [![](walkthrough-images/image16b.png "展开链接二进制文件到库部分")](walkthrough-images/image16b.png#lightbox)

13. 使用**+**按钮以打开对话框让你上面列出的所需的帧框架添加：

    [![](walkthrough-images/image16c.png "上面列出的所需的帧框架添加")](walkthrough-images/image16c.png#lightbox)

14. **二进制文件链接与库**部分现在应类似下图所示：

    [![](walkthrough-images/image16d.png "二进制文件链接与库部分")](walkthrough-images/image16d.png#lightbox)

现在我们已关闭，但不是完全相同，我们已完成。 已创建了静态库，但我们需要编译它以创建 Fat 二进制，包含所有所需的体系结构的 iOS 设备和 iOS 模拟器。

### <a name="creating-a-fat-binary"></a>创建一个 Fat 二进制文件

所有 iOS 设备都有都了随着时间的推移而开发的处理器由 ARM 体系结构提供支持。 每个新的体系结构添加新指令和其他改进同时，仍保持向后兼容性。 在 iOS 设备上我们有 armv6、 armv7、 armv7s、 arm64 指令集 – 虽然[我们不再使用 armv6](~/ios/deploy-test/compiling-for-different-devices.md)。 IOS 模拟器不支持通过 ARM 和 x86 和提供支持的 x86_64 模拟器是而不是去。 我们对我们的意义是，我们必须在每个指令提供库设置。

Fat 库是`.a`文件包含所有支持的体系结构。

创建 fat 二进制是一个三步骤过程：

- 编译静态库的 ARM 7 和 ARM64 版本。
- 编译静态库的 x86 和 x84_64 版本。
- 使用`lipo`命令行工具将两个静态库合并成一个。

虽然这三个步骤是相当简单，并且它可能需要在将来重复这些步骤在 Objective C 库接收更新，或如果我们需要 bug 修复。 如果你决定自动执行这些步骤，它将简化未来的维护和支持 iOS 绑定项目。

有许多工具可用于自动执行此类任务-一个 shell 脚本， [rake](http://rake.rubyforge.org/)， [xbuild](http://www.mono-project.com/docs/tools+libraries/tools/xbuild/)，和[使](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html)。 当我们安装 Xcode 命令行工具时，我们也会安装生成，因此，它是将用于本演练的生成系统。 下面是**生成文件**可用于创建将在 iOS 设备和任何库的模拟器运行的多体系结构共享的库：

```bash
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=./YOUR-PROJECT-NAME
PROJECT=$(PROJECT_ROOT)/YOUR-PROJECT-NAME.xcodeproj
TARGET=YOUR-PROJECT-NAME

all: lib$(TARGET).a

lib$(TARGET)-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

lib$(TARGET)-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET)-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET).a: lib$(TARGET)-i386.a lib$(TARGET)-armv7.a lib$(TARGET)-arm64.a
    xcrun -sdk iphoneos lipo -create -output $@ $^

clean:
    -rm -f *.a *.dll
```

输入**生成文件**你选择的纯文本编辑器中的命令和更新各部分之间用**您的项目名称-**替换为你的项目的名称。 也很重要，以确保我们将粘贴所述的说明，说明中的选项卡都已被保留。

名称保存该文件**生成文件**到我们在上面创建的 InfColorPicker Xcode 静态库相同的位置：

[![](walkthrough-images/lib00.png "生成文件的名称保存该文件")](walkthrough-images/lib00.png#lightbox)

打开你的 Mac 上的终端应用程序并导航到你的生成文件的位置。 类型`make`到终端，按**Enter**和**生成文件**将执行：

[![](walkthrough-images/lib01.png "示例生成文件输出")](walkthrough-images/lib01.png#lightbox)

运行生成时，你将看到大量的文本通过滚动。 如果一切运行正常，你将看到单词**生成成功**和`libInfColorPicker-armv7.a`，`libInfColorPicker-i386.a`和`libInfColorPickerSDK.a`文件将复制到与相同的位置**生成文件**:

[![](walkthrough-images/lib02.png "生成文件生成的 libInfColorPicker armv7.a、 libInfColorPicker i386.a 和 libInfColorPickerSDK.a 文件")](walkthrough-images/lib02.png#lightbox)

您可以通过使用以下命令来确认你 Fat 的二进制文件中的体系结构：

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

这应会显示以下：

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

此时，我们已通过创建静态库使用 Xcode 和 Xcode 命令行工具来完成我们 iOS 绑定的第一步`make`和`lipo`。 让我们将移到下一步并使用**目标 Sharpie**自动创建为我们的 API 绑定。

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>创建绑定项目 Xamarin.iOS

我们可以使用之前**目标 Sharpie**为了自动执行绑定进程，我们需要创建用于容纳 API 定义的 Xamarin.iOS 绑定项目 (我们将使用**目标 Sharpie**以帮助我们生成） 并创建为我们的 C# 绑定。

让我们执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 启动 Visual Studio for mac。
1. 从**文件**菜单上，选择**新建** > **解决方案...**:

    ![](walkthrough-images/bind01.png "启动一个新的解决方案")

1. 从新的解决方案对话框中，选择**库** > **iOS 绑定项目**:

    ![](walkthrough-images/bind02.png "选择 iOS 绑定项目")

1. 单击**下一步**按钮。

1. 输入"InfColorPickerBinding"作为**项目名称**单击**创建**按钮以创建解决方案：

    ![](walkthrough-images/bind02a.png "输入 InfColorPickerBinding 作为项目名称")

将创建解决方案和两个默认文件将包括：

![](walkthrough-images/bind03.png "在解决方案资源管理器中的解决方案结构")


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. 启动 Visual Studio。

1. 从**文件**菜单上，选择**新建** > **项目...**:

    ![](walkthrough-images/bind01vs.png "开始一个新项目")

1. 从新建项目对话框中，选择**iOS** > **绑定库**:

    ![](walkthrough-images/bind02vs.png "选择 iOS 绑定库")

1. 输入"InfColorPickerBinding"作为**名称**单击**确定**按钮以创建解决方案。

将创建解决方案和两个默认文件将包括：

![](walkthrough-images/bind03vs.png "在解决方案资源管理器中的解决方案结构")

-----



- **ApiDefinition.cs** -此文件将包含定义如何将 OBJECTIVE-C 的 API 的包装在 C# 中的协定。
- **Structs.cs** -此文件将保存的任何结构或枚举值所需的接口和委托。

我们将使用在本演练后面这两个文件。 首先，我们需要将 InfColorPicker 库添加到绑定项目。

### <a name="including-the-static-library-in-the-binding-project"></a>绑定项目中包括的静态库

现在，我们使用我们基本绑定项目准备就绪，我们需要添加 Fat 二进制库我们上面创建的**InfColorPicker**库。

请按照下列步骤添加库：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 右键单击**本机引用**文件夹中的解决方案的填充和选择**添加本机引用**:

    ![](walkthrough-images/bind04a.png "添加本机引用")

1. 导航到 Fat 二进制我们进行了更早版本 (`libInfColorPickerSDK.a`)，然后按**打开**按钮：

    ![](walkthrough-images/bind05.png "选择 libInfColorPickerSDK.a 文件")
1. 将项目中包括的文件：

    ![](walkthrough-images/bind04.png "包括文件")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 复制`libInfColorPickerSDK.a`从你**Mac 生成主机**和将其粘贴到你绑定的项目。

1. 右键单击项目并选择**添加 > 现有项...**:

    ![](walkthrough-images/bind04vs.png "添加现有文件")

1. 导航到`libInfColorPickerSDK.a`按**添加**按钮：

    ![](walkthrough-images/bind05vs.png "添加 libInfColorPickerSDK.a")

1. 将项目中包括的文件。

-----


当.a 文件添加到项目中时，将自动设置 Xamarin.iOS**生成操作**的文件的**ObjcBindingNativeLibrary**，并创建名为特殊文件`libInfColorPickerSDK.linkwith.cs`。


此文件包含`LinkWith`告知 Xamarin.iOS 如何句柄的静态库的刚刚添加的属性。 在下面的代码段显示了此文件的内容：

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

`LinkWith`属性标识对于项目以及一些重要链接器标志的静态库。


因此我们需要做的下一步是创建 InfColorPicker 项目的 API 定义。 对于本演练的目的，我们将使用目标 Sharpie 生成文件**ApiDefinition.cs**。

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>使用目标 Sharpie

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


目标 Sharpie 是一个命令行工具 （由 Xamarin） 可以帮助你创建向 C# 中绑定的第三方 Objective C 库所需的定义。 在本部分中，我们将使用目标 Sharpie 创建初始**ApiDefinition.cs** InfColorPicker 项目。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


目标 Sharpie 是一个命令行工具 （由 Xamarin） 可以帮助你创建向 C# 中绑定的第三方 Objective C 库所需的定义。 在本部分中，我们将使用目标 Sharpie 上我们**Mac 生成主机**创建初始**ApiDefinition.cs** InfColorPicker 项目。


-----

若要开始，让我们下载目标 Sharpie 安装程序文件，如所述这[指南](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing)。 运行安装程序并从安装向导在我们的开发计算机上安装目标 Sharpie 按照所有屏幕提示进行操作。

我们已成功地将目标 Sharpie 后安装，让我们启动终端应用并输入以下命令以获取有关的所有工具，以帮助在绑定中提供了帮助：

```bash
sharpie -help
```

如果我们执行上面的命令，将生成以下输出：

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:

  xcode    Get information about Xcode installations and available SDKs.

  bind     Create a Xamarin C# binding to Objective-C APIs
Europa:Resources kmullins$
```

为了进行此演练中，我们将使用以下目标 Sharpie 工具：

- **xcode** -该工具会为我们提供有关我们当前的 Xcode 安装和 iOS 和 Mac Api，我们已安装的版本信息。 我们将使用此信息更高版本时我们生成我们的绑定。
- **将绑定**-我们将使用此工具来分析**.h**到初始 InfColorPicker 项目中的文件**ApiDefinition.cs**和**StructsAndEnums.cs**文件。

若要获得关于特定目标 Sharpie 工具帮助，请输入工具的名称和`-help`选项。 例如，`sharpie xcode -help`将返回以下输出：

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, --help                 Show detailed help
  -v, --verbose              Be verbose with output
      --sdks                 List all available Xcode SDKs. Pass -verbose for
                               more details.
Europa:Resources kmullins$
```

我们可以开始绑定过程之前，我们需要通过终端中输入以下命令获取有关我们当前已安装的 Sdk 信息`sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

综上所述，我们可以看到我们具有`iphoneos8.1`我们计算机上安装 SDK。 使用就地此信息，我们已准备好分析 InfColorPicker 项目`.h`文件到初始**ApiDefinition.cs**和`StructsAndEnums.cs`InfColorPicker 项目。

输入以下命令中的的 Terminal 应用：

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

其中`[full-path-to-project]`是到目录的完整路径其中**InfColorPicker** Xcode 项目文件将位于我们在计算机上，并通过所述，[iphone-os] 是 iOS 我们已安装的 SDK`sharpie xcode -sdks`命令。 请注意，在此示例中我们已传递 **\*.h**作为参数，其中包括*所有*此目录中的中的标头文件通常你应不这样做，但改为仔细阅读标头文件，以了解顶级**.h**引用所有其他相关的文件，并且只传递给目标 Sharpie 的文件。

以下[输出](walkthrough-images/os05.png)将生成在终端中：

```bash
Europa:Resources kmullins$ sharpie bind -output InfColorPicker -namespace InfColorPicker -sdk iphoneos8.1 /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h -unified
Compiler configuration:
    -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk -miphoneos-version-min=8.1 -resource-dir /Library/Frameworks/ObjectiveSharpie.framework/Versions/1.1.1/clang-resources -arch armv7 -ObjC

[  0%] parsing /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h
In file included from /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h:60:
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* sourceColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* resultColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
4 warnings generated.
[100%] parsing complete
[bind] InfColorPicker.cs
Europa:Resources kmullins$
```

与**InfColorPicker.enums.cs**和**InfColorPicker.cs**将我们目录中创建文件：

[![](walkthrough-images/os06.png "InfColorPicker.enums.cs 和 InfColorPicker.cs 文件")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


在我们在上面创建的绑定项目中打开这两个这些文件。 内容复制**InfColorPicker.cs**文件并将其粘贴到**ApiDefinition.cs**文件，将现有`namespace ...`的内容的代码块**InfColorPicker.cs**文件 (离开`using`语句不变):

![](walkthrough-images/os07.png "InfColorPickerControllerDelegate 文件")


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


在我们在上面创建的绑定项目中打开这两个这些文件。 内容复制**InfColorPicker.cs**文件 (从**Mac 生成主机**) 将其粘贴到**ApiDefinition.cs**文件，将现有`namespace ...`内容的代码块**InfColorPicker.cs**文件 (离开`using`语句不变)。


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>规范化的 API 定义

目标 Sharpie 有时具有问题转换`Delegates`，因此我们将需要修改的定义`InfColorPickerControllerDelegate`接口，并替换`[Protocol, Model]`替换为以下行：

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
以便定义如下所示：

[![](walkthrough-images/os11.png "定义")](walkthrough-images/os11.png#lightbox)

接下来，我们执行的内容相同的操作`InfColorPicker.enums.cs`文件，复制和粘贴在`StructsAndEnums.cs`文件，保留`using`语句不变：

[![](walkthrough-images/os09.png "内容 StructsAndEnums.cs 文件 ")](walkthrough-images/os09.png#lightbox)

你还可能会发现目标 Sharpie 已批注的绑定`[Verify]`属性。 这些特性指示，你应验证目标 Sharpie 通过比较与原始 C/Objective C 声明 （这将提供一条注释，该绑定的声明的上方） 绑定未正确操作。 一旦确认绑定，应删除验证属性。 有关详细信息，请参阅[验证](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)指南。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


此时，我们绑定项目应该完整且准备好生成。 让我们生成我们绑定项目，并确保我们结束且未出错：

[生成绑定项目，然后确保没有任何错误](walkthrough-images/os12.png)


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


此时，我们绑定项目应该完整且准备好生成。 让我们生成我们绑定项目，并确保，我们最终的任何错误。


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>使用绑定

请按照下列步骤以创建示例 iPhone 应用程序，以使用的 iOS 上面创建的绑定的库：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. **创建 Xamarin.iOS 项目**-添加一个名为的新的 Xamarin.iOS 项目**InfColorPickerSample**到解决方案中，如以下屏幕截图中所示：

    ![](walkthrough-images/use01.png "添加单一视图应用程序")

    ![](walkthrough-images/use01a.png "设置标识符")

1. **添加到绑定项目引用**-更新**InfColorPickerSample**项目，使其具有对引用**InfColorPickerBinding**项目：

    ![](walkthrough-images/use02.png "添加到绑定项目引用")

1. **创建 iPhone 用户界面**-双击**MainStoryboard.storyboard**文件中**InfColorPickerSample**项目以在 iOS 设计器中编辑它。 添加**按钮**到视图并调用它`ChangeColorButton`，如在下面的示例所示：

    ![](walkthrough-images/use03.png "将按钮添加到视图")
1. **添加 InfColorPickerView.xib** -InfColorPicker Objective C 库包括**.xib**文件。 Xamarin.iOS 将不包括此**.xib**在绑定项目中，这将导致在我们的示例应用程序的运行时错误。 此解决方法是将添加**.xib**到我们的 Xamarin.iOS 项目文件。 选择 Xamarin.iOS 项目，右键单击并选择**添加 > 添加文件**，并添加**.xib**文件中的以下屏幕截图所示：

    ![](walkthrough-images/use04.png "添加 InfColorPickerView.xib")

1. 当系统询问，复制**.xib**到项目的文件。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. **创建 Xamarin.iOS 项目**-添加一个名为的新的 Xamarin.iOS 项目**InfColorPickerSample**到解决方案中，如下面的屏幕截图中所示：

    ![](walkthrough-images/use01vs.png "创建 Xamarin.iOS 项目")

1. **添加到绑定项目引用**-更新**InfColorPickerSample**项目，使其具有对引用**InfColorPickerBinding**项目：

    ![](walkthrough-images/use02vs.png "添加到绑定项目引用")

1. **创建 iPhone 用户界面**-双击**MainStoryboard.storyboard**文件中**InfColorPickerSample**项目以在 iOS 设计器中编辑它。 添加**按钮**到视图并调用它`ChangeColorButton`，如在下面的示例所示：

    ![](walkthrough-images/use03vs.png "创建 iPhone 用户界面")

1. **添加 InfColorPickerView.xib** -InfColorPicker Objective C 库包括**.xib**文件。 Xamarin.iOS 将不包括此**.xib**在绑定项目中，这将导致在我们的示例应用程序的运行时错误。 此解决方法是将添加**.xib**到我们 Xamarin.iOS 项目文件我们**Mac 生成主机**。 选择 Xamarin.iOS 项目，右键单击并选择**添加** > **现有项...**，并添加**.xib**文件。


-----



接下来，让我们快速了解一下 Objective C 和我们处理这些绑定和 C# 代码中的协议。

### <a name="protocols-and-xamarinios"></a>协议和 Xamarin.iOS

在 OBJECTIVE-C 的一种协议定义方法 （或消息），可以在某些情况下使用。 从概念上讲，它们是非常类似于 C# 中的接口。 Objective C 协议和 C# 接口之间的一个主要区别是协议都可以具有可选方法的类没有实现的方法。 Objective C 使用@optional关键字用于指示哪些方法都是可选的。 有关协议的详细信息请参阅[事件、 协议和委托](~/ios/app-fundamentals/delegates-protocols-and-events.md)。

**InfColorPickerController**具有一个此类协议，以下代码段中所示：

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

通过使用此协议**InfColorPickerController**以通知客户端用户具有选取新的颜色， **InfColorPickerController**完成。 目标 Sharpie 映射此协议，如下面的代码段中所示：

```csharp
[BaseType(typeof(NSObject))]
[Model]
public partial interface InfColorPickerControllerDelegate {

    [Export ("colorPickerControllerDidFinish:")]
    void ColorPickerControllerDidFinish (InfColorPickerController controller);

    [Export ("colorPickerControllerDidChangeColor:")]
    void ColorPickerControllerDidChangeColor (InfColorPickerController controller);
}

```

Xamarin.iOS 编译绑定库后，将创建名为的抽象基类`InfColorPickerControllerDelegate`，它与虚拟方法实现此接口。

有两种方法，我们可以在 Xamarin.iOS 应用程序中实现此接口：

- **强委托**-使用强委托涉及到创建该子类的 C# 类`InfColorPickerControllerDelegate`并重写适当的方法。 **InfColorPickerController**将使用此类的实例与其客户端通信。
- **弱委托**-弱委托是一种略有不同的技术，涉及到一些类上创建一个公共方法 (如`InfColorPickerSampleViewController`)，然后曝光到该方法`InfColorPickerDelegate`协议通过`Export`属性。

强委托提供智能感知、 类型安全和更好地封装。 出于这些原因，你应使用强的委托，你可在其中，而不是弱委托。

在本演练中，我们将讨论这两种方法： 首先实现强委托，委托，然后说明如何实现弱委托。

### <a name="implementing-a-strong-delegate"></a>实现强委托

完成 Xamarin.iOS 应用程序使用强委托响应`colorPickerControllerDidFinish:`消息：

**子类 InfColorPickerControllerDelegate** -将新类添加到名为的项目`ColorSelectedDelegate`。 编辑类，以便它具有以下代码：

```csharp
using InfColorPickerBinding;
using UIKit;

namespace InfColorPickerSample
{
    public class ColorSelectedDelegate:InfColorPickerControllerDelegate
    {
        readonly UIViewController parent;

        public ColorSelectedDelegate (UIViewController parent)
        {
            this.parent = parent;
        }

        public override void ColorPickerControllerDidFinish (InfColorPickerController controller)
        {
            parent.View.BackgroundColor = controller.ResultColor;
            parent.DismissViewController (false, null);
        }
    }
}
```

Xamarin.iOS 将通过创建一个名为的抽象基类绑定 Objective C 委托`InfColorPickerControllerDelegate`。 子类此类型和替代`ColorPickerControllerDidFinish`方法来访问的值`ResultColor`属性`InfColorPickerController`。

**创建的实例 ColorSelectedDelegate** -我们事件处理程序将需要的实例`ColorSelectedDelegate`我们在上一步中创建的类型。 编辑类`InfColorPickerSampleViewController`并将以下实例变量添加到类：

```csharp
ColorSelectedDelegate selector;
```

**初始化 ColorSelectedDelegate 变量**-若要确保`selector`是一个有效实例，更新方法`ViewDidLoad`中`ViewController`以匹配以下代码段：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**实现方法 HandleTouchUpInsideWithStrongDelegate**的下一步实现的事件处理程序时在用户接触**ColorChangeButton**。 编辑`ViewController`，并添加以下方法：

```csharp
using InfColorPicker;
...

private void HandleTouchUpInsideWithStrongDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.Delegate = selector;
    picker.PresentModallyOverViewController (this);
}

```

我们首先获取的实例`InfColorPickerController`通过静态方法，并且请注意通过属性我们强委托实例`InfColorPickerController.Delegate`。 此属性是自动生成为我们的目标 Sharpie。 最后，我们调用`PresentModallyOverViewController`若要显示的视图`InfColorPickerSampleViewController.xib`，以便用户可以选择一种颜色。

**运行应用程序**-此时我们已经完成了所有代码。 如果你运行应用程序，你应能更改的背景色`InfColorColorPickerSampleView`如以下屏幕截图中所示：

[![](walkthrough-images/run01.png "运行应用程序")](walkthrough-images/run01.png#lightbox)

祝贺你！ 此时已成功创建，并绑定在 Xamarin.iOS 应用程序中使用 OBJECTIVE-C 的库。 接下来，让我们了解如何使用弱的委托。

### <a name="implementing-a-weak-delegate"></a>实现弱委托

而不是子类化的类为特定的委托绑定到 OBJECTIVE-C 的协议，Xamarin.iOS 还可以在任何派生自的类中实现的协议方法`NSObject`，修饰你的方法与`ExportAttribute`，，然后提供适当的选择器。 采用此方法时，你的类的一个实例分配`WeakDelegate`属性而不是为`Delegate`属性。 弱委托可灵活地采用不同的继承层次结构下你委托类。 让我们将了解如何实现并在我们的 Xamarin.iOS 应用程序中使用弱委托。

**为 TouchUpInside 创建事件处理程序**-让我们来创建新的事件处理程序`TouchUpInside`更改背景色按钮的事件。 此处理程序将填充与相同的角色`HandleTouchUpInsideWithStrongDelegate`我们在上一节中创建，但将使用弱委托而不是强委托处理程序。 编辑类`ViewController`，并添加以下方法：

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**更新 ViewDidLoad** -我们必须更改`ViewDidLoad`，使其使用我们刚刚创建的事件处理程序。 编辑`ViewController`和更改`ViewDidLoad`以类似于下面的代码段：


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**处理 colorPickerControllerDidFinish： 消息**-当`ViewController`是已完成，iOS 将发送该邮件`colorPickerControllerDidFinish:`到`WeakDelegate`。 我们需要创建可以处理此消息的 C# 方法。 若要执行此操作，我们创建的 C# 方法，然后修饰它与`ExportAttribute`。 编辑`ViewController`，并将以下方法添加到类：

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

运行该应用程序。 它应现在的行为完全相同它以前，但它使用弱委托而不强的委托。 此时您已成功完成本演练。 你应该已经了解如何创建和使用 Xamarin.iOS 绑定项目。

## <a name="summary"></a>总结

本文已指导完成创建和使用 Xamarin.iOS 绑定项目的过程。 首先，我们讨论了如何将现有的 Objective C 库编译为静态库。 然后，我们介绍如何创建 Xamarin.iOS 绑定项目，以及如何使用目标 Sharpie 生成 Objective C 库的 API 定义。 我们讨论了如何更新并对生成的 API 定义，以使它们适合公共使用进行调整。 Xamarin.iOS 绑定项目已完成后，我们在上移动到使用该绑定在 Xamarin.iOS 应用程序，以在使用强委托和弱的委托。

## <a name="related-links"></a>相关链接

- [绑定示例 （示例）](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [绑定 Objective-C 库](~/cross-platform/macios/binding/objective-c-libraries.md)
- [绑定详细信息](~/cross-platform/macios/binding/overview.md)
- [绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [面向 Objective-C 开发人员的 Xamarin](~/ios/get-started/objective-c-developers/index.md)
- [框架设计指南](http://msdn.microsoft.com/en-us/library/ms229042.aspx)
