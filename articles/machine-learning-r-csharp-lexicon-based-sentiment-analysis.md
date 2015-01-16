<properties title="Lexicon Based Sentiment Analysis" pageTitle="Schritt 1: Lexikonbasierte Stimmungsanalyse | Azure" description="Lexikonbasierte Stimmungsanalyse" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 



#Lexikonbasierte Stimmungsanalyse 

 


Wie können Sie Meinungen von Benutzern und Haltungen zu Marken oder Themen in sozialen Netzwerken online messen, wie z. B. Facebook-Beiträge, Tweets, Bewertungenund usw.? Mit Stimmungsanalyse.

Insbesondere sind zwei allgemeine Methoden für die Stimmungsanalyse von Bedeutung: ein überwachter Lernalgorithmus und eine andere Methode, die als unbeaufsichtigtes Lernen behandelt werden kann. Überwachte Lernalgorithmen erstellen in der Regel ein Klassifizierungsmodell auf Basis eines großen Korpus mit Anmerkungen und ihre Genauigkeit basiert hauptsächlich auf der Qualität der Anmerkungen. In der Regel dauert das Training sehr lange. Außerdem ist beim Anwenden des Algorithmus auf eine andere Domäne das Ergebnis in der Regel nicht gut. Im Vergleich zum überwachten Lernen verwendet lexikonbasiertes unbeaufsichtigtes Lernen ein Simmungswörterbuch, das nicht das Speichern eines großen Datenkorpus und Training erfordert, wodurch der gesamte Prozess deutlich schneller ist. 

Der [Dienst](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) basiert auf dem MPQA-Lexikon(http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), eines der am häufigsten verwendeten Subjektivitätslexika. Es gibt 5.097 negative und 2.533 positive Wörter im MPQA. Und all diese Wörter sind als stark oder schwach gekennzeichnet. Der gesamte Korpus befindet sich unter der GNU General Public License. Der Webdienst kann auf alle kurzen Sätze wie Tweets, Facebook-Beiträge usw. angewendet werden. 

>Dieser Webdienst kann von Benutzern verwendet werden - möglicherweise mit einer mobilen App, Website oder z. B. sogar auf einem lokalen Computer. Der Zweck des Webdiensts ist auch, als Beispiel dafür zu dienen, wie Sie mit Azure ML Webdienste auf R-Code-Basis erstellen können. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure ML Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden, ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.

##Nutzung des Webdiensts

Die Eingabedaten können ein beliebiger Text sein, aber der Webdienst funktioniert besser mit kurzen Sätzen. Die Ausgabe ist ein numerischer Wert zwischen-1 und 1. Jeder Wert unter 0 zeigt, dass die Stimmung des Texts negativ ist. Bei jedem Wert über 0 ist er positiv. Der absolute Wert des Ergebnisses zeigt die Stärke der zugehörigen Stimmung. 

>Dieser Dienst, gehostet auf Microsoft Azure Marketplace, ist ein OData-Dienst. Diese können durch POST- oder GET-Methoden aufgerufen werden. 

Es gibt mehrere Möglichkeiten der Nutzung des Diensts auf automatisierte Weise. ([Hier](http://microsoftazuremachinelearning.azurewebsites.net/) finden Sie eine Beispiel-App).

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


Die Eingabe ist "Today is a good day", dann lautet die Ausgabe "1", was eine positive Bedeutung des Eingabesatzes zeigt. 

##Erstellen des Webdiensts
>Dieser Webdienst wurde mithilfe von Azure ML erstellt. Eine kostenlose Testversion sowie einführende Videos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) finden Sie unter [azure.com/ml](http://azure.com/ml). Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.


Aus Azure ML wurde ein neues leeres Experiment erstellt. Die nachfolgende Abbildung zeigt den Experimentablauf von lexikonbasierter Stimmungsanalyse. Die Datei "sent_dict.csv" befindet sich im MPQA-Subjektivitätslexikon und ist als eine der Eingaben des Moduls "R-Skript ausführen" festgelegt. Eine andere Eingabe ist Bewertungsdataset mit einer Beispielrezension von Amazon zum Testen, bei der die Vorgänge Auswahl, Spaltenname, Modifizierung und Aufteilen ausgeführt wurden.  Sie verwenden ein Hashpaket, um das Subjektivitätslexikon im Arbeitsspeicher zu speichern und den Prozess der Bewertungsberechnung zu beschleunigen. Der gesamte Text wird vom von "tm"-Paket in Token umgesetzt und mit dem Wort im Stimmungswörterbuch verglichen. Schließlich wird ein Ergebnis berechnet, indem die Gewichtung aller subjektiven Wörter im Text hinzugefügt wird. 

###Experimentablauf:

![experimenmt flow][2]


####Modul 1:
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
    # install hash package
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)
    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)
    #  compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
	polarity_ratio <- 0
	polarity_total <- 0
	not <- 0
	sentence <- tolower(dataset2[m,1])
	if (nchar(sentence) > 0){
		token_array <- scan_tokenizer(sentence)
		for (j in 1:length(token_array)){
			word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
		    for (k in 1:length(negation_word)){
		      if (word == negation_word[k]){
		        not <- (not+1) %% 2

			  }
		    }
			if (word != ""){
			    if (!is.null(sent_dict[[word]])){
			      polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
			      polarity_total <- polarity_total + abs(sent_dict[[word]])
			    }
			}
		  
		}
	}
	if (polarity_total > 0){
		result <- c(result, polarity_ratio/polarity_total)
	}else{
		result<- c(result,0)
	}
    }
    # Sample operation
    data.set <- data.frame(result)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
	


##Einschränkungen

Aus Sicht des Algorithmus ist die lexikonbasierte Stimmungsanalyse ein allgemeines Analysetool, das möglicherweise keine bessere Leistung als eine Klassifizierungsmethode für bestimmte Felder ausführen kann. Das Problem der Negation wird nicht gut gelöst. Sie hartcodieren mehrere Negation Wörter im Programm, eine bessere Möglichkeit ist jedoch die Verwendung eines Negationswörterbuchs und die Erstellung einiger Regeln. Der Webdienst bietet eine bessere Leistung bei kurzen und einfachen Sätze wie z. B. Tweets und Facebook-Beiträge, als bei langen und komplexen Sätze wie z. B. Rezensionen auf Amazon. 

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts oder Veröffentlichung im Marketplace finden Sie [hier](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png








