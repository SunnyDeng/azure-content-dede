<properties title="Microsoft Azure Machine Learning Frequently Asked Questions (FAQ)" pageTitle="Azure Machine Learning FAQ | Azure" description="Frequently asked questions about Microsoft Azure Machine Learning" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="pamehta" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="pamehta"></tags>

# Häufig gestellte Fragen zu Azure Machine Learning (FAQ)

### ALLGEMEIN

**1. Was ist Microsoft Azure Machine Learning?**

Microsoft Azure Machine Learning ist ein vollständig verwalteter Dienst, mit dem Sie Vorhersageanalytik-Lösungen in der Cloud erstellen, testen, betreiben und verwalten können. Mit einem Browser können Sie sich bei Azure Machine Learning anmelden, Daten hochladen und sofort mit eigenen Experimenten im Bereich maschinelles Lernen beginnen. Dank der visuellen Komposition, einer umfangreichen Modulpalette und einer Bibliothek mit Ausgangsvorlagen können Sie gängige Aufgaben im Bereich maschinelles Lernen schnell und einfach ausführen. Sie können ein Vorhersagemodell in ML Studio mit wenigen Klicks in eine öffentliche REST-API umwandeln, die benutzerdefinierte Datentransformationslogik und komplexe Modelle für maschinelles Lernen kapsel.

**2. Was ist Azure Machine Learning Studio?**

Azure ML Studio ist eine im Webbrowser verfügbare Arbeitsumgebung. ML Studio enthält eine Palette von Modulen mit einer visuellen Kompositionsoberfläche, in der Sie durchgängige Datenworkflows in Form von Experimenten erstellen können. ML Studio bietet Module für die Erfassung und Transformation von Daten und die Funktionsauswahl für die Erstellung, Bewertung und Evaluierung von Vorhersagemodellen. Einige der modernsten Algorithmen für maschinelles Lernen in Bing und Xbox sind in ML Studio enthalten. Skalierbare Open Source-Pakete für maschinelles Lernen wie z. B. Vowpal Wabbit sind ebenfalls enthalten. ML Studio unterstützt R. Sie können Ihren existierenden R-Code in Ihren Experimenten verwenden. Mit ML Studio können Sie diese Algorithmen mit Ihrem R-Code zu Vorhersagemodellen kombinieren. ML Studio erleichtert die Zusammenarbeit, indem Sie Ihr Team in Ihre Arbeitsbereiche einladen können, wo die Teammitglieder Ihre Experimente anzeigen und bearbeiten können.

**3. Was ist der Azure ML API-Dienst?**

Mit dem ML API-Dienst können Sie Ihre in ML Studio erstellten Vorhersagemodelle als skalierbare, fehlertolerante Webdienste bereitstellen. Die vom ML API-Dienst erstellten Webdienste sind REST APIs und bieten eine Schnittstelle für die Kommunikation zwischen externen Anwendungen und Ihrem Vorhersageanalytik-Modell. Der Webdienst ermöglicht die Kommunikation mit einem Vorhersagemodell in Echtzeit und liefert Vorhersageergebnisse an beliebige externe Clientanwendungen. Der ML API-Dienst nutzt Microsoft Azure für Bereitstellung, Hosting und Verwaltung der Azure ML REST-APIs. Mit dem Azure ML API-Dienst können zwei Arten von Diensten erstellt werden. Stapelausführungsdienste für asynchronen Stapelzugriff und Anfrage-Antwort-Dienste für synchrone Antworten mit niedriger Latenz.

Sie können ein Vorhersagemodell in Ihrem Arbeitsbereich in den Stagingmodus überführen. Der ML API-Dienst generiert außerdem Hilfeseiten für Webdienste. Die Webdienst-Hilfeseiten enthalten Codebeispiele für Aufrufe des Webdiensts in C\#, R und Python. Sie können interaktive Aufrufe an den Dienst ausführen, um Ihren Webdienst zu testen. Der Webdienst im Stagingmodus kann anschließend mit wenigen Klicks in die Produktionsumgebung überführt werden. In der Produktionsumgebung können Sie bereitgestellte Dienste überwachen und Nutzung und Fehler im Azure-Portal verfolgen. Um die Webdienste zu aktualisieren, ändern Sie einfach das Modell in ML Studio und übertragen die Änderungen per Push in den Staging-Dienst.

**4. Wie kann ich Microsoft Azure Machine Learning nutzen?**

