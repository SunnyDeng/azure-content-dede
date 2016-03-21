<properties 
   pageTitle="Azure Automation DSC – Übersicht | Microsoft Azure" 
   description="Eine Übersicht über Azure Automation Desired State Configuration (DSC), die verwendeten Begriffe und bekannte Fehler." 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="03/02/2016"
   ms.author="coreyp"/>

# Azure Automation DSC – Übersicht #

##Was ist Azure Automation DSC?##
Das Bereitstellen und Beibehalten des gewünschten Zustands von Servern und Anwendungsressourcen kann mühsam und fehleranfällig sein. Mit Azure Automation Desired State Configuration (DSC) können Sie den gewünschten Zustand aller Ihrer IT-Ressourcen nach Maß in der Cloud einheitlich bereitstellen, zuverlässig überwachen und automatisch aktualisieren. Automation DSC basiert auf PowerShell DSC und kann die Computerkonfiguration auf physischen Computern und virtuellen VMs unter Windows und Linux in der Cloud und lokal mit einem bestimmten Zustand abstimmen. Sie können eine kontinuierliche Bereitstellung von IT-Diensten mit einheitlicher Steuerung ermöglichen und schnelle Änderungen in Ihrer gesamten heterogenen Hybrid-IT-Umgebung problemlos verwalten.

Azure Automation DSC setzt auf den mit PowerShell DSC eingeführten Grundlagen auf, um eine einfachere Konfigurationsverwaltung zu ermöglichen. Azure Automation DSC führt dieselbe Verwaltungsschicht in [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) ein, wie Azure Automation sie bereits für die PowerShell-Skripterstellung bietet.

