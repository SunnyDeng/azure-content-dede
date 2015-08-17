<properties 
   pageTitle="Warten-Connector" 
   description="Warten-Connector" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/29/2015"
   ms.author="rajram"/>

#Warten-Connector
Der Warten-Connector ermöglicht einer Anwendung, die Ausführung für einen bestimmten Zeitraum oder bis zum Eintreten einer angegebenen Uhrzeit zu verzögern. Bei Verwendung in einem Workflow kann dieser Connector zum Verzögern der Ausführung verwendet werden.

##Verwenden des Warten-Connectors
Zur Verwendung des Warten-Connectors müssen Sie zunächst eine Instanz der Warten-Connector-API-App erstellen. Dies kann entweder inline beim Erstellen einer Logik-App oder durch Auswählen der Warten-Connector-API-App im Azure Marketplace erfolgen.

##Verwenden des Warten-Connectors auf der Oberfläche des Logik-App-Designers
Der Warten-Connector kann als eine Aktion verwendet werden. Er besitzt keine Trigger.

###Aktion
- Klicken Sie im rechten Bereich auf den Warten-Connector.

	![Liste der Aktionen][1]
- Der Warten-Connector unterstützt zwei Aktionen: 
	- Verzögern
	- Verzögern bis
	 
- Wählen Sie *Verzögern* aus.

	![Eingabe verzögern][2]
- Nehmen Sie die Eingaben für die Aktion vor, und konfigurieren Sie sie.

	![Konfigurierte Aktion][3]

Parameter|Typ|Beschreibung des Parameters
---|---|---
Dauer In Minuten|Ganze Zahl|Dauer der Verzögerung in Minuten


## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [API-Apps und Connector verwalten und überwachen](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=August15_HO6-->