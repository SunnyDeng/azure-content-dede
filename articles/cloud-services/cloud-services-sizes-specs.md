<properties 
 pageTitle="Größen für Cloud Services" 
 description="Führt die verschiedenen Größen für Web-und Workerrollen auf." 
 services="cloud-services" 
 documentationCenter="" 
 authors="Thraka" 
 manager="timlt" 
 editor=""/>
<tags 
 ms.service="cloud-services" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="na" 
 ms.workload="tbd"
 ms.date="05/20/2015" 
 ms.author="adegeo"/>
 
# Größen für Cloud Services

## Übersicht 

In diesem Thema werden die verfügbaren Größen und Optionen für Cloud Service-Rolleninstanzen (Web- und Workerrollen) beschrieben. Darüber hinaus werden Überlegungen zur Bereitstellung angestellt, die Sie berücksichtigen sollten, wenn Sie eine Verwendung dieser Ressourcen planen.

Azure Virtual Machines und Azure Cloud Services sind zwei von mehreren unterschiedlichen Typen von Compute-Ressourcen, die über Azure zur Verfügung stehen. Erläuterungen hierzu finden Sie unter [Compute Hosting Options Provided by Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926) (Compute-Hostingoptionen in Azure, in englischer Sprache).

>[AZURE.NOTE]Verwandte Einschränkungen von Azure finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).

###Größen für Web- und Workerrolleninstanzen

Die folgenden Überlegungen können Ihnen bei der Entscheidung über die Größe behilflich sein:

*   Instanzen können jetzt für die Verwendung von VMs der D-Serie konfiguriert werden. Diese dienen zum Ausführen von Anwendungen, die eine höhere Rechenleistung und eine höhere temporäre Datenträgerleistung erfordern. VMs der D-Serie bieten schnellere Prozessoren, ein höheres Verhältnis von Speicher zu Kern und ein SSD (Solid State Drive) für den temporären Datenträger. Einzelheiten finden Sie in der Ankündigung im Azure-Blog unter [New D-Series Virtual Machine Sizes](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) (Neue VM-Größen der D-Serie, in englischer Sprache).  

*   Web- und Workerrollen erfordern aufgrund der Systemanforderungen mehr temporären Speicherplatz als Azure Virtual Machines. Die Systemdateien reservieren 4 GB Speicherplatz für die Windows-Auslagerungsdatei und 2 GB Speicherplatz für die Windows-Dumpdatei.

*   Der Betriebssystem-Datenträger enthält das Windows-Gastbetriebssystem, den Ordner "Programme" (einschließlich mithilfe von Startaufgaben durchgeführter Installationen, es sei denn, Sie geben Sie einen anderen Datenträger an), Registrierungsänderungen, den Ordner "System32" und .NET Framework.

*   Der lokale Ressourcendatenträger enthält Azure-Protokolle und -Konfigurationsdateien, die Azure-Diagnose (mit IIS-Protokollen) und alle lokalen Speicherressourcen, die Sie definieren.

*   Der Datenträger für Apps (Anwendungen) ist der Ort, an dem Ihre CSPKG-Datei extrahiert wird. Er enthält u. a. Ihre Website, Binärdateien, den Rollenhostprozess, Startaufgaben und die Datei "Web.config".

*   Die VM-Größen A8/A10 und A9/A11 haben die gleiche Kapazität. Die VM-Instanzen A8 und A9 umfassen einen zusätzlichen Netzwerkadapter, der mit einem RDMA (Remote Direct Memory Access)-Netzwerk für die schnelle Kommunikation zwischen virtuellen Computern verbunden ist. Die A8- und A9-Instanzen wurden für High Performance Computing-Anwendungen entwickelt, die während der Ausführung eine konstante Kommunikation mit niedriger Latenz zwischen den Knoten erfordern, z. B. MPI (Message Passing Interface)-Anwendungen. Die VM-Instanzen A10 und A11 umfassen keinen zusätzlichen Netzwerkadapter. Die A10- und A11-Instanzen sind für High Performance Computing-Anwendungen vorgesehen, die keine konstante Kommunikation mit niedriger Latenz zwischen den Knoten erfordern, auch bekannt als parametrische oder hochgradig parallele Anwendungen.

|Größe|CPU-<br>Kerne|Arbeitsspeicher|Datenträgergrößen|
|---|---|---|---|
|Sehr klein|1|768 MB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 19 GB</p><p>Apps = ca. 1,5 GB</p>|
|Klein|1|1,75 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 224 GB</p><p>Apps = ca. 1,5 GB</p>|
|Mittel|2|3,5 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 489 GB</p><p>Apps = ca. 1,5 GB</p>|
|Groß|4|7 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 999 GB</p><p>Apps = ca. 1,5 GB</p>|
|Extragroß|8|14 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 2.039 GB</p><p>Apps = ca. 1,5 GB</p>|
|A5|2|14 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 489 GB</p><p>Apps = ca. 1,5 GB</p>|
|A6|4|28 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 999 GB</p><p>Apps = ca. 1,5 GB</p>|
|A7|8|56 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 2.039 GB</p><p>Apps = ca. 1,5 GB</p>
|A8|8|56 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 1,77 TB</p><p>Apps = ca. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</p></blockquote>|
|A9|16|<p>112 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 1,77 TB</p><p>Apps = ca. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</p></blockquote>|
|A10|8|<p>56 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 1,77 TB</p><p>Apps = ca. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</p></blockquote>|
|A11|16|<p>112 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 1,77 TB</p><p>Apps = ca. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</p></blockquote>|
|Standard_D1|1|3,5 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 50 GB</p><p>Apps = ca. 1,5 GB</p>|
|Standard_D2|2|7 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 100 GB</p><p>Apps = ca. 1,5 GB</p>|
|Standard_D3|4|14 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 200 GB</p><p>Apps = ca. 1,5 GB</p>|
|Standard_D4|8|28 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 400 GB</p><p>Apps = ca. 1,5 GB</p>|
|Standard_D11|2|14 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 100 GB</p><p>Apps = ca. 1.5 GB</p>|
|Standard_D12|4|28 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 200 GB</p><p>Apps = ca. 1.5 GB</p>|
|Standard_D13|8|56 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 400 GB</p><p>Apps = ca. 1,5 GB</p>|
|Standard_D14|16|112 GB|<p>Betriebssystem = Größe des Gastbetriebssystems</p><p>Lokale Ressource = 800 GB</p><p>Apps = ca. 1,5 GB</p>|

### Siehe auch

#### Konzepte

[Einrichten eines Clouddiensts für Azure](https://msdn.microsoft.com/library/hh124108)

[Konfigurieren von Größen für Cloud Services](https://msdn.microsoft.com/library/ee814754)

#### Weitere Ressourcen

[Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md)

 [Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen](http://go.microsoft.com/fwlink/p/?linkid=328042)

 [Größen für Virtual Machines](../virtual-machines-size-specs.md)

<!---HONumber=58--> 