Mithilfe von Azure Automation DSC können Sie [PowerShell DSC-Konfigurationen erstellen und verwalten](https://technet.microsoft.com/library/dn249918.aspx), [DSC-Ressourcen](https://technet.microsoft.com/library/dn282125.aspx) importieren und DSC-Knotenkonfigurationen (MOF-Dokumente) generieren – und das alles in der Cloud. Diese DSC-Elemente werden auf dem [DSC-Pullserver](https://technet.microsoft.com/library/dn249913.aspx) für Azure Automation platziert, sodass Zielknoten (beispielsweise physische und virtuelle Computer) diese in der Cloud oder lokal abrufen können, sich automatisch an den festgelegten Zustand anpassen und ihre Kompatibilität mit dem gewünschten Zustand an Azure Automation zurückmelden.

Möchten Sie sich lieber ein Video ansehen? Betrachten Sie das folgende Video vom Mai 2015, als Azure Automation DSC erstmals angekündigt wurde. **Hinweis:** Die Konzepte und der Lebenszyklus, die in diesem Video erläutert werden, sind noch zutreffend, jedoch wurde Azure Automation DSC seit der Aufzeichnung des Videos beträchtlich weiterentwickelt. Es ist jetzt allgemeine verfügbar, besitzt eine umfangreichere Benutzeroberfläche im Azure-Portal und unterstützt viele zusätzliche Funktionen.

> [AZURE.VIDEO microsoft-ignite-2015-heterogeneous-configuration-management-using-microsoft-azure-automation]



## Azure Automation DSC – Begriffe ##
### Konfiguration ###
PowerShell DSC führt ein neues Konzept der sogenannten Konfigurationen ein. Mithilfe von Konfigurationen können Sie über die PowerShell-Syntax den gewünschten Zustand Ihrer Umgebung definieren. Um DSC zum Konfigurieren Ihrer Umgebung zu verwenden, definieren Sie zunächst einen Windows PowerShell-Skriptblock mit dem Schlüsselwort "Configuration". Auf den Block muss ein Bezeichner folgen, anschließend wird der Block mit Klammern ({}) abgetrennt.

![alt text](./media/automation-dsc-overview/AADSC_1.png)

Innerhalb des Konfigurationsblocks können Sie Knotenkonfigurationsblöcke definieren, mit denen die gewünschte Konfiguration für einen Satz an Knoten (Computern) in Ihrer Umgebung festgelegt wird, die exakt gleich konfiguriert werden sollen. Auf diese Weise stellt eine Knotenkonfiguration eine "Rolle" für einen oder mehrere Knoten dar. Ein Knotenkonfigurationsblock beginnt mit dem Schlüsselwort "Node". Nach diesem Schlüsselwort folgt der Name der Rolle, hierbei kann es sich um eine Variable oder einen Ausdruck handeln. Verwenden Sie nach dem Rollennamen Klammern {}, um den Knotenkonfigurationsblock abzutrennen.

![alt text](./media/automation-dsc-overview/AADSC_2.png)
 
Im Knotenkonfigurationsblock können Sie Ressourcenblöcke definieren, um spezifische DSC-Ressourcen zu konfigurieren. Ein Ressourcenblock beginnt mit dem Namen der Ressource, gefolgt vom gewünschten Bezeichner für diesen Block und Klammern {} zum Abtrennen des Blocks.

![alt text](./media/automation-dsc-overview/AADSC_3.png)

Ausführlichere Informationen zum Schlüsselwort "Configuration" finden Sie unter [Grundlegendes zum Schlüsselwort "Configuration" in DSC](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "Grundlegendes zum Schlüsselwort "Configuration" in DSC").

Das Ausführen (Kompilieren) einer DSC-Konfiguration führt zur Erstellung von einer oder mehreren DSC-Knotenkonfigurationen (MOF-Dokumenten), die die Einstellungen vorgeben, die von den DSC-Knoten zum Erreichen des gewünschten Zustands angewendet werden müssen.

Azure Automation DSC ermöglicht Ihnen das Importieren, Erstellen und Kompilieren von DSC-Konfigurationen in Azure Automation – ähnlich wie Runbooks in Azure Automation importiert, erstellt und gestartet werden können.

>[AZURE.IMPORTANT] Eine Konfiguration sollte nur einen Konfigurationsblock mit demselben Namen wie die Konfiguration in Azure Automation DSC enthalten.


###Knotenkonfiguration###

Wenn eine DSC-Konfiguration kompiliert wird, werden in Abhängigkeit von den Node-Blöcken in der Konfiguration eine oder mehrere Knotenkonfigurationen erstellt. Eine Knotenkonfiguration ist dasselbe wie ein MOF- oder Konfigurationsdokument (wenn Sie mit diesen PS DSC-Begriffen vertraut sind) und repräsentiert eine „Rolle“, wie z. B. einen Webserver oder Worker, deren gewünschten Zustand einer oder mehrere Knoten annehmen sollen oder deren Kompatibilität geprüft werden soll. Namen von Knotenkonfigurationen in Azure Automation DSC weisen das Format „Konfigurationsname.NodeConfigurationBlockName“ auf.

PS DSC-Knoten erkennen Knotenkonfigurationen, die entweder per DSC-Push oder mithilfe von Pullmethoden angewendet werden sollen. Azure Automation DSC basiert auf der DSC-Pullmethode, bei der Knoten Knotenkonfigurationen anfordern, die sie von Azure Automation DSC-Pullservern anwenden sollen. Da die Knoten die Anforderung an Azure Automation DSC senden, können sich die Knoten hinter Firewalls befinden, keine eingehenden Ports geöffnet haben usw. Sie benötigen nur ausgehenden Zugriff auf das Internet (entweder direkt oder über einen Proxy).


###Knoten###

Jeder Computer, dessen Konfiguration von DSC verwaltet wird, ist ein DSC-Knoten. Dabei kann es sich um einen virtuellen Azure-Computer unter Windows oder Linux, eine lokale VM, einen physischen Host oder eine VM in einer anderen öffentlichen Cloud handeln. Knoten wenden Knotenkonfigurationen an, um Kompatibilität mit dem gewünschten definierten Zustand zu erzielen und zu erhalten, und sie können ihren Konfigurationsstatus und ihre Kompatibilität mit dem gewünschten Zustand einem Berichtserver melden.

Azure Automation DSC vereinfacht die Integration von Knoten zur Verwaltung durch Azure Automation DSC und erlaubt das Ändern der Knotenkonfiguration, die jedem Knoten serverseitig zugewiesen ist. Wenn also ein Knoten den Server das nächste Mal auf Anweisungen überprüft, wird eine andere Rolle angenommen und sowohl die Konfiguration als auch der zu meldende Kompatibilitätsstatus entsprechend geändert.


###Ressource###
DSC-Ressourcen sind Bausteine, die Sie zum Definieren einer Windows PowerShell DSC-Konfiguration (Desired State Configuration) verwenden können. DSC verfügt über einen Satz integrierter Ressourcen, beispielsweise für Dateien und Ordner, Serverfunktionen und -rollen, Registrierungseinstellungen, Umgebungsvariablen sowie Dienste und Prozesse. Eine vollständige Liste der integrierten DSC-Ressourcen und Informationen zu deren Verwendung finden Sie unter [Integrierte Windows PowerShell DSC-Ressourcen](https://technet.microsoft.com/library/dn249921.aspx).

DSC-Ressourcen können auch als Bestandteil der PowerShell-Module importiert werden, um den Satz der integrierten DSC-Ressourcen zu erweitern. Nicht standardmäßige Ressourcen werden von den DSC-Knoten vom DSC-Pullserver abgerufen, wenn eine durch den Knoten anzuwendende Knotenkonfiguration Verweise auf diese Ressourcen enthält. Informationen zum Erstellen von benutzerdefinierten Ressourcen finden Sie unter [Erstellen von benutzerdefinierten Windows PowerShell DSC-Ressourcen](https://technet.microsoft.com/library/dn249927.aspx).

Zum Lieferumfang von Azure Automation DSC gehören dieselben integrierten DSC-Ressourcen wie bei PS DSC. Sie können Azure Automation DSC zusätzliche Ressourcen hinzufügen, indem Sie PowerShell-Module mit den gewünschten Ressourcen in Azure Automation importieren.


###Kompilierungsauftrag###
Ein Kompilierungsauftrag in Azure Automation DSC ist eine Instanz einer Konfigurationskompilierung, um eine oder mehrere Knotenkonfigurationen zu erstellen. Sie ähneln Runbookaufträgen in Azure Automation, führen jedoch abgesehen von der Erstellung von Knotenkonfigurationen keine wirklichen Aufgaben aus. Alle durch einen Kompilierungsauftrag erstellten Knotenkonfigurationen werden automatisch auf dem Azure Automation DSC-Pullserver platziert und überschreiben vorherige Versionen der Knotenkonfigurationen, sofern vorhanden. Der Name einer über einen Kompilierungsauftrag erstellten Knotenkonfiguration weist das Format „Konfigurationsname.NodeConfigurationBlockName“ auf. Beispielsweise würde durch das Kompilieren der nachstehenden Konfiguration eine einzelne Knotenkonfiguration mit dem Namen "MyConfiguration.webserver" erstellt werden.


![alt text](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE] Ebenso wie Runbooks können Konfigurationen veröffentlicht werden. Dies hat nichts mit dem Platzieren von DSC-Elementen auf dem Azure Automation DSC-Pullserver zu tun. Kompilierungsaufträge sorgen dafür, dass DSC-Elemente auf dem Azure Automation DSC-Pullserver platziert werden. Weitere Informationen zum "Veröffentlichen" in Azure Automation finden Sie unter [Veröffentlichen eines Runbooks](https://msdn.microsoft.com/library/dn903765.aspx).


##Azure Automation DSC – Lebenszyklus##
Der Weg von einem leeren Automation-Konto zu einer verwalteten und ordnungsgemäß konfigurierten Knotengruppe umfasst eine Reihe von Verfahren zum Definieren von Konfigurationen, zum Umwandeln dieser Konfigurationen in Knotenkonfigurationen und zum Integrieren von Knoten und Knotenkonfigurationen in Azure Automation DSC. Das folgende Diagramm veranschaulicht den Azure Automation DSC-Lebenszyklus:

![alt text](./media/automation-dsc-overview/DSCLifecycle.png)


Die folgende Abbildung veranschaulicht detailliert die Prozessschritte im DSC-Lebenszyklus. Dies umfasst verschiedene Arten, auf die eine Konfiguration importiert und auf Knoten in Azure Automation angewendet werden kann, sowie Komponenten, die erforderlich sind, damit ein lokaler Computer DCS und Interaktionen zwischen verschiedenen Komponenten unterstützen kann.

![DSC-Architektur](./media/automation-dsc-overview/dsc-architecture.png)

##Häufige / bekannte Probleme:##

- Wenn beim Upgrade auf WMF 5 RTM der Computer bereits als Knoten in Azure Automation DSC registriert ist, heben Sie seine Registrierung in Azure Automation DSC auf, und registrieren Sie ihn nach dem Upgrade auf WMF 5 RTM erneut.

- Azure Automation DSC bietet zurzeit keine Unterstützung für teilweise oder zusammengesetzte DSC-Konfigurationen. Zusammengesetzte DSC-Ressourcen können jedoch importiert und genau wie in der lokalen PowerShell verwendet werden, um die Wiederverwendung von Konfigurationen zu ermöglichen.

- Für den PowerShell DSC-Agent für Windows muss die neueste Version von WMF 5 installiert werden, um eine Kommunikation mit Azure Automation zu ermöglichen. Die neueste Version des PowerShell DSC-Agents für Linux muss installiert sein, um unter Linux eine Kommunikation mit Azure Automation zu ermöglichen.

- Der traditionelle PowerShell DSC-Pullserver erwartet, dass Modul-ZIP-Dateien im Format **Modulname\_Version.zip** auf dem Pullserver platziert werden. Azure Automation erwartet, dass PowerShell-Module mit Namen im Format **Modulname.zip** importiert werden. In [diesem Blogbeitrag](https://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/) finden Sie weitere Informationen zum erforderlichen Format von Integrationsmodulen, um diese Module in Azure Automation zu importieren.

- In Azure Automation importierte PowerShell-Module dürfen keine DOC- oder DOCX-Dateien enthalten. Einige PowerShell-Module mit DSC-Ressourcen enthalten diese Dateien zur Bereitstellung von Hilfeinformationen. Diese Dateien müssen vor dem Import in Azure Automation aus den Modulen entfernt werden.

- Bei der ersten Registrierung eines Knotens für ein Azure Automation-Konto, oder wenn der Knoten serverseitig einer anderen Knotenkonfiguration zugewiesen wird, ist sein Zustand kompatibel – selbst, wenn der Knotenzustand tatsächlich nicht mit der Knotenkonfiguration kompatibel ist, der er jetzt zugeordnet ist. Nachdem der Knoten nach der Registrierung oder einer Änderung der Knotenkonfigurationszuweisung einen ersten Pullvorgang ausgeführt oder einen ersten Bericht gesendet hat, kann der Knotenzustand als vertrauenswürdig eingestuft werden.

- Beim Integrieren eines virtuellen Azure-Computers in die Verwaltung durch Azure Automation DSC mithilfe einer unserer Methoden für die direkte Integration kann es bis zu einer Stunde dauern, bis ein DSC-Knoten in Azure Automation angezeigt wird. Dies liegt an der Installation von Windows Management Framework 5.0 auf dem virtuellen Computer durch die Azure VM-Erweiterung für DSC, mit der der virtuelle Computer in Azure Automation DSC integriert werden muss.

- Nach der Registrierung handelt jeder Knoten automatisch ein eindeutiges Zertifikat für die Authentifizierung aus, die nach einem Jahr abläuft. Zu diesem Zeitpunkt kann das Registrierungsprotokoll für den PowerShell-DSC Zertifikate nicht automatisch erneuern, wenn sie sich dem Ablaufdatum nähern, weshalb Sie die Knoten nach einem Jahr erneut registrieren müssen. Stellen Sie vor einer erneuten Registrierung sicher, dass jeder Knoten Windows Management Framework 5.0 RTM ausführt. Wenn das Authentifizierungszertifikat eines Knotens abläuft und er nicht erneut registriert wird, schlägt die Kommunikation mit Azure Automation fehl und der Knoten wird als „nicht reagierend“ markiert. Die erneute Registrierung erfolgt auf dieselbe Weise wie die ursprüngliche Registrierung des Knotens. Eine erneute Registrierung, die 90 Tage oder weniger vor Ablaufzeitpunkt des Zertifikats oder zu einem beliebigen Zeitpunkt nach dessen Ablaufzeitpunkt durchgeführt wird, hat zur Folge, dass ein neues Zertifikat generiert und verwendet wird.

- Wenn beim Upgrade auf WMF 5 RTM der Computer bereits als Knoten in Azure Automation DSC registriert ist, heben Sie seine Registrierung in Azure Automation DSC auf, und registrieren Sie ihn nach dem Upgrade auf WMF 5 RTM erneut. Löschen Sie vor der Neuregistrierung die Datei „$env:windir\\system32\\configuration\\DSCEngineCache.mof“.

- PowerShell DSC-Cmdlets funktionieren möglicherweise nicht, wenn WMF 5 RTM zusätzlich zu WMF 5 Produktion Preview installiert ist. Um dieses Problem zu beheben, führen Sie den folgenden Befehl in einer PowerShell-Sitzung mit erhöhten Rechten (als Administrator) aus: `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`
 

##Verwandte Artikel##

- [Integrieren von Computern für die Verwaltung durch Azure Automation DSC](../automation/automation-dsc-onboarding.md)
- [Kompilieren von Konfigurationen in Azure Automation DSC](../automation/automation-dsc-compile.md)
- [Azure Automation DSC-Cmdlets](https://msdn.microsoft.com/library/mt244122.aspx)
- [Azure Automation DSC – Preise](https://azure.microsoft.com/pricing/details/automation/)
- [Kontinuierliche Bereitstellung für IaaS-VMs mit Azure Automation DSC und Chocolatey](automation-dsc-cd-chocolatey.md)

<!---HONumber=AcomDC_0309_2016-->