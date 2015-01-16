<properties title="Cluster Model" pageTitle="Cluster-Modell | Azure" description="Cluster-Modell" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 


#Cluster-Modell    



Wie können wir das Verhalten von Kreditkartenbesitzergruppen vorhersagen, um die Belastungsrisiken der Kreditunternehmen zu verringern?  Wie können wir Gruppen von Persönlichkeitsmerkmalen von Mitarbeitern definieren, um die Leistung bei der Arbeit zu verbessern? Wie können Ärzte Patienten anhand der Merkmale ihrer Krankheiten in Gruppen unterteilen? Grundsätzlich können alle diese Fragen durch Clusteranalyse beantwortet werden.    
   
In der Praxis klassifiziert Clusteranalyse einen Satz von Beobachtungen in mindestens zwei sich gegenseitig ausschließenden unbekannten Gruppen basierend auf Kombinationen von Variablen. Die Clusteranalyse dient zum Ermitteln eines Systems zum Organisieren von Beobachtungen, in der Regel Personen oder ihren Merkmalen, in Gruppen, wobei Mitglieder der Gruppen Eigenschaften gemeinsam haben. Dieser [Dienst](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) verwendet die K-Means-Methode, ein häufig verwendetes Clusteringverfahren für die Zusammenfassung beliebiger Daten in Gruppen. Dieser Webdienst akzeptiert die Daten und die Anzahl an Clustern k als Eingabe und macht Vorhersagen dazu, welche k-Gruppe zu welcher Beobachtungen gehört. 

>Dieser Webdienst kann von Benutzern verwendet werden - möglicherweise mit einer mobilen App, Website oder z. B. sogar auf einem lokalen Computer. Der Zweck des Webdiensts ist auch, als Beispiel dafür zu dienen, wie Sie mit Azure ML Webdienste auf R-Code-Basis erstellen können. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure ML Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden, ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.  

#Nutzung des Webdiensts   
Dieser Webdienst gruppiert die Daten in einen Satz von k-Gruppen und gibt die Gruppenzuordnung für jede Zeile aus. Der Webdienst erwartet, dass der Endbenutzer seine Daten als Zeichenfolge eingibt, wobei Zeilen durch Kommas (,) getrennt und Spalten durch Semikolons (;) getrennt werden. Der Webdienst erwartet eine Zeile nach der anderen. Ein Beispieldataset könnte folgendermaßen aussehen:

![Sample data][1]

Angenommen, der Benutzer möchte diese Daten in drei sich gegenseitig ausschließende Gruppen aufteilen. Die Dateneingabe für das obige Dataset würde folgendermaßen lauten: value = "10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4"; k="3". Die Ausgabe ist die vorhergesagte Gruppenzugehörigkeit für jede Zeile.

>Dieser Dienst, gehostet auf Microsoft Azure Marketplace, ist ein OData-Dienst. Diese können durch POST- oder GET-Methoden aufgerufen werden. 

Es gibt mehrere Möglichkeiten der Nutzung des Diensts auf automatisierte Weise. ([Hier](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx ) finden Sie eine Beispiel-App).

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
>Dieser Webdienst wurde mithilfe von Azure ML erstellt. Eine kostenlose Testversion sowie einführende Videos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) finden Sie unter [azure.com/ml](http://azure.com/ml). Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.

Aus Azure ML wurde ein neues leeres Experiment erstellt, und zwei "R Skript ausführen"-Module wurden auf den Arbeitsbereich gezogen. Das Schema wurde mit einem einfachen "R-Skript ausführen"-Modul erstellt. Anschließend wurde es mit dem Clustermodellbereich verknüpft, der auch mit einem "R-Skript ausführen"-Modul erstellt wurde. Im für das Clustermodell verwendeten "R-Skript ausführen"-Modul nutzt der Webdienst dann die "k-Means"-Funktion, die bereits in das "R-Skript ausführen"-Modul von Azure ML integriert ist.    
   

     
![Experiment flow][3]

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

	# K-Means Cluster Analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
#Einschränkungen
Dies ist ein sehr einfaches Beispiel eines Webdiensts für Clustering. Wie aus den oben stehenden Beispielcode ersichtlich ist, wird kein Abfangen von Fehlern implementiert und der Dienst geht davon aus, dass alles aus kontinuierlichen Variablen besteht (keine kategorischen Funktionen zulässig), da der Dienst nur Eingaben numerischer Werte zum Zeitpunkt der Erstellung dieses Webdiensts durchführt. Darüber hinaus behandelt der Dienst derzeit einen beschränkten Datenumfang, aufgrund der Antwort-/Anfragebeschaffenheit des Webdienstaufrufs und der Tatsache, dass das Modell jedes Mal angepasst wird, wenn der Webdienst aufgerufen wird. 

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts oder Veröffentlichung im Marketplace finden Sie [hier](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png
