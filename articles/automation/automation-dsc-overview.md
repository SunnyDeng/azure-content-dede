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
   ms.date="10/09/2015"
   ms.author="coreyp"/>

# Azure Automation DSC – Übersicht #

## Was ist PowerShell DSC? ##
Desired State Configuration (DSC) ist eine neue Verwaltungsplattform in Windows PowerShell, die eine Konfigurationsverwaltung für physische Hosts und virtuelle Computer über eine deklarative PowerShell-Syntax ermöglicht.

DSC stellt einen Satz an Windows PowerShell-Spracherweiterungen, neue Windows PowerShell-Cmdlets und Ressourcen bereit, mit deren Hilfe Sie Ihre Softwareumgebung deklarativ konfigurieren können. Darüber hinaus ist es möglich, vorhandene Konfigurationen zu warten und zu verwalten.

### Praktische Anwendung ###
Nachfolgend werden einige Beispielszenarios gezeigt, in denen Sie integrierte DSC-Ressourcen zum automatisierten Konfigurieren und Verwalten einer Gruppe von Computern (auch Zielknoten genannt) verwenden können:

- Aktivieren oder Deaktivieren von Serverrollen und -features
- Verwalten von Registrierungseinstellungen
- Verwalten von Dateien und Verzeichnissen
- Starten, Beenden und Verwalten von Prozessen und Diensten
- Verwalten von Gruppen und Benutzerkonten
- Bereitstellen neuer Software
- Verwalten von Umgebungsvariablen
- Ausführen von Windows PowerShell-Skripts
- Korrigieren einer Konfiguration, die vom gewünschten Zustand abweicht
- Ermitteln des aktuellen Konfigurationsstatus auf einem vorgegebenen Knoten

Zusätzlich können Sie benutzerdefinierte Ressourcen erstellen, um den Zustand einer beliebigen Anwendungs- oder Systemeinstellung zu konfigurieren.


