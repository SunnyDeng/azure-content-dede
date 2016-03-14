<properties 
	pageTitle="Windows Universal Apps SDK – Inhalt" 
	description="Informieren Sie sich über den Inhalt des Windows Universal Apps SDK für Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

#Windows Universal Apps SDK – Inhalt

In diesem Dokument wird aufgelistet und beschrieben, was durch das SDK in der Anwendung bereitgestellt wird.

##Der Ordner `/Resources`

Dieser Ordner enthält alle von Mobile Engagement benötigten Ressourcen. Sie können diese auch an Ihre App anpassen.

- `EngagementConfiguration.xml` : Die Konfigurationsdatei von Mobile Engagement, hier können Sie Mobile Engagement-Einstellungen (Mobile Engagement-Verbindungszeichenfolge, Absturzbericht...) anpassen.

### /html-Ordner

- `EngagementNotification.html` : Der HTML-Entwurf der `Notification`-Webansicht

- `EngagementAnnouncement.html` : Der HTML-Entwurf der `Announcement`-Webansicht

### /images-Ordner

- `EngagementIconNotification.png` : Das Markensymbol, das auf der linken Seite einer Benachrichtigung angezeigt wird. Ersetzen Sie dieses Symbol durch Ihr eigenes Markensymbol.

- `EngagementIconOk.png` : Das `Ok`-Symbol der Reach-Inhaltsseiten für die Aktions- oder Validierungsschaltfläche.

- `EngagementIconNOK.png` : Das `NOK`-Symbol, das verwendet wird, wenn die Validierungsschaltfläche der Reach-Inhaltsseiten deaktiviert ist.
 
- `EngagementIconClose.png` : Das `Close`-Symbol der Reach-Benachrichtigungen und -Inhalte für die Schaltfläche zum Verwerfen.

### /overlay-Ordner

- `EngagementBaseOverlay.cs`: Der Basiscode, der von den Überlagerungen `Announcement` und `Notification` verwendet wird.

- `EngagementOverlayAnnouncement.xaml` : Der xaml-Entwurf für `Announcement`

- `EngagementOverlayAnnouncement.xaml.cs` : Der mit `EngagementOverlayAnnouncement.xaml` verknüpfte Code
 
- `EngagementOverlayNotification.xaml` : Der xaml-Entwurf für `Notification`
 
- `EngagementOverlayNotification.xaml.cs` : Der mit `EngagementOverlayNotification.xaml` verknüpfte Code
 
- `EngagementPageOverlay.cs` : Der Anzeigecode der `Overlay`-Ankündigung und -Benachrichtigung.
  

<!---HONumber=AcomDC_0302_2016-->