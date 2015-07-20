<properties 
	pageTitle="Multivariate lineare Regression | Microsoft Azure" 
	description="Multivariate lineare Regression" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="jaymathe"/>


#Multivariate lineare Regression   
 

 
Angenommen, Sie verfügen über ein Dataset und möchten schnell eine abhängige Variable (y) für einzelne Personen (i) basierend auf unabhängigen Variablen vorhersagen. Die lineare Regression ist ein beliebtes statistisches Verfahren für eine solche Vorhersage. Hier wird angenommen, dass die abhängige Variable y ein kontinuierlicher Wert ist.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Ein einfaches Szenario könnte sein, dass ein Forscher versucht, das Gewicht einer Einzelperson (y) basierend auf deren Größe (x) vorherzusagen. Ein erweitertes Szenario wäre, dass ein Forscher über zusätzliche Informationen zu den Einzelpersonen verfügt (z. B. Gewicht, Geschlecht, Rasse) und versucht, das Gewicht der einzelnen Personen vorherzusagen. Dieser [Webdienst](https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) passt das lineare Regressionsmodell den Daten an und gibt den Prognosewert (y) für jede der Beobachtungen in den Daten aus.

>Dieser Webdienst kann von Benutzern verwendet werden – beispielsweise über eine mobile App, eine Website oder sogar über einen lokalen Computer. Dieser Webdienst ist jedoch auch ein gutes Beispiel dafür, wie Azure Machine Learning zum Erstellen von Webdiensten basierend auf R-Code verwendet werden kann. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure Machine Learning Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden – ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.

##Nutzung des Webdiensts  
Dieser Webdienst liefert die vorhergesagten Werte der abhängigen Variablen basierend auf den unabhängigen Variablen für alle Beobachtungen. Der Webdienst erwartet, dass der Endbenutzer seine Daten als Zeichenfolge eingibt, wobei Zeilen durch Komma (,) und Spalten durch Semikolon (;) getrennt werden. Der Webdienst erwartet eine Zeile nach der anderen, und er erwartet, dass die erste Spalte die abhängige Variable ist. Ein Beispieldataset könnte folgendermaßen aussehen:

![Beispieldaten][1]

Bei Beobachtungen ohne eine abhängige Variable sollte für y "NA" eingegeben werden. Die Dateneingabe für das obige Dataset würde folgendermaßen lauten: "10;5;2,18;1;6,6;5.3;2.1,7;5;5,22;3;4,12;2;1,NA;3;4". Die Ausgabe ist der vorhergesagte Wert für jede Zeile basierend auf der unabhängigen Variablen.

>Dieser Dienst, der im Azure Marketplace gehostet wird, ist ein OData-Dienst. Diese Dienste können durch POST- oder GET-Methoden aufgerufen werden.

Es gibt mehrere Möglichkeiten, den Dienst auf automatisierte Weise zu nutzen ([hier](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx) finden Sie eine Beispiel-App).

###Starten von C#-Code für Webdienstnutzung:

	public class Input
	{
	        public string value;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { value = TextBox1.Text };
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


In Azure Machine Learning wurde ein neues leeres Experiment erstellt, und es wurden zwei [Execute R Script][execute-r-script]-Module in den Arbeitsbereich gezogen. Dieser Webdienst führt ein Azure Machine Learning-Experiment mit einem zugrunde liegenden R-Skript aus. Das Experiment besteht aus zwei Teilen: Schemadefinition und Trainieren/Bewerten des Modells. Das erste Modul definiert die erwartete Struktur des Eingabedatasets, wobei die erste Variable die abhängige Variable ist und die restlichen Variablen unabhängig sind. Das zweite Modul passt ein generisches lineares Regressionsmodell den Eingabedaten an.
  
![Experimentablauf][3]

####Modul 1:
 
####Schemadefinition  
	data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####Modul 2:
####LM-Modellierung   
	data <- maml.mapInputPort(1) # class: data.frame  
  
	data.split <- strsplit(data[1,1], ",")[[1]]  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- as.data.frame(t(data.split)) 
	data.split <- data.matrix(data.split) 
	data.split <- data.frame(data.split) 
	model <- lm(data.split)  

	out=data.frame(predict(model,data.split))  
	out <- data.frame(t(out))

	maml.mapOutputPort("out");  
 
##Einschränkungen
Dies ist ein sehr einfaches Beispiel eines Webdiensts mit multipler linearer Regression. Wie aus den oben stehenden Beispielcode ersichtlich ist, wird kein Abfangen von Fehlern implementiert, und der Dienst geht davon aus, dass der Code vollständig aus kontinuierlichen Variablen besteht (keine kategorischen Features zulässig), da der Dienst zum Zeitpunkt der Erstellung dieses Webdiensts nur numerische Werte eingibt. Aufgrund der Beschaffenheit des Webdienstaufrufs (Anforderung/Antwort) und der Tatsache, dass das Modell bei jedem Aufruf des Webdiensts angepasst wird, verarbeitet der Dienst derzeit nur einen eingeschränkten Datenumfang.

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts und zum Veröffentlichen im Azure Marketplace finden Sie [hier](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=July15_HO2-->