Weitere Informationen zu PowerShell DSC finden Sie unter [Configuration in a DevOps World – Windows PowerShell Desired State Configuration](http://blogs.msdn.com/b/powershell/archive/2013/11/01/configuration-in-a-devops-world-windows-powershell-desired-state-configuration.aspx) (in englischer Sprache).

##Was ist Azure Automation DSC?##
Azure Automation DSC setzt auf den mit PowerShell DSC eingeführten Grundlagen auf, um eine einfachere Konfigurationsverwaltung zu ermöglichen. Azure Automation DSC führt dieselbe Verwaltungsschicht in [PowerShell Desired State Configuration](https://technet.microsoft.com/library/dn249912.aspx) <link> ein, wie Azure Automation sie bereits für die PowerShell-Skripterstellung bietet.

Mithilfe von Azure Automation DSC können Sie [PowerShell DSC-Konfigurationen erstellen und verwalten](https://technet.microsoft.com/library/dn249918.aspx), [DSC-Ressourcen](https://technet.microsoft.com/library/dn282125.aspx) importieren und DSC-Knotenkonfigurationen (MOF-Dokumente) generieren – und das alles in der Cloud. Diese DSC-Elemente werden auf dem [DSC-Pullserver](https://technet.microsoft.com/library/dn249913.aspx) für Azure Automation platziert, sodass Zielknoten (beispielsweise physische und virtuelle Computer) diese in der Cloud oder lokal abrufen können, sich automatisch an den festgelegten Zustand anpassen und ihre Kompatibilität mit dem gewünschten Zustand an Azure Automation zurückmelden.

Möchten Sie sich lieber ein Video ansehen? Betrachten Sie das folgende Video vom Mai 2015, als Azure Automation DSC erstmals angekündigt wurde. **Hinweis:** Die Konzepte und der Lebenszyklus, die in diesem Video erläutert werden, sind noch zutreffend, jedoch wurde Azure Automation DSC seit der Aufzeichnung des Videos beträchtlich weiterentwickelt. Es bietet jetzt eine öffentliche Vorschauversion, besitzt eine umfangreichere Benutzeroberfläche im Azure-Portal und unterstützt zusätzliche Funktionen.

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

>[AZURE.IMPORTANT]Eine Konfiguration sollte nur einen Konfigurationsblock mit demselben Namen wie die Konfiguration in Azure Automation DSC enthalten.


###Knotenkonfiguration###

Wenn eine DSC-Konfiguration kompiliert wird, werden in Abhängigkeit von den Node-Blöcken in der Konfiguration eine oder mehrere Knotenkonfigurationen erstellt. Eine Knotenkonfiguration ist dasselbe wie ein MOF- oder Konfigurationsdokument (wenn Sie mit diesen PS DSC-Begriffen vertraut sind) und repräsentiert eine "Rolle", wie z. B. einen Webserver oder Worker, deren gewünschten Zustand einer oder mehrere Knoten annehmen sollen. Namen von Knotenkonfigurationen in Azure Automation DSC weisen das Format „Konfigurationsname.NodeConfigurationBlockName“ auf.

PS DSC-Knoten erkennen Knotenkonfigurationen, die entweder per DSC-Push oder mithilfe von Pullmethoden angewendet werden sollen. Azure Automation DSC basiert auf der DSC-Pullmethode, bei der Knoten Knotenkonfigurationen anfordern, die sie von Azure Automation DSC-Pullservern anwenden sollen. Da die Knoten die Anforderung an Azure Automation DSC senden, können sich die Knoten hinter Firewalls befinden, keine eingehenden Ports geöffnet haben usw. Die Knoten benötigen lediglich ausgehenden Zugriff auf das Internet.


###Knoten###

Jeder Computer, dessen Konfiguration von DSC verwaltet wird, ist ein DSC-Knoten. Es kann sich hierbei um einen virtuellen Azure-Computer oder um einen lokalen virtuellen Computer bzw. physischen Host handeln. Knoten wenden Knotenkonfigurationen an, um Kompatibilität mit dem gewünschten definierten Zustand zu erzielen und zu erhalten, und sie können ihren Konfigurationsstatus und ihre Kompatibilität an einen Berichtserver melden.

Azure Automation DSC vereinfacht die Integration von Knoten zur Verwaltung durch Azure Automation DSC und erlaubt das Ändern der Knotenkonfiguration, die jedem Knoten serverseitig zugewiesen ist. Wenn also ein Knoten den Server das nächste Mal auf Anweisungen überprüft, wird eine andere Rolle angenommen und die Konfiguration entsprechend abgeändert. Knoten melden außerdem ihren Zustand und ihre Konfigurationskompatibilität an Azure Automation DSC.


###Ressource###
DSC-Ressourcen sind Bausteine, die Sie zum Definieren einer Windows PowerShell DSC-Konfiguration (Desired State Configuration) verwenden können. DSC verfügt über einen Satz integrierter Funktionen zum Konfigurieren von Ressourcen, darunter beispielsweise Dateien und Ordner, Serverfunktionen und -rollen, Registrierungseinstellungen, Umgebungsvariablen sowie Dienste und Prozesse. Eine vollständige Liste der integrierten DSC-Ressourcen und Informationen zu deren Verwendung finden Sie unter [Integrierte Windows PowerShell DSC-Ressourcen](https://technet.microsoft.com/library/dn249921.aspx).

DSC-Ressourcen können auch als Bestandteil der PowerShell-Module importiert werden, um den Satz der integrierten DSC-Ressourcen zu erweitern. Nicht standardmäßige Ressourcen werden von den DSC-Knoten vom DSC-Pullserver abgerufen, wenn eine durch den Knoten anzuwendende Knotenkonfiguration Verweise auf diese Ressourcen enthält. Informationen zum Erstellen von benutzerdefinierten Ressourcen finden Sie unter [Erstellen von benutzerdefinierten Windows PowerShell DSC-Ressourcen](https://technet.microsoft.com/library/dn249927.aspx).

Zum Lieferumfang von Azure Automation DSC gehören dieselben integrierten DSC-Ressourcen wie bei PS DSC. Sie können Azure Automation DSC zusätzliche Ressourcen hinzufügen, indem Sie PowerShell-Module mit den gewünschten Ressourcen in Azure Automation importieren.


###Kompilierungsauftrag###
Ein Kompilierungsauftrag in Azure Automation DSC ist eine Instanz einer Konfigurationskompilierung, um eine oder mehrere Knotenkonfigurationen zu erstellen. Sie ähneln Runbookaufträgen in Azure Automation, führen jedoch abgesehen von der Erstellung von Knotenkonfigurationen keine wirklichen Aufgaben aus. Alle durch einen Kompilierungsauftrag erstellten Knotenkonfigurationen werden automatisch auf dem Azure Automation DSC-Pullserver platziert und überschreiben vorherige Versionen der Knotenkonfigurationen, sofern vorhanden. Der Name einer über einen Kompilierungsauftrag erstellten Knotenkonfiguration weist das Format „Konfigurationsname.NodeConfigurationBlockName“ auf. Beispielsweise würde durch das Kompilieren der nachstehenden Konfiguration eine einzelne Knotenkonfiguration mit dem Namen "MyConfiguration.webserver" erstellt werden.


![alt text](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE]Ebenso wie Runbooks können Konfigurationen veröffentlicht werden. Dies hat nichts mit dem Platzieren von DSC-Elementen auf dem Azure Automation DSC-Pullserver zu tun. Kompilierungsaufträge sorgen dafür, dass DSC-Elemente auf dem Azure Automation DSC-Pullserver platziert werden. Weitere Informationen zum "Veröffentlichen" in Azure Automation finden Sie unter [Veröffentlichen eines Runbooks](https://msdn.microsoft.com/library/dn903765.aspx).

Azure Automation DSC stellt für die Verwaltung von DSC-Kompilierungsaufträgen zurzeit die folgenden Cmdlets im [PowerShell-Modul des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/mt244122.aspx) bereit:

-	`Get-AzureRmAutomationDscCompilationJob`
-	`Get-AzureRmAutomationDscCompilationJobOutput`
-	`Start-AzureRmAutomationDscCompilationJob`

##Azure Automation DSC – Lebenszyklus##
Der Weg von einem leeren Automation-Konto zu einer verwalteten und ordnungsgemäß konfigurierten Knotengruppe umfasst eine Reihe von Verfahren zum Definieren von Konfigurationen, zum Umwandeln dieser Konfigurationen in Knotenkonfigurationen und zum Integrieren von Knoten und Knotenkonfigurationen in Azure Automation DSC. Das folgende Diagramm veranschaulicht den Azure Automation DSC-Lebenszyklus:

![alt text](./media/automation-dsc-overview/DSCLifecycle.png)


##Häufige / bekannte Probleme:##

- Azure Automation DSC bietet zurzeit keine Unterstützung für teilweise oder zusammengesetzte DSC-Konfigurationen.

- Für den PowerShell DSC-Agent für Windows muss die neueste Version von WMF 5 installiert werden, um eine Kommunikation mit Azure Automation zu ermöglichen. Die neueste Version des PowerShell DSC-Agents für Linux muss installiert sein, um unter Linux eine Kommunikation mit Azure Automation zu ermöglichen.

- Azure Automation bietet keine Unterstützung für die parallele Verwendung von PowerShell-Modulen. Dies bedeutet, dass alle Konfigurationen innerhalb eines Automation-Kontos mit der neuesten Version eines in dieses Automation-Konto importierten PowerShell-Moduls sowie mit der letzten Version aller PowerShell DSC-Ressourcen arbeiten müssen, die im Modul enthalten sind und von der Konfiguration verwendet werden.

- Der traditionelle PowerShell DSC-Pullserver erwartet, dass Modul-ZIP-Dateien im Format **Modulname\_Version.zip** auf dem Pullserver platziert werden. Azure Automation erwartet, dass PowerShell-Module mit Namen im Format **Modulname.zip** importiert werden. In [diesem Blogbeitrag](http://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/) finden Sie weitere Informationen zum erforderlichen Format von Integrationsmodulen, um diese Module in Azure Automation zu importieren.

- PowerShell-Module, die über das Format "Version pro Ordner" parallele DSC-Ressourcen im Modul angeben, können in Azure Automation zurzeit nicht verwendet werden.

- In Azure Automation importierte PowerShell-Module dürfen keine DOC- oder DOCX-Dateien enthalten. Einige PowerShell-Module mit DSC-Ressourcen enthalten diese Dateien zur Bereitstellung von Hilfeinformationen. Diese Dateien müssen vor dem Import in Azure Automation aus den Modulen entfernt werden.

- Bei der ersten Registrierung eines Knotens für ein Azure Automation-Konto, oder wenn der Knoten serverseitig einer anderen Knotenkonfiguration zugewiesen wird, ist sein Zustand kompatibel – selbst, wenn der Knotenzustand tatsächlich nicht mit der Knotenkonfiguration kompatibel ist, der er jetzt zugeordnet ist. Nachdem der Knoten nach der Registrierung oder einer Änderung der Knotenkonfigurationszuweisung einen ersten Pullvorgang ausgeführt oder einen ersten Bericht gesendet hat, kann der Knotenzustand als vertrauenswürdig eingestuft werden.

- Wenn beim Integrieren eines virtuellen Azure-Computers zur Verwaltung mit Azure Automation DSC über `Register-AzureAutomationDscNode`, `Set-AzureVMExtension` oder die VM-Erweiterung von Azure Automation DSC im Azure-Vorschauportal die Registrierung mit dem Fehler **Der Computername wurde nicht angegeben, und das Konfigurationsverzeichnis enthält keine Konfigurationsdateien** abgebrochen wird, ist dies ein Fehlalarm. Die VM-Registrierung war tatsächlich erfolgreich. Die erfolgreiche Registrierung kann mit dem Cmdlet `Get-AzureAutomationDscNode` überprüft werden.

- Beim Integrieren eines virtuellen Azure-Computers für die Verwaltung in Azure Automation DSC mithilfe von `Register-AzureAutomationDscNode`, `Set-AzureVMExtension` oder mithilfe der VM-Erweiterung für Azure Automation DSC im Azure-Vorschauportal kann es bis zu eine Stunde dauern, bis der virtuelle Computer als DSC-Knoten in Azure Automation angezeigt wird. Dies liegt an der Installation von Windows Management Framework 5.0 auf dem virtuellen Computer durch die Azure VM-Erweiterung für DSC, mit der der virtuelle Computer in Azure Automation DSC integriert werden muss.

- Nach der Registrierung handelt jeder Knoten automatisch ein eindeutiges Zertifikat für die Authentifizierung aus, die nach einem Jahr abläuft. Zu diesem Zeitpunkt kann das Registrierungsprotokoll für den PowerShell-DSC Zertifikate nicht automatisch erneuern, wenn sie sich dem Ablaufdatum nähern, weshalb Sie die Knoten nach einem Jahr erneut registrieren müssen. Stellen Sie vor einer erneuten Registrierung sicher, dass jeder Knoten Windows Management Framework 5.0 RTM ausführt. Wenn das Authentifizierungszertifikat eines Knotens abläuft und er nicht erneut registriert wird, schlägt die Kommunikation mit Azure Automation fehl und der Knoten wird als „nicht reagierend“ markiert. Die erneute Registrierung erfolgt auf dieselbe Weise wie die ursprüngliche Registrierung des Knotens. Eine erneute Registrierung, die 90 Tage oder weniger vor Ablaufzeitpunkt des Zertifikats oder zu einem beliebigen Zeitpunkt nach dessen Ablaufzeitpunkt durchgeführt wird, hat zur Folge, dass ein neues Zertifikat generiert und verwendet wird.

##Verwandte Artikel##

- [Azure Automation DSC-Cmdlets](https://msdn.microsoft.com/library/mt244122.aspx)
- [Azure Automation DSC – Preise](http://azure.microsoft.com/pricing/details/automation/)
- [Kontinuierliche Bereitstellung für IaaS-VMs mit Azure Automation DSC und Chocolatey](automation-dsc-cd-chocolatey.md)

<!---HONumber=Oct15_HO4-->