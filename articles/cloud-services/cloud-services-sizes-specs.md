<properties
 pageTitle="Größen für Clouddienste"
 description="Führt die verschiedenen Größen für Web-und Workerrollen von Azure-Clouddiensten auf."
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
 ms.date="09/14/2015"
 ms.author="adegeo"/>

# Größen für Clouddienste

In diesem Thema werden die verfügbaren Größen und Optionen für Cloud Service-Rolleninstanzen (Web- und Workerrollen) beschrieben. Darüber hinaus werden Überlegungen zur Bereitstellung angestellt, die Sie berücksichtigen sollten, wenn Sie eine Verwendung dieser Ressourcen planen.

Azure Virtual Machines und Azure Cloud Services sind zwei von mehreren unterschiedlichen Typen von Compute-Ressourcen, die über Azure zur Verfügung stehen. Erläuterungen hierzu finden Sie unter [Compute Hosting Options Provided by Azure](fundamentals-application-models.md) (Compute-Hostingoptionen in Azure).

> [AZURE.NOTE]Zugehörige Einschränkungen von Azure finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).

## Größen für Web- und Workerrolleninstanzen

Die folgenden Überlegungen können Ihnen bei der Entscheidung über die Größe behilflich sein:

* VM-Instanzen der D-Serie dienen zum Ausführen von Anwendungen, die eine höhere Rechenleistung und eine höhere temporäre Datenträgerleistung erfordern. VMs der D-Serie bieten schnellere Prozessoren, ein höheres Verhältnis von Speicher zu Kern und ein SSD (Solid State Drive) für den temporären Datenträger. Einzelheiten finden Sie in der Ankündigung im Azure-Blog unter [New D-Series Virtual Machine Sizes](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) (Neue VM-Größen der D-Serie, in englischer Sprache).  

*   Die Dv2-Serie, eine Nachfolgerin der ursprünglichen D-Serie, hat eine leistungsfähigere CPU. Die CPU der Dv2-Serie ist ca. 35 % schneller als die CPU der D-Serie. Sie basiert auf der neuesten Generation des 2,4-GHz-Intel Xeon ® E5-2673 v3-Prozessors (Haswell) und kann mit der Intel Turbo Boost Technology 2.0 bis auf 3,2 GHz gebracht werden. Der Dv2-Serie hat die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die D-Serie.

    Die regionale Verfügbarkeit der Dv2-Serie basiert auf diesem Zeitplan: Okt. 2015: USA, Osten 2, USA, Mitte, USA, Norden-Mitte, USA, Westen Nov. 2015: USA, Osten, Europa, Norden, Europa, Westen Jan. 2016: USA, Süden-Mitte, Asien-Pazifik, Osten, Asien-Pazifik, Südosten, Japan, Osten, Japan, Westen, Australien, Osten, Australien, Südosten, Brazilien, Süden

* Web- und Workerrollen erfordern aufgrund der Systemanforderungen mehr temporären Speicherplatz als Azure Virtual Machines. Die Systemdateien reservieren 4 GB Speicherplatz für die Windows-Auslagerungsdatei und 2 GB Speicherplatz für die Windows-Dumpdatei.

* Der Betriebssystem-Datenträger enthält das Windows-Gastbetriebssystem, den Ordner "Programme" (einschließlich mithilfe von Startaufgaben durchgeführter Installationen, es sei denn, Sie geben Sie einen anderen Datenträger an), Registrierungsänderungen, den Ordner "System32" und .NET Framework.

* Der lokale Ressourcendatenträger enthält Azure-Protokolle und -Konfigurationsdateien, die Azure-Diagnose (mit IIS-Protokollen) und alle lokalen Speicherressourcen, die Sie definieren.

* Der Datenträger für Apps (Anwendungen) ist der Ort, an dem Ihre CSPKG-Datei extrahiert wird. Er enthält u. a. Ihre Website, Binärdateien, den Rollenhostprozess, Startaufgaben und die Datei "Web.config".

