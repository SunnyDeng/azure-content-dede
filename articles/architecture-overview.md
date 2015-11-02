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
	ms.date="10/16/2015" 
	ms.author="robb"/>

#Anwendungsarchitektur in Microsoft Azure
Ressourcen zum Erstellen von Anwendungen, die Microsoft Azure verwenden. Dies umfasst Tools, mit denen Sie die Diagramme zeichnen können, um Softwaresysteme visuell zu beschreiben.



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

![Entwurfsmuster für die Azure-Architektur](./media/architecture-overview/AzureArchPatterns.jpg)


##Poster mit Entwurfsmustern
Microsoft Patterns and Practices hat das Buch zu [Cloudentwurfsmustern](http://msdn.microsoft.com/library/dn568099.aspx) veröffentlicht, das sowohl auf MSDN als auch als PDF-Download erhältlich ist. Es ist zudem ein großformatiges Poster verfügbar, auf dem alle Muster aufgelistet sind.

![Patterns und Practices – Poster zu Cloudmustern](./media/architecture-overview/PnPPatternPosterThumb.jpg)



##Microsoft-Architektur – Zertifizierungskurs

Microsoft hat vor Kurzem einen neuen Architekturkurs zur Vorbereitung auf die Microsoft-Zertifizierungsprüfung 70-534 veröffentlicht. Der Kurs [steht auf EDX.ORG kostenlos zur Verfügung](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x). Im Kurs wird die neue [Visio-Vorlage "3D Blueprint"](#3d-blueprint-visio-template) verwendet.

![Microsoft-Architektur – Zertifizierungskurs](./media/architecture-overview/EDXCourse.png)


##Microsoft-Architekturpläne

Microsoft veröffentlicht eine Reihe allgemeiner [Architekturpläne](http://aka.ms/azblueprints), die veranschaulichen, wie bestimmte Systemtypen mithilfe von Microsoft-Produkten erstellt werden.

Jeder Plan enthält Folgendes:

- Eine einfache **Visio 2003-basierte 2D-Datei**, die Sie herunterladen und bearbeiten können. 
- Eine lebendigere **3D-PDF-Datei** zur Einführung in den Plan für weniger technisch orientierte Zielgruppen.
- Ein **Video**, das durch die 3D-Version führt. 

In den Plänen wird der [Cloud- und Enterprise-Symbolsatz](#symbol-and-icon-sets) verwendet.

![Microsoft-Architekturpläne – 3D-Diagramm](./media/architecture-overview/BluePrintThumb.jpg)



##Visio-Vorlage "3D Blueprint"

Die 3D-Versionen der [Microsoft-Architekturpläne](http://aka.ms/azblueprints) wurden zunächst in einem Nicht-Microsoft-Tool erstellt. Eine neue Vorlage für Visio 2013 (und höher) wurde am 5. August 2015 im Rahmen eines [Zertifizierungskurses zur Microsoft-Architektur auf EDX.ORG](#microsoft-architecture-certification-course) bereitgestellt.

Die Vorlage steht auch außerhalb des Kurses zur Verfügung.

- [Sehen Sie sich zunächst das Trainingsvideo an](http://aka.ms/3dBlueprintTemplateVideo), damit Sie wissen, was die Vorlage leisten kann.   
- Laden Sie die [Microsoft Visio-Vorlage "3D Blueprint"](http://aka.ms/3DBlueprintTemplate) herunter.
- Laden Sie die [Cloud- und Enterprise-Symbole](#symbol-and-icon-sets) herunter, die mit der 3D-Vorlage verwendet werden.

Wenn Sie noch Fragen haben, die in den Trainingsmaterialien nicht beantwortet werden, oder uns Feedback geben möchten, senden Sie eine E-Mail an [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com). Zweckmäßigkeit ist eines der Hauptziele der Vorlage. Lassen Sie uns deshalb wissen, was gut ist und was verbessert werden sollte.

![Microsoft Visio-Vorlage "3D Blueprint"](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)



##Symbolsätze 

[Sehen Sie sich das Trainingsvideo zu Visio und Symbolen an](http://aka.ms/CnESymbolsVideo), und [laden Sie den Cloud- und Enterprise-Symbolsatz herunter](http://aka.ms/CnESymbols), um technische Materialien zu erstellen, die Azure, Windows Server, SQL Server und andere Produkte beschreiben. Sie können die Symbole in Architekturdiagrammen, Trainingsmaterialien, Präsentationen, Datenblättern, Infografiken, Whitepapers und sogar in Büchern von Drittanbietern verwenden, wenn diese Schulungsinformationen für die Verwendung von Microsoft-Produkten enthalten. Die Symbole sind jedoch nicht für die Verwendung auf Benutzeroberflächen vorgesehen.

Die CnE-Symbole liegen im Visio- und PNG-Format vor. Weitere Anweisungen zur Verwendung der PNG-Dateien in PowerPoint sind in dem Satz enthalten.

Der Symbolsatz wird vierteljährlich herausgegeben und aktualisiert, wenn neue Dienste erscheinen.

Zusätzliche Symbole für Microsoft Office und zugehörige Technologien stehen in der [Microsoft Office Visio-Schablone](http://www.microsoft.com/de-DE/download/details.aspx?id=35772) zur Verfügung, allerdings sind diese nicht wie der CnE-Satz für Architekturdiagramme optimiert.

**Feedback:** Wenn Sie die CnE-Symbole verwendet haben, beantworten Sie die fünf Fragen unserer [Umfrage](http://aka.ms/azuresymbolssurveyv2). Wenn Sie Fragen oder Anliegen haben, senden Sie eine E-Mail an [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com). Ihre Meinung und Ihr Feedback interessieren uns, damit wir weiter an Verbesserungen arbeiten können.

![Cloud- und Enterprise-Symbolsatz](./media/architecture-overview/CnESymbols.png)


##Infografiken zur Architektur

Microsoft veröffentlicht mehrere architekturbezogene Poster/Infografiken. Dazu zählen [Erstellen echter Cloudanwendungen](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) und [Skalieren mit Cloud Services](http://azure.microsoft.com/documentation/infographics/cloud-services/).

![Infografiken zur Azure-Architektur](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=Oct15_HO4-->