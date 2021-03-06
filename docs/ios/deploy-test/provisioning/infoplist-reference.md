---
title: "Info.plist 引用"
ms.topic: article
ms.prod: xamarin
ms.assetid: 944DFDB5-ADBA-4D6E-984C-5AEC19A1CC57
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/18/2017
ms.openlocfilehash: 7732419af22ab8bd37cbfbf828dc59e48841217f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="infoplist-reference"></a>Info.plist 引用

有关使用 Info.Plist 键的详细信息，请参阅[保护安全和隐私](~/ios/app-fundamentals/security-privacy.md)指南。 

## <a name="location"></a>位置 

访问用户位置也需要对 Info.plist 进行修改。 应设置如下与位置数据相关的键： 

* **NSLocationWhenInUseUsageDescription** - 用于用户在和你的应用互动时访问用户的位置。 
* **NSLocationAlwaysUsageDescription** - 用于应用在后台访问用户的位置时。

## <a name="photos"></a>照片 

NSPhotoLibraryUsageDescription  

## <a name="contacts"></a>联系人 

NSContactsUsageDescription 

## <a name="calendar-data"></a>日历数据 
    
NSCalendarsUsageDescription 

## <a name="reminder-data"></a>提醒数据 
    
NSRemindersUsageDescription 

## <a name="bluetooth-peripherals"></a>蓝牙外围设备 
    
NSBluetoothPeripheralUsageDescription 

## <a name="microphone"></a>麦克风 

NSMicrophoneUsageDescription 

## <a name="camera"></a>照相机 
    
NSCameraUsageDescription 

## <a name="reading-healthkit"></a>读取 HealthKit  

NSHealthShareUsageDescription 

NSHealthUpdateUsageDescription 

## <a name="background-modes"></a>后台模式 
    
UIBackgroundModes 

## <a name="homekit"></a>HomeKit 

NSHomeKitUsageDescription 


## <a name="related-links"></a>相关链接

- [Apple 参考指南。](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW10)
