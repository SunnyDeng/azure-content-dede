<properties
	pageTitle="Erstellen von Azure BizTalk Services im Azure-Portal | Microsoft Azure"
	description="Erfahren Sie mehr über das Bereitstellen oder Erstellen von Azure-BizTalk Services im Azure-Portal; MABS, WABS"
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/01/2015"
	ms.author="mandia"/>



# Konfigurieren von BizTalk Services im Azure-Portal

In diesem Artikel wird beschrieben, wie das Erstellen von Azure BizTalk Services im Azure-Portal erfolgt.

> [AZURE.TIP]Um sich beim Azure-Portal anmelden zu können, benötigen Sie ein Azure-Konto und ein Azure-Abonnement. Wenn Sie noch kein Konto haben, können Sie in wenigen Minuten ein kostenloses Testkonto erstellen. Siehe [Kostenlose Azure-Testversion](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## Erstellen eines BizTalk Service
Je nach gewählter Edition stehen unter Umständen nicht alle BizTalk Service-Einstellungen zur Verfügung.

1. Melden Sie sich beim [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Wählen Sie am unteren Rand des Navigationsbereichs **NEU** aus: ![Schaltfläche "Neu" auswählen][NEWButton]

3. Wählen Sie **APP SERVICES** > **BIZTALK SERVICE** > **BENUTZERDEFINIERT ERSTELLEN**: !["BizTalk Service" und "Benutzerdefiniert erstellen" auswählen][NewBizTalkService]

4. Geben Sie die Einstellungen für den BizTalk Service ein:

	<table border="1">
<tr>
<td><strong>Name des BizTalk Service</strong></td>
<td>Sie können einen beliebigen Namen eingeben, er sollte jedoch aussagekräftig sein. Beispiele hierfür sind:<br/><br/>
<em>MeineFirma</em>.biztalk.windows.net<br/>
<em>MeineFirmaMeineAnwendung</em>.biztalk.windows.net<br/>
<em>MeineAnwendung</em>.biztalk.windows.net<br/><br/>".biztalk.windows.net" wird dem von Ihnen eingegebenen Namen automatisch angehängt. Damit wird eine URL für den Zugriff auf Ihren BizTalk Service erstellt. Beispiel: <strong>https://<em>MeineAnwendung</em>.biztalk.windows.net</strong>.
</td>
</tr>
<tr>
<td><strong>Edition</strong></td>
<td>Wenn Sie sich in der Test-/Entwicklungsphase befinden, wählen Sie <strong>Developer</strong>. Wenn Sie sich in der Produktionsphase befinden, ermitteln Sie anhand der <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk Services: Editionsübersicht</a>, ob <strong>Premium</strong>, <strong>Standard</strong> oder <strong>Basic</strong> die richtige Wahl für Ihr Geschäftsszenario ist.
</td>
</tr>
<tr>
<td><strong>Region</strong></td>
<td>Wählen Sie die geografische Region, in der Ihr BizTalk Service gehostet werden soll.</td>
</tr>
<tr>
<td><strong>Domänen-URL</strong></td>
<td><strong>Optional</strong>. Standardmäßig lautet die Domänen-URL <em>IhrBizTalkServicename</em>.biztalk.windows.net. Sie können auch eine benutzerdefinierte Domäne eingeben. Wenn Ihre Domäne beispielsweise <em>contoso</em> lautet, können Sie Folgendes eingeben: <br/><br/>
<em>MeineFirma</em>.contoso.com<br/>
<em>MeineFirmaMeineAnwendung</em>.contoso.com<br/>
<em>MeineAnwendung</em>.contoso.com<br/>
<em>IhrBizTalkServicename</em>.contoso.com<br/>
</td>
</tr>
</table>
Wählen Sie den Pfeil für "Weiter" aus.

5. Geben Sie die Speicher- und Datenbankeinstellungen ein:

	<table border="1">
<tr>
<td><strong>Überwachen/Archivieren des Speicherkontos</strong></td>
<td>Wählen Sie ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues. <br/><br/>Wenn Sie ein neues Speicherkonto erstellen, geben Sie den <strong>Speicherkontonamen</strong> ein.</td>
</tr>
<tr>
<td><strong>Nachverfolgungsdatenbank</strong></td>
<td>Sie können eine vorhandene Azure SQL-Datenbank verwenden, wenn sie nicht bereits von einem anderen BizTalk Service genutzt wird. Sie benötigen den Anmeldenamen und das Kennwort, der bzw. das beim Erstellen des Azure SQL-Datenbankservers eingegeben wurde.<br/><br/><strong>TIPP</strong> Erstellen Sie die Nachverfolgungsdatenbank und das Speicherkonto für die Überwachung/Archivierung in derselben Region wie den BizTalk Service.</td>
</tr>
</table>
Wählen Sie den Pfeil für "Weiter" aus.

6. Geben Sie die Datenbankeinstellungen ein:

	<table border="1">
<tr>
<td><strong>Name</strong></td>
<td>Verfügbar, wenn im vorherigen Bildschirm <strong>Eine neue SQL-Datenbankinstanz erstellen</strong> ausgewählt wurde.
<br/><br/>
Geben Sie den Namen der SQL-Datenbank ein, die vom BizTalk Service verwendet werden soll.</td>
</tr>
<tr>
<td><strong>Server</strong></td>
<td>Verfügbar, wenn im vorherigen Bildschirm <strong>Eine neue SQL-Datenbankinstanz erstellen</strong> ausgewählt wurde.
<br/><br/>
Wählen Sie einen vorhandenen SQL-Datenbankserver aus, oder erstellen Sie einen neuen.</td>
</tr>
<tr>
<td><strong>Serveranmeldename</strong></td>
<td>Geben Sie den Benutzernamen für die Anmeldung ein.</td>
</tr>
<tr>
<td><strong>Kennwort für die Serveranmeldung</strong></td>
<td>Geben Sie das Kennwort für die Anmeldung ein.</td>
</tr>
<tr>
<td><strong>Region</strong></td>
<td>Verfügbar, wenn <strong>Eine neue SQL-Datenbankinstanz erstellen</strong> ausgewählt wurde. Wählen Sie die geografische Region aus, in der die SQL-Datenbank gehostet werden soll.</td>
</tr>
</table>

Wählen Sie das Häkchen, um den Assistenten abzuschließen. Das Statussymbol wird angezeigt: ![Das Statussymbol wird angezeigt, wenn der Vorgang abgeschlossen ist][ProgressComplete]

Nach Abschluss des Vorgangs ist der Azure BizTalk Service erstellt und für die Anwendungen einsatzbereit. Die Standardeinstellungen sind ausreichend. Wenn Sie die Standardeinstellungen ändern möchten, wählen Sie im linken Navigationsbereich **BIZTALK SERVICES** und anschließend Ihren BizTalk-Dienst aus. Auf den oben angezeigten [Registerkarten "Dashboard", "Überwachen" und "Skalieren"](biztalk-dashboard-monitor-scale-tabs.md) sind weitere Einstellungen enthalten.

Abhängig vom Status des BizTalk Service können einige Vorgänge nicht abgeschlossen werden. Eine Liste dieser Vorgänge finden Sie unter [BizTalk Services: Dienststatusübersicht](biztalk-service-state-chart.md).


## Nach der Bereitstellung auszuführende Schritte

-  [Installieren des Zertifikats auf einem lokalen Computer](#InstallCert)
-  [Hinzufügen eines produktionsreifen Zertifikats](#AddCert)
-  [Abrufen des Access Control-Namespace](#ACS)

#### <a name="InstallCert"></a>Installieren des Zertifikats auf einem lokalen Computer
Im Rahmen der BizTalk Service-Bereitstellung wird ein selbstsigniertes Zertifikat erstellt und ihrem BizTalk Service-Abonnement zugewiesen. Sie müssen dieses Zertifikat herunterladen und auf den Computern installieren, von denen aus Sie entweder BizTalk Service-Anwendungen bereitstellen oder Nachrichten an einen BizTalk Service-Endpunkt senden.

1. Melden Sie sich beim [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Wählen Sie im linken Navigationsbereich **BIZTALK SERVICES** und anschließend Ihr BizTalk Service-Abonnement aus.
3. Klicken Sie auf die Registerkarte **Dashboard**.
4. Wählen Sie **SSL-Zertifikat herunterladen** aus. ![SSL-Zertifikat ändern][QuickGlance]
5. Doppelklicken Sie auf das Zertifikat, und durchlaufen Sie den Assistenten zum Installieren des Zertifikats. Stellen Sie sicher, dass das Zertifikat im Speicher für **vertrauenswürdige Stammzertifizierungsstellen** installiert wird.

#### <a name="AddCert"></a>Hinzufügen eines produktionsreifen Zertifikats
Das selbstsignierte Zertifikat, das bei der Erstellung von BizTalk Services automatisch erstellt wird, ist nur für den Einsatz in Entwicklungsumgebungen gedacht. Für Produktionsszenarien muss es durch ein produktionsreifes Zertifikat ersetzt werden.

1. Klicken Sie auf der Registerkarte **Dashboard** auf **SSL-Zertifikat aktualisieren**.
2. Navigieren Sie zu Ihrem privaten SSL-Zertifikat (*Zertifikatname*.pfx), das den Namen Ihres BizTalk-Diensts enthält, geben Sie das Kennwort ein, und klicken Sie auf das Häkchen.

#### <a name="ACS"></a>Abrufen des Access Control-Namespace

1. Melden Sie sich beim [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Wählen Sie im linken Navigationsbereich **BIZTALK SERVICES** und anschließend Ihren BizTalk-Dienst aus.
3. Klicken Sie auf der Taskleiste auf **Verbindungsinformationen**: ![Verbindungsinformationen auswählen][ACSConnectInfo]

4. Kopieren Sie die Access Control-Werte.

Wenn Sie ein BizTalk Service-Projekt von Visual Studio aus bereitstellen, geben Sie diesen Access Control-Namespace an. Der Access Control-Namespace wird automatisch für den BizTalk Service erstellt.

Die Access Control-Werte können mit jeder Anwendung verwendet werden. Nach Erstellung von Azure BizTalk Services steuert dieser Access Control-Namespace die Authentifizierung bei der BizTalk Service-Bereitstellung. Wenn Sie das Abonnement ändern oder den Namespace verwalten möchten, wählen Sie im linken Navigationsbereich zunächst **ACTIVE DIRECTORY** und anschließend Ihren Namespace aus. In der Taskleiste sind Ihre Optionen aufgeführt.

Wenn Sie auf **Verwalten** klicken, wird das Access Control-Verwaltungsportal geöffnet. Im Access Control-Verwaltungsportal verwendet der BizTalk-Dienst **Dienstidentitäten**: ![ACS-Dienstidentitäten im Access Control-Verwaltungsportal][ACSServiceIdentities]

Die Access Control Service-Identität besteht aus einem Satz von Anmeldeinformationen, mit denen sich Anwendungen oder Clients direkt bei Access Control authentifizieren und ein Token abrufen können.

> [AZURE.IMPORTANT]Der BizTalk Service verwendet als Standarddienstidentität **Owner** und den Wert **Kennwort**. Wenn Sie den Wert des symmetrischen Schlüssels anstelle des Kennwortwerts verwenden, kann folgender Fehler auftreten.<br/><br/>*Mit den angegebenen Anmeldeinformationen konnte keine Verbindung zum Access Control-Verwaltungsdienstkonto hergestellt werden*

[Verwalten Ihres ACS-Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx) enthält einige Richtlinien und Empfehlungen.

## Erläuterung der Anforderungen

Diese Anforderungen gelten nicht für die Free Edition. <table border="1"> <tr bgcolor="FAF9F9"> <td><strong>Anforderungen</strong></td> <td><strong>Grund</strong></td> </tr> <tr> <td>Azure-Abonnement</td> <td>Das Abonnement bestimmt, wer sich beim Azure-Portal anmelden kann. Der Kontoinhaber erstellt das Abonnement unter <a HREF="https://account.windowsazure.com/Subscriptions">Azure-Abonnements</a>. <br/><br/> Das Azure-Konto kann über mehrere Abonnements verfügen und von jeder berechtigten Person verwaltet werden. Beispielsweise kann der Azure-Kontoinhaber ein Abonnement namens <em>BizTalkService-Abonnement</em> erstellen und den BizTalk-Administratoren in Ihrem Unternehmen (z. B. ContosoBTSAdmins@live.com) Zugriff auf dieses Abonnement gewähren. In diesem Szenario melden sich die BizTalk-Administratoren beim Azure-Portal an und haben uneingeschränkte Administratorrechte für alle gehosteten Dienste des Abonnements, einschließlich Azure BizTalk Services. Die BizTalk-Administratoren sind nicht die Azure-Kontoinhaber und haben daher keinen Zugriff auf Abrechnungsinformationen. <br/><br/> <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577"> Verwalten von Abonnements und Speicherkonten im Azure-Portal</a> enthält nähere Informationen. </td> </tr> <tr> <td>Azure SQL-Datenbank</td> <td>Enthält die vom BizTalk Service verwendeten Tabellen, Ansichten und gespeicherten Prozeduren, einschließlich Nachverfolgungsdaten. <br/><br/> Wenn Sie einen BizTalk Service erstellen, können Sie einen vorhandenen Azure SQL Server und eine vorhandene Azure SQL-Datenbank verwenden oder einen neuen Server bzw. eine neue Datenbank automatisch erstellen. <br/><br/> Die SQL-Datenbankskalierung wird automatisch konfiguriert Normalerweise ist die Standardskalierung für einen BizTalk Service ausreichend. Änderungen der Skalierung können sich auf den Preis auswirken. Siehe <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Konten und Abrechnung für Azure SQL-Datenbanken</a> <br/><br/> <strong>Hinweise</strong> <br/> <ul> <li> Wenn Sie einen neuen Azure SQL Server und eine Datenbank erstellen, wird Azure Services automatisch aktiviert.. Für den BizTalk Service muss Azure Services aktiviert sein.</li> <li>Bei der Erstellung einer neuen Azure SQL-Datenbank auf einem vorhandenen Azure SQL Server werden die Firewallregeln auf dem Server nicht verändert. Folglich ist es möglich, dass andere Azure-Dienste keinen Zugriff auf die Datenbanken des Servers erhalten.</li> </ul> </td> </tr> <tr> <td>Azure Access Control-Namespace</td> <td>Dient zur Authentifizierung bei Azure BizTalk Services. Wenn Sie ein BizTalk Service-Projekt von Visual Studio aus bereitstellen, geben Sie diesen Access Control-Namespace an. Bei der Erstellung eines BizTalk Service wird der Access Control-Namespace automatisch erstellt.</td> </tr>

<tr>
<td>Azure-Speicherkonto</td>
<td>Bietet Zugriff auf die Tabellen, Blobs und Warteschlangen, die vom BizTalk Service für die Speicherung von Folgendem verwendet werden:

<ul>
<li>Protokolldateien zur Überwachung des BizTalk Service. Die Ausgabe der Überwachungsfunktion wird auch auf der Registerkarte "Überwachung" im Azure-Portal angezeigt.</li>
<li>Beim Erstellen einer X12- oder AS2-Vereinbarung zwischen Partnern können Sie die Funktion "Überwachung" aktivieren, um Eigenschaften von Nachrichten zu speichern. Diese Daten werden im Speicherkonto gespeichert.</li>
</ul>
<br/>
Wenn Sie einen BizTalk Service erstellen, können Sie ein vorhandenes Speicherkonto verwenden oder automatisch ein neues erstellen.
<br/><br/>
Die Standardspeichereinstellungen sind für einen BizTalk Service ausreichend.
<br/><br/>
Beim Erstellen eines Speicherkontos werden automatisch ein Primärschlüssel und ein Sekundärschlüssel erstellt. Diese Schlüssel steuern den Zugriff auf das Speicherkonto. Der BizTalk Service verwendet automatisch den Primärschlüssel.
<br/><br/>
Weitere Informationen finden Sie unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">Speicher</a>.
</td>
</tr>

<tr>
<td>Privates SSL-Zertifikat</td>
<td>
Bei Erstellung eines Azure BizTalk Service wird außerdem eine HTTPS-URL erstellt, die den Namen Ihres BizTalk Service enthält. Diese URL wird automatisch zur Verwendung eines selbstsignierten Zertifikats konfiguriert, das ausschließlich für die Entwicklung gilt. Für die Produktion benötigen Sie ein privates SSL-Zertifikat.
<br/><br/>
<strong>Wichtige Informationen zum SSL-Zertifikat</strong>

<ul>
<li>Das Ablaufdatum des Zertifikats muss innerhalb der nächsten 5 Jahre liegen.</li>
<li>Für alle privaten Zertifikate ist ein Kennwort erforderlich. Bringen Sie dieses Kennwort in Erfahrung, und teilen Sie es am besten Ihren Administratoren mit.</li>
<li>Selbstsignierte Zertifikate werden in Test-/Entwicklungsumgebungen verwendet. Bei Verwendung selbstsignierter Zertifikate importieren Sie das Zertifikat in Ihren persönlichen Zertifikatspeicher und den Zertifikatspeicher "Vertrauenswürdige Stammzertifizierungsstellen".</li>
</ul>
<br/>Wenn Sie die Produktionszertifikatanforderung an Ihre Zertifizierungsstelle senden, geben Sie die folgenden Zertifikateigenschaften an:
<br/>

<ul>
<li><strong>Erweiterte Schlüsselverwendung</strong>: Für Azure BizTalk Services ist mindestens die Serverauthentifizierung erforderlich.</li>
<li><strong>Gemeinsamer Name</strong>: Geben Sie den vollqualifizierten Domänennamen (FQDN) Ihrer Azure BizTalk Service-URL ein. Siehe <a HREF="#BizTalk">Erstellen eines BizTalk Service</a> in diesem Artikel.</li>
</ul>
<br/>
Nach der Erstellung des BizTalk Service kann ein neues oder anderes Zertifikat hinzugefügt werden.
</td>
</tr>
</table>



## Hybridverbindungen

Wenn Sie einen Azure BizTalk Service erstellen, steht die Registerkarte **Hybridverbindungen** zur Verfügung:

![Registerkarte "Hybridverbindungen"][HybridConnectionTab]

Über Hybridverbindungen werden Azure-Websites oder Azure Mobile Services mit einer lokalen Ressource (wie SQL Server, MySQL, HTTP Web-APIs, Mobile Services und die meisten benutzerdefinierten Webdienste) verbunden, die einen statischen TCP-Port verwendet. Hybridverbindungen unterscheiden sich vom BizTalk Adapter Service. Der BizTalk Adapter Service stellt eine Verbindung zwischen Azure BizTalk Services und einem lokalen Branchensystem her.

 Weitere Informationen, beispielsweise zum Erstellen und Verwalten von Hybridverbindungen, finden Sie unter [Hybridverbindungen](integration-hybrid-connection-overview.md).


## Nächste Schritte

Nachdem Sie jetzt einen BizTalk Service erstellt haben, machen Sie sich noch mit den verschiedenen Registerkarten unter [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](biztalk-dashboard-monitor-scale-tabs.md) vertraut. Ihr BizTalk Service steht jetzt für Ihre Anwendungen zur Verfügung. Wenn Sie mit dem Erstellen von Anwendungen beginnen möchten, wechseln Sie zu [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Weitere Informationen
- [BizTalk Services: Editionsübersicht](biztalk-editions-feature-chart.md)<br/>
- [BizTalk Services: Statusübersicht](biztalk-service-state-chart.md)<br/>
- [BizTalk Services: Sichern und Wiederherstellen](biztalk-backup-restore.md)<br/>
- [BizTalk Services: Drosselung](biztalk-throttling-thresholds.md)<br/>
- [BizTalk Services: Name und Schlüssel des Ausstellers](biztalk-issuer-name-issuer-key.md)<br/>
- [Wie verwende ich das Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Hybridverbindungen](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png

<!---HONumber=AcomDC_1203_2015-->