<properties linkid="manage-services-how-to-monitor-a-cloud-service" urlDisplayName="How to monitor" pageTitle="How to monitor a cloud service - Azure" metaKeywords="Azure monitoring cloud services, Azure Management Portal cloud services" description="Learn how to monitor cloud services by using the Azure Management Portal." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Monitor Cloud Services" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Überwachung von Cloud-Diensten

[WACOM.INCLUDE [Haftungsausschluss][Haftungsausschluss]]

Im Azure-Verwaltungsportal können Sie wichtige Leistungsmetriken Ihrer Cloud-Dienste überwachen. Sie können die Überwachungsstufe für die einzelnen Dienstrollen auf minimal oder ausführlich einstellen und die Überwachungsanzeige anpassen. Ausführliche Überwachungsdaten werden in einem Speicherkonto gespeichert, auf das Sie außerhalb des Portals Zugriff haben.

Die Überwachungsanzeigen im Verwaltungsportal können ausführlich konfiguriert werden. Sie können die zu überwachenden Metriken in der Metrikenliste auf der Seite **Monitor** auswählen. Außerdem können Sie auswählen, welche Metriken in den Diagrammen auf der Seite **Monitor** und im Dashboard eingetragen werden sollen.

## Inhaltsverzeichnis

-   [Konzepte][Konzepte]
-   [Gewusst wie: Konfigurieren der Überwachung für Cloud-Dienste][Gewusst wie: Konfigurieren der Überwachung für Cloud-Dienste]
-   [Gewusst wie: Empfangen von Warnungen für Metriken von Cloud-Diensten][Gewusst wie: Empfangen von Warnungen für Metriken von Cloud-Diensten]
-   [Gewusst wie: Hinzufügen von Metriken zur Metrikentabelle][Gewusst wie: Hinzufügen von Metriken zur Metrikentabelle]
-   [Gewusst wie: Anpassen des Metrikendiagramms][Gewusst wie: Anpassen des Metrikendiagramms]
-   [Gewusst wie: Zugreifen auf ausführliche Überwachungsdaten außerhalb des Verwaltungsportals][Gewusst wie: Zugreifen auf ausführliche Überwachungsdaten außerhalb des Verwaltungsportals]

## <span id="concepts"></span></a>Konzepte

Standardmäßig werden neue Cloud-Dienste mit minimaler Überwachung eingerichtet. Dabei werden Leistungsindikatoren des Hostbetriebssystems für die Rolleninstanzen (virtuelle Computer) gesammelt. Die minimalen Metriken beschränken sich auf CPU-Auslastung, Dateneingang, Datenausgang, Lese- und Schreibdurchsatz von Laufwerken. Mit der ausführlichen Überwachung erhalten Sie zusätzliche Metriken auf Basis von Leistungsdaten der einzelnen virtuellen Computer (Rolleninstanzen). Die ausführliche Überwachung ermöglicht eine genauere Analyse von Problemen beim Betrieb von Anwendungen.

> [WACOM.NOTE]
> Wenn Sie ausführliche Überwachung verwenden, können Sie weitere Leistungsindikatoren beim Start der Rolleninstanz entweder in Form einer Diagnose-Konfigurationsdatei oder per Fernsteuerung über die Azure Diagnostics API hinzufügen. Um diese Metriken im Verwaltungsportal überwachen zu können, müssen Sie die Leistungsindikatoren hinzufügen, bevor Sie die ausführliche Überwachung aktivieren. Weitere Informationen finden Sie unter [Sammeln von Protokollierungsdaten mit der Azure-Diagnose][Sammeln von Protokollierungsdaten mit der Azure-Diagnose] und [Erstellen und Verwenden von Leistungsindikatoren in einer Azure-Anwendung][Erstellen und Verwenden von Leistungsindikatoren in einer Azure-Anwendung].

