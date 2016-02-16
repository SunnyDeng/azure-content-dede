<properties 
	pageTitle="Häufig gestellte Fragen zu RemoteApp | Microsoft Azure" 
	description="Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure RemoteApp." 
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
	ms.date="02/02/2016" 
	ms.author="elizapo"/>

# Azure RemoteApp – Häufig gestellte Fragen
Folgende Fragen wurden zu Azure RemoteApp an uns gestellt. Haben Sie weitere Fragen? Besuchen Sie die [RemoteApp-Foren](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp), und teilen Sie uns mit, was Sie wissen müssen, oder hinterlassen Sie uns unten einen Kommentar.

## Was ist Azure RemoteApp? ##


- **Was ist Azure RemoteApp?** RemoteApp ist ein Azure-Dienst, mit dessen Hilfe Sie für einen sicheren Remotezugriff auf Anwendungen von vielen unterschiedlichen Benutzergeräten aus sorgen können. Erfahren Sie mehr über [Azure RemoteApp](remoteapp-whatis.md).
- **Welche Bereitstellungsoptionen gibt es?** Es gibt zwei Arten von RemoteApp-Sammlungen: Cloud und Hybrid. Welche der beiden Sie benötigen, hängt von einer Reihe von Faktoren ab, z. B. davon, ob Sie einen Domänenbeitritt benötigen. Wir diskutieren diese Entscheidungen [hier](remoteapp-collections.md).

## Tipps zum Verwenden von Azure RemoteApp ##
- **Wie lange kann ich inaktiv sein, bis ich getrennt werde? Wie viel Zeit habe ich bis zur automatischen Abmeldung?** 4 Stunden. Wenn Sie oder einer Ihrer Benutzer 4 Stunden lang inaktiv ist, werden Sie automatisch von Azure RemoteApp abgemeldet. Alle weiteren Standardeinstellungen werden unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md) näher besprochen.
- **Kann ich diesen Dienst kostenlos testen?** Ja. Es ist eine kostenlose Testversion für 30 Tage verfügbar. Nach Ablauf des Testzeitraums können Sie auf ein gebührenpflichtiges Konto (für die Produktionsumgebung) umstellen oder den Dienst beenden. Starten Sie die kostenlose Testversion unter [portal.azure.com](http://portal.azure.com), und erstellen Sie eine neue Instanz von RemoteApp. Mit der kostenlosen Testversion können Sie zwei Instanzen von RemoteApp mit 10 Benutzern pro Instanz erstellen. Beachten Sie, dass diese Testversion nur für 30 Tage gilt.
## Informationen zu Azure RemoteApp-Abonnements ##

- **Was sind die Dienstgrenzwerte?** Informationen zu den Standardeinstellungen und Dienstbeschränkungen für Azure RemoteApp finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](azure-subscription-service-limits.md). Sie können sich gern an uns wenden, wenn Sie weitere Fragen haben.
- **Wie viele Benutzer benötige ich?** Es sind mindestens 20 Benutzer erforderlich. Ich wiederhole dies noch einmal, damit es klar verständlich ist: das MINIMUM beträgt 20. Ihnen werden 20 Benutzer berechnet. 
- **Wie hoch sind die Kosten für RemoteApp?** Beachten Sie die [Preisdetails für Azure RemoteApp](../../../pricing/details/remoteapp/).
- **Sind bestimmte Sammlungstypen teurer als andere?** Ja, dies kann abhängig von Ihren Anforderungen an Sammlungen der Fall sein. Für eine Hybridsammlung ist eine Verbindung von Azure RemoteApp zu Ihrem lokalen Netzwerk erforderlich. Wenn Sie ein vorhandenes VNET/ExpressRoute verwenden, fallen keine zusätzlichen Kosten an. Wenn Sie jedoch ein neues Azure-VNET und ein Gateway oder ExpressRoute verwenden, fallen für das [VPN-Gateway](../../../pricing/details/vpn-gateway) oder [ExpressRoute](../../../pricing/details/expressroute/) Kosten an. Diese Kosten (siehe Links) werden zusätzlich zu Ihren monatlichen Kosten für Azure RemoteApp berechnet.

