<properties title="Survival Analysis" pageTitle="Lebensdaueranalysen | Azure" description="Survival Analysis" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#Lebensdaueranalysen 





In vielen Szenarien ist das wichtigste bewertete Ergebnis die Zeit, bis ein bestimmtes Ereignis eintritt. Anders ausgedrückt, es wird die Frage "Wann dieses Ereignis ein?" gestellt. Betrachten Sie beispielsweise Situationen, in denen die Daten die verstrichene Zeit (Tage, Jahre, Kilometerstand usw.) beschreiben, bis das erwartete Ereignis (Krankheitsrückfall, Promotion, Versagen der Bremsbeläge) eintritt. Jede Instanz in den Daten steht für ein bestimmtes Objekt (einen Patienten, eine Person, ein Auto usw.).

Dieser [Webdienst]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) gibt eine Antwort auf die Frage "Was ist die Wahrscheinlichkeit, dass das erwartete Ereignis zum Zeitpunkt n für Objekt x stattfindet?". Durch Bereitstellen eines Lebensdaueranalysemodells ermöglicht es dieser Webdienst den Benutzern, Daten zum Trainieren und Testen des Modells bereitzustellen. Am wichtigsten ist für dieses Modell, die Länge der verstrichenen Zeit zu modellieren, bis das gewünschte Ereignis eintritt. 

>Dieser Webdienst kann von Benutzern verwendet werden - möglicherweise mit einer mobilen App, Website oder z. B. sogar auf einem lokalen Computer. Der Zweck des Webdiensts ist auch, als Beispiel dafür zu dienen, wie Sie mit Azure ML Webdienste auf R-Code-Basis erstellen können. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure ML Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden, ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.  

##Nutzung des Webdiensts

Das Eingabedatenschema des Webdienstes wird in der folgenden Tabelle dargestellt. Sechs Informationseinheiten werden als Eingabe benötigt: Trainingsdaten, Testdaten, erwartete Zeit, der Index der "time"-Dimension, der Index der "event"-Dimension und die Variablentypen (kontinuierlich oder Faktor). Die Trainingsdaten werden mit einer Zeichenfolge dargestellt, in der die Zeilen durch Kommas getrennt sind und die Spalten werden durch Semikolons. Die Anzahl der Funktionen der Daten ist flexibel. Alle Elemente in der Eingabezeichenfolge müssen numerisch sein. In den Trainingsdaten gibt die Dimension "time" die Anzahl der Zeiteinheiten (Tage, Jahre, Kilometer usw.) an, die seit der Beginn der Studie vergangen sind (ein Patient beginnt einen Drogenentzug, ein Student beginnt mit der Arbeit an seiner Promotion, ein Auto wird gefahren usw.), bis das erwartete Ereignis eintritt (der Patient nimmt erneut Drogen, der Student promoviert, die Bremsen des Fahrzeugs versagen usw.). Die Dimension "event" gibt an, ob das erwartete Ereignis am Ende der Studie auftritt. "event = 1" bedeutet, das erwartete Ereignis tritt zu dem Zeitpunkt auf, den die Dimension "time" angibt. "event = 0" bedeutet, das erwartete Ereignis ist zu dem Zeitpunkt, den die Dimension "time" angibt, noch nicht aufgetreten.

- trainingdata: Eine Zeichenfolge. Zeilen werden durch Kommas und Spalten durch Semikolons getrennt. Jede Zeile enthält die Dimension "time", die Dimension "event" und Prognosevariablen.
- testingdata: Eine Zeile mit Daten, die Prognosevariablen für ein bestimmtes Objekt enthält.
- time_of_interest: Die verstrichene erwartete Zeit n
- index_time: Der Spaltenindex der Dimension "time" (beginnend mit 1)
- index_event: Der Spaltenindex der Dimension "event" (beginnend mit 1)
- variable_types: Eine Zeichenfolge mit Semikolon als Trennzeichen. 0 stellt kontinuierliche und 1 Faktorvariablen dar.


Die Ausgabe ist die Wahrscheinlichkeit, dass ein Ereignis zu einem bestimmten Zeitpunkt eintritt. 

>Dieser Dienst, gehostet auf Microsoft Azure Marketplace, ist ein OData-Dienst. Diese können durch POST- oder GET-Methoden aufgerufen werden. 

Es gibt mehrere Möglichkeiten der Nutzung des Diensts auf automatisierte Weise. ([Hier](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx) finden Sie eine Beispiel-App). 

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


Die Interpretation dieses Tests lautet wie folgt. Angenommen, das Ziel der Daten ist die Erstellung eines Modells zur verstrichenen Zeit bis die Patienten, die eines der beiden Behandlungsprogramme durchlaufen haben, wieder Drogen nehmen. Die Ausgabe des Webdiensts ergibt: Patienten, die 35 Jahre alt werden, zuvor 2 Entzüge durchlaufen haben, das lange Entzugsprogramm vor Ort wählen und sowohl Heroin als auch Kokain konsumiert haben, werden an Tag 500 zu 95,64 % wieder rückfällig.

##Erstellen des Webdiensts

>Dieser Webdienst wurde mithilfe von Azure ML erstellt. Eine kostenlose Testversion sowie einführende Videos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/) finden Sie unter [azure.com/ml](http://azure.com/ml). Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.

Aus Azure ML wurde ein neues leeres Experiment erstellt, und zwei "R Skript ausführen"-Module wurden auf den Arbeitsbereich gezogen. Das Datenschema wurde mit einem einfachen "R-Skript ausführen"-Modul erstellt, welches das Schema der Eingabedaten für den Webdienst definiert. Dieses Modul wird dann mit dem zweiten "R-Skript ausführen"-Modul verknüpft, das die Hauptarbeit übernimmt. Dieses Modul führt die Datenvorverarbeitung, Modellerstellung und Vorhersagen aus. Beim Schritt der Datenvorverarbeitung werden die Eingabedaten, die durch eine lange Zeichenfolge dargestellt werden, transformiert und in einem Datenrahmen konvertiert. Im Modellerstelungsschritt wird ein externes R-Paket "survival_2.37-7.zip" zuerst für die Durchführung von Lebensdaueranalysen installiert. Anschließend wird die Funktion "coxph" nach einer Reihe von Datenverarbeitungsaufgaben ausgeführt. Die Details der Funktion "coxph" für Lebensdaueranalysen können der R-Dokumentation entnommen werden. Im Prognoseschritt wird eine Testinstanz in das trainierte Modell mit der Funktion "surfit" ein gespeist und die Lebensdauerkurve für diese Testinstanz wird als Variable "curve" erstellt. Schließlich erhalten Sie die Wahrscheinlichkeit des erwarteten Zeitpunkts. 

###Experimentablauf:

![experiment flow][1]

####Modul 1:

    #data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1"
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)
    maml.mapOutputPort("sampleInput"); #send data to output port
	
####Modul 2:

    #read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")
    # preprocessing trainingdata
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)
    # preprocessing testingdata
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))
    # preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types
    # necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)
    # prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # based on user input, find the event occurance probablity
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }
    #pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##Einschränkungen

Dieser Webdienst kann nur numerische Werte als Funktionsvariablen (Spalten) enthalten. Die Spalte "event" kann nur den Wert 0 oder 1 akzeptieren. Der Wert der Spalte "time" muss eine positive ganze Zahl sein.

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts oder Veröffentlichung im Marketplace finden Sie [hier](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png
