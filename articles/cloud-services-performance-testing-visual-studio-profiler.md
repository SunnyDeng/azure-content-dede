<properties urldisplayname="Team Foundation Service" headerexpose="" pageTitle="Lokale Profilerstellung eines Cloud-Diensts im Serveremulator" metakeywords="" footerexpose="" description="" umbraconavihide="0" disquscomments="1" title="Lokales Testen der Leistung eines Cloud-Diensts im Azure-Serveremulator mithilfe des Visual Studio-Profiler" authors="ghogen" manager="douge" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

# Lokales Testen der Leistung eines Cloud-Diensts im Azure-Serveremulator mithilfe des Visual Studio-Profiler

Für das Testen der Leistung von Cloud-Diensten stehen zahlreiche Tools und Techniken zur Verfügung.
Wenn Sie einen Cloud-Dienst in Azure veröffentlichen, können Sie Visual Studio
zum Erfassen von Profildaten und zum lokalen Analysieren dieser Daten verwenden, wie in [Profilerstellung einer Azure-Anwendung][Profilerstellung einer Azure-Anwendung] beschrieben.
Außerdem haben Sie die Möglichkeit, mithilfe von Diagnosen eine Reihe von Leistungsindikatoren
zu verfolgen, wie in [Verwenden von Leistungsindikatoren in Azure][Verwenden von Leistungsindikatoren in Azure] beschrieben.
Vielleicht möchten Sie auch Ihre Anwendung lokal im Serveremulator profilieren, bevor Sie sie in der Cloud bereitstellen.

Dieser Artikel behandelt die CPU-Sampling-Methode der Profilerstellung, welche lokal im Emulator erfolgen kann. CPU-Sampling ist eine wenig intrusive Profilerstellungsmethode. Zu einem festgelegten Sampling-Intervall erstellt der Profiler eine Momentaufnahme der Aufrufliste. Die Daten werden über einen bestimmte Zeitraum hinweg erfasst und in einem Bericht angezeigt. Diese Profilerstellungsmethode gibt vor allem an, wo in einer rechenintensiven Anwendung die meiste CPU-Arbeit erfolgt. Dadurch können Sie sich auf den "Hot Path" konzentrieren, wo Ihre Anwendung die meiste Zeit verbringt.

## Voraussetzungen

Sie können den Profiler nur lokal ausführen, wenn Sie Visual Studio Premium oder Visual Studio Ultimate haben.

## Themen in diesem Artikel:

-   [Schritt 1: Konfigurieren von Visual Studio für die Profilerstellung][Schritt 1: Konfigurieren von Visual Studio für die Profilerstellung]

-   [Schritt 2: Anfügen an einen Prozess][Schritt 2: Anfügen an einen Prozess]

-   [Schritt 3: Anzeigen von Profilberichten][Schritt 3: Anzeigen von Profilberichten]

-   [Schritt 4: Änderungen vornehmen und Leistung vergleichen][Schritt 4: Änderungen vornehmen und Leistung vergleichen]

-   [Problembehandlung][Problembehandlung]

-   [Nächste Schritte][Nächste Schritte]

## <a name="step1"> </a>Schritt 1: Konfigurieren von Visual Studio für die Profilerstellung

Es gibt wenige Visual Studio-Konfigurationsoptionen, die für die Profilerstellung hilfreich sind. Um Profilberichte sinnvoll zu nutzen, benötigen Sie sowohl Symbole (.pdb-Dateien) für die Anwendung als auch Symbole für Systembibliotheken. Sie möchten möglicherweise überprüfen, dass Sie auf die verfügbaren Symbolserver verweisen. Wählen Sie dazu im Menü **Extras** in Visual Studio **Optionen**, und wählen Sie dann **Debugging** und anschließend **Symbole**. Überprüfen Sie, dass Microsoft Symbol Servers unter **Speicherorte für Symboldateien (.pdb)** aufgelistet ist. Sie können zudem auf <http://referencesource.microsoft.com/symbols> verweisen, wo möglicherweise zusätzliche Symbole verfügbar sind.

![][0]

Sie können die Berichte, welche der Profiler erstellt, vereinfachen, indem Sie "Nur eigenen Code" einstellen. Wenn Sie "Nur eigenen Code" aktiviert haben, sind die Funktionsanruflisten vereinfacht, und Aufrufe innerhalb der Bibliotheken und .NET Framework werden nicht in den Berichten angezeigt. Wählen Sie im Menü **Extras** **Optionen**. Erweitern Sie dann den Knoten **Leistungstools**, und wählen Sie **Allgemein**. Aktivieren Sie dann das Kontrollkästchen für **"Nur eigenen Code" für Profilerberichte aktivieren**.

![][1]

