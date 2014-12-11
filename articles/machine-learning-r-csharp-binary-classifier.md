<properties title="Binary Classifier" pageTitle="Binäre Klassifizierung | Azure" description="Binary Classifier" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 



#Binäre Klassifizierung 

  


Angenommen, Sie verfügen über ein Dataset und möchten eine binäre abhängige Variable basierend auf den unabhängigen Variablen vorhersagen. Die "logistische Regression" ist ein beliebtes statistisches Verfahren für eine solche Vorhersage. Hier ist die abhängige Variable binär oder dichotom und p ist die Wahrscheinlichkeit der Anwesenheit für das betrachtete Merkmal. 

Ein einfaches Szenario könnte sein, dass der Forscher versucht vorherzusehen, ob ein potenzieller Student das Zulassungsangebot einer Universität basierend auf Informationen (Abschlussnote, Familieneinkommen, Staat/Bundesland des Wohnorts, Geschlecht) annehmen wird. Das vorhergesagte Ergebnis ist die Wahrscheinlichkeit, dass ein potenzieller Student das Zulassungsangebot annimmt. Dieser [Webdienst]( https://datamarket.azure.com/dataset/aml_labs/log_regression) passt das logistische Regressionsmodell den Daten an und gibt den Wahrscheinlichkeitswert (y) für jede der Beobachtungen in den Daten aus.  
  
>Dieser Webdienst kann von Benutzern verwendet werden - möglicherweise mit einer mobilen App, Website oder z. B. sogar auf einem lokalen Computer. Der Zweck des Webdiensts ist auch, als Beispiel dafür zu dienen, wie Sie mit Azure ML Webdienste auf R-Code-Basis erstellen können. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure ML Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden, ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.  
  

#Nutzung des Webdiensts  
Dieser Webdienst liefert die vorhergesagten Werte der abhängigen Variablen basierend auf den unabhängigen Variablen für alle Beobachtungen. Der Webdienst erwartet, dass der Endbenutzer seine Daten als Zeichenfolge eingibt, wobei Zeilen durch Kommas (,) getrennt und Spalten durch Semikolons (;) getrennt werden. Der Webdienst erwartet eine Zeile nach der anderen und dass die erste Spalte die abhängige Variable ist. Ein Beispieldataset könnte folgendermaßen aussehen:

![Sample data][1]

Bei Beobachtungen ohne eine abhängige Variable sollte für y "NA" eingegeben werden. Die Dateneingabe für das obige Dataset würde folgendermaßen lauten: "1; 5; 2,1; 1; 6,0; 5.3; 2.1,0; 5; 5,0; 3; 4,1; 2; 1, NA; 3; 4". Die Ausgabe ist der vorhergesagte Wert für jede Zeile basierend auf der unabhängigen Variablen. 

>Dieser Dienst, gehostet auf Microsoft Azure Marketplace, ist ein OData-Dienst. Diese können durch POST- oder GET-Methoden aufgerufen werden. 

Es gibt mehrere Möglichkeiten der Nutzung des Diensts auf automatisierte Weise. ([Hier](http://microsoftazuremachinelearning.azurewebsites.net/BinaryClassifier.aspx ) finden Sie eine Beispiel-App).

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

Aus Azure ML wurde ein neues leeres Experiment erstellt, und zwei "R Skript ausführen"-Module wurden auf den Arbeitsbereich gezogen. Dieser Webdienst führt ein Azure Machine Learning-Experiment mit zugrunde liegendem R-Skript aus.  Es gibt zwei Teile dieses Experiments: Schemadefinition Modell trainieren und bewerten.  Das erste Modul definiert die erwartete Struktur des Eingabedatasets, wobei die erste Variable die abhängige Variable ist und die restlichen Variablen unabhängig sind. Das zweite Modul passt ein generisches logistisches Regressionsmodell den Eingabedaten an.    

![Experiment flow][2]

####Modul 1:

	#Schema definition  
	data <- data.frame(value = "1;2;3,1;5;6,0;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####Module 2:
	#GLM Modeling   
	data <- maml.mapInputPort(1) # class: data.frame  data.split <- strsplit(data[1,1], ",")[[1]] data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) data.split <- as.data.frame(t(data.split)) data.split <- 
	data.matrix(data.split) data.split <- data.frame(data.split) model <- glm(data.split$V1 ~., family='binomial', data=data.split)  out <- 
	data.frame(predict(model,data.split, type="response")) pred1 <- as.data.frame(out) group <- array(1:nrow(pred1)) for (i in 
	1:nrow(pred1))  
	{if(as.numeric(pred1[i,])>0.5) {group[i]=1} else {group[i]=0}} pred2 <- as.data.frame(group) maml.mapOutputPort("pred2");  


#Einschränkungen
Dies ist ein sehr einfaches Beispiel eines Webdiensts für binäre Klassifikation. Wie aus den oben stehenden Beispielcode ersichtlich ist, wird kein Abfangen von Fehlern implementiert und der Dienst geht davon aus, dass alles aus binären/kontinuierlichen Variablen besteht (keine kategorischen Funktionen zulässig), da der Dienst nur Eingaben numerischer Werte zum Zeitpunkt der Erstellung dieses Webdiensts durchführt. Darüber hinaus behandelt der Dienst derzeit einen beschränkten Datenumfang, aufgrund der Antwort-/Anfragebeschaffenheit des Webdienstaufrufs und der Tatsache, dass das Modell jedes Mal angepasst wird, wenn der Webdienst aufgerufen wird. 

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts oder Veröffentlichung im Marketplace finden Sie [hier](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-binary-classifier/binary1.png
[2]: ./media/machine-learning-r-csharp-binary-classifier/binary2.png
