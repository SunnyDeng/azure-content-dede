<properties urlDisplayName="What is a Cloud Service" pageTitle="Was ist ein Cloud-Dienst? Azure-Dienstverwaltung" metaKeywords="Azure cloud services intro, cloud services overview, cloud services basics" description="Eine Einf&uuml;hrung zu Cloud-Diensten in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="Was ist ein Clouddienst?" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />

# Was ist ein Clouddienst?

Wenn Sie eine Anwendung erstellen und in Azure ausführen, wird die Kombination aus Code und Konfiguration als Azure-Clouddienst bezeichnet (in früheren Azure-Versionen als *gehosteter Dienst*).

Durch Erstellen eines Cloud-Diensts können Sie eine Webanwendung mit mehreren Ebenen in Azure bereitstellen, wobei Sie mehrere Rollen definieren können, um die Verarbeitung zu verteilen und eine flexible Skalierung Ihrer Anwendung zu ermöglichen. Ein Clouddienst umfasst eine oder mehrere Webrollen und/oder Workerrollen, die jeweils über eigene Anwendungsdateien und eine eigene Konfiguration verfügen. Azure-Websites und virtuelle Computer ermöglichen ebenfalls Webanwendungen in Azure. Der Hauptvorteil von Cloud-Diensten ist die Möglichkeit, komplexere Architekturen mit mehreren Ebenen zu unterstützen. Einen genaueren Vergleich finden Sie unter [Vergleich von Websites, Clouddiensten und virtuellen Computern in Azure][Vergleich von Websites, Clouddiensten und virtuellen Computern in Azure].

Azure verwaltet automatisch die Infrastruktur für einen Clouddienst, indem es Routinewartungen durchführt, die Betriebssysteme patcht und eine Wiederherstellung nach Dienst- und Hardwarefehlern versucht. Wenn Sie für jede Rolle mindestens zwei Instanzen definieren, können die meisten Wartungsaufgaben sowie Ihre eigenen Dienstupgrades ohne Dienstunterbrechung ausgeführt werden. Ein Cloud-Dienst muss über mindestens zwei Instanzen für jede Rolle verfügen, damit der Dienst den Anforderungen der Azure-Vereinbarung zum Servicelevel genügt. Diese Vereinbarung garantiert den dem Internet zugewandten Rollen eine externe Konnektivität für mindestens 99,95 Prozent der Zeit.

Jeder Clouddienst hat zwei Umgebungen, in denen Sie das Dienstpaket und die Konfiguration bereitstellen können. Sie können einen Clouddienst in der Stagingumgebung bereitstellen, um sie zu testen, bevor Sie sie in die Produktionsumgebung überführen. Bei der Überführung eines Clouddiensts aus der Staging- in die Produktionsumgebung werden lediglich die virtuellen IP-Adressen ausgetauscht, die den beiden Umgebungen zugeordnet sind.

## Konzepte

-   **Clouddienstrolle:** Eine Clouddienstrolle umfasst Anwendungsdateien und eine Konfiguration. Ein Clouddienst kann zwei Typen von Rollen aufweisen:

> -   **Webrolle:** Eine Webrolle stellt einen dedizierten Internet Information Services (IIS)-Webserver bereit, der zum Hosten von Front-End-Webanwendungen verwendet wird.

> -   **Workerrolle:** Anwendungen, die in Workerrollen gehostet werden, können asynchrone, langfristige oder fortwährende Aufgaben ausführen, die von einer Benutzerinteraktion oder -eingabe unabhängig sind.

-   **Rolleninstanz:** Eine Rolleninstanz ist ein virtueller Computer, auf dem der Anwendungscode und die Rollenkonfiguration ausgeführt werden. Eine Rolle kann mehrere Instanzen haben, die in der Dienstkonfigurationsdatei definiert sind.

-   **Gastbetriebssystem:** Das Gastbetriebssystem für einen Clouddienst ist das auf den Rolleninstanzen (virtuellen Computern) installierte Betriebssystem, auf denen der Anwendungscode ausgeführt wird.

-   **Clouddienstkomponenten:** Für die Bereitstellung einer Anwendung als Clouddienst in Azure sind drei Komponenten erforderlich:

> -   **Dienstdefinitionsdatei:** Die Dienstdefinitionsdatei (CSDEF) definiert das Dienstmodell einschließlich der Rollenanzahl.

> -   **Dienstkonfigurationsdatei:** Die Clouddienst-Konfigurationsdatei (CSCFG) stellt Konfigurationseinstellungen für den Clouddienst und individuelle Rollen bereit, einschließlich der Anzahl der Rolleninstanzen.

> -   **Dienstpaket:** Das Dienstpaket (CSPKG) enthält den Anwendungscode und die Dienstdefinitionsdatei.

-   **Clouddienstbereitstellung:** Eine Clouddienstbereitstellung ist eine Instanz eines Clouddiensts, der in der Azure-Staging- oder -Produktionsumgebung bereitgestellt ist. Sie können Bereitstellungen sowohl in der Staging- als auch in der Produktionsumgebung verwalten.

