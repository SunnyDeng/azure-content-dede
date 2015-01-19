<properties title="What is RemoteApp?" pageTitle="Was ist RemoteApp?" description="Informationen über Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Was ist Azure RemoteApp?
 
Azure RemoteApp stattet Azure mit der Funktionalität des lokalen Microsoft RemoteApp-Programms, unterstützt von Remotedesktopdiensten, aus. Azure RemoteApp sorgt für den sicheren Remotezugriff auf Anwendungen von vielen unterschiedlichen Benutzergeräten aus.

Wenn Sie RemoteApp für Azure verwenden, können Sie den Speicher, die Skalierbarkeit und die globale Reichweite von Azure nutzen, ohne sich um eine komplexe lokale Konfiguration kümmern zu müssen. Microsoft sorgt für die Wartung von Azure und damit auch für die Zuverlässigkeit der Software. Dadurch können Sie sich auf wichtigere Dinge konzentrieren, wie z. B. auf die Erstellung optimaler Apps für Ihr Unternehmen. Ein weiterer Vorteil von Azure RemoteApp ist der Zugriff - Benutzer können von Geräten mit Windows, iOS, Mac OS X und Android auf die RemoteApp-Programme zugreifen. Die Benutzer können die Apps in der von ihnen bevorzugten Umgebung verwenden, während Ihnen für die Verwaltung dieser Apps das Azure-Verwaltungsportal zur Verfügung stehe. 

