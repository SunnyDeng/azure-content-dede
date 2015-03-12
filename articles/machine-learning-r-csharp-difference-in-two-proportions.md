<properties 
	pageTitle="Unterschied im Anteilstest | Azure" 
	description="Unterschied im Anteilstest" 
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


#Unterschied im Anteilstest




Unterscheiden sich zwei Anteile statistisch? Angenommen, ein Benutzer möchte zwei Filme vergleichen, um zu bestimmen, ob ein Film einen wesentlich höheren Anteil an "Likes" im Vergleich zum anderen hat. Bei einem großen Beispiel kann es einen statistisch signifikanten Unterschied zwischen dem Anteil 0,50 und 0,51 geben, während es in einem kleinen Beispiel möglicherweise nicht genügend Daten gibt, um zu bestimmen, ob diese Anteile tatsächlich verschieden sind. 

Dieser [Webdienst]( https://datamarket.azure.com/dataset/aml_labs/prop_test) führt einen Hypothesentest des Unterschieds zweier Anteile duch, auf Grundlage der Benutzereingabe der Anzahl der Erfolge und der Gesamtzahl der Versuche für die zwei Vergleichsgruppen. Ein Szenario wäre, dass dieser Webdienst aus einer Filmvergleichsapp den Benutzer anhand von Bewertungen darüber informiert, ob einer der Filme tatsächlich mehr positive Bewertungen als der andere erhalten hat.

>Dieser Webdienst kann von Benutzern verwendet werden - möglicherweise mit einer mobilen App, Website oder z. B. sogar auf einem lokalen Computer. Der Zweck des Webdiensts ist auch, als Beispiel dafür zu dienen, wie Sie mit Azure ML Webdienste auf R-Code-Basis erstellen können. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure ML Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden, ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.


##Nutzung des Webdiensts

Dieser Dienst akzeptiert vier Argumente und testet eine Hypothese zu den Anteilen.

Die Eingabeargumente sind:

* Successes1: Anzahl der Erfolgsereignisse in Beispiel 1
* Successes2: Anzahl der Erfolgsereignisse in Beispiel 2
* Total1: Größe des Beispiels 1
* Total2: Größe des Beispiels 2

Die Ausgabe des Diensts ist das Ergebnis des Hypothesentests zusammen mit der Chi-Quadrat Statistik, df, p-Wert Anteil in Beispiel 1/2 und Vertrauensbereichgrenzen.

>Dieser Dienst, gehostet auf Microsoft Azure Marketplace, ist ein OData-Dienst. Diese können durch POST- oder GET-Methoden aufgerufen werden. 

Es gibt mehrere Möglichkeiten der Nutzung des Diensts auf automatisierte Weise. ([Hier](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx ) finden Sie eine Beispiel-App).

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

##Erstellen des Webdiensts

>Dieser Webdienst wurde mithilfe von Azure ML erstellt. Eine kostenlose Testversion sowie einführende Videos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) finden Sie unter [azure.com/ml](http://azure.com/ml). Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.

Aus Azure ML wurde ein neues leeres Experiment mit zwei "R Skript ausführen"-Modulen erstellt. Im ersten Modul wird das Schema definiert, während das zweite Modul den prop.test-Befehl in R verwendet, um die Hypothese für 2 Anteile zu testen. 


###Experimentablauf:

![Experiment flow][2]


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

Dies ist ein sehr einfaches Beispiel für das Testen des Unterschieds bei 2 Anteilen. Wie aus den oben stehenden Beispielcode ersichtlich ist, wird kein Abfangen von Fehlern implementiert und der Dienst geht davon aus, dass alle Variablen kontinuierlich sind.

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts oder Veröffentlichung im Marketplace finden Sie [hier](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png

<!--HONumber=46--> 
