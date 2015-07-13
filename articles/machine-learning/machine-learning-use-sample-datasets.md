<properties 
	pageTitle="Verwenden von Beispiel-DataSets in Machine Learning Studio | Microsoft Azure" 
	description="Beschreibungen von in Beispielmodellen verwendete DataSets in ML Studio. Sie können diese Beispiel-DataSets für Experimente verwenden." 
	keywords="data sets,datasets,ml studio,sample data sets"
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="garye"/>


# Verwenden von Beispiel-DataSets in Azure Machine Learning Studio

[top]: #machine-learning-sample-datasets

Beim Erstellen eines neuen Arbeitsbereichs in Azure Machine Learning ist eine Reihe von Beispielexperimenten und -DataSets standardmäßig enthalten. Viele dieser Beispiel-DataSets sind für die Beispielmodelle im [Modellkatalog für Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/models/) gedacht, während andere als Beispiele für verschiedene Arten von Daten für Machine Learning enthalten sind.

Einige dieser DataSets sind im Azure-Blob-Speicher verfügbar. Für diese DataSets finden Sie in den folgenden Tabellen einen direkten Link. Sie können diese DataSets mithilfe des [Reader][reader]-Moduls für Ihre Experimente verwenden. Ein Beispiel für den Zugriff auf ein DataSet erhalten Sie, wenn Sie die Eigenschaften des [Reader][reader]-Moduls in dem dem DataSet zugeordneten Beispielexperiment anzeigen.

Der Rest dieser Beispiel-DataSets wird in der Modulpalette auf der linken Seite des Experimentcanvas unter **Saved Datasets** angezeigt, wenn Sie in ML Studio ein neues Experiment öffnen oder erstellen. Sie können alle diese DataSets für Ihre eigenen Experimente verwenden, indem Sie sie auf Ihren Experimentcanvas ziehen.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Modellen im Machine Learning-Katalog zugeordnete Beispiel-DataSets

Die folgenden DataSets werden von Beispielmodellen im [Azure Machine Learning-Modellkatalog](http://azure.microsoft.com/documentation/services/machine-learning/models/) verwendet. Weitere Informationen zum Modell und zum zugeordneten Experiment finden Sie über den Link in der Tabelle.

<table>

<tr>
  <th align=left>Beispielmodell</th>
  <th align=left>Zugeordnetes Experiment</th>
  <th align=left>Datasetname</th>
  <th align=left>Datasetbeschreibung</th>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-color-quantization-using-k-means-clustering/">Farbquantifizierung mit K-Means-Clustering</a></td>
  <td valign=top>Beispielexperiment - Auf Farbe basierte Bildkomprimierung mithilfe von K-Means-Clustering - Entwicklung</td>
  <td valign=top>RGB-Bild von Bill Gates</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-credit-risk-prediction/">Kreditrisiko-Vorhersage</a></td>
  <td valign=top>Beispielexperiment - Deutsche Kredit – Entwicklung</td>
  <td valign=top>Deutsche Kreditkarte UCI-Dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td rowspan=4 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-crm-task/">CRM-Aufgabe</a></td>
  <td rowspan=4 valign=top>Beispielexperimente - CRM - Entwicklung</td>
  <td valign=top>CRM-Dataset gemeinsam genutzt</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]
  </td>
</tr>
<tr ID=crm-appetency-labels-shared>
  <td valign=top>CRM Appetency Bezeichnungen freigegeben</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-churn-labels-shared>
  <td valign=top>CRM Codeänderung Bezeichnungen freigegeben</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-upselling-labels-shared>
  <td valign=top>CRM Upselling Bezeichnungen freigegeben</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-finding-similar-companies/">Suche nach ähnlichen Firmen</a></td>
  <td valign=top>Beispielexperiment - Clustering für S &amp; P 500 Unternehmen - Entwicklung</td>
  <td valign=top>Wikipedia SP 500 Dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td rowspan=3 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-flight-delay-prediction/">Vorhersagen von Flugverspätungen</a></td>
  <td rowspan=3 valign=top>Beispielexperiment - Vorhersagen von Flugverspätungen - Entwicklung</td>
  <td valign=top>Flughafencodes-Dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../../includes/machine-learning-sample-dataset-airport-codes.md)]
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Flugverspätungsdaten</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../../includes/machine-learning-sample-dataset-flight-delays-data.md)]
</td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Wetter-Dataset</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../../includes/machine-learning-sample-dataset-weather-dataset.md)]
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top> <a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-network-intrusion-detection/">Erkennen von Netzwerkangriffen</a></td>
  <td valign=top>Beispielexperiment - Network Intrusion Detection - Entwicklung</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../../includes/machine-learning-sample-dataset-network-intrusion.md)]
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-number-of-bike-rentals/">Vorhersage der Anzahl ausgeliehener Fahrräder</a></td>
  <td valign=top>Beispielexperiment - verlangen von Fahrrädern Prognose</td>
  <td valign=top>Fahrradvermietung UCI-Datensatz</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-student-performance/">Vorhersage der Leistung der Schüler</a></td>
  <td valign=top>Beispielexperiment - Leistung des Schülers - Entwicklung</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../../includes/machine-learning-sample-dataset-student-performance.md)]
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-sentiment-analysis/">Stimmungsanalyse</a></td>
  <td valign=top>Beispielexperiment - Stimmungs-Klassifizierung - Entwicklung</td>
  <td valign=top>Buchbesprechungen von Amazon</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
  </td>
</tr>

</table>



## In Machine Learning Studio-Beispielexperimenten verwendete DataSets