Fahren Sie mit der Lektüre dieses Artikels fort, um weitere Informationen zu erhalten, oder [testen Sie jetzt](http://azure.microsoft.com/de-de/services/remoteapp/), wenn wir Sie bereits überzeugt haben.

Haben Sie Fragen zu Azure RemoteApp? Schlagen Sie unter den [häufig gestellten Fragen ](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-faq/) nach.

Azure RemoteApp ist Teil der [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/de-de/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Neu!** Möchten Sie mehr über Azure RemoteApp erfahren? Oder sind Sie bereit, RemoteApp im größeren Umfang zu testen? Nehmen Sie an unserem wöchentlichen [Ask the Experts Webinar ](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html) teil.

##RemoteApp-Bereitstellungsoptionen
Es gibt zwei Arten von RemoteApp-Sammlungen:


- Eine **Cloud-Sammlung** wird in der Cloud gehostet und speichert alle Daten für Programme in der Azure-Cloud. Benutzer können sich mit ihrem Microsoft-Konto oder mit den Anmeldeinformationen des Unternehmens, die mit Azure Active Directory verbunden sind oder synchronisiert werden, anmelden und auf die Apps zugreifen.
- Eine **Hybrid-Sammlung** wird in der Cloud gehostet und speichert die Daten in der Azure-Cloud. Benutzer können aber auch auf die Daten und Ressourcen zugreifen, die im lokalen Netzwerk gespeichert sind. Benutzer können sich mit den Anmeldeinformationen des Unternehmens, die mit Azure Active Directory verbunden sind oder synchronisiert werden, anmelden und auf die Apps zugreifen.

###Cloud-Sammlung

Die [Cloud-Sammlung von RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-cloud-deployment/) bietet eine eigenständige Möglichkeit zum Hosten von Anwendungen in der Cloud. Eine Cloud-Sammlung ist nur in der Azure-Cloud vorhanden und nicht mit dem lokalen Netzwerk verbunden.

Im Rahmen der RemoteApp-Testversion stellen wir Ihnen die vorinstallierten Office 365 ProPlus- oder Office 2013-Apps bereit, die Sie sofort für Ihre Benutzer freigeben können. Wenn Sie sich dafür entscheiden, die verfügbare Software mit einzubinden, können Sie Ihren Dienst schnell bereitstellen.

Ein zusätzlicher Vorteil der Cloud-Sammlung mit den Office-Apps besteht darin, dass Apps und Betriebssystem (auf dem der Dienst basiert) durch regelmäßige Updates immer auf dem aktuellen Stand gehalten werden und Microsoft Anti-Malware Endpoint Protection kontinuierlichen Schutz bietet. Die Endbenutzer verwenden ihre Microsoft-Konten oder die Anmeldeinformationen des Unternehmens, um auf die Apps zuzugreifen. Sie als Administrator brauchen sich nur noch darum zu kümmern, wer Zugriff auf welche Apps haben soll.

Sie können für Ihre Benutzer auch eine Cloud-Sammlung zum Freigeben einer benutzerdefinierten Anwendung oder einer Gruppe von Anwendungen erstellen. Zu diesem Zweck müssen Sie [ein benutzerdefiniertes Vorlagenimage erstellen](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-custom-image/) (es gibt an, wie Apps in RemoteApp veröffentlicht werden). Wenn Sie Ihre Sammlung erstellen, brauchen Sie nur dieses Image auszuwählen (anstelle des Office 2013-Images). 

###Hybrid-Sammlung
Mit der [Hybrid-Sammlung von RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-hybrid-deployment/) können Sie für Ihre Benutzer sowohl einen benutzerdefinierten Satz von Anwendungen bereitstellen als auch den Zugriff auf die Daten und Ressourcen in Ihrem lokalen Netzwerk ermöglichen. Im Gegensatz zum benutzerdefinierten Image, das bei der Cloud-Sammlung verwendet wird, werden mit dem für eine Hybrid-Sammlung erstellten Image die Apps in einer in die Domäne eingebundenen Umgebung ausgeführt, die den vollständigen Zugriff auf das lokale Netzwerk und die Daten ermöglicht.

Durch die Integration von Active Directory mit Azure Active Directory (mithilfe von "DirSync") können die Benutzer für den Zugriff auf Apps und Daten die Anmeldeinformationen des Unternehmens verwenden. Wenn Sie in Active Directory ein Geschäftskonto verwenden, können Sie die Unternehmensrichtlinien in die Cloud übernehmen und damit das App-Angebot über RemoteApp steuern.

Wenn Sie das Vorlagenimage mit dem Remotedesktop-Sitzungshost-Rollendienst auf Windows Server 2012 R2 erstellen, gibt es hinsichtlich der Apps, die Sie für Ihre Benutzer veröffentlichen können, einige Einschränkungen. Wenn die Anwendungen in der betreffenden Vorlagenimage-Umgebung ordnungsgemäß funktionieren, können die Endbenutzer über RemoteApp darauf zugreifen. 

###Aktualisieren Ihrer Sammlung
Einer der Hauptunterschiede zwischen Hybrid- und Cloud-Sammlungen ist die Behandlung von Softwareupdates. Bei einer Cloud-Sammlung, die das vorinstallierte Image von Office 365 ProPlus oder Office 2013 verwendet, brauchen Sie sich nicht um Updates zu kümmern. Der Dienst verwaltet sich selbst und stellt fortlaufend Updates für Apps und Betriebssystem bereit.

Bei Hybrid-Sammlungen sowie bei Cloud-Sammlungen, die ein benutzerdefiniertes Vorlagenimage verwenden, sind Sie für die Wartung und Pflege von Image und Apps verantwortlich. Bei in die Domäne eingebundenen Images können Sie Updates mithilfe von Tools wie Windows Update, Gruppenrichtlinien oder System Center steuern.

Nach dem Update des benutzerdefinierten Vorlagenimages laden Sie das neue Image in die Azure-Cloud hoch und aktualisieren dann die Sammlung, um das neue Image zu verwenden. (Diese Aufgabe können Sie von der RemoteApp-Schnellstart-Seite oder im Dashboard ausführen.)

##Unterstützte RemoteApp-Clients
Azure RemoteApp wird in RemoteApp-Client-Apps für Windows und Windows RT sowie in Microsoft Remote Desktop-Apps für Mac, IOS und Android unterstützt. Die Benutzer können diese Apps auf ihren Mobilgeräten oder Computern für den Zugriff auf die neuen RemoteApp-Programme verwenden.

##Nächste Schritte
Los! Testen Sie jetzt! Diese Artikel helfen Ihnen beim Einstieg in RemoteApp:

- [Erstellen eines benutzerdefinierten Vorlagenimages für RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-custom-image/)
- [Erstellen einer Cloud-Sammlung von RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-cloud-deployment/)
- [Erstellen einer Hybrid-Sammlung von RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-hybrid-deployment/)
- [Wie funktioniert die Lizenzierung in RemoteApp?](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-licensing/)
- [Bewährte Methoden für die Verwendung von Azure RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-bestpractices/)
- [Azure RemoteApp - Häufig gestellte Fragen](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-faq/)

<!--HONumber=35.2-->
