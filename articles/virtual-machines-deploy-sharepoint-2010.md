<properties pageTitle="SharePoint 2010-Bereitstellung auf Azure Virtual Machines" description="Verstehen Sie die unterstützten Szenarien für die Verwendung von SharePoint 2010 auf virtuellen Azure-Computern." services="virtual-machines" documentationCenter="" authors="JoeDavies-MSFT" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="1/16/2015" ms.author="josephd"/>




<h1>SharePoint 2010-Bereitstellung auf Azure Virtual Machines</h1>
<h2>Kurzfassung</h2>

Microsoft SharePoint Server 2010 bietet eine umfangreiche Bereitstellungsflexibilität, die Organisationen bei der Ermittlung der richtigen Einsatzszenarien für ihre geschäftlichen Anforderungen und Ziele unterstützen kann. Das Angebot von Azure Virtual Machines, das in der Cloud gehostet und verwaltet wird, umfasst eine vollständige, zuverlässige und verfügbare Infrastruktur für die Unterstützung zahlreicher On-Demand-Anwendungs- und Datenbankarbeitsauslastungen, wie Microsoft SQL Server- und SharePoint-Bereitstellungen.


Azure Virtual Machines unterstützen mehrere Arbeitsauslastungen, dieses Dokument konzentriert sich aber auf SharePoint-Bereitstellungen. Mit Azure Virtual Machines können Organisationen ihre SharePoint-Infrastruktur mit geringem Zeitaufwand erstellen und verwalten, wodurch das universelle Bereitstellen und Zugreifen auf nahezu jeden Host ermöglicht wird, genauso wie die vollständige Kontrolle und Verwaltung von Prozessoren, RAM, CPU-Bereichen und anderen Ressourcen von virtuellen SharePoint-Computern (VMs).

Azure Virtual Machines senken den Hardwarebedarf, sodass Organisationen sich dem Erstellen und Verwalten einer maßgerechten Infrastruktur widmen können, anstatt sich um hohe Vorlaufkosten und Komplexität kümmern zu müssen. Das bedeutet, dass Innovationen, Experimente und Iterationen in Stunden durchgeführt werden können, im Gegensatz zu Tagen und Wochen, wie das bei herkömmlichen Bereitstellungen der Fall ist.

<h3>Zielgruppe dieses Dokuments</h3>

Dieses Dokument richtet sich an IT-Experten. Darüber hinaus können technische Entscheidungsträger wie Architekten und Systemadministratoren, anhand dieser Informationen und der vorgestellten Szenarien eine virtualisierte SharePoint-Infrastruktur auf Azure planen und entwerfen.

<h3>Zweck dieses Dokuments</h3>

Dieses Dokument erläutert, wie Organisationen SharePoint in Azure Virtual Machines konfigurieren und bereitstellen können. Die Vorteile dieser Bereitstellungsart für Organisationen unterschiedlicher Größe werden ebenfalls behandelt.

<h2>Umstieg auf Cloud-Computing</h2>

Gartner definiert Cloud-Computing als eine "Art von Computing, bei der stark skalierbare IT-basierte Funktionen für externe Kunden mit Internettechnologien  'as a service' bereitgestellt werden". Die zentralen Begriffe in dieser Definition sind "skalierbar", "Dienst" und "Internet". Zusammengefasst kann Cloud-Computing als IT-Dienste definiert werden, die <strong>über das Internet bereitgestellt und geliefert werden</strong> und <strong>bei Bedarf skalierbar</strong> sind.

Cloud-Computing stellt zweifellos eine der wesentlichsten Veränderungen in der heutigen IT dar. In der Vergangenheit waren die zentralen Aspekte Konsolidierung und Kosten. Heute geht es um die neue Klasse von Leistungen, die Cloud-Computing erbringen kann. Es geht um die Änderung der Art und Weise, wie die IT durch die Nutzung einer neuen Leistungsart Organisationen unterstützt. Cloud-Computing ändert die IT-Welt grundlegend - mit Auswirkungen auf alle Rollen vom Dienstanbieter und Systemarchitekten bis hin zum Entwickler und Endbenutzer.

Die Forschung zeigt, dass Agilität, Fokus und wirtschaftliche Aspekte die drei Haupttriebkräfte für Cloud-Initiativen sind:

<ul>
<li><p><strong>Agilität</strong>: Cloud-Computing kann Organisationen dabei unterstützen, schnell von neuen Chancen zu profitieren und auf Änderungen von Geschäftsanforderungen zu reagieren.</p></li>
<li><p><strong>Fokus</strong>: Cloud-Computing ermöglicht IT-Abteilungen eine deutliche Senkung der Infrastrukturkosten. Die Infrastruktur wird abstrahiert und Ressourcen werden gebündelt, sodass die IT eher wie ein Hilfsprogramm als eine Sammlung von komplizierten Diensten und Systemen ausgeführt wird. Außerdem kann die IT jetzt in innovativere und strategischere Rollen übergeführt werden.</p></li>
<li><p><strong>Wirtschaftliche Aspekte</strong>: Cloud-Computing reduziert die Kosten für die Bereitstellung der IT und erhöht die Auslastung und Effizienz des Rechenzentrums. Bereitstellungskosten sinken, weil durch Cloud-Computing Anwendungen und Ressourcen zu einem Self-Service werden und die Verwendung dieser Ressourcen auf eine neue und präzise Art gemessen werden kann. Auch die Hardwarenutzung steigt, weil jetzt Infrastrukturressourcen (Speicher, Compute Services und Netzwerk) gebündelt und abstrahiert sind.</p></li>
</ul>

<h2>Breitstellungsmodelle für Clouddienste</h2>

Einfach ausgedrückt ist Cloud-Computing die Abstraktion von IT-Diensten. Diese Dienste können von der Basisinfrastruktur bis zu vollständigen Anwendungen reichen. Endbenutzer fordern und nutzen abstrahierte Dienste ohne Verwaltungsbedarf (oder sogar ohne Kenntnis davon), und genau das macht diese Dienste aus. Die Branche kennt heute drei Breitstellungsmodelle für Clouddienste, die jeweils einen anderen Kompromiss zwischen Kontrolle/Flexibilität und Gesamtkosten bieten:

<ul>
<li><p><strong>Infrastructure-as-a-Service</strong> (IaaS): Virtuelle Infrastruktur, die virtuelle Computer und größtenteils vorhandene Anwendungen hostet.</p></li>
<li><p><strong>Platform-as-a-Service</strong> (PaaS): Cloud-Anwendungsinfrastruktur, die eine bedarfsgesteuerte Anwendungshostumgebung bereitstellt.</p></li>
<li><p><strong>Software-as-a-Service</strong> (SaaS): Clouddienst-Modell, bei dem eine Anwendung über das Internet bereitgestellt wird und Kunden auf Nutzungsbasis bezahlen (z. B. Microsoft Office 365 oder Microsoft CRM Online).</p></li>
</ul>

Abbildung 1 zeigt die Systematik von Clouddiensten und wie sie zu den Komponenten in einer IT-Infrastruktur zugeordnet werden. Bei einem lokalen Modell ist der Kunde für die Verwaltung des gesamten Stapels von der Netzwerkverbindung bis hin zu den Anwendungen verantwortlich. Bei IaaS werden die unteren Ebenen des Stapels von einem Anbieter verwaltet. Der Kunde ist für die Verwaltung des Betriebssystems und der Anwendungen verantwortlich. Bei PaaS stellt ein Plattformanbieter alle Komponenten von der Netzwerkverbindung bis zur Laufzeitumgebung bereit und verwaltet diese auch. Der Kunde muss nur Anwendungen und Daten verwalten. (Das Azure-Angebot passt am besten in dieses Modell.) Beim letzten Modell, SaaS, stellt ein Anbieter die Anwendungen bereit und abstrahiert alle Dienste aller zugrunde liegenden Komponenten.

<p class="caption">Abbildung 1: Systematik von Clouddiensten</p>

![azure-sharepoint-wp-1](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-1.png)

<h2>Azure Virtual Machines</h2>

