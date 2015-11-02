<properties 
	pageTitle="Ausführen von Python-Machine Learning-Skripts | Microsoft Azure" 
	description="Beschreibt die Entwurfsprinzipien, die der Unterstützung für Python-Skripts in Azure Machine Learning zugrunde liegen, sowie die grundlegenden Verwendungsszenarien, Funktionen und Einschränkungen." 
	keywords="maschinelles Lernen in Python,Pandas,Python Pandas,Python-Skripts,Ausführen von Python-Skripts"
	services="machine-learning"
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2015" 
	ms.author="bradsev" />


# Ausführen von Python-Machine Learning-Skripts in Azure Machine Learning Studio

Dieses Thema beschreibt die Entwurfsprinzipien, die der aktuellen Unterstützung von Python-Skripts in Azure Machine Learning zugrunde liegen. Zudem werden die Hauptfunktionen beschrieben, einschließlich der Unterstützung für das Importieren von vorhandenem Code und das Exportieren von Visualisierungen. Abschließend werden einige der Einschränkungen und laufende Arbeiten erläutert.

[Python](https://www.python.org/) ist ein unverzichtbares Tool im Werkzeugkasten vieler Datenanalysten. Es bietet eine elegante und prägnante Syntax, plattformübergreifende Unterstützung, eine umfangreiche Sammlung leistungsstarker Bibliotheken sowie ausgereifte Entwicklungstools. Python kommt in allen Phasen des Workflows zum Einsatz, der in der Regel bei der Machine Learning-Modellierung verwendet wird, beginnend bei der Datenerfassung und -verarbeitung über die Featureentwicklung bis hin zu Training, Überprüfung und Bereitstellung von Modellen.

Azure Machine Learning Studio unterstützt das Einbetten von Python-Skripts in verschiedene Teile eines Machine Learning-Versuchs und auch deren nahtlose Veröffentlichung als skalierbare, operationalisierte Webdienste in Microsoft Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Entwurfsprinzipien für Python-Skripts in Machine Learning
Die primäre Schnittstelle zu Python in Azure Machine Learning Studio wird durch das Modul [Execute Python Script][execute-python-script] bereitgestellt, das in Abbildung 1 dargestellt wird.

![image1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Bild2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

Abbildung 1. Das **Execute Python Script**-Modul

Ebenso wie sein R-Äquivalent, das [Execute R Script][execute-r-script]-Modul, akzeptiert das [Execute Python Script][execute-python-script]-Modul bis zu drei Eingaben und erzeugt bis zu zwei Ausgaben (wie unten beschrieben). Der auszuführende Python-Code wird in das Parameterfeld als speziell benannte Einstiegspunktfunktion namens `azureml_main` eingegeben. Im Folgenden sind die wichtigsten Entwurfsprinzipien zur Implementierung dieses Moduls aufgeführt:

1.	*Muss für Python-Benutzer idiomatisch sein.* Die meisten Python-Benutzer schreiben ihren Code als Funktionen innerhalb von Modulen. Daher werden in ein Modell der obersten Ebene eher selten viele ausführbare Anweisungen eingefügt. Demzufolge akzeptiert das Skriptfeld auch eine speziell benannte Python-Funktion und nicht nur eine Sequenz von Anweisungen. Die in der Funktion verfügbar gemachten Objekte sind Standardtypen der Python-Bibliothek, z. B. [Pandas](http://pandas.pydata.org/)-Datenrahmen und [NumPy](http://www.numpy.org/)-Arrays.
2.	*Erfordert eine hohe Genauigkeit zwischen lokalen und Cloudausführungen.* Das zum Ausführen des Python-Codes verwendete Back-End basiert auf [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1, einer weit verbreiteten plattformübergreifenden, wissenschaftlichen Python-Distribution. Sie wird mit knapp 200 der gängigsten Python-Pakete geliefert. Aus diesem Grund kann ein Datenanalyst den Code in der lokalen, mit Azure Machine Learning kompatiblen Anaconda-Umgebung debuggen und qualifizieren. Hierzu können vorhandene Entwicklungsumgebungen wie [IPython](http://ipython.org/) Notebook oder [Python-Tools für Visual Studio] verwendet und mit hoher Zuverlässigkeit als Teil eines Azure Machine Learning-Versuchs ausgeführt werden. Zudem handelt es sich beim `azureml_main`-Einstiegspunkt um eine einfache Python-Funktion, die ohne spezifischen Azure Machine Learning-Code und ohne Installation des SDK erstellt werden kann.
3.	*Muss eine nahtlos mit anderen Azure Machine Learning-Modulen einsetzbar sein.* Das Modul [Execute Python Script][execute-python-script] akzeptiert Azure Machine Learning-Standard-Datasets als Eingabe und Ausgabe. Das zugrunde liegende Framework verbindet transparent und effizient die Laufzeiten von Azure Machine Learning und Python (mit Unterstützung von Features wie z. B. fehlenden Werten). Python kann daher in Verbindung mit vorhandenen Azure Machine Learning-Workflows eingesetzt werden, auch mit solchen, die R- und SQLite-Aufrufe verwenden. Daher kann man sich Workflows vorstellen, die folgende Aktionen ausführen:
  * Verwenden von Python und Pandas zur Datenvorbereitung und -bereinigung 
  * Einführen von Daten in eine SQL-Transformation, wobei mehrere Datasets zu Features verknüpft werden 
  * Trainieren von Modellen mit der umfassenden Sammlung von Algorithmen in Azure Machine Learning 
  * Evaluieren und Nachverarbeiten der Ergebnisse mit R.


## Grundlegende Verwendungsszenarien für Python-Skripts in Machine Learning
In diesem Abschnitt untersuchen wir einige der grundlegenden Anwendungsmöglichkeiten des Moduls [Execute Python Script][execute-python-script]. Wie bereits erwähnt, werden alle Eingaben in das Python-Modul als Pandas-Datenrahmen verfügbar gemacht. Weitere Informationen zu Python Pandas und wie es zum effektiven und effizienten Verarbeiten von Daten verwendet werden kann, finden Sie in *Python for Data Analysis* (in englischer Sprache; Sebastopol (Kalifornien, USA): O' Reilly, 2012) von W. McKinney. Die Funktion muss einen einzigen Pandas-Datenrahmen zurückgeben, der in eine Python-[Sequenz](https://docs.python.org/2/c-api/sequence.html) wie z. B. ein Tupel, eine Liste oder ein NumPy-Array eingebettet ist. Das erste Element der Sequenz wird anschließend am ersten Ausgabeport des Moduls zurückgegeben. Dieses Schema wird in Abbildung 2 dargestellt.

![image3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

Abbildung 2. Zuordnung von Eingabeports zu Parametern und des Rückgabewerts zum Ausgabeport.

Eine ausführlichere Semantik zur Zuordnung der Eingangsports zu Parametern der `azureml_main`-Funktion wird in Tabelle 1 dargestellt:

![image1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabelle 1. Zuordnung von Eingabeports zu Funktionsparametern.

Beachten Sie, dass die Zuordnung zwischen Eingabeports und Funktionsparametern nach Position erfolgt, d. h. der erste verbundene Eingabeport wird dem ersten Parameter der Funktion zugeordnet, und der zweite Eingabeport (falls verbunden) wird dem zweiten Parameter der Funktion zugeordnet.

## Übersetzung von Eingabe- und Ausgabetypen
Wie bereits erwähnt, werden Eingabedatasets in Azure Machine Learning in Datenrahmen in Pandas konvertiert, und Ausgabedatenrahmen werden in Azure Machine Learning-Datasets zurückkonvertiert. Die folgenden Konvertierungen werden durchgeführt:

1.	Zeichenfolgen- und numerische Spalten werden unverändert konvertiert, und fehlende Werte in einem Dataset werden in NA-Werte in Pandas konvertiert. Dieselbe Konvertierung erfolgt auf dem Rückweg (NA-Werte in Pandas werden in fehlende Werte in Azure Machine Learning konvertiert).
2.	Indexvektoren in Pandas werden in Azure Machine Learning nicht unterstützt, und alle Eingabedatenrahmen in der Python-Funktion besitzen immer einen numerischen 64-Bit-Index von 0 bis zur Anzahl der Zeilen minus 1. 
3.	Azure Machine Learning-Datasets können keine doppelten Spaltennamen aufweisen und keine Spaltennamen, die keine Zeichenfolgen sind. Wenn ein Ausgabedatenrahmen nicht-numerische Spalten enthält, ruft das Framework `str` für die Spaltennamen auf. Ebenso werden alle doppelten Spaltennamen automatisch geändert, um sicherzustellen, dass die Namen eindeutig sind. Das Suffix (2) wird dem ersten Duplikat, das Suffix (3) dem zweiten Duplikat usw. hinzugefügt.

## Operationalisieren von Python-Skripts
Alle [Execute Python Script][execute-python-script]-Module in einem Bewertungsexperiment werden bei der Veröffentlichung als Webdienst aufgerufen. Abbildung 3 zeigt beispielsweise ein Bewertungsexperiment, das den Code zum Auswerten eines einzelnen Python-Ausdrucks enthält.

![image4](./media/machine-learning-execute-python-scripts/figure3a.png)

![Bild5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

Abbildung 3. Webdienst zum Auswerten eines Python-Ausdrucks.

Ein aus diesem Versuch erstellter Webdienst akzeptiert als Eingabe einen Python-Ausdruck (als Zeichenfolge), sendet ihn an den Python-Interpreter und gibt eine Tabelle mit dem Ausdruck und dem ausgewerteten Ergebnis zurück.

## Importieren vorhandener Python-Skriptmodule
Ein häufiges Anwendungsbeispiel für viele Datenanalysten besteht darin, vorhandene Python-Skripts in Azure Machine Learning-Versuche zu integrieren. Statt den gesamten Code zu verketten und in ein einziges Skriptfeld einzufügen, akzeptiert das [Execute Python Script][execute-python-script]-Modul einen dritten Eingabeport, mit dem eine ZIP-Datei, die die Python-Module enthält, verbunden werden kann. Die Datei wird dann vom Ausführungs-Framework entpackt, und die Inhalte werden dem Bibliothekspfad des Python-Interpreters hinzugefügt. Die `azureml_main`-Einstiegspunktfunktion kann diese Module anschließend direkt importieren.

Stellen Sie sich beispielsweise die Datei "Hello.py" mit einer einfachen "Hello, World"-Funktion vor.

![Bild6](./media/machine-learning-execute-python-scripts/figure4.png)

Abbildung 4. Benutzerdefinierte Funktion.

Danach können wir eine Datei "Hello.zip" erstellen, die "Hello.py" enthält:

![Bild7](./media/machine-learning-execute-python-scripts/figure5.png)

Abbildung 5. ZIP-Datei mit benutzerdefiniertem Python-Code.

Dann laden Sie diese als ein Dataset in Azure Machine Learning Studio hoch. Wenn wir anschließend einen einfachen Versuch erstellen und ausführen, der das Modul verwendet:

![Bild8](./media/machine-learning-execute-python-scripts/figure6a.png)

![Bild9](./media/machine-learning-execute-python-scripts/figure6b.png)

Abbildung 6. Beispielversuch mit benutzerdefiniertem Python-Code, der als ZIP-Datei hochgeladen wird.

Die Modulausgabe zeigt, dass die ZIP-Datei entpackt wurde und dass die Funktion `print_hello` tatsächlich ausgeführt wurde. ![Bild10](./media/machine-learning-execute-python-scripts/figure7.png)
 
Abbildung 7. Eine benutzerdefinierte Funktion, die innerhalb des [Execute Python Script][execute-python-script]-Moduls verwendet wird.

## Arbeiten mit Visualisierungen
Mit MatplotLib erstellte Plots, die im Browser visualisiert werden können, können von [Execute Python Script][execute-python-script] zurückgegeben werden. Die Plots werden jedoch nicht, wie bei R, automatisch in Bilder umgeleitet. Daher muss der Benutzer Plots explizit als PNG-Dateien speichern, wenn sie an Azure Machine Learning zurückgegeben werden sollen.

Um Bilder aus MatplotLib zu generieren, müssen Sie das folgende Verfahren abschließen:

* Ändern Sie das Back-End vom Qt-basierten Standardrenderer in "AGG". 
* Erstellen Sie ein neues Abbildungsobjekt. 
* Rufen Sie die Achse ab, und generieren Sie alle zugehörigen Plots. 
* Speichern Sie die Abbildung als PNG-Datei. 

Dieser Prozess wird unten in Abbildung 8 dargestellt, die mithilfe der scatter\_matrix-Funktion in Pandas eine Punktdiagramm-Matrix erstellt.
 
![Bild1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

Abbildung 8. Speichern von MatplotLib-Abbildungen in Bildern.



Abbildung 9 zeigt einen Versuch, bei dem das oben gezeigte Skript verwendet wird, um Plots über den zweiten Ausgabeport zurückzugeben.

![Bild2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png)
	 
![Bild2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png)

Abbildung 9. Visualisieren von Plots, die aus Python-Code generiert wurden.

Beachten Sie, dass es möglich ist, mehrere Abbildungen zurückzugeben, indem Sie sie in verschiedenen Bildern speichern. Die Azure Machine Learning-Laufzeit übernimmt alle Bilder und verkettet sie zur Visualisierung.


## Fortgeschrittene Beispiele
Die in Azure Machine Learning installierte Anaconda-Umgebung enthält gängige Pakete wie NumPy, SciPy und Scikits-Learn. Diese können effektiv für verschiedene Aufgaben der Datenverarbeitung in einer typischen Pipeline für maschinelles Lernen eingesetzt werden. Als Beispiel veranschaulichen der folgende Versuch und das Skript die Verwendung von Ensemble-Learnern in Scikits-Learn, um Featurewichtigkeitsbewertungen für ein Dataset zu berechnen. Anhand der Ergebnisse kann dann eine überwachte Featureauswahl durchgeführt werden, bevor diese in ein anderes Machine Learning-Modell aufgenommen werden.

Die Python-Funktion zum Berechnen der Wichtigkeitsbewertungen und zum Sortieren der Features anhand der Ergebnisse ist unten dargestellt:

![Bild11](./media/machine-learning-execute-python-scripts/figure8.png)

Abbildung 10. Funktion zum Klassifizieren von Features nach Bewertungen. Der folgende Versuch berechnet anschließend die Wichtigkeitsbewertungen der Features und gibt sie im Dataset "Pima Indian Diabetes" in Azure Machine Learning aus:

![Bild12](./media/machine-learning-execute-python-scripts/figure9a.png) ![Bild13](./media/machine-learning-execute-python-scripts/figure9b.png)
	
Abbildung 11. Versuch zum Klassifizieren von Features im Dataset "Pima Indian Diabetes".

## Einschränkungen 
Das Modul [Execute Python Script][execute-python-script] weist derzeit die folgenden Einschränkungen auf:

1.	*Sandbox-Ausführung.* Die Python-Laufzeit befindet sich derzeit im Sandbox-Modus und ermöglicht daher keinen dauerhaften Zugriff auf das Netzwerk oder auf das lokale Dateisystem. Alle lokal gespeicherten Dateien sind isoliert und werden nach Abschluss des Moduls gelöscht. Auf dem Computer, auf dem er ausgeführt wird, kann der Python-Code nur auf das aktuelle Verzeichnis und die zugehörigen Unterverzeichnisse zugreifen.
2.	*Keine differenzierte Unterstützung für Entwicklung und Debuggen.* IDE-Features wie Intellisense und Debuggen werden vom Python-Modul derzeit nicht unterstützt. Wenn das Modul zur Laufzeit einen Fehler verursacht, steht die vollständige Python-Stapelüberwachung zur Verfügung, muss jedoch im Ausgabeprotokoll für das Modul angezeigt werden. Derzeit wird empfohlen, dass Benutzer ihre Python-Skripts in einer Umgebung wie IPython entwickeln und debuggen und dann den Code in das Modul importieren.
3.	*Ausgabe in einem einzelnen Datenrahmen.* Der Python-Einstiegspunkt kann nur einen einzelnen Datenrahmen als Ausgabe zurückgeben. Derzeit ist es nicht möglich, beliebige Python-Objekte wie z. B. trainierte Modelle direkt an die Azure Machine Learning-Laufzeit zurückzugeben. Wie beim [Execute R Script][execute-r-script]-Modul, das dieselbe Einschränkung aufweist, ist es jedoch in vielen Fällen möglich, Objekte in ein Bytearray einzubetten und dieses innerhalb eines Datenrahmens zurückzugeben.
4.	*Keine Möglichkeit zum Anpassen der Python-Installation*. Derzeit besteht die einzige Möglichkeit zum Hinzufügen benutzerdefinierter Python-Module über den weiter oben beschriebenen ZIP-Dateimechanismus. Während dies bei kleinen Modulen machbar ist, ist dieser Ansatz für große Module (vor allem solche mit systemeigenen DLLs) oder eine große Anzahl von Modulen umständlich. 


##Zusammenfassung
Das [Execute Python Script][execute-python-script]-Modul ermöglicht einem Datenanalysten das Einbetten von vorhandenem Python-Code in cloudgehostete Workflows für maschinelles Lernen in Azure Machine Learning sowie deren nahtlose Operationalisierung als Teil eines Webdiensts. Das Python-Skriptmodul interagiert natürlich mit anderen Modulen in Azure Machine Learning und kann für verschiedenste Vorgänge eingesetzt werden, von der Datenerkundung über die Vorbereitung und Featureextraktion bis hin zur Bewertung und Nachverarbeitung der Ergebnisse. Die zur Ausführung verwendete Back-End-Laufzeit basiert auf Anaconda, einer vielgetesteten und häufig verwendeten Python-Distribution. So können Benutzer vorhandene Codeobjekte ganz einfach in die Cloud integrieren.

In den nächsten Monaten werden wir zusätzliche Funktionen für das [Execute Python Script][execute-python-script]-Modul bereitstellen, z. B. die Möglichkeit, die Modelle in Python zu trainieren und zu operationalisieren. Außerdem fügen wir eine bessere Unterstützung für das Entwickeln und Debuggen von Code in Azure Machine Learning Studio hinzu.

## Nächste Schritte

Weitere Informationen finden Sie im [Python Developer Center](/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[Python-Tools für Visual Studio]: http://aka.ms/ptvs

<!---HONumber=Oct15_HO4-->