* Die VM-Größen A8/A10 und A9/A11 haben die gleiche Kapazität. Die VM-Instanzen A8 und A9 umfassen einen zusätzlichen Netzwerkadapter, der mit einem RDMA (Remote Direct Memory Access)-Netzwerk für die schnelle Kommunikation zwischen virtuellen Computern verbunden ist. Die A8- und A9-Instanzen wurden für High Performance Computing-Anwendungen entwickelt, die während der Ausführung eine konstante Kommunikation mit niedriger Latenz zwischen den Knoten erfordern, z. B. MPI (Message Passing Interface)-Anwendungen. Die VM-Instanzen A10 und A11 enthalten keinen zusätzlichen Netzwerkadapter. Die A10- und A11-Instanzen sind für High Performance Computing-Anwendungen vorgesehen, die keine konstante Kommunikation mit niedriger Latenz zwischen den Knoten erfordern, auch bekannt als parametrische oder hochgradig parallele Anwendungen.

|Größe|CPU-<br>Kerne|Arbeitsspeicher|Datenträgergrößen|
|---|---|---|---|
|Sehr klein|1|768 MB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 15384 MB<br/>Apps = ca. 1,5 GB|
|Klein|1|1,75 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 225304 MB<br/>Apps = ca. 1,5 GB|
|Mittel|2|3,5 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 496664 MB<br/>Apps = ca. 1,5 GB|
|Groß|4|7 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 1018904 MB<br/>Apps = ca. 1,5 GB|
|Extragroß|8|14 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 2083864 MB<br/>Apps = ca. 1,5 GB|
|A5|2|14 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 496664 MB<br/>Apps = ca. 1,5 GB|
|A6|4|28 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 1018904 MB<br/>Apps = ca. 1,5 GB|
|A7|8|56 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 2083864 MB<br/>Apps = ca. 1,5 GB
|A8|8|56 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 1856172 MB<br/>Apps = ca. 1,5 GB<blockquote>Hinweis: Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</blockquote>|
|A9|16|112 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 1856172 MB<br/>Apps = ca. 1,5 GB<blockquote>Hinweis: Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</blockquote>|
|A10|8|56 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 1856172 MB<br/>Apps = ca. 1,5 GB<blockquote>Hinweis: Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</blockquote>|
|A11|16|112 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 1856172 MB<br/>Apps = ca. 1,5 GB<blockquote>Hinweis: Weitere Informationen und Überlegungen zum Verwenden dieser Größe finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen</a>.</blockquote>|
|Standard\_D1|1|3,5 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 46104 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D2|2|7 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 97304 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D3|4|14 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 199704 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D4|8|28 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 404504 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D11|2|14 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 97304 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D12|4|28 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 199704 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D13|8|56 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 404504 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D14|16|112 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 814104 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D1\_v2|1|3,5 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 46104 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D2\_v2|2|7 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 97304 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D3\_v2|4|14 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 199704 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D4\_v2|8|28 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 404504 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D5\_v2|16|56 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 814104 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D11\_v2|2|14 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 97304 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D12\_v2|4|28 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 199704 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D13\_v2|8|56 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 404504 MB<br/>Apps = ca. 1,5 GB|
|Standard\_D14\_v2|16|112 GB|Betriebssystem = Größe des Gastbetriebssystems<br/>Lokale Ressource = 814104 MB<br/>Apps = ca. 1,5 GB|
## Konfigurieren von Größen für Cloud Services

Sie können die Größe des virtuellen Computers einer Rolleninstanz als Teil des Service-Modells angeben, wie durch die Dienstdefinitionsdatei beschrieben. Die Größe einer Rolle bestimmt die Anzahl der CPU-Kerne, die Speicherkapazität und die lokale Dateisystemgröße, die einer aktiven Instanz zugeordnet werden. Wählen Sie die Rollengröße basierend auf der Ressourcenanforderung Ihrer Anwendung.

Dies ist ein Beispiel, in der eine kleine Rollengröße für eine Webrolleninstanz festgelegt wird:


    <WebRole name="WebRole1" vmsize="Small">
    …
    </WebRole>

Achten Sie darauf, dass die angegebene lokale Ressourcengröße kleiner gleich der maximalen lokalen Ressourcengröße in der obigen Tabelle ist.
## Nächste Schritte

[Einrichten eines Clouddiensts für Azure](https://msdn.microsoft.com/library/hh124108)

<!---HONumber=Oct15_HO3-->