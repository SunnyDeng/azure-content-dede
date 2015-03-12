<properties 
	pageTitle="Multivariate lineare Regression | Azure" 
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
	ms.date="02/11/2015" 
	ms.author="jaymathe"/> 


#Multivariate lineare Regression   
 

 
Angenommen, Sie verfügen über ein Dataset und möchten schnell eine abhängige Variable y für jede Einzelperson (i) basierend auf anderen unabhängigen Variablen vorhersagen. 
Lineare Regression ist ein beliebtes statistisches Verfahren für eine solche Vorhersage. Hier wird angenommen, dass die abhängige Variable y ein kontinuierlicher Wert ist.    

Ein einfaches Szenario könnte sein, dass ein Forscher versucht, das Gewicht einer Einzelperson (y) basierend auf deren Größe (x) vorherzusagen. Ein erweitertes Szenario wäre, dass ein Forscher über zusätzliche Informationen zu den Einzelpersonen verfügt (z. B. Gewicht, Geschlecht, Rasse) und versucht, das Gewicht der einzelnen Personen vorherzusagen. Dieser [Webdienst]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) passt das lineare Regressionsmodell den Daten an und gibt den Prognosewert (y) für jede der Beobachtungen in den Daten aus.

>Dieser Webdienst kann von Benutzern verwendet werden - möglicherweise mit einer mobilen App, Website oder z. B. sogar auf einem lokalen Computer. Der Zweck des Webdiensts ist auch, als Beispiel dafür zu dienen, wie Sie mit Azure ML Webdienste auf R-Code-Basis erstellen können. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure ML Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden, ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.  

#Nutzung des Webdiensts  
Dieser Webdienst liefert die vorhergesagten Werte der abhängigen Variablen basierend auf den unabhängigen Variablen für alle Beobachtungen. Der Webdienst erwartet, dass der Endbenutzer seine Daten als Zeichenfolge eingibt, wobei Zeilen durch Kommas (,) getrennt und Spalten durch Semikolons (;) getrennt werden. Der Webdienst erwartet eine Zeile nach der anderen und dass die erste Spalte die abhängige Variable ist. Ein Beispieldataset könnte folgendermaßen aussehen:

![Sample data][1]

Bei Beobachtungen ohne eine abhängige Variable sollte für y "NA" eingegeben werden. Die Dateneingabe für das obige Dataset würde folgendermaßen lauten: "10;5;2,18;1;6,6;5.3;2.1,7;5;5,22;3;4,12;2;1,NA;3;4". Die Ausgabe ist der vorhergesagte Wert für jede Zeile basierend auf der unabhängigen Variablen. 

>Dieser Dienst, gehostet auf Microsoft Azure Marketplace, ist ein OData-Dienst. Diese können durch POST- oder GET-Methoden aufgerufen werden. 

Es gibt mehrere Möglichkeiten der Nutzung des Diensts auf automatisierte Weise. ([Hier](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx ) finden Sie eine Beispiel-App).

###Starten von C#-Code für Webdienstnutzung:

	public class Input{
	public double Recency;
	public double Frequency;
	public double Monetary;
	public double Time;
	public double Class;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        System.Diagnostics.Debug.WriteLine("AuthenticationHeaderValue" + new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray)));
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
       
	void Main()
	{
  	var input = new Input(){Recency =1, Frequency=0,Monetary=0,Time=1, Class= 0};
	var json = JsonConvert.SerializeObject(input);
	var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
       
  	var httpClient = new HttpClient();
   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
  	var result = query.Result.Content;
  	var scoreResult = result.ReadAsStringAsync().Result;
  	scoreResult.Dump();
	}


#Erstellen des Webdiensts  
>Dieser Webdienst wurde mithilfe von Azure ML erstellt. Eine kostenlose Testversion sowie einführende Videos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) finden Sie unter [azure.com/ml](http://azure.com/ml). Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.


Aus Azure ML wurde ein neues leeres Experiment erstellt, und zwei "R Skript ausführen"-Module wurden auf den Arbeitsbereich gezogen. Dieser Webdienst führt ein Azure Machine Learning-Experiment mit zugrunde liegendem R-Skript aus.  Es gibt zwei Teile dieses Experiments: Schemadefinition Modell trainieren und bewerten.  Das erste Modul definiert die erwartete Struktur des Eingabedatasets, wobei die erste Variable die abhängige Variable ist und die restlichen Variablen unabhängig sind. Das zweite Modul passt ein generisches lineares Regressionsmodell den Eingabedaten an.  
  
![Experiment flow][3]

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
Dies ist ein sehr einfaches Beispiel eines Webdiensts mit multipler linearer Regression. Wie aus den oben stehenden Beispielcode ersichtlich ist, wird kein Abfangen von Fehlern implementiert und der Dienst geht davon aus, dass alles aus kontinuierlichen Variablen besteht (keine kategorischen Funktionen zulässig), da der Dienst nur Eingaben numerischer Werte zum Zeitpunkt der Erstellung dieses Webdiensts durchführt. Darüber hinaus behandelt der Dienst derzeit einen beschränkten Datenumfang, aufgrund der Antwort-/Anfragebeschaffenheit des Webdienstaufrufs und der Tatsache, dass das Modell jedes Mal angepasst wird, wenn der Webdienst aufgerufen wird. 

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts oder Veröffentlichung im Marketplace finden Sie [hier](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png

<!--HONumber=46--> 
