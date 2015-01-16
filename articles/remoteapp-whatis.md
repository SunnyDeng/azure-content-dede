<properties title="What is RemoteApp?" pageTitle="Was ist RemoteApp?" description="Erfahren Sie mehr über Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

#Was ist Azure RemoteApp?

Mit Azure RemoteApp halten die Funktionen des lokalen Microsoft-RemoteApp-Programms, gestützt von Remotedesktopdiensten, Einzug in Azure. Mit Azure RemoteApp können Sie sicheren Remotezugriff auf Anwendungen von vielen verschiedenen Benutzergeräten bieten.

Durch das Ergänzen von Azure mit RemoteApp können Sie den Speicher, die Skalierbarkeit und die globale Reichweite von Azure nutzen, ohne eine komplexe lokale Konfiguration zu benötigen. Microsoft bietet Wartung für Azure, sodass für Zuverlässigkeit gesorgt ist und Sie sich auf wichtigere Angelegenheiten, wie das Erstellen der besten Apps für Ihr Unternehmen, konzentrieren können. Ein weiterer Vorteil von Azure RemoteApp ist der Zugriff - die Benutzer können auf RemoteApp-Programme von Windows-, iOS-, Mac OS X- und Android-Geräten aus zugreifen. Sie können Ihre Apps in der von ihnen bevorzugten Umgebung nutzen, und Sie können diese Apps über das Azure-Verwaltungsportal verwalten. 