Die Daten der Leistungsindikatoren aus Rolleninstanzen werden standardmäßig alle 3 Minuten von der Rolleninstanz gemessen und übertragen. Bei der ausführlichen Überwachung werden die Leistungsindikatorendaten für die einzelnen Rolleninstanzen und rolleninstanzübergreifend für die einzelnen Rollen in Intervallen von 5 Minuten, 1 Stunde und 12 Stunden aggregiert. Die aggregierten Daten werden nach 10 Tagen gelöscht.

Nach dem Aktivieren der ausführlichen Überwachung werden die aggregierten Überwachungsdaten in Tabellen in Ihrem Speicherkonto gespeichert. Um die ausführliche Überwachung für eine Rolle zu aktivieren, müssen Sie eine Diagnose-Verbindungszeichenfolge konfigurieren, die auf das Speicherkonto verweist. Sie können unterschiedliche Speicherkonten für unterschiedliche Rollen verwenden.

Beachten Sie, dass bei der ausführlichen Überwachung Ihre Speicherkosten für Datenspeicher, Datenübertragung und Speichertransaktionen ansteigen. Für minimale Überwachung wird kein Speicherkonto benötigt. Die Daten der die bei minimaler Überwachungsstufe gesammelten Metriken werden nicht in Ihrem Speicherkonto gespeichert, selbst wenn Sie die Überwachungsstufe auf ausführlich stellen.

## <span id="verbose"></span></a>Gewusst wie: Konfigurieren der Überwachung für Cloud-Dienste

Führen Sie die folgenden Schritte aus, um im Verwaltungsportal ausführliche oder minimale Überwachung zu konfigurieren. Bevor Sie die ausführliche Überwachung aktivieren können, müssen Sie die Azure-Diagnose aktivieren und die Diagnose-Verbindungszeichenfolgen konfigurieren, sodass die Azure-Diagnose Zugriff auf die Speicherkonten hat, um die Daten dort speichern zu können.

### Vorbemerkungen

-   Erstellen Sie ein Speicherkonto zum Speichern der Überwachungsdaten. Sie können unterschiedliche Speicherkonten für unterschiedliche Rollen verwenden. Weitere Informationen finden Sie in der Hilfe zu **Speicherkonten** oder unter [How To Create a Storage Account][How To Create a Storage Account] (Erstellen eines Speicherkontos, in englischer Sprache).

-   Aktivieren Sie die Azure-Diagnose für Ihre Cloud-Dienstrollen.
    Sie müssen die Cloud-Dienst-Definitionsdatei (CSDEF) und die Cloud-Dienst-Konfigurationsdatei (CSCFG) ändern. Weitere Informationen finden Sie unter [Configuring Azure Diagnostics][Configuring Azure Diagnostics] (Konfigurieren von Azure-Diagnose, in englischer Sprache).

Im Verwaltungsportal können Sie die Diagnose-Verbindungszeichenfolgen hinzufügen oder bearbeiten, die von der Azure-Diagnose für den Zugriff auf die Speicherkonten für ausführliche Überwachungsdaten verwendet werden. Außerdem können Sie die Überwachungsstufe auf ausführlich oder minimal stellen. Da die Daten bei der ausführlichen Überwachung in einem Speicherkonto gespeichert werden, müssen Sie die Diagnose-Verbindungszeichenfolgen konfigurieren, bevor Sie die Überwachungsstufe auf ausführlich stellen.

### Konfigurieren von Diagnose-Verbindungszeichenfolgen für die ausführliche Überwachung

1.  Kopieren Sie einen Speicherzugriffsschlüssel für das Speicherkonto, das Sie zum Speichern der ausführlichen Überwachungsdaten verwenden möchten. Verwenden Sie dazu im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] die Funktion **Schlüssel verwalten** auf der Seite **Speicherkonten**. Weitere Informationen finden Sie unter [How to Manage Cloud Services][How to Manage Cloud Services] (Verwaltung von Cloud-Diensten, in englischer Sprache) oder in der Hilfe auf der Seite **Speicherkonten**.

2.  Öffnen von **Cloud-Diensten**. Klicken Sie anschließend auf den Namen des zu konfigurierenden Cloud-Dienstes, um das Dashboard zu öffnen.

