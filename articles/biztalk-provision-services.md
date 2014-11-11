<properties linkid="provisioning-biztalk-service" urlDisplayName="Provision BizTalk Services in management portal" pageTitle="Create BizTalk Services in management portal | Azure" metaKeywords="Get started Azure biztalk services, provision, Azure unstructured data" description="Learn how to provision a BizTalk service in the Azure Management Portal, as well as create an optional SQL database server and Storage account." metaCanonical="http://www.windowsazure.com/de-de/manage/services/biztalk-services/provisioning-biztalk-service" services="biztalk-services" documentationCenter="" title="BizTalk Services: Provisioning Using Azure Management Portal" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# Erstellen eines BizTalk Service im Azure-Verwaltungsportal

In diesem Thema wird die Erstellung eines Azure BizTalk Service im Azure-Verwaltungsportal Schritt für Schritt beschrieben. Dies umfasst Folgendes:

-   [Erstellen eines BizTalk Service][Erstellen eines BizTalk Service]
-   [Nach der Bereitstellung auszuführende Schritte][Nach der Bereitstellung auszuführende Schritte]
-   [Erläuterung der Anforderungen][Erläuterung der Anforderungen]
-   [Hybridverbindungen - Neu!][Hybridverbindungen - Neu!]

<div class="dev-callout"> 
<b>Tipp</b> 
<p>Um sich beim Azure-Verwaltungsportal anmelden zu k&ouml;nnen, ben&ouml;tigen Sie ein Azure-Konto und ein Azure-Abonnement. Wenn Sie &uuml;ber kein Konto verf&uuml;gen, k&ouml;nnen Sie in wenigen Minuten ein kostenloses Testkonto erstellen. Siehe <a href="http://go.microsoft.com/fwlink/p/?LinkID=239738">Kostenlose Azure-Testversion</a>.</p> 
</div>

## <a name="BizTalk"></a>Erstellen eines BizTalk Service

Je nach gewählter Edition stehen unter Umständen nicht alle BizTalk Service-Einstellungen zur Verfügung.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Wählen Sie im unteren Navigationsbereich **NEU** aus:
    ![Schaltfläche 'Neu' auswählen][Schaltfläche 'Neu' auswählen]

3.  Wählen Sie **APP-DIENSTE** \> **BIZTALK SERVICE** \> **BENUTZERDEFINIERT ERSTELLEN** aus:
    ![BizTalk Service und 'Benutzerdefiniert erstellen' auswählen][BizTalk Service und 'Benutzerdefiniert erstellen' auswählen]

4.  Geben Sie die folgenden Einstellungen für den BizTalk Service ein.

	<table border="1">
	<tr>
	<td><strong>BizTalk-Dienstname</strong></td>
	<td>
	Sie können jeden beliebigen Namen eingeben, er sollte jedoch aussagekräftig sein. Beispiele hierfür sind:
	<br></br><br></br>
	<em>MeineFirma</em>.biztalk.windows.net<br></br>
	<em>MeineFirmaMeineAnwendung</em>.biztalk.windows.net<br></br>
	<em>MeineAnwendung</em>.biztalk.windows.net<br></br><br></br>
	".biztalk.windows.net" wird automatisch dem von Ihnen eingegebenen Namen angehängt. Damit wird eine URL für den Zugriff auf Ihren BizTalk Service erstellt. Beispiel: <strong>https://<em>MeineAnwendung</em>.biztalk.windows.net</strong>.
	</td>
	</tr>
	<tr>
	<td><strong>Edition</strong></td>
	<td>Wenn Sie sich in der Test-/Entwicklungsphase befinden, wählen Sie <strong>Developer</strong>. Wenn Sie sich in der Produktionsphase befinden, können Sie anhand von <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk Services: Editionen-Diagramm</a> entscheiden, ob die <strong>Premium</strong>, <strong>Standard</strong> oder <strong>Basic</strong> Edition die richtige Wahl für Ihr Unternehmensszenario wäre.</td>
	</tr>
	<tr>
	<td><strong>Region</strong></td>
	<td>Wählen Sie die geografische Region, in der Ihr BizTalk Service gehostet werden soll.</td>
	</tr>
	<tr>
	<td><strong>Domänen-URL</strong></td>
	<td><strong>Optional</strong>. Standardmäßig lautet die Domänen-URL "<em>IhrBizTalkDienstname</em>.biztalk.windows.net". Sie können auch eine benutzerdefinierte Domäne eingeben. Wenn Ihre Domäne beispielsweise *contoso* lautet, können Sie Folgendes eingeben:<br></br><br></br>
	<em>MeineFirma</em>.contoso.com<br></br>
	<em>MeineFirmaMeineAnwendung</em>.contoso.com<br></br>
	<em>MeineAnwendung</em>.contoso.com<br></br>
	<em>MeinBizTalkServiceName</em>.contoso.com</td>
	</tr>
	</table>

	Wählen Sie den Pfeil für "Weiter" aus.

