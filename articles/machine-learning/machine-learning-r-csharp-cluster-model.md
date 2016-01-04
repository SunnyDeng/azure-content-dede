<properties 
	pageTitle="Clustermodell | Microsoft Azure" 
	description="Clustermodell" 
	services="machine-learning" 
	documentationCenter="" 
	authors="FrancescaLazzeri" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2015" 
	ms.author="lazzeri"/>


#Clustermodell    

Wie lässt sich das Verhalten von Gruppen von Kreditkartenbesitzern vorhersagen, um die Belastungsrisiken der Kreditunternehmen zu verringern? Wie lassen sich Gruppen von Persönlichkeitsmerkmalen von Mitarbeitern definieren, um die Leistung bei der Arbeit zu verbessern? Wie können Ärzte Patienten anhand der Merkmale ihrer Krankheiten in Gruppen unterteilen? Grundsätzlich können all diese Fragen durch Clusteranalyse beantwortet werden.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
   
Die Clusteranalyse klassifiziert einen Satz von Beobachtungen basierend auf Kombinationen von Variablen in mindestens zwei sich gegenseitig ausschließende unbekannte Gruppen. Die Clusteranalyse dient zum Ermitteln eines Systems zum Organisieren von Beobachtungen, in der Regel Personen oder ihren Merkmalen, in Gruppen, wobei Mitglieder der Gruppen Eigenschaften gemeinsam haben. Dieser [Dienst](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) verwendet die K-Means-Methode, ein häufig verwendetes Clusteringverfahren für die Zusammenfassung beliebiger Daten in Gruppen. Dieser Webdienst akzeptiert die Daten und die Anzahl von k Clustern als Eingabe und macht Vorhersagen dazu, welche der k Gruppen zu welcher Beobachtung gehört.

>Dieser Webdienst kann von Benutzern verwendet werden – beispielsweise über eine mobile App, eine Website oder sogar über einen lokalen Computer. Dieser Webdienst ist jedoch auch ein gutes Beispiel dafür, wie Azure Machine Learning zum Erstellen von Webdiensten basierend auf R-Code verwendet werden kann. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure Machine Learning Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden – ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.

##Nutzung des Webdiensts   
Dieser Webdienst gruppiert die Daten in einen Satz von k Gruppen und gibt die Gruppenzuordnung für jede Zeile aus. Der Webdienst erwartet, dass der Endbenutzer seine Daten als Zeichenfolge eingibt, wobei Zeilen durch Komma (,) und Spalten durch Semikolon (;) getrennt werden. Der Webdienst erwartet eine Zeile nach der anderen. Ein Beispieldataset könnte folgendermaßen aussehen:

![Beispieldaten][1]

Angenommen, der Benutzer möchte diese Daten in drei sich gegenseitig ausschließende Gruppen aufteilen. Die Dateneingabe für das obige Dataset würde folgendermaßen lauten: value = "10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4"; k="3". Die Ausgabe ist die vorhergesagte Gruppenzugehörigkeit für jede Zeile.

>Dieser Dienst, der im Azure Marketplace gehostet wird, ist ein OData-Dienst. Diese Dienste können durch POST- oder GET-Methoden aufgerufen werden.

Es gibt mehrere Möglichkeiten, den Dienst auf automatisierte Weise zu nutzen ([hier](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx) finden Sie eine Beispiel-App).

###Starten von C#-Code für Webdienstnutzung:

	public class Input
	{
	        public string value;
	        public string k;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
	    	var scoreResult = result.ReadAsStringAsync().Result;
	}




##Erstellen des Webdiensts  
>Dieser Webdienst wurde mithilfe von Azure Machine Learning erstellt. Eine kostenlose Testversion sowie Einführungsvideos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](machine-learning-publish-a-machine-learning-web-service.md) finden Sie unter [azure.com/ml](http://azure.com/ml). Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.

In Azure Machine Learning wurde ein neues leeres Experiment erstellt, und es wurden zwei [Execute R Script][execute-r-script]-Module in den Arbeitsbereich gezogen. Das Datenschema wurde mit einem einfachen [Execute R Script][execute-r-script]-Modul erstellt. Anschließend wurde das Datenschema mit dem Clustermodellabschnitt verknüpft, der ebenfalls mit einem [Execute R Script][execute-r-script]-Modul erstellt wurde. In dem für das Clustermodell verwendeten [Execute R Script][execute-r-script]-Modul nutzt der Webdienst dann die k-means-Funktion, die bereits in das [Execute R Script][execute-r-script]-Modul von Azure Machine Learning integriert ist.
   

     
![Experimentablauf][3]

####Modul 1: 
	#Enter the input data as a string 
	mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
	
	maml.mapOutputPort("mydata");     
	

####Modul 2:
	# Map 1-based optional input ports to variables
	mydata <- maml.mapInputPort(1) # class: data.frame

	data.split <- strsplit(mydata[1,1], ",")[[1]]
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- as.data.frame(t(data.split))

	data.split <- data.matrix(data.split)
	data.split <- data.frame(data.split)

	# K-Means cluster analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# Get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# Append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
##Einschränkungen
Dies ist ein sehr einfaches Beispiel eines Webdiensts für Clustering. Wie aus den oben stehenden Beispielcode ersichtlich ist, wird kein Abfangen von Fehlern implementiert, und der Dienst geht davon aus, dass der Code vollständig aus kontinuierlichen Variablen besteht (keine kategorischen Features zulässig), da der Dienst zum Zeitpunkt der Erstellung dieses Webdiensts nur numerische Werte eingibt. Aufgrund der Beschaffenheit des Webdienstaufrufs (Anforderung/Antwort) und der Tatsache, dass das Modell bei jedem Aufruf des Webdiensts angepasst wird, verarbeitet der Dienst derzeit nur einen eingeschränkten Datenumfang.

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts und zum Veröffentlichen im Azure Marketplace finden Sie [hier](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_1125_2015-->