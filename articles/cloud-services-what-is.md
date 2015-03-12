<properties 
	pageTitle="Was ist ein Cloud-Dienst? Azure-Dienstverwaltung" 
	description="Eine Einführung zu Cloud-Diensten in Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="adegeo"/>




# Was ist ein Cloud-Dienst?
Wenn Sie eine Anwendung erstellen und in Azure ausführen, wird die Kombination aus Code und Konfiguration als Azure-Cloud-Dienst bezeichnet (in früheren Azure-Versionen als *hosted service*).

Durch Erstellen eines Cloud-Diensts können Sie eine Webanwendung mit mehreren Ebenen in Azure bereitstellen, wobei Sie mehrere Rollen definieren können, um die Verarbeitung zu verteilen und eine flexible Skalierung Ihrer Anwendung zu ermöglichen. Ein Cloud-Dienst umfasst eine oder mehrere Webrollen und/oder Workerrollen, die jeweils über eigene Anwendungsdateien und eine eigene Konfiguration verfügen. Azure-Websites und Virtual Machines ermöglichen ebenfalls Webanwendungen in Azure.  Der Hauptvorteil von Cloud-Diensten ist die Möglichkeit, komplexere Architekturen mit mehreren Ebenen zu unterstützen. Einen ausführlichen Vergleich finden Sie unter [Vergleich von Azure-Websites, Cloud Services und Virtual Machines][Comparison].

Azure verwaltet automatisch die Infrastruktur für einen Cloud-Dienst, indem es Routinewartungen durchführt, die Betriebssysteme patcht und eine Wiederherstellung nach Dienst- und Hardwarefehlern versucht. Wenn Sie für jede Rolle mindestens zwei Instanzen definieren, können die meisten Wartungsaufgaben sowie Ihre eigenen Dienstupgrades ohne Dienstunterbrechung ausgeführt werden. Ein Cloud-Dienst muss über mindestens zwei Instanzen für jede Rolle verfügen, damit der Dienst den Anforderungen der Azure-Vereinbarung zum Servicelevel genügt. Diese Vereinbarung garantiert den dem Internet zugewandten Rollen eine externe Konnektivität für mindestens 99,95 Prozent der Zeit. 

Jeder Cloud-Dienst hat zwei Umgebungen, in denen Sie das Dienstpaket und die Konfiguration bereitstellen können. Sie können einen Cloud-Dienst in der Stagingumgebung bereitstellen, um sie zu testen, bevor Sie sie in die Produktionsumgebung überführen. Bei der Überführung eines Cloud-Diensts aus der Staging- in die Produktionsumgebung werden lediglich die virtuellen IP-Adressen ausgetauscht, die den beiden Umgebungen zugeordnet sind. 


## Konzepte ##


- **Cloud-Dienstrolle:** Eine Cloud-Dienstrolle besteht aus Anwendungsdateien und einer Konfiguration. Ein Cloud-Dienst kann zwei Arten von Rollen haben:
 
>- **Webrolle**: Eine Webrolle stellt einen dedizierten Internet Information Services (IIS)-Webserver bereit, der zum Hosten von Front-End-Webanwendungen verwendet wird.

>- **Workerrolle:** Anwendungen, die in Workerrollen gehostet werden, können asynchrone, langfristige oder fortwährende Aufgaben ausführen, die von einer Benutzerinteraktion oder -eingabe unabhängig sind.

- **Rolleninstanz:** Eine Rolleninstanz ist ein virtueller Computer, auf dem der Anwendungscode und die Rollenkonfiguration ausgeführt werden. Eine Rolle kann mehrere Instanzen haben, die in der Dienstkonfigurationsdatei definiert sind.

- **Gastbetriebssystem:** Das Gastbetriebssystem für einen Cloud-Dienst ist das auf den Rolleninstanzen (virtuellen Computern) installierte Betriebssystem, auf denen der Anwendungscode ausgeführt wird.

- **Cloud-Dienstkomponenten:** Für die Bereitstellung einer Anwendung als Cloud-Dienst in Azure sind drei Komponenten erforderlich:

>- **Dienstdefinitionsdatei:** Die Cloud-Dienstdefinitionsdatei (CSDEF) definiert das Dienstmodell einschließlich der Rollenanzahl.

>- **Dienstkonfigurationsdatei:** Die Cloud-Dienstkonfigurationsdatei (CSCFG) stellt Konfigurationseinstellungen für den Cloud-Dienst und individuelle Rollen bereit, einschließlich der Anzahl der Rolleninstanzen.

>- **Dienstpaket:** Das Dienstpaket (CSPKG) enthält den Anwendungscode und die Dienstdefinitionsdatei.

- **Cloud-Dienstbereitstellung:** Eine Cloud-Dienstbereitstellung ist eine Instanz eines Cloud-Diensts, der in der Azure-Staging- oder -Produktionsumgebung bereitgestellt ist. Sie können Bereitstellungen sowohl in der Staging- als auch in der Produktionsumgebung verwalten.

