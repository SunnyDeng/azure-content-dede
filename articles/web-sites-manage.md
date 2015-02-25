<properties 
	pageTitle="Verwalten von Websites - Microsoft Azure-Dienstverwaltung" 
	description="Eine Referenz für die Websiteverwaltungs-Portalseiten in Microsoft Azure. Es werden Details für jede Websiteverwaltungsseite bereitgestellt. 
	services="web-sites" 
	documentationCenter="" 
	authors="MikeWasson" 
	writer="mwasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/18/2014" 
	ms.author="mwasson"/>

#<a name="howtomanage"></a>Verwalten von Websites über das Azure-Verwaltungsportal

Zur Verwaltung Ihrer Websites stehen Ihnen im Azure-Portal verschiedene Seiten oder "Registerkarten" zur Verfügung. Alle Verwaltungsseiten für Websites werden im Folgenden beschrieben.

## Schnellstart
Die Verwaltungsseite **Schnellstart** umfasst die folgenden Abschnitte:

- **Tools herunterladen** Bietet Links zur [Installation von WebMatrix][mswebmatrix] und zum [Microsoft Azure SDK][azuresdk].
- **Veröffentlichen Sie Ihre App** - Bietet Links zum Herunterladen des Veröffentlichungsprofils der Website, zum Zurücksetzen der Bereitstellungsanmeldeinformationen für die Website, zum Hinzufügen eines Slots für die gestaffelte Veröffentlichung (Bereitstellung) auf einer Website ohne Stagingumgebung sowie zu Informationen zur gestaffelten Veröffentlichung.
- **Quellcodeverwaltung integrieren** - Einrichten und Verwalten von Bereitstellungen über Quellcodeverwaltungstools oder Websites wie TFS, CodePlex, GitHub, Dropbox, Bitbucket oder lokales Git.

## Dashboard
Die Verwaltungsseite **Dashboard** umfasst die folgenden Abschnitte:

Ein Diagramm mit einer Zusammenfassung der Websitenutzung zur Messung bestimmter Metriken.

 - **CPU-Zeit** - Messung der CPU-Auslastung der Website.
 - **Eingehende Daten** - Messung der Daten, die die Website von Clients empfangen hat.
 - **Ausgehende Daten** - Messung der Daten, die die Website an Clients gesendet hat.
 - **HTTP-Serverfehler** - Anzahl der gesendeten HTTP-Meldungen vom Typ "5xx Serverfehler".
 - **Anforderungen** - Anzahl aller Clientanforderungen an die Website.

**Hinweis:** Sie können zusätzliche Leistungsmetriken auf der Verwaltungsseite **Überwachen** hinzufügen, indem Sie unten auf der Seite auf **Metriken hinzufügen** klicken. Weitere Informationen finden Sie unter [Überwachen von Websites][Überwachen].

**Webendpunktstatus** - Eine Liste von Webendpunkten, die zur Überwachung konfiguriert wurden. Falls keine Endpunkte konfiguriert wurden, klicken Sie auf **Webendpunkt-Überwachung konfigurieren** und wechseln zum Abschnitt **Überwachung** der Verwaltungsseite **Konfigurieren**. Weitere Informationen finden Sie unter [Überwachen von Websites][Überwachen].

**Autoskalierungsstatus** - Im Modus "Standard" können Sie Ihre Ressourcen automatisch skalieren, sodass Sie nur so viel wie nötig ausgeben. Um die automatische Skalierung zu aktivieren, wählen Sie **Autoskalierung konfigurieren** aus, woraufhin Sie zur Seite **Skalieren** gelangen. Falls sich Ihre Website derzeit im Modus "Kostenlos" oder "Freigegeben" befindet, müssen Sie zum Modus "Standard" wechseln (auf der Seite **Skalieren**), bevor Sie die automatische Skalierung konfigurieren können. **Über AutoScale-Vorgangsprotokolle** gelangen Sie zum Portal **Verwaltungsdienste**, wo Sie den Verlauf der automatischen Skalierung Ihrer Website anzeigen können. Die Standardabfrage erfolgt für die letzten 24 Stunden, Sie können die Abfrage jedoch anpassen.

**Verwendungsübersicht** - Dieser Abschnitt zeigt die Statistiken für die CPU-, Dateisystem- und Arbeitsspeicherauslastung der Website an.

