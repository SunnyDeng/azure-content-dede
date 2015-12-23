<properties
    pageTitle="Durchführen von Auslastungstests für Anwendungen mithilfe von Visual Studio Online | Microsoft Azure"
    description="Erfahren Sie, wie Sie mithilfe von Visual Studio Online Belastungstests für Ihre Azure Service Fabric-Anwendungen durchführen können."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Durchführen von Auslastungstests für Anwendungen mithilfe von Visual Studio Online

In diesem Artikel wird erläutert, wie Sie die Visual Studio-Funktionen für Auslastungstests verwenden, um Belastungstests für eine Anwendung durchzuführen. Es wird ein Service Fabric-Back-End für zustandsbehaftete Dienste und ein Web-Front-End für zustandslose Dienste verwendet. Bei der hier verwendeten Beispielanwendung handelt es sich um einen Standortsimulator für Flugzeuge. Sie stellen eine Flugzeugkennung sowie den Abflug- und den Zielort bereit. Das Back-End der Anwendung verarbeitet die Anforderungen, und das Front-End zeigt das Flugzeug entsprechend den Kriterien auf einer Karte an.

Das folgende Diagramm veranschaulicht die Service Fabric-Anwendung, die Sie testen werden.

![][0]

## Voraussetzungen
Bevor Sie beginnen, müssen Sie folgende Aufgaben erledigen.

