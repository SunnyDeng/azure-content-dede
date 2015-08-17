<properties 
	pageTitle="Azure Machine Learning – häufig gestellte Fragen (FAQ) | Microsoft Azure" 
	description="Einführung in Azure Machine Learning: häufig gestellte Fragen (FAQ) zu Abrechnung, Funktionen und Einschränkungen von Clouddiensten für die optimierte Vorhersagemodellierung." 
	services="machine-learning" 
	documentationCenter="" 
	authors="pablissima" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="paulettm"/>

#Häufig gestellte Fragen zu Azure Machine Learning (FAQ): Abrechnung, Funktionen, Einschränkungen und Support

In diesem Dokument werden häufig gestellte Fragen zu Azure Machine Learning beantwortet, einem Clouddienst für Lösungen zur Vorhersagemodellierung und Operationalisierung über Webdienste. Dazu gehören Fragen zur Verwendung des Diensts, einschließlich Abrechnungsmodell, Funktionen, Einschränkungen und Unterstützung.
 
##Allgemeine Fragen

**Was ist Azure Machine Learning?**
 
Azure Machine Learning ist ein vollständig verwalteter Dienst, mit dem Sie Lösungen zu Vorhersageanalyse in der Cloud erstellen, testen, betreiben und verwalten können. Mit einem Browser können Sie sich anmelden, Daten hochladen und sofort mit Experimenten im Bereich Machine Learning beginnen. Dank der Vorhersagemodellierung per Drag & Drop, einer umfangreichen Modulpalette und einer Bibliothek mit Ausgangsvorlagen können Sie gängige Aufgaben im Bereich Machine Learning schnell und einfach ausführen. Weitere Informationen finden Sie auf der Übersichtsseite zu [Azure Machine Learning](/services/machine-learning/). Eine Einführung in Machine Learning mit wichtigen Begriffen und Konzepten finden Sie unter [Einführung in Azure Machine Learning](machine-learning-what-is-machine-learning.md).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
**Was ist Machine Learning Studio?**

Machine Learning Studio ist eine Workbench-Umgebung, auf die Sie über einen Webbrowser zugreifen. Machine Learning Studio enthält eine Palette von Modulen mit einer visuellen Kompositionsoberfläche, in der Sie durchgängige Datenworkflows in Form von Experimenten erstellen können.

Weitere Informationen zu Machine Learning Studio finden Sie unter [Was ist Machine Learning Studio](machine-learning-what-is-ml-studio.md).

**Was ist der Machine Learning-API-Dienst?**

Mit dem Machine Learning-API-Dienst können Sie Ihre in Machine Learning Studio erstellten Vorhersagemodelle als skalierbare, fehlertolerante Webdienste bereitstellen. Die vom Machine Learning-API-Dienst erstellten Webdienste sind REST-APIs. Sie bieten eine Schnittstelle für die Kommunikation zwischen externen Anwendungen und Ihren Predictive Analytics-Modellen.

Weitere Informationen finden Sie unter [Verbinden mit einem Machine Learning-Webdienst](machine-learning-connect-to-azure-machine-learning-web-service.md).


##Fragen zur Abrechnung

**Wie funktioniert die Abrechnung von Machine Learning?**