**Verknüpfte Ressourcen** -Dieser Abschnitt enthält eine Liste der mit Ihrer Website verbundenen Ressourcen wie SQL- oder MySQL-Datenbanken bzw. ein Microsoft Azure-Speicherkonto. Klicken Sie auf den Namen der Ressource, um sie zu verwalten. Falls Sie über eine MySQL-Datenbank verfügen, gelangen Sie zur ClearDB-Verwaltungsseite, sobald Sie auf den Namen klicken. Dort können Sie die Leistungsmetriken anzeigen oder das ClearDB-Dashboard aufrufen, wo sich die MySQL-Datenbank bei Bedarf upgraden lässt. Sind keine Ressourcen aufgelistet, klicken Sie auf **Verknüpfte Ressourcen verwalten**, um zur Seite **Verknüpfte Ressourcen** zu gelangen, wo Sie einen Link zu einer Ressource für Ihre Website hinzufügen können.

Der Abschnitt **Auf einen Blick** enthält die folgenden zusammenfassenden Informationen und Links (abhängig von Ihren Einstellungen sind einige der unten aufgelisteten Optionen möglicherweise nicht vorhanden):

 - **Zutreffende Add-Ons anzeigen** - Öffnet das Dialogfeld **In Store kaufen**, wo Sie Add-Ons erwerben können, die Ihre Website um zusätzliche Funktionen erweitern. Einige Add-Ons sind möglicherweise nicht in Ihrer Region oder für Ihre Umgebung verfügbar.
 - **Verbindungszeichenfolgen anzeigen** - Zeigt die Datenbank-Verbindungszeichenfolgen Ihrer Website an.
 - **Veröffentlichungsprofil herunterladen** - Klicken Sie auf diesen Link, um das Veröffentlichungsprofil für Ihre Website herunterzuladen. Das Veröffentlichungsprofil enthält Ihre Anmeldeinformationen (Benutzername und Kennwort) sowie die URLs zum Hochladen von Inhalt auf Ihre Website über FTP und Git. Die Profildatei liegt im XML-Format vor und kann in einem Texteditor angezeigt werden.
 - **Anmeldeinformationen für die Bereitstellung einrichten** - Klicken Sie auf diese Option, um einen Benutzernamen und ein Kennwort zum Hochladen von Inhalt auf Ihre Website über FTP oder Git festzulegen. Sie können mit diesen Anmeldeinformationen Inhalt an beliebige Websites in Ihrem Abonnement mithilfe von Push übertragen. (Siehe [FTP-Anmeldeinformationen].) **Hinweis**: Authentifizierung beim FTP-Host oder dem Git-Repository mittels Microsoft-Konto-Anmeldung (Live ID) wird nicht unterstützt.
 - **Anmeldeinformationen des Veröffentlichungsprofils zurücksetzen** -Setzt das Veröffentlichungsprofil für Ihre Website zurück. Zuvor heruntergeladene Veröffentlichungsprofile werden ungültig.
 - **Bereitstellung über Quellcodeverwaltung einrichten** - Öffnet ein Dialogfeld, in dem Sie eine kontinuierliche Veröffentlichung per Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket oder lokalem Git einrichten können.
 - **Einen neuen Bereitstellungsslot hinzufügen** - Verwenden Sie diese Funktion für Websites im Modus "Standard", um einen Stagingslot für die Website zu erstellen. Der Stagingslot (gestaffelte Website) bietet die Möglichkeit, den Inhalt und die Konfiguration der Website zu prüfen, bevor diese in Produktion geht. Sie können die Stagingversion der Website auch verwenden, um Inhaltsaktualisierungen nach und nach hinzuzufügen und dann die Website in die Produktion zu übernehmen, wenn alle Aktualisierungen im Stagingslot komplett sind. (Websites, die sich bereits in der Stagingphase befinden, lassen sich nicht um einen weiteren Slot erweitern.)
 - **In Visual Studio Online bearbeiten** - Klicken Sie auf diesen Link, um Ihre Website direkt online mithilfe von Visual Studio Online im Microsoft Azure-Portal zu bearbeiten. Diese Option ist nur verfügbar, wenn Sie sie auf der Seite **Konfigurieren** aktivieren.
 - **Von Dropbox trennen** - Falls Sie für die Bereitstellung eine Verbindung mit Dropbox eingerichtet haben, können Sie die Verbindung über diesen Link trennen.
 - **Git-Repository löschen** - Falls Sie ein Git-Repository eingerichtet haben, können Sie es über diesen Link löschen.
 - **Status** - Gibt an, ob die Website ausgeführt wird.
 - **Verwaltungsdienste** - Klicken Sie auf den Link **Vorgangsprotokolle**, um die Vorgangsprotokolle für Ihre Website aus dem Microsoft Azure-Verwaltungsdienstportal anzuzeigen.
 - **Virtuelle IP-Adresse** - Zeigt die virtuelle IP-Adresse der Website an, wenn Sie im Abschnitt **SSL-Bindungen** der Registerkarte **Konfigurieren** eine IP-basierte SSL-Bindung für die Website konfiguriert haben.
 - **Website-URL** - Gibt die öffentlich zugängliche Adresse der Website im Internet an.
 - **Servermodus** - Gibt an, ob die Website im Modus "Kostenlos", "Freigegeben", "Basic" oder "Standard" ausgeführt wird. Weitere Informationen zum Webskalierungsgruppen-Modus finden Sie unter [Skalieren von Websites][Skalieren].
 - **FTP-Hostname** - Gibt die zu verwendende URL an, wenn die Veröffentlichung auf der Website über FTP erfolgt (siehe [FTP-Anmeldeinformationen]).
 - **FTPS-Hostname** - Gibt die zu verwendende URL an, wenn die Veröffentlichung auf der Website über FTPS erfolgt (siehe [FTP-Anmeldeinformationen]).
 - **Bereitstellungs-/FTP-Benutzer** - Gibt das verwendete Konto an, wenn die Bereitstellung der Website auf Microsoft Azure über FTP oder Git erfolgt (siehe [FTP-Anmeldeinformationen]).
 - **FTP-Diagnoseprotokolle** - Gibt das FTP-Verzeichnis der Diagnoseprotokolle der Website an, wenn die Diagnoseprotokollierung auf der Verwaltungsseite **Konfigurieren** aktiviert wurde.
 - **FTPS-Diagnoseprotokolle** - Gibt das FTPS-Verzeichnis der Diagnoseprotokolle der Website an, wenn die Diagnoseprotokollierung auf der Verwaltungsseite **Konfigurieren** aktiviert wurde.
 - **Standort** - Gibt die Region des Rechenzentrums an, das die Website hostet.
 - **Abonnementname** - Gibt den Namen des Abonnements an, zu dem die Website gehört.
 - **Abonnement-ID** - Gibt die eindeutige Abonnement-ID (GUID) des Abonnements an, zu dem die Website gehört.


