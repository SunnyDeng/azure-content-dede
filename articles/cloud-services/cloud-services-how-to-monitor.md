<properties 
	pageTitle="Überwachen eines Clouddiensts| Microsoft Azure"
	description="Hier erfahren Sie mehr über die Überwachung von Clouddiensten im Azure-Verwaltungsportal."
	services="cloud-services"
	documentationCenter=""
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2015"
	ms.author="robb"/>


#Überwachung von Clouddiensten

[AZURE.INCLUDE [Haftungsausschluss](../../includes/disclaimer.md)]

Im Azure-Verwaltungsportal können Sie wichtige Leistungsmetriken Ihrer Clouddienste überwachen. Sie können die Überwachungsstufe für die einzelnen Dienstrollen auf minimal oder ausführlich einstellen und die Überwachungsanzeige anpassen. Ausführliche Überwachungsdaten werden in einem Speicherkonto gespeichert, auf das Sie außerhalb des Portals Zugriff haben.

Die Überwachungsanzeigen im Verwaltungsportal können ausführlich konfiguriert werden. Sie können die zu überwachenden Metriken in der Metrikenliste auf der Seite **Monitor** auswählen. Außerdem können Sie auswählen, welche Metriken in den Diagrammen auf der Seite **Monitor** und im Dashboard eingetragen werden sollen.

##Konzepte##

Standardmäßig werden neue Clouddienste mit minimaler Überwachung eingerichtet. Dabei werden Leistungsindikatoren des Hostbetriebssystems für die Rolleninstanzen (virtuelle Computer) gesammelt. Die minimalen Metriken beschränken sich auf CPU-Auslastung, Dateneingang, Datenausgang, Lese- und Schreibdurchsatz von Laufwerken. Mit der ausführlichen Überwachung erhalten Sie zusätzliche Metriken auf Basis von Leistungsdaten der einzelnen virtuellen Computer (Rolleninstanzen). Die ausführliche Überwachung ermöglicht eine genauere Analyse von Problemen beim Betrieb von Anwendungen.

Die Daten der Leistungsindikatoren aus Rolleninstanzen werden standardmäßig alle 3 Minuten von der Rolleninstanz gemessen und übertragen. Bei der ausführlichen Überwachung werden die Leistungsindikatorendaten für die einzelnen Rolleninstanzen und rolleninstanzübergreifend für die einzelnen Rollen in Intervallen von 5 Minuten, 1 Stunde und 12 Stunden aggregiert. Die aggregierten Daten werden nach 10 Tagen gelöscht.

Nach dem Aktivieren der ausführlichen Überwachung werden die aggregierten Überwachungsdaten in Tabellen in Ihrem Speicherkonto gespeichert. Um die ausführliche Überwachung für eine Rolle zu aktivieren, müssen Sie eine Diagnose-Verbindungszeichenfolge konfigurieren, die auf das Speicherkonto verweist. Sie können unterschiedliche Speicherkonten für unterschiedliche Rollen verwenden.

Beachten Sie, dass bei der ausführlichen Überwachung Ihre Speicherkosten für Datenspeicher, Datenübertragung und Speichertransaktionen ansteigen. Für minimale Überwachung wird kein Speicherkonto benötigt. Die Daten der die bei minimaler Überwachungsstufe gesammelten Metriken werden nicht in Ihrem Speicherkonto gespeichert, selbst wenn Sie die Überwachungsstufe auf ausführlich stellen.


##Konfigurieren der Überwachung für Clouddienste##

Führen Sie die folgenden Schritte aus, um im Verwaltungsportal ausführliche oder minimale Überwachung zu konfigurieren.

###Voraussetzungen###

- Erstellen Sie ein Speicherkonto zum Speichern der Überwachungsdaten. Sie können unterschiedliche Speicherkonten für unterschiedliche Rollen verwenden. Weitere Informationen finden Sie in der Hilfe zu **Speicherkonten** oder unter [How To Create a Storage Account](/manage/services/storage/how-to-create-a-storage-account/) (Erstellen eines Speicherkontos, in englischer Sprache).