Besuchen Sie die Seite [Erste Schritte][], um mit der Nutzung von Azure Machine Learning zu beginnen. Im [Azure Machine Learning Center][] finden Sie Updates zum Dienst und Neuigkeiten im ML-Teamblog und können in Foren mit der Machine Learning-Community in Kontakt treten, Produkthilfen abrufen, die Modellgalerie durchsuchen und Feedback zum Dienst abgeben, um uns bei der Erarbeitung der Produkt-Roadmap zu unterstützen.

### ABRECHNUNG

**5. Wie funktioniert die Abrechnung von Machine Learning?**

Der Azure ML-Dienst wird pro Nutzungsstunde für aktive Experimente abgerechnet, und die Abrechnung erfolgt exakt für unvollständige Stunden. Die Azure ML API wird pro 1.000 Aufrufe der Vorhersage-API und pro Nutzungsstunde abgerechnet, wenn eine Vorhersage aktiv ausgeführt wird. Die Abrechnung erfolgt anteilig für Vorhersagemengen unterhalb von 1.000 und für teilweise genutzte Stunden.

Die Kosten werden pro Arbeitsbereich für Ihr Konto aggregiert. In jedem Arbeitsbereich sehen Sie Kosten für die drei Elemente

-   Studio Experiment-Stunden - Dieser Zähler aggregiert alle Berechnungskosten für laufende Experimente in ML Studio und laufende Vorhersagen in der Stagingumgebung.
-   Vorhersagestunden API-Dienst - Dieser Zähler enthält Berechnungskosten für Webdienste in der Produktionsumgebung.
-   Vorhersagen API-Dienst (in 1000er-Schritten) - Dieser Zähler enthält die angefallenen Kosten für Aufrufe an Ihren Produktions-Webdienst.

Weitere Informationen finden Sie auf der Seite mit Preisdetails <http://azure.microsoft.com/en-us/pricing/details/machine-learning/>.

**6. Existiert eine kostenlose Testversion von Azure Machine Learning?**

Azure ML ist Teil der kostenlosen Azure-Testversion. Wenn Sie sich für die kostenlose Azure-Testversion anmelden, können Sie die Azure-Dienste einen Monat lang ausprobieren. Weitere Informationen über die kostenlose Azure-Testversion finden Sie unter <http://azure.microsoft.com/en-us/pricing/free-trial-faq/>.

### MACHINE LEARNING STUDIO

**7. Welche Daten unterstützt Azure ML?**

Daten können auf zwei Arten in ML Studio geladen werden: per Upload lokaler Dateien als Datensätze oder über das Lesemodul für den Datenimport. Sie können lokale Daten als Datensätze hochladen, um neue Datensätze in ML Studio hinzuzufügen. Im Hilfethema **Datenabruf** in ML Studio finden Sie weitere Informationen zu unterstützten Dateiformaten.

Das **Lesemodul** kann Daten aus Azure-Tabellen, Azure-Blobs, SQL-Datenbanken (Azure) oder HDInsight lesen. Außerdem können Sie Daten von einer Datenquelle per HTTP abrufen. Weitere Details finden Sie im Hilfethema zum **Lesemodul** in ML Studio.

**8. Wie groß dürfen meine Datensätze sein?**

ML Studio unterstützt Trainings-Datensätze bis zu 10 GB. Für die Größe von Datensätzen für Webdienste gibt es keine Einschränkung. Die Verarbeitung größerer Datensätze per Hive oder SQL-Abfragen vor der Erfassung wird ebenfalls unterstützt. Wenn Sie mit Daten größer als 10 GB arbeiten, können Sie mehrere Datensätze erstellen und die Module "Partitionierung und Proben", "Aufteilen" oder "Verknüpfung" verwenden, um diese Datensätze in ML Studio erneut zu kombinieren, um Trainingssätze für die Erstellung von Vorhersagemodellen zu generieren. In der Modulhilfe in ML Studio finden Sie weitere Informationen zu diesen Modulen.

Für Datensätze größer als ca. 2 GB empfehlen wir, die Daten in den Azure-Speicher oder eine SQL-Datenbank (Azure) zu laden oder HDInsight zu verwenden, anstatt die Daten direkt aus lokalen Dateien hochzuladen.

**9. Welche ML-Algorithmen werden in ML Studio unterstützt?**

