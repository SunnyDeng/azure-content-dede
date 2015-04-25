<properties title="Azure RemoteApp FAQ" pageTitle="Azure RemoteApp - Häufig gestellte Fragen" description="Informationen über Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Azure RemoteApp - Häufig gestellte Fragen
Folgende Fragen wurden zu Azure RemoteApp an uns gestellt. Haben Sie weitere Fragen? Besuchen Sie die [RemoteApp-Foren](https://social.msdn.microsoft.com/Forums/azure/de-de/home?forum=AzureRemoteApp), und teilen Sie uns mit, was Sie wissen müssen.

##Was ist Azure RemoteApp?##


- **Was ist Azure RemoteApp?** RemoteApp ist ein Azure-Dienst, der die Funktionalität der lokalen Microsoft RemoteApp, unterstützt von Remotedesktopdiensten, bereitstellt. Mit RemoteApp stellen Sie von vielen verschiedenen Benutzergeräten einen sicheren Remotezugriff auf Anwendungen bereit. Erfahren Sie mehr über [Azure RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-whatis/).
- **Wie unterscheiden sich die beiden Bereitstellungsarten?** Es gibt zwei RemoteApp-Bereitstellungsarten (bzw. Sammlungen): Cloud und Hybrid. Finden Sie heraus, welche [Bereitstellungsoption](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-whatis/) für Ihre Organisation am besten geeignet ist.

##Unterstützte Konfigurationen##


- **Werden benutzerdefinierte Line-of-Business (LOB)-Anwendungen unterstützt?** Ja. Zum Verwenden einer benutzerdefinierten Anwendung in Azure RemoteApp müssen Sie ein [benutzerdefiniertes Vorlagenimage](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-custom-image/) erstellen und es anschließend in die RemoteApp-Sammlung hochladen.
- **Funktioniert meine benutzerdefinierte LOB-Anwendung in Azure RemoteApp?** Das finden Sie am besten heraus, indem Sie sie testen. Lesen Sie die [Anforderungen an die Anwendungskompatibilität](http://www.microsoft.com/de-de/download/details.aspx?id=18704) durch, und überprüfen Sie das [RD-Kompatibilitätscenter](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Welche Bereitstellungsmethode (Cloud oder Hybrid) ist für mein Unternehmen am besten geeignet?** Hybrid-Sammlungen bieten die umfassendste Funktionalität, wenn Sie eine vollständige Integration mit einmaliger Anmeldung (SSO) und sicherer lokaler Netzwerkkonnektivität anstreben. Cloud-Sammlungen bieten durch die Verwendung mehrerer Authentifizierungsmethoden eine flexible und einfache Möglichkeit, die Bereitstellung zu isolieren. Erfahren Sie mehr über die [Bereitstellungsoptionen](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-whatis/).
- **Für die Hybrid-Sammlung ist ein virtuelles Netzwerk (VNET) erforderlich. Können wir unser vorhandenes Netzwerk verwenden?** Derzeit noch nicht, aber wir wissen, dass das wünschenswert ist. Wir arbeiten daran. In der Zwischenzeit können Sie Ihr vorhandenes VNET mit dem Azure RemoteApp VNET verbinden, indem Sie [diese Anweisungen](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx) ausführen.
- **Kann ich eine Cloud oder einen vorhandenen virtuellen Computer als Vorlage für meine RemoteApp-Sammlung verwenden?** Derzeit noch nicht, aber dieser Wunsch wird häufig auf unserer [Feedback-Website](http://feedback.azure.com/forums/247748-azure-remoteapp/suggestions/5923679-allow-creation-of-custom-hybrid-image-completely-w) geäußert.
- **Wir haben eine SQL- oder andere Datenbank entweder lokal oder in Azure. Welchen Bereitstellungstyp sollten wir verwenden?** Das hängt davon ab, wo sich Ihre SQL- oder Back-End-Datenbank befindet. Befindet sich die Datenbank in einem privaten Netzwerk, sollten Sie die Hybrid-Sammlung verwenden. Wenn die Datenbank über das Internet verfügbar ist und Clientverbindungen zulässt, können Sie die Cloud-Sammlung verwenden.
- **Wie sieht es mit Laufwerkzuordnung, USB und seriellem Anschluss, gemeinsamer Nutzung der Zwischenablage und Druckerumleitung aus?** All diese Funktionen werden in Azure RemoteApp unterstützt. Die gemeinsame Nutzung der Zwischenablage und die Druckerumleitung sind standardmäßig aktiviert. [Nehmen Sie unter ](Mailto:remoteappforum@microsoft.com? Subject = Azure % 20RemoteApp % 20enable % 20Redirection % 20request) Kontakt mit uns auf, um Laufwerkzuordnung, USB-Umleitung oder die Umleitung serieller Anschlüsse zu aktivieren. (Wir arbeiten daran, das Portal mit diesen Funktionen auszustatten; diese Unterstützung ist derzeit jedoch noch nicht verfügbar.)
- **Wie sieht es mit der Authentifizierung aus? Welche Methoden werden unterstützt?** Die Cloud-Sammlung unterstützt Microsoft-Konten und Azure Active Directory-Konten, die gleichzeitig auch Office 365-Konten sind. Die Hybrid-Sammlung unterstützt nur Azure Active Directory-Konten, die von einer Windows Server Active Directory-Bereitstellung synchronisiert wurden (mithilfe eines Tools wie "DirSync"); hier besonders Konten, die entweder mit der Option "Kennwortsynchronisierung" oder mit Active Directory Federation Services (AD FS) in einer Verbundkonfiguration synchronisiert wurden. Sie können auch die [Multi-Factor Authentication (MFA)](http://azure.microsoft.com/de-de/documentation/services/multi-factor-authentication/) konfigurieren.

	**Hinweis:** Azure Active Directory-Benutzer müssen zu dem Mandanten gehören, der Ihrem Abonnement zugeordnet ist. (Ihr Abonnement können Sie im Portal auf der Registerkarte **Einstellungen** anzeigen und ändern. Weitere Informationen hierzu finden Sie unter [Ändern des von RemoteApp verwendeten Azure Active Directory-Mandanten](http://msdn.microsoft.com/de-de/3d6c4fd1-c981-4c57-9402-59fe31b11883).)

- **Warum kann ich mein Azure Active Directory-Konto nicht für den Zugriff verwenden?** Azure Active Directory-Benutzer müssen zu dem Verzeichnis gehören, das Ihrem Abonnement zugeordnet ist. Sie können dieses Verzeichnis im Portal auf der Registerkarte "Einstellungen" anzeigen oder ändern. Weitere Informationen finden Sie unter [Ändern des von RemoteApp verwendeten Azure Active Directory-Mandanten](http://msdn.microsoft.com/de-de/3d6c4fd1-c981-4c57-9402-59fe31b11883).
- **Welche Geräte und Betriebssysteme unterstützen die Clientanwendungen?** Clientanwendungen sind für Windows 8.1, Windows 8, Windows 7 Service Pack 1, iOS, Mac OS X, Windows RT, Android-Geräte und Windows Phone verfügbar. Unterstützt wird auch Windows 10 Preview.
 
	Hier können Sie einen RemoteApp-Client [herunterladen](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx).
- **Unterstützt Azure RemoteApp Thin Clients?** Ja. Unterstützt werden die folgenden Windows Embedded-Thin-Clients:
	- Windows Embedded Standard 7 mit Servicepack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **Welche Version von Windows Server wird für den Remotedesktop-Sitzungshost(RDSH) unterstützt?** Windows Server 2012 R2.

##Support und Feedback

- **Kann ich diesen Dienst kostenlos testen?** Ja. Es gibt eine kostenlose Testversion für 30 Tage. Nach Ablauf des Testzeitraums können Sie zu einem gebührenpflichtigen Konto wechseln (das in der Produktion verwendet werden kann) oder den Dienst beenden. Starten Sie jetzt Ihre kostenlose Testversion auf [manage.windowsazure.com](http://manage.windowsazure.com) - Erstellen Sie eine neuen Instanz von RemoteApp. Mit der kostenlosen Testversion können Sie 2 Instanzen von RemoteApp mit jeweils 10 Benutzern erstellen. Denken Sie daran, dass diese Testversion nur 30 Tage aktiv ist.
- **Welchen Supportplan gibt es für RemoteApp?** Support für Abrechnungs- und Abonnementverwaltung wird kostenlos bereitgestellt. Technischer Support ist über [Azure-Servicepläne](http://azure.microsoft.com/support/plans/) verfügbar. Außerdem erhalten Sie in unserem [Azure-Diskussionsforum](http://social.msdn.microsoft.com/Forums/windowsazure/de-de/home?forum=AzureRemoteApp) kostenlosen Support von der Community.
- **Wie hoch sind die Kosten für RemoteApp?** Schlagen Sie unter [Azure RemoteApp - Preisdetails](http://azure.microsoft.com/de-de/pricing/details/remoteapp/) nach.
- **Wie übermittle ich Feedback?** Besuchen Sie das [Feedback-Forum](http://feedback.azure.com/forums/247748-azure-remoteapp).
- **Mit wem kann sprechen, um weitere Informationen zu Azure RemoteApp zu erhalten?** Neben unserem [Diskussionsforum](http://social.msdn.microsoft.com/Forums/windowsazure/de-de/home?forum=AzureRemoteApp), das ein idealer Ausgangspunkt für Fragestellungen ist, können Sie am wöchentlichen [Ask the Experts Webinar](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html) teilnehmen, bei dem alle Themen bezüglich RemoteApp behandelt werden.
- **Wie sieht es mit der RemoteApp-Dokumentation aus?** Gut, dass Sie danach fragen. Neben den Inhalten im Portal-Hilfefach (klicken Sie einfach auf einer beliebigen Seite im Portal auf das Fragezeichen **?**) sind die folgenden Artikel verfügbar, in denen Sie alles über RemoteApp erfahren:
	- **Erste Schritte:**
		- [Was ist RemoteApp?](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-whatis/)
		- [Was enthalten RemoteApp-Vorlagenimages?](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-images/)
		- [Wie funktioniert die Lizenzierung?](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-licensing/)
		- [Wie arbeiten RemoteApp und Office zusammen?](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-o365/)
	- **Bereitstellung:**
		- [Erstellen eines benutzerdefinierten Vorlagenimages](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-custom-image/)
		- [Erstellen einer Hybrid-Sammlung](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-hybrid-deployment/)
		- [Erstellen einer Cloud-Sammlung](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-create-cloud-deployment/)
		- [Konfigurieren von Azure Active Directory für RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-ad/)
		- [Veröffentlichen einer App in RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-publish/)
	- **Verwaltung:**
		- [Hinzufügen von Benutzern](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-user/)
		- [Bewährte Methoden zum Konfigurieren und Verwenden von Azure RemoteApp](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-bestpractices/)	

	Videos! Wir haben auch eine Reihe von Videos zu RemoteApp. Einige enthalten eine Einführung ([Einführung in Azure RemoteApp](http://azure.microsoft.com/de-de/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)), während andere die Bereitstellung erläutern ([Cloud-Bereitstellung](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) und [Hybrid-Bereitstellung](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Schauen Sie sie an!


<!--HONumber=35.2-->
