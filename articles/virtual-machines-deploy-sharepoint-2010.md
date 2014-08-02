<properties linkid="manage-windows-other-resource-sharepoint-wp" urlDisplayName="SharePoint on Azure" pageTitle="SharePoint 2010 Deployment on Azure Virtual Machines" metaKeywords="" description="Understand the supported scenarios for using SharePoint 2010 on Azure virtual machines." metaCanonical="" services="virtual-machines" documentationCenter="" title="SharePoint Deployment on Azure Virtual Machines" authors="" solutions="" manager="" editor="" />

SharePoint-Bereitstellung auf Azure Virtual Machines
====================================================

Kurzfassung
-----------

Microsoft SharePoint Server 2010 bietet eine umfangreiche Bereitstellungsflexibilität, die Organisationen bei der Ermittlung der richtigen Einsatzszenarien für ihre geschäftlichen Anforderungen und Ziele unterstützen kann. Das Angebot von Azure Virtual Machines, das in der Cloud gehostet und verwaltet wird, umfasst eine vollständige, zuverlässige und verfügbare Infrastruktur für die Unterstützung zahlreicher On-Demand-Anwendungs- und Datenbankarbeitsauslastungen, wie Microsoft SQL Server- und SharePoint-Bereitstellungen.

Azure Virtual Machines unterstützen mehrere Arbeitsauslastungen, dieses Dokument konzentriert sich aber auf SharePoint-Bereitstellungen. Mit Azure Virtual Machines können Organisationen ihre SharePoint-Infrastruktur mit geringem Zeitaufwand erstellen und verwalten – die Bereitstellung und der Zugriff auf fast jeden Host weltweit wird dadurch möglich, genauso wie eine vollständige Kontrolle und Verwaltung von Prozessoren, RAM, CPU-Bereichen und anderen Ressourcen von virtuellen SharePoint-Computern (VMs).

Azure Virtual Machines senken den Hardwarebedarf, sodass Organisationen sich dem Erstellen und Verwalten einer maßgerechten Infrastruktur widmen können, anstatt sich um hohe Vorlaufkosten und Komplexität kümmern zu müssen. Das bedeutet, dass Innovationen, Experimente und Iterationen in Stunden durchgeführt werden können, im Gegensatz zu Tagen und Wochen, wie das bei herkömmlichen Bereitstellungen der Fall ist.

### Zielgruppe dieses Dokuments

Dieses Dokument richtet sich an IT-Experten. Darüber hinaus können technische Entscheidungsträger wie Architekten und Systemadministratoren, anhand dieser Informationen und der vorgestellten Szenarien eine virtualisierte SharePoint-Infrastruktur auf Azure planen und entwerfen.

### Zweck dieses Dokuments

Dieses Dokument erläutert, wie Organisationen SharePoint in Azure Virtual Machines konfigurieren und bereitstellen können. Die Vorteile dieser Bereitstellungsart für Organisationen unterschiedlicher Größe werden ebenfalls behandelt.

Umstieg auf Cloud-Computing
---------------------------

Gartner definiert Cloud-Computing als eine „Art von Computing, bei der stark skalierbare IT-basierte Funktionen für externe Kunden mit Internettechnologien 'als Dienst' bereitgestellt werden“. Die zentralen Begriffe in dieser Definition sind „skalierbar“, „Dienst“ und „Internet“. Zusammengefasst kann Cloud-Computing als IT-Dienste definiert werden, die **über das Internet bereitgestellt und geliefert werden** und **bei Bedarf skalierbar** sind.

Cloud-Computing stellt zweifellos eine der wesentlichsten Veränderungen in der heutigen IT dar. In der Vergangenheit waren die zentralen Aspekte Konsolidierung und Kosten. Heute geht es um die neue Klasse von Leistungen, die Cloud-Computing erbringen kann. Es geht um die Änderung der Art und Weise, wie die IT durch die Nutzung einer neuen Leistungsart Organisationen unterstützt. Cloud-Computing ändert die IT-Welt grundlegend – mit Auswirkungen auf alle Rollen vom Dienstanbieter und Systemarchitekten bis hin zum Entwickler und Endbenutzer.

Die Forschung zeigt, dass Agilität, Fokus und wirtschaftliche Aspekte die drei Haupttriebkräfte für Cloud-Initiativen sind:

-   **Agilität**: Cloud-Computing kann Organisationen dabei unterstützen, schnell von neuen Chancen zu profitieren und auf Änderungen von Geschäftsanforderungen zu reagieren.

-   **Fokus**: Cloud-Computing ermöglicht IT-Abteilungen eine deutliche Senkung der Infrastrukturkosten. Die Infrastruktur wird abstrahiert und Ressourcen werden gebündelt, sodass die IT eher wie ein Hilfsprogramm als eine Sammlung von komplizierten Diensten und Systemen ausgeführt wird. Außerdem kann die IT jetzt in innovativere und strategischere Rollen übergeführt werden.

-   **Wirtschaftliche Aspekte**: Cloud-Computing reduziert die Kosten für die Bereitstellung der IT und erhöht die Auslastung und Effizienz des Rechenzentrums. Bereitstellungskosten sinken, weil durch Cloud-Computing Anwendungen und Ressourcen zu einem Self-Service werden und die Verwendung dieser Ressourcen auf eine neue und präzise Art gemessen werden kann. Auch die Hardwarenutzung steigt, weil jetzt Infrastrukturressourcen (Speicher, Compute Services und Netzwerk) gebündelt und abstrahiert sind.

Breitstellungsmodelle für Cloud-Dienste
---------------------------------------

Einfach ausgedrückt ist Cloud-Computing die Abstraktion von IT-Diensten. Diese Dienste können von der Basisinfrastruktur bis zu vollständigen Anwendungen reichen. Endbenutzer fordern und nutzen abstrahierte Dienste ohne Verwaltungsbedarf (oder sogar ohne Kenntnis davon), und genau das macht diese Dienste aus. Die Branche kennt heute drei Breitstellungsmodelle für Cloud-Dienste, die jeweils einen anderen Kompromiss zwischen Kontrolle/Flexibilität und Gesamtkosten bieten:

-   **Infrastructure-as-a-Service** (IaaS): Virtuelle Infrastruktur, die virtuelle Computer und größtenteils vorhandene Anwendungen hostet.

-   **Platform-as-a-Service** (PaaS): Cloud-Anwendungsinfrastruktur, die eine bedarfsgesteuerte Anwendungshostumgebung bereitstellt.

-   **Software-as-a-Service** (SaaS): Cloud-Dienstmodell, bei dem eine Anwendung über das Internet bereitgestellt wird und Kunden auf Nutzungsbasis bezahlen (z. B. Microsoft Office 365 oder Microsoft CRM Online).

Abbildung 1 zeigt die Systematik von Cloud-Diensten und wie sie zu den Komponenten in einer IT-Infrastruktur zugeordnet werden. Bei einem lokalen Modell ist der Kunde für die Verwaltung des gesamten Stapels von der Netzwerkverbindung bis hin zu den Anwendungen verantwortlich. Bei IaaS werden die unteren Ebenen des Stapels von einem Anbieter verwaltet. Der Kunde ist für die Verwaltung des Betriebssystems und der Anwendungen verantwortlich. Bei PaaS stellt ein Plattformanbieter alle Komponenten von der Netzwerkverbindung bis zur Laufzeitumgebung bereit und verwaltet diese auch. Der Kunde muss nur Anwendungen und Daten verwalten. (Das Azure-Angebot passt am besten in dieses Modell.) Beim letzten Modell, SaaS, stellt ein Anbieter die Anwendungen bereit und abstrahiert alle Dienste aller zugrunde liegenden Komponenten.

