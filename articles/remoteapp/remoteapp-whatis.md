<properties 
	pageTitle="Was ist Azure RemoteApp? | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Apps und Ressourcen über Azure RemoteApp auf beliebigen Geräten nutzen können." 
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
	ms.topic="get-started-article" 
	ms.date="02/19/2016" 
	ms.author="elizapo"/>

# Was ist Azure RemoteApp?

Azure RemoteApp stattet Azure mit der Funktionalität des lokalen Microsoft RemoteApp-Programms, unterstützt von Remotedesktopdiensten, aus. Azure RemoteApp sorgt für den sicheren Remotezugriff auf Anwendungen von vielen unterschiedlichen Benutzergeräten aus. Im Wesentlichen hostet Azure RemoteApp nicht persistente Terminal Server-Sitzungen in der Cloud, die Sie verwenden und an Ihre Benutzer freigeben können.

Mit Azure RemoteApp können Sie Apps und Ressourcen auf fast jedem Gerät für Benutzer freigeben. Wir hosten Ihre Apps in der Cloud, was bedeutet, dass wir uns um Hardware und Skalierung kümmern, um die Anforderungen der Benutzer zu erfüllen. Sie müssen lediglich die Apps, die Sie freigeben möchten, hochladen, und Ihre Benutzer dann dazu veranlassen, sie zu verwenden. [Benutzer behalten ihre eigenen Geräte](remoteapp-clients.md), während Sie alles über das Azure-Portal verwalten. Sie haben sogar die Möglichkeit, die Anmeldeinformationen Ihres Unternehmens zu verwenden, sodass Sie die Sicherheit von Apps und Daten gewährleisten.

