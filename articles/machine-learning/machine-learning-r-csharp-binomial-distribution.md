<properties 
	pageTitle="Binomial Distribution Suite | Azure" 
	description="Binomial Distribution Suite" 
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


#Binomial Distribution Suite




Die Binomial Distribution Suite ist ein Satz mit Beispielwebdiensten ([Binomial Generator](https://datamarket.azure.com/dataset/aml_labs/bdg5), [Probability Calculator]( https://datamarket.azure.com/dataset/aml_labs/bdp4), [Quantile Calculator]( https://datamarket.azure.com/dataset/aml_labs/bdq5)), insbesondere drei Dienste, mit denen Sie Binomialverteilung generieren und behandeln können. Mit den Diensten können Sie eine Binomialverteilungssequenz beliebiger Länge generieren, die Quantile aus angegebenen Wahrscheinlichkeiten und Wahrscheinlichkeiten aus einem angegebenen Quantil errechnen.  Alle Dienste machen unterschiedliche Ausgaben basierend auf dem ausgewählten Dienst (siehe Beschreibung unten). Die Binomial Distribution Suite basiert auf den R-Funktionen qbinom, rbinom und pbinom, die im R-Statistikpaket enthalten sind. 

>Diese Webdienste können von Benutzern verwendet werden - möglicherweise mit einer mobilen App, Website oder z. B. sogar auf einem lokalen Computer. Der Zweck des Webdiensts ist auch, als Beispiel dafür zu dienen, wie Sie mit Azure ML Webdienste auf R-Code-Basis erstellen können. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure ML Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden, ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.

##Nutzung des Webdiensts
Die Binomial Distribution Suite enthält die folgenden drei Dienste:

###Binomial Distribution Quantile Calculator
Dieser Dienst akzeptiert vier Argumente einer Normalverteilung und berechnet die zugehörigen Quantile.
Die Eingabeargumente sind:

- p - Eine einzige aggregierte Wahrscheinlichkeit von mehreren Versuchen  
- size - Die Anzahl der Versuche
- prob - Die Wahrscheinlichkeit des Erfolgs eines Versuchs
- Side - L für den unteren Rand der Verteilung, U für den oberen Rand der Verteilung 

Die Ausgabe des Diensts ist das berechnete Quantil, das zur angegebenen Wahrscheinlichkeit gehört.

###Binomial Distribution Probability Calculator
Dieser Dienst akzeptiert vier Argumente einer Binomialverteilung und berechnet die zugehörige Wahrscheinlichkeit.
Die Eingabeargumente sind:

- Q - Ein einzelnes Quantil eines Ereignisses mit Binomialverteilung 
- size - Die Anzahl der Versuche
- prob - Die Wahrscheinlichkeit des Erfolgs eines Versuchs
- side - L für den unteren Rand der Verteilung, U für den oberen Rand der Verteilung oder E, die einer einzelnen Zahl von Erfolgen entspricht.

Die Ausgabe des Diensts ist die berechnete Wahrscheinlichkeit, die dem angegebenen Quantil zugeordnet ist.

###Binomial Distribution Generator
Dieser Dienst akzeptiert 3 Argumente einer Binomialverteilung und generiert eine zufällige Sequenz von Zahlen, die binomial verteilt werden. 
Die folgenden Argumente sollten innerhalb der Anforderung bereitgestellt werden:

- n - Anzahl der Beobachtungen 
- size - Die Anzahl der Versuche
- prob - Die Wahrscheinlichkeit des Erfolgs

Die Ausgabe des Diensts ist eine Sequenz der Länge n mit einer Binomialverteilung anhand der Argumente size und prob.

>Dieser Dienst, gehostet auf Microsoft Azure Marketplace, ist ein OData-Dienst. Diese können durch POST- oder GET-Methoden aufgerufen werden. 

Es gibt mehrere Möglichkeiten der Nutzung des Diensts auf automatisierte Weise. (Beispiel-Apps finden Sie hier: [Generator](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx),
[Probability Calculator](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx),
[Quantile Calculator](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)). 

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

###Binomial Distribution Quantile Calculator

![Create workspace][4]

####Modul 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####Modul 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
    } else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
    }

    if (param$prob < 0 ) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 0
    } else if (param$prob > 1) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
	band=subset(df,x>quantile)
    } else if (param$side =='L') {
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
	band=subset(df,x<=quantile)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###Binomial Distribution Probability Calculator

![Create workspace][5]

####Modul 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####Modul 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
	prob = 1 - prob
	band=subset(df,x>param$q)
    } else if (param$side =='E') {
	prob = prob.eq
	band=subset(df,x==param$q)
    } else if (param$side =='L') {
	prob = prob
	band=subset(df,x<=param$q)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(prob)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###Binomial Distribution Generator

![Create workspace][6]

####Modul 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####Modul 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##Einschränkungen 
Dies sind sehr einfache Beispiele zum Thema Binomialverteilung. Wie aus den oben stehenden Beispielcode ersichtlich ist, wird kaum Abfangen von Fehlern implementiert.

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts oder Veröffentlichung im Marketplace finden Sie [hier](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png

<!--HONumber=46--> 
 