- Legen Sie ein Visual Studio Online-Konto (VSO) an. Unter [Visual Studio Online](https://www.visualstudio.com) können Sie ein kostenloses Konto erhalten.
- Laden Sie Visual Studio 2013 oder Visual Studio 2015 herunter, und installieren Sie die Software. In diesem Artikel wird Visual Studio 2015 Enterprise verwendet, Visual Studio 2013 und andere Editionen funktionieren jedoch genauso.
- Stellen Sie Ihre Anwendung in einer Stagingumgebung bereit. Informationen hierzu finden Sie unter [Veröffentlichen einer Anwendung in einem Remotecluster mit Visual Studio](service-fabric-publish-app-remote-cluster.md).
- Machen Sie sich mit dem Verwendungsmuster Ihrer Anwendung vertraut. Diese Informationen werden zur Simulation des Auslastungsmusters verwendet.
- Machen Sie sich mit dem Ziel des Auslastungstests vertraut. So können Sie die Ergebnisse des Auslastungstests analysieren und richtig interpretieren.

## Erstellen und Ausführen des Webleistungs- und Auslastungstestprojekts

### Erstellen eines Webleistungs- und Auslastungstestprojekts

1. Öffnen Sie Visual Studio 2015. Wählen Sie in der Menüleiste die Optionen **Datei** > **Neu** > **Projekt**, um das Dialogfeld **Neues Projekt** zu öffnen.

1. Erweitern Sie den Knoten **Visual C#**, und wählen Sie **Test** > **Testprojekt für Webleistung und Auslastung** aus. Benennen Sie das Projekt, und klicken Sie dann auf die Schaltfläche **OK**.

    ![][1]

    Das neue Webleistungs- und Auslastungstestprojekt sollte nun angezeigt werden

    ![][2]

### Aufzeichnen eines Webleistungstests

1. Öffnen Sie das WEBTEST-Projekt.

1. Wählen Sie das Symbol **Aufzeichnung hinzufügen**, um eine Aufzeichnung in Ihrem Browser zu starten.

    ![][3]

    ![][4]

1. Navigieren Sie zur Service Fabric-Anwendung. Der Aufzeichnungsbereich sollte die Webanforderungen anzeigen.

    ![][5]

1. Führen Sie eine Reihe von Aktionen aus, die wahrscheinlich auch von Ihren Benutzern ausgeführt werden. Diese Aktionen werden als Muster zum Generieren der Auslastung verwendet.

1. Wenn Sie fertig sind, klicken Sie auf **Beenden**, um die Aufzeichnung zu beenden.

    ![][6]

    Das WEBTEST-Projekt in Visual Studio sollte nun eine Reihe von Anforderungen aufgezeichnet haben. Dynamische Parameter werden automatisch ersetzt. An dieser Stelle können Sie zusätzliche, wiederholte Abhängigkeitsanforderungen löschen, die nicht zu Ihrem Testszenario gehören.

1. Speichern Sie das Projekt, und wählen Sie dann den Befehl **Test ausführen**, um den Webleistungstest lokal auszuführen und sicherzustellen, dass alles ordnungsgemäß funktioniert.

    ![][7]

### Parametrisieren des Webleistungstests

Sie können den Webleistungstest parametrisieren, indem Sie ihn in einen codierten Webleistungstest konvertieren und den Code anschließend bearbeiten. Alternativ dazu können Sie den Webleistungstest an eine Datenliste binden, sodass der Test die Daten durchläuft. Informationen zum Konvertieren des Webleistungstests in einen codierten Test finden Sie unter [Generieren und Ausführen eines codierten Webleistungstests](https://msdn.microsoft.com/library/ms182552.aspx). Informationen zum Binden von Daten an einen Webleistungstest finden Sie unter [Hinzufügen einer Datenquelle für einen Webleistungstest](https://msdn.microsoft.com/library/ms243142.aspx).

Für das vorliegende Beispiel konvertieren wir den Webleistungstest in einen codierten Test, sodass Sie die Flugzeugkennung durch eine generierte GUID ersetzen und weitere Fluganforderungen für verschiedene Standorte hinzufügen können.

### Erstellen eines Auslastungstestprojekts

Ein Auslastungstestprojekt besteht aus mindestens einem Szenario, das durch einen Webleistungstest und einen Komponententest beschrieben wird, sowie aus zusätzlichen festgelegten Einstellungen für den Auslastungstest. Die folgenden Schritte zeigen, wie Sie ein Auslastungstestprojekt erstellen.

1. Wählen Sie im Kontextmenü Ihres Webleistungs- und Auslastungstestprojekts die Optionen **Hinzufügen** > **Auslastungstest**. Klicken Sie im **Auslastungstest**-Assistenten auf die Schaltfläche **Weiter**, um die Testeinstellungen zu konfigurieren.

1. Wählen Sie im Abschnitt **Auslastungsmuster** aus, ob Sie mit einer konstanten Benutzerauslastung oder einer schrittweise Auslastung testen möchten. Letztere beginnt mit wenigen Benutzern und erhöht die Anzahl der Benutzer im Lauf der Zeit.

    Wenn Sie die Benutzerauslastung einigermaßen sicher einschätzen können und die Leistung Ihres aktuellen Systems testen möchten, wählen Sie **Konstante Auslastung**. Wenn Sie dagegen herausfinden möchten, ob das System unter verschiedenen Auslastungen eine konsistente Leistung erbringt, wählen Sie **Schrittweise Auslastung**.

1. Klicken Sie im Abschnitt **Testmischung** auf die Schaltfläche **Hinzufügen**, und wählen Sie den Test aus, den Sie in den Auslastungstest einschließen möchten. Sie können die Spalte **Verteilung** verwenden, um den Prozentsatz der Testläufe insgesamt für jeden Test anzugeben.

1. Geben Sie im Abschnitt **Testlaufeinstellungen** die Dauer des Auslastungstests an.

    >[AZURE.NOTE]Die Option **Testiterationen** ist nur verfügbar, wenn Sie den Auslastungstest lokal über Visual Studio ausführen.


1. Geben Sie im Abschnitt **Speicherort** der **Testlaufeinstellungen** das Verzeichnis an, in dem Auslastungstestanforderungen generiert werden. Der Assistent fordert Sie möglicherweise auf, sich bei Ihrem VSO-Konto anzumelden. Melden Sie sich bei Ihrem VSO-Konto an, und wählen Sie dann einen geografischen Standort. Wenn Sie fertig sind, klicken Sie auf die Schaltfläche **Fertig stellen**.

1. Nachdem der Auslastungstest erstellt wurde, öffnen Sie das LOADTEST-Projekt, und wählen Sie die aktuelle Testlaufeinstellung aus, beispielsweise **Testlaufeinstellungen** > **Testlaufeinstellungen1 [Aktiv]**. Dadurch werden die Testlaufeinstellungen im Fenster **Eigenschaften** geöffnet.

1. Im Abschnitt **Ergebnisse** des Fensters **Testlaufeinstellungen** sollte die Einstellung **Speicher für Details der zeitlichen Steuerung** den Standardwert **Keine** aufweisen. Ändern Sie diesen Wert zu **Alle einzelnen Details**, um weitere Informationen zum Ergebnis des Auslastungstests zu erhalten. Informationen zum Herstellen einer Verbindung mit Visual Studio Online und Ausführen eines Auslastungstests finden Sie unter [Auslastungstests](https://www.visualstudio.com/load-testing.aspx).

### Ausführen eines Auslastungstests mit Visual Studio Online

Wählen Sie den Befehl **Auslastungstest ausführen**, um den Testlauf zu starten.

![][8]

## Anzeigen und Analysieren von Auslastungstestergebnissen

Während der Auslastungstest ausgeführt wird, werden Informationen zur Leistung in einem Diagramm dargestellt. Es sollte ein Diagramm ähnlich dem folgenden angezeigt werden.

![][9]

1. Klicken Sie auf den Link **Bericht herunterladen** im oberen Bereich der Seite. Wenn der Bericht heruntergeladen wurde, klicken Sie auf die Schaltfläche **Bericht anzeigen**.

    Auf der Registerkarte **Diagramm** können Sie Diagramme für verschiedene Leistungsindikatoren sehen. Auf der Registerkarte **Zusammenfassung** werden die Gesamtergebnisse des Tests angezeigt. Auf der Registerkarte **Tabellen** wird die Gesamtanzahl von erfolgreichen und fehlerhaften Auslastungstests angezeigt.

1. Klicken Sie auf die Links ///unter den Zahlen in den Spalten **Test** > **Fehler** sowie **Fehler** > **Anzahl**, um Details zu den Fehlern anzuzeigen.

    Auf der Registerkarte **Detail** werden Informationen zu den virtuellen Benutzern und dem Testszenario für fehlerhafte Anforderungen angezeigt. Diese Daten können hilfreich sein, wenn der Auslastungstest mehrere Szenarien umfasst.

Informationen zum Anzeigen der Ergebnisse der Auslastungstests finden Sie unter [Analysieren von Auslastungstestergebnissen in der Diagrammansicht der Auslastungstestanalyse](https://www.visualstudio.com/load-testing.aspx).

## Automatisieren des Auslastungstests

Visual Studio Online-Auslastungstests stellen APIs bereit, mit deren Hilfe Sie in einem VSO-Konto Auslastungstests verwalten und Ergebnisse analysieren können. Weitere Informationen finden Sie unter [Cloud Load Testing Rest APIs](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx).

## Nächste Schritte
- [Überwachen und Diagnostizieren von Diensten in einer Entwicklungsumgebung auf einem lokalen Computer](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png

<!---HONumber=AcomDC_1210_2015-->
