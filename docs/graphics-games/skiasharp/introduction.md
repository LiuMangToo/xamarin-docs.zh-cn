---
title: "SkiaSharp 简介"
description: "这提供了 SkiaSharp 背后的概念的简要介绍"
ms.topic: article
ms.prod: xamarin
ms.assetid: 19506F08-2603-465E-A806-6BD01638DE90
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 09/14/2017
ms.openlocfilehash: 747c158460b23b91e4c2986d212802528cdd3979
ms.sourcegitcommit: cc38757f56aab53bce200e40f873eb8d0e5393c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2018
---
# <a name="an-introduction-to-skiasharp"></a>SkiaSharp 简介

_这提供了 SkiaSharp 背后的概念的简要介绍_

SkiaSharp 提供丰富且功能强大二维图形 API 可用于呈现到二维缓冲区。  你可以使用这些实现自定义用户界面元素和可以合并到你的应用程序的二维图形。  SkiaSharp 是对的.NET 绑定[Skia](https://skia.org)库和继承的功能和此库的能力。

库是跨平台目前只有[NuGet 包](https://www.nuget.org/packages/SkiaSharp)，您可以通过添加 NuGet 引用将它添加到你的项目。

若要绘制，你的代码将创建`SkCanvas`该主题描述了将执行绘制操作的图面。

## <a name="obtaining-an-skcanvas"></a>获取 SKCanvas

```csharp
using (var surface = SKSurface.Create (width: 640, height: 480, SKImageInfo.PlatformColorType, SKAlphaType.Premul)) {
    SKCanvas myCanvas = surface.Canvas;

    // Your drawing code goes here.
}
```

## <a name="drawing-on-skcanvas"></a>在 SKCanvas 上绘制

`SKCanvas`使用其他图形的设计理念相似的绘图模型模型，你可能比较熟悉，使用可选的透明度通道的颜色，并可以绘制线条、 弧、 文本和图像。

以下是只是几个可通过 SkiaSharp 许多不同的事物。  在下面变量的示例`canvas`属于类型 SKCanvas。

### <a name="drawing-xamagon"></a>绘制 Xamagon

此示例绘制 Xamarin 的徽标 Xamagon:

```csharp
// clear the canvas / fill with white
canvas.Clear (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x2c, 0x3e, 0x50);
  paint.StrokeCap = SKStrokeCap.Round;

  // create the Xamagon path
  using (var path = new SKPath ()) {
    path.MoveTo (71.4311121f, 56f);
    path.CubicTo (68.6763107f, 56.0058575f, 65.9796704f, 57.5737917f, 64.5928855f, 59.965729f);
    path.LineTo (43.0238921f, 97.5342563f);
    path.CubicTo (41.6587026f, 99.9325978f, 41.6587026f, 103.067402f, 43.0238921f, 105.465744f);
    path.LineTo (64.5928855f, 143.034271f);
    path.CubicTo (65.9798162f, 145.426228f, 68.6763107f, 146.994582f, 71.4311121f, 147f);
    path.LineTo (114.568946f, 147f);
    path.CubicTo (117.323748f, 146.994143f, 120.020241f, 145.426228f, 121.407172f, 143.034271f);
    path.LineTo (142.976161f, 105.465744f);
    path.CubicTo (144.34135f, 103.067402f, 144.341209f, 99.9325978f, 142.976161f, 97.5342563f);
    path.LineTo (121.407172f, 59.965729f);
    path.CubicTo (120.020241f, 57.5737917f, 117.323748f, 56.0054182f, 114.568946f, 56f);
    path.LineTo (71.4311121f, 56f);
    path.Close ();

    // draw the Xamagon path
    canvas.DrawPath (path, paint);
  }
}
```

### <a name="drawing-text"></a>绘制文本

```csharp
// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.TextSize = 64.0f;
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x42, 0x81, 0xA4);
  paint.IsStroke = false;

  // draw the text
  canvas.DrawText ("Skia", 0.0f, 64.0f, paint);
}
```

### <a name="drawing-bitmaps"></a>绘制位图

```csharp
Stream fileStream = File.OpenRead ("MyImage.png");

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  canvas.DrawBitmap(bitmap, SKRect.Create(Width, Height), paint);
}
```

### <a name="drawing-with-image-filters"></a>绘制带有映像筛选器

```csharp
Stream fileStream = File.OpenRead ("MyImage.png"); // open a stream to an image file

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  // create the image filter
  using (var filter = SKImageFilter.CreateBlur(5, 5)) {
    paint.ImageFilter = filter;

    // draw the bitmap through the filter
    canvas.DrawBitmap(bitmap, SKRect.Create(width, height), paint);
  }
}
```

# <a name="more-information"></a>详细信息

有关使用 SkiaSharp 的详细信息可以位于[联机 API 文档](https://developer.xamarin.com/api/namespace/SkiaSharp/)


## <a name="related-links"></a>相关链接

- [SkiaSharp iOS 工作簿](https://developer.xamarin.com/workbooks/graphics/skiasharp/logo/skialogo-ios.workbook)
