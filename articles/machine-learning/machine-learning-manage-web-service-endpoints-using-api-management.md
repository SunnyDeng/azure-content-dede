<properties
	pageTitle="Erfahren Sie, wie Sie Azure ML-Webdienste mithilfe von API Management verwalten | Microsoft Azure"
	description="Diese Leitfaden zeigt, wie Sie Azure ML-Webdienste mithilfe von API Management verwalten."
	keywords="machine learning,api management"
	services="machine-learning"
	documentationCenter=""
	authors="roalexan"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="roalexan" />


# Erfahren Sie, wie Sie Azure ML-Webdienste mithilfe von API Management verwalten

##Übersicht

Dieser Leitfaden beschreibt die ersten Schritte mit API Management zur Verwaltung Ihrer Azure ML-Webdienste.

##Was ist Azure API Management?

Azure API Management ist ein Azure-Dienst, mit dem Sie Ihre REST-API-Endpunkte verwalten können, indem Sie Benutzerzugriff, Nutzungseinschränkungen und Dashboardüberwachung definieren. Klicken Sie [hier](http://azure.microsoft.com/services/api-management/), um Informationen zu Azure API Management zu erhalten. Klicken Sie [hier](api-management/api-management-get-started.md), um eine Anleitung zum Einstieg in Azure API Management zu erhalten. Dieser Leitfaden (auf dem der vorliegende Leitfaden basiert) behandelt weitere Themen, z. B. Benachrichtigungskonfiguration, Tarife, Antwortverarbeitung, Benutzerauthentifizierung, Produkterstellung, Entwicklerabonnements und Nutzungsdashboards.

##Was ist Azure ML?

Azure ML ist ein Azure-Dienst für Machine Learning, mit dem Sie erweiterte Analyselösungen ganz einfach entwickeln, bereitstellen und freigeben können. Klicken Sie [hier](http://azure.microsoft.com/services/machine-learning/), um Informationen zu Azure ML zu erhalten.

##Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

* Ein Azure-Konto. Wenn Sie kein Azure-Konto haben, klicken Sie [hier](http://azure.microsoft.com/pricing/free-trial/), um zu erfahren, wie Sie ein kostenloses Testkonto erstellen.
* Ein Azure ML-Konto. Wenn Sie kein Azure ML-Konto haben, klicken Sie [hier](https://studio.azureml.net/), um zu erfahren, wie Sie ein kostenloses Testkonto erstellen.
* Den Arbeitsbereich, den Dienst und den API-Schlüssel für ein als Webdienst veröffentlichtes Azure ML-Experiment. Klicken Sie [hier](machine-learning/machine-learning-create-experiment.md), um Informationen zum Erstellen eines Azure ML-Experiments zu erhalten. Klicken Sie [hier](machine-learning/machine-learning-publish-a-machine-learning-web-service.md), um Informationen zum Veröffentlichen eines Azure ML-Experiments als Webdienst zu erhalten. Alternativ dazu enthält Anhang A Anweisungen zum Erstellen und Testen eines einfachen Azure ML-Experiments und zum Veröffentlichen dieses Experiments als Webdienst.

##Erstellen einer API Management-Instanz

Im Folgenden finden Sie die Schritte zur Verwendung von API Management zur Verwaltung Ihres Azure ML-Webdiensts. Erstellen Sie zuerst eine Instanz des Diensts. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com/) an, und klicken Sie auf **Neu** > **App Services** > **API Management** > **Erstellen**.

![create-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

Geben Sie eine eindeutige **URL** ein. In diesem Leitfaden wird **demoazureml** verwendet – Sie müssen etwas anderes eingeben. Wählen Sie die gewünschten Werte für **Abonnement** und **Region** für Ihre Dienstinstanz aus. Treffen Sie Ihre Auswahl und klicken Sie auf Weiter.

![create-service-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

Geben Sie einen Wert für den **Organisationsnamen** ein. In diesem Leitfaden wird **demoazureml** verwendet – Sie müssen etwas anderes eingeben. Geben Sie Ihre E-Mail-Adresse in das Feld **Administrator-E-Mail** ein. Diese E-Mail-Adresse wird für Benachrichtigungen vom API Management-System verwendet.

![create-service-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

Aktivieren Sie das Kontrollkästchen, um Ihre Dienstinstanz zu erstellen. *Die Erstellung des neuen Diensts dauert bis zu 30 Minuten.*

##Erstellen der API

Nach der Erstellung der Dienstinstanz können Sie die API erstellen. Eine API besteht aus einem Satz von Vorgängen, die aus Clientanwendungen aufgerufen werden können. API-Operationen funktionieren als Proxys für vorhandene Webdienste. In diesem Leitfaden werden APIs erstellt, die per Proxy an die vorhandenen Azure ML-Webdienste "RRS" und "BES" weiterleiten.

APIs werden im API-Herausgeberportal erstellt und konfiguriert, das über das Azure-Verwaltungsportal erreichbar ist. Um auf das Herausgeberportal zuzugreifen, wählen Sie Ihre Dienstinstanz aus.

![select-service-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

Klicken Sie im Azure-Portal für Ihren API Management-Dienst auf **Verwalten**.

![manage-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

Klicken Sie auf der linken Seite im Menü **API Management** auf **APIs**, und klicken Sie anschließend auf **API hinzufügen**.

![api-management-menu](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

Geben Sie **Azure ML-Demo-API** als **Web-API-Namen** ein. Geben Sie **https://ussouthcentral.services.azureml.net** als **Webdienst-URL** ein. Geben Sie **azureml-demo** als **Web-API-URL-Suffix** ein. Aktivieren Sie **HTTPS** als **Web-API-URL**-Schema. Wählen Sie **Starter** als **Produkte** aus. Wenn Sie fertig sind, klicken Sie auf **Speichern**, um die API zu erstellen.

![add-new-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##Hinzufügen der Vorgänge

Klicken Sie auf **Vorgang hinzufügen**, um Vorgänge zu dieser API hinzuzufügen.

![add-operation](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

Das Fenster **Neue Operation** wird angezeigt, und die Registerkarte **Signatur** ist standardmäßig ausgewählt.

##Hinzufügen eines RRS-Vorgangs

Erstellen Sie zunächst einen Vorgang für den Azure ML-RRS-Dienst. Wählen Sie **POST** als **HTTP-Verb** aus. Geben Sie **/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}** als **URL-Vorlage** ein. Geben Sie **RRS Execute** als **Anzeigenamen** ein.

![add-rrs-operation-signature](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Klicken Sie links auf **Antworten** > **HINZUFÜGEN**, und wählen Sie **200 OK** aus. Klicken Sie auf **Speichern**, um diesen Vorgang zu speichern.

![add-rrs-operation-response](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##Hinzufügen von BES-Vorgängen

Für die BES-Vorgänge werden keine Screenshots bereitgestellt, da die Vorgehensweise dem Hinzufügen des RRS-Vorgangs sehr ähnlich ist.

###Senden eines Batchausführungsauftrags (ohne ihn jedoch zu starten)

Klicken Sie auf **Vorgang hinzufügen**, um der API den Azure ML-BES-Vorgang hinzuzufügen. Wählen Sie **POST** als **HTTP-Verb** aus. Geben Sie **/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}** als **URL-Vorlage** ein. Geben Sie **BES Submit** als **Anzeigenamen** ein. Klicken Sie links auf **Antworten** > **HINZUFÜGEN**, und wählen Sie **200 OK** aus. Klicken Sie auf **Speichern**, um diesen Vorgang zu speichern.

###Starten eines Batchausführungsauftrags

Klicken Sie auf **Vorgang hinzufügen**, um der API den Azure ML-BES-Vorgang hinzuzufügen. Wählen Sie **POST** als **HTTP-Verb** aus. Geben Sie **/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}** als **URL-Vorlage** ein. Geben Sie **BES Start** als **Anzeigenamen** ein. Klicken Sie links auf **Antworten** > **HINZUFÜGEN**, und wählen Sie **200 OK** aus. Klicken Sie auf **Speichern**, um diesen Vorgang zu speichern.

###Abrufen des Status oder des Ergebnisses eines Batchausführungsauftrags

Klicken Sie auf **Vorgang hinzufügen**, um der API den Azure ML-BES-Vorgang hinzuzufügen. Wählen Sie **GET** als **HTTP-Verb** aus. Geben Sie **/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}** als **URL-Vorlage** ein. Geben Sie **BES Status** als **Anzeigenamen** ein. Klicken Sie links auf **Antworten** > **HINZUFÜGEN**, und wählen Sie **200 OK** aus. Klicken Sie auf **Speichern**, um diesen Vorgang zu speichern.

###Löschen eines Batchausführungsauftrags

Klicken Sie auf **Vorgang hinzufügen**, um der API den Azure ML-BES-Vorgang hinzuzufügen. Wählen Sie **DELETE** als **HTTP-Verb** aus. Geben Sie **/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}** als **URL-Vorlage** ein. Geben Sie **BES Delete** als **Anzeigenamen** ein. Klicken Sie links auf **Antworten** > **HINZUFÜGEN**, und wählen Sie **200 OK** aus. Klicken Sie auf **Speichern**, um diesen Vorgang zu speichern.

##Aufrufen einer Operation aus dem Entwicklerportal

Operationen können direkt aus dem Entwicklerportal aufgerufen werden. Dies ist ein einfacher Weg, um die Operationen einer API anzuzeigen und zu testen. In diesem Schritt rufen Sie die **RRS Execute**-Methode auf, die der **Azure ML-Demo-API** hinzugefügt wurde. Klicken Sie auf **Entwicklerportal** im Menü oben rechts im Verwaltungsportal.

![developer-portal](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

Klicken Sie im Hauptmenü auf **APIs** und anschließend auf **Azure ML-Demo-API**, um die verfügbaren Vorgänge anzuzeigen.

![demoazureml-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Wählen Sie als Vorgang **RRS Execute** aus. Klicken Sie auf **Testen**.

![try-it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

Geben Sie als Anforderungsparameter Ihren **Arbeitsbereich** und **Dienst** sowie **2.0** als **API-Version** und **true** für die **Details** ein. Sie finden Ihren **Arbeitsbereich** und **Dienst** im Azure ML-Webdienstdashboard (siehe **Testen des Webdiensts** in Anhang A).

Um den Anforderungsheader festzulegen, klicken Sie auf **Header hinzufügen**, und geben Sie **Content-Type** und **application/json** ein. Klicken Sie anschließend auf **Header hinzufügen**, und geben Sie **Authorization** und **Bearer<YOUR AZUREML SERVICE API-KEY>** ein. Sie finden Ihren **API-Schlüssel** im Azure ML-Webdienstdashboard (siehe **Testen des Webdiensts** in Anhang A).

Geben Sie **{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}** als Anforderungstext ein.

![azureml-demo-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Klicken Sie auf **Send**.

![send](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

Nach dem Aufruf der Operation zeigt das Entwicklerportal die **Angeforderte URL** vom Back-End-Dienst, den **Antwortstatus**, die **Antwortheader** sowie den **Antwortinhalt** an.

![response-status](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##Anhang A – Erstellen und Testen eines einfachen Azure ML-Webdiensts

###Erstellen des Experiments

Im Folgenden finden Sie die Schritte, die zum Erstellen eines einfachen Azure ML-Experiments und Veröffentlichen des Experiments als Webdienst erforderlich sind. Der Webdienst akzeptiert als Eingabe eine Spalte mit beliebigem Text und gibt einen Satz von Features zurück, die als Ganzzahlen dargestellt werden. Beispiel:

Text | Text im Hashformat
--- | ---
This is a good day | 1 1 2 2 0 2 0 1

Navigieren Sie in einem Browser Ihrer Wahl zu dieser Adresse: [https://studio.azureml.net/](https://studio.azureml.net/). Geben Sie dort Ihre Anmeldeinformationen ein, um sich anzumelden. Erstellen Sie als Nächstes ein neues Experiment

![search-experiment-templates](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Benennen Sie es um in: **SimpleFeatureHashingExperiment**. Erweitern Sie **Saved Datasets**, und ziehen Sie **Book Reviews from Amazon** in Ihr Experiment.

![simple-feature-hashing-experiment](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Erweitern Sie **Data Transformation** und **Manipulation**, und ziehen Sie **Project Columns** in Ihr Experiment. Verbinden Sie **Book Reviews from Amazon** mit **Project Columns**.

![project-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

Klicken Sie auf **Project Columns**. Klicken Sie dann auf **Launch column selector**, und wählen Sie **Col2** aus. Klicken Sie auf das Häkchen, um diese Änderungen zu übernehmen.

![select-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

Erweitern Sie **Text Analytics**, und ziehen Sie **Feature Hashing** in das Experiment. Verbinden Sie **Project Columns** mit **Feature Hashing**.

![connect-project-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Geben Sie **3** als **Hashing bitsize** ein. Dadurch werden 8 (23) Spalten erstellt.

![hashing-bitsize](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

An diesem Punkt können Sie auf **Run** klicken, um das Experiment zu testen.

![run](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###Erstellen eines Webdiensts

Nun erstellen Sie einen Webdienst. Erweitern Sie **Web Service**, und ziehen Sie **Input** in das Experiment. Verbinden Sie **Input** mit **Feature Hashing**. Ziehen Sie auch **Output** in Ihr Experiment. Verbinden Sie **Output** mit **Feature Hashing**.

![output-to-feature-hashing](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klicken Sie auf **Publish web service**.

![publish-web-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klicken Sie auf **Yes**, um das Experiment zu veröffentlichen.

![yes-to-publish](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###Testen des Webdiensts

Ein Azure ML-Webdienst besteht aus RRS-Endpunkten (Request/Response Service, Anforderungs-/Antwortdienst) und BES-Endpunkten (Batch Execution Service, Batchausführungsdienst). RRS dient zur synchronen Ausführung. BES dient zur asynchronen Auftragsausführung. Um Ihren Webdienst mit dem unten aufgeführten Python-Beispielcode zu testen, müssen Sie möglicherweise das Azure-SDK für Python herunterladen und installieren (siehe [Installieren von Python und SDK](python-how-to-install.md)).

Sie benötigen auch den **Arbeitsbereich**, den **Dienst** und den **API-Schlüssel** Ihres Experiments für den unten stehenden Beispielcode. Sie finden den Arbeitsbereich und den Dienst, indem Sie im Webdienstdashboard für Ihr Experiment entweder auf **Anforderung/Antwort** oder auf **Batchausführung** klicken.

![find-workspace-and-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Den **API-Schlüssel** finden Sie, indem Sie im Webdienstdashboard auf Ihr Experiment klicken.

![find-api-key](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####Testen des RRS-Endpunkts

#####Schaltfläche "Test"

Um den RRS-Endpunkt zu testen, können Sie ganz einfach auf dem Webdienstdashboard auf **Test** klicken.

![test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

Geben Sie für **col2** den Text **This is a good day** ein. Klicken Sie auf das Häkchen.

![enter-data](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

Die Ausgabe sollte folgendermaßen aussehen:

![sample-output](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####Beispielcode

Sie können Ihren RRS-Endpunkt auch mithilfe des Clientcodes testen. Wenn Sie auf dem Dashboard auf **Anforderung/Antwort** klicken und ganz nach unten scrollen, sehen Sie Beispielcode für C#, Python und R. Sie sehen auch die Syntax der RRS-Anforderung einschließlich URI, Header und Text der Anforderung.

Dieser Leitfaden zeigt ein funktionierendes Python-Beispiel. Sie müssen dieses Beispiel mit dem **Arbeitsbereich**, dem **Dienst** und dem **API-Schlüssel** Ihres Experiments bearbeiten.

	import urllib2
	import json
	workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
	service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
	api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
	data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
	}
	url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
	headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	body = str.encode(json.dumps(data))
	try:
    	req = urllib2.Request(url, body, headers)
    	response = urllib2.urlopen(req)
    	result = response.read()
    	print "result:" + result
			except urllib2.HTTPError, error:
    	print("The request failed with status code: " + str(error.code))
    	print(error.info())
    	print(json.loads(error.read()))

####Testen des BES-Endpunkts
Klicken Sie auf dem Dashboard auf **Batchausführung**, und scrollen Sie bis zum Ende. Sie sehen Beispielcode für C#, Python und R. Sie sehen auch die Syntax der BES-Anforderungen zum Übermitteln, Starten, Abrufen des Status und Löschen eines Auftrags.

Dieser Leitfaden zeigt ein funktionierendes Python-Beispiel. Sie müssen dieses Beispiel mit dem **Arbeitsbereich**, dem **Dienst** und dem **API-Schlüssel** Ihres Experiments bearbeiten. Darüber hinaus müssen Sie den **Speicherkontonamen**, den **Speicherkontoschlüssel** und den **Speichercontainernamen** ändern. Und schließlich müssen Sie den Speicherort der **Eingabedatei** und den Speicherort der **Ausgabedatei** bearbeiten.

	import urllib2
	import json
	import time
	from azure.storage import *
	workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
	service = "<REPLACE WITH YOUR SERVICE ID>"
	api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
	storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
	storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
	storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
	input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\mydata.csv
	output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\myresults.csv
	input_blob_name = "mydatablob.csv"
	output_blob_name = "myresultsblob.csv"
	def printHttpError(httpError):
	print("The request failed with status code: " + str(httpError.code))
	print(httpError.info())
	print(json.loads(httpError.read()))
	return
	def saveBlobToFile(blobUrl, resultsLabel):
	print("Reading the result from " + blobUrl)
	try:
		response = urllib2.urlopen(blobUrl)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	with open(output_file, "w+") as f:
		f.write(response.read())
	print(resultsLabel + " have been written to the file " + output_file)
	return
	def processResults(result):
	first = True
	results = result["Results"]
	for outputName in results:
		result_blob_location = results[outputName]
		sas_token = result_blob_location["SasBlobToken"]
		base_url = result_blob_location["BaseLocation"]
		relative_url = result_blob_location["RelativeLocation"]
		print("The results for " + outputName + " are available at the following Azure Storage location:")
		print("BaseLocation: " + base_url)
		print("RelativeLocation: " + relative_url)
		print("SasBlobToken: " + sas_token)
		if (first):
			first = False
			url3 = base_url + relative_url + sas_token
			saveBlobToFile(url3, "The results for " + outputName)
	return

	def invokeBatchExecutionService():
	url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
	blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
	print("Uploading the input to blob storage...")
	data_to_upload = open(input_file, "r").read()
	blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
	print "Uploaded the input to blob storage"
	input_blob_path = "/" + storage_container_name + "/" + input_blob_name
	connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
	payload =  {
		"Input": {
			"ConnectionString": connection_string,
			"RelativeLocation": input_blob_path
		},
		"Outputs": {
			"output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
		},
		"GlobalParameters": {
		}
	}
		body = str.encode(json.dumps(payload))
	headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
	print("Submitting the job...")
	# submit the job
	req = urllib2.Request(url + "?api-version=2.0", body, headers)
	try:
		response = urllib2.urlopen(req)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	result = response.read()
	job_id = result[1:-1] # remove the enclosing double-quotes
	print("Job ID: " + job_id)
	# start the job
	print("Starting the job...")
	req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
	try:
		response = urllib2.urlopen(req)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	url2 = url + "/" + job_id + "?api-version=2.0"

	while True:
		print("Checking the job status...")
		# If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
		req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
		try:
			response = urllib2.urlopen(req)
		except urllib2.HTTPError, error:
			printHttpError(error)
			return
		result = json.loads(response.read())
		status = result["StatusCode"]
		print "status:" + status
		if (status == 0 or status == "NotStarted"):
			print("Job " + job_id + " not yet started...")
		elif (status == 1 or status == "Running"):
			print("Job " + job_id + " running...")
		elif (status == 2 or status == "Failed"):
			print("Job " + job_id + " failed!")
			print("Error details: " + result["Details"])
			break
		elif (status == 3 or status == "Cancelled"):
			print("Job " + job_id + " cancelled!")
			break
		elif (status == 4 or status == "Finished"):
			print("Job " + job_id + " finished!")
			processResults(result)
			break
		time.sleep(1) # wait one second
	return
	invokeBatchExecutionService()

<!---HONumber=July15_HO4-->