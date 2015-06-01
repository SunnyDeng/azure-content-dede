<properties 
	pageTitle="Windows Store-SDK für Azure Mobile Engagement - Inhalt" 
	description="Neueste Updates und Verfahren für Windows Store-SDK für Azure Mobile Engagement" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# SDK-Inhalt

Dieses Dokument enthält eine Liste und beschreibt den Inhalt des SDK-Archivs.

## Der `Root` Ordner

Dieser Ordner enthält Kopien der Softwarelizenzen und einen Link zu der Onlinedokumentation.

`documentation.html` : Enthält einen Link zur Engagement-SDK-Onlinedokumentation für Windows 8 C#-Metro-Anwendung.

Dieser Ordner enthält auch Lizenzdateien.

## Der `/lib` Ordner

Dieser Ordner enthält Informationen darüber, wo Sie das Engagement-SDK erhalten.

`azuresdk-mobileengagement-windows-X.X.X.nupkg` : NuGet-Paket für die SDK-Integration.

## Der `/Resources` Ordner

Dieser Ordner enthält alle Ressourcen, die Engagement benötigt. Sie können Ihre App auch entsprechend anpassen.

`EngagementConfiguration.xml` : Die Konfigurationsdatei von Engagement, hier können Sie Engagement-Einstellungen (Engagement-Verbindungszeichenfolge, Berichtabsturz...) anpassen.

### /html-Ordner

`EngagementNotification.html` :  Das `HTML-Design der Webansicht `Benachrichtigung.

`EngagementAnnouncement.html` :  Das ` HTML-Design der Webansicht `Ankündigung.

### /images-Ordner

`EngagementIconNotification.png` : Das Markensymbol, das auf der linken Seite einer Benachrichtigung angezeigt wird.

`EngagementIconOk.png` :  Das `Ok`-Symbol der Reichweiten-Inhaltsseiten für die Aktions- oder Validierungsschaltfläche.

`EngagementIconNOK.png` :  Das `NOK`-Symbol wird verwendet, wenn die Validierungsschaltfläche der Reichweiten-Inhaltsseiten deaktiviert ist.

`EngagementIconClose.png` :  Das `Schließen`-Symbol der Reichweitenbenachrichtigungen und Inhalte für die Schaltfläche "Verwerfen".

### /Overlay-Ordner

`EngagementOverlayAnnouncement.xaml` :  Das `XAML-Design von `Ankündigung.

`EngagementOverlayAnnouncement.xaml.cs` : Der mit  `EngagementOverlayAnnouncement.xaml` verknüpfte Code.

`EngagementOverlayNotification.xaml` :  Das XAML-Design von  `Benachrichtigung`.

`EngagementOverlayNotification.xaml.cs` : Der mit  `EngagementOverlayNotification.xaml` verknüpfte Code.

`EngagementPageOverlay.cs` :  Der Anzeigecode für  `Überlagerungs`ankündigung und Benachrichtigung.

## Der `/src/agent` Ordner

Dieser Ordner enthält die EngagementPage.

`EngagementPage.cs` : Die Basisklasse für die Seiten, die automatisch eine Aktivität an Engagement meldet.

<!--HONumber=47-->
