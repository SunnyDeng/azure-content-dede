<properties title="Azure Machine Learning Sample: Prediction of student performance" pageTitle="Machine Learning-Beispiel: Vorhersagen der Leistung des Schülers | Azure" description="Azure Machine Learning-Beispielexperiment zur Entwicklung eines Modells, das die Schülerleistungen bei Tests prognostiziert" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning-Beispiel: Vorhersage der Leistung der Schüler

>[AZURE.HINWEIS]
>Das [Beispielexperiment] und das [Beispieldataset] im Zusammenhang mit diesem Modell sind in ML Studio verfügbar. Weitere Details erhalten Sie unten.
[Beispielexperiment]: #sample-experiment
[Beispieldataset]: #sample-dataset


##Dataset und Problembeschreibung
In diesem Experiment ist unser Dataset das Algebra_2008_2009 von KDD Cup 2010. Dieses Dataset kann von [https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp](https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp) heruntergeladen werden. Das Dataset enthält Protokolldateien des Schülernachhilfesystems. Die bereitgestellten Funktionen umfassen eine Problem-ID und eine zugehörige Kurzbeschreibung, die Schüler-ID, einen Zeitstempel und die Anzahl der Versuche, bevor das Problem vom Schüler ordnungsgemäß gelöst wurde. Das Lernproblem besteht in der Vorhersage, ob ein Schüler ein bestimmtes Problem beim ersten Versuch lösen kann. Gemäß den KDD Cup-Regeln messen wir die Genauigkeit der Lernalgorithmen mithilfe der [Wurzel der mittleren quadratischen Abweichung](http://en.wikipedia.org/wiki/Root-mean-square_deviation) (RMSE). Das ursprüngliche Dataset verfügt über 8,9 Millionen Datensätze. Wir haben das Dataset auf die ersten 100.000 Zeilen begrenzt, um das Experiment zu beschleunigen. Das Dataset enthält 23 Spalten mit verschiedenen Typen: Numerisch, kategorisch und Zeitstempel. Die Spalten sind durch Tabulatoren getrennt.
 
##Entwicklungs-Workflow
Das Dataset enthält Nicht-ASCII-Zeichen, die nicht vom Modul **R Operation anwenden** behandelt werden können. Bevor das Dataset in Passau verwendet wurde, haben wird die Nicht-ASCII-Zeichen mithilfe der folgenden PowerShell-Befehle entfernt:  

	gc algebra_2008_2009_train.txt -head 100000 | sc algebra_train_small.txt
	sc tmp.txt -Encoding ASCII -Value (gc algebra_train_small.txt)
	cat tmp.txt | %{$_ -replace "\?\?sqrt","+sqrt"} | sc algebra_train_small.txt_ascii  

Die Ergebnisdatei "algebra_train_small.txt_ascii" ist immer noch recht groß und benötigt 36 M. Wir speichern diese Datei im Azure-BLOB-Speicher und laden die Datei mithilfe des **Lesemoduls** in das Experiment. PowerShell-Befehle zum Speichern der Datei im BLOB-Speicher sind:  

	Add-AzureAccount
	$key = Get-AzureStorageKey -StorageAccountName <your storage account name>
	$ctxt = New-AzureStorageContext -StorageAccountName $key.StorageAccountName -StorageAccountKey $key.Primary
	Set-AzureStorageBlobContent -Container <container name in your storage account> -File "algebra_train_small.txt_ascii" -Context $ctxt

![][1]
 
Die Parameter des **Lese** moduls werden oben angezeigt. In diesem Beispiel lautet der Speicherkontoname "datascience" und die Datasetdatei "algebra_train_small.txt_ascii" wird im Container "sampleexperiments" abgelegt. Der Kontoschlüssel ist ein Zugriffsschlüssel des Azure-Speicherkontos. Dieser Schlüssel kann über Ihr Konto im Azure-Verwaltungsportal ([https://manage.windowsazure.com](https://manage.windowsazure.com)) abgerufen werden.
 
![][2]
 
In den nächsten Schritten, die oben aufgeführt sind, werden eine Reihe von Transformationen ausgeführt, um das Dataset in das richtige Format zu bringen, das zu unserem Lernalgorithmus passt. Wir verwenden den **Metadaten-Editor**, um die Zeitstempelspalte "First Transaction Time" in das Zeichenfolgenformat zu konvertieren. Dieses ist zum Generieren der Trainings-/Testaufspaltung erforderlich. Mithilfe der **Projektspalten** entfernen wir eine Reihe von Spalten, die im Experiment nicht verwendet werden. Wir verwenden die Bereinigung fehlender Werte, um alle fehlenden Werte durch "0" zu ersetzen. Im nächsten Schritt unterteilen wir die Spalte "Unit Name, Section Name" in die beiden Spalten "Unit Name" und "Section Name". Dies wird über den folgenden R-Code im Modul **R ausführen** erreicht:  

	dataset <- maml.mapInputPort(1)
	b <- data.frame(do.call(rbind,strsplit(as.vector(dataset[,3]),",")))
	names(b) <- c("Unit Name","Section Name")
	data.set <- cbind(dataset[,1:2],b,dataset[,4:15])
	maml.mapOutputPort("data.set")  

 
Die ersten Schritte des Experiments, die das Laden der Daten und die anfänglichen Transformationen einbeziehen, sind oben aufgeführt. Nach der Überarbeitung der Daten unterteilen wir das Experiment in vier Teilgebiete. In jedem Teilgebiet wird ein anderer Funktionssatz getestet. Einige unserer Funktionssätze wurden zuvor von [1] verwendet. Im ersten Teilgebiet, das nachfolgend veranschaulicht wird, besteht unser Funktionssatz aus den Funktionen "StudentID", "Unit"  

![][3]  
![][4]
 
 
"Name", "Section Name", "Problem Name", "Problem View" und "Opportunity". Nicht ganzzahlige Funktionen werden als kategorische Funktionen dargestellt. In diesem Teilgebiet beginnen wir mit dem Entfernen der Spalte "Hints" mithilfe von **Projektspalten**, da die Spalte "Hints" nicht verfügbar ist, wenn der Erfolg neuer Schüler vorhergesagt wird. Dann verwenden wir das Modul **Teilen**, um das Dataset in einen Trainings- und einen Testsatz zu unterteilen. Da alle Beispiele über Zeitstempel verfügen, ist unsere Unterteilung zeitbasiert.  Alle Zeilen mit einer ersten Transaktionszeit von 2008 befinden sich im Trainingssatz, während sich die Zeilen aus 2009 im Testsatz befinden. Die Parameter des Moduls **Teilen** werden auf der rechten Seite angezeigt. Nach der Unterteilung generieren wir ein binäres Klassifizierungsmodell mithilfe des Boosted Decision Tree und erzielen die Testdaten. Die Bezeichnungsspalte im Modul **Schulungsmodell** ist "Erster korrekter Versuch".  

![][5]  

 
In der nächsten Folge der Module **Mathematische Operation anwenden** und **Elementare Statistiken**, die oben angezeigt werden, berechnen wir die Wurzel der mittleren quadratischen Abweichung (RMSE) des Modells mithilfe von Originalergebnissen und zutreffenden Bezeichnungen. Beachten Sie, dass diese Metrik für Regressionsmodelle vom Modul **Bewertung** berechnet wird. Für andere Modelle müssen wir diese Metrik manuell berechnen. Im ersten Modul **Mathematische Operation anwenden** berechnen wir die Differenz zwischen Bezeichnungsspalte und Ergebnisspalte, die mithilfe des Moduls **Modell bewerten** generiert wurde. Im zweiten Modul **Mathematische Operation anwenden** berechnen wir das Quadrat dieser Differenz. Dann berechnet das Modul **Elementare Statistiken** den Mittelwert der quadrierten Differenz. Abschließend wird das letzte Modul **Mathematische Operation anwenden** zum Berechnen der Quadratwurzel verwendet. Die Parameter dieser vier Module werden nachfolgend veranschaulicht.  

![][6] 
![][7]  
![][8]  
![][9] 
 
 
Im zweiten Teilbereich, der oben aufgeführt ist, nutzen wir auch die zeitliche Dimension. Zusätzlich zu den Funktionen des ersten Teilbereichs verwenden wir Namen der letzten beiden Schritte des Problems, das aktuell vom Benutzer gelöst wird, zusammen mit ihren Beschreibungen. Im bereitgestellten Dataset werden alle Aktivitäten des Benutzers hinsichtlich des Zeitstempels in aufsteigender Reihenfolge gespeichert. Außerdem überlappen die Aktivitäten des Benutzers im bereitgestellten Dataset nicht. Das bedeutet, anfänglich sind dort alle Zeilen des ersten Benutzers, dann alle Zeilen des zweiten Benutzers usw. Somit nutzen wir die Spalte "RowID", um die letzten Schritte des Benutzers zu finden. Für einen festen Benutzer dient diese Spalte als Zeitachse. Indem wir mithilfe von "Mathmatische Operation anwenden****" den Wert 1 zu dieser Spalte hinzufügen, verschieben wir jede Zeile um eine Zeiteinheit nach vorne. Dann verwenden wir das Modul **Verbinden**, um das ursprüngliche Dataset unter Verwendung von "RowID", "StudentID" und "ProblemName" als Schlüssel mit dem verschobenen Dataset zu verbinden. Als Ergebnis erhalten wir ein erweitertes Dataset, in dem jede Zeile einen Datensatz vom Zeitpunkt "t" und "t -1" für dieselbe "StudentID" und "ProblemName" besitzt. Wir verwenden *Left Outer Join*, um die Zeilen zu erhalten, die die vorherigen Schritte mit denselben "StudentID" und "ProblemID" nicht aufweisen. Wir wenden erneut eine Kombination von **Mathematische Operation anwenden** und **Verbinden** an, um zwei Schritte zuvor Funktionen zu erhalten. Die genauen Parameter von **Mathematische Operation anwenden** und **Verbinden werden** unten angezeigt.  

![][10]  
![][11]  
![][12]  

 
Nachdem wir zwei ausgeführt haben, erhalten wir eine Reihe von identischen Spalten. Aufgrund unserer Verwendung des Moduls "Verbinden" wird die Spalte "ProblemName" dreimal repliziert, für die Schritte "t", "t-1" und "t-2". Wir verwenden das Modul für **Projektspalten**, um redundante Spalten zu entfernen. Abschließend können einige Zeilen, die vom Join-Operator generiert wurden, fehlende Werte aufweisen, da wir *Left Outer Join* verwendet haben. Wir verwenden die **Bereinigung fehlender Werte**, um alle fehlenden Werte durch die Zeichenfolge "0" zu ersetzen. Die Parameter der **Bereinigung fehlender Werte** werden oben angezeigt.  

Nach der Berechnung des neuen Funktionssatzes ist der Workflow des zweiten Teilbereichs identisch mit dem des ersten Teilbereichs.  

![][13]
 
Im dritten Teilbereich verwenden wir zusätzlich zu den im zweiten Teilbereich verwendeten Funktionen auch die quadratischen und kubischen Funktionen, die Verkettungen der ursprünglichen Funktionen des ersten Teilbereichs sind. Quadratische und kubische Funktionen werden mithilfe des Moduls **R-Operation ausführen** mit dem oben angezeigten R-Code berechnet. Nach der Berechnung des neuen Funktionssatzes fahren wir mit dem Training, der Bewertung und Auswertung auf dieselbe Weise wie in den anderen beiden Teilbereichen fort.  

Im vierten Teilbereich verwenden wir Funktionen, die sich vollkommen von denen in den ersten drei Teilbereichen unterscheiden. Für jede "StudentID" berechnen wird den Mittelwert für den richtigen ersten Versuch bis (aber nicht einschließlich) zum Zeitpunkt "t". Wir bezeichnen diesen Wert als "CFA(StudentID,t)". Ebenso bezeichnen wir mit "Hints(StudentID,t)" den Mittelwert der Hinweise für eine feste "StudentID" bis (aber nicht einschließlich) zum Zeitpunkt "t". Anstatt das gesamte Dataset zu berücksichtigen, betrachten wir nur die 10 letzten Datensätze vor dem Zeitpunkt "t", um die Berechnung zu beschleunigen. Die Definitionen von "CFA(Problem Name,t)", "CFA(Step Name,t)", "CFA(Description,t)", "Hints(Problem Name,t)", "Hints(Step Name,t)" und "Hints(Description,t)" sind ähnlich. Bei einem Beispiel "x" mit "First Transaction Time=t(x)" und den Spaltenwerten "StudentID(x)", "Problem Name(x)", "Step Name(x)" und "Description(x)" generieren wir die obigen acht "CFA"- und "Hints"-Funktionen:  

	CFA(StudentID(x),t(x)), CFA(Problem Name(x),t(x)), CFA(Step Name(x),t(x)), CFA(Description(x),t(x)), 
	Hints(StudentID(x),t(x)), Hints(Problem Name(x),t(x)), Hints(Step Name(x),t(x)), Hints(Description(x),t(x))  

![][14]  

 
Auf die gleiche Weise berechnen wir auch acht zusätzliche "CFA"- und "Hints"-Funktionen mithilfe der Verkettung von "StudentID" und "Problem Name", "Problem Name" und "Step Name", "StudentId" und "UnitName", "StudentID" und "Problem Description". Insgesamt erhalten wir 16 Funktionen, mit denen der Wert für die Spalte des ersten richtigen Versuchs vorhergesagt wird. Die Berechnung dieser 16 Funktionen erfolgt mithilfe des R-Codes im Modul **R-Operation ausführen**. Dieser Code ist lang und mühsam, aber in hohem Maße optimiert. Nach der Berechnung dieser Funktionen entfernen wir alle Hilfsspalten, die mit dem R-Code hinzugefügt wurden. Zu diesem Zweck verwenden wir das Modul für **Projektspalten**. Der vollständige Workflow zum Berechnen von Funktionen im vierten Teilbereich wird oben veranschaulicht. Nach der Berechnung des neuen Funktionssatzes fahren wir mit dem Training, der Bewertung und Auswertung auf dieselbe Weise wie in den anderen drei Teilbereichen fort.  

Nach der Berechnung der RMSE-Werte in allen vier Teilbereichen erfassen wir die Werte mithilfe des Moduls **Zeile hinzufügen**. Zudem generieren wir Anmerkungen mithilfe des Moduls **R ausführen**. Der Workflow für diesen letzten Teil des Experiments wird unten veranschaulicht.  

![][15]  
 
 
Die endgültige Ausgabe des Experiments befindet sich in der folgenden Tabelle. Die erste Spalte enthält den Namen des Funktionssatzes und die zweite Zeile ist die in den Testbeispielen gemessene Wurzel der mittleren quadratischen Abweichung:  

![][16]
 

Wir schließen daraus, dass der vierte Funktionssatz die niedrigste Wurzel der mittleren quadratischen Abweichung (RMSE) ergibt.  

##Referenzen
H.-F. Yu et al. Feature Engineering and Classifier Ensemble for KDD Cup 2010. KDD Cup 2010 Workshop, 2010.



## Beispielexperiment

Das nachfolgende Beispielexperiment im Zusammenhang mit diesem Modellist in ML Studio im Abschnitt **EXPERIMENTE** auf der Registerkarte **BEISPIELE** verfügbar.

> **Beispielexperiment - Leistung des Schülers - Entwicklung**


## Beispieldataset

Das folgende Beispieldataset, das von diesem Experiment verwendet wird, finden Sie in der Modulpalette in ML Studio unter **Gespeicherte Datasets**.

###student_performance.txt
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../includes/machine-learning-sample-dataset-student-performance.md)]



[1]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-1.jpg
[2]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-2.jpg
[3]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-3.jpg
[4]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-4.jpg
[5]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-5.jpg
[6]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-6.jpg
[7]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-7.jpg
[8]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-8.jpg
[9]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-9.jpg
[10]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-10.jpg
[11]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-11.jpg
[12]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-12.jpg
[13]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-13.jpg
[14]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-14.jpg
[15]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-15.jpg
[16]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-16.jpg
