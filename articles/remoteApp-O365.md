<properties title="Using Office365 with Azure RemoteApp" pageTitle="Verwenden von Office 365 mit Azure RemoteApp" description="Informationen über das Zusammenwirken von Office 365 und Azure RemoteApp" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#Verwenden von Office 365 mit Azure RemoteApp

Sie haben zwei Möglichkeiten für das Hosten von Office-Anwendungen in RemoteApp: Office 365 ProPlus oder Office 2013 Professional Plus (Testversion).

##Office 365 ProPlus 
Sie können eine RemoteApp-Sammlung mit dem Office 365 ProPlus-Vorlagenimage erstellen. Diese Option ermöglicht die Erweiterung Ihres Office 365-Diensts um RemoteApp. Dazu benötigen Sie einen vorhandenen Abonnementplan, und Ihre Benutzer müssen entweder eigenständig oder über Office 365-Servicepläne für den Office 365 ProPlus-Dienst lizenziert sein. Die RemoteApp-Instanzen der Office-Anwendungen zählen zu den fünf Installationsinstanzen, die für jeden Benutzer zulässig sind, und werden in vollem Umfang aktiviert. Sie können Ihre Office 365-Benutzerlizenzen im [Verwaltungsportal von Office 365](https://portal.office365.com/) verwalten. Weitere Informationen finden Sie unter [Office 365-Servicepläne](http://technet.microsoft.com/library/office-365-plan-options.aspx).  

Die Office 365 ProPlus-Option ist sowohl während des kostenlosen 30-Tage-Tests als auch im Produktionsmodus verfügbar und wird nach Ablauf des Testzeitraums als einzige Option weiterhin unterstützt.  

Beachten Sie, dass Sie auch ein benutzerdefiniertes Vorlagenimage mit Office 365 ProPlus erstellen können. Um ein solches Vorlagenimage zu erstellen, führen Sie die [Bereitstellungsschritte](http://technet.microsoft.com/de-de/library/dn782858(v=office.15)aspx) für Office 365 ProPlus auf RDS aus.

##Office 2013 Professional Plus (Testversion) 
Während des 30-Tage-Tests von RemoteApp können Sie das Vorlagenimage von Office 2013 Professional Plus (Testversion) zum Erstellen einer RemoteApp-Sammlung verwenden. Sie können dieser Testsammlung Benutzer zuweisen und dafür deren Azure Active Directory-Geschäftskonten oder Microsoft-Konten verwenden. Ein zusätzliches Abonnement ist nicht erforderlich.

Dies ist eine hervorragende Möglichkeit, sich einen guten Eindruck zu verschaffen und Office in RemoteApp auszuprobieren. Allerdings ist diese Option nur zur Evaluierung und für Testzwecke vorgesehen. RemoteApp-Sammlungen, die mit dem Vorlagenimage von Office 2013 Professional Plus (Testversion) erstellt wurden, können nicht in den Produktionsmodus übergehen und werden am Ende des Testzeitraums deaktiviert.

##Wechseln von der Testversion zur Produktion
Zu Beginn Ihres kostenlosen 30-Tage-Tests wird im RemoteApp-Bereich des Portals ein Hinweis angezeigt, der besagt, wie lange Sie die Testversion noch nutzen können, bevor Sie zu einem gebührenpflichtigen Konto wechseln müssen. Sie können über den Link in diesem Hinweis Ihr Konto aktivieren und in den Produktionsmodus wechseln. 

Wenn Sie Ihr Konto aktivieren, wirkt sich dies auf alle RemoteApp-Sammlungen in Ihrem Konto aus. 

- Sammlungen, die mit Vorlagenimages von Windows Server 2012 R2 oder Office 365 ProPlus ausgeführt werden, gehen nahtlos in die Produktion über. Alle Benutzerdaten und Einstellungen, einschließlich der laufenden Sitzungen bleiben erhalten.
- Wenn Sie ein benutzerdefiniertes Vorlagenimage hochgeladen haben, gehen auch die Sammlungen, die diese Images verwenden, nahtlos in die Produktion über.
- Das Vorlagenimage von Office 2013 Professional Plus (Testversion) dient nur zu Testzwecken. Sammlungen, die mit diesem Vorlagenimage ausgeführt werden, können nicht in die Produktion übergehen. Sie werden in den Status "deaktiviert" versetzt.


Wenn Sie nach Ablauf des Testzeitraums nicht zum Produktionsmodus wechseln, werden die RemoteApp-Sammlungen deaktiviert. Keine Sorge - Ihre Einstellungen und Benutzerdaten bleiben für weitere 90 Tage gespeichert, sodass Sie Ihren Dienst noch aktivieren und ohne Datenverlust in den Produktionsmodus wechseln können.

<!--HONumber=35.2-->
