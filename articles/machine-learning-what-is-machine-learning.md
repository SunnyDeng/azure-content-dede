<properties 
    pageTitle="1. Was ist Microsoft Azure Machine Learning? | Microsoft Azure" 
    description="Übersicht über den Azure Machine Learning-Dienst" 
    services="machine-learning" 
    documentationCenter="" 
    authors="tedway" 
    manager="neerajkh" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/22/2015" 
    ms.author="tedway;olgali"/>


# Was ist maschinelles Lernen?
Überall im Alltag kommt maschinelles Lernen zum Einsatz. Wenn Sie online einkaufen, trägt maschinelles Lernen dazu bei, dass Ihnen anhand der gekauften Produkte weitere Produkte empfohlen werden. Wenn Ihre Kreditkarte verwendet wird, hilft maschinelles Lernen der Bank, eine Betrugserkennung durchzuführen und Sie zu benachrichtigen, wenn die Transaktion verdächtig erscheint. Maschinelles Lernen lässt sich definieren als Prozess zur Erstellung von Modellen, um aus vorhandenen Daten zu lernen und Vorhersageanalysen für zukünftige Daten anzustellen.

## Was ist Azure Machine Learning?
Azure Machine Learning ist ein leistungsfähiger cloudbasierter Vorhersageanalysedienst, der die schnelle Erstellung von Analyselösungen ermöglicht. Wir erstellten Azure Machine Learning, um das maschinelle Lernen allen zugänglich zu machen: Wir eliminierten die Komplexität der Erstellung und Bereitstellung der Technologie für das maschinelle Lernen, damit es jeder nutzen kann. Es ist ein vollständig verwalteter Dienst. Das bedeutet, dass Sie weder Hardware kaufen noch virtuelle Computer manuell verwalten müssen.

Sie können das browser-basierte Tool [Machine Learning Studio](machine-learning-what-is-ml-studio.md) verwenden, um schnell Workflows für das maschinelle Lernen zu erstellen und zu automatisieren. Hunderte von vorhandenen [Machine Learning-Bibliotheken](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) können per Drag & Drop zur Entwicklung von Vorhersageanalyselösungen genutzt werden, und anschließend können Sie bei Bedarf Ihre eigenen benutzerdefinierten [R](machine-learning-r-quickstart.md)- und [Python](machine-learning-execute-python-scripts.md)- Skripts hinzufügen, um die Lösungen zu erweitern. Studio funktioniert in jedem Browser und ermöglicht Ihnen die schnelle Entwicklung und Iteration von Lösungen. ![Vorhersageanalyse-Experimente in der Cloud mit Azure Machine Learning Studio](./media/machine-learning-what-is-machine-learning/AzureMLStudio.png)

Sie können problemlos [Webdienste](machine-learning-publish-a-machine-learning-web-service.md) ermitteln und erstellen, [die Modelle über APIs trainieren und neu trainieren](machine-learning-retrain-models-programmatically.md), [Endpunkte verwalten](machine-learning-create-endpoint.md) und [Webdienste für einzelne Kunden skalieren](machine-learning-scaling-endpoints.md). Zudem können Sie die Diagnosefunktionen für das Überwachen und Debuggen des Diensts konfigurieren. Folgende Features sind ganz neu:

- Die Fähigkeit, ein konfigurierbares Modul für benutzerdefiniertes R zu erstellen, eigene R-Skripts für Training/Vorhersage zu integrieren und Python-Skripts mit einem riesigen Ökosystem von Bibliotheken, z. B. numpy, scipy, pandas oder scikit-learn, hinzuzufügen. Sie können jetzt mit Terabyte von Daten unter Verwendung von [Learning with Counts][learning-with-counts] trainieren, PCA oder eine SVM mit einer Klasse zum Erkennen von Anomalien verwenden und Daten unter Verwendung des vertrauten vertrauten SQLite problemlos ändern, filtern und bereinigen. 
- [Machine Learning Community Gallery](machine-learning-gallery-how-to-use-contribute-publish.md) bietet Ihnen die Möglichkeit, interessante Experimente, die von anderen Benutzern erstellt wurden, zu entdecken und zu verwenden. In der [Galerie](http://gallery.azureml.net) können Sie Fragen stellen oder Anmerkungen zu Experimenten oder Ihre eigenen Experimente veröffentlichen. Sie können Links zu interessanten Experimenten über soziale Kanäle, z. B. LinkedIn und Twitter, freigeben. Die Galerie ist eine hervorragende Möglichkeit für Benutzer, in Azure Machine Learning einzusteigen und von anderen in der Community zu lernen. ![Probieren Sie Beispiele für Vorhersageanalyse-Experimente aus, oder ergänzen Sie die Azure Machine Learning-Galerie durch eigene Experimente](./media/machine-learning-what-is-machine-learning/AzureMLGallery.png)
- Sie können über ein Azure-Abonnement [Marketplace-Apps](https://datamarket.azure.com/browse?query=machine+learning) erwerben und fertiggestellte Webdienste für Empfehlungen, Textanalysen und die Erkennung von Anomalien direkt aus dem Azure Marketplace nutzen. 
- Eine schrittweise Anleitung für den Weg von Rohdaten zu einem nutzbaren Webdienst, um der cloudbasierten Data Science den Weg zu ebnen. Zudem ist es nun möglich, gängige Tools, z. B. iPython Notebook und Python-Tools für Visual Studio, in Verbindung mit Azure  Machine Learning zu verwenden.

## Erste Schritte
Die Grundlagen der Vorhersageanalyse und des maschinellen Lernens werden anhand eines [schrittweises Lernprogramms](machine-learning-create-experiment.md) und auf der [Grundlage von Beispielen](machine-learning-sample-experiments.md) vermittelt. Für erste Schritte mit Machine Learning ist weder ein Azure-Abonnement noch eine Kreditkarte erforderlich, wenn Sie Experimente in Studio ausprobieren.


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!--HONumber=54-->