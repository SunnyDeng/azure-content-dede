<properties 
	pageTitle="Was ist RemoteApp?" 
	description="Erfahren Sie mehr über Azure RemoteApp." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="elizapo"/>

# Was ist Azure RemoteApp?

Azure RemoteApp stattet Azure mit der Funktionalität des lokalen Microsoft RemoteApp-Programms, unterstützt von Remotedesktopdiensten, aus. Azure RemoteApp sorgt für den sicheren Remotezugriff auf Anwendungen von vielen unterschiedlichen Benutzergeräten aus.

Wenn Sie RemoteApp für Azure verwenden, können Sie den Speicher, die Skalierbarkeit und die globale Reichweite von Azure nutzen, ohne sich um eine komplexe lokale Konfiguration kümmern zu müssen. Microsoft bietet Wartung für Azure, sodass für Zuverlässigkeit gesorgt ist und Sie sich auf wichtigere Angelegenheiten, wie das Erstellen der besten Apps für Ihr Unternehmen, konzentrieren können. Ein weiterer Vorteil von Azure RemoteApp ist der Zugriff – die Benutzer können auf RemoteApp-Programme von Windows-, iOS-, Mac OS X- und Android-Geräten aus zugreifen. Sie können Ihre Apps in der von ihnen bevorzugten Umgebung nutzen, und Sie können diese Apps über das Azure-Verwaltungsportal verwalten.

