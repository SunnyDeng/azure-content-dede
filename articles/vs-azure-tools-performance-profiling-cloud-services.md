<properties 
   pageTitle="Testen der Leistung eines Clouddiensts | Microsoft Azure"
	description="Testen der Leistung eines Clouddiensts mit dem Visual Studio-Profiler"
	services="visual-studio-online"
	documentationCenter="n/a"
	authors="patshea123"
	manager="douge"
	editor="tlee"/>
<tags 
   ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/14/2015"
	ms.author="patshea"/>


# Testen der Leistung eines Clouddiensts 

##Übersicht

Sie können die Leistung eines Clouddiensts mit folgenden Methoden testen:

- Verwenden Sie die Azure-Diagnose, um Informationen zu Anforderungen und Verbindungen zu erfassen und Websitestatistiken zu überprüfen, welche die Leistung des Diensts aus Kundenperspektive darstellen. Informationen zum Einstieg finden Sie unter [Konfigurieren der Diagnose für Azure Cloud Services und Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=623009)

- Verwenden Sie den Visual Studio-Profiler, um eine detaillierte Analyse der Computingaspekte der Dienstausführung zu erhalten. Wie in diesem Thema beschrieben, können Sie den Profiler verwenden, um die Leistung zu messen, während ein Dienst in Azure ausgeführt wird. Informationen dazu, wie Sie den Profiler verwenden, um die Leistung eines lokal in einem Compute-Emulator ausgeführten Diensts zu messen, finden Sie unter [Testen der Leistung eines lokalen Azure-Clouddiensts im Compute-Emulator mithilfe des Visual Studio-Profilers](http://go.microsoft.com/fwlink/p/?LinkId=262845).



## Auswählen einer Leistungstestmethode

###Verwenden Sie die Azure-Diagnose, um folgende Daten zu erfassen:###

- Statistiken zu Webseiten oder -diensten, wie etwa Anforderungen und Verbindungen.

- Statistiken zu Rollen, z. B. wie oft eine Rolle neu gestartet wird.

- Allgemeine Informationen zur Speicherauslastung, z. B. die Zeit in Prozent, die der Garbage Collector benötigt, oder der Speicherplatz einer ausgeführten Rolle.

###Verwenden Sie den Visual Studio-Profiler für Folgendes:###

- Ermitteln, welche Funktionen die meiste Zeit in Anspruch nehmen.

- Messen, wie lange jeder Teil eines rechenintensiven Programms genau dauert.

- Vergleichen der detaillierten Leistungsberichte für zwei Versionen eines Diensts.

- Analysieren der Speicherbelegung, ausführlicher und genauer als auf der Ebene einzelner Speicherbelegungen.

- Analysieren von Parallelitätsproblemen in Multithreadcode.

Wenn Sie den Profiler verwenden, können Sie Daten erfassen, wenn ein Clouddienst lokal oder in Azure ausgeführt wird.

###Erfassen Sie Profilerstellungsdaten lokal zu folgenden Zwecken:###

- Testen der Leistung eines Teils eines Clouddiensts, wie z. B. der Ausführung einer bestimmten Workerrolle, die keine realistische simulierte Last erfordert.

- Testen der Leistung eines Clouddiensts in Isolation unter kontrollierten Bedingungen.

- Testen der Leistung eines Clouddiensts vor der Bereitstellung in Azure.

- Privates Testen der Leistung eines Clouddiensts, ohne Beeinträchtigung der vorhandenen Bereitstellungen.

- Testen der Leistung eines Clouddiensts, ohne dass Gebühren für die Ausführung in Azure anfallen.

###Erfassen Sie Profilerstellungsdaten in Azure zu folgenden Zwecken:###

- Testen der Leistung eines Clouddiensts unter einer simulierten oder realen Last.

- Verwenden der Instrumentationsmethode zur Erfassung von Profilerstellungsdaten, wie weiter unten in diesem Thema beschrieben.

- Testen der Leistung des Diensts in der gleichen Umgebung, in der er auch in der Produktion ausgeführt wird.

Üblicherweise simulieren Sie eine Last, um Clouddienste unter Bedingungen mit normaler oder hoher Belastung zu testen.

## Profilerstellung für einen Clouddienst in Azure

Wenn Sie Ihren Clouddienst aus Visual Studio veröffentlichen, können Sie ein Profil für den Dienst erstellen und die Einstellungen für die Profilerstellung angeben, die Ihnen die gewünschten Informationen liefern. Eine Profilerstellungssitzung wird für jede Instanz einer Rolle gestartet. Weitere Informationen zum Veröffentlichen Ihres Diensts aus Visual Studio finden Sie unter [Veröffentlichen in einen Azure-Clouddienst aus Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Weitere Informationen zur Leistungsprofilerstellung in Visual Studio finden Sie unter [Einführung in die Leistungsprofilerstellung](https://msdn.microsoft.com/library/azure/ms182372.aspx) und [Analysieren der Anwendungsleistung durch Verwenden von Profilerstellungstools](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

>[AZURE.NOTE]Sie können entweder IntelliTrace oder die Profilerstellung aktivieren, wenn Sie den Clouddienst veröffentlichen. Sie können nicht beide Verfahren aktivieren.

###Erfassungsmethoden des Profilers

Basierend auf den Leistungsproblemen, die Sie untersuchen möchten, können Sie verschiedene Erfassungsmethoden für die Profilerstellung verwenden:

- **CPU-Sampling**: Diese Methode erfasst Anwendungsstatistiken, die für eine erste Analyse von CPU-Auslastungsproblemen hilfreich sind. CPU-Sampling ist die empfohlene Methode für den Anfang der meisten Leistungsuntersuchungen. Die Erfassung von CPU-Samplingdaten wirkt sich nur sehr geringfügig auf die Anwendung aus, für die Sie ein Profil erstellen möchten.

- **Instrumentation**: Diese Methode erfasst detaillierte Zeitdaten, die für fokussierte Analysen sowie zur Analyse von Leistungsproblemen bei der Eingabe/Ausgabe hilfreich sind. Die Instrumentationsmethode zeichnet jeden Start-, Beendigungs- und Funktionsaufruf der Funktionen in einem Modul während eines Profilerstellungsvorgangs auf. Diese Methode eignet sich, um detaillierte Zeitdaten zu einem Abschnitt des Codes zu erfassen und um die Auswirkungen von Eingabe- und Ausgabevorgängen auf die Anwendungsleistung zu verstehen. Diese Methode ist für Computer mit 32-Bit-Betriebssystemen deaktiviert. Diese Option ist nur verfügbar, wenn Sie den Clouddienst in Azure ausführen, nicht lokal im Compute-Emulator.

- **.NET-Speicherbelegung**: Diese Methode nutzt die Sampling-Profilerstellungsmethode, um .NET Framework-Speicherbelegungsdaten zu erfassen. Zu den erfassten Daten gehören Anzahl und Größe der zugeordneten Objekte.

- **Parallelität**: Diese Methode erfasst Daten zu Ressourcenkonflikten sowie Prozess- und Threadausführungsdaten, die für die Analyse von Anwendungen mit mehreren Threads und Prozessen hilfreich sind. Die Parallelitätsmethode erfasst Daten für jedes Ereignis, das die Ausführung des Codes blockiert, wie z. B. ein Thread, der darauf wartet, dass der gesperrte Zugriff auf eine Anwendungsressource freigegeben wird. Diese Methode eignet sich für die Analyse von Anwendungen mit mehreren Threads.

- Sie können auch die **Profilerstellung für Ebeneninteraktion** aktivieren, die zusätzliche Informationen zu den Ausführungszeiten synchroner ADO.NET-Aufrufe in Funktionen von Anwendungen mit mehreren Ebenen bereitstellt, die mit einer oder mehreren Datenbanken kommunizieren. Sie können Ebeneninteraktionsdaten mit einer der folgenden Profilerstellungsmethoden erfassen. Weitere Informationen zur Profilerstellung für Ebeneninteraktionen finden Sie unter [Ansicht "Ebeneninteraktionen"](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## Konfigurieren von Profilerstellungseinstellungen

Die folgende Abbildung zeigt, wie Sie die Profilerstellungseinstellungen im Dialogfeld "Azure-Anwendung veröffentlichen" konfigurieren.

![Konfigurieren von Profilerstellungseinstellungen](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

>[AZURE.NOTE]Um das Kontrollkästchen **Profilerstellung aktivieren** zu aktivieren, muss der Profiler auf dem lokalen Computer installiert sein, mit dem Sie den Clouddienst veröffentlichen. Der Profiler wird standardmäßig installiert, wenn Sie Visual Studio Ultimate oder Visual Studio Premium installieren.

### So konfigurieren Sie Profilerstellungseinstellungen

1. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für Ihr Azure-Projekt, und wählen Sie anschließend **Veröffentlichen** aus. Detaillierte Informationen zum Veröffentlichen eines Clouddiensts finden Sie unter [Veröffentlichen eines Clouddiensts mit den Azure-Tools](http://go.microsoft.com/fwlink/p?LinkId=623012).

1. Wählen Sie im Dialogfeld **Azure-Anwendung veröffentlichen** die Registerkarte **Erweiterte Einstellungen**.

1. Aktivieren Sie zum Aktivieren der Profilerstellung das Kontrollkästchen **Profilerstellung aktivieren**.

1. Zum Konfigurieren der Profilerstellungseinstellungen wählen Sie den Hyperlink **Einstellungen** aus. Das Dialogfeld "Profilerstellungseinstellungen" wird angezeigt.

1. Wählen Sie aus den Optionsschaltflächen unter **Welche Profilerstellungsmethode möchten Sie verwenden?** die gewünschte Profilerstellungsmethode aus.

1. Um die Profilerstellungsdaten für die Ebeneninteraktion zu erfassen, aktivieren Sie das Kontrollkästchen **Profilerstellung für Ebeneninteraktion aktivieren**.

1. Wählen Sie die Schaltfläche **OK**, um die Einstellungen zu speichern.

    Wenn Sie diese Anwendung veröffentlichen, werden diese Einstellungen verwendet, um die Profilerstellungssitzung für jede Rolle zu erstellen.

## Anzeigen von Profilerstellungsberichten

Für jede Instanz einer Rolle in Ihrem Clouddienst wird eine Profilerstellungssitzung erstellt. Um die Profilerstellungsberichte zu jeder Sitzung in Visual Studio anzuzeigen, können Sie das Server-Explorer-Fenster anzeigen und den Azure-Computeknoten auswählen, um die Instanz einer Rolle auszuwählen. Danach können Sie den Profilerstellungsbericht anzeigen, wie in der folgenden Abbildung veranschaulicht.

![Anzeigen von Profilerstellungsberichten in Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### So zeigen Sie Profilerstellungsberichte an

1. Wählen Sie zum Anzeigen des Server-Explorer-Fensters in Visual Studio in der Menüleiste nacheinander die Optionen "Ansicht" und "Server-Explorer".

1. Wählen Sie den Azure-Computeknoten und anschließend den Azure-Bereitstellungsknoten für den Clouddienst aus, den Sie bei der Veröffentlichung aus Visual Studio für die Profilerstellung ausgewählt haben.

1. Wählen Sie die Rolle im Dienst aus, öffnen Sie dann das Kontextmenü für eine bestimmte Instanz, und wählen Sie **Profilerstellungsbericht anzeigen** aus, um Profilerstellungsberichte für eine Instanz anzuzeigen.

    Der Bericht, eine VSP-Datei, wird nun aus Azure heruntergeladen, und der Status des Downloads wird im Azure-Aktivitätsprotokoll angezeigt. Wenn der Download abgeschlossen ist, wird der Profilerstellungsbericht auf einer Registerkarte im Editor für Visual Studio unter der Bezeichnung "<Role name>\_<Instance Number>\_<identifier>.vsp" angezeigt. Es werden Übersichtsdaten für den Bericht angezeigt.

1. Um verschiedene Ansichten des Berichts anzuzeigen, wählen Sie in der Liste der aktuellen Ansichten die gewünschte Ansicht aus. Weitere Informationen finden Sie unter [Berichtsansichten für Profilerstellungstools](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## Nächste Schritte

[Debuggen von Cloud-Diensten.](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Veröffentlichen eines Clouddiensts mit Azure Tools](https://msdn.microsoft.com/library/azure/ee460772.aspx)

<!---HONumber=August15_HO9-->