-   **Bereitstellungsumgebungen:** Azure bietet zwei Bereitstellungsumgebungen für Clouddienste: eine *Stagingumgebung*, in der Sie die Bereitstellung testen können, bevor Sie sie in die *Produktionsumgebung* überführen. Die beiden Umgebungen unterscheiden sich nur durch die virtuellen IP-Adressen, über die auf den Clouddienst zugegriffen wird. In der Stagingumgebung identifiziert die GUID (Globally Unique Identifier) des Clouddiensts den Clouddienst in URLs (*GUID*.cloudapp.net). In der Produktionsumgebung beruht die URL auf dem benutzerfreundlicheren DNS-Präfix, das dem Clouddienst zugewiesen ist (z. B. *meinDienst*.cloudapp.net).

-   **Austauschen von Bereitstellungen:** Um eine Bereitstellung in der Azure-Stagingumgebung in die Produktionsumgebung zu überführen, können Sie die Bereitstellungen "austauschen", indem Sie die virtuellen IP-Adressen umschalten, über die auf die beiden Bereitstellungen zugegriffen wird. Nach der Bereitstellung verweist der DNS-Name für den Clouddienst auf die Bereitstellung, die sich zuvor in der Stagingumgebung befunden hatte.

-   **Minimale und ausführliche Überwachung:** Die *minimale Überwachung*, die für einen Clouddienst standardmäßig konfiguriert ist, verwendet Leistungsindikatoren, die aus den Hostbetriebssystemen für Rolleninstanzen (virtuelle Computer) erfasst werden. Bei der *ausführlichen Überwachung* werden zusätzliche Kennzahlen basierend auf Leistungsdaten innerhalb der Rolleninstanzen erfasst, um eine genauere Analyse von Problemen zu ermöglichen, die während der Anwendungsverarbeitung auftreten. Weitere Informationen finden Sie unter [How to Monitor Cloud Services][How to Monitor Cloud Services] (Überwachen von Clouddiensten, in englischer Sprache).

-   **Azure-Diagnose:** Azure-Diagnose ist die API, die die Erfassung von Diagnosedaten von Anwendungen ermöglicht, die in Azure ausgeführt werden. Azure-Diagnose muss für Clouddienstrollen aktiviert werden, damit die ausführliche Überwachung eingeschaltet werden kann. Weitere Informationen finden Sie unter [Aktivieren der Diagnose in Azure][Aktivieren der Diagnose in Azure].

-   **Verknüpfen einer Ressource:** Um die Abhängigkeiten des Clouddiensts von anderen Ressourcen, z. B. einer Azure-SQL-Datenbankinstanz, anzuzeigen, können Sie die Ressource mit dem Clouddienst verknüpfen. Im Vorschau-Verwaltungsportal können Sie verknüpfte Ressourcen auf der Seite **Verknüpfte Ressourcen** anzeigen, Sie können ihren Status im Dashboard anzeigen und eine verknüpfte SQL-Datenbankinstanz zusammen mit den Dienstrollen auf der Seite **Skalieren** skalieren. Wenn eine Ressource in diesem Sinne verknüpft wird, wird sie nicht mit der Anwendung verbunden. Sie müssen die Verbindungen im Anwendungscode konfigurieren.

-   **Skalieren eines Clouddiensts:** Ein Clouddienst wird vergrößert, indem die Anzahl der Rolleninstanzen (virtuellen Computer) erhöht wird, die für eine Rolle bereitgestellt werden. Ein Clouddienst wird durch Verringern der Anzahl der Rolleinstanzen verkleinert. Im Vorschauverwaltungsportal können Sie auch eine verknüpfte SQL-Datenbankinstanz skalieren, indem Sie die Edition der SQL-Datenbank und die maximale Größe der Datenbank ändern, wenn Sie die Dienstrollen skalieren.

-   **Azure-Vereinbarung zum Servicelevel (SLA):** Die Azure Compute-SLA garantiert, dass bei einer Bereitstellung von mindestens zwei Instanzen für jede Rolle der Zugriff auf den Clouddienst für mindestens 99,95 Prozent der Zeit aufrechterhalten wird. Zudem werden Erkennungs- und Korrekturmaßnahmen in 99,9 Prozent der Zeit initiiert, wenn der Prozess einer Rolleninstanz nicht ausgeführt wird. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel][Vereinbarungen zum Servicelevel].

  [Vergleich von Websites, Clouddiensten und virtuellen Computern in Azure]: http://azure.microsoft.com/de-de/documentation/articles/choose-web-site-cloud-service-vm/
  [How to Monitor Cloud Services]: http://azure.microsoft.com/de-de/manage/services/cloud-services/how-to-monitor-a-cloud-service/
  [Aktivieren der Diagnose in Azure]: http://azure.microsoft.com/de-de/documentation/articles/cloud-services-dotnet-diagnostics/
  [Vereinbarungen zum Servicelevel]: http://azure.microsoft.com/de-de/support/legal/sla/