3.  Klicken Sie auf **Produktion** oder **Staging**, um die zu konfigurierende Installation anzuzeigen.

4.  Klicken Sie auf **Konfigurieren**.

    Bearbeiten Sie die **Überwachungs**-Einstellungen im oberen Bereich der Seite **Konfigurieren**, wie unten gezeigt. Falls Sie Azure-Diagnose für den Cloud-Dienst nicht aktiviert haben, ist die Option **Stufe** nicht verfügbar. Sie können die Datenaufbewahrungsrichtlinie nicht ändern. Ausführliche Überwachungsdaten für Cloud-Dienste werden für 10 Tage gespeichert.

    ![Überwachungsoptionen][Überwachungsoptionen]

5.  Vervollständigen Sie unter **Diagnose-Verbindungszeichenfolgen**die Diagnose-Verbindungszeichenfolgen für sämtliche Rollen, die Sie ausführlich überwachen möchten.

    Die Verbindungszeichenfolgen haben das folgende Format. (Das Beispiel gilt für einen Cloud-Dienst, der Standard-Endpunkte verwendet.) Um eine Verbindungszeichenfolge zu aktualisieren, geben Sie einen gültigen Speicherkontonamen und Speicherzugriffsschlüssel für das Speicherkonto ein, das Sie verwenden möchten.

    DefaultEndpointsProtocol=https;AccountName=StorageAccountName;AccountKey=StorageAccountKey

6.  Klicken Sie auf **Speichern**.

Falls Sie die ausführliche Überwachung aktivieren, führen Sie die folgenden Schritte erst aus, nachdem Sie die Diagnose-Verbindungszeichenfolgen für die Dienstrollen konfiguriert haben.

### Ändern der Überwachungsstufe auf ausführlich oder minimal

1.  Öffnen Sie im [Verwaltungsportal][Azure-Verwaltungsportal] die Seite **Konfigurieren** für die Cloud-Dienst-Bereitstellung.

2.  Klicken Sie unter **Stufe** auf **Ausführlich** oder **Minimal**.

3.  Klicken Sie auf **Speichern**.

Nachdem Sie die ausführliche Überwachung aktiviert haben, sollten Sie die Überwachungsdaten im Verwaltungsportal nach kurzer Zeit sehen können.

Leistungsindikatordaten und aggregierte Überwachungsdaten werden im Speicherkonto in Tabellen abgelegt, deren Namen von der Bereitstellungs-ID der jeweiligen Rollen abhängen.

## <span id="receivealerts"></span></a>Gewusst wie: Empfangen von Warnungen für Metriken von Cloud-Diensten

Sie können Warnungen auf Basis der Überwachungsmetriken Ihrer Cloud-Dienste empfangen. Auf der Seite **Verwaltungsdienste** im Azure-Verwaltungsportal können Sie Regeln erstellen, die einen Alarm auslösen, wenn eine bestimmte Metrik einen von Ihnen ausgewählten Wert erreicht. Außerdem können Sie eine E-Mail senden, wenn der Alarm ausgelöst wird. Weitere Informationen finden Sie unter [Gewusst wie: Empfangen von Warnungs-Benachrichtigungen und Verwaltung von Alarmregeln in Azure][Gewusst wie: Empfangen von Warnungs-Benachrichtigungen und Verwaltung von Alarmregeln in Azure].

## <span id="addmetrics"></span></a>Gewusst wie: Hinzufügen von Metriken zur Metrikentabelle

1.  Öffnen Sie im [Verwaltungsportal][Verwaltungsportal] die Seite **Monitor** für den Cloud-Dienst.

    Standardmäßig zeigt die Metrikentabelle eine Teilmenge der verfügbaren Metriken an. Die Abbildung zeit die Standardmetriken für Cloud-Dienste. Diese sind beschränkt auf die Leistungsindikatoren für Speicher/verfügbare MByte, und die Daten werden auf Rollenebene aggregiert. Unter **Metriken hinzufügen** können Sie weitere aggregierte Metriken oder Metriken auf Rollenebene für die Überwachung im Verwaltungsportal auswählen.

    ![Ausführliche Anzeige][Ausführliche Anzeige]