Die folgenden DataSets werden in Beispielexperimenten in [Machine Learning Studio](https://studio.azureml.net/Home) verwendet.

Diese DataSets werden unter **Saved Datasets** in der Modulpalette auf der linken Seite des Experimentcanvas angezeigt, wenn Sie in ML Studio ein neues Experiment öffnen oder erstellen. Sie können alle diese DataSets für Ihre eigenen Experimente verwenden, indem Sie sie auf Ihren Experimentcanvas ziehen.

Alle hier aufgeführten Beispielexperimente finden Sie auf der Registerkarte **Beispiele** auf der Seite **Experimente** in ML Studio. Um eine bearbeitbare Kopie eines Beispielexperiments zu erstellen, öffnen Sie das Experiment in ML Studio, und klicken Sie auf **Speichern unter**.


<table>

<tr>
  <th align=left>Datasetname</th>
  <th align=left>Beispielexperiment</th>
  <th align=left>Datasetbeschreibung</th>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Brustkrebsfunktionen
  <td valign=top>Beispielexperiment – Brustkrebs – Entwicklung</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-features](../../includes/machine-learning-sample-dataset-breast-cancer-features.md)]
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Brustkrebsdaten</td>
  <td valign=top><i>identisch</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-info](../../includes/machine-learning-sample-dataset-breast-cancer-info.md)]
  </td>
</tr>

<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>Beispielexperiment – Direktmarketing – Entwicklung – Schulung</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-direct-marketing](../../includes/machine-learning-sample-dataset-direct-marketing.md)]
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>IMDB-Filmtitel</td>
  <td valign=top>Beispielexperiment – Filmempfehlungen – Entwicklung</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-imdb-movie-titles](../../includes/machine-learning-sample-dataset-imdb-movie-titles.md)]
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Film-Tweets</td>
  <td valign=top>Beispielexperiment – Rangfolge der Film-Tweets anhand der zukünftigen Beliebtheit – Entwicklung</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-movie-tweets](../../includes/machine-learning-sample-dataset-movie-tweets.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>Beispielexperiment – Nachrichtenkategorisierung – Entwicklung</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-test](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-test.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top><i>identisch</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-train](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-train.md)]
  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top><i>identisch</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-rcv1-v2-topics](../../includes/machine-learning-sample-dataset-rcv1-v2-topics.md)]
  </td>
</tr>

</table>



## Verschiedene Beispiel-DataSets in Machine Learning Studio

Die folgenden DataSets sind in [Machine Learning Studio](https://studio.azureml.net/Home) als Beispiele für verschiedene Arten von Daten für maschinelles Lernen enthalten. Sie werden unter **Saved Datasets** in der Modulpalette auf der linken Seite des Experimentcanvas angezeigt, wenn Sie in ML Studio ein neues Experiment öffnen oder erstellen. Sie können alle diese DataSets für Ihre eigenen Experimente verwenden, indem Sie sie auf Ihren Experimentcanvas ziehen.

<table>
<tr>
  <th align=left>Datasetname</th>
  <th align=left>Beschreibung</th>
</tr>

<tr>
  <td valign=top>Binäres Klassifizierungsdataset der Einkommenserhebung für Erwachsene</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-adult-census-income](../../includes/machine-learning-sample-dataset-adult-census-income.md)]
  </td>
</tr>

<tr>
  <td valign=top>Fahrzeugpreisdaten (unformatiert)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-price](../../includes/machine-learning-sample-dataset-automobile-price.md)]
  </td>
</tr>

<tr>
  <td valign=top>Blutspendedaten</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-blood-donation](../../includes/machine-learning-sample-dataset-blood-donation.md)]
  </td>
</tr>

<tr>
  <td valign=top>Brustkrebsdaten</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-data](../../includes/machine-learning-sample-dataset-breast-cancer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Daten zur Energieeffizienzregression</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-energy-efficiency-regression](../../includes/machine-learning-sample-dataset-energy-efficiency-regression.md)]
  </td>
</tr>

<tr>
  <td valign=top>Planmäßige Durchführung von Flügen (unformatiert)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-on-time-performance](../../includes/machine-learning-sample-dataset-flight-on-time-performance.md)]
  </td>
</tr>

<tr>
  <td valign=top>Waldbranddaten</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-forest-fires](../../includes/machine-learning-sample-dataset-forest-fires.md)]
  </td>
</tr>

<tr>
  <td valign=top>Zwei-Klassen-Daten zu Schwertlilien</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-iris-two-class](../../includes/machine-learning-sample-dataset-iris-two-class.md)]
  </td>
</tr>

<tr>
  <td valign=top>Verbrauchsdaten für verschiedene Fahrzeuge</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-mpg](../../includes/machine-learning-sample-dataset-automobile-mpg.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dataset für binäre Klassifizierung der Diabetes bei Pima-Indianern</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-pima-indians-diabetes](../../includes/machine-learning-sample-dataset-pima-indians-diabetes.md)]
  </td>
</tr>

<tr>
  <td valign=top>Daten zu Restaurantkunden</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-customer-data](../../includes/machine-learning-sample-dataset-restaurant-customer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Daten zu Restaurantmerkmalen</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-feature-data](../../includes/machine-learning-sample-dataset-restaurant-feature-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Restaurantbewertungen</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-ratings](../../includes/machine-learning-sample-dataset-restaurant-ratings.md)]
  </td>
</tr>

<tr>
  <td valign=top>Multiklassen-Dataset zur Stahlhärtung</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-steel-annealing](../../includes/machine-learning-sample-dataset-steel-annealing.md)]
  </td>
</tr>

<tr>
  <td valign=top>Teleskopdaten</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-telescope-data](../../includes/machine-learning-sample-dataset-telescope-data.md)]
  </td>
</tr>


</table>


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=July15_HO1-->