Sie können diese Anweisungen mit einem vorhandenen Projekt oder einem neuen Projekt verwenden. Wenn Sie ein neues Projekt erstellen, um die unten beschriebenen Techniken auszuprobieren, wählen Sie ein C#-**Azure-Cloud-Dienst**-Projekt, und wählen Sie eine **Webrolle** und eine **Workerrolle** aus.

![][2]

Erweitern Sie beispielsweise Ihr Projekt um Code, der viel Zeit in
Anspruch nimmt und zu offensichtlichen Leistungsproblemen führt. Fügen Sie zum Beispiel folgenden Code zu einem Workerrollenprojekt hinzu:

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = ""
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Rufen Sie diesen Code von der Ausführungsmethode in der RoleEntryPoint-abgeleiteten Klasse der Workerrolle ab.

    public override void Run()
    {
        while (true)
        {
            Concatenator.Concatenate(10000);
        }
    }

Erstellen Sie den Cloud-Dienst und führen Sie ihn lokal aus, wobei für die Lösungskonfiguration **Release** festgelegt ist. Dadurch wird sichergestellt, dass alle Dateien
und Ordner für das lokale Ausführen der Anwendung erstellt und alle Emulatoren gestartet wurden.

## <a name="step2"> </a>Schritt 2: Anfügen an einen Prozess

Anstatt die Profilerstellung der Anwendung über die Visual Studio 2010-IDE-Schnittstelle zu starten, müssen Sie den Profiler an einen laufenden Prozess anfügen.

Um den Profiler an einen Prozess anzufügen, wählen Sie im Menü **Analysieren** die Option **Profiler** und **Anfügen/Trennen**.

![][3]

Suchen Sie für eine Workerrolle den Prozess "WaWorkerHost.exe".

![][4]

Wenn sich der Projektordner auf einem Netzwerklaufwerk befindet, fragt Sie der Profiler nach einem anderen Speicherort für die Speicherung der Profilberichte.

Sie können auch eine Webrolle anfügen, indem Sie diese zu "WaIISHost.exe" hinzufügen.
 Wenn es mehrere Workerrollenprozesse in Ihrer Anwendung gibt, müssen Sie die "processID" verwenden, um sie zu unterscheiden. Sie können die "processID" programmgesteuert abfragen, indem Sie auf das Prozessobjekt zugreifen. Wenn Sie diesen Code zum Beispiel zur Ausführungsmethode der RoleEntryPoint-abgeleiteten Klasse in einer Rolle hinzufügen,
können Sie sich das Protokoll in der Serveremulator-Benutzeroberfläche ansehen, um herauszufinden, mit welchem Prozess eine Verbindung hergestellt werden muss.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Starten Sie die Serveremulator-Benutzeroberfläche, um das Protokoll anzuzeigen.

![][5]

Öffnen Sie das Konsolenfenster des Workerrollenprotokolls in der Serveremulator-Benutzeroberfläche, indem Sie auf die Titelleiste des Konsolenfensters klicken. Sie sehen die Prozess-ID im Protokoll.

![][6]

Führen Sie (falls erforderlich) nach dem Anfügen die Schritte in der Benutzeroberfläche der Anwendung aus, um das Szenario zu reproduzieren.

Wenn Sie die Profilerstellung beenden möchten, wählen Sie den Link **Beenden Sie die Profilerstellung** aus.

![][7]

## <a name="step3"> </a>Schritt 3: Anzeigen der Leistungsberichte

Der Leistungsbericht für Ihre Anwendung wird angezeigt.

Zu diesem Zeitpunkt stoppt der Profiler die Ausführung, speichert die Daten in einer
VSP-Datei, und zeigt eine Analyse dieser Daten an.

![][8]

Wenn Sie "String.wstrcpy" im Hot Path sehen, klicken Sie auf "Nur eigenen Code", um die Ansicht so zu ändern, dass nur Benutzercode angezeigt wird. Wenn Sie "String.Concat" sehen, sollten Sie auf "Gesamten Code anzeigen" drücken.

Sie werden sehen, dass die Concatenate-Methode und "String.Concat" einen großen
Teil der Ausführungszeit in Anspruch nehmen.

![][9]

Wenn Sie den Zeichenkettenverkettungscode in diesem Artikel hinzugefügt haben, sollten Sie dafür eine Warnung in der Aufgabenliste sehen. Sie sehen möglicherweise auch eine Warnung bezüglich einer sehr großen Menge an Garbage Collection, was an der Anzahl der entsorgten Zeichenketten liegt.

![][10]

## <a name="step4"> </a>Schritt 4: Änderungen vornehmen und Leistung vergleichen

