<properties
	pageTitle="SharePoint Server 2010 auf Azure VMs | Microsoft Azure"
	description="Enthält Informationen zum Nutzen von SharePoint auf virtuellen Azure-Computern, zum Schritt-für-Schritt-Prozess und zu den unterstützten Szenarios für die Verwendung von SharePoint 2010."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="josephd"/>


# SharePoint 2010-Bereitstellung auf Azure Virtual Machines

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Microsoft SharePoint Server 2010 bietet eine umfangreiche Bereitstellungsflexibilität, die Organisationen bei der Ermittlung der passenden Einsatzszenarien für ihre geschäftlichen Anforderungen und Ziele unterstützen kann. Das Angebot von Azure Virtual Machines, das in Microsoft Azure gehostet und verwaltet wird, umfasst eine vollständige, zuverlässige und verfügbare Infrastruktur für die Unterstützung zahlreicher On-Demand-Anwendungs- und -Datenbankworkloads, z. B. Microsoft SQL Server- und SharePoint-Bereitstellungen.

Virtuelle Azure-Computer unterstützen zwar mehrere Workloads, aber in diesem Artikel geht es vorwiegend um SharePoint-Bereitstellungen. Mit virtuellen Azure-Computern können Organisationen ihre SharePoint-Infrastruktur mit geringem Zeitaufwand erstellen und verwalten, indem für nahezu jeden Host die universelle Bereitstellung mit entsprechendem Zugriff durchgeführt wird. Sie ermöglichen die vollständige Kontrolle und Verwaltung von Prozessoren, RAM, CPU-Bereichen und anderen Ressourcen von virtuellen SharePoint-Computern.

Virtuelle Azure-Computer senken den Hardwarebedarf, sodass Organisationen sich dem Erstellen und Verwalten einer maßgerechten Infrastruktur widmen können, anstatt sich um hohe Vorlaufkosten und Komplexität kümmern zu müssen. Dies bedeutet, dass Innovationen, Experimente und Iterationen innerhalb von Stunden durchgeführt werden können, im Gegensatz zu Tagen und Wochen, wie es bei herkömmlichen Bereitstellungen der Fall ist.

> [AZURE.NOTE]Informationen zum Bereitstellen von SharePoint 2013 in Azure finden Sie unter [Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) und [In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen](virtual-machines-sharepoint-infrastructure-services.md).

## SharePoint auf Azure Virtual Machines

SharePoint 2010 unterstützt die meisten Workloads einer Bereitstellung von virtuellen Azure-Computern auf flexible Weise. Virtuelle Azure-Computer passen bestens zu FIS (SharePoint Server for Internet Sites)- und Entwicklungsszenarien. Kernworkloads von SharePoint werden ebenfalls unterstützt. Wenn eine Organisation ihre eigene SharePoint 2010-Implementierung bei gleichzeitiger Nutzung der Virtualisierungsoptionen in einer Cloudinfrastruktur verwalten und steuern möchte, sind virtuelle Azure-Computer das ideale Bereitstellungsinstrument.

Das Azure Virtual Machines-Angebot wird in der Cloud gehostet und verwaltet. Für Flexibilität bei der Bereitstellung ist gesorgt, und Kosten werden durch Verringern der Investitionsaufwendungen für die Hardwarebeschaffung gesenkt. Bei größerer Agilität der Infrastruktur können Organisationen SharePoint Server innerhalb von Stunden bereitstellen, anstatt erst nach Tagen oder Wochen. Darüber hinaus ermöglichen es virtuelle Azure-Computer Organisationen, SharePoint-Workloads in der Cloud mit einem „nutzungsbasierten Gebührenmodell“ bereitzustellen. Wenn sich die SharePoint-Workloads vergrößern, kann eine Organisation die Infrastruktur schnell erweitern. Sobald sich der Serveraufwand wieder verringert, können die nicht mehr benötigten Ressourcen zurückgegeben werden, damit nur für die genutzten Ressourcen Kosten anfallen.

### Verschiebung des IT-Fokus

Viele Organisationen lagern allgemeine Komponenten ihrer IT-Infrastruktur und -Verwaltung aus, z. B. Hardware, Betriebssysteme, Sicherheit, Datenspeicherung und Backup, behalten aber die Kontrolle über geschäftskritische Anwendungen, z. B. SharePoint Server. Durch die Delegierung aller nicht geschäftskritischen Dienstebenen ihrer IT-Plattformen an einen virtuellen Anbieter können Organisationen ihren IT-Fokus auf zentrale, geschäftskritische SharePoint-Dienste richten und mit SharePoint-Projekten zur Wertschöpfung beitragen, anstatt Zeit für die Konfiguration der Infrastruktur aufzuwenden.

### Schnellere Bereitstellung

Die Unterstützung und Bereitstellung einer großen SharePoint-Infrastruktur kann den schnellen Einsatz der IT für Geschäftsanforderungen behindern. Abhängig von den Verfahren und Einschränkungen der Organisation kann es Wochen oder sogar Monate dauern, um SharePoint-Server und -Farmen zu erstellen, zu testen und vorzubereiten sowie sie in einer Produktionsumgebung bereitzustellen. Virtuelle Azure-Computer ermöglichen es Organisationen, ihre SharePoint-Workloads ohne Investitionsaufwendungen für Hardware schnell bereitzustellen. So können Organisationen die Infrastrukturagilität nutzen, um Bereitstellungen innerhalb von Stunden anstatt von Tagen oder Wochen zu bewerkstelligen.

### Skalierbarkeit

