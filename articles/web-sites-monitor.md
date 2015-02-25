<properties 
	pageTitle="Überwachen von Websites - Azure-Dienstverwaltung" 
	description="Erfahren Sie, wie Sie Azure-Websites mithilfe der Überwachungsseite im Verwaltungsportal überwachen." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>



#<a name="howtomonitor"></a>Überwachen von Websites

Websites bieten Überwachungsfunktionen über die Verwaltungsseite "Monitor". Die Verwaltungsseite "Monitor" enthält Leistungsstatistiken für eine Website, wie unten beschrieben.

## Inhaltsverzeichnis
- [Vorgehensweise: Hinzufügen von Websitemetriken](#websitemetrics)
- [Vorgehensweise: Empfangen von Warnungen mittels Websitemetriken](#howtoreceivealerts)
- [Vorgehensweise: Anzeigen der Nutzungskontingente für eine Website](#howtoviewusage)
- [Vorgehensweise: Kontingentüberschreitungen vermeiden](#resourceusage)
- [Vorgehensweise: Diagnosekonfiguration und Herunterladen von Protokollen für eine Website](#howtoconfigdiagnostics)
- [Vorgehensweise: Überwachen des Web-Endpunktstatus](#webendpointstatus)

##<a name="websitemetrics"></a>Vorgehensweise: Hinzufügen von Websitemetriken
1. Klicken Sie im [Azure-Verwaltungsportal](http://manage.windowsazure.com/) auf den Verwaltungsseiten der Website auf die Registerkarte **Überwachen**, um die Verwaltungsseite **Monitor** anzuzeigen. Standardmäßig zeigt das Diagramm auf der Seite **Monitor** dieselben Kennzahlen wie das Diagramm auf der Seite **Dashboard** an. 

2. Zum Anzeigen zusätzlicher Metriken für die Website klicken Sie unten auf der Seite auf **Metriken hinzufügen**, um das Dialogfeld **Metriken auswählen** anzuzeigen. 

3. Wählen Sie zusätzliche Kennzahlen zum Anzeigen auf der Seite **Monitor** aus, indem Sie darauf klicken. 

4. Nachdem Sie die gewünschten Metriken für die Seite **Monitor** ausgewählt haben, klicken Sie unten auf das Häkchen. 

5. Nachdem Sie die Metriken zur Seite **Monitor** hinzugefügt haben, klicken Sie zum Aktivieren/Deaktivieren auf die Optionsfelder neben jeder Kennzahl, um sie im Diagramm oben auf der Seite hinzuzufügen bzw. zu entfernen.

6. Um Kennzahlen von der Seite **Monitor** zu entfernen, wählen Sie die zu entfernende Kennzahl aus und klicken auf das Symbol **Metrik löschen** unten auf der Seite.

##<a name="howtoreceivealerts"></a>Vorgehensweise: Empfangen von Warnungen mittels Websitemetriken
Im Websitemodus **Standard** können Sie Warnungen basierend auf den Überwachungskennzahlen der Website empfangen. Zur Verwendung der Warnfunktion müssen Sie zunächst einen Webendpunkt zur Überwachung auswählen, was Sie im Abschnitt **Überwachung** der Seite **Konfigurieren** tun können. Auf der Seite **Einstellungen** des Azure-Verwaltungsportals können Sie anschließend eine Regel erstellen, um eine Warnung auszulösen, wenn die von Ihnen ausgewählte Kennzahl einen festgelegten Wert erreicht. Außerdem können Sie eine E-Mail senden, wenn der Alarm ausgelöst wird. Weitere Informationen finden Sie unter [Vorgehensweise: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure](http://go.microsoft.com/fwlink/?LinkId=309356).  

##<a name="howtoviewusage"></a>Vorgehensweise: Anzeigen der Nutzungskontingente für eine Website

Websites können auf der Verwaltungsseite **Skalieren** zur Ausführung entweder im Websitemodus **Freigegeben** oder **Standard** konfiguriert werden. Jedes Azure-Abonnement hat Zugriff auf einen Ressourcenpool, der für die Ausführung von bis zu 100 Websites pro Region im Websitemodus **Freigegeben** bereitgestellt wird. Der für jedes Websiteabonnement zu diesem Zweck bereitgestellte Ressourcenpool wird auch von anderen Websites in derselben geografischen Region genutzt, die für die Ausführung im Modus **Freigegeben** konfiguriert sind. Da diese Ressourcen von anderen Websites ebenfalls genutzt werden, verfügen alle Abonnements über begrenzte Ressourcennutzung. Die Höchstgrenzen für die Ressourcennutzung von Abonnements werden als Nutzungskontingente bezeichnet und sind im Abschnitt "Nutzungsübersicht" der Verwaltungsseite **Dashboard** jeder Website aufgelistet.

>[AZURE.NOTE] Wenn eine Website für die Ausführung im Modus **Standard** konfiguriert ist, werden ihr dedizierte Ressourcen entsprechend der Größe **Klein** (Standard), **Mittel** oder **Groß** des virtuellen Computers zugewiesen, die in der Tabelle unter [Größen virtueller Computer und Cloud-Dienste für Azure][vmsizes] aufgelistet sind. Es gibt kein Limit, wie viele Ressourcen ein Abonnement zum Ausführen von Websites im Modus **Standard** verwenden kann. Die Anzahl der im Modus **Standard** erstellten Websites pro Region ist jedoch auf 500 begrenzt.
 
### Vorgehensweise: Anzeigen der Nutzungskontingente für Websites, die für den Websitemodus "Freigegeben" konfiguriert sind
Um zu bestimmen, in welchem Umfang eine Website die Ressourcennutzungskontingente belastet, führen Sie folgende Schritte aus:

1. Öffnen Sie die Website-Verwaltungsseite **Dashboard**.
2. Im Abschnitt **Nutzungsübersicht** werden die Nutzungskontingente für den jeweiligen Webhostingplan angezeigt, der eine Teilmenge von Folgendem darstellt:
	-	**Ausgehende Daten**, **CPU-Zeit** und **Arbeitsspeicher** - Wird das Kontingent überschritten, beendet Azure die Website für den Rest des aktuellen Kontingentintervalls. Azure startet die Websites zu Beginn des nächsten Kontingentintervalls.
	-	**Dateisystemspeicher** - Wenn dieses Kontingent erreicht wird, bleibt der Dateisystemspeicher für Lesevorgänge weiterhin erreichbar. Alle Schreibvorgänge sind jedoch blockiert, einschließlich derjenigen, die für normale Websiteaktivitäten erforderlich sind. Schreibvorgänge werden wieder aufgenommen, wenn Sie die Dateinutzung verringern oder die Webseite in einen Webhostingplan mit einem höheren Kontingent verschieben.
	-	**Verknüpfte Ressourcen** - Kontingente für etwaige verknüpfte Ressourcen auf der Website wie etwa eine Datenbank oder Speicher werden hier ebenfalls angezeigt.

	Einige Kontingente lassen sich pro Webhostingplan nutzen, andere pro Website. Detailinformationen zu Nutzungskontingenten für jeden Webhostingplan finden Sie unter [Websitelimits](http://azure.microsoft.com/de-de/documentation/articles/azure-subscription-service-limits/#websiteslimits).


##<a name="resourceusage"></a>Vorgehensweise: Kontingentüberschreitungen vermeiden

Kontingente sind keine Angelegenheit von Leistung oder Kosten, sondern stellen die Art und Weise da, wie Azure die Ressourcennutzung in einem Umfeld mit vielen Mandanten handhabt. So wird die übermäßige Nutzung von Ressourcen durch einzelne Mandanten unterbunden. Weil das Überschreiten Ihres Kontingent Ausfallzeiten oder eingeschränkte Funktionalität Ihrer Website bedeutet, sollten Sie Folgendes berücksichtigen, damit Ihre Website funktionsfähig bleibt, wenn Sie dabei sind, das Kontingent auszuschöpfen:

- Verschieben Sie die Website(s) auf einen größeren Webhostingplan und nutzen Sie das höhere Kontingent dort. Das einzige Kontingent bei den Plänen **Basic** und **Standard** gilt für den Dateisystemspeicher. 
- Je mehr die Anzahl der Instanzen einer Website wächst, desto wahrscheinlicher werden die gemeinsam genutzten Ressourcenkontigente ausgeschöpft. Wenn machbar, können Sie zusätzliche Instanzen einer Website zurückfahren, wenn eine Überschreitung der gemeinsam genutzten Ressourcenkontigente droht.


##<a name="howtoconfigdiagnostics"></a>Vorgehensweise: Diagnosekonfiguration und Herunterladen von Protokollen für eine Website

Diagnosen werden auf der Verwaltungsseite **Konfigurieren** für die Website aktiviert. Es gibt zwei Arten von Diagnosen: **Anwendungsdiagnose** und **Website-Diagnose**.

####Anwendungsdiagnose

Der Abschnitt **Anwendungsdiagnose** auf der Verwaltungsseite **Konfigurieren** steuert die Protokollierung der von der Anwendung erzeugten Informationen. Dies ist nützlich beim Protokollieren von Ereignissen, die innerhalb einer Anwendung auftreten. Wenn also ein Fehler in der Anwendung auftritt, können Sie beispielsweise einen benutzerfreundlichen Fehler anzeigen und die ausführlicheren Fehlerinformationen zur späteren Analyse in das Protokoll schreiben.

Sie können die folgenden Anwendungsdiagnose-Optionen aktivieren oder deaktivieren:

- **Anwendungsprotokollierung (Dateisystem)** - Aktiviert die Protokollierung der von der Anwendung erzeugten Informationen. Das Feld **Protokolliergrad** bestimmt, ob Fehler, Warnmeldungen oder Informationen protokolliert werden. Sie können auch "Ausführlich" auswählen, um alle von der Anwendung erzeugten Informationen zu protokollieren.

	Die durch diese Einstellung erzeugten Protokolle werden im Dateisystem der Website gespeichert und können mithilfe der unten unter **Herunterladen von Protokolldateien für eine Website** beschriebenen Schritte heruntergeladen werden.

- **Anwendungsprotokollierung (Tabellenspeicher)** - Aktiviert die Protokollierung der von der Anwendung erzeugten Informationen, ähnlich wie die Option "Anwendungsprotokollierung (Dateisystem)". Die Protokollinformationen werden jedoch in einer Tabelle im Azure-Speicherkonto gespeichert.

	Zur Angabe des Azure-Speicherkontos und der Tabelle aktivieren Sie **Ein**, wählen **Protokolliergrad** aus und legen **Tabellenspeicher verwalten** fest. Geben Sie die zu verwendende Tabelle und das Speicherkonto an, oder erstellen Sie eine neue Tabelle.

	Die in der Tabelle gespeicherten Protokollinformationen können über einen Azure-Speicherclient abgerufen werden.

- **Anwendungsprotokollierung (BLOB-Speicher)** - Aktiviert die Protokollierung der von der Anwendung erzeugten Informationen, ähnlich wie die Option "Anwendungsprotokollierung (Tabellenspeicher)". Die Protokollinformationen werden jedoch in einem Blob im Azure-Speicherkonto gespeichert.

	Zur Angabe des Azure-Speicherkontos und Blobs aktivieren Sie **Ein**, wählen **Protokolliergrad** aus und legen **Blob-Speicher verwalten** fest. Geben Sie das Speicherkonto, den Blob-Container und den zu verwendenden Blob-Namen an, oder erstellen Sie einen neuen Container mit Blob.

Weitere Informationen zu Azure-Speicherkonten finden Sie unter [Verwalten von Speicherkonten](https://www.windowsazure.com/de-de/manage/services/storage/how-to-manage-a-storage-account/).

> [AZURE.NOTE] Anwendungsprotokollierung in einer Tabelle oder einem Blob-Speicher wird nur für .NET-Anwendungen unterstützt.

Da die Anwendungsprotokollierung im Speicher die Verwendung eines Speicherclients zum Anzeigen der Protokolldaten erfordert, ist es hilfreich, wenn Sie eine Anwendung oder einen Dienst verwenden, die bzw. der Daten direkt aus der Azure-Tabelle oder dem Blob-Speicher lesen und verarbeiten kann. Bei der Protokollierung im Dateisystem werden Dateien erzeugt, die über FTP oder andere Dienstprogramme auf Ihren lokalen Computer heruntergeladen werden können, wie später in diesem Abschnitt beschrieben wird.

> [AZURE.NOTE] **Anwendungsdiagnose (Dateisystem)**, **Anwendungsdiagnose (Tabellenspeicher)** und **Anwendungsdiagnose (BLOB-Speicher)** können parallel aktiviert werden und über individuelle Protokolliergrade aufweisen. Sie können beispielsweise Fehler und Warnmeldungen als langfristige Protokollierungslösung im Speicher protokollieren, während die Dateisystemprotokollierung nach der Instrumentierung des Anwendungscodes zum Zweck der Fehlerbehebung ausführlich erfolgt.

> [AZURE.NOTE] Diagnosen können auch über die Azure PowerShell mit dem Cmdlet **Set-AzureWebsite** aktiviert werden.
> 
> Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](http://www.windowsazure.com/de-de/develop/nodejs/how-to-guides/powershell-cmdlets/).

> [AZURE.NOTE] Die Anwendungsprotokollierung beruht auf Protokollinformationen, die von Ihrer Anwendung erzeugt werden. Die verwendete Methode zum Erzeugen von Protokollinformationen sowie das Format der Informationen hängt von der Sprache ab, in der Ihre Anwendung geschrieben ist. Erläuterungen zur sprachspezifischen Verwendung der Anwendungsprotokollierung finden Sie in den folgenden Artikeln:
> 
> - **.NET** - [Aktivieren der Diagnoseprotokollierung für Azure-Websites](/de-de/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)
> - **Node.js** - [Debuggen einer Node.js-Anwendung auf Azure-Websites](/de-de/develop/nodejs/how-to-guides/Debug-Website/)
> 
> Anwendungsprotokollierung in einer Tabelle oder einem Blob-Speicher wird nur für .NET-Anwendungen unterstützt.

####Website-Diagnose

Der Abschnitt **Website-Diagnose** der Verwaltungsseite **Konfigurieren** steuert die vom Webserver durchgeführte Protokollierung, wie z. B. das Protokollieren von Webanfragen, Fehler bei der Seitenbereitstellung und wie lange es dauert, eine Seite bereitzustellen. Sie können die folgenden Optionen aktivieren oder deaktivieren:

- **Webserverprotokollierung** - Aktivieren Sie die Webserverprotokollierung, um Websiteprotokolle im erweiterten W3C-Protokolldateiformat zu speichern. Die Webserverprotokollierung erzeugt einen Datensatz aller eingehenden Anforderungen an die Website, mit Informationen wie Client-IP-Adresse, angefordertem URI, HTTP-Statuscode der Antwort sowie Benutzer-Agent-Zeichenfolge des Clients. Sie können die Protokolle auf einem Azure-Speicherkonto oder im Dateisystem speichern.

 Um die Webserverprotokolle in einem Azure-Speicherkonto zu speichern, wählen Sie **Speicher** und dann **Speicher verwalten** aus, um ein Speicherkonto und einen Azure Blob-Container anzugeben, in dem die Protokolle gespeichert werden. Weitere Informationen zu Azure-Speicherkonten finden Sie unter [Verwalten von Speicherkonten](https://www.windowsazure.com/de-de/manage/services/storage/how-to-manage-a-storage-account/).

   Um die Webserverprotokolle im Dateisystem zu speichern, wählen Sie **Dateisystem** aus. Daraufhin wird das Feld **Kontingent** aktiviert, in dem Sie den maximalen Speicherplatz für die Protokolldateien angeben können. Die Mindestgröße beträgt 25 MB, das Maximum 100 MB. Die Standardgröße ist 35 MB.

 Standardmäßig werden Webserverprotokolle nie gelöscht. Wenn Sie einen Zeitraum angeben möchten, nach dem Protokolle automatisch gelöscht werden, wählen Sie **Aufbewahrung festlegen** aus, und geben Sie unter **Aufbewahrungsdauer** die Anzahl der Tage ein, für die Protokolle aufbewahrt werden sollen. Diese Einstellung ist für die Azure Speicher- oder Dateisystem-Option verfügbar.

- **Detaillierte Fehlermeldungen** - Aktiviert die detaillierte Fehlerprotokollierung, um zusätzliche Informationen über HTTP-Fehler zu protokollieren (höhere Statuscodes als 400).

- **Ablaufverfolgung für Anforderungsfehler** - Aktiviert die Verfolgung fehlgeschlagener Anforderungen, um Informationen zu fehlgeschlagenen Clientanforderungen zu erfassen, wie HTTP-400-Statuscodes.  Die Verfolgung fehlgeschlagener Anforderungen erzeugt ein XML-Dokument, in dem man verfolgen kann, welche Module die Anforderung in IIS durchlaufen hat, die vom Modul zurückgegebenen Details sowie den Zeitpunkt des Modulaufrufs. Diese Informationen können verwendet werden, um zu isolieren, in welcher Komponente der Fehler aufgetreten ist.


Klicken Sie nach dem Aktivieren der Diagnose für eine Website auf das Symbol **Speichern** unten auf der Verwaltungsseite **Konfigurieren**, um die von Ihnen festgelegten Optionen anzuwenden.

> [AZURE.IMPORTANT] Durch die Protokollierung und Ablaufverfolgung kann die Website erheblich belastet werden. Wir empfehlen, die Protokollierung und Verfolgung wieder zu deaktivieren, nachdem Sie die zu behebenden Probleme reproduziert haben.

###Erweiterte Konfiguration###

Die Diagnose kann weiter angepasst werden, indem Sie Schlüssel/Wert-Paare im Abschnitt **App-Einstellungen** der Verwaltungsseite **Konfigurieren** hinzufügen. Folgende Einstellungen können über **App-Einstellungen** konfiguriert werden:

**DIAGNOSTICS_TEXTTRACELOGDIRECTORY**

- Das Verzeichnis, in dem die Anwendungsprotokolle gespeichert werden, in Bezug auf das Web-Stammverzeichnis.

- Standardwert:  ..\\..\\LogFiles\\Application

**DIAGNOSTICS_TEXTTRACEMAXBUFFERSIZEBYTES**

- Die maximale Puffergröße für das Erfassen von Anwendungsprotokollen. Die Informationen werden zunächst in den Puffer geschrieben, bevor sie in die Datei oder den Speicher übertragen werden. Wenn neue Informationen in den Puffer geschrieben werden, bevor dieser geleert (übertragen) wurde, können die zuvor erfassten Informationen verloren gehen. Falls Ihre Anwendung große Mengen von Protokollinformationen erzeugt, sollten Sie die Puffergröße erhöhen.

- Standardwert: 10 MB

**DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- Die maximale Größe des Ordners **Application**, in dem die in die Datei geschriebenen Anwendungsdiagnosen gespeichert werden.

- Standardwert: 1 MB

###Herunterladen von Protokolldateien für eine Website

Protokolldateien können entweder mit FTP, Azure PowerShell oder den Azure-Befehlszeilentools heruntergeladen werden.

**FTP**

1. Öffnen Sie die Verwaltungsseite **Dashboard** der Website, und notieren Sie die unter **Diagnoseprotokolle** aufgelistete FTP-Website sowie das unter **Bereitstellungsbenutzer** aufgeführte Konto. Auf der FTP-Site befinden sich die Protokolldateien, und das unter "Bereitstellungsbenutzer" aufgelistete Konto wird zur Authentifizierung bei der FTP-Site verwendet.
2. Falls Sie noch keine Bereitstellungsanmeldeinformationen erstellt haben, wird das Konto unter **Bereitstellungsbenutzer** als **Nicht festgelegt** aufgeführt. In diesem Fall müssen Sie die Bereitstellungsanmeldeinformationen wie im Abschnitt "Zurücksetzen der Bereitstellungsanmeldeinformationen" des Dashboards beschrieben erstellen, da diese Anmeldeinformationen zur Authentifizierung bei der FTP-Site erforderlich sind, wo die Protokolldateien gespeichert werden. Azure unterstützt keine Authentifizierung bei der FTP-Site mittels Live ID-Anmeldung.
3. Verwenden Sie einen FTP-Client wie [FileZilla][fzilla] für die Verbindung mit der FTP-Site. Ein FTP-Client vereinfacht die Eingabe der Anmeldeinformationen und das Anzeigen der Ordner auf der FTP-Site gegenüber der Verwendung eines Browsers.
4. Kopieren Sie die Protokolldateien von der FTP-Site auf Ihren lokalen Computer.

**Azure PowerShell**

1. Suchen Sie auf dem **Startbildschirm** oder im **Startmenü** nach **Azure PowerShell**. Klicken Sie mit der rechten Maustaste auf den Eintrag **Azure PowerShell**, und wählen Sie **Als Administrator ausführen** aus.

	> [AZURE.NOTE] Falls **Azure PowerShell** nicht installiert ist, finden Sie Informationen zur Installation und Konfiguration unter [Erste Schritte mit Azure PowerShell-Cmdlets](http://msdn.microsoft.com/de-de/library/windowsazure/jj554332.aspx).

2. Geben Sie an der Eingabeaufforderung von Azure PowerShell den folgenden Befehl ein, um die Protokolldateien herunterzuladen:

		Save-AzureWebSiteLog -Name websitename

	Daraufhin werden die Protokolldateien für die Website heruntergeladen, die unter **Websitename** angegeben ist, und in der Datei **log.zip** im aktuellen Verzeichnis gespeichert.
	
	Sie können auch einen Live-Stream der Protokollereignisse mit dem folgenden Befehl anzeigen:

		Get-AzureWebSiteLog -Name websitename -Tail

	Daraufhin werden die Protokollinformationen an der Eingabeaufforderung von Azure PowerShell angezeigt, sobald sie auftreten.

**Azure-Befehlszeilentools**

Öffnen Sie eine neue Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung, und verwenden Sie den folgenden Befehl zum Herunterladen der Protokolldateien:

	azure site log download websitename

Daraufhin werden die Protokolldateien für die Website heruntergeladen, die unter **Websitename** angegeben ist, und in der Datei **log.zip** im aktuellen Verzeichnis gespeichert.

Sie können auch einen Live-Stream der Protokollereignisse mit dem folgenden Befehl anzeigen:

	azure site log tail websitename

Daraufhin werden die Protokollinformationen an der Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung angezeigt, von der der Befehl ausgeführt wird.

> [AZURE.NOTE] Falls der Befehl **azure** nicht installiert ist, finden Sie Informationen zur Installation und Konfiguration unter [Verwenden der Azure-Befehlszeilentools](http://www.windowsazure.com/de-de/develop/nodejs/how-to-guides/command-line-tools/).

###Lesen der Protokolldateien

Die Protokolldateien, die erzeugt werden, nachdem Sie Protokollierung und/oder Ablaufverfolgung für eine Website aktiviert haben, sind je nach der auf der Verwaltungsseite "Konfigurieren" für die Website eingestellten Protokollierungs-/Ablaufverfolgungsebene unterschiedlich. Nachfolgend ist das Verzeichnis der Protokolldateien und die Art der Analyse angegeben:

**Protokolldateityp: Anwendungsprotokollierung**

- Standort /LogFiles/Application/ Dieser Ordner enthält eine oder mehrere Textdateien mit Informationen, die bei der Anwendungsprotokollierung erzeugt wurden. Zu den protokollierten Informationen gehören Datum und Zeit, Prozess-ID (PID) der Anwendung sowie der von der Anwendungsinstrumentierung erzeugte Wert.

- Lesen Sie Dateien mit: Einem Texteditor oder Parser, der die von der Anwendung erzeugten Werte versteht

**Protokolldateityp: Ablaufverfolgung für Anforderungsfehler**

- Location: /LogFiles/W3SVC#########/. Dieser Ordner enthält eine XSL-Datei und eine oder mehrere XML-Dateien. Vergewissern Sie sich, dass Sie die XSL-Datei in dasselbe Verzeichnis wie die XML-Datei(en) herunterladen, da die XSL-Datei die Funktionalität zum Formatieren und Filtern des Inhalts der XML-Datei(en) zur Anzeige in Internet Explorer zur Verfügung stellt. 

- Lesen Sie Dateien mit: Internet Explorer

**Protokolldateityp: Detaillierte Fehlerprotokollierung**

- Location: /LogFiles/DetailedErrors/. Der Ordner /LogFiles/DetailedErrors/ enthält eine oder mehrere HTM-Dateien, die umfangreiche Informationen zu aufgetretenen HTTP-Fehlern bereitstellen. 

- Lesen Sie Dateien mit: Webbrowser

Die HTM-Dateien umfassen die folgenden Abschnitte:

- **Ausführliche Fehlerinformationen:** enthält Informationen zum Fehler, z. B. <em>Modul</em>, <em>Handler</em>, <em>Fehlercode</em>, und <em>angeforderte URL</em>.

- **Wahrscheinliche Ursachen:** Listet verschiedene mögliche Fehlerursachen auf.

- **Sie können Folgendes versuchen:** Listet mögliche Lösungen für das im Fehler gemeldete Problem auf.

- **Links und weitere Informationen**: Bietet zusätzliche zusammenfassende Informationen zum Fehler sowie ggf. Links zu anderen Ressourcen wie Microsoft Knowledge Base-Artikeln.

**Protokolldateityp: Webserverprotokollierung**

- Location: /LogFiles/http/RawLogs. Die in den Dateien gespeicherten Informationen sind im [erweiterten W3C-Protokollformat](http://go.microsoft.com/fwlink/?LinkID=90561) formatiert. Die Felder "s-computername", "s-ip" und "cs-version" werden von Azure-Websites nicht verwendet.

- Lesen Sie Dateien mit: Log Parser. Dient zum Analysieren und Abfragen von IIS-Protokolldateien. Log Parser 2.2 ist verfügbar im Microsoft Download Center unter <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>..


##<a name="webendpointstatus"></a>Vorgehensweise: Überwachen des Web-Endpunktstatus

Mit dieser Funktion, die im Modus **Standard** verfügbar ist, können Sie bis zu 2 Endpunkte von 3 geografischen Standorten aus überwachen. 

Endpunktüberwachung konfiguriert Webtests von geografisch verteilten Standorten zum Testen der Reaktionszeit und der Verfügbarkeit von Web-URLs. Der Test führt einen HTTP GET-Aufruf der Web-URL durch, um die Reaktionszeit und Verfügbarkeit von jedem Standort zu bestimmen. Jeder konfigurierte Standort führt alle fünf Minuten einen Test aus.

Die Verfügbarkeit wird mit HTTP-Antwortcodes überwacht, und die Reaktionszeit wird in Millisekunden gemessen. Die Verfügbarkeit gilt als 100 %, wenn die Reaktionszeit unter 30 Sekunden beträgt und der HTTP-Statuscode unter 400 liegt. Die Verfügbarkeit beträgt 0%, wenn die Reaktionszeit über 30 Sekunden beträgt und der HTTP-Statuscode über 400 liegt.

Nachdem Sie die Endpunktüberwachung konfiguriert haben, können Sie Details zu den einzelnen Endpunkten aufrufen, um die Reaktionszeit und den Verfügbarkeitsstatus für das Überwachungsintervall von jedem der Teststandorte anzuzeigen. Sie können beispielsweise auch eine Warnungsregel einrichten, wenn der Endpunkt zu lange für eine Reaktion braucht. 

**So konfigurieren Sie die Endpunktüberwachung:**

1.	Öffnen Sie **Websites**. Klicken Sie auf den Namen der Website, die Sie konfigurieren möchten.
2.	Klicken Sie auf die Registerkarte **Konfigurieren**. 
3.     Wechseln Sie zum Abschnitt **Überwachung**, um Ihre Endpunkteinstellungen einzugeben.
4.	Geben Sie einen Namen für den Endpunkt ein.
5.	Geben Sie die URL für den Teil der Website ein, den Sie überwachen möchten. Beispiel: [http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive). 
6.	Wählen Sie einen oder mehrere geografische Standorte aus der Liste aus.
7.	Sie können optional die vorherigen Schritte wiederholen, um einen zweiten Endpunkt zu erstellen.
8.	Klicken Sie auf **Speichern**. Es kann einige Zeit dauern, bis die Daten der Web-Endpunktüberwachung auf den Registerkarten **Dashboard** und **Monitor** verfügbar sind.

	Gehen Sie folgendermaßen vor, um eine E-Mail-Regel zu erstellen:

9.	Klicken Sie auf der Diensteleiste links auf **Verwaltungsdienste**.
10.	Klicken Sie unten auf **Regel hinzufügen**.
11.	Wählen Sie in **Diensttyp** die Option **Website** aus und dann die Website, für die Sie zuvor die Endpunktüberwachung konfiguriert haben. Klicken Sie auf **Weiter**.
12.	In **Metrik** können Sie jetzt weitere Kennzahlen für den von Ihnen konfigurierten Endpunkt auswählen. Beispiel: **Reaktionszeit (Homepage/USA: IL-Chicago)**. Wählen Sie die Metrik "Reaktionszeit" und geben Sie 3 in **Schwellenwert**, was 3 Sekunden als Grenzwert festlegt.
13.	Wählen Sie **E-Mail an Dienstadministrator und Co-Administratoren senden**. Klicken Sie auf **Fertig stellen**.

	Azure überwacht nun aktiv den Endpunkt und sendet eine E-Mail-Mitteilung, wenn die Antwortzeit 3 Sekunden überschreitet.


Weitere Informationen zur Überwachung von Website-Endpunkten erhalten Sie in den folgenden Videos:

- [Scott Guthrie mit einer Einführung zu Azure-Websites und Einrichtung der Endpunktüberwachung](http://www.windowsazure.com/de-de/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Pflegen von Azure-Websites plus Endpunktüberwachung - mit Stefan Schackow](http://www.windowsazure.com/de-de/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)





[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169








<!--HONumber=42-->