##Bereitstellungen
 Diese Registerkarte wird nur angezeigt, wenn Sie die Bereitstellung über die Quellcodeverwaltung eingerichtet haben. Auf der Verwaltungsseite **Bereitstellungen** finden Sie eine Zusammenfassung aller Bereitstellungen, die mit Ihrer ausgewählten Veröffentlichungsmethode auf der Website erfolgt sind. Wenn Git-Veröffentlichung für die Website konfiguriert wurde, aber keine Bereitstellungen durchgeführt wurden, enthält die Verwaltungsseite **Bereitstellungen** Informationen darüber, wie Sie unter Verwendung von GIT eine Webanwendung auf der Website bereitstellen.

##Überwachen
Die Verwaltungsseite **Überwachen** enthält ein Diagramm mit den Nutzungsinformationen der Website. Standardmäßig zeigt dieses Diagramm dieselben Metriken wie das Diagramm auf der Seite **Dashboard** an, die oben im Abschnitt "Dashboard" beschrieben ist. Das Diagramm kann auch zur Anzeige der Metriken für HTTP-Erfolge, HTTP-Umleitungen, HTTP 401-Fehler, HTTP 403-Fehler, HTTP 404-Fehler und HTTP 406-Fehler konfiguriert werden. Weitere Informationen zu diesen Metriken finden Sie unter [Überwachen von Websites][Überwachen].

