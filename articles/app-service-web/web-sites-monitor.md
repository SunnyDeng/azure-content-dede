<properties
	pageTitle="Überwachen von Web-Apps in Azure App Service"
	description="Dieses Thema beschreibt die Überwachung von Web-Apps in Azure App Service mithilfe des Verwaltungsportals."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="cephalin"/>

#<a name="howtomonitor"></a>Überwachen von Web-Apps in Azure App Service

[App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) bieten über die Verwaltungsseite "Überwachung" Überwachungsfunktionen für die Standard- und Premium-Pläne von App Service. Die Verwaltungsseite "Überwachung" enthält Leistungsstatistiken für eine Web-App, die unten beschrieben sind.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a name="websitemetrics"></a>Vorgehensweise: Hinzufügen von Web-App-Kennzahlen

1. Klicken Sie im [Azure-Portal](https://manage.windowsazure.com) auf der Seite der Web-App auf die Registerkarte **Überwachen**, um die Verwaltungsseite **Überwachung** anzuzeigen. Standardmäßig zeigt das Diagramm auf der Seite **Monitor** dieselben Kennzahlen wie das Diagramm auf der Seite **Dashboard** an.

2. Zum Anzeigen zusätzlicher Metriken für die Web-App klicken Sie unten auf der Seite auf **Metriken hinzufügen**, um das Dialogfeld **Metriken auswählen** anzuzeigen.

3. Wählen Sie zusätzliche Kennzahlen zum Anzeigen auf der Seite **Monitor** durch Anklicken aus.

4. Nachdem Sie die gewünschten Metriken für die Seite **Monitor** ausgewählt haben, klicken Sie unten auf das Häkchen.

5. Nachdem Sie die Metriken zur Seite **Monitor** hinzugefügt haben, klicken Sie zum Aktivieren/Deaktivieren auf die Optionsfelder neben jeder Kennzahl, um sie im Diagramm oben auf der Seite hinzuzufügen bzw. zu entfernen.

6. Um Kennzahlen von der Seite **Monitor** zu entfernen, wählen Sie die zu entfernende Kennzahl aus und klicken auf das Symbol **Metrik löschen** unten auf der Seite.

##<a name="howtoreceivealerts"></a>Vorgehensweise: Empfangen von Warnungen mittels Web-App-Kennzahlen

Im Web-App-Modus **Standard** können Sie basierend auf den Web-App-Überwachungskennzahlen Warnungen empfangen. Zur Verwendung der Warnfunktion müssen Sie zuerst einen Web-Endpunkt zur Überwachung auswählen, was Sie im Abschnitt **Monitoring** der Seite **Configure** tun können. Sie können auch festlegen, dass eine E-Mail gesendet wird, wenn eine ausgewählte Metrik den angegebenen Wert erreicht. Weitere Informationen finden Sie unter [Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

##<a name="howtoviewusage"></a>Vorgehensweise: Anzeigen der Nutzungskontingente für eine Web-App

Web-Apps können im [Azure-Portal](https://manage.windowsazure.com) auf der Verwaltungsseite **Skalierung** der Web-App zur Ausführung entweder im Modus **Shared** oder **Standard** konfiguriert werden. Jedes Azure-Abonnement hat Zugriff auf einen Ressourcenpool, der für die Ausführung von bis zu 100 Web-Apps pro Region im Modus **Freigegeben** bereitgestellt wird. Der für jedes Web-App-Abonnement für diesen Zweck bereitgestellte Ressourcenpool wird in derselben geografischen Region auch von anderen Web-Apps genutzt, die im Modus **Freigegeben** konfiguriert sind. Da diese Ressourcen von anderen Web-Apps ebenfalls genutzt werden, verfügen alle Abonnements über begrenzte Ressourcennutzung. Die Höchstgrenzen für die Ressourcennutzung von Abonnements werden als Nutzungskontingente bezeichnet und sind im Abschnitt "Nutzungsübersicht" der Verwaltungsseite **Dashboard** jeder Web-App aufgelistet.

>[AZURE.NOTE]Wenn eine Web-App für die Ausführung im Modus **Standard** konfiguriert ist, werden ihr dedizierte Ressourcen entsprechend der Größe **Klein** (Standard), **Mittel** oder **Groß** des virtuellen Computers zugewiesen, die in der Tabelle unter [Größen virtueller Computer und Cloud-Dienste für Azure][vmsizes] aufgelistet sind. Es gibt kein Limit, wie viele Ressourcen ein Abonnement zum Ausführen von Web-Apps im **Standard**-Modus verwenden kann. Die Anzahl der im **Standard**-Modus erstellten Web-Apps pro Region ist jedoch auf 500 begrenzt.

### Vorgehensweise: Anzeigen der Nutzungskontingente für Web-Apps, die für den Modus "Freigegeben" konfiguriert sind ###
Um zu bestimmen, in welchem Umfang eine Web-App sich auf die Ressourcennutzungskontingente auswirkt, führen Sie folgende Schritte aus:

1. Öffnen Sie die Verwaltungsseite **Dashboard** der Web-App im [Azure-Portal](https://manage.windowsazure.com).
2. Im Abschnitt **Nutzungsübersicht** werden die Nutzungskontingente für den jeweiligen [App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Plan angezeigt, der eine Teilmenge von Folgendem darstellt:
	-	**Ausgehende Daten**, **CPU-Zeit** und **Arbeitsspeicher** – Wird das Kontingent überschritten, beendet Azure die Web-App für den Rest des aktuellen Kontingentintervalls. Azure startet die Web-Apps zu Beginn des nächsten Kontingentintervalls erneut.
	-	**Dateisystemspeicher** - Wenn dieses Kontingent erreicht wird, bleibt der Dateisystemspeicher für Lesevorgänge weiterhin erreichbar. Alle Schreibvorgänge sind jedoch blockiert, einschließlich derjenigen, die für normale Web-App-Aktivitäten erforderlich sind. Schreibvorgänge werden wieder aufgenommen, wenn Sie die Dateinutzung verringern oder die Web-App in einen App Service-Plan mit einem höheren Kontingent verschieben.
	-	**Verknüpfte Ressourcen** - Kontingente für etwaige verknüpfte Ressourcen in der Web-App wie etwa eine Datenbank oder Speicher werden hier ebenfalls angezeigt.

	Einige Kontingente lassen sich pro Webhostingplan nutzen, andere pro Website. Detailinformationen zu Nutzungskontingenten für jeden Webhostingplan finden Sie unter [Websitelimits](azure-subscription-service-limits.md#websiteslimits).

##<a name="resourceusage"></a> Vorgehensweise: Kontingentüberschreitungen vermeiden

Kontingente sind keine Angelegenheit von Leistung oder Kosten, sondern stellen die Art und Weise da, wie Azure die Ressourcennutzung in einem Umfeld mit vielen Mandanten handhabt. So wird die übermäßige Nutzung von Ressourcen durch einzelne Mandanten unterbunden. Weil das Überschreiten Ihres Kontingents Ausfallzeiten oder eingeschränkte Funktionalität Ihrer Web-App bedeutet, sollten Sie Folgendes berücksichtigen, damit Ihre Website funktionsfähig bleibt, wenn Sie dabei sind, das Kontingent auszuschöpfen:

- Verschieben Sie die Web-App(s) in einen größeren App Service-Plan und nutzen Sie das höhere Kontingent dort. Das einzige Kontingent bei den Plänen **Basic** und **Standard** gilt für den Dateisystemspeicher.
- Je mehr die Anzahl der Instanzen einer Web-App wächst, desto wahrscheinlicher werden die gemeinsam genutzten Ressourcenkontingente ausgeschöpft. Wenn machbar, können Sie zusätzliche Instanzen einer Web-App zurückfahren, wenn eine Überschreitung der gemeinsam genutzten Ressourcenkontingente droht.

##<a name="howtoconfigdiagnostics"></a>Vorgehensweise: Konfigurieren der Diagnose und Herunterladen von Protokollen für eine Web-App

Diagnosen werden auf der Registerkarte **Konfigurieren** für die Web-App im [Azure-Portal](https://manage.windowsazure.com) aktiviert. Es gibt zwei Arten von Diagnosen: **Anwendungsdiagnose** und **Websitediagnose**.

#### Anwendungsdiagnose ####

Der Abschnitt **Anwendungsdiagnose** auf der Verwaltungsseite **Konfigurieren** steuert die Protokollierung der von der Anwendung erzeugten Informationen. Dies ist nützlich beim Protokollieren von Ereignissen, die innerhalb einer Anwendung auftreten. Wenn also ein Fehler in der Anwendung auftritt, können Sie beispielsweise einen benutzerfreundlichen Fehler anzeigen und die ausführlicheren Fehlerinformationen zur späteren Analyse in das Protokoll schreiben.

Sie können die folgenden Anwendungsdiagnose-Optionen aktivieren oder deaktivieren:

- **Anwendungsprotokollierung (Dateisystem)** - Aktiviert die Protokollierung der von der Anwendung erzeugten Informationen. Das Feld **Protokolliergrad** bestimmt, ob Fehler, Warnmeldungen oder Informationen protokolliert werden. Sie können auch "Verbose" auswählen, um alle von der Anwendung erzeugten Informationen zu protokollieren.

	Die von dieser Einstellung erzeugten Protokolle werden im Dateisystem der Web-App gespeichert und können mithilfe der unter **Herunterladen von Protokolldateien für eine Website** beschriebenen Schritte heruntergeladen werden.

- **Application Logging (Table Storage)** - Aktiviert die Protokollierung der von der Anwendung erzeugten Informationen, ähnlich wie die Option "Application Logging (File System)". Die Protokollinformationen werden jedoch in einer Tabelle im Azure-Speicherkonto gespeichert.

	Zur Angabe des Azure-Speicherkontos und der Tabelle aktivieren Sie **Ein**, wählen **Protokolliergrad** aus und legen **Tabellenspeicher verwalten** fest. Geben Sie die zu verwendende Tabelle und das Speicherkonto an, oder erstellen Sie eine neue Tabelle.

	Die in der Tabelle gespeicherten Protokollinformationen können über einen Azure-Speicherclient abgerufen werden.

- **Anwendungsprotokollierung (BLOB-Speicher)** - Aktiviert die Protokollierung der von der Anwendung erzeugten Informationen, ähnlich wie die Option "Anwendungsprotokollierung (Tabellenspeicher)". Die Protokollinformationen werden jedoch in einem Blob im Azure-Speicherkonto gespeichert.

	Zur Angabe des Azure-Speicherkontos und des Blobs aktivieren Sie **Ein**, wählen **Protokolliergrad** aus und legen **BLOB-Speicher verwalten** fest. Geben Sie das Speicherkonto, den Blob-Container und den zu verwendenden Blob-Namen an, oder erstellen Sie einen neuen Container mit Blob.

Weitere Informationen zu Azure-Speicherkonten finden Sie unter [Verwalten von Speicherkonten](/manage/services/storage/how-to-manage-a-storage-account/).

> [AZURE.NOTE]Anwendungsprotokollierung in einer Tabelle oder einem Blob-Speicher wird nur für .NET-Anwendungen unterstützt.

Da die Anwendungsprotokollierung im Speicher die Verwendung eines Speicherclients zum Anzeigen der Protokolldaten erfordert, ist es hilfreich, wenn Sie eine Anwendung oder einen Dienst verwenden, die bzw. der Daten direkt aus der Azure-Tabelle oder dem Blob-Speicher lesen und verarbeiten kann. Bei der Protokollierung im Dateisystem werden Dateien erzeugt, die über FTP oder andere Dienstprogramme auf Ihren lokalen Computer heruntergeladen werden können, wie später in diesem Abschnitt beschrieben wird.

**Anwendungsdiagnose (Dateisystem)**, **Anwendungsdiagnose (Tabellenspeicher)** und **Anwendungsdiagnose (BLOB-Speicher)** können parallel aktiviert werden und über individuelle Protokolliergrade aufweisen. Sie können beispielsweise Fehler und Warnmeldungen als langfristige Protokollierungslösung im Speicher protokollieren, während die Dateisystemprotokollierung nach der Instrumentierung des Anwendungscodes zum Zweck der Fehlerbehebung ausführlich erfolgt.

Diagnosen können auch über die Azure PowerShell mit dem Cmdlet **Set-AzureWebsite** aktiviert werden. Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

> [AZURE.NOTE]Die Anwendungsprotokollierung beruht auf Protokollinformationen, die von Ihrer Anwendung erzeugt werden. Die verwendete Methode zum Erzeugen von Protokollinformationen sowie das Format der Informationen hängt von der Sprache ab, in der Ihre Anwendung geschrieben ist. Erläuterungen zur sprachspezifischen Verwendung der Anwendungsprotokollierung finden Sie in den folgenden Artikeln:
>
> - **.NET** – [Problembehandlung von Web-Apps in Azure App Service in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
> - **Node.js** - [Debuggen einer Node.js-Anwendung auf Azure-Websites](web-sites-nodejs-debug.md)
>
> Anwendungsprotokollierung in einer Tabelle oder einem Blob-Speicher wird nur für .NET-Anwendungen unterstützt.

#### Site Diagnostics ####

Der Abschnitt **Website-Diagnose** der Verwaltungsseite **Konfigurieren** steuert die vom Webserver durchgeführte Protokollierung, wie z. B. das Protokollieren von Webanfragen, Fehler bei der Seitenbereitstellung und wie lange es dauert, eine Seite bereitzustellen. Sie können die folgenden Optionen aktivieren oder deaktivieren:

- **Webserverprotokollierung** - Aktivieren Sie die Webserverprotokollierung, um Web-App-Protokolle im erweiterten W3C-Protokolldateiformat zu speichern. Die Webserverprotokollierung erzeugt einen Datensatz aller eingehenden Anfragen an die Web-App, mit Informationen wie Client-IP-Adresse, angeforderte URI, HTTP-Statuscode der Antwort sowie Benutzer-Agent-Zeichenfolge des Clients. Sie können die Protokolle auf einem Azure-Speicherkonto oder im Dateisystem speichern.

 Um die Webserverprotokolle in einem Azure-Speicherkonto zu speichern, wählen Sie **Speicher** und dann **Speicher verwalten** aus, um ein Speicherkonto und einen Azure Blob-Container anzugeben, in dem die Protokolle gespeichert werden. Weitere Informationen zu Azure-Speicherkonten finden Sie unter [Verwalten von Speicherkonten](/manage/services/storage/how-to-manage-a-storage-account/).

   Um die Webserverprotokolle im Dateisystem zu speichern, wählen Sie **Dateisystem** aus. Daraufhin wird das Feld **Kontingent** aktiviert, in dem Sie den maximalen Speicherplatz für die Protokolldateien angeben können. Die Mindestgröße beträgt 25 MB, das Maximum 100 MB. Die Standardgröße ist 35 MB.

 Standardmäßig werden Webserverprotokolle nie gelöscht. Wenn Sie einen Zeitraum angeben möchten, nach dem Protokolle automatisch gelöscht werden, wählen Sie **Aufbewahrung festlegen** aus, und geben Sie unter **Aufbewahrungsdauer** die Anzahl der Tage ein, für die Protokolle aufbewahrt werden sollen. Diese Einstellung ist für die Azure Speicher- oder Dateisystem-Option verfügbar.

- **Detaillierte Fehlermeldungen** - Aktiviert die detaillierte Fehlerprotokollierung, um zusätzliche Informationen über HTTP-Fehler zu protokollieren (höhere Statuscodes als 400).

- **Ablaufverfolgung für Anforderungsfehler** - Aktiviert die Verfolgung fehlgeschlagener Anforderungen, um Informationen zu fehlgeschlagenen Clientanforderungen zu erfassen, wie HTTP-400-Statuscodes. Die Verfolgung fehlgeschlagener Anforderungen erzeugt ein XML-Dokument, in dem man verfolgen kann, welche Module die Anforderung in IIS durchlaufen hat, die vom Modul zurückgegebenen Details sowie den Zeitpunkt des Modulaufrufs. Diese Informationen können verwendet werden, um zu isolieren, in welcher Komponente der Fehler aufgetreten ist.


Klicken Sie nach dem Aktivieren der Diagnose für eine Web-App unten auf der Verwaltungsseite **Konfigurieren** auf das Symbol **Speichern**, um die von Ihnen festgelegten Optionen anzuwenden.

> [AZURE.IMPORTANT]Detaillierte Fehlermeldungen und Ablaufverfolgung für Anforderungsfehler belasten eine Web-App erheblich. Wir empfehlen, diese Funktionen wieder zu deaktivieren, nachdem Sie die zu behebenden Probleme reproduziert haben.

### Erweiterte Konfiguration ###

Die Diagnose kann weiter angepasst werden, indem Sie Schlüssel/Wert-Paare im Abschnitt **App-Einstellungen** der Verwaltungsseite **Konfigurieren** hinzufügen. Folgende Einstellungen können über **App-Einstellungen** konfiguriert werden:

**DIAGNOSTICS\_TEXTTRACELOGDIRECTORY**

- Das Verzeichnis, in dem die Anwendungsprotokolle gespeichert werden, in Bezug auf das Web-Stammverzeichnis.

- Standardwert: ..\\..\\LogFiles\\Application

**DIAGNOSTICS\_TEXTTRACEMAXBUFFERSIZEBYTES**

- Die maximale Puffergröße für das Erfassen von Anwendungsprotokollen. Die Informationen werden zunächst in den Puffer geschrieben, bevor sie in die Datei oder den Speicher übertragen werden. Wenn neue Informationen in den Puffer geschrieben werden, bevor dieser geleert (übertragen) wurde, können die zuvor erfassten Informationen verloren gehen. Falls Ihre Anwendung große Mengen von Protokollinformationen erzeugt, sollten Sie die Puffergröße erhöhen.

- Standardwert: 10 MB

**DIAGNOSTICS\_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- Die maximale Größe des Ordners **Application**, in welchem die in die Datei geschriebenen Anwendungsdiagnosen gespeichert werden.

- Standardwert: 1 MB

###Herunterladen von Protokolldateien für eine Web-App

Protokolldateien können per FTP, mit Azure PowerShell oder mit der Azure-Befehlszeilenschnittstelle heruntergeladen werden.

**FTP**

1. Öffnen Sie die Verwaltungsseite **Dashboard** der Web-App im [Azure-Portal](https://manage.windowsazure.com), und notieren Sie die unter **Diagnoseprotokolle** aufgelistete FTP-Website sowie das unter **Bereitstellungsbenutzer** aufgeführte Konto. Auf der FTP-Site befinden sich die Protokolldateien, und das unter "Deployment User" aufgelistete Konto wird zur Authentifizierung bei der FTP-Site verwendet.
2. Falls Sie noch keine Bereitstellungsanmeldeinformationen erstellt haben, wird das Konto unter **Bereitstellungsbenutzer** als **Nicht festgelegt** aufgeführt. In diesem Fall müssen Sie die Bereitstellungsanmeldeinformationen wie im Abschnitt "Zurücksetzen der Bereitstellungsanmeldeinformationen" des Dashboards beschrieben erstellen, da diese Anmeldeinformationen zur Authentifizierung bei der FTP-Site erforderlich sind, wo die Protokolldateien gespeichert werden. Azure unterstützt keine Authentifizierung bei der FTP-Site mittels Live ID-Anmeldung.
3. Verwenden Sie einen FTP-Client wie [FileZilla][fzilla] für die Verbindung mit der FTP-Site. Ein FTP-Client vereinfacht die Eingabe der Anmeldeinformationen und das Anzeigen der Ordner auf der FTP-Site gegenüber der Verwendung eines Browsers.
4. Kopieren Sie die Protokolldateien von der FTP-Site auf Ihren lokalen Computer.

**Azure PowerShell**

1. Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Azure PowerShell**. Klicken Sie mit der rechten Maustaste auf den Eintrag **Azure PowerShell**, und wählen Sie **Als Administrator ausführen** aus.

	> [AZURE.NOTE]Falls **Azure PowerShell** nicht installiert ist, finden Sie Informationen zur Installation und Konfiguration unter [Erste Schritte mit Azure PowerShell-Cmdlets](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

2. Geben Sie an der Eingabeaufforderung von Azure PowerShell den folgenden Befehl ein, um die Protokolldateien herunterzuladen:

		Save-AzureWebSiteLog -Name webappname

	Daraufhin werden die Protokolldateien für die Web-App heruntergeladen, die unter **webappname** angegeben ist, und in der Datei **log.zip** im aktuellen Verzeichnis gespeichert.

	Sie können auch einen Live-Stream der Protokollereignisse mit dem folgenden Befehl anzeigen:

		Get-AzureWebSiteLog -Name webappname -Tail

	Daraufhin werden die Protokollinformationen an der Eingabeaufforderung von Azure PowerShell angezeigt, sobald sie auftreten.

**Azure-Befehlszeilenschnittstelle**

Öffnen Sie eine neue Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung, und verwenden Sie den folgenden Befehl zum Herunterladen der Protokolldateien:

	azure site log download webappname

Daraufhin werden die Protokolldateien für die Web-App heruntergeladen, die unter **webappname** angegeben ist, und in der Datei **log.zip** im aktuellen Verzeichnis gespeichert.

Sie können auch einen Live-Stream der Protokollereignisse mit dem folgenden Befehl anzeigen:

	azure site log tail webappname

Daraufhin werden die Protokollinformationen an der Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung angezeigt, von der der Befehl ausgeführt wird.

> [AZURE.NOTE]Falls der Befehl **azure** nicht installiert ist, finden Sie Informationen zur Installation und Konfiguration unter [Verwenden der Azure-CLI](../virtual-machines-command-line-tools.md).

### Lesen der Protokolldateien ###

Die Protokolldateien, die erzeugt werden, nachdem Sie Protokollierung und/oder Verfolgung für eine Web-App aktiviert haben, sind je nach der auf der Verwaltungsseite "Konfigurieren" für die Web-App eingestellten Protokollierungs-/Verfolgungsebene unterschiedlich. Nachfolgend ist das Verzeichnis der Protokolldateien und die Art der Analyse angegeben:

**Protokolldateityp: Anwendungsprotokollierung**

- Verzeichnis: /LogFiles/Application/. Dieser Ordner enthält eine oder mehrere Textdateien mit Informationen, die bei der Anwendungsprotokollierung erzeugt wurden. Zu den protokollierten Informationen gehören Datum und Zeit, Prozess-ID (PID) der Anwendung sowie der von der Anwendungsinstrumentierung erzeugte Wert.

- Lesen Sie Dateien mit: Einem Texteditor oder Parser, der die von der Anwendung erzeugten Werte versteht

**Protokolldateityp: Ablaufverfolgung für Anforderungsfehler**

- Speicherort: /LogFiles/W3SVC#########/. Dieser Ordner enthält eine XSL-Datei und eine oder mehrere XML-Dateien. Vergewissern Sie sich, dass Sie die XSL-Datei in dasselbe Verzeichnis wie die XML-Datei(en) herunterladen, da die XSL-Datei die Funktionalität zum Formatieren und Filtern des Inhalts der XML-Datei(en) zur Anzeige in Internet Explorer zur Verfügung stellt.

- Lesen Sie Dateien mit: Internet Explorer

**Protokolldateityp: Detaillierte Fehlerprotokollierung**

- Speicherort: /LogFiles/DetailedErrors/. Der Ordner "/LogFiles/DetailedErrors/" enthält eine oder mehrere HTM-Dateien, die umfangreiche Informationen zu aufgetretenen HTTP-Fehlern bereitstellen.

- Lesen von Dateien mit: Webbrowser

Die HTM-Dateien umfassen die folgenden Abschnitte:

- **Ausführliche Fehlerinformationen:** Enthält Informationen zum Fehler, z. B. <em>Modul</em>, <em>Handler</em>, <em>Fehlercode</em> und <em>angeforderte URL</em>.

- **Wahrscheinliche Ursachen:** Listet verschiedene mögliche Fehlerursachen auf.

- **Sie können Folgendes versuchen:** Listet mögliche Lösungen für das im Fehler gemeldete Problem auf.

- **Links und weitere Informationen**: Bietet zusätzliche zusammenfassende Informationen zum Fehler sowie ggf. Links zu anderen Ressourcen wie Microsoft Knowledge Base-Artikeln.

**Protokolldateityp: Webserverprotokollierung**

- Speicherort: /LogFiles/http/RawLogs. Die in den Dateien gespeicherten Informationen sind im [erweiterten W3C-Protokollformat](http://go.microsoft.com/fwlink/?LinkID=90561) formatiert. Die Felder "s-computername", "s-ip" und "cs-version" werden von Azure-Web-Apps nicht verwendet.

- Lesen Sie Dateien mit: Log Parser. Dient zum Analysieren und Abfragen von IIS-Protokolldateien. Log Parser 2.2 ist im Microsoft Download Center unter <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a> verfügbar.


##<a name="webendpointstatus"></a> Vorgehensweise: Überwachen des Web-Endpunktstatus

Mit dieser Funktion, die im **Standard**-Modus verfügbar ist, können Sie bis zu 2 Endpunkte von 3 geografischen Standorten aus überwachen.

Endpunktüberwachung konfiguriert Webtests von geografisch verteilten Standorten zum Testen der Reaktionszeit und der Verfügbarkeit von Web-URLs. Der Test führt einen HTTP GET-Aufruf der Web-URL durch, um die Reaktionszeit und Verfügbarkeit von jedem Standort zu bestimmen. Jeder konfigurierte Standort führt alle fünf Minuten einen Test aus.

Die Verfügbarkeit wird mit HTTP-Antwortcodes überwacht, und die Reaktionszeit wird in Millisekunden gemessen. Die Verfügbarkeit gilt als 100 %, wenn die Reaktionszeit unter 30 Sekunden beträgt und der HTTP-Statuscode unter 400 liegt. Die Verfügbarkeit beträgt 0%, wenn die Reaktionszeit über 30 Sekunden beträgt und der HTTP-Statuscode über 400 liegt.

Nachdem Sie die Endpunktüberwachung konfiguriert haben, können Sie Details zu den einzelnen Endpunkten aufrufen, um die Reaktionszeit und den Verfügbarkeitsstatus für das Überwachungsintervall von jedem der Teststandorte anzuzeigen. Sie können beispielsweise auch eine Warnungsregel einrichten, wenn der Endpunkt zu lange für eine Reaktion braucht.

**So konfigurieren Sie die Endpunktüberwachung:**

1.	Öffnen Sie **Web- Apps**. Klicken Sie auf den Namen der Web-App, die Sie konfigurieren möchten.
2.	Klicken Sie auf die Registerkarte **Konfigurieren**.
3.     Wechseln Sie zum Abschnitt **Überwachung**, um Ihre Endpunkteinstellungen einzugeben.
4.	Geben Sie einen Namen für den Endpunkt ein.
5.	Geben Sie die URL für den Teil der Web-App ein, den Sie überwachen möchten. Zum Beispiel: [http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive)
6.	Wählen Sie einen oder mehrere geografische Standorte aus der Liste aus.
7.	Sie können optional die vorherigen Schritte wiederholen, um einen zweiten Endpunkt zu erstellen.
8.	Klicken Sie auf **Speichern**. Es kann einige Zeit dauern, bis die Daten der Web-Endpunktüberwachung auf den Registerkarten **Dashboard** und **Monitor** verfügbar sind.

	Gehen Sie folgendermaßen vor, um eine E-Mail-Regel zu erstellen:

9.	Klicken Sie in der Diensteleiste links auf **Verwaltungsdienste**.
10.	Klicken Sie unten auf **Regel hinzufügen**.
11.	Wählen Sie in **Diensttyp** die Option **Web-App** aus und dann die Web-App, für die Sie zuvor die Endpunktüberwachung konfiguriert haben. Klicken Sie auf **Weiter**.
12.	In **Metrik** können Sie jetzt weitere Kennzahlen für den von Ihnen konfigurierten Endpunkt auswählen. Beispiel: **Reaktionszeit (Homepage/USA: IL-Chicago)**. Wählen Sie die Metrik "Reaktionszeit", und geben Sie in **Schwellenwert** den Wert "3" ein. Dadurch werden 3 Sekunden als Grenzwert festlegt.
13.	Wählen Sie**E-Mail an Dienstadministrator und Co-Administratoren senden**. Klicken Sie auf **Fertig stellen**.

	Azure überwacht nun aktiv den Endpunkt und sendet eine E-Mail-Mitteilung, wenn die Antwortzeit 3 Sekunden überschreitet.

Weitere Informationen zur Web-App-Endpunktüberwachung erhalten Sie in den folgenden Videos:

- [Scott Guthrie mit einer Einführung zu Azure-Websites und Einrichtung der Endpunktüberwachung](/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Pflegen von Azure-Websites plus Endpunktüberwachung - mit Stefan Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Azure-Portals gegenüber dem Azure-Vorschauportal finden Sie unter [Referenz zur Navigation im Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715)

[fzilla]: http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]: http://go.microsoft.com/fwlink/?LinkID=309169
 

<!---HONumber=Oct15_HO1-->