Sie können auch die Leistung vor und nach einer Codeänderung vergleichen. Bearbeiten Sie den Code, um den Zeichenkettenverkettungsvorgang durch die Verwendung von StringBuilder zu ersetzen:

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Führen Sie eine andere Leistungsausführung durch, und vergleichen Sie dann die Leistung. Im Leistungs-Explorer können Sie, wenn die Ausführungen sich in derselben Sitzung befinden, nur beide Berichte auswählen, das Kurzwahlmenü öffnen und **Leistungsberichte vergleichen** auswählen. Wenn Sie dies mit einer Ausführung in einer anderen Leistungssitzung vergleichen möchten, öffnen Sie das Menü **Analysieren**, und wählen Sie **Leistungsberichte vergleichen**. Geben Sie beide Dateien im Dialogfeld ein, das angezeigt wird.

![][11]

Der Bericht zeigt die Unterschiede zwischen den beiden Ausführungen an.

![][12]

Glückwunsch! Sie haben die ersten Schritte mit dem Profiler geschafft.

## <a name="troubleshooting"> </a>Problembehandlung

-   Stellen Sie sicher, dass Sie eine Releaseversion profilieren.

-   Führen Sie den Leistungsassistenten aus, wenn die Anfügen/Trennen-Option nicht im Profilermenü aktiviert ist.

-   Verwenden Sie die Serveremulator-Benutzeroberfläche, um den Status der Anwendung anzuzeigen.

-   Wenn Sie Probleme beim Starten der Anwendungen im Emulator oder beim Anfügen an den Profiler haben, sollten Sie den Serveremulator herunterfahren und neu starten. Wenn das Problem dadurch nicht behoben wird, sollten Sie einen Neustart versuchen. Das Problem kann auftreten, wenn Sie den Serveremulator verwenden, um laufende Bereitstellungen anzuhalten und zu entfernen.

-   Wenn Sie Profilbefehle aus der Befehlszeile verwendet haben,
    insbesondere die globalen Einstellungen, sollten Sie sicherstellen, dass "VSPerfClrEnv /globaloff" aufgerufen und "VsPerfMon.exe" beendet wurde.

-   Wenn Ihnen beim Sampling die Nachricht "PRF0025: Es wurden keine Daten erfasst" angezeigt wird, müssen Sie sicherstellen, dass der angefügte Prozess CPU-Aktivität hat. Anwendungen, die keine Berechnungen vornehmen, produzieren möglicherweise keine Samplingdaten. Es ist auch möglich, dass der Vorgang beendet wurde, bevor das Sampling erfolgte. Überprüfen Sie, dass die Ausführungsmethode für eine Rolle, für die Sie ein Profil erstellen, nicht bendet wurde.

## <a name="nextSteps"> </a>Nächste Schritte

Die Instrumentierung von Azure-Binärdateien im Emulator wird im Visual Studio 2010-Profiler nicht unterstützt, aber wenn Sie die Speicherzuteilung testen möchten, können Sie diese Option bei der Profilerstellung auswählen. Sie können zudem die Parallelitätsprofilerstellung auswählen, mit der Sie bestimmen können, ob Threads beim Kampf um Sperren Zeit verschwenden, oder die Profilerstellung für Ebeneninteraktion, mit der Sie Leistungsprobleme bei der Interaktion zwischen den Ebenen einer Anwendung aufspüren können, meistens zwischen der Datenebene und einer Workerrolle. Sie können die Datenbankabfragen, welche die Anwendung generiert, anzeigen sowie die Profilerstellungsdaten zur Verbesserung der Datenbankverwendung nutzen. Informationen zur Profilerstellung für Ebeneninteraktion finden Sie unter [Walkthrough: Using the Tier Interaction Profiler in Visual Studio Team System 2010][Walkthrough: Using the Tier Interaction Profiler in Visual Studio Team System 2010] (in englischer Sprache).

  [Profilerstellung einer Azure-Anwendung]: http://msdn.microsoft.com/de-de/library/windowsazure/hh369930.aspx
  [Verwenden von Leistungsindikatoren in Azure]: http://www.windowsazure.com/de-de/develop/net/common-tasks/performance-profiling
  [Schritt 1: Konfigurieren von Visual Studio für die Profilerstellung]: #step1
  [Schritt 2: Anfügen an einen Prozess]: #step2
  [Schritt 3: Anzeigen von Profilberichten]: #step3
  [Schritt 4: Änderungen vornehmen und Leistung vergleichen]: #step4
  [Problembehandlung]: #troubleshooting
  [Nächste Schritte]: #nextSteps
  [0]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
  [1]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
  [2]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
  [3]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
  [4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
  [5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
  [6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
  [7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
  [8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
  [9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
  [10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png
  [11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
  [12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
  [Walkthrough: Using the Tier Interaction Profiler in Visual Studio Team System 2010]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
