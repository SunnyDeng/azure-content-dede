<properties 
	pageTitle="Anwendungsarchitektur in Microsoft Azure" 
	description="Übersicht über die Architektur, die allgemeine Entwurfsmuster abdeckt." 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="robb"/>

#Anwendungsarchitektur in Microsoft Azure
Ressourcen zum Erstellen von Anwendungen, die Microsoft Azure verwenden.

##Entwurfsmuster für die Azure-Architektur
Microsoft veröffentlicht eine Reihe von Architekturentwurfsmustern, mit deren Hilfe Sie eigene benutzerdefinierte Entwürfe erstellen können. Diese Muster dienen als Bausteine für die Architektur. Sie können miteinander kombiniert werden, damit Sie Microsoft Azure Platform optimal für die geschäftlichen Anforderungen Ihres Unternehmens nutzen können.


[Übersicht](../azure-architectures-cpif-overview/) – [Hybrid Networking](../azure-architectures-cpif-infrastructure-hybrid-networking/) – [Offsite Batch Processing](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) – [Multi-Site Data Tier](../azure-architectures-cpif-foundation-multi-site-data-tier/) – [Global Load Balanced Web Tier](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) – [Azure Search Tier](../azure-architectures-cpif-foundation-azure-search-tier/)
 
Jedes Muster enthält Folgendes:
 
- Eine Dienstbeschreibung
- Eine Liste der zur Nutzung des Musters erforderlichen Azure-Dienste
- Architekturdiagramme
- Architekturabhängigkeiten
- Einschränkungen des Musters oder Überlegungen, die sich auf das Muster auswirken können
- Schnittstellen und Endpunkten
- Antimuster
- Wichtige grundlegende Überlegungen zur Architektur: Verfügbarkeit und Stabilität, zusammengesetzte SLAs für die verwendeten Dienste, Skalierung und Leistung, Kosten und operative Überlegungen.

##Microsoft-Architekturpläne

Microsoft veröffentlicht eine Reihe von allgemeinen Architekturplänen, die veranschaulichen, wie bestimmte Systemtypen mit Microsoft-Produkten und Microsoft Azure-Diensten erstellt werden. Jeder Plan umfasst eine Visio-basierte 2D-Datei, die Sie herunterladen und bearbeiten können, eine lebendigere 3D-PDF-Datei zur Einführung sowie ein Video, das durch die 3D-Version führt. Siehe [Microsoft-Architekturpläne](http://msdn.microsoft.com/dn630664).

Die 2D-Plandiagramme verwenden den nachstehend genannten Cloud- und Enterprise-Symbolsatz.

Die 3D-PDF-Plandateien wurden in einem Nicht-Microsoft-Tool erstellt, eine Visio-Vorlage befindet sich jedoch in Entwicklung. Weitere Informationen finden Sie im [Trainingsvideo zur BETA-Vorlage](http://aka.ms/3dBlueprintTemplate). Wenn Sie die 3D-Visio-Vorlage (Beta) erhalten möchten, senden Sie eine E-Mail an [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).

![Microsoft-Architekturpläne – 3D-Diagramm](./media/architecture-overview/BluePrintThumb.jpg)

##Cloud- und Enterprise-Symbolsatz

[Laden Sie den Cloud- und Enterprise-Symbolsatz herunter](http://aka.ms/CnESymbols), um technische Materialien zu erstellen, die Azure, Windows Server, SQL Server und andere Microsoft-Produkte beschreiben. Sie können diese in Architekturdiagrammen, Trainingsmaterialien, Präsentationen, Datenblättern, Infografiken, Whitepapers und sogar in Büchern von Drittanbietern verwenden, wenn diese Schulungsinformationen für die Verwendung von Microsoft-Produkten enthalten. Die Symbole liegen in Visio- und PNG-Formaten vor. Anweisungen zur Verwendung der PNG-Dateien in PowerPoint sind enthalten.

Der Symbolsatz wird vierteljährlich herausgegeben und aktualisiert, wenn neue Dienste erscheinen. Wenn Sie eine Vorschau der neuesten Version anzeigen möchten, die möglicherweise zusätzliche Azure-Dienste einschließt, senden Sie eine E-Mail an [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).

Ihr Feedback ist uns sehr wichtig. Daher ist im Download eine Feedback-Anleitung enthalten. Wenn Sie die Symbole verwendet haben, beantworten Sie die fünf Fragen unserer [Umfrage](http://aka.ms/azuresymbolssurveyv2) um uns mitzuteilen, ob die Symbole für Sie nützlich sind und wie Sie sie verwenden.

Zusätzliche Symbole stehen in der [Microsoft Office Visio-Schablone](http://www.microsoft.com/de-de/download/details.aspx?id=35772) zur Verfügung, allerdings sind diese nicht wie der CnE-Satz für Architekturdiagramme optimiert.

![Cloud- und Enterprise-Symbolsatz](./media/architecture-overview/CnESymbols.png)

##Entwurfsmuster
Microsoft Patterns and Practices hat das Buch zu [Cloudentwurfsmustern](http://msdn.microsoft.com/library/dn568099.aspx) veröffentlicht, das sowohl auf MSDN als auch als PDF-Download erhältlich ist. Es ist zudem ein großformatiges Poster verfügbar, auf dem alle Muster aufgelistet sind.

![Patterns und Practices – Poster zu Cloudmustern](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Infografiken zur Architektur
Microsoft veröffentlicht mehrere architekturbezogene Poster/Infografiken. Dazu zählen [Erstellen echter Cloudanwendungen](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) und [Skalieren mit Cloud Services](http://azure.microsoft.com/documentation/infographics/cloud-services/).

![Infografiken zur Azure-Architektur](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=July15_HO4-->