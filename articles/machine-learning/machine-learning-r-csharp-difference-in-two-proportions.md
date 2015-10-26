<properties 
	pageTitle="Unterschied im Anteilstest | Microsoft Azure" 
	description="Unterschied im Anteilstest" 
	services="machine-learning" 
	documentationCenter="" 
	authors="aniedea" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/02/2015" 
	ms.author="aniedea"/>


#Unterschied im Anteilstest


Unterscheiden sich zwei Anteile statistisch? Angenommen, ein Benutzer möchte zwei Filme vergleichen, um zu bestimmen, ob ein Film einen wesentlich höheren Anteil an "Likes" im Vergleich zum anderen hat. Mit einer umfangreichen Stichprobe kann sich eine statistisch relevante Abweichung zwischen den Anteilen 0,50 und 0,51 ergeben. Mit einer kleinen Stichprobe sind möglicherweise nicht genügend Daten vorhanden, um zu bestimmen, ob sich diese Anteile tatsächlich unterscheiden.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Dieser [Webdienst](https://datamarket.azure.com/dataset/aml_labs/prop_test) führt auf Grundlage der Benutzereingabe der Anzahl von Erfolgen und der Gesamtanzahl von Versuchen für die zwei Vergleichsgruppen einen Hypothesentest des Unterschieds zweier Anteile durch. Ein Szenario wäre, dass dieser Webdienst aus einer Filmvergleichs-App aufgerufen wird und den Benutzer anhand von Bewertungen darüber informiert, ob einer der Filme tatsächlich mehr "Likes" als der andere erhalten hat.

>Dieser Webdienst kann von Benutzern verwendet werden – beispielsweise über eine mobile App, eine Website oder sogar über einen lokalen Computer. Dieser Webdienst ist jedoch auch ein gutes Beispiel dafür, wie Azure Machine Learning zum Erstellen von Webdiensten basierend auf R-Code verwendet werden kann. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure Machine Learning Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden – ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.


##Nutzung des Webdiensts

Dieser Dienst akzeptiert vier Argumente und testet eine Hypothese zu den Anteilen.

Die Eingabeargumente sind:

* Successes1 – Anzahl von Erfolgsereignissen in Stichprobe 1
* Successes2 – Anzahl von Erfolgsereignissen in Stichprobe 2
* Total1 – Größe von Stichprobe 1
* Total2 – Größe von Stichprobe 2

Die Ausgabe des Diensts ist das Ergebnis des Hypothesentests, gemeinsam mit Chi-Quadrat-Statistik, df, p-Wert und Anteil in Stichprobe 1/2 und Vertrauensbereichsgrenzen.

>Dieser Dienst, der im Azure Marketplace gehostet wird, ist ein OData-Dienst. Diese Dienste können durch POST- oder GET-Methoden aufgerufen werden.

Es gibt mehrere Möglichkeiten, den Dienst auf automatisierte Weise zu nutzen ([hier](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx) finden Sie eine Beispiel-App).

###Starten von C#-Code für Webdienstnutzung:

	public class Input
	{
	        public string successes1;
	        public string successes2;
	        public string total1;
	        public string total2;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}

	void Main()
	{
	        var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
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

In Azure Machine Learning wurde ein neues leeres Experiment mit zwei [Execute R Script][execute-r-script]-Modulen erstellt. Im ersten Modul wird das Schema definiert, während das zweite Modul den prop.test-Befehl in R verwendet, um die Hypothese für zwei Anteile zu testen.


###Experimentablauf:

![Experimentablauf][2]


####Modul 1:
	####Schema definition  
	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####Modul 2:

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##Einschränkungen 

Dies ist ein sehr einfaches Beispiel für das Testen des Unterschieds bei zwei Anteilen. Wie aus den oben stehenden Beispielcode ersichtlich ist, wird kein Abfangen von Fehlern implementiert und der Dienst geht davon aus, dass alle Variablen kontinuierlich sind.

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts und zum Veröffentlichen im Azure Marketplace finden Sie [hier](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=Oct15_HO3-->