<properties title="Forecasting-Exponential Smoothing" pageTitle="Prognose mit exponentieller Glättung | Azure" description="Web service: Forecasting-Exponential Smoothing" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#Prognose mit exponentieller Glättung 
  



Dieser [Webdienst]( https://datamarket.azure.com/dataset/aml_labs/ets) implementiert ein Exponentielles Glättungsmodell (Exponential Smoothing, ETS), um Vorhersagen auf Grundlage der Verlaufsdaten, die vom Benutzer bereitgestellt werden, zu erzeugen. Erhöht sich der Bedarf für ein bestimmtes Produkt in diesem Jahr? Kann ich meine Produktverkäufe für die Weihnachtssaison vorhersagen, damit ich meine Inventur effektiv planen kann? Planungsmodelle sind für solche Fragen die passende Lösung. Angesichts der letzten Daten, untersuchen diese Modelle versteckte Trends und Saisonabhängigkeit, um zukünftige Trends vorherzusagen.  
 
>Dieser Webdienst kann von Benutzern verwendet werden - möglicherweise mit einer mobilen App, Website oder z. B. sogar auf einem lokalen Computer. Der Zweck des Webdiensts ist auch, als Beispiel dafür zu dienen, wie Sie mit Azure ML Webdienste auf R-Code-Basis erstellen können. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure ML Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden, ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.
 
##Nutzung des Webdiensts 
 
Dieser Dienst akzeptiert 4 Argumente und berechnet die ETS-Prognosen.
Die Eingabeargumente sind:

* Häufigkeit: Gibt die Häufigkeit der Rohdaten an (täglich/wöchentlich/monatlich/vierteljährlich/jährlich)
* Horizont: Zeitrahmen der zukünftigen Prognose
* Datum: Hinzufügen der neuen Zeitreihendaten für Zeit
* Wert: Hinzufügen der neuen Zeitreihendaten für Datenwerte

Die Ausgabe des Dienstes sind die berechneten Werte für die Prognose.

Eine Beispieleingabe wäre: 

* Häufigkeit: 12
* Horizont: 12
* Date:1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012;
1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013;
1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Wert: 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511;
3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509
 
>Dieser Dienst, gehostet auf Microsoft Azure Marketplace, ist ein OData-Dienst. Diese können durch POST- oder GET-Methoden aufgerufen werden. 

Es gibt mehrere Möglichkeiten der Nutzung des Diensts auf automatisierte Weise. ([Hier](http://microsoftazuremachinelearning.azurewebsites.net/etsForecasting.aspx) finden Sie eine Beispiel-App).

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

>Dieser Webdienst wurde mithilfe von Azure ML erstellt. Eine kostenlose Testversion sowie einführende Videos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) finden Sie unter [azure.com/ml](http://azure.com/ml). Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.

Aus Azure ML wurde ein neues leeres Experiment erstellt. Beispieleingabedaten wurden mit einem vordefinierten Datenschema hochgeladen. Mit dem Schema verknüpft ist ein "R-Skript ausführen"-Modul, welches das ARIMA-Prognmoseodell mithilfe der 'ets'- und 'forecast'-Funktionen von R generiert. 


![Experiment flow][2]

####Modul 1:
 
	Hinzufügen der CSV-Datei mit den Daten im unten gezeigten Format 
![Data sample][3]	

####Modul 2:
	# data input
	data <- maml.mapInputPort(1) # class: data.frame
	library(forecast)
	
	# preprocessing
	colnames(data) <- c("frequency", "horizon", "dates", "values")
	dates <- strsplit(data$dates, ";")[[1]]
	values <- strsplit(data$values, ";")[[1]]
	
	dates <- as.Date(dates, format = '%m/%d/%Y')
	values <- as.numeric(values)
	
	# fit a time-series model
	train_ts<- ts(values, frequency=data$frequency)
	fit1 <- ets(train_ts)
	train_model <- forecast(fit1, h = data$horizon)
	plot(train_model)
	
	# produce forcasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# data output
	maml.mapOutputPort("data.forecast");

 
##Einschränkungen 

Dies ist ein sehr einfaches Beispiel für die Prognose mit ETS. Wie aus den oben stehenden Beispielcode ersichtlich ist, wird kein Abfangen von Fehlern implementiert und der Dienst geht davon aus, dass alle Variablen kontinuierliche/positive Werte sind und die Häufigkeit sollte eine ganze Zahl größer als 1 sein. Die Länge der Vektoren Datum und Wert sollte identisch sein. Die Variable für das Datum muss dem Format "mm/tt/jjjj" entsprechen.

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts oder Veröffentlichung im Marketplace finden Sie [hier](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img1.png
[2]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img2.png
[3]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img3.png
