<properties 
	pageTitle="Azure RemoteApp – Häufig gestellte Fragen" 
	description="Häufig gestellte Fragen zu Azure RemoteApp" 
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
	ms.date="04/28/2015" 
	ms.author="elizapo"/>

# Azure RemoteApp – Häufig gestellte Fragen
Folgende Fragen wurden zu Azure RemoteApp an uns gestellt. Haben Sie weitere Fragen? Besuchen Sie die [RemoteApp-Foren](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp), und teilen Sie uns mit, was Sie wissen müssen.

## Was ist Azure RemoteApp? ##


- **Was ist Azure RemoteApp?** Azure RemoteApp stattet Azure mit der Funktionalität des lokalen Microsoft RemoteApp-Programms aus und wird von den Remotedesktopdiensten unterstützt. RemoteApp sorgt für den sicheren Remotezugriff auf Anwendungen von vielen unterschiedlichen Benutzergeräten aus. Erfahren Sie mehr über [Azure RemoteApp](remoteapp-whatis.md).
- **Welches sind die beiden Bereitstellungsoptionen?** Es gibt zwei Arten von RemoteApp-Bereitstellungen (oder -Sammlungen): Cloud und Hybrid. Ermitteln Sie, welche [Bereitstellungsoption](remoteapp-whatis.md) sich für Ihr Unternehmen am besten eignet.

## Unterstützte Konfigurationen ##


