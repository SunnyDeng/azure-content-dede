<properties 
	pageTitle="Computehostingoptionen in Azure" 
	description="Erfahren Sie mehr über Computehostingoptionen in Azure und ihre Funktionsweise: Virtual Machines, Websites, Cloud Services und weitere Optionen." 
	headerExpose="" 
	footerExpose="" 
	services="cloud-services,virtual-machines"
	authors="Thraka" 
	documentationCenter=""
	manager="timlt"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="adegeo;cephalin;kathydav"/>




# Computehostingoptionen in Azure

Azure bietet verschiedene Hostingmodelle für die Ausführung von Anwendungen. Jedes Modell umfasst andere Dienste. Sie können also genau das Modell auswählen, das sich für Ihr Vorhaben eignet. Dieser Artikel geht auf die jeweiligen Optionen ein, beschreibt die einzelnen Technologien und führt Beispiele für ihre Verwendung auf.

| Computeoptionen | Zielgruppe |
| ------------------ | --------   |
| [App Service] | Skalierbare Web-Apps, mobile Apps, API-Apps und Logik-Apps für beliebige Geräte |
| [Cloud Services] | Hoch verfügbare, skalierbare, n-schichtige Cloud-Apps mit mehr Kontrolle des Betriebssystems |
| [Virtual Machines] | Angepasste Windows- und Linux-VMs mit vollständiger Kontrolle des Betriebssystems |

[AZURE.INCLUDE [Inhalt](../../includes/app-service-choose-me-content.md)]

[AZURE.INCLUDE [Inhalt](../../includes/cloud-services-choose-me-content.md)]

[AZURE.INCLUDE [Inhalt](../../includes/virtual-machines-choose-me-content.md)]

## Weitere Optionen

Azure bietet auch andere Computehostingmodelle für spezielle Zwecke, beispielsweise:

* [Mobile Services](/services/mobile-services/) – Für die Bereitstellung eines Cloud-Back-Ends für Apps optimiert, die auf mobilen Geräten ausgeführt werden.
* [Batch](/services/batch/) – Für die Verarbeitung einer großen Anzahl von ähnlichen Aufgaben optimiert, idealerweise Workloads, die sich für die parallele Ausführung auf mehreren Computern eignen.
* [HDInsight (Hadoop)](/services/hdinsight/) – Für die Ausführung von [MapReduce](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options/#hadoop)-Aufträgen in Hadoop-Clustern optimiert. 

## Welches Modell sollte ich verwenden? Treffen einer Auswahl

Alle dieser drei allgemeinen Computehostingmodelle von Azure eignen sich für die Erstellung skalierbarer und zuverlässiger Anwendungen in der Cloud. Welches Modell sollten Sie angesichts dieser scheinbaren Ähnlichkeit verwenden?

App Service stellt für die meisten Web-Apps die beste Wahl dar. Die Bereitstellungs- und die Verwaltungsfunktionen sind in die Plattform integriert. Sites können rasch skaliert werden, um umfangreiche Datenaufkommen zu bewältigen. Außerdem bieten der integrierte Lastenausgleich und der Traffic Manager eine hohe Verfügbarkeit. Sie können vorhandene Websites mit einem [Onlinemigrationstool](https://www.migratetoazure.net/) ohne Weiteres in App Service verschieben, eine Open-Source-App aus dem Web-App-Katalog verwenden oder eine neue Website mithilfe des Frameworks und der Tools Ihrer Wahl erstellen. Mit der [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226)-Funktion können Sie problemlos die Auftragsverarbeitung im Hintergrund zu Ihrer App hinzufügen oder sogar Computeworkloads ausführen, die keine Web-Apps sind.

Wenn Sie mehr Kontrolle über die Webserverumgebung benötigen, wie etwa die Fähigkeit eines Fernzugriffs auf den Server oder der Konfiguration von Serverstartaufgaben, sind Azure Cloud Services üblicherweise die beste Option.

Wenn Sie über eine vorhandene Anwendung verfügen, die für die Ausführung unter Azure-Websites oder Azure Cloud Services erhebliche Änderungen erforderlich macht, können Sie Azure Virtual Machines wählen, um die Migration in die Cloud zu vereinfachen. Eine korrekte Konfiguration, Sicherung und Wartung von VMs erfordert im Vergleich zu Azure Websites oder Cloud Services jedoch weitaus mehr Zeit und IT-Kenntnisse. Wenn Sie die Nutzung von Azure Virtual Machines in Erwägung ziehen, müssen Sie den fortlaufenden Wartungsaufwand berücksichtigen, der für das Anpassen, Aktualisieren oder Verwalten der VM-Umgebung erforderlich ist. Falls Sie über eine Anwendung verfügen, die wesentlich geändert werden müsste, um sie in App Service oder Azure Cloud Services auszuführen, können Sie die Migration zur Cloud mithilfe von Azure Virtual Machines vereinfachen. Eine korrekte Konfiguration, Sicherung und Wartung von VMs erfordert im Vergleich zu App Service und Cloud Services jedoch weitaus mehr Zeit und IT-Kenntnisse. Wenn Sie die Nutzung von Azure Virtual Machines in Erwägung ziehen, müssen Sie den fortlaufenden Wartungsaufwand berücksichtigen, der für das Anpassen, Aktualisieren oder Verwalten der VM-Umgebung erforderlich ist.

Unter Umständen reicht eine Option jedoch nicht aus. In diesen Fällen können Sie eine Kombination der unterschiedlichen Optionen verwenden. Dies bietet sich beispielsweise an, wenn Sie eine Anwendung erstellen und dabei die Vorteile der Verwaltungsoptionen von Cloud Services-Webrollen nutzen möchten, aber aus Kompatibilitäts- oder Leistungsgründen eine auf einer VM gehostete Standardversion von SQL Server verwenden müssen.

<!-- In this case, the best option is to combine compute hosting options, as the figure below shows.--

<a name="fig4"></a>
![07_CombineTechnologies][07_CombineTechnologies] 
 
**Figure: A single application can use multiple hosting options.**

As the figure illustrates, the Cloud Services VMs run in a separate cloud service from the Virtual Machines VMs. Still, the two can communicate quite efficiently, so building an app this way is sometimes the best choice.
[07_CombineTechnologies]: ./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png
!-->

[App Service]: #tellmeas
[Virtual Machines]: #tellmevm
[Cloud Services]: #tellmecs

## Nächste Schritte

* App Service, Cloud Services und Virtual Machines [im Vergleich](../choose-web-site-cloud-service-vm/)
* Weitere Informationen zu [App Service](../app-service-web-overview.md)
* Weitere Informationen zu [Cloud Services](services/cloud-services/)
* Weitere Informationen zu [Virtual Machines](https://msdn.microsoft.com/library/azure/jj156143.aspx) 

<!---HONumber=62-->