- **Bereitstellungsumgebungen:** Azure bietet zwei Bereitstellungsumgebungen für Cloud-Dienste: eine *staging environment*, in der Sie die Bereitstellung testen können, bevor Sie sie in die  *production environment* höher stufen. Die beiden Umgebungen unterscheiden sich nur durch die virtuellen IP-Adressen, über die auf den Cloud-Dienst zugegriffen wird. In der Stagingumgebung identifiziert die GUID (Globally Unique Identifier) des Cloud-Diensts den Cloud-Dienst in URLs (*GUID*.cloudapp.net). In der Produktionsumgebung beruht die URL auf dem benutzerfreundlicheren DNS-Präfix, das dem Cloud-Dienst zugewiesen ist (z. B.  *myservice*.cloudapp.net).

- **Austauschen von Bereitstellungen:** Um eine Bereitstellung in der Azure-Stagingumgebung in die Produktionsumgebung zu überführen, können Sie die Bereitstellungen "austauschen", indem Sie die virtuellen IP-Adressen umschalten, über die auf die beiden Bereitstellungen zugegriffen wird. Nach der Bereitstellung verweist der DNS-Name für den Cloud-Dienst auf die Bereitstellung, die sich zuvor in der Stagingumgebung befunden hatte. 

- **Minimale und ausführliche Überwachung: Die ** *Minimal monitoring*, die für einen Cloud-Dienst standardmäßig konfiguriert ist, verwendet Leistungsindikatoren, die aus den Hostbetriebssystemen für Rolleninstanzen (virtuelle Computer) erfasst werden. Bei der  *Verbose monitoring* werden zusätzliche Kennzahlen basierend auf Leistungsdaten innerhalb der Rolleninstanzen erfasst, um eine genauere Analyse von Problemen zu ermöglichen, die während der Anwendungsverarbeitung auftreten. Weitere Informationen finden Sie unter [Überwachen von Cloud-Diensten][HTMonitorCloudServices].

- **Azure-Diagnose:** Azure-Diagnose ist die API, die die Erfassung von Diagnosedaten von Anwendungen ermöglicht, die in Azure ausgeführt werden. Azure-Diagnose muss für Cloud-Dienstrollen aktiviert werden, damit die ausführliche Überwachung eingeschaltet werden kann. Weitere Informationen finden Sie unter [Aktivieren der Diagnose in Azure][CloudServicesDiagnostics].

- **Verknüpfen einer Ressource:** Um die Abhängigkeiten des Cloud-Diensts von anderen Ressourcen, z. B. einer Azure-SQL-Datenbankinstanz, anzuzeigen, können Sie die Ressource mit dem Cloud-Dienst verknüpfen. Im Vorschau-Verwaltungsportal können Sie verknüpfte Ressourcen auf der Seite **Verknüpfte Ressourcen** anzeigen, Sie können ihren Status im Dashboard anzeigen und eine verknüpfte SQL-Datenbankinstanz zusammen mit den Dienstrollen auf der Seite **Skalieren** skalieren. Wenn eine Ressource in diesem Sinne verknüpft wird, wird sie nicht mit der Anwendung verbunden. Sie müssen die Verbindungen im Anwendungscode konfigurieren.

- **Skalieren ein Cloud-Diensts:** Ein Cloud-Dienst wird vergrößert, indem die Anzahl der Rolleninstanzen (virtuellen Computer) erhöht wird, die für eine Rolle bereitgestellt werden. Ein Cloud-Dienst wird durch Verringern der Anzahl der Rolleinstanzen verkleinert. Im Vorschauverwaltungsportal können Sie auch eine verknüpfte SQL-Datenbankinstanz skalieren, indem Sie die Edition der SQL-Datenbank und die maximale Größe der Datenbank ändern, wenn Sie die Dienstrollen skalieren.

- **Azure-Vereinbarung zum Servicelevel (SLA):** Die Azure Compute-SLA garantiert, dass bei einer Bereitstellung von mindestens zwei Instanzen für jede Rolle der Zugriff auf den Cloud-Dienst für mindestens 99,95 Prozent der Zeit aufrechterhalten wird. Zudem werden Erkennungs- und Korrekturmaßnahmen in 99,9 Prozent der Zeit initiiert, wenn der Prozess einer Rolleninstanz nicht ausgeführt wird. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel][SLA].

[HTMonitorCloudServices]:http://azure.microsoft.com/ manage/services/cloud-services/how-to-monitor-a-cloud-service/
[SLA]: http://azure.microsoft.com/ support/legal/sla/
[CloudServicesDiagnostics]: http://azure.microsoft.com/ documentation/articles/cloud-services-dotnet-diagnostics/
[Comparison]: http://azure.microsoft.com/ documentation/articles/choose-web-site-cloud-service-vm/

<!--HONumber=45--> 