- **Werden benutzerdefinierte Line-of-Business-Anwendungen (LOB) unterstützt?** Ja. Zum Verwenden einer benutzerdefinierten Anwendung in Azure RemoteApp müssen Sie ein [benutzerdefiniertes Vorlagenimage](remoteapp-create-custom-image.md) erstellen und anschließend in die RemoteApp-Sammlung hochladen.
- **Kann ich meine benutzerdefinierte LOB-Anwendung in Azure RemoteApp ausführen?** Dies lässt sich am besten im Rahmen eines Tests ermitteln. Überprüfen Sie die [Anforderungen an die Anwendungskompatibilität](http://www.microsoft.com/download/details.aspx?id=18704), und informieren Sie sich im [RD-Kompatibilitätscenter](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Welche Bereitstellungsmethode (Cloud oder Hybrid) eignet sich am besten für mein Unternehmen?** Hybrid-Sammlungen sind die umfassendste Lösung, wenn Sie eine vollständige Integration mit einmaligem Anmelden und einer sicheren lokalen Netzwerkverbindung anstreben. Cloud-Sammlungen sind eine flexible und einfache Möglichkeit, Ihre Bereitstellung mithilfe mehrerer Authentifizierungsmethoden zu isolieren. Erfahren Sie mehr über die [Bereitstellungsoptionen](remoteapp-whatis.md).
- **Für die Hybrid-Sammlung ist ein virtuelles Netzwerk (VNET) erforderlich. Können wir unser vorhandenes Netzwerk verwenden?** Derzeit noch nicht, aber wir wissen, dass das wünschenswert ist. Wir arbeiten daran. In der Zwischenzeit können Sie Ihr vorhandenes VNET mit dem Azure RemoteApp-VNET verbinden, indem Sie [diese Anweisungen](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx) ausführen.
- **Kann ich eine Cloud oder die vorhandenen virtuellen Computer als Vorlage für meine RemoteApp-Sammlung verwenden?** Ja. Sie können beruhend auf einem virtuellen Azure-Computer ein Image erstellen, eines der im Abonnement enthaltenen Images verwenden oder ein benutzerdefiniertes Image erstellen. Beachten Sie die [RemoteApp-Image-Optionen](remoteapp-imageoptions.md).
- **Wir haben eine SQL- oder andere Datenbank entweder lokal oder in Azure. Welchen Bereitstellungstyp sollten wir verwenden?** Das hängt davon ab, wo sich Ihre SQL- oder Back-End-Datenbank befindet. Befindet sich die Datenbank in einem privaten Netzwerk, sollten Sie die Hybrid-Sammlung verwenden. Wenn die Datenbank über das Internet verfügbar ist und Clientverbindungen zulässt, können Sie die Cloud-Sammlung verwenden.
- **Wie sieht es mit der Laufwerkzuordnung, USB und seriellen Ports, der Freigabe der Zwischenablage und der Druckerumleitung aus?** Alle diese Funktionen werden in Azure RemoteApp unterstützt. Die Freigabe der Zwischenablage und die Druckerumleitung sind standardmäßig aktiviert. [Hier](remoteapp-redirection.md) erfahren Sie mehr über die Umleitung. 


- **Wie sieht es mit der Authentifizierung aus? Welche Methoden werden unterstützt?** Die Cloud-Sammlung unterstützt Microsoft-Konten und Azure Active Directory-Konten, die gleichzeitig auch Office 365-Konten sind. Die Hybrid-Sammlung unterstützt nur Azure Active Directory-Konten, die von einer Windows Server Active Directory-Bereitstellung synchronisiert wurden (mithilfe eines Tools wie z. B. [Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)). Dies gilt insbesondere für Konten, die entweder mit der Option "Kennwortsynchronisierung" oder mit Active Directory Federation Services (AD FS) in einer Verbundkonfiguration synchronisiert wurden. Sie können auch die [Multi-Factor Authentication (MFA)](../../services/multi-factor-authentication/) konfigurieren.

	**Hinweis:** Azure Active Directory-Benutzer müssen dem Ihrem Abonnement zugeordneten Mandanten angehören. (Sie können Ihr Abonnement im Portal auf der Registerkarte **Einstellungen** anzeigen und bearbeiten. Weitere Informationen finden Sie unter [Ändern des von RemoteApp verwendeten Azure Active Directory-Mandanten](remoteapp-changetenant.md).)

- **Warum kann ich meinem Azure Active Directory-Konto keinen Zugriff erteilen?** Azure Active Directory-Benutzer müssen dem Ihrem Abonnement zugeordneten Verzeichnis angehören. Sie können Ihr Abonnement im Portal auf der Registerkarte "Einstellungen" anzeigen und bearbeiten. Weitere Informationen finden Sie unter [Ändern des von RemoteApp verwendeten Azure Active Directory-Verzeichnisses](remoteapp-changetenant.md).
- **Welche Geräte und Betriebssysteme werden von den Clientanwendungen unterstützt?** Client-Anwendungen sind verfügbar für Windows 8.1, Windows 8, Windows 7 Service Pack 1, iOS, Mac OS X, Windows RT, Android-Geräte und Windows Phone. Wir unterstützen auch die Windows 10-Vorschau.
 
	Laden Sie den RemoteApp-Client jetzt [herunter](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx).
- **Unterstützt Azure RemoteApp auch Thin Clients?** Ja, die folgenden Windows Embedded-Thin Clients werden unterstützt:
	- Windows Embedded Standard 7 mit Servicepack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **Welche Version von Windows Server wird für den Remotedesktop-Sitzungshost (RDSH) unterstützt?** Windows Server 2012 R2

## Support und Feedback

- **Kann ich diesen Dienst kostenlos testen?** Ja. Es ist eine kostenlose Testversion für 30 Tage verfügbar. Nach Ablauf des Testzeitraums können Sie auf ein gebührenpflichtiges Konto (für die Produktionsumgebung) umstellen oder den Dienst beenden. Starten Sie die kostenlose Testversion unter [manage.windowsazure.com](http://manage.windowsazure.com), und erstellen Sie eine neue Instanz von RemoteApp. Mit der kostenlosen Testversion können Sie zwei Instanzen von RemoteApp mit 10 Benutzern pro Instanz erstellen. Beachten Sie, dass diese Testversion nur für 30 Tage gilt.
- **Wie lautet der Support-Plan für RemoteApp?** Der Support für die Abrechnungs- und Abonnementverwaltung wird kostenlos bereitgestellt. Der technische Support ist über die [Azure-Servicepläne](../../../support/plans/) verfügbar. Über unser [Azure-Diskussionsforum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp) erhalten Sie zudem eine kostenlose Unterstützung durch die Community. 
- **Wie hoch sind die Kosten für RemoteApp?** Beachten Sie die [Preisdetails für Azure RemoteApp](../../../pricing/details/remoteapp/).
- **Wie übermittle ich Feedback?** Besuchen Sie das [Feedbackforum](http://feedback.azure.com/forums/247748-azure-remoteapp).
- **Von wem erfahre ich mehr über Azure RemoteApp?** Neben unserem [Diskussionsforum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), das ein idealer Ausgangspunkt für Fragestellungen ist, können Sie am wöchentlichen [Webinar "Ask the Experts"](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html) teilnehmen, bei dem alle Themen bezüglich RemoteApp behandelt werden.
- **Gibt es eine RemoteApp-Dokumentation?** Gut, dass Sie danach fragen. Neben den Inhalten im Portal-Hilfefach (klicken Sie einfach auf einer beliebigen Seite im Portal auf das Fragezeichen **?**) sind die folgenden Artikel verfügbar, in denen Sie alles über RemoteApp erfahren:
	- **Erste Schritte:**
		- [Was ist RemoteApp?](remoteapp-whatis.md)
		- [Was enthalten RemoteApp-Vorlagenimages?](remoteapp-images.md)
		- [Wie funktioniert die Lizenzierung?](remoteapp-licensing.md)
		- [Wie arbeiten RemoteApp und Office zusammen?](remoteapp-o365.md)
		- [Wie funktioniert die Umleitung in RemoteApp](remoteapp-redirection.md)?
	- **Bereitstellen:**
		- [Erstellen eines benutzerdefinierten Vorlagenimages](remoteapp-create-custom-image.md)
		- [Erstellen einer Hybrid-Sammlung](remoteapp-create-hybrid-deployment.md)
		- [Erstellen einer Cloud-Sammlung](remoteapp-create-cloud-deployment.md)
		- [Konfigurieren von Azure Active Directory für RemoteApp](remoteapp-ad.md)
		- [Veröffentlichen einer App in RemoteApp](remoteapp-publish.md)
	- **Verwalten:**
		- [Hinzufügen von Benutzern](remoteapp-user.md)
		- [Bewährte Methoden zum Konfigurieren und Verwenden von Azure RemoteApp](remoteapp-bestpractices.md)	

	Videos! Wir haben auch eine Reihe von Videos zu RemoteApp. Einige enthalten eine Einführung ([Einführung in Azure RemoteApp](http://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)), während andere die Bereitstellung erläutern ([Cloud-Bereitstellung](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) und [Hybrid-Bereitstellung](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Schauen Sie sie an!


<!--HONumber=54--> 