Nachfolgend erhalten Sie weitere Informationen zu Azure RemoteApp. Falls wir Sie schon überzeugt haben, können Sie den Dienst [jetzt ausprobieren](https://azure.microsoft.com/services/remoteapp/).

Haben Sie Fragen zu Azure RemoteApp? Schlagen Sie unter den [häufig gestellten Fragen](remoteapp-faq.md) nach.

Azure RemoteApp ist Teil der [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Neu!** Sie möchten mehr über Azure RemoteApp erfahren? Oder Azure RemoteApp individuell testen? Nehmen Sie an unserem wöchentlichen [Webinar mit Expertenantworten](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website) teil.

## Azure RemoteApp-Sammlungen
Es gibt zwei Arten von [Azure RemoteApp-Sammlungen](remoteapp-collections.md):


- Eine **Cloudsammlung** wird in der Cloud gehostet und speichert dort Daten für Programme. Benutzer können sich mit ihrem Microsoft-Konto oder mit den Anmeldeinformationen des Unternehmens, die mit Azure Active Directory verbunden sind oder synchronisiert werden, anmelden und auf die Apps zugreifen.

	Verwenden Sie eine Cloud-Sammlung, wenn die Anwendung, die Sie freigeben möchten, keine Verbindung mit einer Ressource im privaten Netzwerk Ihres Unternehmens (beispielsweise über ein VPN-Gerät) erfordert. Eine Cloud-Sammlung ist geeignet, wenn die Anwendung Ressourcen wie das Internet, OneDrive oder Azure verwendet. Darüber hinaus lässt sie sich am schnellsten erstellen.

- Eine **Hybrid-Sammlung** wird in der Azure-Cloud gehostet und speichert dort die Daten. Benutzer können aber auch auf die Daten und Ressourcen zugreifen, die im lokalen Netzwerk gespeichert sind. Benutzer können sich mit den Anmeldeinformationen des Unternehmens, die mit Azure Active Directory verbunden sind oder synchronisiert werden, anmelden und auf die Apps zugreifen.

	Wählen Sie eine Hybrid-Sammlung, wenn Sie eine Verbindung mit Ressourcen im privaten Netzwerk Ihres Unternehmens benötigen. Dies ist beispielsweise der Fall, wenn die Anwendung Zugriff Folgendes benötigt:

	- Dateiserver in Ihrem Intranet
	- Quicken
	- Datenbanken hinter einer Firewall

	Hybrid eignet sich in der Regel eher für große Unternehmen, deren private Netzwerke zahlreiche Ressourcen enthalten, die nicht in die Cloud verschoben werden können.

Die verschiedenen Sammlungen verfügen über verschiedene Optionen, einschließlich Netzwerkoptionen, sodass Sie herausfinden können, [welche Sammlung](remoteapp-collections.md) für Sie am Besten funktioniert.


### Aktualisieren Ihrer Sammlung
Einer der Hauptunterschiede zwischen Hybrid- und Cloud-Sammlungen ist die Behandlung von Softwareupdates. Bei einer Cloud-Sammlung, die das vorinstallierte Image von Office 365 ProPlus oder Office 2013 verwendet, brauchen Sie sich nicht um Updates zu kümmern. Der Dienst verwaltet sich selbst und stellt fortlaufend Updates für Apps und Betriebssystem bereit.

Bei Hybrid-Sammlungen sowie bei Cloud-Sammlungen, die ein benutzerdefiniertes Vorlagenimage verwenden, sind Sie für die Wartung und Pflege von Image und Apps verantwortlich. Bei in die Domäne eingebundenen Images können Sie Updates mithilfe von Tools wie Windows Update, Gruppenrichtlinien oder System Center steuern.

Nach dem Update des benutzerdefinierten Vorlagenimages laden Sie das neue Image in die Azure-Cloud hoch und aktualisieren dann die Sammlung, um das neue Image zu verwenden. (Das können Sie über die Seite **Schnellstart** in Azure RemoteApp oder über das Dashboard erledigen.)

Weitere Informationen finden Sie unter [Aktualisieren Ihrer Sammlung](remoteapp-update.md).

## Unterstützte RemoteApp-Clients
Azure RemoteApp wird in RemoteApp-Client-Apps für Windows und Windows RT sowie in Microsoft Remote Desktop-Apps für Mac, IOS und Android unterstützt. Die Benutzer können diese Apps auf ihren Mobilgeräten oder Computern für den Zugriff auf die neuen Azure RemoteApp-Programme verwenden.

Weitere Informationen zu den Clients finden Sie unter [Zugreifen auf Ihre Apps in Azure RemoteApp](remoteapp-clients.md).

## Nächste Schritte
Los! Probieren Sie es aus! Diese Artikel helfen Ihnen beim Einstieg in Azure RemoteApp:

- [Welche Art von Sammlung benötigen Sie für Azure RemoteApp?](remoteapp-collections.md)
- [Erstellen von Azure RemoteApp-Images](remoteapp-imageoptions.md)
- [Erstellen einer Cloudsammlung von Azure RemoteApp](remoteapp-create-cloud-deployment.md)
- [Erstellen einer Hybridsammlung von Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Wie funktioniert die Lizenzierung in Azure RemoteApp?](remoteapp-licensing.md)
- [Bewährte Methoden für die Verwendung von Azure RemoteApp](remoteapp-bestpractices.md)
- [Azure RemoteApp – Häufig gestellte Fragen](remoteapp-faq.md)
 

### Helfen Sie uns, Ihnen zu helfen 
Wussten Sie schon, dass Sie diesen Artikel im Bereich unten nicht nur bewerten und kommentieren, sondern ihn auch selbst ändern können? Fehlt etwas? Ist etwas nicht ganz richtig? Habe ich etwas geschrieben, das eher verwirrend ist? Scrollen Sie nach oben, und klicken Sie auf **Edit on GitHub**, um die gewünschten Änderungen vorzunehmen. Ihr Vorschlag wird uns vorgelegt, und wenn wir ihn bestätigt haben, werden Ihre Änderungen und Verbesserungen hier angezeigt.

<!---HONumber=AcomDC_0302_2016-->