---
title: "在 Xamarin.Android 的通知"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: bc39faa37adae660a7751313d0d573237fadce94
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="notifications-in-xamarinandroid"></a>在 Xamarin.Android 的通知


## <a name="overview"></a>概述

本部分介绍如何在 Xamarin.Android 中实现通知。 它介绍 Android 通知的各种 UI 元素，并介绍 API 的涉及创建和显示一条通知。


## <a name="sections"></a>部分

### <a name="local-notifications-in-androidlocal-notificationsmd"></a>[在 Android 中的本地通知](local-notifications.md)

本部分介绍如何在 Xamarin.Android 中实现本地通知。 它介绍 Android 通知的各种 UI 元素，并讨论 API 的涉及创建和显示一条通知。 

### <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[演练-在 Xamarin.Android 中使用本地通知](local-notifications-walkthrough.md)  
 
本演练介绍如何在 Xamarin.Android 应用程序中使用本地通知。 它演示了创建和发布通知的基础知识。 当用户单击通知抽屉中的通知时它会启动第二个活动。 


## <a name="for-further-reading"></a>其他阅读材料

[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase 云消息传送 (FCM) 是一项服务，它方便了移动应用程序和服务器应用程序之间的消息传递。 Firebase Cloud Messaging 可以用于实现 （也称为推送通知） 的远程通知 Xamarin.Android 应用程序中。

[通知](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)&ndash;此 Android 开发人员主题是 Android 通知的权威指南。 它包括的设计注意事项部分，可帮助你设计你的通知，以便它们符合 Android 用户界面的准则。 启动活动时，它提供有关 preserviing 导航的更多背景信息，并介绍如何在通知和控制的媒体播放上锁定屏幕中显示进度。 

[NotificationListenerService](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) &ndash;此 Android 服务使您的应用程序侦听 （并与之交互） 所有通知都发布在 Android 设备上，而不仅仅是你的应用程序注册到通知接收。 请注意，用户必须显式授予对你的应用程序要成为能够侦听通知设备上的权限。





## <a name="related-links"></a>相关链接

- [本地通知 （示例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [远程通知 （示例）](https://developer.xamarin.com/samples/monodroid/RemoteNotifications/)
