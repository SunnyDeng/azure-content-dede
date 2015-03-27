<properties 
	pageTitle="Integration von Azure Mobile Engagement für Windows Phone SDK" 
	description="Erfahren Sie mehr über den Inhalt des Windows Phone SDK für Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />
	
# SDK-Inhalt

Dieses Dokument enthält eine Liste und beschreibt den Inhalt des SDK-Archivs.

## Der `Root` Ordner

Dieser Ordner enthält Kopien der Softwarelizenzen und einen Link zu der Onlinedokumentation.

`documentation.html` : Enthält einen Link zur Engagement-SDK-Onlinedokumentation für Windows Phone.

Dieser Ordner enthält auch Lizenzdateien.

## Der `/lib` Ordner

Dieser Ordner enthält Informationen darüber, wo Sie das Engagement-SDK erhalten.

`azuresdk-mobileengagement-windowsphone-X.X.X.nupkg` : NuGet-Paket für die SDK-Integration.

## Der `/Resources` Ordner

Dieser Ordner enthält alle Ressourcen, die Engagement benötigt. Sie können Ihre App auch entsprechend anpassen.

`EngagementIconNotification.png` : Das Markensymbol, das auf der linken Seite einer Benachrichtigung angezeigt wird.

`EngagementIconOk.png` : Das Symbol "Ok" des ApplicationBar-Objekts auf den Reichweitenseiten.

`EngagementIconCancel.png` : Das Symbol "Abbrechen" des ApplicationBar-Objekts auf den Reichweitenseiten.

`EngagementIconCloseLight.png` : Das Symbol "Schließen" der Reichweitenbenachrichtigung von Engagement für Windows Phone, helles Design.

`EngagementIconCloseDark.png` : Das Symbol "Schließen", der Reichweitenbenachrichtigung von Engagement für Windows Phone, dunkles Design.

`EngagementConfiguration.xml` : Die Konfigurationsdatei von Engagement, hier können Sie Engagement-Einstellungen (Engagement-Verbindungszeichenfolge, Berichtabsturz...) anpassen.

## Der `/src/agent` Ordner

Dieser Ordner enthält die EngagementPage.

`EngagementPage.cs` : Die Basisklasse für die Seiten, die automatisch eine Aktivität an Engagement meldet.

## Der `/src/reach` Ordner

Abschließend finden Sie in diesem Ordner die Standardeinstellungen XAML (und deren C\#-Gegenstücke in) jeder Seite.

Sie können diese als Grundlage für Ihre eigenen Seiten verwenden. Befolgen Sie die Kommentare, um zu wissen, was Sie genau tun müssen.

### TextView-Ankündigung

`EngagementDefaultTextViewAnnouncementPage.xaml`

`EngagementDefaultTextViewAnnouncementPage.xaml.cs`

### WebView-Ankündigung

`EngagementDefaultWebViewAnnouncementPage.xaml`

`EngagementDefaultWebViewAnnouncementPage.xaml.cs`

### Umfrage

`EngagementDefaultPollPage.xaml`

`EngagementDefaultPollPage.xaml.cs`

### Benachrichtigung

`EngagementBasicNotificationView.xaml`

`EngagementBasicNotificationView.xaml.cs`

<!--HONumber=47-->