ML Studio unterstützt moderne ML-Algorithmen wie z. B. skalierbare Boosted Decision-Strukturen, Bayessche Empfehlungssysteme, tiefe neuronale Netze und die von Microsoft Research entwickelten "Decision Jungles". Skalierbare Open Source-Pakete für maschinelles Lernen wie z. B. Vowpal Wabbit sind ebenfalls enthalten. ML Studio unterstützt Algorithmen für maschinelles Lernen für mehrklassige und binäre Klassifizierung, Regression und Clustering. Eine vollständige Liste der Algorithmen für maschinelles Lernen finden Sie in der ML Studio-Hilfe.

**10. Mein Algorithmus für maschinelles Lernen, mein Datenformat oder meine Datentransformation ist nicht in Azure ML Studio enthalten. Welche Optionen habe ich?**

Sie können das [Forum für Benutzer-Feedback][] besuchen, um herauszufinden, welche Featureanfragen wir momentan verfolgen. Stimmen Sie für die entsprechende Anfrage ab, wenn ein von Ihnen gewünschtes Feature bereits angefordert wurde. Falls das gewünschte Feature nicht existiert, können Sie eine neue Anfrage erstellen. Sie können den Status Ihrer Anfrage ebenfalls in diesem Forum verfolgen. Wir verfolgen diese Liste regelmäßig und ändern den Verfügbarkeitsstatus von Features häufig.

**11. Kann ich meinen existierenden Code in ML Studio verwenden?**

ML Studio unterstützt momentan R, und Sie können Ihren existierenden R-Code in ML Studio importieren, zusammen im gleichen Experiment mit den Lernmodulen in ML Studio verwenden und das Resultat als Webdienst über Azure ML veröffentlichen. Azure ML ist der schnellste Weg zur Umwandlung Ihrer Analysebestände in R in Produktions-Webdienste auf Enterprise-Niveau. Im ML Studio-Hilfethema **Erweiterbarkeit mit R** erfahren Sie, wie Sie Ihre R-Codes und Visualisierungen in ML Studio verwenden können.

**12. Welche R-Pakete sind in ML Studio verfügbar?**

ML Studi unterstützt bereits über 350 R-Pakete, und diese Liste wird ständig erweitert. Im ML Studio-Hilfethema **Erweiterbarkeit mit R** erfahren Sie, wo Sie eine Liste der unterstützten R-Pakete finden. Falls das gewünschte Paket nicht in der Liste enthalten ist, können Sie den Namen des Pakets im [Forum für Benutzer-Feedback][] hinterlassen.

### ML API-DIENST

**13. Wann sollte ich mein Vorhersagemodell als Stapelausführungsdienst oder als Anfrage-/Antwort-Modell ausführen?**

Der Antwort-/Anfrage-Dienst (Request-Response Service RRS) ist ein hochskalierbarer Webdienst mit niedriger Latenz und stellt eine Schnittstelle für zustandslose Modelle bereit, die in der Experimentumgebung erstellt und veröffentlicht wurden. Der Stapelausführungsdienst (Batch Execution Service BES) dient zur asynchronen Bewertung eines Stapels von Datensätzen. Die Eingaben für RRS und BES sind einander sehr ähnlich. BES liest im Gegensatz zu RRS einen Block von Einträgen aus einer Vielzahl von Quellen wie z. B. Blobs, Tabellen in Azure, SQL-Datenbanken (Azure), HDInsight (Hive-Abfrage) und HTTP-Quellen. Die Bewertungsergebnisse werden in eine Datei im Azure-Blobspeicher geschrieben, und der Speicher-Endpunkt wird in der Antwort zurückgegeben.

Der Stapelausführungsdienst eignen sich für Szenarien, in denen eine große Anzahl von Datenpunkten stapelweise bewertet werden muss, oder wenn ein Großteil Ihrer Daten bereits in einem Dateiformat z. B. im Azure-Speicher oder einem Hadoop-Cluster vorliegt. Der Webdienst kann die gelesenen Daten transformieren, bevor er sie an das Modell überträgt. Sie können also einfach Ihre wöchentlichen Transaktionsdaten an einen Stapeldienst übergeben, der die Daten transformiert und Ergebnisse zurückgibt.

Anfrage-/Antwort-Dienste eignen sich für Szenarien, die Vorhersageanalytik nahezu in Echtzeit benötigen, um ein Live-Dashboard zu unterstützen oder um Benutzeraktionen oder Inhalte zu steuern, die über eine mobile oder Webanwendung ausgeliefert werden.

