---
title: "样式"
description: "使用样式外观进行自定义"
ms.topic: article
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 934948579e5f3fb19c7afe49f4e86a1ef255b77f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="styles"></a>样式

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

Xamarin.Forms 应用程序通常包含多个具有相同的外观的控件。 设置每个控件的外观可能非常重复，而且容易出错。 相反，样式可以创建自定义控件外观的分组和可用的控件类型的设置属性。

## <a name="explicit-stylesexplicitmd"></a>[显式样式](explicit.md)

*显式*样式是指通过设置有选择地应用于控件及其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性。

## <a name="implicit-stylesimplicitmd"></a>[隐式样式](implicit.md)

*隐式*样式是指所有控件的相同都使用[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)，而无需每个控件，以引用样式。

## <a name="global-stylesapplicationmd"></a>[全局样式](application.md)

样式可提供全局通过将它们添加到应用程序的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 这有助于避免能跨页或控件的重复样式。

## <a name="style-inheritanceinheritancemd"></a>[样式继承](inheritance.md)

样式可以继承其他样式来减少重复并启用重用。

## <a name="dynamic-stylesdynamicmd"></a>[动态样式](dynamic.md)

样式，不要响应属性更改和应用程序的持续时间内保持不变。 但是，应用程序可以响应在运行时动态的样式更改通过使用动态资源。

## <a name="device-stylesdevicemd"></a>[设备样式](device.md)

Xamarin.Forms 包括六个*动态*样式，称为*设备*样式，在[ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)类。 可将所有六个样式应用于[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)仅限实例。