- Aktivieren Sie die Azure-Diagnose für Ihre Clouddienstrollen. Siehe [Konfigurieren der Diagnose für Cloud Services](https://msdn.microsoft.com/library/azure/dn186185.aspx#BK_EnableBefore).

Stellen Sie sicher, dass die Diagnose-Verbindungszeichenfolge in der Konfiguration der Rolle vorhanden ist. Sie können die ausführliche Überwachung erst aktivieren, wenn die Azure-Diagnose aktiviert ist und eine Diagnoseverbindungszeichenfolge in der Rollenkonfiguration eingeschlossen wurde.

> [AZURE.NOTE]Projekte für Azure SDK 2.5 haben die Diagnoseverbindungszeichenfolge nicht automatisch in der Projektvorlage eingeschlossen. Für diese Projekte müssen Sie die Diagnoseverbindungszeichenfolge manuell der Rollenkonfiguration hinzufügen.

**so fügen Sie der Rollenkonfiguration Diagnoseverbindungszeichenfolgen manuell hinzu**

1. Öffnen Sie das Cloud Services-Projekt in Visual Studio.
2. Doppelklicken Sie auf die **Rolle**, um den Rollendesigner zu öffnen, und wählen Sie die Registerkarte **Einstellungen** aus
3. Suchen Sie nach der Einstellung namens **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Wenn diese Einstellung nicht vorhanden ist, klicken Sie auf die Schaltfläche **Einstellung hinzufügen**, um sie der Konfiguration hinzufügen, und ändern Sie den Typ für die neue Einstellung in **ConnectionString**
5. Legen Sie den Wert für die Verbindungszeichenfolge, indem Sie auf die Schaltfläche **...** klicken. Dies öffnet ein Dialogfeld, sodass Sie ein Speicherkonto auswählen können.

	![Visual Studio-Einstellungen](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

###Ändern der Überwachungsstufe auf ausführlich oder minimal###

1. Öffnen Sie im [Verwaltungsportal](https://manage.windowsazure.com/) die Seite **Konfigurieren** für die Clouddienst-Bereitstellung.

2. Klicken Sie unter **Stufe** auf **Ausführlich** oder **Minimal**.

3. Klicken Sie auf **Speichern**.

Nachdem Sie die ausführliche Überwachung aktiviert haben, sollten Sie die Überwachungsdaten im Verwaltungsportal nach kurzer Zeit sehen können.

Leistungsindikatordaten und aggregierte Überwachungsdaten werden im Speicherkonto in Tabellen abgelegt, deren Namen von der Bereitstellungs-ID der jeweiligen Rollen abhängen.

##Empfangen von Warnungen für Metriken von Clouddiensten##

Sie können Warnungen auf Basis der Überwachungsmetriken Ihrer Clouddienste empfangen. Auf der Seite **Verwaltungsdienste** im Azure-Verwaltungsportal können Sie Regeln erstellen, die einen Alarm auslösen, wenn eine bestimmte Metrik einen von Ihnen ausgewählten Wert erreicht. Außerdem können Sie eine E-Mail senden, wenn der Alarm ausgelöst wird. Weitere Informationen finden Sie unter [Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

##Hinzufügen von Metriken zur Metrikentabelle##

1. Öffnen Sie im [Verwaltungsportal](http://manage.windowsazure.com/) die Seite **Monitor** für den Clouddienst.

	Standardmäßig zeigt die Metrikentabelle eine Teilmenge der verfügbaren Metriken an. Die Abbildung zeit die Standardmetriken für Clouddienste. Diese sind beschränkt auf die Leistungsindikatoren für Speicher/verfügbare MByte, und die Daten werden auf Rollenebene aggregiert. Unter **Metriken hinzufügen** können Sie weitere aggregierte Metriken oder Metriken auf Rollenebene für die Überwachung im Verwaltungsportal auswählen.

	![Ausführliche Anzeige](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
 
2. Hinzufügen von Metriken zur Metrikentabelle:

	a. Klicken Sie auf **Metriken hinzufügen**, um **Metriken auswählen** zu öffnen, wie unten gezeigt. Die erste verfügbare Metrik ist erweitert, um die verfügbaren Optionen anzuzeigen. Für jede Metrik zeigt die oberste Option die aggregierten Überwachungsdaten für alle Rollen an. Zusätzlich können Sie einzelne Rollen auswählen, deren Daten angezeigt werden sollen.

	![Metriken hinzufügen](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)


	b. So wählen Sie die anzuzeigenden Metriken aus:

	- Klicken Sie auf den Pfeil nach unten neben der Metrik, um die Überwachungsoptionen zu erweitern.
	- Markieren Sie die Kontrollkästchen für alle Überwachungsoptionen, die Sie anzeigen möchten.

	Sie können bis zu 50 Metriken in der Metrikentabelle anzeigen.

	> [AZURE.TIP]Bei der ausführlichen Überwachung kann die Metrikenliste Dutzende von Metriken enthalten. Zeigen Sie mit der Maus auf den rechten Rand des Dialogfelds, um eine Bildlaufleiste anzuzeigen. Um die Liste zu filtern, klicken Sie auf das Suchsymbol und geben Sie Text in das Suchfeld ein, wie unten gezeigt.
 
	![Suche zum Hinzufügen von Metriken](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)

3. Wählen Sie die gewünschten Metriken aus, und klicken Sie auf OK (Kontrollkästchen).

	Die ausgewählten Metriken werden zur Metrikentabelle hinzugefügt, wie unten gezeigt.

	![Überwachen von Metriken](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)

 
4. Um eine Metrik aus der Metrikentabelle zu löschen, klicken Sie auf die Metrik, um diese auszuwählen, und anschließend auf **Metrik löschen**. (Die Option **Metrik löschen** ist nur sichtbar, wenn Sie eine Metrik ausgewählt haben.)

###So fügen Sie benutzerdefinierte Metriken zur Metrikentabelle hinzu###
Die Überwachungsstufe **Ausführlich** bietet eine Liste der Standardmetriken, die Sie im Portal überwachen können. Darüber hinaus können Sie alle benutzerdefinierten Metriken oder Leistungsindikatoren über das Portal überwachen, die von der Anwendung definiert wurden.

Für die folgenden Schritte wird davon ausgegangen, dass Sie die Überwachungsstufe **Ausführlich** aktiviert und die Anwendung so konfiguriert haben, dass benutzerdefinierte Leistungsindikatoren erfasst und übertragen werden.

Zum Anzeigen von benutzerdefinierten Leistungsindikatoren im Portal müssen Sie die Konfiguration im Wad-Control-Container aktualisieren:
 
1.	Öffnen Sie das Blob "Wad-Control-Container" im Diagnosespeicherkonto. Sie können dafür Visual Studio oder einen anderen Speicher-Explorer verwenden.

	![Server-Explorer von Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)
2. Navigieren Sie gemäß dem Muster **DeploymentId/ RoleName/RoleInstance** zum Blobpfad, um die Konfiguration für die Rolleninstanz zu suchen. 

	![Speicher-Explorer von Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Laden Sie die Konfigurationsdatei für die Rolleninstanz herunter, und aktualisieren Sie sie, um alle benutzerdefinierten Leistungsindikatoren einzuschließen. Fügen Sie beispielsweise zum Überwachen von *Datenträgerschreibvorgang Byte/s* für *Laufwerk C* Folgendes unter dem Knoten **PerformanceCounters\\Subscriptions** ein.

	```xml
	<PerformanceCounterConfiguration>
	<CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
	<SampleRateInSeconds>180</SampleRateInSeconds>
	</PerformanceCounterConfiguration>
	```
4. Speichern Sie die Änderungen, und laden Sie die Konfigurationsdatei an demselben Speicherort hoch, wobei Sie die vorhandene Datei im Blob überschreiben.
5. Wechseln Sie in der Konfiguration des Verwaltungsportals in den ausführlichen Modus. Wenn Sie bereits im ausführlichen Modus waren, müssen Sie zuerst auf "Minimal" umschalten und dann wieder auf "Ausführlich".
6. Der benutzerdefinierten Leistungsindikator steht jetzt im Dialogfeld **Metriken hinzufügen** zur Verfügung. 

##Anpassen des Metrikendiagramms##

1. In der Metrikentabelle können Sie bis zu 6 Metriken auswählen, die im Metrikendiagramm angezeigt werden sollen. Klicken Sie auf das Kontrollkästchen links von einer Metrik, um diese auszuwählen. Heben Sie die Auswahl in der Metrikentabelle auf, um eine Metrik aus dem Metrikendiagramm zu entfernen.

	Die ausgewählten Metriken in der Metrikentabelle werden zum Metrikendiagramm hinzugefügt. Auf kleinen Bildschirmen wird eine Auswahlliste mit **n weitere** angezeigt. Diese enthält Metrikenheader, die nicht auf den Bildschirm passen.

 
2. Wählen Sie Relativ oder Absolut im oberen Bereich des Diagramms, um zwischen relativen (nur endgültiger Wert für jede Metrik) und absoluten Werten (Y-Achse wird angezeigt) umzuschalten.

	![Relativ oder Absolut](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)

3. Wählen Sie 1 Stunde, 24 Stunden oder 7 Tage im oberen Bereich des Diagramms aus, um das Zeitintervall für das Metrikendiagramm zu ändern.

	![Anzeigeintervall für die Überwachung](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)

	Das Metrikendiagramm im Dashboard stellt die Metriken auf eine andere Weise dar. Eine Standardauswahl an Metriken ist verfügbar, und Metriken werden durch Auswählen des Metrikheaders hinzugefügt oder entfernt.

###Anpassen des Metrikendiagramms im Dashboard###

1. Öffnen Sie das Dashboard für den Clouddienst.

2. Hinzufügen oder Entfernen von Metriken im Diagramm:

	- Markieren Sie das Kontrollkästchen für die Metrik im Tabellenkopf, um eine neue Metrik anzuzeigen. Falls Sie auf einem kleinen Bildschirm arbeiten, klicken Sie auf ***n* Metriken**, um eine Metrik anzuzeigen, die im Tabellenkopf nicht angezeigt werden kann.

	- Heben Sie die Auswahl des entsprechenden Kontrollkästchens auf, um eine aktuell angezeigte Metrik zu löschen.

3. Umschalten zwischen **Relativer** und **Absoluter** Anzeige.

4. Wählen Sie aus, ob Daten für 1 Stunde, 24 Stunden oder 7 Tage angezeigt werden sollen.

##Zugreifen auf ausführliche Überwachungsdaten außerhalb des Verwaltungsportals##

Ausführliche Überwachungsdaten werden in Tabellen in Speicherkonten gespeichert, die Sie für die einzelnen Rollen angeben. Pro Clouddienstbereitstellung werden sechs Tabellen für die Rolle erstellt. Jeweils zwei dieser Tabellen sind für die einzelnen Intervalle (5 Minuten, 1 Stunde und 12 Stunden). Eine dieser Tabellen speichert Aggregationen auf Rollenebene, und die andere Tabelle speichert Aggregationen für Rolleninstanzen.

Die Tabellennamen haben das folgende Format:

	WAD*deploymentID*PT*aggregation_interval*[R|RI]Table

Hinweis:

- *deploymentID* ist die GUID der Clouddienstbereitstellung

- *aggregation\_interval* = 5M, 1H oder 12H

- Aggregationen auf Rollenebene = R

- Aggregationen für Rolleninstanzen = RI

Die folgenden Tabellen speichern z. B. ausführliche Überwachungsdaten aggregiert in 1-stündige Intervalle:

	WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

	WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
 

<!---HONumber=August15_HO9-->