**14. Wie kann ich das Modell für einen bereitgestellten Produktionsdienst ändern?**

Sie können das Vorhersagemodell für einen bereitgestellten Dienst einfach ändern, indem Sie das Experiment bearbeiten und erneut ausführen, das Sie beim Erstellen und Speichern des trainierten Modells verwendet haben. Sobald die neue Version des trainierten Modells verfügbar ist, werden sie von ML Studio gefragt, ob Sie Ihren Staging-Webdienst aktualisieren möchten. Nach der Aktualisierung des Staging-Webdiensts ist dasselbe Update auch für die Übernahme in den Produktions-Webdienst verfügbar. Im ML Studio-Hilfethemen **Aktualisieren des Webdiensts** erfahren Sie, wie Sie einen bereitgestellten Webdienst aktualisieren können.

### SICHERHEIT UND VERFÜGBARKEIT

**15. Wer hat standardmäßig Zugriff auf den HTTP-Endpunkt für den Webdienst in der Produktionsumgebung? Wie kann ich den Zugriff auf den Endpunkt einschränken?**

Sobald ein Vorhersagemodell in die Produktionsumgebung übernommen wurde, wird die URL für die bereitgestellten Webdienste im Azure-Portal aufgelistet. Die URLs von Staging-Diensten sind in der ML Studio-Umgebung im Bereich Webdienste verfügbar, und die URLs von Produktionsdiensten sind im Azure-Portal im Bereich Maschinelles Lernen verfügbar. Die Zugriffsschlüssel für die Staging- und Produktions-Webdienste finden Sie im Webdienste-Dashboard in ML Studio bzw. im Azure-Portal. Die Zugriffsschlüssel werden für Aufrufe an die Webdienste in den Produktions- und Stagingumgebungen benötigt.

**16. Wie kann ich meinen Webdienst in der Produktionsumgebung überwachen?**

Sobald ein Vorhersagemodell in die Produktionsumgebung übernommen wurde, können Sie es im Azure-Portal überwachen. Jeder bereitgestellte Dienst hat ein eigenes Dashboard mit Überwachungsinformationen für den jeweiligen Dienst.

### SUPPORT UND LERNMATERIAL

**17. Wo finde ich Lernmaterial für Azure ML?**

Im [Azure Machine Learning Center][] finden Sie Videoanleitungen und Leitfäden. Diese ausführlichen Leitfäden enthalten eine Einführung in die Dienste und beschreiben den Lebenszyklus der Daten von Import und Bereinigung der Daten über die Erstellung von Vorhersagemodellen und der Bereitstellung der Modelle mit Azure ML.

Die Videoanleitungen enthalten einen visuellen Rundgang durch ML Studio und den ML API-Dienst. Die Videoanleitungen demonstrieren die Vielseitigkeit des Dienstes, häufig verwendete Module für Datenerfassung, -Bereinigung und -Verarbeitung; Erstellung von Vorhersagemodellen und Bereitstellung der Modelle. Die Videoanleitungen behandeln außerdem Aufgaben wie die Einrichtung von Arbeitsbereichen und die Bereitstellung von Staging-Modellen in der Produktionsumgebung.

Wir werden fortlaufend neues Material zum Machine Learning Center hinzufügen. Sie können jederzeit zusätzliches Material im Machine Learning Center im [Forum für Benutzer-Feedback][1] anfordern.

**18. Wo erhalte ich Support für Azure ML?**

Der Support für Azure ML ist Teil des Azure-Supportangebots. Um technischen Support für Azure ML zu erhalten, wählen Sie "Maschinelles Lernen" als Dienst aus. Daraufhin erhalten Sie eine Kategorie von Themen für die Übermittlung Ihres Support-Tickets. Weitere Informationen zum Azure-Support finden Sie unter <http://azure.microsoft.com/en-us/support/options/>

Für Azure Machine Learning gibt es außerdem ein Community-Forum auf MSDN, in dem Sie Fragen zu Azure ML stellen können. Das Forum wird vom Azure ML-Team überwacht. [Azure Forum][] besuchen.

  [Erste Schritte]: http://go.microsoft.com/fwlink/?LinkId=404226
  [Azure Machine Learning Center]: http://azure.microsoft.com/en-us/documentation/services/machine-learning/
  [Forum für Benutzer-Feedback]: http://go.microsoft.com/fwlink/?LinkId=404231
  [1]: https://windowsazure.uservoice.com/forums/257792-machine-learning
  [Azure Forum]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning
