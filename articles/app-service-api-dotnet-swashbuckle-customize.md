
<properties 
	pageTitle="Anpassen von mit Swashbuckle generierten Vorgangs-IDs" 
	description="Erfahren Sie, wie Sie Swagger-Vorgangs-IDs anpassen können, die von Swashbuckle für eine API-App in Azure App Service generiert werden." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="bradyg"/>

# Anpassen von mit Swashbuckle generierten Vorgangs-IDs 

## Übersicht

Swashbuckle generiert Swagger-Vorgangs-ID, indem Controllername und Methodennamen miteinander verbunden werden. Aufgrund dieses Musters kommt es zu einem Problem, wenn Sie mehrere Überladungen einer Methode haben: Swashbuckle generiert doppelte Vorgang-IDs, was eine ungültige Swagger-JSON ist.

Bei dem folgenden Controllercode generiert Swashbuckle beispielsweise drei "Contact_Get"-Vorgang-IDs.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Sie können das Problem manuell beheben, indem Sie den Methoden eindeutige Namen geben, wie z. B. in dem die folgenden Beispiel:

* Get
* GetById
* GetPage

Die Alternative besteht darin, Swashbuckle zu erweitern, damit es automatisch eindeutige Vorgangs-IDs generiert. Dieser Artikel beschreibt, wie Sie dies tun können.

## Erweitern von Swashbuckle 

Die folgenden Schritte zeigen, wie Sie Swashbuckle mithilfe der Datei *SwaggerConfig.cs* anpassen können, die mit der Visual Studio-API-Apps-Vorschauprojektvorlage in das Projekt eingeschlossen wird. Sie können Swashbuckle auch in einem Web-API-Projekt anpassen, das Sie für die Bereitstellung als API-App konfigurieren.

1. Erstellen einer benutzerdefinierten `IOperationFilter`-Implementierung 

	Die `IOperationFilter`-Schnittstelle bietet einen Erweiterungspunkt für Swashbuckle-Benutzer, die verschiedene Aspekte des Swagger-Metadatenprozesses anpassen möchten. Der folgende Code veranschaulicht eine Methode zum Ändern des Verhaltens zur Vorgangs-ID-Generation. Der Code fügt Parameternamen an den Namen der Vorgangs-ID an.

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. Rufen Sie in der Datei *App_Start\SwaggerConfig.cs* die `OperationFilter`-Methode auf, damit Swashbuckle die neue `IOperationFilter`-Implementierung verwendet.

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Die Datei *SwaggerConfig.cs*, die vom Swashbuckle NuGet-Paket abgelegt wird, enthält zahlreiche auskommentierte Beispiele für Erweiterungspunkte. Die zusätzlichen Kommentare werden hier nicht angezeigt.

	Nach dieser Änderung wird die `IOperationFilter`-Implementierung verwendet und bewirkt, dass eindeutige Vorgangs-IDs generiert werden.
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

## Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Swashbuckle anpassen können, um eindeutige Vorgangs-IDs zu generieren. Weitere Informationen finden Sie unter [Swashbuckle auf GitHub](https://github.com/domaindrivendev/Swashbuckle).

<!---HONumber=58-->