Azure Virtual Machines führt Funktionen ein, die eine vollständige Kontrolle und Verwaltung von VMs sowie einen extensiven virtuellen Netzwerkbetrieb ermöglichen. Durch dieses Angebot können Organisationen strategische Vorteile erzielen:

<ul>
<li><p><strong>Verwaltung</strong>: Zentrales Verwalten von VMs in der Cloud mit vollständiger Kontrolle der Konfiguration und Wartung der Infrastruktur.</p></li>
<li><p><strong>Anwendungsmobilität</strong>: Verschieben von virtuellen Festplatten (VHDs) zwischen lokalen und cloudbasierten Umgebungen. Anwendungen müssen für die Ausführung in der Cloud nicht neu erstellt werden.</p></li>
<li><p><strong>Zugriff auf Microsoft-Serveranwendungen</strong>: Ausführen der gleichen lokalen Anwendungen und Infrastruktur in der Cloud, einschließlich Microsoft SQL Server, SharePoint Server, Windows Server und Active Directory.</p></li>
</ul>

Azure Virtual Machines ist eine einfache, offene und flexible sowie leistungsstarke Plattform, mit der Organisationen Windows Server- und Linux-VMs im Handumdrehen bereitstellen und ausführen können:

<ul>
<li><p><strong>Einfach</strong>: Mit Azure Virtual Machines können VMs sehr einfach in der Cloud erstellt, migriert, bereitgestellt und verwaltet werden. Organisationen können Arbeitsauslastungen auf Azure migrieren, ohne dass vorhandener Code geändert werden müsste, oder sie können mit nur ein paar Klicks neue VMs in Azure einrichten. Das Angebot umfasst auch die Unterstützung bei der Entwicklung neuer Cloud-Anwendungen durch Integration der IaaS- und PaaS-Funktionen von Azure.</p></li>

<li><p><strong>Offen und flexibel</strong>: Azure ist eine offene Plattform mit hoher Flexibilität für Organisationen. Sie können mit einem vorgefertigten Image aus der Abbildbibliothek starten oder angepasste und lokale VHDs erstellen und verwenden und diese in die Abbildbibliothek hochladen. Community- und kommerzielle Versionen von Linux sind auch verfügbar.</p></li>

<li><p><strong>Leistungsstark</strong>: Azure ist eine unternehmensfähige Cloud-Plattform für die Ausführung von Anwendungen wie SQL Server, SharePoint Server oder Active Directory in der Cloud. Organisationen können hybride lokale und Cloud-Lösungen mit VPN-Konnektivität zwischen dem Azure-Rechenzentrum und eigenen Netzwerken einrichten.</p></li>
</ul>

<h2>SharePoint auf Azure Virtual Machines</h2>

SharePoint 2010 unterstützt auf flexible Weise die meisten Arbeitsauslastungen in einer Azure Virtual Machines-Bereitstellung. Azure Virtual Machines passen bestens zu FIS-(SharePoint Server for Internet Sites-) und Entwicklungsszenarien. Kernarbeitsauslastungen von SharePoint werden ebenfalls unterstützt. Wenn eine Organisation ihre eigene SharePoint 2010-Implementierung bei gleichzeitiger Nutzung der Virtualisierungsoptionen in der Cloud verwalten und steuern möchte, ist Azure Virtual Machines das ideale Bereitstellungsinstrument.

Das Azure Virtual Machines-Angebot wird in der Cloud gehostet und verwaltet. Für Flexibilität bei der Bereitstellung ist gesorgt, und Kosten werden durch Verringern der Investitionsaufwendungen für die Hardwarebeschaffung gesenkt. Bei größerer Agilität der Infrastruktur können Organisationen SharePoint Server in Stunden anstatt in Tagen oder Wochen bereitstellen. Darüber hinaus ermöglicht Azure Virtual Machines, Organisationen SharePoint-Arbeitsauslastungen in der Cloud mit einem "nutzungsbasierten Gebührenmodell" bereitzustellen. Wenn die SharePoint-Arbeitsauslastungen zunehmen, können Organisationen sofort die Infrastruktur erweitern; sinkt der Computing-Bedarf können die nicht mehr benötigten Ressourcen zurückgegeben werden, und es müssen nur die verwendeten Ressourcen bezahlt werden.

<h3>Verschiebung des IT-Fokus</h3>

Viele Organisationen lagern allgemeine Komponenten ihrer IT-Infrastruktur und -Verwaltung aus, wie z. B. Hardware, Betriebssysteme, Sicherheit, Datenspeicherung und Backup, behalten aber die Kontrolle über geschäftskritische Anwendungen, wie SharePoint Server. Durch die Delegierung aller nicht geschäftskritischen Dienstebenen ihrer IT-Plattformen an einen virtuellen Anbieter können Organisationen ihren IT-Fokus auf zentrale, geschäftskritische SharePoint-Dienste richten und mit SharePoint-Projekten zur Wertschöpfung beitragen, anstatt Zeit für die Konfiguration der Infrastruktur aufzuwenden.

<h3>Schnellere Bereitstellung</h3>

Die Unterstützung und Bereitstellung einer großen SharePoint-Infrastruktur kann den schnellen Einsatz der IT für Geschäftsanforderungen behindern. Abhängig von den Verfahren und Einschränkungen der Organisation kann es Wochen oder sogar Monate dauern, um SharePoint-Server und -Farmen zu erstellen, zu testen und vorzubereiten sowie sie in einer Produktionsumgebung bereitzustellen. Azure Virtual Machines ermöglicht Organisationen, ihre SharePoint-Arbeitsauslastungen ohne Investitionsaufwendungen für Hardware schnell bereitzustellen. So können Organisationen die Infrastrukturagilität nutzen, um Bereitstellungen innerhalb von Stunden anstatt von Tagen oder Wochen zu bewerkstelligen.

<h3>Skalierbarkeit</h3>

Ohne die Notwendigkeit physische SharePoint-Server und -Farmen bereitzustellen, zu testen und vorzubereiten, können Organisationen bei Bedarf Rechenkapazität jederzeit erweitern und verringern. Wenn die Anforderungen an die SharePoint-Arbeitsauslastung anwachsen, kann eine Organisation ihre Infrastruktur in der Cloud rasch erweitern. Ebenso kann die Organisation Ressourcen verringern, wenn der Computing-Bedarf abnimmt, und nur für die tatsächlich genutzten Ressourcen fallen Kosten an. Azure Virtual Machines reduzieren Vorlaufkosten und langfristige Verpflichtungen, indem sie Organisationen ermöglichen, maßgerechte SharePoint-Infrastrukturen zu erstellen und zu verwalten. Auch hier bedeutet dies, dass Organisationen Innovationen, Experimente und Iterationen in Stunden durchführen können, im Gegensatz zu Tagen und Wochen, wie das bei herkömmlichen Bereitstellungen der Fall ist.

<h3>Nutzungsbezogene Gebühren</h3>

Azure Virtual Machines liefern Rechenleistung, Arbeitsspeicher und Speicher für SharePoint-Szenarien, deren Preise in der Regel auf dem Ressourcenverbrauch basieren. Für Organisationen fallen nur für die genutzten Ressourcen Kosten an, und der Dienst liefert die gesamte für den Betrieb der SharePoint-Infrastruktur benötigte Kapazität. Weitere Informationen zur Preisgestaltung und Gebührenabrechnung finden Sie unter <a href="/de-de/pricing/details/">Azure-Preisübersicht</a>. Beachten Sie bitte, dass minimale Gebühren für das Speichern und Verschieben von Daten aus der Azure-Cloud in ein lokales Netzwerk anfallen. Für das Hochladen von Daten auf Azure werden aber keine Gebühren berechnet.

<h3>Flexibilität</h3>

