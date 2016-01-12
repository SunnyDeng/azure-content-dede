<properties
   pageTitle="Publish-WebApplicationWebSite (Windows PowerShell-Skript)) | Microsoft Azure"
   description="Informationen zum Veröffentlichen eines Webprojekts auf einer Azure-Website. Dieses Skript erstellt die erforderlichen Ressourcen in Ihrem Azure-Abonnement, sofern noch nicht vorhanden."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/17/2015"
   ms.author="tarcher" />

# Publish-WebApplicationWebSite (Windows PowerShell-Skript)

##Syntax

Veröffentlicht ein Webprojekt auf einer Azure-Website. Das Skript erstellt die erforderlichen Ressourcen in Ihrem Azure-Abonnement, wenn sie noch nicht vorhanden sind.

	Publish-WebApplicationWebSite
	–Configuration <configuration>
	-SubscriptionName <subscriptionName>
	-WebDeployPackage <packageName>
	-DatabaseServerPassword @{Name = "name"; Password = "password"}
	-SendHostMessagesToOutput
	-Verbose


## Konfiguration

Der Pfad zur JSON-Konfigurationsdatei, in der die Details der Bereitstellung beschrieben sind.

|Parameter|Standardwert|
|---|---|
|Aliase|(Keine)|
|Erforderlich|true|
|Position|benannt|
|Standardwert|(Keine)|
|Pipelineeingabe akzeptieren?|false|
|Platzhalterzeichen akzeptieren?|false|

## SubscriptionName

Der Name des Azure-Abonnements, in dem Sie die Website erstellen möchten.

|Parameter|Standardwert|
|---|---|
|Aliase|(Keine)|
|Erforderlich|false|
|Position|benannt|
|Standardwert|(Keine)|
|Pipelineeingabe akzeptieren?|false|
|Platzhalterzeichen akzeptieren?|false|

## WebDeployPackage

Der Pfad zum Webbereitstellungspaket für die Veröffentlichung auf der Website. Sie können dieses Paket in Visual Studio mithilfe des Assistenten "Web veröffentlichen" erstellen. Weitere Informationen finden Sie unter [Erste Schritte mit Azure-Clouddiensten und ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

|Parameter|Standardwert|
|---|---|
|Aliase|(Keine)|
|Erforderlich|false|
|Position|benannt|
|Standardwert|(Keine)|
|Pipelineeingabe akzeptieren?|false|
|Platzhalterzeichen akzeptieren?|false|

## DatabaseServerPassword

Der Benutzername und das Kennwort für die SQL-Datenbank in Azure.

|Parameter|Standardwert|
|---|---|
|Aliase|(Keine)|
|Erforderlich|false|
|Position|benannt|
|Standardwert|(Keine)|
|Pipelineeingabe akzeptieren?|false|
|Platzhalterzeichen akzeptieren?|false|

## SendHostMessagesToOutput

Falls "true", werden Nachrichten vom Skript in den Ausgabedatenstrom ausgegeben.

|Parameter|Standardwert|
|---|---|
|Aliase|(Keine)|
|Erforderlich|false|
|Position|benannt|
|Standardwert|false|
|Pipelineeingabe akzeptieren?|false|
|Platzhalterzeichen akzeptieren?|false|

## Hinweise

Eine ausführliche Erläuterung der Verwendung des Skripts zum Erstellen von Entwicklungs- und Testumgebungen finden Sie unter [Verwenden von Windows PowerShell-Skripts zum Veröffentlichen in Entwicklungs- und Testumgebungen](vs-azure-tools-publishing-using-powershell-scripts.md).

In der JSON-Konfigurationsdatei sind die Details angegeben, was bereitgestellt werden muss. Dazu zählen die Informationen, die Sie beim Erstellen des Projekts angegeben haben, z. B. den Namen und Benutzernamen für die Website. Sie umfassen auch die bereitzustellende Datenbank, sofern vorhanden. Der folgende Code zeigt ein Beispiel einer JSON-Konfigurationsdatei:

	{
	    "environmentSettings": {
	        "webSite": {
	            "name": "WebApplication10554",
	            "location": "West US"
	        },
	        "databases": [
	            {
	                "connectionStringName": "DefaultConnection",
	                "databaseName": "WebApplication10554_db",
	                "serverName": "iss00brc88",
	                "user": "sqluser2",
	                "password": "",
	                "edition": "",
	                "size": "",
	                "collation": "",
	                "location": "West US"
	            }
	        ]
	    }
	}

Sie können die JSON-Konfigurationsdatei bearbeiten, um den Umfang der Bereitstellung zu ändern. Der Abschnitt "Website" ist erforderlich, der Abschnitt "Datenbank" optional.

## Nächste Schritte

Weitere Informationen finden Sie unter [Publish-WebApplicationWebSite (Windows PowerShell-Skript)](vs-azure-tools-publish-webapplicationvm.md).

<!---HONumber=AcomDC_1223_2015-->