Abbildung 1: Systematik von Cloud-Diensten

![azure-sharepoint-wp-1](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-1.png)

Azure Virtual Machines
----------------------

Azure Virtual Machines führt Funktionen ein, die eine vollständige Kontrolle und Verwaltung von VMs sowie einen extensiven virtuellen Netzwerkbetrieb ermöglichen. Durch dieses Angebot können Organisationen strategische Vorteile erzielen:

-   **Verwaltung**: Zentrales Verwalten von VMs in der Cloud mit vollständiger Kontrolle der Konfiguration und Wartung der Infrastruktur.

-   **Anwendungsmobilität**: Verschieben von virtuellen Festplatten (VHDs) zwischen lokalen und cloudbasierten Umgebungen. Anwendungen müssen für die Ausführung in der Cloud nicht neu erstellt werden.

-   **Zugriff auf Microsoft-Serveranwendungen**: Ausführen der gleichen lokalen Anwendungen und Infrastruktur in der Cloud, einschließlich Microsoft SQL Server, SharePoint Server, Windows Server und Active Directory.

Azure Virtual Machines ist eine einfache, offene und flexible sowie leistungsstarke Plattform, mit der Organisationen Windows Server- und Linux-VMs im Handumdrehen bereitstellen und ausführen können:

-   **Einfach**: Mit Azure Virtual Machines können VMs sehr einfach in der Cloud erstellt, migriert, bereitgestellt und verwaltet werden. Organisationen können Arbeitsauslastungen auf Azure migrieren, ohne dass vorhandener Code geändert werden müsste, oder sie können mit nur ein paar Klicks neue VMs in Azure einrichten. Das Angebot umfasst auch die Unterstützung bei der Entwicklung neuer Cloud-Anwendungen durch Integration der IaaS- und PaaS-Funktionen von Azure.

-   **Offen und flexibel**: Azure ist eine offene Plattform mit hoher Flexibilität für Organisationen. Sie können mit einem vorgefertigten Image aus der Abbildbibliothek starten oder angepasste und lokale VHDs erstellen und verwenden und diese in die Abbildbibliothek hochladen. Community- und kommerzielle Versionen von Linux sind auch verfügbar.

-   **Leistungsstark**: Azure ist eine unternehmensfähige Cloud-Plattform für die Ausführung von Anwendungen wie SQL Server, SharePoint Server oder Active Directory in der Cloud. Organisationen können hybride lokale und Cloud-Lösungen mit VPN-Konnektivität zwischen dem Azure-Rechenzentrum und eigenen Netzwerken einrichten.

SharePoint auf Azure Virtual Machines
-------------------------------------

SharePoint 2010 unterstützt auf flexible Weise die meisten Arbeitsauslastungen in einer Azure Virtual Machines-Bereitstellung. Azure Virtual Machines passen bestens zu FIS-(SharePoint Server for Internet Sites-) und Entwicklungsszenarien. Kernarbeitsauslastungen von SharePoint werden ebenfalls unterstützt. Wenn eine Organisation ihre eigene SharePoint 2010-Implementierung bei gleichzeitiger Nutzung der Virtualisierungsoptionen in der Cloud verwalten und steuern möchte, ist Azure Virtual Machines das ideale Bereitstellungsinstrument.

Das Azure Virtual Machines-Angebot wird in der Cloud gehostet und verwaltet. Für Flexibilität bei der Bereitstellung ist gesorgt, und Kosten werden durch Verringern der Investitionsaufwendungen für die Hardwarebeschaffung gesenkt. Bei größerer Agilität der Infrastruktur können Organisationen SharePoint Server in Stunden anstatt in Tagen oder Wochen bereitstellen. Darüber hinaus ermöglicht Azure Virtual Machines, Organisationen SharePoint-Arbeitsauslastungen in der Cloud mit einem „nutzungsbasierten Gebührenmodell“ bereitzustellen. Wenn die SharePoint-Arbeitsauslastungen zunehmen, können Organisationen sofort die Infrastruktur erweitern; sinkt der Computing-Bedarf können die nicht mehr benötigten Ressourcen zurückgegeben werden, und es müssen nur die verwendeten Ressourcen bezahlt werden.

### Verschiebung des IT-Fokus

Viele Organisationen lagern allgemeine Komponenten ihrer IT-Infrastruktur und -Verwaltung aus, wie z. B. Hardware, Betriebssysteme, Sicherheit, Datenspeicherung und Backup, behalten aber die Kontrolle über geschäftskritische Anwendungen, wie SharePoint Server. Durch die Delegierung aller nicht geschäftskritischen Dienstebenen ihrer IT-Plattformen an einen virtuellen Anbieter können Organisationen ihren IT-Fokus auf zentrale, geschäftskritische SharePoint-Dienste richten und mit SharePoint-Projekten zur Wertschöpfung beitragen, anstatt Zeit für die Konfiguration der Infrastruktur aufzuwenden.

### Schnellere Bereitstellung

Die Unterstützung und Bereitstellung einer großen SharePoint-Infrastruktur kann den schnellen Einsatz der IT für Geschäftsanforderungen behindern. Abhängig von den Verfahren und Einschränkungen der Organisation kann es Wochen oder sogar Monate dauern, um SharePoint-Server und -Farmen zu erstellen, zu testen und vorzubereiten sowie sie in einer Produktionsumgebung bereitzustellen. Azure Virtual Machines ermöglicht Organisationen, ihre SharePoint-Arbeitsauslastungen ohne Investitionsaufwendungen für Hardware schnell bereitzustellen. So können Organisationen die Infrastrukturagilität nutzen, um Bereitstellungen innerhalb von Stunden anstatt von Tagen oder Wochen zu bewerkstelligen.

### Skalierbarkeit

Ohne die Notwendigkeit physische SharePoint-Server und -Farmen bereitzustellen, zu testen und vorzubereiten, können Organisationen bei Bedarf Rechenkapazität jederzeit erweitern und verringern. Wenn die Anforderungen an die SharePoint-Arbeitsauslastung anwachsen, kann eine Organisation ihre Infrastruktur in der Cloud rasch erweitern. Ebenso kann die Organisation Ressourcen verringern, wenn der Computing-Bedarf abnimmt, und nur für die tatsächlich genutzten Ressourcen fallen Kosten an. Azure Virtual Machines reduzieren Vorlaufkosten und langfristige Verpflichtungen, indem sie Organisationen ermöglichen, maßgerechte SharePoint-Infrastrukturen zu erstellen und zu verwalten. Auch hier bedeutet dies, dass Organisationen Innovationen, Experimente und Iterationen in Stunden durchführen können, im Gegensatz zu Tagen und Wochen, wie das bei herkömmlichen Bereitstellungen der Fall ist.

### Nutzungsbezogene Gebühren

Azure Virtual Machines liefern Rechenleistung, Arbeitsspeicher und Speicher für SharePoint-Szenarien, deren Preise in der Regel auf dem Ressourcenverbrauch basieren. Für Organisationen fallen nur für die genutzten Ressourcen Kosten an, und der Dienst liefert die gesamte für den Betrieb der SharePoint-Infrastruktur benötigte Kapazität. Weitere Informationen zur Preisgestaltung und Gebührenabrechnung finden Sie unter [Azure-Preisübersicht](/en-us/pricing/details/). Beachten Sie bitte, dass minimale Gebühren für das Speichern und Verschieben von Daten aus der Azure-Cloud in ein lokales Netzwerk anfallen. Für das Hochladen von Daten auf Azure werden aber keine Gebühren berechnet.