2.  Hinzufügen von Metriken zur Metrikentabelle:

    a. Klicken Sie auf **Metriken hinzufügen**, um **Metriken auswählen** anzuzeigen, wie unten dargestellt.
    Die erste verfügbare Metrik ist erweitert, um die verfügbaren Optionen anzuzeigen. Für jede Metrik zeigt die oberste Option die aggregierten Überwachungsdaten für alle Rollen an. Zusätzlich können Sie einzelne Rollen auswählen, deren Daten angezeigt werden sollen.

    ![Metriken hinzufügen][Metriken hinzufügen]

    b. Auswählen der anzuzeigenden Metriken:

    -   Klicken Sie auf den Pfeil nach unten neben der Metrik, um die Überwachungsoptionen zu erweitern.
    -   Markieren Sie die Kontrollkästchen für alle Überwachungsoptionen, die Sie anzeigen möchten.

    Sie können bis zu 50 Metriken in der Metrikentabelle anzeigen.

    <div class="dev-callout"> 
<b>Hinweis</b> 
<p>Bei der ausf&uuml;hrlichen &Uuml;berwachung kann die Metrikenliste Dutzende von Metriken enthalten. Zeigen Sie mit der Maus auf den rechten Rand des Dialogfelds, um eine Bildlaufleiste anzuzeigen. Um die Liste zu filtern, klicken Sie auf das Suchsymbol und geben Sie Text in das Suchfeld ein, wie unten gezeigt.</p> 
</div>

    ![Suche zum Hinzufügen von Metriken][Suche zum Hinzufügen von Metriken]

3.  Wählen Sie die gewünschten Metriken aus und klicken Sie auf OK (Kontrollkästchen).

    Die ausgewählten Metriken werden zur Metrikentabelle hinzugefügt, wie unten gezeigt.

    ![Überwachen von Metriken][Überwachen von Metriken]

4.  Um eine Metrik aus der Metrikentabelle zu löschen, klicken Sie auf die Metrik, um diese auszuwählen, und anschließend auf **Metrik löschen**. (Die Option **Metrik löschen** ist nur sichtbar, wenn Sie eine Metrik ausgewählt haben.)

## <span id="customizechart"></span></a>Gewusst wie: Anpassen des Metrikendiagramms

1.  In der Metrikentabelle können Sie bis zu 6 Metriken auswählen, die im Metrikendiagramm angezeigt werden sollen. Klicken Sie auf das Kontrollkästchen links von einer Metrik, um diese auszuwählen. Heben Sie die Auswahl in der Metrikentabelle auf, um eine Metrik aus dem Metrikendiagramm zu entfernen.

    Die ausgewählten Metriken in der Metrikentabelle werden zum Metrikendiagramm hinzugefügt. Auf kleinen Bildschirmen wird eine Auswahlliste mit **n weitere** angezeigt. Diese enthält Metrikenheader, die nicht auf den Bildschirm passen.

2.  Wählen Sie Relativ oder Absolut im oberen Bereich des Diagramms, um zwischen relativen (nur endgültiger Wert für jede Metrik) und absoluten Werten (Y-Achse wird angezeigt) umzuschalten.

    ![Relativ oder Absolut][Relativ oder Absolut]

3.  Wählen Sie 1 Stunde, 24 Stunden oder 7 Tage im oberen Bereich des Diagramms aus, um das Zeitintervall für das Metrikendiagramm zu ändern.

    ![Anzeigeintervall für die Überwachung][Anzeigeintervall für die Überwachung]

    Das Metrikendiagramm im Dashboard stellt die Metriken auf eine andere Weise dar. Eine Standardauswahl an Metriken ist verfügbar, und Metriken werden durch Auswählen des Metrikheaders hinzugefügt oder entfernt.

### Anpassen des Metrikendiagramms im Dashboard

1.  Öffnen Sie das Dashboard für den Cloud-Dienst.