Ohne die Notwendigkeit physische SharePoint-Server und -Farmen bereitzustellen, zu testen und vorzubereiten, können Organisationen bei Bedarf Rechenkapazität jederzeit erweitern und verringern. Wenn die Anforderungen an die SharePoint-Workload anwachsen, kann eine Organisation ihre Cloudinfrastruktur rasch erweitern. Ebenso kann die Organisation Ressourcen verringern, wenn der Computing-Bedarf abnimmt, und nur für die tatsächlich genutzten Ressourcen fallen Kosten an. Mit virtuellen Azure-Computern werden Vorlaufkosten und langfristige Verpflichtungen reduziert, indem sie Organisationen ermöglichen, maßgerechte SharePoint-Infrastrukturen zu erstellen und zu verwalten. Auch hier bedeutet dies, dass Organisationen Innovationen, Experimente und Iterationen innerhalb von Stunden durchführen können, im Gegensatz zu Tagen und Wochen, wie es bei herkömmlichen Bereitstellungen der Fall ist.

### Nutzungsbezogene Gebühren

Virtuelle Azure-Computer liefern Rechenleistung, Arbeitsspeicher und Speicher für SharePoint-Szenarien, deren Preise in der Regel auf dem Ressourcenverbrauch basieren. Für Organisationen fallen nur für die genutzten Ressourcen Kosten an, und der Dienst liefert die gesamte für den Betrieb der SharePoint-Infrastruktur benötigte Kapazität. Weitere Informationen zur Preisgestaltung und Gebührenabrechnung finden Sie unter [Azure-Preisübersicht](http://azure.microsoft.com/pricing/). Beachten Sie, dass für die Speicherung und Datenübertragungen von Azure in ein lokales Netzwerk nominale Kosten anfallen. Für das Hochladen von Daten auf Azure werden aber keine Gebühren berechnet.

### Flexibilität

Mit virtuellen Azure-Computern erhalten Entwickler die Flexibilität, ihre bevorzugte Sprache oder Laufzeitumgebung zu wählen, mit offizieller Unterstützung von .NET, Node.js, Java und PHP. Entwickler können zudem ihre Tools wählen, mit Unterstützung von Microsoft Visual Studio, WebMatrix, Eclipse und Texteditoren. Darüber hinaus bietet Microsoft einen kostensparenden, risikoarmen Pfad zur Cloud und eine kostengünstige, einfache Bereitstellung für Cloud-Berichterstellungsfunktionen – sowie Zugang zu Business Intelligence (BI) für Geräte und Standorte. Schließlich können Benutzer mit dem Azure-Angebot nicht nur VHDs in die Cloud verschieben, sondern auch eine VHD zurückkopieren und sie lokal oder über einen anderen Cloud-Anbieter ausführen, solange sie über die geeignete Lizenz dafür verfügen.

## Bereitstellung

Die Abbildbibliothek in Azure enthält eine Liste der verfügbaren vorkonfigurierten virtuellen Computer. Benutzer können ISO/VHDs von SharePoint Server, SQL Server, Windows Server und anderen in der Abbildbibliothek veröffentlichen. Um die Erstellung von virtuellen Computern zu erleichtern, werden Basisimages angelegt und in der Bibliothek veröffentlicht. Autorisierte Benutzer können anhand dieser Images den gewünschten virtuellen Computer generieren. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers mit Windows im Azure-Portal](virtual-machines-windows-tutorial-classic-portal.md). Abbildung 1 zeigt die grundlegenden Schritte zum Erstellen eines virtuellen Computers mit dem Azure-Portal.

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

**Abbildung 1: Übersicht über die Schritte zum Erstellen eines virtuellen Computers**

Benutzer können im Azure-Verwaltungsportal auch ein vom System erstelltes Image hochladen. Weitere Informationen finden Sie unter [Erstellen und Hochladen einer Windows Server-VHD nach Azure](virtual-machines-create-upload-vhd-windows-server.md). Abbildung 2 zeigt die grundlegenden Schritte für das Hochladen eines Image, um eine VM zu erstellen.

Im Azure-Portal können Benutzer auch ein Image hochladen, für das das SysPrep-Tool bereits ausgeführt wurde. Weitere Informationen finden Sie unter [Erstellen und Hochladen einer Windows Server-VHD nach Azure](virtual-machines-create-upload-vhd-windows-server.md). Abbildung 2 zeigt die grundlegenden Schritte für das Hochladen eines Image, um einen virtuellen Computer zu erstellen.

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

**Abbildung 2: Übersicht über die Schritte zum Hochladen eines Image**

## Bereitstellen von SharePoint 2010 auf Azure