### Flexibilität

Mit Azure Virtual Machines erhalten Entwickler die Flexibilität, ihre bevorzugte Sprache oder Laufzeitumgebung zu wählen, mit offizieller Unterstützung von .NET, Node.js, Java und PHP. Entwickler können zudem ihre Tools wählen, mit Unterstützung von Microsoft Visual Studio, WebMatrix, Eclipse und Texteditoren. Darüber hinaus bietet Microsoft einen kostensparenden, risikoarmen Pfad zur Cloud und durch den Zugang zu Business Intelligence (BI) für Geräte und Standorte eine kostengünstige, einfache Bereitstellung für Cloud-Berichterstellungsfunktionen. Schließlich können Benutzer mit dem Azure-Angebot nicht nur VHDs in die Cloud verschieben, sondern auch eine VHD zurückkopieren und sie lokal oder über einen anderen Cloud-Anbieter ausführen, solange sie über die geeignete Lizenz dafür verfügen.

Bereitstellung
--------------

Dieser Unterabschnitt behandelt die Grundlagen der Bereitstellung in Azure. Die **Abbildbibliothek** in Azure enthält eine Liste der verfügbaren vorkonfigurierten VMs. Benutzer können ISO/VHDs von SharePoint Server, SQL Server, Windows Server und anderen in der Abbildbibliothek veröffentlichen. Um die Erstellung von VMs zu erleichtern, werden Basis-Images angelegt und in der Bibliothek veröffentlicht. Autorisierte Benutzer können anhand dieser Images die gewünschte VM generieren. Weitere Informationen finden Sie auf der Azure-Site unter [Erstellen eines virtuellen Computers, auf dem Windows Server 2008 R2 ausgeführt wird](/en-us/manage/windows/tutorials/virtual-machine-from-gallery/). Abbildung 2 zeigt die grundlegenden Schritte, um eine VM mit dem Azure-Verwaltungsportal zu erstellen:

Abbildung 2: Übersicht über die Schritte zum Erstellen einer VM

