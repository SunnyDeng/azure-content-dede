<properties 
	pageTitle="Daten-Factory – Benennungsregeln | Microsoft Azure" 
	description="Beschreibt die Benennungsregeln für Data Factory-Entitäten." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015" 
	ms.author="spelluru"/>

# Azure Data Factory – Benennungsregeln 
Die folgende Tabelle enthält Benennungsregeln für Data Factory-Artefakte.



Name | Eindeutigkeit des Namens | Überprüfungen
:--- | :-------------- | :----------------
Data Factory | Für Microsoft Azure eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt, sodass mit „MyDF“ und „mydf“ auf dieselbe Data Factory verwiesen wird. |<ul><li>Jede Data Factory ist mit genau einem Azure-Abonnement verknüpft.</li><li>Objektnamen müssen mit einem Buchstaben oder einer Zahl beginnen und können nur Buchstaben, Zahlen und Bindestriche (-) enthalten.</li><li>Vor und nach jedem Bindestrich (-) muss direkt ein Buchstabe oder eine Zahl stehen, und aufeinander folgende Bindestriche sind für Containernamen nicht zulässig.</li><li>Der Name kann 3 bis 63 Zeichen lang sein.</li></ul>
Verknüpfte Dienste/Tabellen/Pipelines | Innerhalb einer Data Factory eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt. | <ul><li>Maximale Anzahl von Zeichen in einem Tabellennamen: 260.</li><li>Objektnamen müssen mit einem Buchstaben, einer Zahl oder einem Unterstrich beginnen (_).</li><li>Folgende Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\\“</li></ul> Ressourcengruppe | Für Microsoft Azure eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt. | <ul><li>Maximale Anzahl von Zeichen: 1.000.</li><li>Der Name darf Buchstaben, Zahlen und die folgenden Zeichen enthalten: „-“, „_“, „,“ und „.“.</li></ul>

<!---HONumber=Oct15_HO4-->