5.  Geben Sie die Speicher- und Datenbankeinstellungen ein:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Überwachen/Archivieren des Speicherkontos</strong></td>
    <td align="left">Wählen Sie ein vorhandenes Speicherkonto aus, oder erstellen Sie über &quot;Neues Speicherkonto erstellen&quot; ein neues.<br /><br /> Wenn Sie ein neues Speicherkonto erstellen, geben Sie den <strong>Speicherkontonamen</strong> ein.</td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Nachverfolgungsdatenbank</strong></td>
    <td align="left">Sie können eine vorhandene Azure SQL-Datenbank verwenden, wenn sie nicht bereits von einem anderen BizTalk Service genutzt wird. Sie benötigen den Anmeldenamen und das Kennwort, der bzw. das beim Erstellen des Azure SQL-Datenbankservers eingegeben wurde.<br /><br />
    <div class="dev-callout"> 
<b>Tipp</b> 
<p>Erstellen Sie die Nachverfolgungsdatenbank und das Speicherkonto f&uuml;r die &Uuml;berwachung/Archivierung in derselben Region wie den BizTalk Service.</p> 
</div></td>
    </tr>
    </tbody>
    </table>

    Wählen Sie den Pfeil für "Weiter" aus.

6.  Geben Sie die Datenbankeinstellungen ein:

	<table border="1">
	<tr>
	<td><strong>Name</strong></td>
	<td>Verfügbar, wenn im vorherigen Bildschirm <strong>Eine neue SQL-Datenbankinstanz erstellen</strong> ausgewählt wurde.<br></br><br></br>
	Geben Sie den Namen der SQL-Datenbank ein, die vom BizTalk Service verwendet werden soll.</td>
	</tr>
	<tr>
	<td><strong>Server</strong></td>
	<td>Verfügbar, wenn im vorherigen Bildschirm <strong>Eine neue SQL-Datenbankinstanz erstellen</strong> ausgewählt wurde.<br></br><br></br>
	Wählen Sie einen vorhandenen SQL-Datenbankserver aus, oder erstellen Sie einen neuen.</td>
	</tr>
	<tr>
	<td><strong>Serveranmeldename</strong></td>
	<td>Geben Sie den Serveranmeldenamen ein.</td>
	</tr>
	<tr>
    <td><strong>Kennwort für die Serveranmeldung</strong></td>
	<td>Geben Sie das Kennwort für die Serveranmeldung ein.</td>
	</tr>
	<tr>
	<td><strong>Region</strong></td>
	<td>Verfügbar, wenn <strong>Eine neue SQL-Datenbankinstanz erstellen</strong> ausgewählt wurde. Wählen Sie die geografische Region aus, in der die SQL-Datenbank gehostet werden soll.</td>
	</tr>
	</table>

Wählen Sie das Häkchen, um den Assistenten abzuschließen. Das Statussymbol wird angezeigt:<br></br><br></br>
![Anzeige des Statussymbols nach Abschluss][Anzeige des Statussymbols nach Abschluss]