![azure-sharepoint-wp-2](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

Benutzer können im Azure-Verwaltungsportal auch ein vom System erstelltes Image hochladen. Weitere Informationen finden Sie unter [Erstellen und Hochladen einer virtuellen Festplatte](/en-us/manage/windows/common-tasks/upload-a-vhd/). Abbildung 3 zeigt die grundlegenden Schritte für das Hochladen eines Image, um eine VM zu erstellen:

Abbildung 3: Übersicht über die Schritte zum Hochladen eines Image

![azure-sharepoint-wp-3](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

### Bereitstellen von SharePoint 2010 auf Azure

Sie können SharePoint 2010 auf Azure mit den folgenden Schritten bereitstellen:

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal](http://manage.windowsazure.com/) mit Ihrem Konto an.
    -   Wenn Sie kein Azure-Konto haben, [melden Sie sich für eine kostenlose Testversion von Azure an](http://www.windowsazure.com/en-us/pricing/free-trial/).

2.  Erstellen Sie eine VM mit einem Basisbetriebssystem: Klicken Sie im Azure-Verwaltungsportal auf **+NEU**, auf **VIRTUELLER COMPUTER** und dann auf **AUS KATALOG**. ![azure-sharepoint-wp-4](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-4.png)
3.  Das Dialogfeld **Image auswählen** wird angezeigt. Klicken Sie auf **Plattform-Images**, und markieren Sie das Plattform-Image **Windows Server 2008 R2 SP1**. ![azure-sharepoint-wp-5](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-5.png)
4.  Das Dialogfeld ***Konfiguration des virtuellen Computers*** wird angezeigt. Geben Sie die folgenden Informationen ein:
    -   Geben Sie einen **NAMEN DES VIRTUELLEN COMPUTERS** ein.
        -   Dieser Computername muss global eindeutig sein.
    -   Belassen Sie im Feld **NEUER BENUTZERNAME** den Eintrag „Administrator“.
    -   Geben Sie in das Feld **NEUES KENNWORT** ein sicheres Kennwort ein.
    -   Geben Sie das Kennwort im Feld **BESTÄTIGEN** nochmals ein.
    -   Wählen Sie die passende **GRÖSSE** aus.
        -   Für eine Produktionsumgebung (SharePoint-Anwendungsserver und -Datenbank) empfehlen wir die Größe *(4 Kerne, 7 GB Arbeitsspeicher)*.

    ![azure-sharepoint-wp-6](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-6.png)
5.  Das Dialogfeld ***Modus des virtuellen Computers*** wird angezeigt. Geben Sie die folgenden Informationen ein:
    -   Wählen Sie „Eigenständiger virtueller Computer“.
    -   Geben Sie in das Feld **DNS NAME** den ersten Teil eines DNS-Namens Ihrer Wahl ein.
        -   Dadurch entsteht insgesamt ein Name im Format „MyService1.cloudapp.net“.
    -   Wählen Sie im Feld **SPEICHERKONTO** eine der folgenden Möglichkeiten aus:
        -   Wählen Sie ein Speicherkonto aus, in dem die VHD-Datei gespeichert wird.
        -   Wählen Sie aus, dass ein automatisch generiertes Speicherkonto verwendet werden soll.
            -   Es wird automatisch nur ein Speicherkonto pro Region erstellt. Alle anderen mit dieser Einstellung erstellten VMs werden in dieses Speicherkonto aufgenommen.
            -   Es sind maximal 20 Speicherkonten möglich.
            -   Weitere Informationen finden Sie unter [Erstellen eines Speicherkontos in Azure](/en-us/manage/windows/common-tasks/upload-a-vhd/#createstorage).
    -   Wählen Sie im Feld **REGION/AFFINITÄTSGRUPPE/VIRTUELLES NETZWERK** die Region aus, in der das virtuelle Image gehostet wird.

    ![azure-sharepoint-wp-7](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-7.png)
6.  Das Dialogfeld ***Optionen des virtueller Computers*** wird angezeigt. Geben Sie die folgenden Informationen ein:
    -   Wählen Sie im Feld **VERFÜGBARKEITSGRUPPE** den Eintrag „(Keine)“.
    -   Lesen Sie sich die Bedingungen durch, und bestätigen Sie diese.
    -   Klicken Sie auf das Häkchen, um die VM zu erstellen.

    ![azure-sharepoint-wp-8](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-8.png)
7.  Die Seite ***Virtueller Computer - Instanzen*** wird angezeigt. Überprüfen Sie, ob Ihre VM erfolgreich erstellt wurde.

	![azure-sharepoint-wp-9](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-9.png)

8.  Schließen Sie die Konfiguration der VM ab:
    -   Öffnen Sie die VM mit Remotedesktop.
    -   Wählen Sie im Azure-Verwaltungsportal Ihre VM aus, und wählen Sie dann die Seite **DASHBOARD** aus.
    -   Klicken Sie auf **Verbinden**.

    ![azure-sharepoint-wp-10](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-10.png)
9.  Erstellen Sie die SQL Server-VM mit einer der folgenden Optionen:
    -   Erstellen Sie anhand der Schritte 1 bis 7 eine SQL Server 2012-VM. Verwenden Sie aber in **Schritt 3** das SQL Server 2012-Image anstelle des Windows Server 2008 R2 SP1-Image. Weitere Informationen finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](/en-us/manage/windows/common-tasks/install-sql-server/).
        -   Wenn Sie diese Option auswählen, wird bei der Bereitstellung eine Kopie der SQL Server 2012-Setupdateien im Verzeichnis *C:\\SQLServer\_11.0\_Full* gespeichert, damit Sie die Installation anpassen können. Sie können beispielsweise die Evaluierungsinstallation von SQL Server 2012 mit Ihrem Lizenzschlüssel in eine lizenzierte Version umwandeln.
    -   Mit dem SQL Server-Systemvorbereitungstool (SysPrep) können Sie SQL Server auf der VM mit einem Basisbetriebssystem installieren (siehe Schritte 1 bis 7). Weitere Informationen finden Sie unter [Installieren von SQL Server 2012 mit SysPrep](http://msdn.microsoft.com/en-us/library/ee210664.aspx).
    -   Installieren Sie SQL Server über die Eingabeaufforderung. Weitere Informationen finden Sie unter [Installieren von SQL Server 2012 von der Eingabeaufforderung aus](http://msdn.microsoft.com/en-us/library/ms144259.aspx#SysPrep).
    -   Mit unterstützten SQL Server-Medien und Ihrem Lizenzschlüssel können Sie SQL Server auf der VM mit einem Basisbetriebssystem installieren (siehe Schritte 1 bis 7).

10. Erstellen Sie die SharePoint-Farm anhand der folgenden Unterschritte:
    -   Unterschritt 1: Konfigurieren Sie das Azure-Abonnement mit Skriptdateien.
    -   Unterschritt 2: Stellen Sie SharePoint-Server bereit, indem Sie eine weitere VM mit einem Basisbetriebssystem erstellen (siehe Schritte 1 bis 7). Um einen SharePoint-Server auf dieser VM zu erstellen, wählen Sie eine der folgenden Optionen:
        -   Bereitstellen mit der SharePoint-GUI:
            -   Informationen zum Erstellen und Bereitstellen einer SharePoint-Farm finden Sie unter [Erstellen einer Microsoft SharePoint-Serverfarm](http://technet.microsoft.com/en-us/library/ee805948.aspx#CreateConfigure).
            -   Informationen zum Hinzufügen eines Web- oder Anwendungsservers zu der Farm finden Sie unter [Hinzufügen von Web- oder Anwendungsservern zu Farmen (SharePoint Server 2010)](http://technet.microsoft.com/en-us/library/cc261752.aspx).
            -   Informationen zum Hinzufügen eines Datenbankservers zu einer vorhandenen Farm finden Sie unter [Hinzufügen eines Datenbankservers zu einer vorhandenen Farm](http://technet.microsoft.com/en-us/library/cc262781).

                -   Um SQL Server 2012 für Ihre SharePoint-Farm zu verwenden, müssen Sie Service Pack 1 für SharePoint Server 2010 nach der Installation der Anwendung, und ohne den Server zu konfigurieren, herunterladen und installieren. Weitere Informationen finden Sie unter [Service Pack 1 für SharePoint Server 2010](http://www.microsoft.com/en-us/download/details.aspx?id=26623).
                -   Um die Vorteile der BI-Funktionen von SQL Server zu nutzen, sollten Sie SharePoint Server als Serverfarm anstatt als einen eigenständigen Server installieren. Weitere Informationen finden Sie unter [Installieren von SQL Server 2012 Business Intelligence-Funktionen](http://technet.microsoft.com/en-us/library/hh231681(v=sql.110).aspx).

        -   Bereitstellen mit Microsoft Windows PowerShell: Sie können mit dem Befehlszeilentool Psconfig als alternative Schnittstelle verschiedene Vorgänge zur Steuerung der Bereitstellung von SharePoint 2010-Produkten ausführen. Weitere Informationen finden Sie unter [Psconfig-Befehlszeilenreferenz](http://technet.microsoft.com/en-us/library/cc263093.aspx).
    -   Unterschritt 3: Konfigurieren Sie SharePoint. Wenn sich alle SharePoint-VMs im Bereit-Zustand befinden, konfigurieren Sie SharePoint Server auf jedem Server anhand einer der folgenden Optionen:
        -   Konfigurieren von SharePoint mithilfe der GUI.
        -   Konfigurieren von SharePoint mithilfe von Windows PowerShell. Weitere Informationen finden Sie unter [Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell](http://technet.microsoft.com/en-us/library/cc262839.aspx).
            -   Sie können auch AutoSPInstaller aus dem CodePlex-Projekt verwenden, das aus Windows PowerShell-Skripten, einer XML-Eingabedatei und einer Microsoft Windows-Standardbatchdatei besteht. AutoSPInstaller stellt ein Framework für ein SharePoint 2010-Installationsskript auf Grundlage von Windows PowerShell bereit. Weitere Informationen finden Sie unter [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/). **Hinweis**: Konfigurieren Sie Sicherheitsoptionen am Endpunkt des Verwaltungsportals, und legen Sie einen eingehenden Port in der Windows Firewall der VM fest. Überprüfen Sie anschließend, ob Sie eine Remotesitzung mit Windows PowerShell für einen der SharePoint-Anwendungsserver starten können, indem Sie eine Windows PowerShell-Sitzung mit Administratoranmeldeinformationen öffnen.

11. Stellen Sie nach Beendigung des Skripts eine Verbindung zu der VM über das VM-Dashboard her.
12. Überprüfen Sie die SharePoint-Konfiguration: Melden Sie sich beim SharePoint-Server an, und überprüfen Sie mit der Zentraladministration die Konfiguration.

### Erstellen und Hochladen einer virtuellen Festplatte

Sie können auch eigene Images erstellen und sie auf Azure als VHD-Datei hochladen. Führen Sie die folgenden Schritte aus, um eine VHD-Datei zu erstellen und auf Azure hochzuladen:

1.  Erstellen eines Hyper-V-fähigen Image: Verwenden Sie den Hyper-V-Manager, um die Hyper-V-fähige VHD zu erstellen. Weitere Informationen finden Sie unter [Erstellen virtueller Festplatten](http://technet.microsoft.com/en-us/library/cc742509).
2.  Erstellen eines Speicherkontos in Azure: Ein Speicherkonto in Azure ist erforderlich, um eine VHD-Datei hochzuladen, mit der eine VM erstellt werden kann. Dieses Konto kann im Azure-Verwaltungsportal erstellt werden. Weitere Informationen finden Sie unter [Erstellen eines Speicherkontos in Azure](/en-us/manage/windows/common-tasks/upload-a-vhd/).
3.  Vorbereiten des hochzuladenden Image: Bevor das Image auf Azure hochgeladen werden kann, muss es mit dem Befehl „SysPrep“ generalisiert werden. Weitere Informationen finden Sie unter [How to Use SysPrep: An Introduction](http://technet.microsoft.com/en-us/library/bb457073.aspx).
4.  Hochladen des Image auf Azure: Um ein Image hochzuladen, das in einer VHD-Datei enthalten ist, müssen Sie ein Verwaltungszertifikat erstellen und installieren. Rufen Sie den Fingerabdruck des Zertifikats und die Abonnement-ID ab. Legen Sie die Verbindung fest, und laden Sie die VHD-Datei mit dem Befehlszeilentool CSUpload hoch. Weitere Informationen finden Sie unter [Hochladen des Image auf Azure](/en-us/manage/windows/common-tasks/upload-a-vhd/).

Verwendungsszenarien
--------------------

Dieser Abschnitt behandelt einige führende Kundenszenarien für SharePoint-Bereitstellungen mit Azure Virtual Machines. Jedes Szenario ist in zwei Teile gegliedert – eine kurze Beschreibung des Szenarios gefolgt von Schritten für den Einstieg.

### Szenario 1: Einfache SharePoint-Bereitstellung und -Testumgebung

#### Beschreibung

Organisationen suchen nach agileren Möglichkeiten, um SharePoint-Anwendungen zu erstellen und SharePoint-Umgebungen für die Bereitstellung und den Test, sowohl vor Ort als auch ausgelagert, zu konfigurieren. Im Grunde möchten sie die Zeit für das Einrichten von SharePoint-Anwendungsentwicklungsprojekten verkürzen und Kosten durch Nutzung der Testumgebungen senken. Eine Organisation möchte beispielsweise einen bedarfsgesteuerten Auslastungstest auf SharePoint Server und Benutzerakzeptanztests (UAT) mit mehr gleichzeitigen Benutzern an unterschiedlichen geografischen Standorten durchführen. Zudem ist für viele Organisationen die Integration von lokalen/externen Teams ein zunehmend wichtiges Geschäftserfordernis.

Dieses Szenario beschreibt, wie Organisationen vorkonfigurierte SharePoint-Farmen für Entwicklungs- und Testarbeitsauslastungen einsetzen können. Eine SharePoint-Bereitstellungstopologie unterscheidet sich nicht von einer lokalen virtualisierten Bereitstellung. Vorhandene IT-Fähigkeiten können 1:1 für eine Azure Virtual Machines-Bereitstellung eingesetzt werden, mit dem großen Vorteil, einer fast vollständigen Kostenverschiebung von Investitionsaufwendungen zu operativen Aufwendungen – es müssen dafür keine physischen Server angeschafft werden. Organisationen können Investitionskosten für Serverhardware vermeiden und größere Flexibilität erzielen, indem die Dauer für das Erstellen, Konfigurieren oder Erweitern einer SharePoint-Farm für eine Test- oder Entwicklungsumgebung erheblich reduziert wird. Die IT kann je nach Test- und Entwicklungsbedarf Kapazitäten dynamisch hinzufügen und entfernen. Zudem kann sich die IT mehr auf die Wertschöpfung durch SharePoint-Projekte konzentrieren als auf die Verwaltung der Infrastruktur.

Um Auslastungstest-Computer vollständig zu nutzen, können Organisationen virtualisierte SharePoint-Entwicklungs- und Testcomputer auf Azure mit Betriebssystemunterstützung für Windows Sever 2008 R2 konfigurieren. Dies ermöglicht Entwicklungsteams, Anwendungen zu erstellen und zu testen und auf einfache Weise ohne Codeänderungen auf lokale oder Cloud-Produktionsumgebungen zu migrieren. Die gleichen Frameworks und Toolsets können vor Ort und in der Cloud verwendet werden, sodass verteilten Teams der Zugang zu derselben Umgebung ermöglicht wird. Benutzer können durch Herstellen einer direkten VPN-Verbindung auch auf lokale Daten und Anwendungen zugreifen.

#### Erste Schritte

Abbildung 4 zeigt eine SharePoint-Entwicklungs- und Testumgebung in einer Azure-VM. Um diese Bereitstellung zu erstellen, verwenden Sie zunächst dieselbe lokale SharePoint-Entwicklungs- und Testumgebung, in der Sie Anwendungen entwickeln. Laden Sie anschließend die Anwendungen in die Azure-VM hoch, und stellen Sie sie bereit, um Tests und weitere Entwicklungen durchzuführen. Wenn Ihre Organisation entscheidet, die Anwendung wieder lokal zu nutzen, kann dies ohne die Änderung der Anwendung geschehen.

Abbildung 4: SharePoint-Entwicklungs- und Testumgebung in Azure Virtual Machines

![azure-sharepoint-wp-11](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

#### Konfigurieren der Umgebung für das Szenario

Führen Sie die folgenden Schritte aus, um eine SharePoint-Entwicklungs- und Testumgebung auf Azure zu implementieren:

1.  *Bereitstellen*: Stellen Sie zuerst mit Azure Virtual Network eine VPN-Verbindung zwischen Ihrem Standort und Azure bereit. (Da hier nicht Active Directory verwendet wird, ist ein VPN-Tunnel erforderlich.) Weitere Informationen finden Sie unter [Virtuelle Netzwerke im Überblick (Entwurfsüberlegungen, Sichere Verbindungsszenarien)](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx). Stellen Sie dann im Verwaltungsportal eine neue VM mithilfe eines vorhandenen Image aus der Abbildbibliothek bereit.
    -   Sie können die lokalen SharePoint-Entwicklungs- und Test-VMs in Ihr Azure-Speicherkonto hochladen und über die Abbildbibliothek auf diese VMs verweisen, um die erforderliche Umgebung zu erstellen.
    -   Sie können das SQL Server 2012-Image anstelle des Windows Server 2008 R2 SP1-Image verwenden. Weitere Informationen finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](/en-us/manage/windows/common-tasks/install-sql-server/).

2.  *Installieren*: Installieren Sie mithilfe einer Remotedesktopverbindung SharePoint Server, Visual Studio und SQL Server auf den VMs.
    -   Wählen Sie für die Installation von SharePoint Server eine Option:
        -   Erstellen eines SharePoint-Entwicklercomputers mit dem SharePoint 2010 Easy Setup Script. Weitere Informationen finden Sie unter [SharePoint 2010 Easy Setup Script](http://www.microsoft.com/en-us/download/details.aspx?id=23415).
        -   Verwenden von Windows PowerShell. Weitere Informationen finden Sie unter [Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell](http://technet.microsoft.com/en-us/library/cc262839.aspx).
        -   Verwenden von AutoSPInstaller aus dem CodePlex-Projekt. Weitere Informationen finden Sie unter [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
    -   Installieren von Visual Studio. Weitere Informationen finden Sie unter [Installieren von Visual Studio](http://msdn.microsoft.com/en-us/library/e2h7fzkw.aspx).
    -   Installieren von SQL Server. Weitere Informationen finden Sie unter [Installieren von SQL Server mit SysPrep](http://msdn.microsoft.com/en-us/library/ee210664.aspx).
        -   Informationen zum Erstellen und Konfigurieren von SQL Server 2012 für eine SharePoint-Farmbereitstellung finden Sie in den praktischen Übungen: [Konfigurieren von SQL Server 2012 für SharePoint in Azure](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint).
        -   Informationen zum Erstellen einer SharePoint-Farm durch Konfigurieren von Active Directory und Verwenden einer einzelnen SQL Server-Datenbank finden Sie in den praktischen Übungen: [Bereitstellen einer SharePoint-Farm mit Azure Virtual Machines](https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs).

3.  *Entwickeln von Bereitstellungspaketen und -skripten für Anwendungen und Datenbanken*: Wenn Sie vorhaben, eine VM aus der Abbildbibliothek zu verwenden, können Sie die lokalen Anwendungen und Datenbanken auf Azure Virtual Machines bereitstellen:
    -   Erstellen Sie mit SQL Server Data Tools und Visual Studio Bereitstellungspakete für vorhandene Anwendungen und Datenbanken.
    -   Stellen Sie mit diesen Paketen die Anwendungen und Datenbanken auf Azure Virtual Machines bereit.

4.  *Bereitstellen von SharePoint-Anwendungen und -Datenbanken*:
    -   Konfigurieren Sie Sicherheitsoptionen am Endpunkt des Verwaltungsportals, und legen Sie einen eingehenden Port in der Windows Firewall der VM fest.
    -   Stellen Sie SharePoint-Anwendungen und -Datenbanken auf Azure Virtual Machines mit den in Schritt 3 erstellten Bereitstellungspaketen und -skripten bereit.
    -   Testen Sie bereitgestellte Anwendungen und Datenbanken.

5.  *Verwalten von VMs*:
    -   Überwachen Sie die VMs im Verwaltungsportal.
    -   Überwachen Sie die Anwendungen mit Visual Studio und SQL Server Management Studio.
    -   Sie können VMs auch mit lokaler Verwaltungssoftware, wie Microsoft System Center – Operations Manager, überwachen und verwalten.

### Szenario 2: Öffentliche SharePoint-Farm mit Anpassungen

#### Beschreibung

Organisationen möchten eine Internetpräsenz erstellen, die in der Cloud gehostet wird und auf Basis von Bedarf und Nachfrage einfach skalierbar ist. Außerdem möchten sie Partner-Extranetwebsites für die Zusammenarbeit erstellen und ein einfaches Verfahren für die verteilte Erstellung und Genehmigung des Websiteinhalts implementieren. Schließlich möchten diese Organisationen auf ihren Websites bedarfsgesteuerte Kapazitäten vorsehen, um auf wachsende Auslastungen reagieren zu können.

In diesem Szenario wird SharePoint Server als Basis für das Hosten einer öffentlichen Website verwendet. Organisationen haben damit die Möglichkeit, ihre Geschäftswebsites in einer sicheren, skalierbaren Cloud-Infrastruktur schnell bereitzustellen, anzupassen und zu hosten. Mit öffentlichen SharePoint-Websites auf Azure können Organisationen mit dem Anwachsen des Datenverkehrs Kapazitäten skalieren und nur für das bezahlen, was sie nutzen. Allgemeine Tools, vergleichbar mit den lokal verwendeten, können mit SharePoint für die Inhaltserstellung, den Workflow und die Genehmigung auf Azure eingesetzt werden.

Außerdem können Organisationen mit Azure Virtual Machines Staging- oder Produktionsumgebungen, die auf VMs laufen, einfach konfigurieren. Öffentliche, in Azure erstellte SharePoint-VMs können im virtuellen Speicher gesichert werden. Für die Notfallwiederherstellung ist es zusätzlich möglich, mit der fortlaufenden Georeplikationsfunktion VMs aus einem Rechenzentrum automatisch in einem anderen weit entfernten Rechenzentrum zu sichern. (Weitere Informationen über Georeplikation finden Sie unter [Einführung in die Georeplikation für Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx)).

VMs in der Azure-Infrastruktur werden in Bezug auf die Zusammenarbeit mit anderen Microsoft-Produkten, wie SQL Server und SharePoint Server, überprüft und unterstützt. Azure und SharePoint Server eignen sich besonders für eine gemeinsame Nutzung: Beide sind Teil der Microsoft-Familie und sorgfältig aufeinander abgestimmt, und beide wurden gemeinsam getestet, um eine optimale Leistung zu erzielen. Beide haben einen einzelnen Unterstützungspunkt für die SharePoint-Anwendung und die Azure-Infrastruktur.

#### Erste Schritte

In diesem Szenario müssen weitere Front-End-Webserver für SharePoint Server hinzugefügt werden, um zusätzlichen Datenverkehr zu unterstützen. Diese Server benötigen eine verbesserte Sicherheit und Domänencontroller der Active Directory-Domänendienste für die Benutzerauthentifizierung und -autorisierung. Abbildung 5 zeigt das Layout dieses Szenarios.

Abbildung 5: Öffentliche SharePoint-Farm mit Anpassungen

![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

#### Konfigurieren der Umgebung für das Szenario

Führen Sie die folgenden Schritte aus, um eine öffentliche SharePoint-Farm auf Azure zu implementieren:

1.  *Bereitstellen von Active Directory*: Die Grundanforderungen für die Bereitstellung von Active Directory auf Azure Virtual Machines sind den Anforderungen für die Bereitstellung auf lokalen VMs (und bis zu einem gewissen Ausmaß auf physischen Computern) ähnlich, aber nicht identisch damit. Weitere Information zu den Unterschieden sowie Richtlinien und andere Überlegungen finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090). So stellen Sie Active Directory auf Azure bereit:
    -   Definieren und erstellen Sie ein virtuelles Netzwerk, in dem die VMs zu bestimmten Subnetzen zugewiesen werden können. Weitere Informationen finden Sie unter [Konfigurieren von virtuellen Netzwerken](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md).
    -   Erstellen Sie im Verwaltungsportal den Domänencontroller auf der neuen VM in Azure, und stellen Sie ihn bereit. Weitere Informationen finden Sie unter [Bereitstellen und Erstellen des Domänencontrollers](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md).
        -   Sie können auch anhand des Windows PowerShell-Skripts eine eigenständige Domäne in der Cloud mit Azure Virtual Machines und Virtual Network bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Active Directory in Azure (Windows PowerShell)](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS).
        -   Weitere Informationen zum Erstellen einer neuen Active Directory-Gesamtstruktur auf einer VM in Azure Virtual Network finden Sie unter [Installieren einer neuen Active Directory-Gesamtstruktur in Azure](/en-us/manage/services/networking/active-directory-forest/).

2.  *Bereitstellen einer VM*: Stellen Sie im Verwaltungsportal eine neue VM mithilfe eines vorhandenen Image aus der Abbildbibliothek bereit.
3.  *Bereitstellen einer SharePoint-Farm*:
    -   Installieren Sie mit der neu bereitgestellten VM SharePoint, und generieren Sie ein wiederverwendbares Image. Weitere Informationen zum Installieren von SharePoint Server finden Sie unter [Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell](http://technet.microsoft.com/en-us/library/cc262839.aspx) oder [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
    -   Konfigurieren Sie die SharePoint-VM so, dass die SharePoint-Farm erstellt und eine Verbindung damit hergestellt wird.
    -   Konfigurieren Sie im Verwaltungsportal den Lastenausgleich.
        -   Konfigurieren Sie die VM-Endpunkte, wählen Sie die Option zum Laden des Ausgleichsdatenverkehrs auf einen vorhandenen Endpunkt, und geben Sie dann den Namen der VM mit dem Lastenausgleich an.
        -   Fügen Sie der vorhandenen SharePoint-Farm eine weitere Front-End-Web-VM für zusätzlichen Datenverkehr hinzu.

4.  *Verwalten von VMs*:
    -   Überwachen Sie die VMs im Verwaltungsportal.
    -   Überwachen Sie die SharePoint-Farm mit der Zentraladministration.

### Szenario 3: Horizontal skalierte Farm für weitere BI-Dienste

#### Beschreibung

Business Intelligence ist unerlässlich, um wichtige Erkenntnisse zu gewinnen und schnelle, fundierte Entscheidungen zu treffen. Wenn Organisationen von einem lokalen Ansatz zur Bereitstellung von BI-Anwendungen in der Cloud übergehen, möchten sie keine Änderungen an der BI-Umgebung vornehmen. Berichte von SQL Server Analysis Services (SSAS) oder SQL Server Reporting Services (SSRS) sollen in einer sehr stabilen Umgebung mit hoher Verfügbarkeit gehostet und dabei die komplette Kontrolle über die BI-Anwendung aufrechterhalten werden – und das alles mit geringem Zeit- und Kostenaufwand für die Wartung.

Dieses Szenario beschreibt, wie Organisationen mit Azure Virtual Machines geschäftskritische BI-Anwendungen hosten können. Organisationen können SharePoint-Farmen in Azure Virtual Machines bereitstellen und die BI-Komponenten der Anwendungsserver-VM, wie SSRS oder Excel Services, horizontal skalieren. Durch die Skalierung ressourcenintensiver Komponenten in der Cloud können besondere Arbeitsauslastungen besser und einfacher unterstützt werden. SQL Server eignet sich gut für Azure Virtual Machines, weil SQL Server-Instanzen einfach von kleinen bis zu sehr großen Installationen skaliert werden können. Die dadurch erreichte Flexibilität ermöglicht Organisationen, BI-Instanzen auf Basis unmittelbarer Arbeitsauslastungsanforderungen dynamisch bereitzustellen (zu erweitern) bzw. die Bereitstellung zurückzunehmen (zu verkleinern).

Die Migration vorhandener BI-Anwendungen auf Azure ermöglicht eine bessere Skalierung. Mit SSAS, SSRS und SharePoint Server können Organisationen leistungsstarke BI- und Berichtsanwendungen und -Dashboards erstellen, die nach oben und unten skalierbar sind. Diese Anwendungen und Dashboards können zudem sicherer in lokale Daten und Anwendungen integriert werden. Azure sorgt für die Rechenzentrums-Compilance mit Unterstützung für ISO 27001. Weitere Informationen finden Sie unter [Microsoft Azure-Vertrauenscenter](/en-us/support/trust-center/compliance/).

#### Erste Schritte

Um die Bereitstellung von BI-Komponenten horizontal zu skalieren, muss ein neuer Anwendungsserver mit Diensten, wie PowerPivot, Power View, Excel Services oder PerformancePoint Services, installiert werden. Oder der vorhandenen Farm müssen SQL Server-BI-Instanzen, wie SSAS oder SSRS, hinzugefügt werden, um zusätzliche Abfrageverarbeitungen zu unterstützen. Der Server kann als neue Azure-VM mit installiertem SharePoint 2010 Server oder SQL Server hinzugefügt werden. Anschließend können auf diesem Server die BI-Komponenten installiert, bereitgestellt und konfiguriert werden (Abbildung 6).

Abbildung 6: Für zusätzliche BI-Dienste horizontal skalierte SharePoint-Farm

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

#### Konfigurieren der Umgebung für das Szenario

Führen Sie die folgenden Schritte aus, um eine BI-Umgebung auf Azure horizontal zu skalieren:

1.  *Bereitstellen*:
    -   Stellen Sie mit Azure Virtual Network eine VPN-Verbindung zwischen Ihrem Standort und Azure bereit. Weitere Informationen finden Sie unter [Virtuelle Netzwerke im Überblick (Entwurfsüberlegungen, Sichere Verbindungsszenarien)](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx).
    -   Stellen Sie im Verwaltungsportal eine neue VM mithilfe eines vorhandenen Image aus der Abbildbibliothek bereit.
        -   Sie können BI-Arbeitsauslastungs-Images von SharePoint Server oder SQL Server in die Abbildbibliothek hochladen, und jeder autorisierte Benutzer kann diese BI-Komponenten-VMs auswählen, um die horizontal skalierte Umgebung zu erstellen.

2.  *Installieren*: Wenn Ihre Organisation über keine vorgefertigten Images von SharePoint Server- oder SQL Server-BI-Komponenten verfügt, installieren Sie SharePoint Server und SQL Server mit einer Remotedesktopverbindung auf den VMs.
    -   Weitere Informationen zum Installieren von SharePoint finden Sie unter [Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell](http://technet.microsoft.com/en-us/library/cc262839.aspx) oder [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
    -   Weitere Informationen zum Installieren von SQL Server finden Sie unter [Installieren von SQL Server mit SysPrep](http://msdn.microsoft.com/en-us/library/ee210664.aspx).
    -   Informationen zum Erstellen und Konfigurieren von SQL Server 2012 für eine SharePoint-Farmbereitstellung finden Sie in den praktischen Übungen: [Konfigurieren von SQL Server 2012 für SharePoint in Azure](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint).
    -   Informationen zum Erstellen einer SharePoint-Farm durch Konfigurieren von Active Directory und Verwenden einer einzelnen SQL Server-Datenbank finden Sie in den praktischen Übungen: [Bereitstellen einer SharePoint-Farm mit Azure Virtual Machines](https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs).

3.  *Hinzufügen der BI-VM*:
    -   Konfigurieren Sie Sicherheitsoptionen am Endpunkt des Verwaltungsportals, und legen Sie einen eingehenden Port in der Windows Firewall der VM fest.
    -   Fügen Sie die neu erstellte BI-VM der vorhandenen SharePoint- oder SQL Server-Farm hinzu.

4.  *Verwalten von VMs*:
    -   Überwachen Sie die VMs im Verwaltungsportal.
    -   Überwachen Sie die SharePoint-Farm mit der Zentraladministration.
    -   Überwachen und verwalten Sie VMs mit lokaler Verwaltungssoftware, wie Microsoft System Center – Operations Manager.

### Szenario 4: Vollständig angepasste SharePoint-basierte Website

#### Beschreibung

Organisationen möchten zunehmend vollständig angepasste SharePoint-Websites in der Cloud erstellen. Sie benötigen eine äußerst stabile Umgebung mit hoher Verfügbarkeit und umfangreichen Wartungsmöglichkeiten komplexer Anwendungen in der Cloud ohne großen Zeit- und Kostenaufwand.

In diesem Szenario kann eine Organisation ihre gesamte SharePoint-Farm in der Cloud bereitstellen und alle Komponenten dynamisch skalieren, um zusätzliche Kapazitäten zu erhalten, oder sie kann ihre lokale Bereitstellung auf die Cloud ausweiten, um bei Bedarf die Kapazität zu erhöhen und die Leistung zu steigern. Das Szenario konzentriert sich auf Organisationen, die eine umfassende „SharePoint-Leistung“ für die Anwendungsentwicklung und das Enterprise Content Management möchten. Komplexere Sites können auch erweiterte Berichtserstellungsfunktionen, Power View, PerformancePoint, PowerPivot, detaillierte Diagramme und die meisten anderen SharePoint-Sitefunktionen enthalten, um die gesamte Ende-zu-Ende-Funktionalität bereitzustellen.

Organisationen können mit Azure Virtual Machines angepasste Anwendungen und zugehörige Komponenten in einer kostengünstigen und hochsicheren Cloud-Infrastruktur hosten. Sie können auch ein lokales Microsoft System Center als gemeinsames Verwaltungstool für lokale und Cloud-Anwendungen verwenden.

#### Erste Schritte

Um eine vollständig angepasste SharePoint-Website auf Azure zu implementieren, muss eine Organisation eine Active Directory-Domäne in der Cloud und neue VMs in dieser Domäne bereitstellen. Dann muss eine VM mit SQL Server 2012 als Teil einer SharePoint-Farm erstellt und konfiguriert werden. Schließlich muss die SharePoint-Farm erstellt, der Lastenausgleich verteilt und die Farm mit Active Directory und SQL Server verbunden werden (Abbildung 7).

Abbildung 7: Vollständig angepasste SharePoint-basierte Website

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

#### Konfigurieren der Umgebung für das Szenario

Die folgenden Schritte zeigen, wie eine angepasste SharePoint-Farmumgebung aus vorgefertigten Images aus der Abbildbibliothek erstellt wird. Sie können aber auch SharePoint-Farm-VMs in die Abbildbibliothek hochladen, und autorisierte Benutzer können anhand dieser VMs die gewünschte SharePoint-Farm auf Azure erstellen.

1.  *Bereitstellen von Active Directory*: Die Grundanforderungen für die Bereitstellung von Active Directory auf Azure Virtual Machines sind den Anforderungen für die Bereitstellung auf lokalen VMs (und bis zu einem gewissen Ausmaß auf physischen Computern) ähnlich, aber nicht identisch damit. Weitere Information zu den Unterschieden sowie Richtlinien und andere Überlegungen finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090). So stellen Sie Active Directory auf Azure bereit:
    -   Definieren und erstellen Sie ein virtuelles Netzwerk, in dem die VMs zu bestimmten Subnetzen zugewiesen werden können. Weitere Informationen finden Sie unter [Konfigurieren von virtuellen Netzwerken](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md).
    -   Erstellen Sie im Verwaltungsportal den Domänencontroller auf der neuen VM in Azure, und stellen Sie ihn bereit. Weitere Informationen finden Sie unter [Bereitstellen und Erstellen des Domänencontrollers](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md).
        -   Sie können auch anhand des Windows PowerShell-Skripts eine eigenständige Domäne in der Cloud mit Azure Virtual Machines und Virtual Network bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Active Directory in Azure (Windows PowerShell)](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS).
        -   Weitere Informationen zum Erstellen einer neuen Active Directory-Gesamtstruktur auf einer VM in Azure Virtual Network finden Sie unter [Installieren einer neuen Active Directory-Gesamtstruktur in Azure](/en-us/manage/services/networking/active-directory-forest/).

2.  *Bereitstellen von SQL Server*:
    -   Stellen Sie im Verwaltungsportal eine neue VM mithilfe eines vorhandenen Image aus der Abbildbibliothek bereit.
    -   Konfigurieren Sie SQL Server auf der VM. Weitere Informationen finden Sie unter [Installieren von SQL Server mit SysPrep](http://msdn.microsoft.com/en-us/library/ee210664.aspx).
    -   Verknüpfen Sie die VM mit der neu angelegten Active Directory-Domäne.

3.  *Bereitstellen einer SharePoint-Farm mit mehreren Servern*:
    -   Erstellen Sie ein virtuelles Netzwerk. Weitere Informationen finden Sie unter [Virtuelle Netzwerke im Überblick (Entwurfsüberlegungen, Sichere Verbindungsszenarien)](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx).
        -   Wenn SharePoint-VMs bereitgestellt werden, müssen Subnetze für SharePoint Server vorhanden sein, damit die DNS-Adressen in dem lokalen Active Directory während der Bereitstellung verfügbar sind.
    -   Erstellen Sie im Verwaltungsportal eine VM.
    -   Installieren Sie SharePoint Server auf dieser VM, und generieren Sie ein wiederverwendbares Image. Weitere Informationen zum Installieren von SharePoint Server finden Sie unter [Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell](http://technet.microsoft.com/en-us/library/cc262839.aspx) oder [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
    -   Konfigurieren Sie die SharePoint-VM so, dass die SharePoint-Farm erstellt und eine Verbindung damit mit dem Befehl [Join-SharePointFarm](http://technet.microsoft.com/en-us/library/ff607979.aspx) hergestellt wird.
    -   Konfigurieren Sie im Verwaltungsportal den Lastenausgleich:
        -   Konfigurieren Sie die VM-Endpunkte, wählen Sie die Option zum Laden des Ausgleichsdatenverkehrs auf einen vorhandenen Endpunkt, und geben Sie dann den Namen der VM mit dem Lastenausgleich an.
            -   Weitere Informationen zur Bereitstellung von SharePoint-Farmen auf Azure Virtual Machines finden Sie in diesem Video [TechEd North America 2012](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2012/AZR327).

4.  *Verwalten der SharePoint-Farm im System Center*:
    -   Verbinden Sie mit dem Operations Manager-Agent und dem neuen Azure-Integrationspaket Ihr lokales System Center mit Azure Virtual Machines.
    -   Verwenden Sie den lokalen App Controller und Orchestrator für die Verwaltungsfunktionen.

Zusammenfassung
---------------

Cloud-Computing verändert die Art und Weise, wie die IT Organisationen unterstützt. Der Grund dafür ist, dass Cloud-Computing eine neue Klasse von Vorteilen nutzen kann, unter anderem drastisch gesenkte Kosten gekoppelt mit erhöhter IT-Konzentration, -Agilität und -Flexibilität. Azure ist durch die Bereitstellung einer einfachen, offenen, flexiblen und leistungsstarken virtuellen Infrastruktur die führende Plattform für Cloud-Computing. Azure Virtual Machines senken den Hardwarebedarf, sodass Organisationen Kosten und Komplexität durch Erstellen einer maßgerechten Infrastruktur mit vollständiger Kontrolle und optimierter Verwaltung reduzieren können.

Azure Virtual Machines bieten ein vollständiges Kontinuum von SharePoint-Bereitstellungen. Azure Virtual Machines werden komplett unterstützt und ausgiebig getestet, um zusammen mit anderen Microsoft-Anwendungen eine optimale Leistung zu liefern. Daher können Organisationen SharePoint Server in Azure einfach konfigurieren und bereitstellen, entweder um eine Infrastruktur für eine neue SharePoint-Bereitstellung einzurichten oder um eine vorhandene zu erweitern. Wenn Geschäftsarbeitsauslastungen zunehmen, können Organisationen ihre SharePoint-Infrastruktur schnell erweitern. Ebenso lassen sich bei sinkender Arbeitsauslastung Ressourcen nach Bedarf verringern, wobei nur Kosten für tatsächlich genutzte Ressourcen anfallen. Azure Virtual Machines bieten eine außergewöhnliche Infrastruktur für eine breite Palette von Geschäftsanforderungen, wie anhand der vier SharePoint-basierten Szenarien in diesem Dokument gezeigt wurde.

Voraussetzung für eine erfolgreiche Bereitstellung von SharePoint Server auf Azure Virtual Machines ist eine solide Planung, vor allem in Bezug auf die kritischen Farmarchitektur- und -bereitstellungsoptionen. Die in diesem Dokument erläuterten Erkenntnisse und bewährten Methoden können Sie als Entscheidungshilfe bei der Implementierung einer fundierten SharePoint-Bereitstellung heranziehen.

Zusätzliche Ressourcen
----------------------

-   Azure-Trainingskit: Praktische Übungen und Präsentationen

    <http://windowsazure-trainingkit.github.com/labs.htm>

-   Erste Schritte mit Azure PowerShell

    [http://msdn.microsoft.com/de-de/library/windowsazure/jj156055](http://msdn.microsoft.com/en-us/library/windowsazure/jj156055)

-   Azure-Cmdlets für die Verwaltung

    <http://msdn.microsoft.com/en-us/library/windowsazure/jj152841>

-   Befehlszeilentools und PowerShell-Cmdlets für verschiedene Betriebssysteme

    [https://www.windowsazure.com/en-us/manage/downloads/](/en-us/manage/downloads/)

-   Anleitungen und Dokumentation von bewährten Methoden

    [https://www.windowsazure.com/en-us/manage/windows/](/en-us/manage/windows/)


