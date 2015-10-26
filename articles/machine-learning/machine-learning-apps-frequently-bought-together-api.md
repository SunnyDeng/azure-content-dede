<properties 
	pageTitle="Machine Learning-Beispiel-App: Häufig zusammen gekauft | Microsoft Azure" 
	description="Dieser Machine Learning-Webdienst führt eine Online-Shopping-Warenkorbanalyse durch, um Produktempfehlungen von häufig gekauften Elementen basierend auf dem vom Benutzer bereitgestellten Transaktionsverlauf zu erstellen." 
	services="machine-learning" 
	documentationCenter="" 
	authors="CoromT" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="luisca"/>

# Machine Learning-Beispiel-App: Häufig zusammen gekauft


## WICHTIG: DIESER DIENST WURDE ALS VERALTET MARKIERT

Die Funktionalität „Häufig zusammen gekauft“, die dieser Dienst bietet, wurde jetzt in unsere umfassendere [Empfehlungen-API](http://gallery.azureml.net/MachineLearningAPI/3574432384684cac9cc766e57729ea4c) integriert. Wir empfehlen Ihnen, statt dieses Dienstes diese Empfehlungen zu verwenden.

##Übersicht

Der Webdienst [Häufig zusammen gekauft](https://datamarket.azure.com/dataset/amla/mba) in Machine Learning führt Analysen von Onlineeinkaufskörben durch, um Produktempfehlungen für häufig zusammen gekaufte Artikel aus historischen Transaktionen abgeben zu können. Empfehlungen für häufig zusammen gekaufte Artikel helfen Kunden bei der Identifikation von Produkten in einem Katalog, die beim Kauf eines bestimmten Produkts besonders relevant sind. Das Anzeigen dieser Empfehlungen an gut sichtbarer Stelle hat sich als effektiv zur Verbesserung der Umsätze bei Onlinehändlern erwiesen.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]
  
##Erste Schritte 

Nachdem Sie den Webdienst "Häufig zusammen gekauft" abonniert haben, können Sie mithilfe der [Market Basket Analysis Service-Beispiel-Webanwendung](https://marketbasket.cloudapp.net/) auf einfache Weise Daten in ein Modell hochladen und häufig gekaufte Produktgruppen entdecken. Zum Verwenden der Anwendung oder der API benötigen Sie zunächst Ihren API-Schlüssel, den Sie auf Ihrer [Azure Data Market-Kontoseite](https://datamarket.azure.com/account) finden.

##Nutzung des Webdiensts 

Dieser Dienst enthält APIs zum Erstellen von Modellen für "Häufig zusammen gekauft", zum Hochladen historischer Transaktionen und zum Abrufen der bestplatzierten häufig zusammen gekauften Produkte für ein bestimmtes Produkt. Beispiele, die veranschaulichen, wie Sie diese APIs verwenden, finden Sie im [Azure-MachineLearning-DataScience](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether)-Archiv auf GitHub.

 

<!---HONumber=Oct15_HO3-->