Nach Abschluss des Vorgangs ist der Azure BizTalk Service erstellt und für die Anwendungen einsatzbereit. Die Standardeinstellungen sind ausreichend. Wenn Sie die Standardeinstellungen ändern möchten, wählen Sie im linken Navigationsbereich **BIZTALK SERVICES** und anschließend Ihren BizTalk Service aus. Auf den oben angezeigten [Registerkarten "Dashboard", "Überwachen" und "Skalieren"][Registerkarten "Dashboard", "Überwachen" und "Skalieren"] sind weitere Einstellungen enthalten.

Abhängig vom Status des BizTalk Service können einige Vorgänge nicht abgeschlossen werden. Eine Liste dieser Vorgänge finden Sie unter [BizTalk-Dienststatusübersicht][BizTalk-Dienststatusübersicht].

## <a name="PostProv"></a>Nach der Bereitstellung auszuführende Schritte

-   [Installieren des Zertifikats auf einem lokalen Computer][Installieren des Zertifikats auf einem lokalen Computer]
-   [Hinzufügen eines produktionsreifen Zertifikats][Hinzufügen eines produktionsreifen Zertifikats]
-   [Abrufen des Access Control-Namespace][Abrufen des Access Control-Namespace]

#### <a name="InstallCert"></a>Installieren des Zertifikats auf einem lokalen Computer

Im Rahmen der BizTalk Service-Bereitstellung wird ein selbstsigniertes Zertifikat erstellt und ihrem BizTalk Service-Abonnement zugewiesen. Sie müssen dieses Zertifikat herunterladen und auf den Computern installieren, von denen aus Sie entweder BizTalk Service-Anwendungen bereitstellen oder Nachrichten an einen BizTalk Service-Endpunkt senden.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Wählen Sie im linken Navigationsbereich **BIZTALK SERVICES** und anschließend Ihr BizTalk Service-Abonnement aus.
3.  Klicken Sie auf die Registerkarte **Dashboard**.
4.  Klicken Sie auf **SSL-Zertifikat herunterladen**.
    ![SSL-Zertifikat ändern][SSL-Zertifikat ändern]
5.  Doppelklicken Sie auf das Zertifikat, und durchlaufen Sie den Assistenten zum Installieren des Zertifikats. Stellen Sie sicher, dass das Zertifikat im Speicher für **vertrauenswürdige Stammzertifizierungsstellen** installiert wird.

#### <a name="AddCert"></a>Hinzufügen eines produktionsreifen Zertifikats

Das selbstsignierte Zertifikat, das bei der Bereitstellung von BizTalk Services automatisch erstellt wird, ist nur für den Einsatz in Entwicklungsumgebungen gedacht. Für Produktionsszenarien muss es durch ein produktionsreifes Zertifikat ersetzt werden.

1.  Klicken Sie auf der Registerkarte **Dashboard** auf **SSL-Zertifikat aktualisieren**.
2.  Navigieren Sie zu Ihrem privaten SSL-Zertifikat (*ZertifikatName*.pfx), das den Namen Ihres BizTalk Service enthält, geben Sie das Kennwort ein, und klicken Sie auf das Häkchen.

#### <a name="ACS"></a>Abrufen des Access Control-Namespace

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Wählen Sie im linken Navigationsbereich **BIZTALK SERVICES** und anschließend Ihren BizTalk Service aus.
3.  Wählen Sie in der Taskleiste **Verbindungsinformationen** aus:
    !['Verbindungsinformationen' auswählen]['Verbindungsinformationen' auswählen]

4.  Kopieren Sie die Access Control-Werte.

Wenn Sie ein BizTalk Service-Projekt von Visual Studio aus bereitstellen, geben Sie diesen Access Control-Namespace an. Der Access Control-Namespace wird automatisch für den BizTalk Service erstellt.

Die Access Control-Werte können mit jeder Anwendung verwendet werden. Nach Erstellung von Azure BizTalk Services steuert dieser Access Control-Namespace die Authentifizierung bei der BizTalk Service-Bereitstellung. Wenn Sie das Abonnement ändern oder den Namespace verwalten möchten, wählen Sie im linken Navigationsbereich zunächst **ACTIVE DIRECTORY** und anschließend Ihren Namespace aus. In der Taskleiste sind Ihre Optionen aufgeführt.

Wenn Sie auf **Verwalten** klicken, wird das Access Control-Verwaltungsportal geöffnet. Im Access Control-Verwaltungsportal verwendet der BizTalk Service **Dienstidentitäten**:
![ACS-Dienstidentitäten im Access Control-Verwaltungsportal][ACS-Dienstidentitäten im Access Control-Verwaltungsportal]

Die Access Control-Dienstidentität besteht aus einem Satz von Anmeldeinformationen, mit denen sich Anwendungen oder Clients direkt bei Access Control authentifizieren und einen Token abrufen können.

**Wichtig**<br></br>
Der BizTalk Service verwendet als Standarddienstidentität **Owner** (Besitzer) und den Wert **Password** (Kennwort). Wenn Sie statt "Password" (Kennwort) den Wert "Symmetric Key" (Symmetrischer Schlüssel) verwenden, wird möglicherweise folgender Fehler angezeigt:

*Could not connect to the Access Control Management Service account with the specified credentials (Konnte mit den angegebenen Anmeldeinformationen keine Verbindung zum Access Control-Verwaltungsdienstkonto herstellen)*

[Verwalten Ihres ACS-Namespace][Verwalten Ihres ACS-Namespace] enthält einige Richtlinien und Empfehlungen.

## <a name="Requirements"></a>Erläuterung der Anforderungen

Diese Anforderungen gelten nicht für die Free Edition.

<table border="1">

<tr bgcolor="FAF9F9">

<td>
<strong>Erforderliches Element</strong>

</td>

<td>
<strong>Grund</strong>

</td>

</tr>

<tr>

<td>
Azure-Abonnement

</td>

<td>
Das Abonnement bestimmt, wer sich beim Azure-Verwaltungsportal anmelden kann. Der Kontoinhaber erstellt das Abonnement unter <a HREF="https://account.windowsazure.com/Subscriptions"> Azure-Abonnements</a>.<br></br><br></br>
Das Azure-Konto kann über mehrere Abonnements verfügen und von jeder berechtigten Person verwaltet werden. Beispielsweise kann der Azure-Kontoinhaber ein Abonnement namens *BizTalkServiceAbonnement* erstellen und den BizTalk-Administratoren in Ihrem Unternehmen (z. B. <ContosoBTSAdmins@live.com>) Zugriff auf dieses Abonnement gewähren. In diesem Szenario melden sich die BizTalk-Administratoren beim Azure-Verwaltungsportal an und haben uneingeschränkte Administratorrechte für alle gehosteten Dienste des Abonnements, einschließlich Azure BizTalk Services. Die BizTalk-Administratoren sind nicht die Azure-Kontoinhaber und haben daher keinen Zugriff auf Abrechnungsinformationen.<br></br><br></br>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577"> Verwalten von Abonnements und Speicherkonten im Azure-Verwaltungsportal</a> enthält nähere Informationen.

</td>

</tr>

<tr>

<td>
Azure SQL-Datenbank

</td>

<td>
Enthält die vom BizTalk Service verwendeten Tabellen, Ansichten und gespeicherten Prozeduren, einschließlich Nachverfolgungsdaten.<br></br><br></br>
Wenn Sie einen BizTalk Service erstellen, können Sie einen vorhandenen Azure SQL Server und eine vorhandene Azure SQL-Datenbank verwenden oder einen neuen Server bzw. eine neue Datenbank automatisch erstellen.<br></br><br></br>
Die SQL-Datenbankskalierung wird automatisch konfiguriert. Normalerweise ist die Standardskalierung für einen BizTalk Service ausreichend. Änderungen der Skalierung können sich auf den Preis auswirken. Siehe [Konten und Abrechnung für Azure SQL-Datenbanken][Konten und Abrechnung für Azure SQL-Datenbanken]<br></br><br></br>
<strong>Hinweise</strong><br></br>
<ul>
<li>Wenn Sie einen neuen Azure SQL Server und eine Datenbank erstellen, wird Azure Services automatisch aktiviert. Für den BizTalk Service muss Azure Services aktiviert sein.</li>
<li>Bei der Erstellung einer neuen Azure SQL-Datenbank auf einem vorhandenen Azure SQL Server werden die Firewallregeln auf dem Server nicht verändert. Folglich ist es möglich, dass andere Azure Services keinen Zugriff auf die Datenbanken des Servers erhalten.</li>
</ul>

</td>

</tr>

<tr>

<td>
Azure Access Control-Namespace

</td>

<td>
Dient zur Authentifizierung bei Azure BizTalk Services. Wenn Sie ein BizTalk Service-Projekt von Visual Studio aus bereitstellen, geben Sie diesen Access Control-Namespace an. Bei der Erstellung eines BizTalk Service wird der Access Control-Namespace automatisch erstellt.

</td>

</tr>
</p>
<tr>
<td>
Azure-Speicherkonto

</td>
<td>
Bietet Zugriff auf die Tabellen, Blobs und Warteschlangen, die vom BizTalk Service für die Speicherung von Folgendem verwendet werden:
<ul>
<li>Protokolldateien zur Überwachung des BizTalk Service. Die Ausgabe der Überwachungsfunktion wird auch auf der Registerkarte "Überwachung" im Azure-Verwaltungsportal angezeigt.</li>
<li>Beim Erstellen einer X12- oder AS2-Vereinbarung zwischen Partnern können Sie die Funkion "Überwachung" aktivieren, um Eigenschaften von Nachrichten zu speichern. Diese Daten werden im Speicherkonto gespeichert.</li>
</ul><br></br>
 Wenn Sie einen BizTalk Service erstellen, können Sie ein vorhandenes Speicherkonto verwenden oder automatisch ein neues erstellen.<br></br><br></br>
 Die Standardspeichereinstellungen sind für einen BizTalk Service ausreichend.<br></br><br></br>
 Beim Erstellen eines Speicherkontos werden automatisch ein Primärschlüssel und ein Sekundärschlüssel erstellt. Diese Schlüssel steuern den Zugriff auf das Speicherkonto. Der BizTalk Service verwendet automatisch den Primärschlüssel.<br></br><br></br>
 Weitere Informationen finden Sie unter <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671"> Speicher</a>.

</td>
</tr>
<tr>
<td>
Privates SSL-Zertifikat

</td>
<td>
Bei Erstellung eines Azure BizTalk Service wird außerdem eine HTTPS-URL erstellt, die den Namen Ihres BizTalk Service enthält. Diese URL wird automatisch zur Verwendung eines selbstsignierten Zertifikats konfiguriert, das ausschließlich für die Entwicklung gilt. Für die Produktion benötigen Sie ein privates SSL-Zertifikat.<br></br><br></br>
 <strong>Wichtige Informationen zum SSL-Zertifikat</strong>
<ul>
<li>Das Ablaufdatum des Zertifikats muss innerhalb der nächsten 5 Jahre liegen.</li>
<li>Für alle privaten Zertifikate ist ein Kennwort erforderlich. Bringen Sie dieses Kennwort in Erfahrung, und teilen Sie es am besten Ihren Administratoren mit.</li>
<li>Selbstsignierte Zertifikate werden in Test-/Entwicklungsumgebungen verwendet. Bei Verwendung selbstsignierter Zertifikate importieren Sie das Zertifikat in Ihren persönlichen Zertifikatsspeicher und den Zertifikatspeicher "Vertrauenswürdige Stammzertifizierungsstellen".</li>
</ul><br></br>
Wenn Sie die Produktionszertifikatsanforderung an Ihre Zertifizierungsstelle senden, geben Sie die folgenden Zertifikateigenschaften an:
<ul>
<li><strong>Erweiterte Schlüsselverwendung</strong>: Für Azure BizTalk Services ist mindestens die Serverauthentifizierung erforderlich.</li>
<li><strong>Gemeinsamer Name</strong>: Geben Sie den vollqualifizierten Domänennamen (FQDN) Ihrer Azure BizTalk Service-URL ein. Siehe <a HREF="#BizTalk">Erstellen eines BizTalk Service</a> in diesem Thema.</li>
</ul><br></br>
 Nach der Erstellung des BizTalk Service kann ein neues oder anderes Zertifikat hinzugefügt werden.

</td>
</tr>
</table>
## <a name="HC"></a>Hybridverbindungen

Wenn Sie einen Azure BizTalk Service erstellen, steht die Registerkarte **Hybridverbindungen** zur Verfügung:

![Registerkarte "Hybridverbindungen"][Registerkarte "Hybridverbindungen"]

Über Hybridverbindungen werden Azure-Websites oder Azure Mobile Services mit einer lokalen Ressource (wie SQL Server, MySQL, HTTP Web-APIs, Mobile Services und die meisten benutzerdefinierten Webdienste) verbunden, die einen statischen TCP-Port verwendet. Hybridverbindungen unterscheiden sich vom BizTalk Adapter Service. Der BizTalk Adapter Service stellt eine Verbindung zwischen Azure BizTalk Services und einem lokalen Branchensystem her.

Weitere Informationen, beispielsweise zum Erstellen und Verwalten von Hybridverbindungen, finden Sie unter [Hybridverbindungen][Hybridverbindungen].

## Weiter

Nachdem Sie jetzt einen BizTalk Service erstellt haben, machen Sie sich noch mit den verschiedenen Registerkarten unter [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"][Registerkarten "Dashboard", "Überwachen" und "Skalieren"] vertraut. Ihr BizTalk Service steht jetzt für Ihre Anwendungen zur Verfügung. Wenn Sie mit dem Erstellen von Anwendungen beginnen möchten, wechseln Sie zu [Azure BizTalk Services][Azure BizTalk Services].

## Weitere Informationen

-   [BizTalk Services: Editionen-Diagramm][BizTalk Services: Editionen-Diagramm]
-   [BizTalk Services: Statusübersicht][BizTalk-Dienststatusübersicht]
-   [BizTalk Services: Sichern und Wiederherstellen][BizTalk Services: Sichern und Wiederherstellen]
-   [BizTalk Services: Drosselung][BizTalk Services: Drosselung]
-   [BizTalk Services: Name und Schlüssel des Ausstellers][BizTalk Services: Name und Schlüssel des Ausstellers]
-   [Wie verwende ich das Azure BizTalk Services SDK][Wie verwende ich das Azure BizTalk Services SDK]
-   [Hybridverbindungen][Hybridverbindungen]

  [Erstellen eines BizTalk Service]: #BizTalk
  [Nach der Bereitstellung auszuführende Schritte]: #PostProv
  [Erläuterung der Anforderungen]: #Requirements
  [Hybridverbindungen - Neu!]: #HC
  [Azure-Verwaltungsportal]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Schaltfläche 'Neu' auswählen]: ./media/biztalk-provision-services/WABS_New.png
  [BizTalk Service und 'Benutzerdefiniert erstellen' auswählen]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
  [BizTalk Services: Editionen-Diagramm]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Anzeige des Statussymbols nach Abschluss]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
  [Registerkarten "Dashboard", "Überwachen" und "Skalieren"]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk-Dienststatusübersicht]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Installieren des Zertifikats auf einem lokalen Computer]: #InstallCert
  [Hinzufügen eines produktionsreifen Zertifikats]: #AddCert
  [Abrufen des Access Control-Namespace]: #ACS
  [SSL-Zertifikat ändern]: ./media/biztalk-provision-services/WABS_QuickGlance.png
  ['Verbindungsinformationen' auswählen]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
  [ACS-Dienstidentitäten im Access Control-Verwaltungsportal]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
  [Verwalten Ihres ACS-Namespace]: http://go.microsoft.com/fwlink/p/?LinkID=285670
  [Konten und Abrechnung für Azure SQL-Datenbanken]: http://go.microsoft.com/fwlink/p/?LinkID=234930
  [Registerkarte "Hybridverbindungen"]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
  [Hybridverbindungen]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Azure BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=235197
  [BizTalk Services: Sichern und Wiederherstellen]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk Services: Drosselung]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk Services: Name und Schlüssel des Ausstellers]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Wie verwende ich das Azure BizTalk Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=302335