Abrechnungs- und Preisinformationen finden Sie unter [Machine Learning Preise](http://azure.microsoft.com/pricing/details/machine-learning/).

**Gibt es eine kostenlose Testversion von Machine Learning?**

 Wenn Sie sich für die kostenlose Azure-Testversion anmelden, können Sie die Azure-Dienste einen Monat lang ausprobieren. Weitere Informationen zur kostenlosen Azure-Testversion finden Sie in den [häufig gestellten Fragen zur kostenlosen Testversion von Azure](/pricing/free-trial-faq/).

## Fragen zu Machine Learning Studio

###Erstellen von Experimenten	
**Gibt es Versionskontrolle oder eine Git-Integration für Experimentdiagramme?**

Nein. Bei jedem Ausführen eines Experiments wird die entsprechende Version des Diagramms jedoch beibehalten und kann nicht von anderen Benutzern geändert werden.

###Importieren und Exportieren von Daten für Machine Learning
**Welche Datenquellen unterstützt Machine Learning?**

Daten können auf zwei Arten in Machine Learning Studio geladen werden: per Upload lokaler Dateien als DataSets oder über das Lesemodul für den Datenimport. Sie können lokale Daten hochladen, um neue DataSets in Machine Learning Studio hinzuzufügen. Weitere Informationen zu den unterstützten Dateiformaten finden Sie unter [Importieren von Trainingsdaten in Machine Learning Studio](machine-learning-import-data.md).


####<a id="ModuleLimit"></a>Wie groß können DataSets für meine Module sein?

Module in Machine Learning Studio unterstützen in normalen Anwendungsfällen DataSets bis zu einer Größe von 10 GB an dichten numerischen Daten. Wenn ein Modul mehrere Eingaben akzeptiert, entsprechen 10 GB der Summe aller Eingabegrößen. Sie können über Hive- oder Azure SQL-Datenbank-Abfragen oder durch Vorverarbeitung per Lernen nach Anzahl auch Teile größerer DataSets übernehmen.

Die folgenden Typen von Daten können während der Featurenormalisierung in größere DataSets erweitert werden und sind auf weniger als 10 GB beschränkt:

- Platzsparend
- Kategorisch
- Zeichenfolgen
- Binärdaten

Die folgenden Bereiche sind auf DataSets mit einer Größe unter 10 GB beschränkt:

- Empfohlene Module
- SMOTE-Modul
- Skripting-Module: R, Python, SQL
- Module, bei denen die Größe der Ausgabedaten die der Eingabedaten überschreiten kann, z. B. Join oder Feature-Hashing.
- Cross-Validation, Sweep Parameters, Ordinal Regression und One-vs-All Multiclass, wenn eine sehr große Anzahl von Iterationen durchgeführt wird.

Für DataSets größer als einige GB sollten Sie die Daten in Azure Storage oder Azure SQL-Datenbank laden oder HDInsight verwenden, anstatt die Daten direkt aus lokalen Dateien hochzuladen.


####<a id="UploadLimit"></a>Was sind die Limits für Datenuploads?
Laden Sie für DataSets größer als einige GB die Daten in Azure Storage oder Azure SQL-Datenbank, oder verwenden Sie HDInsight, anstatt die Daten direkt aus lokalen Dateien hochzuladen.

**Können Daten von Amazon S3 gelesen werden?**

Wenn Ihre Daten nicht sehr umfangreich sind und Sie diese über eine HTTP-URL verfügbar machen möchten, können Sie das [Reader][reader]-Modul verwenden. Größere Datenmengen sollten Sie zunächst in Azure Storage übertragen und anschließend mit dem [Reader][reader]-Modul in das Experiment übernehmen. <!--
<SEE CLOUD DS PROCESS>
-->

**Gibt es eine integrierte Imageeingabefunktion?**

Informationen über die Imageeingabefunktion finden Sie in der Referenz zu [Image Reader][image-reader].

###Module 

**Mein Algorithmus, mein Datenformat oder meine Datentransformation ist nicht in Azure ML Studio enthalten. Welche Optionen habe ich?**

Sie können das [Forum für Benutzer-Feedback](http://go.microsoft.com/fwlink/?LinkId=404231) besuchen, um herauszufinden, welche Featureanfragen wir momentan verfolgen. Stimmen Sie für die entsprechende Anfrage ab, wenn ein von Ihnen gewünschtes Feature bereits angefordert wurde. Falls das gewünschte Feature nicht existiert, können Sie eine neue Anfrage erstellen. Sie können den Status Ihrer Anfrage ebenfalls in diesem Forum verfolgen. Wir verfolgen diese Liste regelmäßig und aktualisieren den Verfügbarkeitsstatus von Features häufig. Mit der integrierten Unterstützung für R und Python können außerdem benutzerdefinierte Transformationen nach Bedarf erstellt werden.


**Kann ich meinen vorhandenen Code in ML Studio verwenden?**

Ja, Sie können Ihren vorhandenen R-Code in ML Studio importieren, zusammen im gleichen Experiment mit den Lernmodulen in Machine Learning Studio verwenden und das Ergebnis als Webdienst über Azure Machine Learning veröffentlichen. Weitere Informationen finden Sie unter [Erweitern Ihres Experiments mit R](machine-learning-extend-your-experiment-with-r.md).

**Ist es möglich, z. B. mit [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) ein Modell zu definieren?**

Nein, dies wird nicht unterstützt. Benutzerdefinierter R- und Python-Code kann jedoch zum Definieren eines Moduls verwendet werden.


###Datenverarbeitung 
**Gibt es eine Möglichkeit zum interaktiven Anzeigen von Daten (neben R-Visualisierungen) im Experiment?**

Durch Klicken auf die Ausgabe eines Moduls können Sie die Daten visualisieren und Statistiken abrufen.

**Bei der Vorschau von Ergebnissen oder Daten im Browser ist die Anzahl der Zeilen und Spalten beschränkt. Warum?**

Da die an den Browser übertragenen Daten umfangreich sein können, ist die Datengröße beschränkt, damit ML Studio nicht verlangsamt wird. Es ist besser, die Daten/Ergebnisse herunterzuladen oder Excel oder ein anderes Tool zu verwenden, um die gesamten Daten zu visualisieren.

###Algorithmen
**Welche vorhandenen Algorithmen werden in Machine Learning Studio unterstützt?**

Machine Learning Studio unterstützt moderne Algorithmen wie z. B. skalierbare Boosted Decision-Strukturen, Bayessche Empfehlungssysteme, tiefe neuronale Netze und die von Microsoft Research entwickelten "Entscheidungsdschungel". Skalierbare Open Source-Pakete für Machine Learning wie z. B. Vowpal Wabbit sind ebenfalls enthalten. Machine Learning Studio unterstützt Algorithmen für Machine Learning für mehrklassige und binäre Klassifizierung, Regression und Clustering. Weitere Informationen finden Sie in der vollständigen Liste der [Machine Learning-Module][machine-learning-modules].

**Wird automatisch der richtige Machine Learning-Algorithmus für meine Daten vorgeschlagen?**

Nein, es gibt jedoch in Machine Learning Studio verschiedene Möglichkeiten zum Vergleichen der Ergebnisse jedes Algorithmus, um den richtigen für das Problem zu ermitteln.

**Gibt es Richtlinien zum Auswählen eines Algorithmus für die bereitgestellten Algorithmen?** Informationen dazu finden Sie unter [Auswählen eines Algorithmus](machine-learning-algorithm-choice.md).

**Wurden die bereitgestellten Algorithmen in R oder Python geschrieben?**

Diese Algorithmen wurden größtenteils in kompilierten Sprachen geschrieben, um eine höhere Leistung zu erzielen.

**Gibt es nähere Informationen zu den Algorithmen?**

Die Dokumentation enthält Informationen zu den Algorithmen. Darin werden auch die Parameter zur Feinabstimmung beschrieben, damit Sie den Algorithmus für Ihre Zwecke optimieren können.

**Gibt es Unterstützung für das Onlinelernen?**

Nein, derzeit wird nur programmgesteuertes erneutes Trainieren unterstützt.

**Können die Ebenen eines Modells für ein neuronales Netz mit dem integrierten Modul visualisiert werden?**

Nr.

**Kann ich eigene Module in C# oder einer anderen Sprache erstellen?**

Derzeit können neue benutzerdefinierte Module nur in R erstellt werden.

###R-Modul 
**Welche R-Pakete sind in Machine Learning Studio verfügbar?**

Machine Learning Studio unterstützt bereits über 400 R-Pakete, und diese Liste wird ständig erweitert. Anweisungen zum Abrufen einer Liste der unterstützten R-Pakete finden Sie unter [Erweitern des Experiments mit R](machine-learning-extend-your-experiment-with-r.md). Falls das gewünschte Paket nicht in der Liste enthalten ist, können Sie den Namen des Pakets im [Forum für Benutzer-Feedback](http://go.microsoft.com/fwlink/?LinkId=404231) hinterlassen.

**Ist es möglich, benutzerdefinierte R-Module zu erstellen?**

Ja. Weitere Informationen finden Sie unter [Erstellen von benutzerdefinierten R-Modulen in Azure Machine Learning](machine-learning-custom-r-modules.md).

**Gibt es eine REPL-Umgebung für R?**

Nein, es gibt keine REPL-Umgebung für R in Machine Learning Studio.

###Python-Modul 

**Ist es möglich, benutzerdefinierte Python-Module zu erstellen?**

Derzeit nicht, mit dem Python-Standardmodul kann jedoch das gleiche Ergebnis erzielt werden.

**Gibt es eine REPL-Umgebung für Python?**

Nein, es gibt keine REPL-Umgebung für Python in Machine Learning Studio.

## Webdienst
###Programmgesteuertes erneutes Trainieren von Modellen

**Wie kann ich Azure ML-Modelle programmgesteuert erneut trainieren?** Verwenden Sie die APIs zum erneuten Trainieren. Beispielcode steht [hier](https://azuremlretrain.codeplex.com/) zur Verfügung.

###Erstellen

**Kann ich das Modell lokal oder in einer Anwendung ohne Internetverbindung bereitstellen?** Nr.


**Gibt es eine Grundlatenz, die für alle Webdienste erwartet wird?**

Informationen hierzu finden Sie unter [Einschränkungen für Azure-Abonnements](../azure-subscription-service-limits.md)

###Verwenden Sie
**Wann sollte ich mein Vorhersagemodell als Stapelausführungsdienst oder als Anfrage-Antwort-Dienst ausführen?**

Der Anfrage-Antwort-Dienst (Request Response Service, RRS) ist ein hoch skalierbarer Webdienst mit niedriger Latenz, der eine Schnittstelle für zustandslose Modelle bereitstellt, die in der Experimentumgebung erstellt und veröffentlicht wurden. Der Stapelausführungsdienst (Batch Execution Service, BES) dient zur asynchronen Bewertung eines Stapels von Datensätzen. Die Eingaben für RRS und BES sind einander sehr ähnlich. BES liest im Gegensatz zu RRS einen Block von Einträgen aus einer Vielzahl von Quellen wie z. B. dem Blobdienst und dem Tabellenspeicherdienst in Azure, Azure SQL-Datenbank, HDInsight (Hive-Abfrage) und HTTP-Quellen. Weitere Informationen finden Sie unter [Verwenden von Machine Learning-Webdiensten](machine-learning-consume-web-services.md).

**Wie aktualisiere ich das Modell für den bereitgestellten Webdienst?**

Sie können das Vorhersagemodell für einen bereitgestellten Dienst einfach ändern, indem Sie das Experiment bearbeiten und erneut ausführen, das Sie beim Erstellen und Speichern des trainierten Modells verwendet haben. Sobald die neue Version des trainierten Modells verfügbar ist, werden sie von ML Studio gefragt, ob Sie Ihren Staging-Webdienst aktualisieren möchten. Nach dem Update des Stagingwebdiensts ist dasselbe Update auch für die Übernahme in den Produktionswebdienst verfügbar. Ausführliche Informationen zum Aktualisieren eines bereitgestellten Webdiensts finden Sie unter [Veröffentlichen von Machine Learning-Webdiensten](machine-learning-publish-a-machine-learning-web-service.md).


**Wie überwache ich meinen Webdienst in der Produktionsumgebung?**

Sobald ein Vorhersagemodell in die Produktionsumgebung übernommen wurde, können Sie es im Azure-Portal überwachen. Jeder bereitgestellte Dienst hat ein eigenes Dashboard mit Überwachungsinformationen für den jeweiligen Dienst.

**Kann ich die Ausgabe meines RRS/BES an einer Stelle einsehen?**

Ja. Sie müssen einen Blobspeicherort angeben, dann erfolgt dort die Ausgabe des RRS/BES.



##Skalierbarkeit 

**Wie skalierbar ist der Webdienst?**

Derzeit gilt ein Höchstwert von 20 gleichzeitigen Anforderungen pro Endpunkt, es ist jedoch eine Skalierung auf 80 Endpunkte möglich. Dies ergibt 4.800 gleichzeitige Anforderungen bei Verwendung jeder Ressource (300 Worker).


**Werden R-Aufträge auf die Knoten verteilt?**

Nr.


**Wie viele Daten kann ich zum Trainieren verwenden?**

Module in Machine Learning Studio unterstützen in normalen Anwendungsfällen DataSets bis zu einer Größe von 10 GB an dichten numerischen Daten. Wenn ein Modul mehrere Eingaben akzeptiert, entsprechen 10 GB der Summe aller Eingabegrößen. Sie können über Hive- oder Azure SQL-Datenbank-Abfragen oder durch Vorverarbeitung per Lernen nach Anzahl auch Teile größerer DataSets übernehmen.

Die folgenden Typen von Daten können während der Featurenormalisierung in größere DataSets erweitert werden und sind auf weniger als 10 GB beschränkt:

- Platzsparend
- Kategorisch
- Zeichenfolgen
- Binärdaten

Die folgenden Bereiche sind auf DataSets mit einer Größe unter 10 GB beschränkt:

- Empfohlene Module
- SMOTE-Modul
- Skripting-Module: R, Python, SQL
- Module, bei denen die Größe der Ausgabedaten die der Eingabedaten überschreiten kann, z. B. Join oder Feature-Hashing.
- Cross-Validation, Sweep Parameters, Ordinal Regression und One-vs-All Multiclass, wenn eine sehr große Anzahl von Iterationen durchgeführt wird.

Für DataSets größer als einige GB sollten Sie die Daten in Azure Storage oder Azure SQL-Datenbank laden oder HDInsight verwenden, anstatt die Daten direkt aus lokalen Dateien hochzuladen.


**Gibt es Vektorgrößenbeschränkungen?**

Zeilen und Spalten sind jeweils auf die .NET-Einschränkung für Ganzzahlen beschränkt: 2.147.483.647.

**Kann die Größe des virtuellen Computers für die Ausführung angepasst werden?**

Nr.

##Sicherheit und Verfügbarkeit 

**Wer hat standardmäßig Zugriff auf den HTTP-Endpunkt für den Webdienst in der Produktionsumgebung? Wie kann ich den Zugriff auf den Endpunkt einschränken?**

Sobald ein Vorhersagemodell in die Produktionsumgebung übernommen wurde, wird die URL für die bereitgestellten Webdienste im Azure-Portal aufgelistet. Die URLs von Stagingdiensten sind in der Machine Learning Studio-Umgebung im Bereich "Webdienste" verfügbar, und die URLs von Produktionsdiensten sind im Azure-Portal im Bereich für Machine Learning verfügbar. Die Zugriffsschlüssel für die Staging- und Produktionswebdienste finden Sie im Webdienst-Dashboard in Machine Learning Studio bzw. im Azure-Portal. Die Zugriffsschlüssel werden für Aufrufe an die Webdienste in den Produktions- und Stagingumgebungen benötigt. Weitere Informationen finden Sie unter [Verbinden mit einem Machine Learning-Webdienst](machine-learning-connect-to-azure-machine-learning-web-service.md).

**Was geschieht, wenn mein Speicherkonto nicht gefunden werden kann?**

In Machine Learning Studio ist ein vom Benutzer bereitgestelltes Azure-Speicherkonto zum Speichern von temporären Daten beim Ausführen des Workflows erforderlich. Dieses Speicherkonto wird Machine Learning Studio beim Erstellen des Arbeitsbereichs zur Verfügung gestellt. Wenn das Speicherkonto nach dem Erstellen des Arbeitsbereichs gelöscht wird, funktioniert dieser Arbeitsbereich nicht mehr, und alle dort ausgeführten Experimente schlagen fehl.
 
Wenn Sie das Speicherkonto versehentlich löschen, ist die einzige Möglichkeit der Wiederherstellung ein erneutes Erstellen des Speicherkontos mit identischem Namen und in der identischen Region wie das gelöschte. Anschließend muss der Zugriffsschlüssel wieder synchronisiert werden.
 

**Was geschieht, wenn mein Speicherkonto-Zugriffsschlüssel nicht mehr synchronisiert ist?** In Machine Learning Studio ist ein vom Benutzer bereitgestelltes Azure-Speicherkonto zum Speichern von temporären Daten beim Ausführen des Workflows erforderlich. Dieses Speicherkonto wird Machine Learning Studio beim Erstellen des Arbeitsbereichs zur Verfügung gestellt, und der Zugriffsschlüssel wird diesem Arbeitsbereich zugewiesen. Wenn Zugriffsschlüssel nach dem Erstellen des Arbeitsbereichs geändert werden, kann dieser Arbeitsbereich nicht mehr auf das Speicherkonto zugreifen, und alle in diesem Arbeitsbereich ausgeführten Experimente schlagen fehl.

Wenn Sie Zugriffsschlüssel für Speicherkonten geändert haben, synchronisieren Sie die Zugriffsschlüssel in der Arbeitsbereichseinstellung im Azure-Portal unbedingt neu.


##Azure Marketplace 

Weitere Informationen finden Sie unter [FAQ zum Veröffentlichen und Verwenden von Apps im Machine Learning Marketplace](machine-learning-marketplace-faq.md).

##Support und Lernmaterial 

**Wo finde ich Lernmaterial für Azure ML?**

Im [Azure Machine Learning Documentation Center](/services/machine-learning/) finden Sie Videoanleitungen und Leitfäden. Diese ausführlichen Leitfäden enthalten eine Einführung in die Dienste und beschreiben den Lebenszyklus der Daten vom Import und der Bereinigung der Daten bis hin zur Erstellung von Vorhersagemodellen und der Bereitstellung der Modelle in der Produktionsumgebung mit Azure ML.

Wir werden fortlaufend neues Material zum Machine Learning Center hinzufügen. Sie können jederzeit zusätzliches Material im Machine Learning Center im [Forum für Benutzerfeedback](https://windowsazure.uservoice.com/forums/257792-machine-learning) anfordern.

Es werden auch Schulungen an der [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning) angeboten.

**Wo erhalte ich Support für Azure Machine Learning?**

Technischen Support für Azure Machine Learning erhalten Sie, indem Sie den [Azure-Support](/support/options/) besuchen und **Machine Learning** auswählen.

Für Azure Machine Learning gibt es außerdem ein Community-Forum auf MSDN, in dem Sie Fragen zu Azure ML stellen können. Das Forum wird vom Azure ML-Team überwacht. [Azure Forum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) besuchen.


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 

<!---HONumber=August15_HO6-->