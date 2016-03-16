<properties 
	pageTitle="Normal Distribution Web Service Suite | Microsoft Azure" 
	description="Normal Distribution Web Service Suite" 
	services="machine-learning" 
	documentationCenter="" 
	authors="ireiter" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2016" 
	ms.author="ireiter"/>

#Normal Distribution Suite



Die Normal Distribution Suite ist ein Satz mit Beispielwebdiensten ([Generator](https://datamarket.azure.com/dataset/aml_labs/ndg7), [Quantile Calculator](https://datamarket.azure.com/dataset/aml_labs/ndq5), [Probability Calculator](https://datamarket.azure.com/dataset/aml_labs/ndp5)), mit denen Sie Normalverteilungen generieren und verarbeiten können. Mit den Diensten können Sie eine Normalverteilungssequenz beliebiger Länge generieren, die Quantile aus angegebenen Wahrscheinlichkeiten sowie Wahrscheinlichkeiten aus einem angegebenen Quantil errechnen. Alle Dienste machen unterschiedliche Ausgaben basierend auf dem ausgewählten Dienst (siehe Beschreibung unten). Die Normal Distribution Suite basiert auf den R-Funktionen "qnorm", "rnorm" und "pnorm", die im R-Statistikpaket enthalten sind.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Dieser Webdienst kann von Benutzern verwendet werden – beispielsweise über eine mobile App, eine Website oder sogar über einen lokalen Computer. Dieser Webdienst ist jedoch auch ein gutes Beispiel dafür, wie Azure Machine Learning zum Erstellen von Webdiensten basierend auf R-Code verwendet werden kann. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure Machine Learning Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden – ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.
 

##Nutzung des Webdiensts
Die Normal Distribution Suite enthält die folgenden drei Dienste:

###Normal Distribution Quantile Calculator
Dieser Dienst akzeptiert vier Argumente einer Normalverteilung und berechnet die zugehörigen Quantile.

Die Eingabeargumente sind:

* p – eine einzige Wahrscheinlichkeit eines Ereignisses mit Normalverteilung 
* mean – Mittelwert der Normalverteilung
* sd – Standardabweichung der Normalverteilung 
* side – L für den unteren Rand der Verteilung, U für den oberen Rand der Verteilung

Die Ausgabe des Diensts ist das berechnete Quantil, das zur angegebenen Wahrscheinlichkeit gehört.

###Normal Distribution Probability Calculator
Dieser Dienst akzeptiert vier Argumente einer Normalverteilung und berechnet die zugehörige Wahrscheinlichkeit.

Die Eingabeargumente sind:

* q – ein einzelnes Quantil eines Ereignisses mit Normalverteilung 
* mean – Mittelwert der Normalverteilung
* sd – Standardabweichung der Normalverteilung 
* side – L für den unteren Rand der Verteilung, U für den oberen Rand der Verteilung

Die Ausgabe des Diensts ist die berechnete Wahrscheinlichkeit, die dem angegebenen Quantil zugeordnet ist.

###Normal Distribution Generator
Dieser Dienst akzeptiert 3 Argumente einer Binomialverteilung und generiert eine zufällige Sequenz von Zahlen, die normal verteilt werden. Die folgenden Argumente sollten innerhalb der Anforderung bereitgestellt werden:

* n – Anzahl von Beobachtungen 
* mean – Mittelwert der Normalverteilung
* sd – Standardabweichung der Normalverteilung 

Die Ausgabe des Diensts ist eine Sequenz der Länge n mit einer Normalverteilung anhand der Argumente mean und SD.

>Dieser Dienst, der im Azure Marketplace gehostet wird, ist ein OData-Dienst. Diese Dienste können durch POST- oder GET-Methoden aufgerufen werden.

Es gibt mehrere Möglichkeiten, den Dienst auf automatisierte Weise zu nutzen (Beispiel-Apps finden Sie hier: [Generator](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx), [Probability Calculator](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx), [Quantile Calculator](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

###Starten von C#-Code für Webdienstnutzung:

###Normal Distribution Quantile Calculator
	public class Input
	{
	        public string p;
	        public string mean;
	        public string sd;
	        public string side;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
	    	var scoreResult = result.ReadAsStringAsync().Result;
	}


###Normal Distribution Probability Calculator
	public class Input
	{
	        public string q;
	        public string mean;
	        public string sd;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}

###Normal Distribution Generator
	public class Input
	{
	        public string n;
	        public string mean;
	        public string sd;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
>Dieser Webdienst wurde mithilfe von Azure Machine Learning erstellt. Eine kostenlose Testversion sowie Einführungsvideos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](machine-learning-publish-a-machine-learning-web-service.md) finden Sie unter [azure.com/ml](http://azure.com/ml).

Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.

###Normal Distribution Quantile Calculator

Experimentablauf:

![Experimentablauf][2]
 
	#Data schema with example data (replaced with data from web service)
	data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
	} else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
	}
	q = qnorm(param$p,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	q = 2* param$mean - q
	} else if (param$side =='L') {
	q = q
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(q)
	
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Normal Distribution Probability Calculator
Experimentablauf:

![Experimentablauf][3]
 
 	#Data schema with example data (replaced with data from web service)
	data.set=data.frame(q=-1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	prob = pnorm(param$q,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	prob = 1 - prob
	} else if (param$side =='B') {
	prob = ifelse(prob<=0.5,prob * 2, 1)
	} else if (param$side =='L') {
	prob = prob
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(prob)
	
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Normal Distribution Generator
Experimentablauf:

![Experimentablauf][4]

	#Data schema with example data (replaced with data from web service)
	data.set=data.frame(n=50,mean=0,sd=1);
	maml.mapOutputPort("data.set"); #send data to output port
	
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	dist = rnorm(param$n,mean=param$mean,sd=param$sd)

	output = as.data.frame(t(dist))

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");

##Einschränkungen 

Dies sind sehr einfache Beispiele zum Thema Normalverteilung. Wie aus den oben stehenden Beispielcode ersichtlich ist, wird kaum Abfangen von Fehlern implementiert.

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts und zum Veröffentlichen im Azure Marketplace finden Sie [hier](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
 

<!---HONumber=AcomDC_0302_2016-->