2.  Hinzufügen oder Entfernen von Metriken im Diagramm:

    -   Markieren Sie das Kontrollkästchen für die Metrik im Tabellenkopf, um eine neue Metrik anzuzeigen. Falls Sie auf einem kleinen Bildschirm arbeiten, klicken Sie auf ***n* Metriken**, um eine Metrik anzuzeigen, die im Tabellenkopf nicht angezeigt werden kann.

    -   Heben Sie die Auswahl des entsprechenden Kontrollkästchens auf, um eine aktuell angezeigte Metrik zu löschen.

3.  Umschalten zwischen **Relativer** und **Absoluter** Anzeige.

4.  Wählen Sie aus, ob Daten für 1 Stunde, 24 Stunden oder 7 Tage angezeigt werden sollen.

## <span id="accessverbose"></span></a>Gewusst wie: Zugreifen auf ausführliche Überwachungsdaten außerhalb des Verwaltungsportals

Ausführliche Überwachungsdaten werden in Tabellen in Speicherkonten gespeichert, die Sie für die einzelnen Rollen angeben. Pro Cloud-Dienst-Bereitstellung werden sechs Tabellen für die Rolle erstellt. Jeweils zwei dieser Tabellen sind für die einzelnen Intervalle (5 Minuten, 1 Stunde und 12 Stunden). Eine dieser Tabellen speichert Aggregationen auf Rollenebene, und die andere Tabelle speichert Aggregationen für Rolleninstanzen.

Die Tabellennamen haben das folgende Format:

    WAD*deploymentID*PT*aggregation_interval*[R|RI]Table

Hinweis:

-   *deploymentID* ist die GUID der Cloud-Dienst-Bereitstellung

-   *aggregation\_interval* = 5M, 1H oder 12H

-   Aggregationen auf Rollenebene = R

-   Aggregationen für Rolleninstanzen = RI

Die folgenden Tabellen speichern z. B. ausführliche Überwachungsdaten aggregiert in 1-stündige Intervalle:

    WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)
    WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)

  [Haftungsausschluss]: ../includes/disclaimer.md
  [Konzepte]: #concepts
  [Gewusst wie: Konfigurieren der Überwachung für Cloud-Dienste]: #verbose
  [Gewusst wie: Empfangen von Warnungen für Metriken von Cloud-Diensten]: #receivealerts
  [Gewusst wie: Hinzufügen von Metriken zur Metrikentabelle]: #addmetrics
  [Gewusst wie: Anpassen des Metrikendiagramms]: #customizechart
  [Gewusst wie: Zugreifen auf ausführliche Überwachungsdaten außerhalb des Verwaltungsportals]: #accessverbose
  [Sammeln von Protokollierungsdaten mit der Azure-Diagnose]: http://msdn.microsoft.com/de-de/library/gg433048.aspx
  [Erstellen und Verwenden von Leistungsindikatoren in einer Azure-Anwendung]: http://msdn.microsoft.com/de-de/library/hh411542.aspx
  [How To Create a Storage Account]: /de-de/manage/services/storage/how-to-create-a-storage-account/
  [Configuring Azure Diagnostics]: http://msdn.microsoft.com/de-de/library/windowsazure/dn186185.aspx
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [How to Manage Cloud Services]: http://www.windowsazure.com/de-de/manage/services/cloud-services/how-to-manage-a-cloud-service/
  [Überwachungsoptionen]: ./media/cloud-services-how-to-monitor/CloudServices_MonitoringOptions.png
  [Gewusst wie: Empfangen von Warnungs-Benachrichtigungen und Verwaltung von Alarmregeln in Azure]: http://go.microsoft.com/fwlink/?LinkId=309356
  [Verwaltungsportal]: http://manage.windowsazure.com/
  [Ausführliche Anzeige]: ./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png
  [Metriken hinzufügen]: ./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png
  [Suche zum Hinzufügen von Metriken]: ./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png
  [Überwachen von Metriken]: ./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png
  [Relativ oder Absolut]: ./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png
  [Anzeigeintervall für die Überwachung]: ./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png