Nachfolgend erhalten Sie weitere Informationen zu RemoteApp, und falls wir Sie schon überzeugt haben, können Sie [es ausprobieren](http://azure.microsoft.com/services/remoteapp/).

Haben Sie Fragen zu Azure RemoteApp? Schlagen Sie unter den [häufig gestellten Fragen](remoteapp-faq.md) nach.

Azure RemoteApp ist Teil der [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Neu!** Sie möchten mehr über Azure RemoteApp erfahren? Oder RemoteApp individuell testen? Nehmen Sie an unserem wöchentlichen [Webinar mit Expertenantworten](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website) teil.

## RemoteApp-Sammlungen
Es gibt zwei Arten von RemoteApp-Sammlungen:


- Eine **Cloud-Sammlung** wird in der Azure-Cloud gehostet und speichert dort alle Daten für Programme. Benutzer können sich mit ihrem Microsoft-Konto oder mit den Anmeldeinformationen des Unternehmens, die mit Azure Active Directory verbunden sind oder synchronisiert werden, anmelden und auf die Apps zugreifen.
- Eine **Hybrid-Sammlung** wird in der Azure-Cloud gehostet und speichert dort die Daten. Benutzer können aber auch auf die Daten und Ressourcen zugreifen, die im lokalen Netzwerk gespeichert sind. Benutzer können sich mit den Anmeldeinformationen des Unternehmens, die mit Azure Active Directory verbunden sind oder synchronisiert werden, anmelden und auf die Apps zugreifen.

### Cloud-Sammlung

Die [Cloud-Sammlung von RemoteApp](remoteapp-create-cloud-deployment.md) bietet eine eigenständige Möglichkeit zum Hosten von Anwendungen in der Cloud. Eine Cloud-Sammlung ist nur in der Azure-Cloud vorhanden und nicht mit dem lokalen Netzwerk verbunden.

Im Rahmen der RemoteApp-Testversion stellen wir Ihnen die vorinstallierten Office 365 ProPlus- oder Office 2013-Apps bereit, die Sie sofort für Ihre Benutzer freigeben können. Wenn Sie sich dafür entscheiden, die verfügbare Software mit einzubinden, können Sie Ihren Dienst schnell bereitstellen.

Ein zusätzlicher Vorteil der Cloud-Sammlung mit den Office-Apps besteht darin, dass Apps und Betriebssystem (auf dem der Dienst basiert) durch regelmäßige Updates immer auf dem aktuellen Stand gehalten werden und Microsoft Anti-Malware Endpoint Protection kontinuierlichen Schutz bietet. Ihre Endbenutzer können über ihre Microsoft-Konten oder mit Unternehmensanmeldedaten auf die Apps zugreifen. Sie als Administrator brauchen sich nur noch darum zu kümmern, wer Zugriff auf welche Apps haben soll.

Sie können für Ihre Benutzer auch eine Cloud-Sammlung zum Freigeben einer benutzerdefinierten Anwendung oder einer Gruppe von Anwendungen erstellen. Zu diesem Zweck müssen Sie ein [benutzerdefiniertes Image](remoteapp-imageoptions.md) erstellen (es gibt an, wie Apps in RemoteApp veröffentlicht werden). Wenn Sie Ihre Sammlung erstellen, müssen Sie nur dieses Image auswählen (anstelle des Office 2013-Images).

#### Argumente für die Cloud

Verwenden Sie eine Cloud-Sammlung, wenn die Anwendung, die Sie freigeben möchten, keine Verbindung mit einer Ressource im privaten Netzwerk Ihres Unternehmens (beispielsweise über ein VPN-Gerät) erfordert. Eine Cloud-Sammlung ist geeignet, wenn die Anwendung Ressourcen wie das Internet, OneDrive oder Azure verwendet. Darüber hinaus lässt sie sich am schnellsten erstellen.


### Hybrid-Sammlung
Mit der [Hybrid-Sammlung von RemoteApp](remoteapp-create-hybrid-deployment.md) können Sie für Ihre Benutzer sowohl einen benutzerdefinierten Satz von Anwendungen bereitstellen als auch den Zugriff auf die Daten und Ressourcen in Ihrem lokalen Netzwerk ermöglichen. Im Gegensatz zum benutzerdefinierten Image, das bei der Cloud-Sammlung verwendet wird, werden mit dem für eine Hybrid-Sammlung erstellten Image die Apps in einer in die Domäne eingebundenen Umgebung ausgeführt, die den vollständigen Zugriff auf das lokale Netzwerk und die Daten ermöglicht.

Durch die Integration von Active Directory mit Azure Active Directory (mithilfe von "DirSync") können die Benutzer für den Zugriff auf Apps und Daten die Anmeldeinformationen des Unternehmens verwenden. Wenn Sie in Active Directory ein Geschäftskonto verwenden, können Sie die Unternehmensrichtlinien in die Cloud übernehmen und damit das App-Angebot über RemoteApp steuern.

Wenn Sie das Vorlagenimage mit dem Remotedesktop-Sitzungshost-Rollendienst auf Windows Server 2012 R2 erstellen, gibt es hinsichtlich der Apps, die Sie für Ihre Benutzer veröffentlichen können, einige Einschränkungen. Wenn die Anwendungen in der betreffenden Vorlagenimage-Umgebung ordnungsgemäß funktionieren, können die Endbenutzer über RemoteApp darauf zugreifen.

#### Argumente für Hybrid

Wählen Sie eine Hybrid-Sammlung, wenn Sie eine Verbindung mit Ressourcen im privaten Netzwerk Ihres Unternehmens benötigen. Dies ist beispielsweise der Fall, wenn die Anwendung Zugriff Folgendes benötigt:

- Dateiserver in Ihrem Intranet
- Quicken
- Datenbanken hinter einer Firewall

Hybrid eignet sich in der Regel eher für große Unternehmen, deren private Netzwerke zahlreiche Ressourcen enthalten, die nicht in die Cloud verschoben werden können.

### Aktualisieren Ihrer Sammlung
Einer der Hauptunterschiede zwischen Hybrid- und Cloud-Sammlungen ist die Behandlung von Softwareupdates. Bei einer Cloud-Sammlung, die das vorinstallierte Image von Office 365 ProPlus oder Office 2013 verwendet, brauchen Sie sich nicht um Updates zu kümmern. Der Dienst verwaltet sich selbst und stellt fortlaufend Updates für Apps und Betriebssystem bereit.

Bei Hybrid-Sammlungen sowie bei Cloud-Sammlungen, die ein benutzerdefiniertes Vorlagenimage verwenden, sind Sie für die Wartung und Pflege von Image und Apps verantwortlich. Bei in die Domäne eingebundenen Images können Sie Updates mithilfe von Tools wie Windows Update, Gruppenrichtlinien oder System Center steuern.

Nach dem Update des benutzerdefinierten Vorlagenimages laden Sie das neue Image in die Azure-Cloud hoch und aktualisieren dann die Sammlung, um das neue Image zu verwenden. (Das können Sie über die Seite **Schnellstart** in RemoteApp oder das Dashboard erledigen.)

Weitere Informationen finden Sie unter [Aktualisieren Ihrer Sammlung](remoteapp-update.md).

## Unterstützte RemoteApp-Clients
Azure RemoteApp wird in RemoteApp-Client-Apps für Windows und Windows RT sowie in Microsoft Remote Desktop-Apps für Mac, IOS und Android unterstützt. Die Benutzer können diese Apps auf ihren Mobilgeräten oder Computern für den Zugriff auf die neuen RemoteApp-Programme verwenden.

Weitere Informationen zu den Clients finden Sie unter [Zugreifen auf Ihre Apps in Azure RemoteApp](remoteapp-clients.md).

## Nächste Schritte
Los! Probieren Sie es aus! Diese Artikel helfen Ihnen beim Einstieg in RemoteApp:

- [Erstellen eines RemoteApp-Images](remoteapp-imageoptions.md)
- [Erstellen einer Cloud-Sammlung von RemoteApp](remoteapp-create-cloud-deployment.md)
- [Erstellen einer Hybrid-Sammlung von RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Wie funktioniert die Lizenzierung in RemoteApp?](remoteapp-licensing.md)
- [Bewährte Methoden für die Verwendung von Azure RemoteApp](remoteapp-bestpractices.md)
- [Azure RemoteApp – Häufig gestellte Fragen](remoteapp-faq.md)
 

<!---HONumber=July15_HO4-->