## Sammlungen: Was wird unterstützt, welche sollten Sie verwenden usw.
- **Werden benutzerdefinierte Line-of-Business-Anwendungen (LOB) unterstützt?** Ja. Zum Verwenden einer benutzerdefinierten Anwendung in Azure RemoteApp müssen Sie ein [benutzerdefiniertes Vorlagenimage](remoteapp-create-custom-image.md) erstellen und anschließend in die RemoteApp-Sammlung hochladen.
- **Kann ich meine benutzerdefinierte LOB-Anwendung in Azure RemoteApp ausführen?** Dies lässt sich am besten im Rahmen eines Tests ermitteln. Überprüfen Sie die [Anforderungen an die Anwendungskompatibilität](http://www.microsoft.com/download/details.aspx?id=18704), und informieren Sie sich im [RD-Kompatibilitätscenter](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Welche Bereitstellungsmethode (Cloud oder Hybrid) eignet sich am besten für mein Unternehmen?** Hybridsammlungen sind die umfassendste Lösung, wenn Sie eine vollständige Integration mit einmaligem Anmelden und einer sicheren lokalen Netzwerkverbindung anstreben. Cloudsammlungen sind eine flexible und einfache Möglichkeit, Ihre Bereitstellung mithilfe mehrerer Authentifizierungsmethoden zu isolieren. Erfahren Sie mehr über die [Bereitstellungsoptionen](remoteapp-whatis.md).
- **Wir haben eine SQL- oder andere Datenbank entweder lokal oder in Azure. Welchen Bereitstellungstyp sollten wir verwenden?** Das hängt davon ab, wo sich Ihre SQL- oder Back-End-Datenbank befindet. Befindet sich die Datenbank in einem privaten Netzwerk, sollten Sie die Hybridsammlung verwenden. Wenn die Datenbank über das Internet verfügbar ist und Clientverbindungen zulässt, können Sie die Cloudsammlung verwenden.
- **Wie sieht es mit der Laufwerkzuordnung, USB und seriellen Ports, der Freigabe der Zwischenablage und der Druckerumleitung aus?** Alle diese Funktionen werden in Azure RemoteApp unterstützt. Die Freigabe der Zwischenablage und die Druckerumleitung sind standardmäßig aktiviert. [Hier](remoteapp-redirection.md) erfahren Sie mehr über die Umleitung. 


## Vorlagenimages
- **Kann ich eine Cloud oder die vorhandenen virtuellen Computer als Vorlage für meine RemoteApp-Sammlung verwenden?** Ja. Sie können beruhend auf einem virtuellen Azure-Computer ein Image erstellen, eines der im Abonnement enthaltenen Images verwenden oder ein benutzerdefiniertes Image erstellen. Beachten Sie die [RemoteApp-Image-Optionen](remoteapp-imageoptions.md).


## Netzwerkoptionen
- **Für die Hybrid-Sammlung ist ein virtuelles Netzwerk (VNET) erforderlich. Können wir unser vorhandenes VNET verwenden?** Das ist möglich, wenn das vorhandene VNET ein Azure VNET ist. Weitere Informationen finden Sie unter "Schritt 1: Einrichten des virtuellen Netzwerks" in den [Anweisungen zu Hybridsammlungen](remoteapp-create-hybrid-deployment.md).
- **Kann ich ein VNET mit einer Cloudsammlung verwenden?** Dies ist möglich. Unter [Erstellen einer Cloudsammlung](remoteapp-create-cloud-deployment.md), besonders Schritt 1, finden Sie weitere Informationen.

## Authentifizierungsoptionen



- **Wie sieht es mit der Authentifizierung aus? Welche Methoden werden unterstützt?** Die Cloudsammlung unterstützt Microsoft-Konten und Azure Active Directory-Konten, die gleichzeitig auch Office 365-Konten sind. Die Hybrid-Sammlung unterstützt nur Azure Active Directory-Konten, die von einer Windows Server Active Directory-Bereitstellung synchronisiert wurden (mithilfe eines Tools wie z. B. [Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)). Dies gilt insbesondere für Konten, die entweder mit der Option "Kennwortsynchronisierung" oder mit Active Directory Federation Services (AD FS) in einer Verbundkonfiguration synchronisiert wurden. Sie können auch die [Multi-Factor Authentication (MFA)](../../services/multi-factor-authentication/) konfigurieren.

>[AZURE.NOTE]Azure Active Directory-Benutzer müssen dem Ihrem Abonnement zugeordneten Mandanten angehören. (Sie können Ihr Abonnement im Portal auf der Registerkarte **Einstellungen** anzeigen und bearbeiten. Weitere Informationen finden Sie unter [Ändern des von RemoteApp verwendeten Azure Active Directory-Mandanten](remoteapp-changetenant.md).)

- **Warum kann ich meinem Azure Active Directory-Konto keinen Zugriff erteilen?** Azure Active Directory-Benutzer müssen dem Ihrem Abonnement zugeordneten Verzeichnis angehören. Sie können Ihr Abonnement im Portal auf der Registerkarte "Einstellungen" anzeigen und bearbeiten. Weitere Informationen finden Sie unter [Ändern des von RemoteApp verwendeten Azure Active Directory-Verzeichnisses](remoteapp-changetenant.md).

## Clients: Mit welchen Geräten kann ich auf Azure RemoteApp zugreifen?
Unter [Zugreifen auf Anwendungen in Azure RemoteApp](remoteapp-clients.md) finden Sie Informationen zu Clients sowie Schritte zum Installieren der verschiedenen Clients.

- **Welche Geräte und Betriebssysteme werden von den Clientanwendungen unterstützt?** Zunächst Computer und Tablets: 
	- Windows 10 (Clientvorschau)
	- Windows 8.1 und Windows 8
	- Windows 7 Service Pack 1
	- Mac OS X
	- Windows RT
	- Android-Tablets
	- iPads und Smartphones:
	- iPhone
	- Android-Smartphone
	- Windows Phone
 
	Laden Sie den RemoteApp-Client jetzt [herunter](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx).
- **Unterstützt Azure RemoteApp auch Thin Clients?** Ja, die folgenden Windows Embedded-Thin Clients werden unterstützt:
	- Windows Embedded Standard 7
	- Windows Embedded 8 Standard
	- Windows Embedded 8.1 Industry Pro
	- Windows 10 IoT Enterprise

- **Welche Version von Windows Server wird für den Remotedesktop-Sitzungshost (RDSH) unterstützt?** Windows Server 2012 R2

##Support und Feedback


- **Wie lautet der Support-Plan für RemoteApp?** Der Support für die Abrechnungs- und Abonnementverwaltung wird kostenlos bereitgestellt. Der technische Support ist über die [Azure-Servicepläne](../../../support/plans/) verfügbar. Über unser [Azure-Diskussionsforum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp) erhalten Sie zudem eine kostenlose Unterstützung durch die Community. 
- **Wie übermittle ich Feedback?** Besuchen Sie das [Feedbackforum](https://feedback.azure.com/forums/247748-azure-remoteapp/).
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

	Videos! Wir haben auch eine Reihe von Videos zu RemoteApp. Einige enthalten eine Einführung ([Einführung in Azure RemoteApp](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)), während andere die Bereitstellung erläutern ([Cloud-Bereitstellung](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) und [Hybrid-Bereitstellung](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Schauen Sie sie an!

 
### Helfen Sie uns, Ihnen zu helfen 
Wussten Sie schon, dass Sie diesen Artikel im Bereich unten nicht nur bewerten und kommentieren, sondern ihn auch selbst ändern können? Fehlt etwas? Ist etwas nicht ganz richtig? Habe ich etwas geschrieben, das eher verwirrend ist? Scrollen Sie nach oben, und klicken Sie auf **Edit on GitHub**, um die gewünschten Änderungen vorzunehmen. Ihr Vorschlag wird uns vorgelegt, und wenn wir ihn bestätigt haben, werden Ihre Änderungen und Verbesserungen hier angezeigt.

<!---HONumber=AcomDC_0211_2016-->