##Webaufträge
Auf der Verwaltungsseite "Webaufträge" können Sie Aufgaben für Ihre Website bei Bedarf, geplant oder kontinuierlich ausführen lassen. Weitere Informationen finden Sie unter [Verwenden der WebJobs-Funktion auf Microsoft Azure-Websites](http://www.windowsazure.com/de-de/documentation/articles/web-sites-create-web-jobs/).

##Konfigurieren
Die Verwaltungsseite **Konfigurieren** dient zum Festlegen von anwendungsspezifischen Einstellungen.

Einzelheiten finden Sie unter [Konfigurieren von Websites][Konfigurieren].


##Skalieren
Auf der Verwaltungsseite **Skalieren** können Sie den Webskalierungsgruppen-Modus (**Kostenlos**, **Freigegeben**, **Basic** oder **Standard**) festlegen. **Freigegeben**, **Basic** und **Standard** bieten höheren Durchsatz und bessere Leistung. **In den Modi Freigegeben**, **Basic** und **Standard** können Sie die **Instanzanzahl** erhöhen. Dies ist die Anzahl der virtuellen Computer, die von Ihrer Website und anderen Websites in derselben Webskalierungsgruppe verwendet werden.
 
Im Modus **Standard** können Sie außerdem die Anzahl der Kerne und die Arbeitsspeicherkapazität jeder Instanz erhöhen, indem Sie den Wert von **Instanzgröße** ändern.  Zur Steigerung der Kosteneffizienz können Sie die Option **Automatisch skalieren** verwenden, damit Microsoft Azure Ihrer Website Ressourcen dynamisch zuweist. 

Weitere Informationen zur Konfiguration von Skalierungsoptionen für eine Website finden Sie unter [Skalieren von Websites][Skalieren].

##Verknüpfte Ressourcen
Die Verwaltungsseite **Verknüpfte Ressourcen** enthält eine Liste der Microsoft Azure-Ressourcen, die Ihre Website verwendet, darunter SQL-Datenbanken, MySQL-Datenbanken und Azure-Speicherkonten. Klicken Sie auf den Namen der Ressource, um sie zu verwalten.

##Sicherungen
Auf der Verwaltungsseite **Sicherungen** können Sie automatisierte oder manuelle Sicherungen der Website erstellen, die Website in einem vorherigen Zustand wiederherstellen oder eine neue Website basierend auf einer Sicherung erstellen. Weitere Informationen finden Sie unter [Sicherungen von Microsoft Azure-Websites](http://www.windowsazure.com/de-de/documentation/articles/web-sites-backup/) und unter [Wiederherstellen einer Microsoft Azure-Website](http://www.windowsazure.com/de-de/documentation/articles/web-sites-restore/).

##Symbole auf der Verwaltungsseite
Symbole werden unten auf jeder Verwaltungsseite der Website angezeigt. Verschiedene dieser Symbole sind auf mehreren Seiten vorhanden, und einige Symbole werden nur auf bestimmten Seiten angezeigt.  Die folgenden Symbole werden unten auf der Verwaltungsseite **Dashboard** angezeigt:

- **Durchsuchen** - Öffnet die Standardseite der Website.
- **Beenden** - Hält die Website an.
- **Neu starten** - Startet die Website neu.
- **Domänen verwalten** - Ordnet der jeweiligen Website eine Domäne zu. Ist im Skalierungsmodus **Kostenlos** nicht für Websites verfügbar.
- **Löschen** - Löscht die Website.
- **WebMatrix** - Öffnet unterstützte Websites in WebMatrix, sodass Sie Änderungen an der Website vornehmen können und diese auf der Website in Microsoft Azure veröffentlichen können.

Die folgenden Symbole werden nicht unten auf der Verwaltungsseite **Dashboard** angezeigt, aber auf anderen Verwaltungsseiten, um bestimmte Aufgaben auszuführen:

- **Hinzufügen von Metriken** - Unten auf der Verwaltungsseite **Überwachen** können Sie mit diesem Symbol Metriken zum Diagramm hinzufügen, das auf der Verwaltungsseite "Überwachen" angezeigt wird.
- **Verknüpfen** - Unten auf der Verwaltungsseite **Verknüpfte Ressourcen** können Sie mit diesem Symbol Verwaltungslinks zu anderen Microsoft
-  Azure-Ressourcen erstellen. Wenn Ihre Website beispielsweise auf eine SQL-Datenbank zugreift, können Sie einen Verwaltungslink zur Datenbankressource herstellen, indem Sie auf **Verknüpfen** klicken.


## FTP-Anmeldeinformationen

Es gibt zwei Sätze von FTP-Anmeldeinformationen, die Sie verwenden können: Anmeldeinformationen für die  *deployment* und Anmeldeinformationen für die  *publishing profile*.  Im Folgenden werden die Hauptunterschiede erläutert:

**Anmeldeinformationen für die Bereitstellung**

- Gehören zu einem Microsoft-Konto. 
- Können zur Bereitstellung auf jeder beliebigen Website verwendet werden, in allen Abonnements, die zum Konto gehören. 
- Der Benutzername und das Kennwort werden von Ihnen ausgewählt.
- Werden normalerweise für Git- oder FTP-Bereitstellungen verwendet.

	 
**Anmeldeinformationen für das Veröffentlichungsprofil**

- Gehören zu einer bestimmten Website. 
- Der Benutzername und das Kennwort werden nicht von Ihnen ausgewählt.
- Werden normalerweise für Web Deploy verwendet, sind aber auch für FTP einsetzbar.


Sie können beide Sätze von Anmeldeinformationen verwenden. Die FTP- und FTPS-Hostnamen werden auf dem Dashboard unter **Auf einen Blick** aufgeführt.


### Verwenden der Anmeldeinformationen für die Bereitstellung

So richten Sie Anmeldeinformationen für die Bereitstellung ein 

1.	Navigieren Sie im Verwaltungsportal zur Seite **Dashboard** Ihrer Website.
2.	Klicken Sie auf **Anmeldeinformationen für die Bereitstellung einrichten**.
3.	Geben Sie im Dialogfeld einen Benutzernamen und ein Kennwort ein.

Hinweis: Wenn in Schritt 2 bereits Anmeldeinformationen für die Bereitstellung vorhanden sind, zeigt das Portal **Anmeldeinformationen für die Bereitstellung zurücksetzen** an. Klicken Sie auf diese Option, um ein neues Kennwort festzulegen oder den Benutzernamen zu ändern.

Anmeldeinformationen für die Bereitstellung gehören zu einem Microsoft-Konto. Wenn Sie den Benutzernamen oder das Kennwort ändern, gilt die Änderung für alle Websites, die zum Konto gehören. Wenn ein Azure-Abonnement mehrere Administratoren hat, verfügt jede Person über ihre eigenen Anmeldeinformationen. 
Der vollständige FTP-Benutzername ist "website\username".  Dies wird im Portal unter **Auf einem Blick** unter **Bereitstellungs-/FTP-Benutzer** aufgeführt.


### Verwenden der Anmeldeinformationen für das Veröffentlichungsprofil

Jede Website verfügt über eigene Anmeldeinformationen für das Veröffentlichungsprofil. So zeigen Sie diese Anmeldeinformationen an

1.	Navigieren Sie im Verwaltungsportal zur Seite **Dashboard** Ihrer Website.
2.	Klicken Sie auf **Veröffentlichungsprofil herunterladen**.

Die Datei mit dem Veröffentlichungsprofil ist eine XML-Datei. Sie enthält zwei Profile, eines für Web Deploy und ein weiteres für FTP.

<pre>
&lt;publishData&gt;
  &lt;publishProfile
    profileName="contoso - Web Deploy"
    publishMethod="MSDeploy"
    publishUrl="contoso.scm.azurewebsites.net:443"
    msdeploySite="contoso"
    userName="$contoso"
    userPWD="abc1234..."
    destinationAppUrl="http://contoso.azurewebsites.net"
    SQLServerDBConnectionString=""
    mySQLDBConnectionString=""
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
  &lt;publishProfile 
    profileName="contoso - FTP" 
    <mark>publishMethod="FTP"</mark> 
    publishUrl="ftp://waws-prod-bay-003.ftp.azurewebsites.windows.net/site/wwwroot" 
    ftpPassiveMode="True" 
    <mark>userName="contoso\$contoso"</mark> 
    <mark>userPWD=" abc1234..."</mark>  
    destinationAppUrl="http://contoso.azurewebsites.net" 
    SQLServerDBConnectionString="" 
    mySQLDBConnectionString="" 
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
&lt;/publishData&gt;
</pre>

Suchen Sie nach dem Profil mit <code>publishMethod="FTP"</code>.  Der Benutzername wird unter <code>userName</code>, das Kennwort wird unter <code>userPWD</code> aufgeführt.

Um das Kennwort zurückzusetzen, klicken Sie auf **Anmeldeinformationen des Veröffentlichungsprofils zurücksetzen**. Um die neuen Anmeldeinformationen abzurufen, laden Sie das Veröffentlichungsprofil erneut herunter. Die Anmeldeinformationen des Veröffentlichungsprofils gehören zur Website. Jede Website verfügt über ein eigenes Veröffentlichungsprofil.



<!-- LINKS -->
[mswebmatrix]:http://go.microsoft.com/fwlink/?LinkID=226244

[azuresdk]:http://go.microsoft.com/fwlink/?LinkId=246928

[Konfigurieren]: http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-configure-websites

[Überwachen]: http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-monitor-websites/

[Skalieren]: http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-scale-websites


<!-- Anchors. -->
[FTP-Anmeldeinformationen]: #ftp-credentials





<!--HONumber=42-->