Sie können SharePoint 2010 auf Azure mit dem klassischen Bereitstelungsmodell bereitstellen, indem Sie folgendermaßen vorgehen:

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com/) mit Ihrem Azure-Abonnementkonto an. Wenn Sie kein Azure-Konto haben, [melden Sie sich für eine kostenlose Testversion von Azure an](http://azure.microsoft.com/pricing/free-trial/).
2. Klicken Sie zum Erstellen eines virtuellen Computers mit dem Basisbetriebssystem im Azure-Portal auf **Neu > Compute > Virtueller Computer > Aus Katalog**.
3. Das Dialogfeld **Image auswählen** wird angezeigt. Klicken Sie auf das Plattformimage **Windows Server 2008 R2 SP1** und dann auf den Pfeil nach rechts.
4. Das Dialogfeld **Konfiguration des virtuellen Computers** wird angezeigt. Geben Sie die folgenden Informationen ein:
	- Geben Sie den **Namen des virtuellen Computers** ein.
	- Wählen Sie die passende Größe aus. Für Produktionsumgebungen (SharePoint-Anwendungsserver und -Datenbank) wird A3 (4 Kerne, 7 GB Arbeitsspeicher) oder höher empfohlen.
	- Geben Sie im Feld **Neuer Benutzername** den Kontonamen eines lokalen Administrators ein.
	- Geben Sie im Feld **Neues Kennwort** ein sicheres Kennwort ein.
	- Geben Sie im Feld **Bestätigen** das Kennwort erneut ein, und klicken Sie dann auf den Pfeil nach rechts.
5. Das zweite Dialogfeld **Konfiguration des virtuellen Computers** wird angezeigt. Geben Sie die folgenden Informationen ein:
	- Wählen Sie unter **Clouddienst** die Option **Einen neuen Cloud-Dienst erstellen** aus. Geben Sie den DNS-Namen eines Clouddienstes an, oder wählen Sie einen vorhandenen Clouddienst aus.
	- Wählen Sie unter **Region/Affinitätsgruppe/Virtuelles Netzwerk** die Region aus, in der das virtuelle Image gehostet werden soll.
	- Klicken Sie unter **Speicherkonto** auf die Option **Ein automatisch generiertes Speicherkonto verwenden** oder auf den Namen eines vorhandenen Speicherkontos. Es wird nur ein Speicherkonto pro Region automatisch erstellt. Alle weiteren virtuellen Computer, die mit dieser Einstellung erstellt werden, befinden sich in diesem Speicherkonto. Es sind maximal 20 Speicherkonten möglich. Weitere Informationen finden Sie unter [Erstellen eines Speicherkontos in Azure](virtual-machines-create-upload-vhd-windows-server.md#step-2-create-a-storage-account-in-azure).
	- Wählen Sie im Feld **Verfügbarkeitsgruppe** die Option **(Keine)**, aus, und klicken Sie dann auf den Pfeil nach rechts.
6. Aktivieren Sie im dritten Dialogfeld **Konfiguration des virtuellen Computers** das Kontrollkästchen zum Erstellen des virtuellen Computers.

Informationen zum Verbinden mit dem virtuellen Computer finden Sie unter [Anmelden auf einem virtuellen Computer, auf dem Windows Server ausgeführt wird](virtual-machines-log-on-windows-server.md).

Erstellen Sie den virtuellen SQL Server-Computer mit einer der folgenden Optionen:

- Erstellen Sie anhand der Schritte 1 bis 7 einen virtuellen SQL Server 2012-Computer. Verwenden Sie aber in Schritt 3 das SQL Server 2012-Image anstelle des Windows Server 2008 R2 SP1-Image. Weitere Informationen finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](virtual-machines-provision-sql-server.md).
	- Wenn Sie auf diese Option klicken, wird bei der Bereitstellung eine Kopie der SQL Server 2012-Setupdateien im Verzeichnis „C:\\SQLServer\_11.0\_Full“ gespeichert, damit Sie die Installation anpassen können. Sie können beispielsweise die Evaluierungsinstallation von SQL Server 2012 mit Ihrem Lizenzschlüssel in eine lizenzierte Version umwandeln.

- Mit dem SQL Server-Systemvorbereitungstool (SysPrep) können Sie SQL Server auf dem virtuellen Computer mit einem Basisbetriebssystem installieren (siehe Schritte 1 bis 7). Weitere Informationen finden Sie unter [Installieren von SQL Server 2012 mit SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).

- Installieren Sie SQL Server über die Eingabeaufforderung. Weitere Informationen finden Sie unter [Installieren von SQL Server 2012 von der Eingabeaufforderung aus](http://msdn.microsoft.com/library/ms144259.aspx#SysPrep).

- Mit unterstützten SQL Server-Medien und Ihrem Lizenzschlüssel können Sie SQL Server auf dem virtuellen Computer mit dem Basisbetriebssystem installieren (siehe Schritte 1 bis 7).

Erstellen Sie die SharePoint-Farm anhand der folgenden Schritte:

Schritt 1: Konfigurieren Sie das Azure-Abonnement mit Skriptdateien.

Schritt 2. Stellen Sie SharePoint Server bereit, indem Sie einen weiteren virtuellen Computer mit einem Basisbetriebssystem erstellen (siehe oben die Schritte 1 bis 6). Um einen SharePoint-Server auf diesem virtuellen Computer zu erstellen, wählen Sie eine der folgenden Optionen:

- Bereitstellen mit der SharePoint-GUI:
	- Informationen zum Erstellen und Bereitstellen einer SharePoint-Farm finden Sie unter [Erstellen einer Microsoft SharePoint-Serverfarm](http://technet.microsoft.com/library/ee805948.aspx#CreateConfigure).
	- Informationen zum Hinzufügen eines Web- oder Anwendungsservers zur Farm finden Sie unter [Hinzufügen von Web- oder Anwendungsservern zu Farmen](http://technet.microsoft.com/library/cc261752.aspx).
	- Informationen zum Hinzufügen eines Datenbankservers zu einer vorhandenen Farm finden Sie unter [Hinzufügen eines Datenbankservers zu einer vorhandenen Farm](http://technet.microsoft.com/library/cc262781).
	- Um SQL Server 2012 für Ihre SharePoint-Farm zu verwenden, müssen Sie Service Pack 1 für SharePoint Server 2010 nach der Installation der Anwendung, und ohne den Server zu konfigurieren, herunterladen und installieren. Weitere Informationen finden Sie unter [Service Pack 1 für SharePoint Server 2010](http://www.microsoft.com/download/details.aspx?id=26623).
	- Um die Vorteile der BI-Funktionen von SQL Server zu nutzen, sollten Sie SharePoint Server als Serverfarm anstatt als eigenständigen Server installieren. Weitere Informationen finden Sie unter [Installieren von SQL Server 2012 Business Intelligence-Funktionen](http://technet.microsoft.com/library/hh231681.aspx).

- Bereitstellung mit Windows PowerShell: Sie können mit dem Befehlszeilentool Psconfig als alternative Schnittstelle verschiedene Vorgänge zur Steuerung der Bereitstellung von SharePoint 2010-Produkten ausführen. Weitere Informationen finden Sie unter [Psconfig-Befehlszeilenreferenz](http://technet.microsoft.com/library/cc263093.aspx).

Schritt 3. Konfigurieren Sie SharePoint. Wenn sich alle virtuellen SharePoint-Computer im Bereit-Zustand befinden, konfigurieren Sie SharePoint Server auf jedem Server anhand einer der folgenden Optionen:

- Konfigurieren von SharePoint mithilfe der GUI.
- Konfigurieren von SharePoint mithilfe von Windows PowerShell. Weitere Informationen finden Sie unter [Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx).
- Sie können auch AutoSPInstaller aus dem CodePlex-Projekt verwenden, das aus Windows PowerShell-Skripten, einer XML-Eingabedatei und einer Microsoft Windows-Standardbatchdatei besteht. AutoSPInstaller stellt ein Framework für ein SharePoint 2010-Installationsskript auf Grundlage von Windows PowerShell bereit. Weitere Informationen finden Sie unter [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).

Schritt 4: Nachdem das Skript abgeschlossen ist, stellen Sie über das Dashboard für virtuelle Computer eine Verbindung mit dem virtuellen Computer her.

Melden Sie sich beim SharePoint Server an, und überprüfen Sie mit der Zentraladministration die Konfiguration.

> [AZURE.NOTE]Konfigurieren Sie Sicherheitsoptionen am Endpunkt des Azure-Portals, und legen Sie einen eingehenden Port in der Windows Firewall des virtuellen Computers fest. Überprüfen Sie anschließend, ob Sie eine Remotesitzung mit Windows PowerShell für einen der SharePoint-Anwendungsserver starten können, indem Sie eine Windows PowerShell-Sitzung mit Administratoranmeldeinformationen öffnen.

### Erstellen und Hochladen einer virtuellen Festplatte

Sie können auch eigene Images erstellen und sie auf Azure als VHD-Datei hochladen. Weitere Informationen finden Sie unter [Erstellen und Hochladen einer Windows Server-VHD nach Azure](virtual-machines-create-upload-vhd-windows-server.md).

## Verwendungsszenarien

Dieser Abschnitt behandelt einige führende Kundenszenarien für SharePoint-Bereitstellungen mit virtuellen Azure-Computern.

### Szenario 1: Einfache SharePoint-Entwicklungs- und -Testumgebung

Organisationen suchen nach agileren Möglichkeiten, um SharePoint-Anwendungen zu erstellen und SharePoint-Umgebungen für die Bereitstellung und den Test, sowohl vor Ort als auch ausgelagert, zu konfigurieren. Im Grunde möchten sie die Zeit für das Einrichten von SharePoint-Anwendungsentwicklungsprojekten verkürzen und Kosten durch Nutzung der Testumgebungen senken. Eine Organisation möchte beispielsweise einen bedarfsgesteuerten Auslastungstest auf SharePoint Server und Benutzerakzeptanztests (UAT) mit mehr gleichzeitigen Benutzern an unterschiedlichen geografischen Standorten durchführen. Zudem ist für viele Organisationen die Integration von lokalen/externen Teams ein zunehmend wichtiges Geschäftserfordernis.

Dieses Szenario beschreibt, wie Organisationen vorkonfigurierte SharePoint-Farmen für Entwicklungs- und Testworkloads einsetzen können. Eine SharePoint-Bereitstellungstopologie unterscheidet sich nicht von einer lokalen virtualisierten Bereitstellung. Vorhandene IT-Fähigkeiten können 1:1 für eine Bereitstellung von virtuellen Azure-Computern eingesetzt werden, und zwar mit dem großen Vorteil einer fast vollständigen Kostenverschiebung von Investitionsaufwendungen zu operativen Aufwendungen. Es müssen dafür keine physischen Server angeschafft werden. Organisationen können Investitionskosten für Serverhardware vermeiden und größere Flexibilität erzielen, indem die Dauer für das Erstellen, Konfigurieren oder Erweitern einer SharePoint-Farm für eine Test- oder Entwicklungsumgebung erheblich reduziert wird. Die IT kann je nach Test- und Entwicklungsbedarf Kapazitäten dynamisch hinzufügen und entfernen. Zudem kann sich die IT mehr auf die Wertschöpfung durch SharePoint-Projekte konzentrieren als auf die Verwaltung der Infrastruktur.

Um Auslastungstest-Computer vollständig zu nutzen, können Organisationen virtualisierte SharePoint-Entwicklungs- und -Testcomputer auf Azure mit Betriebssystemunterstützung für Windows Server 2008 R2 konfigurieren. Dies ermöglicht Entwicklungsteams, Anwendungen zu erstellen und zu testen und auf einfache Weise ohne Codeänderungen auf lokale oder Cloud-Produktionsumgebungen zu migrieren. Die gleichen Frameworks und Toolsets können lokal und in der Cloud verwendet werden, sodass verteilten Teams der Zugang zu derselben Umgebung ermöglicht wird. Benutzer können durch Herstellen einer direkten VPN-Verbindung auch auf lokale Daten und Anwendungen zugreifen.

Abbildung 3 zeigt eine SharePoint-Entwicklungs- und -Testumgebung auf einem virtuellen Azure-Computer. Um diese Bereitstellung zu erstellen, verwenden Sie zunächst dieselbe lokale SharePoint-Entwicklungs- und Testumgebung, in der Sie Anwendungen entwickeln. Laden Sie anschließend die Anwendungen auf den virtuellen Azure-Computer hoch, und stellen Sie sie bereit, um Tests und weitere Entwicklungen durchzuführen. Wenn Ihre Organisation entscheidet, die Anwendung wieder lokal zu nutzen, kann dies ohne die Änderung der Anwendung geschehen.

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

**Abbildung 3: SharePoint-Entwicklungs- und -Testumgebung auf virtuellen Azure-Computern**

Führen Sie die folgenden Schritte aus, um eine SharePoint-Entwicklungs- und -Testumgebung auf Azure zu implementieren:

1. Bereitstellen: Stellen Sie zuerst mit Azure Virtual Network eine VPN-Verbindung zwischen Ihrem Standort und Azure bereit. (Da hier nicht Active Directory verwendet wird, ist ein VPN-Tunnel erforderlich.) Weitere Informationen finden Sie unter [Virtuelle Netzwerke – Übersicht](../virtual-network/virtual-networks-overview.md). Stellen Sie dann im Azure-Portal einen neuen virtuellen Computer mithilfe eines vorhandenen Image aus der Abbildbibliothek bereit.
	- Sie können die lokalen virtuellen SharePoint-Entwicklungs- und -Testcomputer in Ihr Azure-Speicherkonto hochladen und über die Abbildbibliothek auf diese VMs verweisen, um die erforderliche Umgebung zu erstellen.
	- Sie können das SQL Server 2012-Image anstelle des Windows Server 2008 R2 SP1-Image verwenden. Weitere Informationen finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](virtual-machines-provision-sql-server.md).

2. Installieren: Installieren Sie mithilfe einer Remotedesktopverbindung SharePoint Server, Visual Studio und SQL Server auf den virtuellen Computern.
	- Erstellen eines SharePoint-Entwicklercomputers mit dem SharePoint 2010 Easy Setup Script. Weitere Informationen finden Sie unter [SharePoint 2010 Easy Setup Script](http://www.microsoft.com/download/details.aspx?id=23415). Verwenden von Windows PowerShell. Weitere Informationen finden Sie unter [Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx). Verwenden von AutoSPInstaller aus dem CodePlex-Projekt. Weitere Informationen finden Sie unter [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Installieren von Visual Studio. Weitere Informationen finden Sie unter [Installieren von Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).
	- Installieren von SQL Server. Weitere Informationen finden Sie unter [Installieren von SQL Server mit SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Entwickeln von Bereitstellungspaketen und Skripts für Anwendungen und Datenbanken: Wenn Sie einen verfügbaren virtuellen Computer aus der Abbildbibliothek verwenden möchten, können Sie die gewünschten lokalen Anwendungen und Datenbanken auf virtuellen Azure-Computern bereitstellen:
	- Erstellen Sie mit SQL Server Data Tools und Visual Studio Bereitstellungspakete für vorhandene Anwendungen und Datenbanken.
	- Stellen Sie mit diesen Paketen die Anwendungen und Datenbanken auf virtuellen Azure-Computern bereit.
4. Bereitstellen von SharePoint-Anwendungen und -Datenbanken:
	- Konfigurieren Sie Sicherheitsoptionen am Endpunkt des Azure-Portals, und legen Sie einen eingehenden Port in der Windows Firewall des virtuellen Computers fest.
	- Stellen Sie SharePoint-Anwendungen und -Datenbanken auf virtuellen Azure-Computern mit den in Schritt 3 erstellten Bereitstellungspaketen und -skripts bereit.
- Testen Sie bereitgestellte Anwendungen und Datenbanken.
5. Verwalten virtueller Computer:
	- Überwachen Sie die virtuellen Computer mit dem Azure-Portal.
	- Überwachen Sie die Anwendungen mit Visual Studio und SQL Server Management Studio.
	- Sie können virtuelle Computer auch mit lokaler Verwaltungssoftware wie Microsoft System Center Operations Manager überwachen und verwalten.

### Szenario 2: Öffentliche SharePoint-Farm mit Anpassungen

Organisationen möchten eine Internetpräsenz erstellen, die in der Cloud gehostet wird und auf Basis von Bedarf und Nachfrage einfach skalierbar ist. Außerdem möchten sie Partner-Extranetwebsites für die Zusammenarbeit erstellen und ein einfaches Verfahren für die verteilte Erstellung und Genehmigung des Websiteinhalts implementieren. Schließlich möchten diese Organisationen auf ihren Websites bedarfsgesteuerte Kapazitäten vorsehen, um auf wachsende Auslastungen reagieren zu können.

In diesem Szenario wird SharePoint Server als Basis für das Hosten einer öffentlichen Website verwendet. Organisationen haben damit die Möglichkeit, ihre Geschäftswebsites in einer sicheren, skalierbaren Cloud-Infrastruktur schnell bereitzustellen, anzupassen und zu hosten. Mit öffentlichen SharePoint-Websites auf Azure können Organisationen mit dem Anwachsen des Datenverkehrs Kapazitäten skalieren und nur für das bezahlen, was sie nutzen. Allgemeine Tools, die vergleichbar mit den lokal verwendeten Tools sind, können mit SharePoint für die Inhaltserstellung, den Workflow und die Genehmigung unter Azure eingesetzt werden.

Außerdem können Organisationen mit Azure Virtual Machines Staging- oder Produktionsumgebungen, die auf virtuellen Computern laufen, leicht konfigurieren. Öffentliche, in Azure erstellte virtuelle Computer mit SharePoint können im virtuellen Speicher gesichert werden. Für die Notfallwiederherstellung ist es zusätzlich möglich, mit der fortlaufenden Georeplikationsfunktion virtuelle Computer eines Rechenzentrums automatisch in einem anderen weit entfernten Rechenzentrum zu sichern.

Virtuelle Computer in der Azure-Infrastruktur werden in Bezug auf die Zusammenarbeit mit anderen Microsoft-Produkten, wie SQL Server und SharePoint Server, überprüft und unterstützt. Azure und SharePoint Server sind eine optimale Kombination: Die Anwendungen sind sorgfältig aufeinander abgestimmt und wurden gemeinsam getestet, um eine optimale Leistung zu erzielen. Beide haben einen einzelnen Unterstützungspunkt für die SharePoint-Anwendung und die Azure-Infrastruktur.

In diesem Szenario müssen weitere Front-End-Webserver für SharePoint Server hinzugefügt werden, um zusätzlichen Datenverkehr zu unterstützen. Diese Server benötigen eine verbesserte Sicherheit und Domänencontroller der Active Directory-Domänendienste für die Benutzerauthentifizierung und -autorisierung. Abbildung 4 zeigt das Layout dieses Szenarios.

![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

**Abbildung 4: Öffentliche SharePoint-Farm mit Anpassungen**

Führen Sie die folgenden Schritte aus, um eine öffentliche SharePoint-Farm auf Azure zu implementieren:

1. Bereitstellen von Active Directory: Die Grundanforderungen für die Bereitstellung von Active Directory auf virtuellen Azure-Computern ähneln den Anforderungen für die Bereitstellung auf lokalen virtuellen Computern (und bis zu einem gewissen Grad auf physischen Computern), sind aber nicht identisch damit. Weitere Informationen zu Unterschieden, Richtlinien und anderen Aspekten finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Azure](http://msdn.microsoft.com/library/jj156090). So stellen Sie Active Directory auf Azure bereit:
	- Definieren und erstellen Sie ein virtuelles Netzwerk, in dem die virtuellen Computer bestimmten Subnetzen zugewiesen werden können.
	- Erstellen Sie im Azure-Portal den Domänencontroller auf einem neuen virtuellen Computer in Azure, und stellen Sie ihn bereit. Sie können auch anhand des Windows PowerShell-Skripts eine eigenständige Domäne in der Cloud mit Azure Virtual Machines und Azure Virtual Network bereitstellen. Weitere Informationen zum Erstellen einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Computer in Azure Virtual Network finden Sie unter [Installieren einer neuen Active Directory-Gesamtstruktur in Azure](active-directory-new-forest-virtual-machine.md).
2. Bereitstellen eines virtuellen Computers: Verwenden Sie das Azure-Portal, um einen neuen virtuellen Computer über ein Image bereitzustellen, das in der Abbildbibliothek enthalten ist.
3. Stellen Sie eine SharePoint-Farm bereit.
	- Verwenden Sie das Azure-Portal, um den Lastenausgleich zu konfigurieren. Konfigurieren Sie die Endpunkte des virtuellen Computers, wählen Sie die Option zum Laden des Ausgleichsdatenverkehrs auf einen vorhandenen Endpunkt, und geben Sie dann den Namen des virtuellen Computers mit dem Lastenausgleich an.
	- Fügen Sie der vorhandenen SharePoint-Farm einen weiteren virtuellen Front-End-Web-Computer für zusätzlichen Datenverkehr hinzu.
3. Verwalten virtueller Computer:
	- Überwachen Sie die virtuellen Computer mit dem Azure-Portal.
	- Überwachen Sie die SharePoint-Farm mit der Zentraladministration.

### Szenario 3: Horizontal hochskalierte Farm für weitere BI-Dienste

Business Intelligence (BI) ist unerlässlich, um wichtige Erkenntnisse zu gewinnen und schnelle, fundierte Entscheidungen zu treffen. Wenn Organisationen von einem lokalen Ansatz zur Bereitstellung von BI-Anwendungen in der Cloud übergehen, möchten sie keine Änderungen an der BI-Umgebung vornehmen. Berichte von SQL Server Analysis Services (SSAS) oder SQL Server Reporting Services (SSRS) sollen in einer sehr stabilen Umgebung mit hoher Verfügbarkeit gehostet werden, und dabei soll die komplette Kontrolle über die BI-Anwendung aufrechterhalten werden – und das alles mit geringem Zeit- und Kostenaufwand für die Wartung.

Dieses Szenario beschreibt, wie Organisationen mit virtuellen Azure-Computern geschäftskritische BI-Anwendungen hosten können. Organisationen können SharePoint-Farmen auf virtuellen Azure-Computern bereitstellen und die BI-Komponenten des virtuellen Anwendungsservercomputers, wie SSRS oder Excel Services, horizontal hochskalieren. Durch die Skalierung ressourcenintensiver Komponenten in der Cloud können besondere Workloads besser und einfacher unterstützt werden. SQL Server eignet sich gut für virtuelle Azure-Computer, weil SQL Server-Instanzen einfach von kleinen bis zu sehr großen Installationen skaliert werden können. Die dadurch erreichte Flexibilität ermöglicht Organisationen, BI-Instanzen auf Basis unmittelbarer Workloadanforderungen dynamisch bereitzustellen (sie zu erweitern) bzw. die Bereitstellung zurückzunehmen (sie zu verkleinern).

Die Migration vorhandener BI-Anwendungen auf Azure ermöglicht eine bessere Skalierung. Dank der hohen Leistung von SQL Server Analysis Services (SSAS), SQL Server Reporting Services (SSRS) und SharePoint Server können Organisationen leistungsfähige BI- und Berichterstellungsanwendungen und Dashboards erstellen, die zentral hoch- und herunterskaliert werden können. Diese Anwendungen und Dashboards können zudem sicherer in lokale Daten und Anwendungen integriert werden. Azure sorgt für die Rechenzentrums-Compliance mit Unterstützung für ISO 27001. Weitere Informationen finden Sie unter [Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/compliance/).

Um die Bereitstellung von BI-Komponenten horizontal hochzuskalieren, muss ein neuer Anwendungsserver mit Diensten, z. B. PowerPivot, Power View, Excel Services oder PerformancePoint Services, installiert werden. Oder der vorhandenen Farm müssen SQL Server-BI-Instanzen, wie SSAS oder SSRS, hinzugefügt werden, um zusätzliche Abfrageverarbeitungen zu unterstützen. Der Server kann als neuer virtueller Azure-Computer mit installiertem SharePoint 2010 Server oder SQL Server hinzugefügt werden. Anschließend können auf diesem Server die BI-Komponenten installiert, bereitgestellt und konfiguriert werden (Abbildung 5).

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

**Abbildung 5: Für zusätzliche BI-Dienste horizontal skalierte SharePoint-Farm**

Führen Sie die folgenden Schritte aus, um eine BI-Umgebung auf Azure horizontal zu skalieren:

1. Bereitstellung:
	- Stellen Sie mit Azure Virtual Network eine VPN-Verbindung zwischen Ihrem lokalen Standort und Azure bereit. Weitere Informationen finden Sie unter [Virtuelle Netzwerke – Übersicht](../virtual-network/virtual-networks-overview.md).
	- Stellen Sie im Azure-Portal einen neuen virtuellen Computer mithilfe eines vorhandenen Images aus der Abbildbibliothek bereit. Sie können BI-Workloadimages von SharePoint Server oder SQL Server in die Abbildbibliothek hochladen. Auf diese Weise kann jeder autorisierte Benutzer diese virtuellen BI-Komponentencomputer auswählen, um die horizontal hochskalierte Umgebung zu erstellen.
2. Installation:
	- Wenn Ihre Organisation über keine vorgefertigten Images von SharePoint Server- oder SQL Server-BI-Komponenten verfügt, installieren Sie SharePoint Server und SQL Server mit einer Remotedesktopverbindung auf den virtuellen Computern.
	- Weitere Informationen zum Installieren von SharePoint finden Sie unter [Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) oder [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Weitere Informationen zum Installieren von SQL Server finden Sie unter [Installieren von SQL Server mit SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Hinzufügen des virtuellen BI-Computers:
	- Konfigurieren Sie Sicherheitsoptionen am Endpunkt des Azure-Portals, und legen Sie einen eingehenden Port in der Windows Firewall des virtuellen Computers fest.
	- Fügen Sie den neu erstellten virtuellen BI-Computer der vorhandenen SharePoint- oder SQL Server-Farm hinzu.
4. Verwalten virtueller Computer:
	- Überwachen Sie die virtuellen Computer mit dem Azure-Portal.
	- Überwachen Sie die SharePoint-Farm mit der Zentraladministration.
	- Überwachen und verwalten Sie virtuelle Computer mit lokaler Verwaltungssoftware, wie Microsoft System Center – Operations Manager.

### Szenario 4: Vollständig angepasste SharePoint-basierte Website

Organisationen möchten zunehmend vollständig angepasste SharePoint-Websites in der Cloud erstellen. Sie benötigen eine äußerst stabile Umgebung mit hoher Verfügbarkeit und umfangreichen Wartungsmöglichkeiten komplexer Anwendungen in der Cloud ohne großen Zeit- und Kostenaufwand.

In diesem Szenario kann eine Organisation ihre gesamte SharePoint-Farm in der Cloud bereitstellen und alle Komponenten dynamisch skalieren, um zusätzliche Kapazitäten zu erhalten, oder sie kann ihre lokale Bereitstellung auf die Cloud ausweiten, um bei Bedarf die Kapazität zu erhöhen und die Leistung zu steigern. Das Szenario konzentriert sich auf Organisationen, die eine umfassende SharePoint-Leistung für die Anwendungsentwicklung und das Enterprise Content Management möchten. Komplexere Sites können auch erweiterte Berichtserstellungsfunktionen, Power View, PerformancePoint, PowerPivot, detaillierte Diagramme und die meisten anderen SharePoint-Sitefunktionen enthalten, um die gesamte Ende-zu-Ende-Funktionalität bereitzustellen.

Organisationen können mit virtuellen Azure-Computern angepasste Anwendungen und zugehörige Komponenten in einer kostengünstigen und hochsicheren Cloud-Infrastruktur hosten. Sie können auch ein lokales Microsoft System Center als gemeinsames Verwaltungstool für lokale und Cloud-Anwendungen verwenden.

Um eine vollständig angepasste SharePoint-Website auf Azure zu implementieren, muss eine Organisation eine Active Directory-Domäne in der Cloud und neue virtuelle Computer in dieser Domäne bereitstellen. Dann muss ein virtueller Computer mit SQL Server 2012 als Teil einer SharePoint-Farm erstellt und konfiguriert werden. Schließlich muss die SharePoint-Farm erstellt, die Last verteilt und die Farm mit Active Directory und SQL Server verbunden werden (Abbildung 6).

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

**Abbildung 6: Vollständig angepasste SharePoint-basierte Website**

Die folgenden Schritte zeigen, wie eine angepasste SharePoint-Farmumgebung aus vorgefertigten Images aus der Abbildbibliothek erstellt wird. Sie können aber auch virtuelle Computer einer SharePoint-Farm in die Abbildbibliothek hochladen, und autorisierte Benutzer können anhand dieser virtuellen Computer die gewünschte SharePoint-Farm auf Azure erstellen.

1. Bereitstellen von Active Directory: Die Grundanforderungen für die Bereitstellung von Active Directory auf virtuellen Azure-Computern ähneln den Anforderungen für die Bereitstellung auf lokalen virtuellen Computern (und bis zu einem gewissen Grad auf physischen Computern), sind aber nicht identisch damit. Weitere Informationen zu Unterschieden, Richtlinien und anderen Aspekten finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Azure](http://msdn.microsoft.com/library/jj156090). So stellen Sie Active Directory auf Azure bereit:
	- Definieren und erstellen Sie ein virtuelles Netzwerk, in dem die virtuellen Computer bestimmten Subnetzen zugewiesen werden können.
	- Erstellen Sie im Azure-Portal den Domänencontroller auf einem neuen virtuellen Computer in Azure, und stellen Sie ihn bereit.
	- Weitere Informationen zum Erstellen einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Computer in Azure Virtual Network finden Sie unter [Installieren einer neuen Active Directory-Gesamtstruktur in Azure](active-directory-new-forest-virtual-machine).
2. Bereitstellen von SQL Server:
	- Stellen Sie im Azure-Portal einen neuen virtuellen Computer mithilfe eines vorhandenen Images aus der Abbildbibliothek bereit.
	- Konfigurieren Sie SQL Server auf dem virtuellen Computer. Weitere Informationen finden Sie unter [Installieren von SQL Server mit SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
	- Verknüpfen Sie den virtuellen Computer mit der neu angelegten Active Directory-Domäne.
3. Bereitstellen einer SharePoint-Farm mit mehreren Servern:
	- Erstellen Sie ein virtuelles Netzwerk. Weitere Informationen finden Sie unter [Virtuelle Netzwerke – Übersicht](../virtual-network/virtual-networks-overview.md).
	- Wenn virtuelle SharePoint-Computer bereitgestellt werden, müssen Subnetze für SharePoint Server vorhanden sein, damit die DNS-Adressen in dem lokalen Active Directory während der Bereitstellung verfügbar sind.
	- Verwenden Sie das Azure-Portal, um einen virtuellen Computer zu erstellen.
	- Installieren Sie SharePoint Server auf diesem virtuellen Computer, und generieren Sie ein wiederverwendbares Image. Weitere Informationen zum Installieren von SharePoint Server finden Sie unter [Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) oder [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Konfigurieren Sie den virtuellen SharePoint-Computer so, dass die SharePoint-Farm erstellt und mit dem Befehl [Join-SharePointFarm](http://technet.microsoft.com/library/ff607979.aspx) eine Verbindung damit hergestellt wird.
	- Konfigurieren Sie den Lastenausgleich im Azure-Portal: Konfigurieren Sie die Endpunkte des virtuellen Computers, wählen Sie die Option für den Lastenausgleich des Datenverkehrs auf einem vorhandenen Endpunkt, und geben Sie dann den Namen des virtuellen Computers mit Lastenausgleichsmodul an.
4. Verwalten der SharePoint-Farm im System Center:
	- Verbinden Sie Ihr lokales System Center mit dem Operations Manager-Agent und dem neuen Azure-Integrationspaket mit virtuellen Azure-Computern.
	- Verwenden Sie den lokalen App Controller und Orchestrator für die Verwaltungsfunktionen.

## Zusammenfassung

Virtuelle Azure-Computer bieten ein vollständiges Kontinuum von SharePoint-Bereitstellungen. Azure Virtual Machines werden komplett unterstützt und ausgiebig getestet, um zusammen mit anderen Microsoft-Anwendungen eine optimale Leistung zu liefern. Daher können Organisationen SharePoint Server in Azure einfach konfigurieren und bereitstellen, entweder um eine Infrastruktur für eine neue SharePoint-Bereitstellung einzurichten oder um eine vorhandene zu erweitern. Wenn Geschäftsworkloads zunehmen, können Organisationen ihre SharePoint-Infrastruktur schnell erweitern. Ebenso lassen sich bei sinkendem Workload Ressourcen nach Bedarf verringern, wobei nur Kosten für tatsächlich genutzte Ressourcen anfallen. Virtuelle Azure-Computer bieten eine außergewöhnliche Infrastruktur für eine breite Palette von Geschäftsanforderungen, wie anhand der vier SharePoint-basierten Szenarien in diesem Artikel gezeigt wurde.

Voraussetzung für eine erfolgreiche Bereitstellung von SharePoint Server auf virtuellen Azure-Computern ist eine solide Planung, vor allem in Bezug auf die kritischen Farmarchitektur- und Bereitstellungsoptionen. Die in diesem Artikel erläuterten Erkenntnisse und bewährten Methoden können Sie als Entscheidungshilfe bei der Implementierung einer fundierten SharePoint-Bereitstellung heranziehen.

## Zusätzliche Ressourcen

[In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen](virtual-machines-sharepoint-infrastructure-services.md)

[Azure-Infrastrukturdienste-Workload: SharePoint-Intranetfarm](virtual-machines-workload-intranet-sharepoint-farm)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Test the SharePoint Server 2016 IT Preview in Azure (auf Englisch)](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)

<!---HONumber=AcomDC_1125_2015-->