Mit Azure Virtual Machines erhalten Entwickler die Flexibilität, ihre bevorzugte Sprache oder Laufzeitumgebung zu wählen, mit offizieller Unterstützung von .NET, Node.js, Java und PHP. Entwickler können zudem ihre Tools wählen, mit Unterstützung von Microsoft Visual Studio, WebMatrix, Eclipse und Texteditoren. Darüber hinaus bietet Microsoft einen kostensparenden, risikoarmen Pfad zur Cloud und durch den Zugang zu Business Intelligence (BI) für Geräte und Standorte eine kostengünstige, einfache Bereitstellung für Cloud-Berichterstellungsfunktionen. Schließlich können Benutzer mit dem Azure-Angebot nicht nur VHDs in die Cloud verschieben, sondern auch eine VHD zurückkopieren und sie lokal oder über einen anderen Cloud-Anbieter ausführen, solange sie über die geeignete Lizenz dafür verfügen.

<h2>Bereitstellung</h2>

Dieser Unterabschnitt behandelt die Grundlagen der Bereitstellung in Azure. Die <strong>Abbildbibliothek</strong> in Azure enthält eine Liste der verfügbaren vorkonfigurierten VMs. Benutzer können ISO/VHDs von SharePoint Server, SQL Server, Windows Server und anderen in der Abbildbibliothek veröffentlichen. Um die Erstellung von VMs zu erleichtern, werden Basis-Images angelegt und in der Bibliothek veröffentlicht. Autorisierte Benutzer können anhand dieser Images die gewünschte VM generieren. Weitere Informationen finden Sie auf der Azure-Site unter <a href="/de-de/manage/windows/tutorials/virtual-machine-from-gallery/">Erstellen eines virtuellen Computers, auf dem Windows Server 2008 R2 ausgeführt wird</a>. Abbildung 2 zeigt die grundlegenden Schritte, um eine VM mit dem Azure-Verwaltungsportal zu erstellen:

<p class="caption">Abbildung 2: Übersicht über die Schritte zum Erstellen einer VM</p>
![azure-sharepoint-wp-2](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

Benutzer können im Azure-Verwaltungsportal auch ein vom System erstelltes Image hochladen. Weitere Informationen finden Sie unter <a href="/de-de/manage/windows/common-tasks/upload-a-vhd/">Erstellen und Hochladen einer virtuellen Festplatte</a>. Abbildung 3 zeigt die grundlegenden Schritte für das Hochladen eines Image, um eine VM zu erstellen:

<p class="caption">Abbildung 3: Übersicht über die Schritte zum Hochladen eines Image</p>
![azure-sharepoint-wp-3](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

<h3>Bereitstellen von SharePoint 2010 auf Azure</h3>

Sie können SharePoint 2010 auf Azure mit den folgenden Schritten bereitstellen:

<ol>
<li>Melden Sie sich auf dem <a href="http://manage.windowsazure.com/">Azure-Verwaltungsportal</a> über Ihr Azure-Abonnementkonto an.
<ul>

<li>Wenn Sie kein Azure-Konto haben, <a href="http://www.windowsazure.com/de-de/pricing/free-trial/">melden Sie sich für eine kostenlose Testversion von Azure</a> an.</li>
</ul>
</li>

<li>Klicken Sie zum Erstellen einer VM mit dem Basisbetriebssystem im Azure-Verwaltungsportal auf <strong>NEU > SERVER > VIRTUELLER COMPUTER > AUS KATALOG</strong>.</li>

<li>Das Dialogfeld <strong>Image auswählen</strong> wird angezeigt. Klicken Sie auf das Plattformimage <strong>Windows Server 2008 R2 SP1</strong> und dann auf den Pfeil nach rechts.</li>

<li>Das Dialogfeld <strong><em>Konfiguration des virtuellen Computers</em></strong> wird angezeigt. Geben Sie die folgenden Informationen ein:

<ul>
<li>Geben Sie einen <strong>NAMEN DES VIRTUELLEN COMPUTERS</strong> ein.
</li>
<li>Wählen Sie die passende <strong>GRÖSSE</strong> aus.
<ul>
<li>Für eine Produktionsumgebung (SharePoint-Anwendungsserver und -Datenbank) empfehlen wir A3 <em>(4 Kerne, 7 GB Arbeitsspeicher)</em>.</li>
</ul>
</li>
<li>Geben Sie in <strong>NEUER BENUTZERNAME</strong> einen lokalen Administratorkontonamen ein.</li>
<li>Geben Sie in das Feld <strong>NEUES KENNWORT</strong> ein sicheres Kennwort ein.</li>
<li>Geben Sie im Feld <strong>BESTÄTIGEN</strong> das Kennwort erneut ein, und klicken Sie dann auf den Pfeil nach rechts.</li>
</ul>
<li>Das zweite Dialogfeld <strong>Konfiguration des virtuellen Computers</strong> wird angezeigt. Geben Sie die folgenden Informationen ein:
<ul>
<li>Wählen Sie im Feld <strong>CLOUDDIENST</strong> eine der folgenden Möglichkeiten aus:
<ul>
<li><strong>Einen neuen Clouddienst erstellen</strong>. In diesem Fall müssen Sie auch einen Clouddienst-DNS-Namen angeben.</li>
<li>Wählen Sie einen vorhandenen Clouddienst aus.</li>
</ul>
<li>Wählen Sie im Feld <strong>REGION/AFFINITÄTSGRUPPE/VIRTUELLES NETZWERK</strong> die Region aus, in der das virtuelle Image gehostet wird.</li>
<li>Wählen Sie im Feld <strong>SPEICHERKONTO</strong> eine der folgenden Möglichkeiten aus:
<ul>
<li><strong>Ein automatisch generiertes Speicherkonto verwenden</strong>.</li>
<li>Wählen Sie einen vorhandenen Speicherkontonamen aus.</li>
<ul>
<li>Es wird nur ein Speicherkonto pro Region automatisch erstellt. Alle anderen mit dieser Einstellung erstellten VMs werden in dieses Speicherkonto aufgenommen.</li>
<li>Es sind maximal 20 Speicherkonten möglich.</li>
<li>Weitere Informationen finden Sie unter <a href="/de-de/manage/windows/common-tasks/upload-a-vhd/#createstorage">Erstellen eines Speicherkontos in Azure</a>.</li>
</ul>
</li>
<li>Wählen Sie im Feld <strong>VERFÜGBARKEITSGRUPPE</strong> die Option <STRONG>(Keine)</STRONG> aus, und klicken Sie dann auf den Pfeil nach rechts.</li>
</ul>
</li>
</ul>
</li>
<li>Aktivieren Sie im dritten Dialogfeld <strong>Konfiguration des virtuellen Computers</strong> die Kontrollkästchen zum Erstellen des virtuellen Computers.</li>


<li>So stellen Sie eine Verbindung zum virtuellen Computer her
<ul>
<li>Öffnen Sie die VM mit Remotedesktop.</li>
<li>Wählen Sie im Azure-Verwaltungsportal Ihre VM aus, und wählen Sie dann die Seite <strong>DASHBOARD</strong> aus.</li>
<li>Klicken Sie auf <strong>Verbinden</strong>.</li>
</ul>
</li>
<li>Erstellen Sie die SQL Server-VM mit einer der folgenden Optionen:
<ul>
<li>Erstellen Sie anhand der Schritte 1 bis 7 eine SQL Server 2012-VM. Verwenden Sie aber in <strong>Schritt 3</strong> das SQL Server 2012-Image anstelle des Windows Server 2008 R2 SP1-Image. Weitere Informationen finden Sie unter <a href="/de-de/manage/windows/common-tasks/install-sql-server/">Bereitstellen eines virtuellen Computers mit SQL Server auf Azure</a>.
<ul>
<li>Wenn Sie diese Option auswählen, wird bei der Bereitstellung eine Kopie der SQL Server 2012-Setupdateien im Verzeichnis <em>C:\SQLServer_11.0_Full</em> gespeichert, damit Sie die Installation anpassen können. Sie können beispielsweise die Evaluierungsinstallation von SQL Server 2012 mit Ihrem Lizenzschlüssel in eine lizenzierte Version umwandeln.</li>
</ul>
</li>
<li>Mit dem SQL Server-Systemvorbereitungstool (SysPrep) können Sie SQL Server auf der VM mit einem Basisbetriebssystem installieren (siehe Schritte 1 bis 7). Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/ee210664.aspx">Installieren von SQL Server 2012 mit SysPrep</a>.</li>
<li>Installieren Sie SQL Server über die Eingabeaufforderung. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/ms144259.aspx#SysPrep">Installieren von SQL Server 2012 von der Eingabeaufforderung aus</a>.</li>
<li>Mit unterstützten SQL Server-Medien und Ihrem Lizenzschlüssel können Sie SQL Server auf der VM mit einem Basisbetriebssystem installieren (siehe Schritte 1 bis 7).</li>
</ul>
</li>
<li>Erstellen Sie die SharePoint-Farm anhand der folgenden Unterschritte:
<ul>
<li>Unterschritt 1: Konfigurieren Sie das Azure-Abonnement mit Skriptdateien.</li>
<li>Unterschritt 2: Stellen Sie SharePoint-Server bereit, indem Sie eine weitere VM mit einem Basisbetriebssystem erstellen (siehe Schritte 1 bis 7). Um einen SharePoint-Server auf dieser VM zu erstellen, wählen Sie eine der folgenden Optionen:
<ul>
<li>Bereitstellen mit der SharePoint-GUI:
<ul>
<li>Informationen zum Erstellen und Bereitstellen einer SharePoint-Farm finden Sie unter <a href="http://technet.microsoft.com/de-de/library/ee805948.aspx#CreateConfigure">Erstellen einer Microsoft SharePoint-Serverfarm</a>.</li>
<li>Informationen zum Hinzufügen eines Web- oder Anwendungsservers zu der Farm finden Sie unter <a href="http://technet.microsoft.com/de-de/library/cc261752.aspx">Hinzufügen von Web- oder Anwendungsservern zu Farmen (SharePoint Server 2010)</a>.</li>
<li>
<p>Informationen zum Hinzufügen eines Datenbankservers zu einer vorhandenen Farm finden Sie unter <a href="http://technet.microsoft.com/de-de/library/cc262781">Hinzufügen eines Datenbankservers zu einer vorhandenen Farm</a>.</p>
<ul>
<li>Um SQL Server 2012 für Ihre SharePoint-Farm zu verwenden, müssen Sie Service Pack 1 für SharePoint Server 2010 nach der Installation der Anwendung, und ohne den Server zu konfigurieren, herunterladen und installieren. Weitere Informationen finden Sie unter <a href="http://www.microsoft.com/de-de/download/details.aspx?id=26623">Service Pack 1 für SharePoint Server 2010</a>.</li>
<li>Um die Vorteile der BI-Funktionen von SQL Server zu nutzen, sollten Sie SharePoint Server als Serverfarm anstatt als einen eigenständigen Server installieren. Weitere Informationen finden Sie unter <a href="http://technet.microsoft.com/de-de/library/hh231681(v=sql.110).aspx">Installieren von SQL Server 2012 Business Intelligence-Funktionen</a>.</li>
</ul>
</li>
</ul>
</li>
<li>Bereitstellen mit Microsoft Windows PowerShell: Sie können mit dem Befehlszeilentool Psconfig als alternative Schnittstelle verschiedene Vorgänge zur Steuerung der Bereitstellung von SharePoint 2010-Produkten ausführen. Weitere Informationen finden Sie unter <a href="http://technet.microsoft.com/de-de/library/cc263093.aspx">Psconfig-Befehlszeilenreferenz</a>.</li>
</ul>
</li>
<li>Unterschritt 3: Konfigurieren Sie SharePoint. Wenn sich alle SharePoint-VMs im Bereit-Zustand befinden, konfigurieren Sie SharePoint Server auf jedem Server anhand einer der folgenden Optionen:
<ul>
<li>Konfigurieren von SharePoint mithilfe der GUI.</li>
<li>Konfigurieren von SharePoint mithilfe von Windows PowerShell. Weitere Informationen finden Sie unter <a href="http://technet.microsoft.com/de-de/library/cc262839.aspx">Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell</a>.
<ul>
<li>Sie können auch AutoSPInstaller aus dem CodePlex-Projekt verwenden, das aus Windows PowerShell-Skripten, einer XML-Eingabedatei und einer Microsoft Windows-Standardbatchdatei besteht. AutoSPInstaller stellt ein Framework für ein SharePoint 2010-Installationsskript auf Grundlage von Windows PowerShell bereit. Weitere Informationen finden Sie unter <a href="http://autospinstaller.codeplex.com/">CodePlex: AutoSPInstaller</a>.

<strong>Hinweis</strong>: Konfigurieren Sie Sicherheitsoptionen am Endpunkt des Verwaltungsportals, und legen Sie einen eingehenden Port in der Windows Firewall der VM fest. Überprüfen Sie anschließend, ob Sie eine Remotesitzung mit Windows PowerShell für einen der SharePoint-Anwendungsserver starten können, indem Sie eine Windows PowerShell-Sitzung mit Administratoranmeldeinformationen öffnen.
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li>Stellen Sie nach Beendigung des Skripts eine Verbindung zu der VM über das VM-Dashboard her.</li>
<li>Überprüfen Sie die SharePoint-Konfiguration: Melden Sie sich beim SharePoint-Server an, und überprüfen Sie mit der Zentraladministration die Konfiguration.</li>
</ol>

<h3>Erstellen und Hochladen einer virtuellen Festplatte</h3>

Sie können auch eigene Images erstellen und sie auf Azure als VHD-Datei hochladen. Führen Sie die folgenden Schritte aus, um eine VHD-Datei zu erstellen und auf Azure hochzuladen:

<ol>
<li>Erstellen eines Hyper-V-fähigen Image: Verwenden Sie den Hyper-V-Manager, um die Hyper-V-fähige VHD zu erstellen. Weitere Informationen finden Sie unter <a href="http://technet.microsoft.com/de-de/library/cc742509">Erstellen virtueller Festplatten</a>.</li>
<li>Erstellen eines Speicherkontos in Azure: Ein Speicherkonto in Azure ist erforderlich, um eine VHD-Datei hochzuladen, mit der eine VM erstellt werden kann. Dieses Konto kann im Azure-Verwaltungsportal erstellt werden. Weitere Informationen finden Sie unter <a href="/de-de/manage/windows/common-tasks/upload-a-vhd/">Erstellen eines Speicherkontos in Azure</a>.</li>
<li>Vorbereiten des hochzuladenden Image: Bevor das Image auf Azure hochgeladen werden kann, muss es mit dem Befehl "SysPrep" generalisiert werden. Weitere Informationen finden Sie unter <a href="http://technet.microsoft.com/de-de/library/bb457073.aspx">Verwenden von SysPrep: Einführung</a>.</li>
<li>Hochladen des Image auf Azure: Um ein Image hochzuladen, das in einer VHD-Datei enthalten ist, müssen Sie ein Verwaltungszertifikat erstellen und installieren. Rufen Sie den Fingerabdruck des Zertifikats und die Abonnement-ID ab. Legen Sie die Verbindung fest, und laden Sie die VHD-Datei mit dem Befehlszeilentool CSUpload hoch. Weitere Informationen finden Sie unter <a href="/de-de/manage/windows/common-tasks/upload-a-vhd/">Hochladen des Image auf Azure</a>.</li>
</ol>

<h2>Verwendungsszenarien</h2>

Dieser Abschnitt behandelt einige führende Kundenszenarien für SharePoint-Bereitstellungen mit Azure Virtual Machines. Jedes Szenario ist in zwei Teile gegliedert - eine kurze Beschreibung des Szenarios gefolgt von Schritten für den Einstieg.

<h3>Szenario 1: Einfache SharePoint-Bereitstellung und -Testumgebung</h3>

<h4>Beschreibung</h4>

Organisationen suchen nach agileren Möglichkeiten, um SharePoint-Anwendungen zu erstellen und SharePoint-Umgebungen für die Bereitstellung und den Test, sowohl vor Ort als auch ausgelagert, zu konfigurieren. Im Grunde möchten sie die Zeit für das Einrichten von SharePoint-Anwendungsentwicklungsprojekten verkürzen und Kosten durch Nutzung der Testumgebungen senken. Eine Organisation möchte beispielsweise einen bedarfsgesteuerten Auslastungstest auf SharePoint Server und Benutzerakzeptanztests (UAT) mit mehr gleichzeitigen Benutzern an unterschiedlichen geografischen Standorten durchführen. Zudem ist für viele Organisationen die Integration von lokalen/externen Teams ein zunehmend wichtiges Geschäftserfordernis.

Dieses Szenario beschreibt, wie Organisationen vorkonfigurierte SharePoint-Farmen für Entwicklungs- und Testarbeitsauslastungen einsetzen können. Eine SharePoint-Bereitstellungstopologie unterscheidet sich nicht von einer lokalen virtualisierten Bereitstellung. Vorhandene IT-Fähigkeiten können 1:1 für eine Azure Virtual Machines-Bereitstellung eingesetzt werden, mit dem großen Vorteil, einer fast vollständigen Kostenverschiebung von Investitionsaufwendungen zu operativen Aufwendungen - es müssen dafür keine physischen Server angeschafft werden. Organisationen können Investitionskosten für Serverhardware vermeiden und größere Flexibilität erzielen, indem die Dauer für das Erstellen, Konfigurieren oder Erweitern einer SharePoint-Farm für eine Test- oder Entwicklungsumgebung erheblich reduziert wird. Die IT kann je nach Test- und Entwicklungsbedarf Kapazitäten dynamisch hinzufügen und entfernen. Zudem kann sich die IT mehr auf die Wertschöpfung durch SharePoint-Projekte konzentrieren als auf die Verwaltung der Infrastruktur.

Um Auslastungstest-Computer vollständig zu nutzen, können Organisationen virtualisierte SharePoint-Entwicklungs- und Testcomputer auf Azure mit Betriebssystemunterstützung für Windows Sever 2008 R2 konfigurieren. Dies ermöglicht Entwicklungsteams, Anwendungen zu erstellen und zu testen und auf einfache Weise ohne Codeänderungen auf lokale oder Cloud-Produktionsumgebungen zu migrieren. Die gleichen Frameworks und Toolsets können vor Ort und in der Cloud verwendet werden, sodass verteilten Teams der Zugang zu derselben Umgebung ermöglicht wird. Benutzer können durch Herstellen einer direkten VPN-Verbindung auch auf lokale Daten und Anwendungen zugreifen.

<h4>Erste Schritte</h4>

Abbildung 4 zeigt eine SharePoint-Entwicklungs- und Testumgebung in einer Azure-VM. Um diese Bereitstellung zu erstellen, verwenden Sie zunächst dieselbe lokale SharePoint-Entwicklungs- und Testumgebung, in der Sie Anwendungen entwickeln. Laden Sie anschließend die Anwendungen in die Azure-VM hoch, und stellen Sie sie bereit, um Tests und weitere Entwicklungen durchzuführen. Wenn Ihre Organisation entscheidet, die Anwendung wieder lokal zu nutzen, kann dies ohne die Änderung der Anwendung geschehen.

<p class="caption">Abbildung 4: SharePoint-Entwicklungs- und Testumgebung in Azure Virtual Machines</p>

![azure-sharepoint-wp-11](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

<h4>Konfigurieren der Umgebung für das Szenario</h4>

Führen Sie die folgenden Schritte aus, um eine SharePoint-Entwicklungs- und Testumgebung auf Azure zu implementieren:

<ol>
<li><em>Bereitstellung</em>: Stellen Sie zuerst mit Azure Virtual Network eine VPN-Verbindung zwischen Ihrem Standort und Azure bereit. (Da hier nicht Active Directory verwendet wird, ist ein VPN-Tunnel erforderlich.) Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx">Virtuelle Netzwerke im Überblick (Entwurfsüberlegungen, Sichere Verbindungsszenarien)</a>. Stellen Sie dann im Verwaltungsportal eine neue VM mithilfe eines vorhandenen Image aus der Abbildbibliothek bereit.
<ul>
<li>Sie können die lokalen SharePoint-Entwicklungs- und Test-VMs in Ihr Azure-Speicherkonto hochladen und über die Abbildbibliothek auf diese VMs verweisen, um die erforderliche Umgebung zu erstellen.</li>
<li>Sie können das SQL Server 2012-Image anstelle des Windows Server 2008 R2 SP1-Image verwenden. Weitere Informationen finden Sie unter <a href="/de-de/manage/windows/common-tasks/install-sql-server/">Bereitstellen eines virtuellen Computers mit SQL Server auf Azure</a>.</li>
</ul>
</li>
<li><em>Installation</em>: Installieren Sie mithilfe einer Remotedesktopverbindung SharePoint Server, Visual Studio und SQL Server auf den VMs.
<ul>
<li>Wählen Sie für die Installation von SharePoint Server eine Option:
<ul>
<li>Erstellen eines SharePoint-Entwicklercomputers mit dem SharePoint 2010 Easy Setup Script. Weitere Informationen finden Sie unter <a href="http://www.microsoft.com/de-de/download/details.aspx?id=23415">SharePoint 2010 Easy Setup Script</a>.</li>
<li>Verwenden von Windows PowerShell. Weitere Informationen finden Sie unter <a href="http://technet.microsoft.com/de-de/library/cc262839.aspx">Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell</a>.</li>
<li>Verwenden von AutoSPInstaller aus dem CodePlex-Projekt. Weitere Informationen finden Sie unter <a href="http://autospinstaller.codeplex.com/">CodePlex: AutoSPInstaller</a>.</li>
</ul>
</li>
<li>Installieren von Visual Studio. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/e2h7fzkw.aspx">Installieren von Visual Studio</a>.</li>
<li>Installieren von SQL Server. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/ee210664.aspx">Installieren von SQL Server mit SysPrep</a>.
<ul>
<li>Informationen zum Erstellen und Konfigurieren von SQL Server 2012 für eine SharePoint-Farmbereitstellung finden Sie in den praktischen Übungen: <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint">Konfigurieren von SQL Server für SharePoint in Azure</a>.</li>
<li>Informationen zum Erstellen einer SharePoint-Farm durch Konfigurieren von Active Directory und Verwenden einer einzelnen SQL Server-Datenbank finden Sie in den praktischen Übungen: <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs">Bereitstellen einer SharePoint-Farm mit Azure Virtual Machines</a></li>
</ul>
</li>
</ul>
</li>
<li><em>Entwickeln von Bereitstellungspaketen und -skripten für Anwendungen und Datenbanken</em>: Wenn Sie vorhaben, eine VM aus der Abbildbibliothek zu verwenden, können Sie die lokalen Anwendungen und Datenbanken auf Azure Virtual Machines bereitstellen:
<ul>
<li>Erstellen Sie mit SQL Server Data Tools und Visual Studio Bereitstellungspakete für vorhandene Anwendungen und Datenbanken.</li>
<li>Stellen Sie mit diesen Paketen die Anwendungen und Datenbanken auf Azure Virtual Machines bereit.</li>
</ul>
</li>
<li><em>Bereitstellen von SharePoint-Anwendungen und -Datenbanken</em>:
<ul>
<li>Konfigurieren Sie Sicherheitsoptionen am Endpunkt des Verwaltungsportals, und legen Sie einen eingehenden Port in der Windows Firewall der VM fest.</li>
<li>Stellen Sie SharePoint-Anwendungen und -Datenbanken auf Azure Virtual Machines mit den in Schritt 3 erstellten Bereitstellungspaketen und -skripten bereit.</li>
<li>Testen Sie bereitgestellte Anwendungen und Datenbanken.</li>
</ul>
</li>
<li><em>Verwalten von VMs</em>:
<ul>
<li>Überwachen Sie die VMs im Verwaltungsportal.</li>
<li>Überwachen Sie die Anwendungen mit Visual Studio und SQL Server Management Studio.</li>
<li>Sie können VMs auch mit lokaler Verwaltungssoftware, wie Microsoft System Center - Operations Manager, überwachen und verwalten.</li>
</ul>
</li>
</ol>
<h3>Szenario 2: Öffentliche SharePoint-Farm mit Anpassungen</h3>

<h4>Beschreibung</h4>

Organisationen möchten eine Internetpräsenz erstellen, die in der Cloud gehostet wird und auf Basis von Bedarf und Nachfrage einfach skalierbar ist. Außerdem möchten sie Partner-Extranetwebsites für die Zusammenarbeit erstellen und ein einfaches Verfahren für die verteilte Erstellung und Genehmigung des Websiteinhalts implementieren. Schließlich möchten diese Organisationen auf ihren Websites bedarfsgesteuerte Kapazitäten vorsehen, um auf wachsende Auslastungen reagieren zu können.

In diesem Szenario wird SharePoint Server als Basis für das Hosten einer öffentlichen Website verwendet. Organisationen haben damit die Möglichkeit, ihre Geschäftswebsites in einer sicheren, skalierbaren Cloud-Infrastruktur schnell bereitzustellen, anzupassen und zu hosten. Mit öffentlichen SharePoint-Websites auf Azure können Organisationen mit dem Anwachsen des Datenverkehrs Kapazitäten skalieren und nur für das bezahlen, was sie nutzen. Allgemeine Tools, vergleichbar mit den lokal verwendeten, können mit SharePoint für die Inhaltserstellung, den Workflow und die Genehmigung auf Azure eingesetzt werden.

Außerdem können Organisationen mit Azure Virtual Machines Staging- oder Produktionsumgebungen, die auf VMs laufen, einfach konfigurieren. Öffentliche, in Azure erstellte SharePoint-VMs können im virtuellen Speicher gesichert werden. Für die Notfallwiederherstellung ist es zusätzlich möglich, mit der fortlaufenden Georeplikationsfunktion VMs aus einem Rechenzentrum automatisch in einem anderen weit entfernten Rechenzentrum zu sichern. (Weitere Informationen über Georeplikation finden Sie unter <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx">Einführung in die Georeplikation für Azure Storage</a>).

VMs in der Azure-Infrastruktur werden in Bezug auf die Zusammenarbeit mit anderen Microsoft-Produkten, wie SQL Server und SharePoint Server, überprüft und unterstützt. Azure und SharePoint Server eignen sich besonders für eine gemeinsame Nutzung: Beide sind Teil der Microsoft-Familie und sorgfältig aufeinander abgestimmt, und beide wurden gemeinsam getestet, um eine optimale Leistung zu erzielen. Beide haben einen einzelnen Unterstützungspunkt für die SharePoint-Anwendung und die Azure-Infrastruktur.

<h4>Erste Schritte</h4>

In diesem Szenario müssen weitere Front-End-Webserver für SharePoint Server hinzugefügt werden, um zusätzlichen Datenverkehr zu unterstützen. Diese Server benötigen eine verbesserte Sicherheit und Domänencontroller der Active Directory-Domänendienste für die Benutzerauthentifizierung und -autorisierung. Abbildung 5 zeigt das Layout dieses Szenarios.

<p class="caption">Abbildung 5: Öffentliche SharePoint-Farm mit Anpassungen</p>
![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

<h4>Konfigurieren der Umgebung für das Szenario</h4>

Führen Sie die folgenden Schritte aus, um eine öffentliche SharePoint-Farm auf Azure zu implementieren:

<ol>
<li><em>Bereitstellen von Active Directory</em>: Die Grundanforderungen für die Bereitstellung von Active Directory auf Azure Virtual Machines sind den Anforderungen für die Bereitstellung auf lokalen VMs (und bis zu einem gewissen Ausmaß auf physischen Computern) ähnlich, aber nicht identisch damit. Weitere Information zu den Unterschieden sowie Richtlinien und andere Überlegungen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/jj156090">Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure</a>. So stellen Sie Active Directory auf Azure bereit:
<ul>
<li>Definieren und erstellen Sie ein virtuelles Netzwerk, in dem die VMs zu bestimmten Subnetzen zugewiesen werden können. Weitere Informationen finden Sie unter <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Konfigurieren von virtuellen Netzwerken</a>.</li>
<li>Erstellen Sie im Verwaltungsportal den Domänencontroller auf der neuen VM in Azure, und stellen Sie ihn bereit. Weitere Informationen finden Sie unter <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Bereitstellen und Erstellen des Domänencontrollers</a>.
<ul>
<li>Sie können auch anhand des Windows PowerShell-Skripts eine eigenständige Domäne in der Cloud mit Azure Virtual Machines und Virtual Network bereitstellen. Weitere Informationen finden Sie unter <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS">Bereitstellen von Active Directory in Azure (Windows PowerShell)</a>.</li>
<li>Weitere Informationen zum Erstellen einer neuen Active Directory-Gesamtstruktur auf einer VM in Azure Virtual Network finden Sie unter <a href="/de-de/manage/services/networking/active-directory-forest/">Installieren einer neuen Active Directory-Gesamtstruktur in Azure</a>.</li>
</ul>
</li>
</ul>
</li>
<li><em>Bereitstellen einer VM</em>: Stellen Sie im Verwaltungsportal eine neue VM mithilfe eines vorhandenen Image aus der Abbildbibliothek bereit.</li>
<li><em>Bereitstellen einer SharePoint-Farm</em>:
<ul>
<li>Installieren Sie mit der neu bereitgestellten VM SharePoint, und generieren Sie ein wiederverwendbares Image. Weitere Informationen zum Installieren von SharePoint Server finden Sie unter <a href="http://technet.microsoft.com/de-de/library/cc262839.aspx">Installieren und Konfigurieren von SharePoint Server 2010 mithilfe von Windows PowerShell</a> oder <a href="http://autospinstaller.codeplex.com/">CodePlex: AutoSPInstaller</a>.</li>
<li>Konfigurieren Sie die SharePoint-VM so, dass die SharePoint-Farm erstellt und eine Verbindung damit hergestellt wird.</li>
<li>Konfigurieren Sie im Verwaltungsportal den Lastenausgleich.
<ul>
<li>Konfigurieren Sie die VM-Endpunkte, wählen Sie die Option zum Laden des Ausgleichsdatenverkehrs auf einen vorhandenen Endpunkt, und geben Sie dann den Namen der VM mit dem Lastenausgleich an.</li>
<li>Fügen Sie der vorhandenen SharePoint-Farm eine weitere Front-End-Web-VM für zusätzlichen Datenverkehr hinzu.</li>
</ul>
</li>
</ul>
</li>
<li><em>Verwalten von VMs</em>:
<ul>
<li>Überwachen Sie die VMs im Verwaltungsportal.</li>
<li>Überwachen Sie die SharePoint-Farm mit der Zentraladministration.</li>
</ul>
</li>
</ol>

<h3>Szenario 3: Horizontal skalierte Farm für weitere BI-Dienste</h3>

<h4>Beschreibung</h4>

Business Intelligence ist unerlässlich, um wichtige Erkenntnisse zu gewinnen und schnelle, fundierte Entscheidungen zu treffen. Wenn Organisationen von einem lokalen Ansatz zur Bereitstellung von BI-Anwendungen in der Cloud übergehen, möchten sie keine Änderungen an der BI-Umgebung vornehmen. Berichte von SQL Server Analysis Services (SSAS) oder SQL Server Reporting Services (SSRS) sollen in einer sehr stabilen Umgebung mit hoher Verfügbarkeit gehostet und dabei die komplette Kontrolle über die BI-Anwendung aufrechterhalten werden - und das alles mit geringem Zeit- und Kostenaufwand für die Wartung.

Dieses Szenario beschreibt, wie Organisationen mit Azure Virtual Machines geschäftskritische BI-Anwendungen hosten können. Organisationen können SharePoint-Farmen in Azure Virtual Machines bereitstellen und die BI-Komponenten der Anwendungsserver-VM, wie SSRS oder Excel Services, horizontal skalieren. Durch die Skalierung ressourcenintensiver Komponenten in der Cloud können besondere Arbeitsauslastungen besser und einfacher unterstützt werden. SQL Server eignet sich gut für Azure Virtual Machines, weil SQL Server-Instanzen einfach von kleinen bis zu sehr großen Installationen skaliert werden können. Die dadurch erreichte Flexibilität ermöglicht Organisationen, BI-Instanzen auf Basis unmittelbarer Arbeitsauslastungsanforderungen dynamisch bereitzustellen (zu erweitern) bzw. die Bereitstellung zurückzunehmen (zu verkleinern).

Die Migration vorhandener BI-Anwendungen auf Azure ermöglicht eine bessere Skalierung. Mit SSAS, SSRS und SharePoint Server können Organisationen leistungsstarke BI- und Berichtsanwendungen und -Dashboards erstellen, die nach oben und unten skalierbar sind. Diese Anwendungen und Dashboards können zudem sicherer in lokale Daten und Anwendungen integriert werden. Azure sorgt für die Rechenzentrums-Compilance mit Unterstützung für ISO 27001. Weitere Informationen finden Sie unter <a href="/de-de/support/trust-center/compliance/">Microsoft Azure-Vertrauenscenter</a>.

<h4>Erste Schritte</h4>

Um die Bereitstellung von BI-Komponenten horizontal zu skalieren, muss ein neuer Anwendungsserver mit Diensten, wie PowerPivot, Power View, Excel Services oder PerformancePoint Services, installiert werden. Oder der vorhandenen Farm müssen SQL Server-BI-Instanzen, wie SSAS oder SSRS, hinzugefügt werden, um zusätzliche Abfrageverarbeitungen zu unterstützen. Der Server kann als neue Azure-VM mit installiertem SharePoint 2010 Server oder SQL Server hinzugefügt werden. Anschließend können auf diesem Server die BI-Komponenten installiert, bereitgestellt und konfiguriert werden (Abbildung 6).

<p class="caption">Abbildung 6: Für zusätzliche BI-Dienste horizontal skalierte SharePoint-Farm</p>

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

<h4>Konfigurieren der Umgebung für das Szenario</h4>

Führen Sie die folgenden Schritte aus, um eine BI-Umgebung auf Azure horizontal zu skalieren:

<ol>
<li><em>Bereitstellung</em>:
<ul>
<li>Stellen Sie mit Azure Virtual Network eine VPN-Verbindung zwischen Ihrem Standort und Azure bereit. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx">Virtuelle Netzwerke im Überblick (Entwurfsüberlegungen, Sichere Verbindungsszenarien)</a>.</li>
<li>Stellen Sie im Verwaltungsportal eine neue VM mithilfe eines vorhandenen Image aus der Abbildbibliothek bereit.
<ul>
<li>Sie können BI-Arbeitsauslastungs-Images von SharePoint Server oder SQL Server in die Abbildbibliothek hochladen, und jeder autorisierte Benutzer kann diese BI-Komponenten-VMs auswählen, um die horizontal skalierte Umgebung zu erstellen.</li>
</ul>
</li>
</ul>
</li>
<li><em>Installation</em>: Wenn Ihre Organisation über keine vorgefertigten Images von SharePoint Server- oder SQL Server-BI-Komponenten verfügt, installieren Sie SharePoint Server und SQL Server mit einer Remotedesktopverbindung auf den VMs.
<ul>
<li>Weitere Informationen zum Installieren von SharePoint finden Sie unter <a href="http://technet.microsoft.com/de-de/library/cc262839.aspx">Installieren von SharePoint Server 2010 mithilfe von Windows PowerShell</a> oder <a href="http://autospinstaller.codeplex.com/">CodePlex: AutoSPInstaller</a>.</li>
<li>Weitere Informationen zum Installieren von SQL Server finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/ee210664.aspx">Installieren von SQL Server mit SysPrep</a>.</li>
<li>Informationen zum Erstellen und Konfigurieren von SQL Server 2012 für eine SharePoint-Farmbereitstellung finden Sie in den praktischen Übungen: <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint">Konfigurieren von SQL Server für SharePoint in Azure</a>.</li>
<li>Informationen zum Erstellen einer SharePoint-Farm durch Konfigurieren von Active Directory und Verwenden einer einzelnen SQL Server-Datenbank finden Sie in den praktischen Übungen: <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs">Bereitstellen einer SharePoint-Farm mit Azure Virtual Machines</a></li>
</ul>
</li>
<li><em>Hinzufügen der BI-VM</em>:
<ul>
<li>Konfigurieren Sie Sicherheitsoptionen am Endpunkt des Verwaltungsportals, und legen Sie einen eingehenden Port in der Windows Firewall der VM fest.</li>
<li>Fügen Sie die neu erstellte BI-VM der vorhandenen SharePoint- oder SQL Server-Farm hinzu.</li>
</ul>
</li>
<li><em>Verwalten von VMs</em>:
<ul>
<li>Überwachen Sie die VMs im Verwaltungsportal.</li>
<li>Überwachen Sie die SharePoint-Farm mit der Zentraladministration.</li>
<li>Überwachen und verwalten Sie VMs mit lokaler Verwaltungssoftware, wie Microsoft System Center - Operations Manager.</li>
</ul>
</li>
</ol>
<h3>Szenario 4: Vollständig angepasste SharePoint-basierte Website</h3>

<h4>Beschreibung</h4>

Organisationen möchten zunehmend vollständig angepasste SharePoint-Websites in der Cloud erstellen. Sie benötigen eine äußerst stabile Umgebung mit hoher Verfügbarkeit und umfangreichen Wartungsmöglichkeiten komplexer Anwendungen in der Cloud ohne großen Zeit- und Kostenaufwand.

In diesem Szenario kann eine Organisation ihre gesamte SharePoint-Farm in der Cloud bereitstellen und alle Komponenten dynamisch skalieren, um zusätzliche Kapazitäten zu erhalten, oder sie kann ihre lokale Bereitstellung auf die Cloud ausweiten, um bei Bedarf die Kapazität zu erhöhen und die Leistung zu steigern. Das Szenario konzentriert sich auf Organisationen, die eine umfassende "SharePoint-Leistung" für die Anwendungsentwicklung und das Enterprise Content Management möchten. Komplexere Sites können auch erweiterte Berichtserstellungsfunktionen, Power View, PerformancePoint, PowerPivot, detaillierte Diagramme und die meisten anderen SharePoint-Sitefunktionen enthalten, um die gesamte Ende-zu-Ende-Funktionalität bereitzustellen.

Organisationen können mit Azure Virtual Machines angepasste Anwendungen und zugehörige Komponenten in einer kostengünstigen und hochsicheren Cloud-Infrastruktur hosten. Sie können auch ein lokales Microsoft System Center als gemeinsames Verwaltungstool für lokale und Cloud-Anwendungen verwenden.

<h4>Erste Schritte</h4>

Um eine vollständig angepasste SharePoint-Website auf Azure zu implementieren, muss eine Organisation eine Active Directory-Domäne in der Cloud und neue VMs in dieser Domäne bereitstellen. Dann muss eine VM mit SQL Server 2012 als Teil einer SharePoint-Farm erstellt und konfiguriert werden. Schließlich muss die SharePoint-Farm erstellt, der Lastenausgleich verteilt und die Farm mit Active Directory und SQL Server verbunden werden (Abbildung 7).

<p class="caption">Abbildung 7: Vollständig angepasste SharePoint-basierte Website</p>

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

<h4>Konfigurieren der Umgebung für das Szenario</h4>

Die folgenden Schritte zeigen, wie eine angepasste SharePoint-Farmumgebung aus vorgefertigten Images aus der Abbildbibliothek erstellt wird. Sie können aber auch SharePoint-Farm-VMs in die Abbildbibliothek hochladen, und autorisierte Benutzer können anhand dieser VMs die gewünschte SharePoint-Farm auf Azure erstellen.

<ol>
<li>Bereitstellen von Active Directory<em>: Die Grundanforderungen für die Bereitstellung von Active Directory auf Azure Virtual Machines sind den Anforderungen für die Bereitstellung auf lokalen VMs (und bis zu einem gewissen Ausmaß auf physischen Computern) ähnlich, aber nicht identisch damit. Weitere Information zu den Unterschieden sowie Richtlinien und andere Überlegungen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/jj156090">Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure</a>. So stellen Sie Active Directory auf Azure bereit:</em>
<ul>
<li>Definieren und erstellen Sie ein virtuelles Netzwerk, in dem die VMs zu bestimmten Subnetzen zugewiesen werden können. Weitere Informationen finden Sie unter <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Konfigurieren von virtuellen Netzwerken</a>.</li>
<li>Erstellen Sie im Verwaltungsportal den Domänencontroller auf der neuen VM in Azure, und stellen Sie ihn bereit. Weitere Informationen finden Sie unter <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Bereitstellen und Erstellen des Domänencontrollers</a>.
<ul>
<li>Sie können auch anhand des Windows PowerShell-Skripts eine eigenständige Domäne in der Cloud mit Azure Virtual Machines und Virtual Network bereitstellen. Weitere Informationen finden Sie unter <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS">Bereitstellen von Active Directory in Azure (Windows PowerShell)</a>.</li>
<li>Weitere Informationen zum Erstellen einer neuen Active Directory-Gesamtstruktur auf einer VM in Azure Virtual Network finden Sie unter <a href="/de-de/manage/services/networking/active-directory-forest/">Installieren einer neuen Active Directory-Gesamtstruktur in Azure</a>.</li>
</ul>
</li>
</ul>
</li>
<li><em>Bereitstellen von SQL Server</em>:
<ul>
<li>Stellen Sie im Verwaltungsportal eine neue VM mithilfe eines vorhandenen Image aus der Abbildbibliothek bereit.</li>
<li>Konfigurieren Sie SQL Server auf der VM. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/ee210664.aspx">Installieren von SQL Server mit SysPrep</a>.</li>
<li>Verknüpfen Sie die VM mit der neu angelegten Active Directory-Domäne.</li>
</ul>
</li>
<li><em>Bereitstellen einer SharePoint-Farm mit mehreren Servern</em>:
<ul>
<li>Erstellen Sie ein virtuelles Netzwerk. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx">Virtuelle Netzwerke im Überblick (Entwurfsüberlegungen, Sichere Verbindungsszenarien)</a>.
<ul>
<li>Wenn SharePoint-VMs bereitgestellt werden, müssen Subnetze für SharePoint Server vorhanden sein, damit die DNS-Adressen in dem lokalen Active Directory während der Bereitstellung verfügbar sind.</li>
</ul>
</li>
<li>Erstellen Sie im Verwaltungsportal eine VM.</li>
<li>Installieren Sie SharePoint Server auf dieser VM, und generieren Sie ein wiederverwendbares Image. Weitere Informationen zum Installieren von SharePoint Server finden Sie unter <a href="http://technet.microsoft.com/de-de/library/cc262839.aspx">Installieren und Konfigurieren von SharePoint Server 2010 mithilfe von Windows PowerShell</a> oder <a href="http://autospinstaller.codeplex.com/">CodePlex: AutoSPInstaller</a>.</li>
<li>Konfigurieren Sie die SharePoint-VM so, dass die SharePoint-Farm erstellt und eine Verbindung damit mit dem Befehl <a href="http://technet.microsoft.com/de-de/library/ff607979.aspx">Join-SharePointFarm</a> hergestellt wird.</li>
<li>Konfigurieren Sie im Verwaltungsportal den Lastenausgleich:
<ul>
<li>Konfigurieren Sie die VM-Endpunkte, wählen Sie die Option zum Laden des Ausgleichsdatenverkehrs auf einen vorhandenen Endpunkt, und geben Sie dann den Namen der VM mit dem Lastenausgleich an.
<ul>
<li>Weitere Informationen zur Bereitstellung von SharePoint-Farmen auf Azure Virtual Machines finden Sie im Video <a href="http://channel9.msdn.com/Events/TechEd/NorthAmerica/2012/AZR327">TechEd North America 2012</a>.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><em>Verwalten der SharePoint-Farm im System Center</em>:
<ul>
<li>Verbinden Sie mit dem Operations Manager-Agent und dem neuen Azure-Integrationspaket Ihr lokales System Center mit Azure Virtual Machines.</li>
<li>Verwenden Sie den lokalen App Controller und Orchestrator für die Verwaltungsfunktionen.</li>
</ul>
</li>
</ol>

<h2>Zusammenfassung</h2>
Cloud-Computing verändert die Art und Weise, wie die IT Organisationen unterstützt. Der Grund dafür ist, dass Cloud-Computing eine neue Klasse von Vorteilen nutzen kann, unter anderem drastisch gesenkte Kosten gekoppelt mit erhöhter IT-Konzentration, -Agilität und -Flexibilität. Azure ist durch die Bereitstellung einer einfachen, offenen, flexiblen und leistungsstarken virtuellen Infrastruktur die führende Plattform für Cloud-Computing. Azure Virtual Machines senken den Hardwarebedarf, sodass Organisationen Kosten und Komplexität durch Erstellen einer maßgerechten Infrastruktur mit vollständiger Kontrolle und optimierter Verwaltung reduzieren können.

Azure Virtual Machines bieten ein vollständiges Kontinuum von SharePoint-Bereitstellungen. Azure Virtual Machines werden komplett unterstützt und ausgiebig getestet, um zusammen mit anderen Microsoft-Anwendungen eine optimale Leistung zu liefern. Daher können Organisationen SharePoint Server in Azure einfach konfigurieren und bereitstellen, entweder um eine Infrastruktur für eine neue SharePoint-Bereitstellung einzurichten oder um eine vorhandene zu erweitern. Wenn Geschäftsarbeitsauslastungen zunehmen, können Organisationen ihre SharePoint-Infrastruktur schnell erweitern. Ebenso lassen sich bei sinkender Arbeitsauslastung Ressourcen nach Bedarf verringern, wobei nur Kosten für tatsächlich genutzte Ressourcen anfallen. Azure Virtual Machines bieten eine außergewöhnliche Infrastruktur für eine breite Palette von Geschäftsanforderungen, wie anhand der vier SharePoint-basierten Szenarien in diesem Dokument gezeigt wurde.

Voraussetzung für eine erfolgreiche Bereitstellung von SharePoint Server auf Azure Virtual Machines ist eine solide Planung, vor allem in Bezug auf die kritischen Farmarchitektur- und -bereitstellungsoptionen. Die in diesem Dokument erläuterten Erkenntnisse und bewährten Methoden können Sie als Entscheidungshilfe bei der Implementierung einer fundierten SharePoint-Bereitstellung heranziehen.

<h2>Zusätzliche Ressourcen</h2>

<ul>
<li>
<p>SharePoint in Azure-Infrastrukturdiensten</p>
<p><a href="http://msdn.microsoft.com/library/dn275955.aspx">http://msdn.microsoft.com/library/dn275955.aspx</a></p>
</li>
<li>
<p>Erste Schritte mit Azure PowerShell</p>
<p><a href="http://msdn.microsoft.com/de-de/library/windowsazure/jj156055">http://msdn.microsoft.com/de-de/library/windowsazure/jj156055</a></p>
</li>
<li>
<p>Windows Azure-Verwaltungs-Cmdlets</p>
<p><a href="http://msdn.microsoft.com/de-de/library/windowsazure/jj152841">http://msdn.microsoft.com/de-de/library/windowsazure/jj152841</a></p>
</li>
<li>
<p>Befehlszeilentools und PowerShell-Cmdlets für verschiedene Betriebssysteme</p>
<p><a href="/de-de/manage/downloads/">https://www.windowsazure.com/de-de/manage/downloads/</a></p>
</li>
<li>
<p>Anleitungen und Dokumentation von bewährten Methoden</p>
<p><a href="/de-de/manage/windows/">https://www.windowsazure.com/de-de/manage/windows/</a></p>
</li>
</ul>





<!--HONumber=42-->