Nachfolgend erhalten Sie weitere Informationen zu RemoteApp, und falls wir Sie schon überzeugt haben, können Sie [es ausprobieren](http://azure.microsoft.com/de-de/services/remoteapp/).

Azure RemoteApp ist Teil der [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/de-de/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Neu!** Sie möchten mehr über Azure RemoteApp erfahren? Oder RemoteApp individuell testen? Nehmen Sie an unserem wöchentlichen [Webinar mit Expertenantworten](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html) teil.

##Optionen für die Bereitstellung von RemoteApp
Es existieren zwei Bereitstellungsarten für RemoteApps:


- Eine **Cloud-Bereitstellung** ist in der Cloud gehostet und speichert alle Daten für Programme in der Azure-Cloud. Benutzer können auf Apps zugreifen, indem sie sich mit ihrem Microsoft-Konto oder mit ihren mit Azure Active Directory synchronisierten oder verbundenen Unternehmensanmeldedaten anmelden.
- Eine **Hybridbereitstellung** ist in der Cloud gehostet und speichert Daten in der Azure-Cloud, sie lässt Benutzer aber auch auf in Ihrem lokalen Netzwerk gespeicherte Daten und Ressourcen zugreifen. Benutzer können auf Apps zugreifen, indem sie sich mit ihren mit Azure Active Directory synchronisierten oder verbundenen Unternehmensanmeldedaten anmelden.

###Cloud-Bereitstellung

Die [Cloud-RemoteApp-Bereitstellung](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-cloud-deployment/) bietet eine eigenständige Möglichkeit zum Hosten von Anwendungen in der Cloud. Eine Cloud-Bereitstellung ist nur in der Azure-Cloud vorhanden und wird nicht mit Ihrem lokalen Netzwerk verbunden.

Als Teil der RemoteApp-Vorschau, bieten wir Ihnen vorinstallierte Office 2013-Apps, die Sie direkt an Ihre Benutzer weitergeben können. Wenn Sie die verfügbare Software nutzen möchten, können Sie Ihren Dienst schnell bereitstellen.

Ein zusätzlicher Vorteil der Verwendung der Cloud-Bereitstellung mit den Office 2013-Apps besteht darin, dass die Apps und das Betriebssystem (auf dem Ihr Dienst beruht) dank regelmäßiger Updates stets auf dem neuesten Stand gehalten werden. Zudem sorgt Microsoft Anti-Malware Endpoint Protection für kontinuierlichen Schutz. Ihre Endbenutzer können über ihre Microsoft-Konten oder mit Unternehmensanmeldedaten auf die Apps zugreifen. Sie als Administrator müssen sich lediglich darum Gedanken machen, wer auf welche Apps zugreifen soll.

Sie können auch eine Cloud-Bereitstellung erstellen, um eine benutzerdefinierte Anwendung oder eine Gruppe von Anwendungen für Ihre Benutzer freizugeben. Dafür müssen Sie [ein benutzerdefiniertes Vorlagenimage erstellen](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-custom-image/) (auf diese Weise werden Apps in RemoteApp veröffentlicht) und einfach dieses Image auswählen (anstatt des Office 2013-Image), wenn Sie Ihre Bereitstellung erstellen. 

###Hybridbereitstellung
Über die [hybride RemoteApp-Bereitstellung](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-hybrid-deployment/) können Sie Ihren Benutzern eine benutzerdefinierte Gruppe von Anwendungen und Zugriff auf Daten und Ressourcen in Ihrem lokalen Netzwerk bieten. Anders als ein benutzerdefiniertes Image, das mit der Cloud-Bereitstellung verwendet wird, führt das für eine Hybridbereitstellung erstellte Image Apps in einer mit einer Domäne verbundenen Umgebung mit vollem Zugriff auf lokales Netzwerk und Daten aus.

Durch die Integration von Active Directory in Azure Active Directory (mithilfe von DirSync), können die Benutzer mit ihren Unternehmensanmeldeinformationen auf Apps und Daten zugreifen. Wenn Sie ein Arbeitskonto in Active Directory verwenden, können Sie Ihre Unternehmensrichtlinien auf die Cloud übertragen, um die über RemoteApp angebotenen Apps zu steuern.

Solange Sie das Vorlagenimage mit dem RD-Sitzungshost-Rollendienst auf Windows Server 2012 R2 erstellen, sind den Apps, die Sie für Ihr Benutzer freigeben können, nur wenige Grenzen gesetzt. Wenn die Apps in dieser Vorlagenimage-Umgebung korrekt funktionieren, können Ihre Endbenutzer über RemoteApp auf sie zugreifen. 

###Aktualisieren der Bereitstellung
Einer der Hauptunterschiede zwischen der Hybridbereitstellung und der Cloud-Bereitstellung besteht im Umgang mit Softwareupdates. Bei Cloud-Bereitstellungen, die das vorinstallierte Office 2013-Image verwenden, müssen Sie sich nicht um Updates sorgen. Der Dienst wartet sich selbst und aktualisiert App und Betriebssystem regelmäßig.

Bei Hybridbereitstellungen und Cloud-Bereitstellungen mit benutzerdefiniertem Vorlagenimage müssen Sie Image und Apps warten. Bei mit einer Domäne verbundenen Images können Sie Updates über Tools wie Windows Update, Group Policy und System Center steuern.

Nach der Aktualisierung des benutzerdefinierten Vorlagenimage laden Sie das neue Image in die Azure-Cloud hoch und aktualisieren dann die Bereitstellung, um das neue Image zu verwenden. (Das können Sie über die Seite "Schnellstart" in RemoteApp oder das Dashboard tun.)

##Unterstützte RemoteApp-Clients
Als Teil der Vorschauversion von Azure RemoteApp haben wir eine neue Microsoft-RemoteApp-Client-App für Windows und Windows RT sowie Updates für die Microsoft-Remotedesktop-Apps für iOS und Android veröffentlicht. Die Benutzer können diese Apps auf ihren mobilen Geräten oder Computern für den Zugriff auf die neuen RemoteApp-Programme verwenden.

##Nächste Schritte
Los! Probieren Sie es aus! Diese Artikel helfen Ihnen beim Einstieg in RemoteApp:

- [Erstellen eines benutzerdefinierten Vorlagenimage für RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-custom-image/)
- [Erstellen einer Cloud-Bereitstellung einer RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-cloud-deployment/)
- [Erstellen einer Hybridbereitstellung von RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-hybrid-deployment/)
