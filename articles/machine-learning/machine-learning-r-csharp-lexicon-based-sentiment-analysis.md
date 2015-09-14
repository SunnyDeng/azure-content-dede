<properties 
	pageTitle="Lexikonbasierte Stimmungsanalyse | Microsoft Azure"
	description="Lexikonbasierte Stimmungsanalyse"
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
	ms.date="09/02/2015"
	ms.author="jaymathe"/>



#Lexikonbasierte Stimmungsanalyse 

Wie können Sie die Meinungen und Einstellungen Ihrer Benutzer zu Marken oder Themen in sozialen Netzwerken wie z. B. Facebook-Beiträgen, Tweets, Rezensionen usw. messen? Die Stimmungsanalyse bietet eine Methode, solche Fragen zu analysieren.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Es gibt im Allgemeinen zwei Methoden für die Stimmungsanalyse. Bei der einen Methode handelt es sich um einen überwachten Lernalgorithmus, die andere wird als "nicht überwachtes Lernen" bezeichnet. Ein überwachter Lernalgorithmus erstellt in der Regel ein Klassifizierungsmodell auf Basis eines großen Korpus mit Anmerkungen. Die Genauigkeit hierbei basiert hauptsächlich auf der Qualität der Anmerkungen, und das Training dauert in der Regel sehr lange. Außerdem ist beim Anwenden des Algorithmus auf eine andere Domäne das Ergebnis in der Regel nicht gut. Im Gegensatz zum überwachten Lernen wird beim lexikonbasierten nicht überwachten Lernen ein Simmungswörterbuch verwendet. Für diese Methode muss kein großer Datenkorpus gespeichert werden, und sie erfordert kein Training, wodurch der gesamte Prozess deutlich schneller ist.

Der [Dienst](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) basiert auf dem MPQA Subjectivity Lexicon (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/)), einem der am häufigsten verwendeten Subjektivitätslexika. Es gibt 5.097 negative und 2.533 positive Wörter im MPQA. Und all diese Wörter sind als stark oder schwach gekennzeichnet. Der gesamte Korpus befindet sich unter der GNU General Public License. Der Webdienst kann auf alle kurzen Sätze wie Tweets und Facebook-Beiträge angewendet werden.

>Dieser Webdienst kann von Benutzern verwendet werden – beispielsweise über eine mobile App, eine Website oder sogar über einen lokalen Computer. Dieser Webdienst ist jedoch auch ein gutes Beispiel dafür, wie Azure Machine Learning zum Erstellen von Webdiensten basierend auf R-Code verwendet werden kann. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure Machine Learning Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden – ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.

##Nutzung des Webdiensts

Die Eingabedaten können ein beliebiger Text sein, aber der Webdienst funktioniert besser mit kurzen Sätzen. Die Ausgabe ist ein numerischer Wert zwischen-1 und 1. Jeder Wert unter 0 zeigt, dass die Stimmung des Texts negativ ist. Bei jedem Wert über 0 ist sie positiv. Der absolute Wert des Ergebnisses zeigt die Stärke der zugehörigen Stimmung.

>Dieser Dienst, der im Azure Marketplace gehostet wird, ist ein OData-Dienst. Diese Dienste können durch POST- oder GET-Methoden aufgerufen werden.

Es gibt mehrere Möglichkeiten, den Dienst auf automatisierte Weise zu nutzen ([hier](http://microsoftazuremachinelearning.azurewebsites.net/) finden Sie eine Beispiel-App).

###Starten von C#-Code für Webdienstnutzung:

	public class ScoreResult
	{
	        [DataMember]
	        public double result
	        {
	            get;
	            set;
	        }
	}

	void main()
	{
	        using (var wb = new WebClient())
	        {
	            var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
	            DataServiceContext ctx = new DataServiceContext(acitionUri);
	            var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
	            var cache = new CredentialCache();
	
	            cache.Add(acitionUri, "Basic", cred);
	            ctx.Credentials = cache;
	            var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
	            ScoreResult scoreResult = query.ElementAt(0);
	            double result = scoreResult.result;
	    	}
	}



Die Eingabe lautet: "Today is a good day." Die Ausgabe lautet "1", was darauf hinweist, dass mit dem Eingabesatz eine positive Bedeutung assoziiert wird.

##Erstellen des Webdiensts
>Dieser Webdienst wurde mithilfe von Azure Machine Learning erstellt. Eine kostenlose Testversion sowie Einführungsvideos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](machine-learning-publish-a-machine-learning-web-service.md) finden Sie unter [azure.com/ml](http://azure.com/ml). Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.


In Azure Machine Learning wurde ein neues leeres Experiment erstellt. Die nachfolgende Abbildung zeigt den Experimentablauf bei der lexikonbasierten Stimmungsanalyse. Die Datei "sent\_dict.csv" befindet sich im MPQA-Subjektivitätslexikon und ist als eine der Eingaben für [Execute R Script][execute-r-script] festgelegt. Eine andere Eingabe ist eine Stichprobe des Amazon-Bewertungsdatasets zum Testen, bei der Vorgänge für Auswahl, Modifizieren des Spaltennamens und Aufteilen ausgeführt wurden. Sie verwenden ein Hashpaket, um das Subjektivitätslexikon im Arbeitsspeicher zu speichern und den Prozess der Bewertungsberechnung zu beschleunigen. Der gesamte Text wird vom von "tm"-Paket in Token umgesetzt und mit dem Wort im Stimmungswörterbuch verglichen. Schließlich wird ein Ergebnis berechnet, indem die Gewichtung aller subjektiven Wörter im Text hinzugefügt wird.

###Experimentablauf:

![Experimentablauf][2]


####Modul 1:
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # Install hash package install.packages("src/hash\_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE) success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE) library(tm) library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
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

Hinsichtlich des Algorithmus ist die lexikonbasierte Stimmungsanalyse ein allgemeines Stimmungsanalysetool, das für bestimmte Felder möglicherweise keine bessere Leistung bietet als die Klassifizierungsmethode. Das Problem der Negation wird nicht gut gelöst. Sie hartcodieren mehrere Negation Wörter im Programm, eine bessere Möglichkeit ist jedoch die Verwendung eines Negationswörterbuchs und die Erstellung einiger Regeln. Der Webdienst bietet eine bessere Leistung bei kurzen und einfachen Sätzen, wie z. B. Tweets und Facebook-Beiträgen, als bei langen und komplexen Sätzen wie z. B. Rezensionen auf Amazon.

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts und zum Veröffentlichen im Azure Marketplace finden Sie [hier](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 

<!---HONumber=September15_HO1-->