
<properties 
    pageTitle="Verwenden von Office 365 mit Azure RemoteApp" 
    description="Erfahren Sie, wie Office 365 und Azure RemoteApp zusammenarbeiten" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/06/2015" 
    ms.author="elizapo" />

# Verwenden von Office 365 mit Azure RemoteApp

Sie haben zwei Möglichkeiten für das Hosten von Office-Anwendungen in RemoteApp: Office 365 ProPlus oder die Testversion von Office 2013 Professional Plus.

## Office 365 ProPlus 
Sie können eine RemoteApp-Sammlung mit dem Office 365 ProPlus-Vorlagenimage erstellen. Diese Option ermöglicht die Erweiterung Ihres Office 365-Diensts um RemoteApp. Dazu benötigen Sie einen vorhandenen Abonnementplan, und Ihre Benutzer müssen entweder eigenständig oder über Office 365-Servicepläne für den Office 365 ProPlus-Dienst lizenziert sein.

RemoteApp unterstützt die Aktivierung gemeinsam genutzter Computer für Office 365. Wenn Sie die Aktivierung gemeinsam genutzter Computer aktivieren und das [Office-Bereitstellungstool](http://www.microsoft.com/download/details.aspx?id=36778) für die Installation von Office 365 ProPlus verwenden, wird diese Installation ohne Aktivierung durchgeführt. Wenn sich ein Benutzer bei einer Sammlung mit Office 365 anmeldet, überprüft Office, ob der Benutzer für Office 365 ProPlus bereitgestellt wurde. Wenn dies zutrifft, aktiviert Office vorübergehend Office 365 ProPlus. Diese Aktivierung gilt, bis sich dieser Benutzer beim Dienst abmeldet.

Wenn Sie die Aktivierung gemeinsam genutzter Computer für Office 365 verwenden möchten, müssen Sie eine [benutzerdefinierte Vorlage](remoteapp-create-custom-image.md) erstellen und Office 365 ProPlus anhand [dieser Anleitung](https://technet.microsoft.com/library/dn782858.aspx) installieren.

Sie können Ihre Office 365-Benutzerlizenzen im [Verwaltungsportal von Office 365](https://portal.office365.com/) verwalten. Weitere Informationen finden Sie unter [Office 365-Servicepläne](http://technet.microsoft.com/library/office-365-plan-options.aspx).


## Office 2013 Professional Plus (Testversion) 
Während des 30-Tage-Tests von RemoteApp können Sie das Vorlagenimage von Office 2013 Professional Plus (Testversion) zum Erstellen einer RemoteApp-Sammlung verwenden. Sie können dieser Testsammlung Benutzer zuweisen und dafür deren Azure Active Directory-Geschäftskonten oder Microsoft-Konten verwenden. Ein zusätzliches Abonnement ist nicht erforderlich.

Dies ist eine hervorragende Möglichkeit, sich einen guten Eindruck zu verschaffen und Office in RemoteApp auszuprobieren. Allerdings ist diese Option nur zur Evaluierung und für Testzwecke vorgesehen. RemoteApp-Sammlungen, die mit dem Vorlagenimage von Office 2013 Professional Plus (Testversion) erstellt wurden, können nicht in den Produktionsmodus übergehen und werden am Ende des Testzeitraums deaktiviert.

## Wechseln von der Testversion zur Produktion
Zu Beginn Ihres kostenlosen 30-Tage-Tests wird im RemoteApp-Bereich des Portals ein Hinweis angezeigt, der besagt, wie lange Sie die Testversion noch nutzen können, bevor Sie zu einem gebührenpflichtigen Konto wechseln müssen. Sie können über den Link in diesem Hinweis Ihr Konto aktivieren und in den Produktionsmodus wechseln.

Wenn Sie Ihr Konto aktivieren, wirkt sich dies auf alle RemoteApp-Sammlungen in Ihrem Konto aus.

- Sammlungen, die mit Vorlagenimages von Windows Server 2012 R2 oder Office 365 ProPlus ausgeführt werden, gehen nahtlos in die Produktion über. Alle Benutzerdaten und Einstellungen, einschließlich der laufenden Sitzungen bleiben erhalten.
- Wenn Sie ein benutzerdefiniertes Vorlagenimage hochgeladen haben, gehen auch die Sammlungen, die diese Images verwenden, nahtlos in die Produktion über.
- Das Vorlagenimage von Office 2013 Professional Plus (Testversion) dient nur zu Testzwecken. Sammlungen, die mit diesem Vorlagenimage ausgeführt werden, können nicht in die Produktion übergehen. Sie werden in den Status "deaktiviert" versetzt.


Wenn Sie nach Ablauf des Testzeitraums nicht zum Produktionsmodus wechseln, werden die RemoteApp-Sammlungen deaktiviert. Keine Sorge - Ihre Einstellungen und Benutzerdaten bleiben für weitere 90 Tage gespeichert, sodass Sie Ihren Dienst noch aktivieren und ohne Datenverlust in den Produktionsmodus wechseln können.

<!--HONumber=54-->