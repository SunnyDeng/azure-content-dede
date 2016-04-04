<properties
	pageTitle="Storage Premium: Hochleistungsspeicher für Azure Virtual Machine-Workloads | Microsoft Azure"
	description="Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für E/A-intensive Workloads, die auf virtuellen Azure-Computern ausgeführt werden. Virtuelle Computer der Azure-DS-Serie und -GS-Serie unterstützen Premium-Speicher."
	services="storage"
	documentationCenter=""
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/03/2016"
	ms.author="prkhad"/>


# Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern

## Übersicht

Azure Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für virtuelle Computer mit E/A-intensiven Workloads. Datenträger von virtuellen Computern (VM), die Storage Premium nutzen, speichern Daten auf SSDs (Solid State Drives). Sie können die VM-Datenträger Ihrer Anwendung zu Azure Storage Premium migrieren, um von der Geschwindigkeit und Leistung dieser Laufwerke zu profitieren.

Ein virtueller Azure-Computer unterstützt das Anfügen mehrerer Storage Premium-Datenträger, damit Ihre Anwendung bis zu 64 TB Speicher pro virtuellem Computer nutzen kann. Mit Storage Premium können Ihre Anwendungen bis zu 80.000 IOPS (Input/Output Operations Per Second, E/A-Vorgänge pro Sekunde) pro virtuellem Computer nutzen sowie 2.000 MB Datenträgerdurchsatz pro Sekunde und virtuellem Computer mit äußerst niedriger Latenz für Lesevorgänge erzielen.

Mit Storage Premium bietet Azure die Möglichkeit, anspruchsvollste Unternehmensanwendungen wie Dynamics AX, Dynamics CRM, Exchange Server, SharePoint-Farmen und SAP Business Suite in die Cloud zu verlagern. Sie können eine Vielzahl von leistungsintensiven Datenbankworkloads wie SQL Server, Oracle, MongoDB, MySQL und Redis ausführen, die eine konsistent hohe Leistung und eine geringe Latenz auf Storage Premium benötigen.

>[AZURE.NOTE] Es wird empfohlen, alle Datenträger von virtuellen Computer, die eine hohe IOPS-Leistung erfordern, zu Azure Storage Premium zu migrieren, um die beste Leistung für Ihre Anwendung zu erzielen. Wenn Ihr Datenträger keine hohe IOPS-Leistung erfordert, können Sie die Kosten beschränken, indem Sie sie im Standard-Speicher belassen. Dieser Speicher speichert Daten auf Datenträgern virtueller Computer auf Festplatten (Hard Disk Drives, HDDs) anstelle von SSDs.

Informationen zu den ersten Schritten mit Azure Premium-Speicher finden Sie auf der Seite [Starten Sie kostenlos](https://azure.microsoft.com/pricing/free-trial/). Informationen zur Migration der vorhandenen virtuellen Computer zu Storage Premium finden Sie unter [Migrieren zu Azure Storage Premium](storage-migration-to-premium-storage.md).

>[AZURE.NOTE] Storage Premium wird derzeit in einigen Regionen unterstützt. Die Liste der verfügbaren Regionen finden Sie unter [Azure-Dienste nach Region](https://azure.microsoft.com/regions/#services).

## Storage Premium-Funktionen

**Storage Premium-Datenträger:** Azure Storage Premium unterstützt VM-Datenträger, die an virtuelle Azure-Computer der DS- oder GS-Serie angefügt werden können. Bei Verwendung von Storage Premium haben Sie die Wahl zwischen drei Datenträgergrößen: P10 (128 GiB), P20 (512 GiB) und P30 (1024 GiB), jeweils mit eigenen Leistungsspezifikationen. Je nach Anwendungsanforderung können Sie einen oder mehrere dieser Datenträger an Ihren virtuellen Computer der DS- oder GS-Serie anfügen. Im folgenden Abschnitt [Skalierbarkeits- und Leistungsziele für Storage Premium](#premium-storage-scalability-and-performance-targets) werden die Spezifikationen ausführlicher beschrieben.

**Storage Premium-Seitenblob:** Storage Premium unterstützt Azure-Seitenblobs, die verwendet werden, um persistente Datenträger für virtuelle Azure-Computer zu speichern. Storage Premium unterstützt derzeit nicht Azure Block Blobs, Azure Append Blobs, Azure Files, Azure Tables oder Azure Queues.

**Premium\_LRS-Konto:** Um Storage Premium verwenden zu können, müssen Sie ein Storage Premium-Konto erstellen. Erstellen Sie ein Speicherkonto des Typs „Premium\_LRS“ im [Azure-Portal](https://portal.azure.com) oder mit den folgenden SDK-Bibliotheken: [Storage-REST-API](http://msdn.microsoft.com//library/azure/dd179355.aspx), Version 2014-02-14 oder höher; [Dienstverwaltungs-REST-API](http://msdn.microsoft.com/library/azure/ee460799.aspx), Version 2014-10-01 oder höher (klassische Bereitstellungen); [Referenz zur Azure Storage-Ressourcenanbieter-REST-API](http://msdn.microsoft.com/library/azure/mt163683.aspx) (ARM-Bereitstellungen) und [Azure PowerShell](../powershell-install-configure.md), Version 0.8.10 oder höher. Informationen zu den Begrenzungen von Storage Premium-Konten finden Sie im folgenden Abschnitt über [Skalierbarkeits- und Leistungsziele für Storage Premium](#premium-storage-scalability-and-performance-targets).

**Lokal redundanter Storage Premium:** Ein Storage Premium-Konto ist lokal redundant (LRS) und verwaltet drei Kopien der Daten innerhalb einer einzelnen Region. Überlegungen zur Georeplikation bei der Verwendung von Storage Premium finden Sie in diesem Artikel im Abschnitt [Momentaufnahmen und Kopieren von Blobs](#snapshots-and-copy-blob).

Azure verwendet das Speicherkonto als Container für Ihr Betriebssystem und die Datenträger. Wenn Sie einen virtuellen Azure-Computer der DS- oder GS-Serie erstellen und ein Azure Storage Premium-Konto auswählen, werden Ihr Betriebssystem und die Datenträger in diesem Speicherkonto gespeichert.

Sie können den Premium-Speicher für Datenträger auf zwei Arten verwenden:
- Erstellen Sie zunächst ein neues Storage Premium-Konto. Wählen Sie beim Erstellen eines neuen virtuellen DS- oder GS-Computers dann das Storage Premium-Konto in den Speicherkonfigurationseinstellungen aus. Oder
- Erstellen Sie beim Erstellen eines neuen virtuellen DS- oder GS-Computers in den Speicherkonfigurationseinstellungen ein neues Storage Premium-Konto, oder lassen Sie über das Azure-Portal ein Storage Premium-Standardkonto erstellen.


Schrittweise Anweisungen finden Sie im Abschnitt [Schnellstart](#quick-start) weiter unten in diesem Artikel.

>[AZURE.NOTE] Ein Premium-Speicherkonto kann keinem benutzerdefinierten Domänennamen zugeordnet werden.

## Virtuelle Computer der DS- und GS-Serie

Storage Premium unterstützt virtuelle Azure-Computer der DS- und GS-Serie. Mit der DS- oder GS-Serie der virtuellen Computer können Sie Datenträger des Standard- und Premium-Speichers verwenden. Sie können jedoch keine Datenträger des Premium-Speichers mit virtuellen Computern verwenden, die nicht zur DS- oder GS-Serie gehören. Weitere Informationen zu den verfügbaren Typen und Größen von virtuellen Azure-Computern finden Sie unter [Größen für virtuelle Computer](../virtual-machines/virtual-machines-linux-sizes.md). Im Folgenden sind einige Funktionen der virtuellen Computer der DS- und GS-Serie aufgeführt.

**Clouddienst:** Virtuelle Computer der DS-Serie können einem Clouddienst hinzugefügt werden, der nur virtuelle Computer der DS-Serie umfasst. Fügen Sie virtuelle Computer der DS-Serie nicht einem vorhandenen Clouddienst mit virtuellen Computer hinzu, die nicht aus der DS-Serie stammen. Sie können vorhandene virtuelle Festplatten zu einem neuen Clouddienst migrieren, in dem nur virtuelle Computer der DS-Serie ausgeführt werden. Wenn Sie für den neuen Clouddienst dieselbe virtuelle IP-Adresse (VIP) beibehalten möchten, unter der die virtuellen Computer der DS-Serie gehostet werden, verwenden Sie [reservierte IP-Adressen](../virtual-network/virtual-networks-instance-level-public-ip.md). Virtuelle Computer der GS-Serie können einem vorhandenen Clouddienst hinzugefügt werden, in dem ausschließlich virtuelle Computer der G-Serie ausgeführt werden.

**Betriebssystemdatenträger:** Die virtuellen Azure-Computer der DS-Serie und GS-Serie können so konfiguriert werden, dass sie einen Betriebssystemdatenträger verwenden, der entweder in einem Standard-Speicherkonto oder einem Storage Premium-Konto gehostet wird. Wenn Sie den Betriebssystemdatenträger nur zum Starten verwenden, sollten Sie die Verwendung eines auf dem Standardspeicher basierenden Betriebssystemdatenträgers in Erwägung ziehen. Dadurch ergeben sich ähnliche Kostenvorteile und Leistungsergebnisse nach dem Hochfahren wie bei Storage Premium. Wenn Sie auf dem Betriebssystemdatenträger neben dem Starten weitere Aufgaben ausführen, verwenden Sie Premium-Speicher, da er bessere Leistungsergebnisse bietet. Wenn Ihre Anwendung vom Betriebssystemdatenträger liest und auf diesen Datenträger schreibt, bietet ein auf Premium-Speicher basierender Betriebssystemdatenträger eine bessere Leistung für Ihren virtuellen Computer.

**Datenträger:** Auf demselben virtuellen Computer der DS-Serie bzw. GS-Serie können Sie sowohl Storage Premium- als auch Standard-Speicherdatenträger verwenden. Mit Storage Premium können Sie einen virtuellen Computer der DS- oder GS-Serie bereitstellen und mehrere persistente Datenträger an einen virtuellen Computer anhängen. Bei Bedarf können Sie Daten über die Datenträger verteilen, um die Kapazität und die Leistung des Volumens zu erhöhen.

> [AZURE.NOTE] Wenn Sie Daten über Premium-Speicher-Datenträger mithilfe von [Speicherplätzen](http://technet.microsoft.com/library/hh831739.aspx) verteilen, sollten Sie sie für jeden verwendeten Datenträger eine Spalte konfigurieren. Andernfalls kann die Gesamtleistung des Stripesetvolume aufgrund ungleicher Verteilung des Datenverkehrs auf die Datenträger niedriger sein als erwartet. Standardmäßig können Sie auf der Server-Manager-Benutzeroberfläche Spalten für bis zu 8 Datenträger einrichten. Wenn Sie jedoch mehr als 8 Datenträger besitzen, müssen Sie PowerShell verwenden, um das Volumen zu erstellen. Außerdem müssen Sie die Anzahl der Spalten manuell angeben. Andernfalls verwendet die Server-Manager-Benutzeroberfläche weiterhin 8 Spalten, auch wenn Sie mehr Datenträger besitzen. Wenn Sie beispielsweise 32 Datenträger in einem einzelnen Stripeset besitzen, sollten Sie 32 Spalten angeben. Sie können mit dem *NumberOfColumns*-Parameter des PowerShell-Cmdlets [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) die Anzahl der vom virtuellen Datenträger verwendeten Spalten angeben. Weitere Informationen finden Sie unter [Übersicht über Speicherplätze](http://technet.microsoft.com/library/hh831739.aspx) und [Häufig gestellte Fragen zu Speicherplätzen](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).

**Cache:** Virtuelle Computer der DS- und GS-Serie verfügen über eine einzigartige Cachefunktion, mit der Sie eine hohe Durchsatzrate mit geringer Latenz erzielen können, die die zugrunde liegende Storage Premium-Datenträgerleistung übersteigt. Sie können die Datenträger-Cacherichtlinie auf den Storage Premium-Datenträgern als „ReadOnly“, „ReadWrite“ oder „None“ konfigurieren. Die standardmäßige Datenträger-Cacherichtlinie lautet „ReadOnly“ für alle Premium-Datenträger und „ReadWrite“ für Betriebssystemdatenträger. Verwenden Sie jeweils die richtige Konfigurationseinstellung, um die optimale Leistung für Ihre Anwendung zu erreichen. Legen Sie beispielsweise für Datenträger mit hohem Leseaufkommen oder schreibgeschützte Datenträger, z. B. SQL Server-Datendateien, die Datenträger-Cacherichtlinie auf „ReadOnly“ fest. Legen Sie die Datenträger-Cacherichtlinie bei Datenträgern mit hohem Schreibaufkommen oder mit der Eigenschaft „Nur Schreiben“ dagegen auf „None“ fest. Weitere Informationen zur Optimierung Ihres Entwurfs mit Storage Premium finden Sie unter [Hohe Anwendungsleistung mit Storage Premium](storage-premium-storage-performance.md).

**Analyse:** Um die Leistung virtueller Computer anhand von Datenträgern in Storage Premium-Konten zu analysieren, können Sie im Azure-Portal die Diagnose für virtuelle Azure-Computer aktivieren. Einzelheiten erfahren Sie unter [Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) (in englischer Sprache). Die Datenträgerleistung können Sie mithilfe von auf dem Betriebssystem basierenden Tools analysieren, z. B. [Windows-Leistungsüberwachung](https://technet.microsoft.com/library/cc749249.aspx) für virtuelle Windows-Computer und [IOSTAT](http://linux.die.net/man/1/iostat) für virtuelle Linux-Computer.

**Skalierungslimits und Leistung virtueller Computer:** Jede Größe für virtuelle Computer der DS- und GS-Serie verfügt über Skalierungslimits und Leistungsspezifikationen für IOPS, Bandbreite und die Anzahl der Datenträger, die pro virtuellem Computer angefügt werden können. Stellen Sie bei Verwendung von Storage Premium-Datenträgern mit virtuellen Computern der DS- oder GS-Serie sicher, dass auf dem virtuellen Computer ausreichend IOPS und Bandbreite für den Datenverkehr des Datenträgers verfügbar sind. Bei einem virtuellen Computer vom Typ „STANDARD\_DS1“ steht für Premium-Speicherdatenträgerverkehr beispielsweise eine dedizierte Bandbreite von 32 MB pro Sekunde zur Verfügung. Ein Storage Premium-Datenträger des Typs „P10“ kann eine Bandbreite von 100 MB pro Sekunde bereitstellen. Mit einem an diesen virtuellen Computer angefügten Storage Premium-Datenträger des Typs „P10“ sind maximal 32 MB pro Sekunde möglich (nicht bis zu 100 MB pro Sekunde, wie sie der P10-Datenträger bereitstellen kann).

Derzeit ist der größte virtuelle Computer der DS-Serie der STANDARD\_DS14. Er kann bis zu 512 MB pro Sekunde auf allen Datenträgern bereitstellen. Der größte virtuelle Computer der GS-Serie ist der STANDARD\_GS5. Er kann bis zu 2.000 MB pro Sekunde auf allen Datenträgern bereitstellen. Beachten Sie, dass diese Limits nur für den Datenträgerverkehr und nicht für Cachetreffer- und Netzwerkverkehr gelten. Für den Netzwerkdatenverkehr virtueller Computer steht eine separate Bandbreite zur Verfügung. Diese unterscheidet sich von der dedizierten Bandbreite für Premium-Speicherdatenträger.

Aktuelle Informationen zu maximalen IOPS- und Durchsatzwerten (d.h. Bandbreitenwerte) für virtuelle Computer der DS- und GS-Serie finden Sie unter [Größen für virtuelle Computer](../virtual-machines/virtual-machines-linux-sizes.md). Informationen zu Datenträgern des Premium-Speichers und ihren IOPs und Durchsatzlimits finden Sie in diesem Artikel in der Tabelle im Abschnitt [Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers](#scalability-and-performance-targets-whde-DEing-premium-storage).

## Skalierbarkeits- und Leistungsziele für Storage Premium

In diesem Abschnitt werden alle Skalierbarkeits- und Leistungsziele beschrieben, die Sie bei der Verwendung von Storage Premium berücksichtigen müssen.

### Storage Premium-Kontobegrenzungen

Storage Premium-Konten weisen folgende Skalierbarkeitsziele auf:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Gesamtkapazität des Kontos</strong></td>
	<td><strong>Gesamtbandbreite für ein lokal redundantes Speicherkonto</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Kapazität des Datenträgers: 35 TB</li>
       <li type=round>Kapazität für Momentaufnahmen: 10 TB</li>
    </ul>
	</td>
	<td>Bis zu 50&#160;GB pro Sekunde für eingehenden und ausgehenden Datenverkehr</td>
</tr>
</tbody>
</table>

- "Eingehend" bezieht sich auf alle Daten (Anforderungen), die an ein Speicherkonto gesendet werden.
- "Ausgehend" bezieht sich auf alle Daten (Antworten), die von einem Speicherkonto empfangen werden.

Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungszielen für Azure Storage](storage-scalability-targets.md).

Wenn die Anforderungen Ihrer Anwendung die Skalierbarkeitsziele eines einzelnen Speicherkontos überschreiten, erstellen Sie die Anwendung so, dass mehrere Speicherkonten verwendet werden, und partitionieren Sie Ihre Daten in diesen Speicherkonten. Wenn Sie z. B. Datenträger mit einer Kapazität von 51 Terabyte (TB) an mehrere virtuelle Computer anfügen möchten, verteilen Sie diese auf zwei Speicherkonten, da 35 TB der Grenzwert für ein einzelnes Premium-Speicherkonto ist. Stellen Sie sicher, dass ein Premium-Speicherkonto stets Datenträger mit einer Größe von maximal 35 TB enthält.

### Grenzwerte für Storage Premium-Datenträger

Wenn Sie einen Datenträger für ein Premium-Speicherkonto bereitstellen, hängen die Anzahl der E/A-Vorgänge pro Sekunde (IOPS) und der Durchsatz (Bandbreite) von der Größe des Datenträgers ab. Zurzeit sind drei Datenträgertypen des Premium-Speichers verfügbar: P10, P20 und P30. Jeder Typ weist bestimmte Grenzwerte für IOPS und den Durchsatz auf, wie in der folgenden Tabelle angegeben:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Datenträgertyp des Premium-Speichers</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>Datenträgergröße</strong></td>
	<td>128 GB</td>
	<td>512 GB</td>
	<td>1.024 GiB (1 TB)</td>
</tr>
<tr>
	<td><strong>IOPS pro Datenträger</strong></td>
	<td>500</td>
	<td>2.300</td>
	<td>5.000</td>
</tr>
<tr>
	<td><strong>Durchsatz pro Datenträger</strong></td>
	<td>100 MB pro Sekunde </td>
	<td>150 MB pro Sekunde </td>
	<td>200 MB pro Sekunde </td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Stellen Sie sicher, dass auf Ihrem virtuellen Computer ausreichend Bandbreite für den Datenverkehr des Datenträgers verfügbar ist. Informationen hierzu finden Sie weiter oben in diesem Artikel im Abschnitt [Virtuelle Computer der DS- und GS-Serie](#ds-and-gs-series-vms). Andernfalls werden der Datenträgerdurchsatz und IOPS auf niedrigere Werte beschränkt. Die Grundlage für diese Beschränkung bilden die Limits der virtuellen Computer und nicht die in der vorherigen Tabelle aufgeführten Datenträgerlimits.

Es folgen einige wichtige Punkte, die Sie in Bezug auf die Skalierbarkeits- und Leistungsziele für Storage Premium wissen müssen:

- **Bereitgestellte Kapazität und Leistung:** Im Gegensatz zu einem Standard-Speicherdatenträger sind bei der Bereitstellung eines Storage Premium-Datenträgers die Kapazität, die IOPS und der Durchsatz für diesen Datenträger garantiert. Wenn Sie beispielsweise einen P30-Datenträger erstellen, werden in Azure eine Speicherkapazität von 1.024 GB, 5.000 IOPS und ein Durchsatz von 200 MB pro Sekunde für diesen Datenträger bereitgestellt. Die Anwendung kann die Kapazität und Leistung ganz oder teilweise nutzen.

- **Datenträgergröße:** Azure ordnet die (aufgerundete) Datenträgergröße der nächsten Option für Storage Premium-Datenträger wie in der Tabelle angegeben zu. Ein Datenträger mit der Größe 100 GB wird z. B. als Option „P10“ klassifiziert. Er kann bis zu 500 E/A-Einheiten pro Sekunde mit einem Durchsatz von bis zu 100 MB pro Sekunde ausführen. Analog wird ein Datenträger mit der Größe 400 GB als Option „P20“ klassifiziert. Er kann bis zu 2300 E/A-Einheiten pro Sekunde mit einem Durchsatz von bis zu 150 MB pro Sekunde ausführen.

	> [AZURE.NOTE] Sie können die Größe der vorhandenen Datenträger problemlos erhöhen. Sie können beispielsweise die Größe eines 30-GB-Datenträgers auf 128 GB oder 1 TB erhöhen. Sie haben auch die Möglichkeit, einen P20-Datenträger in einen P30-Datenträger umzuwandeln, wenn Sie mehr Kapazität oder mehr IOPS und einen höheren Durchsatz benötigen. Sie können den Datenträger mithilfe des PowerShell-Cmdlets Update-AzureDisk mit der Eigenschaft -ResizedSizeInGB erweitern. Für diese Aktion muss der Datenträger vom virtuellen Computer getrennt oder der virtuelle Computer angehalten werden.

- **E/A-Größe:** Die Größe der Eingabe-/Ausgabeeinheit (E/A) liegt bei 256 KB. Wenn die Daten, die übertragenen werden, kleiner als 256 KB sind, werden sie als einzelne E/A-Einheit betrachtet. Die größeren E/A-Größen werden als mehrere Ein- bzw. Ausgaben der Größe 256 KB gezählt. Eine EA von 1.100 KB wird beispielsweise als fünf E/A-Einheiten gezählt.

- **Durchsatz:** Zu den Durchsatzlimits zählen Schreib- und Lesevorgänge auf dem Datenträger, die nicht vom Cache verarbeitet werden. Bei P10-Datenträgern liegt der Durchsatz beispielsweise bei 100 MB pro Sekunde pro Datenträger. Einige Beispiele für den gültigen Durchsatz bei P10-Datenträgern:
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Max. Durchsatz pro P10-Datenträger</strong></td>
	<td><strong>Nicht vom Cache verarbeitete Lesevorgänge vom Datenträger</strong></td>
	<td><strong>Nicht vom Cache verarbeitete Schreibvorgänge vom Datenträger</strong></td>
</tr>
<tr>
	<td>100 MB pro Sekunde</td>
	<td>100 MB pro Sekunde</td>
	<td>0</td>
</tr>
<tr>
	<td>100 MB pro Sekunde</td>
	<td>0</td>
	<td>100 MB pro Sekunde</td>
</tr>
<tr>
	<td>100 MB pro Sekunde </td>
	<td>60 MB pro Sekunde </td>
	<td>40&#160;MB pro Sekunde </td>
</tr>
</tbody>
</table>

- **Cachetreffer:** Cachetreffer werden durch die zugeordneten IOPS-/Durchsatzwerte des Datenträgers nicht eingeschränkt. Beispielsweise unterliegen Lesevorgänge, die vom Cache verarbeitet werden, bei Verwendung eines Datenträgers mit der Cacheeinstellung ReadOnly für einen virtuellen Computer der DS- oder GS-Serie nicht den Einschränkungen für Storage Premium-Datenträger. Daher können Sie einen sehr hohen Durchsatz mit einem Datenträger erzielen, wenn der Workload vorwiegend aus Lesevorgängen besteht. Beachten Sie, dass für den Cache separate IOPS-/Durchsatzlimits auf Ebene des virtuellen Computers basierend auf der Größe des virtuellen Computers gelten. Virtuelle Computer der DS-Serie bieten etwa 4.000 IOPS und 33 MB pro Sekunde und Kern für E/A von Caches und lokalen SSDs. Virtuelle Computer der GS-Serie weisen eine Beschränkung von 5.000 IOPS und 50 MB pro Sekunde und Kern für E/A von Caches und lokalen SSDs auf.

## Drosselung
Möglicherweise bemerken Sie eine Drosselung, wenn bei der IOPS oder beim Durchsatz der Anwendung die zugewiesenen Grenzwerte für einen Storage Premium-Datenträger überschritten werden oder der gesamte Datenträgerverkehr auf allen Datenträgern des virtuellen Computers das Bandbreitenlimit des Datenträgers für den virtuellen Computer überschreitet. Um die Drosselung zu verhindern, sollten Sie die Anzahl der ausstehenden E/A-Anforderungen für Datenträger einschränken, und zwar basierend auf den Skalierbarkeits- und Leistungszielen für den bereitgestellten Datenträger und auf der für den virtuellen Computer verfügbaren Datenträgerbandbreite.

Ihre Anwendung kann die geringste Latenz erzielen, wenn sie so entworfen wird, dass Einschränkungen vermieden werden. Wenn die Anzahl der ausstehenden E/A-Anforderungen für den Datenträger zu klein ist, kann Ihre Anwendung andererseits die maximalen IOPS- und Durchsatzebenen nicht nutzen, die für den Datenträger verfügbar sind.

Die folgenden Beispiele veranschaulichen, wie die Einschränkungsebenen berechnet werden. Alle Berechnungen basieren auf der E/A-Einheitengröße von 256 KB.

### Beispiel 1:
Ihre Anwendung hat 495 E/A-Einheiten mit einer Größe von 16 KB in einer Sekunde auf einem Datenträger des Typs P10 ausgeführt. Diese werden als 495 E/A-Einheiten pro Sekunde (IOPS) berechnet. Wenn Sie eine E/A von 2 MB in der gleichen Sekunde versuchen, entspricht die Summe der E/A-Einheiten 495 + 8. Dies liegt daran, dass 2 MB E/A zu 2048 KB / 256 KB = 8 E/A-Einheiten führt, wenn die E/A-Einheitengröße 256 KB beträgt. Da die Summe von 495 + 8 die IOPS-Grenze von 500 für den Datenträger überschreitet, tritt eine Einschränkung auf.

### Beispiel 2:
Ihre Anwendung hat 400 E/A-Einheiten mit einer Größe von 256 KB auf einem Datenträger des Typs P10 ausgeführt. Verwendete Gesamtbandbreite: (400 * 256) / 1024 = 100 MB/s. Das Durchsatzlimit eines P10-Datenträgers liegt bei 100 MB pro Sekunde. Wenn Ihre Anwendung versucht, weitere E/A-Vorgänge in dieser Sekunde auszuführen, wird sie eingeschränkt, weil der zugewiesene Grenzwert überschritten wird.

### Beispiel 3:
Sie besitzen einen virtuellen DS4-Computer mit zwei angefügten P30-Datenträgern. Bei jedem P30-Datenträger ist ein Durchsatz von 200 MB Datendurchsatz pro Sekunde möglich. Ein virtueller DS4-Computer verfügt jedoch über eine Datenträgerbandbreitenkapazität von insgesamt 256 MB pro Sekunde. Daher können Sie auf diesem virtuellen DS4-Computer für die angeschlossenen Datenträger nicht gleichzeitig den maximalen Durchsatz erzielen. Um dieses Problem zu beheben, können Sie auf einem Datenträger Datenverkehr von 200 MB pro Sekunde und auf dem anderen Datenträger 56 MB pro Sekunde tolerieren. Übersteigt die Summe des Datenträgerverkehrs 256 MB pro Sekunde, wird der Datenträgerverkehr gedrosselt.

>[AZURE.NOTE] Wenn der Datenverkehr des Datenträgers vor allem aus kleinen E/A-Größen besteht, ist es sehr wahrscheinlich, dass Ihre Anwendung den IOPS-Grenzwert vor dem Durchsatzgrenzwert erreicht. Wenn der Datenverkehr des Datenträgers vor allem aus großen E/A-Größen besteht, ist es andererseits sehr wahrscheinlich, dass Ihre Anwendung den Durchsatzgrenzwert und nicht den IOPS-Grenzwert erreicht. Sie können die IOPS und die Durchsatzkapazität Ihrer Anwendung mithilfe optimaler E/A-Größen und auch durch das Einschränken der Anzahl der ausstehenden E/A-Anforderungen für den Datenträger maximieren.

Informationen zum Entwerfen einer hohen Anwendungsleistung mit Storage Premium finden Sie im Artikel [Hohe Anwendungsleistung mit Storage Premium](storage-premium-storage-performance.md).

## Momentaufnahmen und Kopieren von Blobs
Sie können eine Momentaufnahme für den Premium-Speicher auf die gleiche Weise erstellen, wie Sie eine Momentaufnahme bei der Verwendung von Standardspeicher erstellen. Da der Premium-Speicher lokal redundant ist, wird empfohlen, Momentaufnahmen zu erstellen und diese dann in ein georedundantes Standardspeicherkonto zu kopieren. Weitere Informationen finden Sie unter [Redundanzoptionen für Azure Storage](storage-redundancy.md).

Wenn ein Datenträger an einen virtuellen Computer angefügt ist, sind bestimmte API-Vorgänge für den Seiten-BLOB unzulässig, der den Datenträger sichert. Sie können beispielsweise den Vorgang [Kopieren von Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) für das Blob nicht durchführen, solange der Datenträger an den virtuellen Computer angeschlossen ist. Erstellen Sie stattdessen zunächst eine Momentaufnahme dieses Blobs, indem Sie die REST-API-Methode [Momentaufnahme-Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) verwenden und anschließend den Vorgang [Kopieren von Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) der Momentaufnahme durchführen, um den angeschlossenen Datenträger zu kopieren. Alternativ können Sie den Datenträger auch trennen und dann alle erforderlichen Vorgänge für das zugrunde liegende BLOB ausführen.

Für Momentaufnahmen von Storage Premium-Blobs gelten folgende Grenzwerte:
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Storage Premium-Grenzwert</strong></td>
	<td><strong>Wert</strong></td>
</tr>
<tr>
	<td>Max. Anzahl der Momentaufnahmen pro Blob</td>
	<td>100</td>
</tr>
<tr>
	<td>Kontospeicherkapazität für Momentaufnahmen (betrifft nur Daten in Momentaufnahmen und nicht die Daten im Basisblob)</td>
	<td>10&#160;TB</td>
</tr>
<tr>
	<td>Mindestzeit zwischen aufeinanderfolgenden Momentaufnahmen</td>
	<td>10 Minuten</td>
</tr>
</tbody>
</table>

Um georedundante Kopien Ihrer Momentaufnahmen aufzubewahren, können Sie Momentaufnahmen aus einem Premium-Speicherkonto mithilfe von "AzCopy" oder "Copy Blob" in ein georedundantes Standardspeicherkonto kopieren. Weitere Informationen finden Sie unter [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md) und [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).

Detaillierte Informationen zum Durchführen von REST-Vorgängen für Seitenblobs in Premium-Speicherkonten finden Sie in der MSDN-Bibliothek unter [Verwenden von Blob-Dienstvorgängen mit Azure Premium-Speicher](http://go.microsoft.com/fwlink/?LinkId=521969).

## Verwenden von virtuellen Linux-Computern mit Premium-Speicher
Nachfolgend finden Sie wichtige Anweisungen zum Konfigurieren virtueller Linux-Computer für Premium-Speicher:

- Für alle Premium-Speicherdatenträger mit der Cacheeinstellung „ReadOnly“ oder „None“ müssen Sie sogenannte Sperren bei der Bereitstellung des Dateisystems deaktivieren, um die Skalierbarkeitsziele für Premium-Speicher zu erreichen. Sie benötigen keine Sperren für dieses Szenario, da die Schreibvorgänge auf in Premium-Speicher gesicherten Datenträgern für diese Cacheeinstellungen beständig sind. Wenn die Schreibanforderung erfolgreich abgeschlossen wurde, wurden Daten in den permanenten Speicher geschrieben. Verwenden Sie abhängig vom Dateisystem die folgenden Methoden zum Deaktivieren von Sperren:
	- Wenn Sie **reiserFS** verwenden, deaktivieren Sie Sperren mithilfe der Bereitstellungsoption „barrier=none“ (verwenden Sie zum Aktivieren von Sperren „barrier=flush“).
	- Wenn Sie **ext3/ext4** verwenden, deaktivieren Sie Sperren mithilfe der Bereitstellungsoption „barrier=0“ (verwenden Sie zum Aktivieren von Sperren „barrier=1“).
	- Wenn Sie **XFS** verwenden, deaktivieren Sie Sperren mithilfe der Bereitstellungsoption „nobarrier“ (verwenden Sie zum Aktivieren von Sperren „barrier“).

- Bei Premium-Speicherdatenträgern mit der Cacheeinstellung „ReadWrite“ müssen Sperren aktiviert werden, um die Beständigkeit von Schreibvorgängen zu gewährleisten.
- Damit die Volumebezeichnungen nach dem Neustart des virtuellen Computers beibehalten werden, müssen Sie „/etc/fstab“ mit den UUID-Verweisen auf die Datenträger aktualisieren. Weitere Informationen finden Sie unter [Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Linux-Computer](../virtual-machines/virtual-machines-linux-classic-attach-disk.md).

Nachfolgend sind die Linux-Distributionen aufgeführt, die für Premium-Speicher überprüft wurden. Es wird empfohlen, dass Sie Ihre virtuellen Computer auf mindestens eine dieser Versionen (oder eine höhere Version) aktualisieren, um eine bessere Leistung und Stabilität mit Premium-Speicher zu erzielen. Außerdem erfordern einige Versionen die neuesten LIS (Linux-Integrationsdienste v4.0 für Microsoft Azure). Der Download und die Installation sind über folgenden Link möglich: Wir fügen der Liste mehr Images hinzu, wenn weitere Überprüfungen ausgeführt wurden. Beachten Sie, dass unsere Überprüfungen ergaben, dass die Leistung für diese Images variiert. Sie hängt auch von den Workloadmerkmalen und -einstellungen der Images ab. Verschiedene Images werden für verschiedene Arten von Workload optimiert.
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Verteilung</strong></td>
	<td><strong>Version</strong></td>
	<td><strong>Unterstützter Kernel</strong></td>
	<td><strong>Unterstütztes Image</strong></td>
</tr>
<tr>
	<td rowspan="4"><strong>Ubuntu</strong></td>
	<td>12.04</td>
	<td>3.2.0-75.110</td>
	<td>Ubuntu-12_04_5-LTS-amd64-server-20150119-de-DE-30GB</td>
</tr>
<tr>
	<td>14.04</td>
	<td>3.13.0-44.73</td>
	<td>Ubuntu-14_04_1-LTS-amd64-server-20150123-de-DE-30GB</td>
</tr>
<tr>
	<td>14.10</td>
	<td>3.16.0-29.39</td>
	<td>Ubuntu-14_10-amd64-server-20150202-de-DE-30GB</td>
</tr>
<tr>
	<td>15.04</td>
	<td>3.19.0-15</td>
	<td>Ubuntu-15_04-amd64-server-20150422-de-DE-30GB</td>
</tr>
<tr>
	<td><strong>SUSE</strong></td>
	<td>SLES 12</td>
	<td>3.12.36-38.1</td>
	<td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td>
</tr>
<tr>
	<td><strong>CoreOS</strong></td>
	<td>584.0.0</td>
	<td>3.18.4</td>
	<td>CoreOS 584.0.0</td>
</tr>
<tr>
	<td rowspan="2"><strong>CentOS</strong></td>
	<td>6.5, 6.6, 6.7, 7.0</td>
	<td></td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 erforderlich </a> </br>
		* Siehe Hinweis unten
	</td>
</tr>
<tr>
	<td>7.1</td>
	<td>3.10.0-229.1.2.el7</td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 empfohlen </a> <br/>
		* Siehe Hinweis unten
	</td>
</tr>

<tr>
	<td rowspan="2"><strong>Oracle</strong></td>
	<td>6.4.</td>
	<td></td>
	<td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 erforderlich </a></td>
</tr>
<tr>
	<td>7.0</td>
	<td></td>
	<td>Ausführliche Informationen erhalten Sie vom Support.</td>
</tr>
</tbody>
</table>


### LIS-Treiber für OpenLogic CentOS

Kunden mit virtuellen OpenLogic CentOS-Computern sollten den folgenden Befehl zum Installieren der neuesten Treiber ausführen:

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

Anschließend ist ein Neustart erforderlich, um die neuen Treiber zu aktivieren.

## Preise und Abrechnung
Bei der Verwendung des Premium-Speichers gelten die folgenden Abrechnungserwägungen:
- Größe von Storage Premium-Datenträgern
- Storage Premium-Momentaufnahmen
- Ausgehende Datenübertragungen

**Größe von Storage Premium-Datenträgern:** Die Abrechnung für einen Storage Premium-Datenträger hängt von der bereitgestellten Größe des Datenträgers ab. Azure ordnet die (aufgerundete) Datenträgergröße der nächsten Option für den Premium-Speicherdatenträger zu (siehe Tabelle im Abschnitt [Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers](#scalability-and-performance-targets-whde-DEing-premium-storage)). Die Abrechnung für bereitgestellte Datenträger erfolgt anteilig auf Stundenbasis unter Verwendung des monatlichen Preises für das Premium-Speicherangebot. Wenn Sie z. B. einen Datenträger des Typs P10 bereitgestellt und diesen nach 20 Stunden gelöscht haben, erfolgt die Abrechnung für das P10-Angebot anteilig für 20 Stunden. Dies ist unabhängig von der tatsächlichen Menge von Daten, die auf den Datenträger geschrieben wurden, oder dem verwendeten IOPS/Durchsatz.

**Storage Premium-Momentaufnahmen:** Für Momentaufnahmen für Storage Premium wird die zusätzliche Kapazität in Rechnung gestellt, die von den Momentaufnahmen verwendet wird. Informationen zu Momentaufnahmen finden Sie unter [Erstellen einer Momentaufnahme eines Blobs](http://msdn.microsoft.com/library/azure/hh488361.aspx).

**Ausgehende Datenübertragungen:** [Ausgehende Datenübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) (Daten, die von den Azure-Rechenzentren ausgehen) verursachen Kosten bei der Bandbreitenverwendung.

Ausführliche Informationen zu den Preisen für den Premium-Speicher und für virtuelle Computern der DS- und der GS-Serie finden Sie unter:

- [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
- [Preisgestaltung für virtuelle Computer](https://azure.microsoft.com/pricing/details/virtual-machines/)

## Schnellstart

## Erstellen und Verwenden eines Premium-Speicherkontos für den Datenträger eines virtuellen Computers

In diesem Abschnitt werden die folgenden Szenarios unter Verwendung des Azure-Portals, von Azure PowerShell und der Azure-Befehlszeilenschnittstelle veranschaulicht:
- Gewusst wie: Erstellen eines Storage Premium-Kontos.
- Gewusst wie: Erstellen eines virtuellen Computers und Anfügen eines Datenträgers an den virtuellen Computer bei Verwendung von Storage Premium.
- Gewusst wie: Ändern der Datenträger-Cacherichtlinie eines an einen virtuellen Computer angefügten Datenträgers.

### Erstellen eines virtuellen Azure-Computers mit Storage Premium-Speicher über das Azure-Portal

#### I. Erstellen eines Storage Premium-Kontos im Azure-Portal

In diesem Abschnitt wird gezeigt, wie ein Storage Premium-Konto mit dem Azure-Portal erstellt wird.

1.	Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Testen Sie das [kostenlose Testkonto](https://azure.microsoft.com/pricing/free-trial/), wenn Sie noch kein Abonnement haben.

2.	Klicken Sie im Hub-Menü auf **Neu**.

3.	Wählen Sie unter **Neu** die Option **Daten und Speicher** aus. Klicken Sie dann auf **Speicherkonto**. Wählen Sie das Bereitstellungsmodell aus. Verwenden Sie für neue Bereitstellungen die Resource Manager-Bereitstellung. Klicken Sie auf **Erstellen**.

4.	Geben Sie im Bereich "Speicherkonto" einen Namen für Ihr Speicherkonto ein. Wählen Sie den gewünschten Standort für die Bereitstellung aus. Überprüfen Sie unter [Azure-Dienste nach Region](https://azure.microsoft.com/regions/#services), ob Storage Premium am ausgewählten Standort verfügbar ist.

5.	Klicken Sie auf **Typ**. Wählen Sie auf dem Blatt **Speicherkontentyp auswählen** die Option **Premium lokal redundant** aus. Klicken Sie auf **Auswählen**. Nach dem Klicken auf **Auswählen** wird unter **Typ** die Option **Premium-LRS** angezeigt.

6.	Erstellen Sie auf dem Blatt **Speicherkonto** eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene Ressourcengruppe aus, wenn bereits eine vorhanden ist. Klicken Sie auf Erstellen.

	![Tarif][Image1]

#### II. Erstellen eines virtuellen Azure-Computers über das Azure-Portal

Sie müssen einen virtuellen Computer der DS- oder GS-Serie erstellen, um Storage Premium verwenden zu können. Führen Sie die unter [Erstellen eines virtuellen Windows-Computers im Azure-Portal](../virtual-machines/virtual-machines-windows-tutorial.md) beschriebenen Schritte aus, um einen neuen virtuellen Computer der DS- oder GS-Serie zu erstellen.

#### III. Anfügen eines Storage Premium-Datenträgers über das Azure-Portal

1. Suchen Sie den neu erstellten oder einen bereits vorhandenen virtuellen Computer der DS- oder GS-Serie im Azure-Portal.
2. Navigieren Sie unter **Alle Einstellungen** für den virtuellen Computer zu **Datenträger**, und klicken Sie auf **Neuen anfügen**.
3. Geben Sie den Namen des Datenträgers ein, und wählen Sie unter **Typ** die Option **Premium** aus. Wählen Sie die gewünschte Einstellung für **Größe** und **Hostzwischenspeicherung** aus.

	![Premium-Datenträger][Image2]

Weitere ausführlichere Schritte finden Sie unter [Gewusst wie: Anfügen eines Datenträgers im Azure-Portal](../virtual-machines/virtual-machines-windows-attach-disk-portal.md).

#### IV. Ändern der Datenträger-Cacherichtlinie über das Azure-Portal

1. Suchen Sie den neu erstellten oder einen bereits vorhandenen virtuellen Computer der DS- oder GS-Serie im Azure-Portal.
2. Navigieren Sie unter „Alle Einstellungen“ für den virtuellen Computer zu „Datenträger“, und klicken Sie auf den zu ändernden Datenträger.
3. Legen Sie für die Option „Hostzwischenspeicherung“ den gewünschten Wert fest: „None“, „ReadOnly“ oder „ReadWrite“.

### Erstellen eines virtuellen Azure-Computers mit Premium-Speicher über Azure PowerShell

#### I. Erstellen eines Storage Premium-Kontos in Azure PowerShell

Dieses PowerShell-Beispiel zeigt, wie Sie ein neues Premium-Speicherkonto erstellen und einen Datenträger, der dieses Konto verwendet, an einen neuen virtuellen Azure-Computer anschließen.

1. Richten Sie die PowerShell-Umgebung mithilfe der unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md) beschriebenen Schritte ein.
2. Starten Sie die PowerShell-Konsole, stellen Sie eine Verbindung mit Ihrem Abonnement her, und führen Sie dann das folgende PowerShell-Cmdlet im Konsolenfenster aus. Wie diese PowerShell-Anweisung zeigt, müssen Sie den Parameter **Type** als **Premium\_LRS** angeben, wenn Sie ein Premium-Speicherkonto erstellen.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

#### II. Erstellen eines virtuellen Azure-Computers über Azure PowerShell

Erstellen Sie im nächsten Schritt einen neuen virtuellen Computer der DS-Serie, und geben Sie an, dass Sie Premium-Speicher wünschen, indem Sie die folgenden PowerShell-Cmdlets im Konsolenfenster ausführen: Mithilfe der gleichen Schritte können Sie auch einen virtuellen Computer der GS-Serie erstellen. Geben Sie die entsprechende Größe des virtuellen Computers in den Befehlen an. Beispiel: Standard\_GS2:

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

#### III. Anfügen eines Storage Premium-Datenträgers über Azure PowerShell

Wenn Sie mehr Speicherplatz für Ihren virtuellen Computer benötigen, fügen Sie einen neuen Datenträger an einen vorhandenen virtuellen Computer der DS- oder GS-Serie an, nachdem dieser erstellt wurde, indem Sie die folgenden PowerShell-Cmdlets im Konsolenfenster ausführen:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

#### IV. Ändern der Datenträger-Cacherichtlinie über Azure PowerShell

Um die Datenträger-Cacherichtlinie zu aktualisieren, notieren Sie sich die LUN-Nummer des angefügten Datenträgers. Führen Sie den folgenden Befehl aus, um den unter LUN-Nummer 2 angefügten Datenträger in „ReadOnly“ zu ändern.

		Get-AzureVM "myservice" -name "MyVM" | Set-AzureDataDisk -LUN 2 -HostCaching ReadOnly | Update-AzureVM

### Erstellen eines virtuellen Azure-Computers mit Premium-Speicher über die Azure-Befehlszeilenschnittstelle

Die [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) (Azure CLI) stellt eine Reihe von plattformübergreifenden Open Source-Befehlen für die Arbeit mit der Azure-Plattform bereit. Die folgenden Beispiele zeigen, wie Sie mit der Azure-Befehlszeilenschnittstelle (Version 0.8.14 und höher) ein Premium-Speicherkonto und einen neuen virtuellen Computer erstellen und einen neuen Datenträger aus einem Premium-Speicherkonto anfügen.

#### I. Erstellen eines Storage Premium-Kontos über die Azure-Befehlszeilenschnittstelle

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### II. Erstellen eines virtuellen Computers der DS-Serie über die Azure-Befehlszeilenschnittstelle

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-de-DE-30GB" -u "myusername" -p "passwd@123"

Anzeigen von Informationen zum virtuellen Computer

	azure vm show premium-test-vm

#### III. Anfügen eines neuen Storage Premium-Datenträgers über die Azure-Befehlszeilenschnittstelle

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

Anzeigen von Informationen zum neuen Datenträger

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

#### IV. Ändern der Datenträger-Cacherichtlinie

Um die Cacherichtlinie auf einem Ihrer Datenträger mit der Azure-Befehlszeilenschnittstelle zu ändern, führen Sie den folgenden Befehl aus:

		$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>

Beachten Sie, dass die Cacherichtlinienoptionen „ReadOnly“, „None“ oder „ReadWrite“ lauten können. Weitere Optionen finden Sie in der Hilfe. Diese wird mithilfe des folgenden Befehls aufgerufen:

		azure vm disk attach --help

## Häufig gestellte Fragen

1. **Kann ich Storage Premium- sowie Standard-Datenträger an einen virtuellen Computer der DS- und GS-Serie anfügen?**

	Ja. Sie können Storage Premium- und Standard-Datenträger an einen virtuellen Computer der DS- und GS-Serie anfügen.

2. **Kann ich Storage Premium- sowie Standard-Datenträger an einen virtuellen Computer der D- und G-Serie anfügen?**

	Nein. An virtuelle Computer, die nicht aus der DS- oder GS-Serie stammen, können Sie nur Standard-Datenträger anfügen.

3. **Welche Kosten fallen für mich an, wenn ich einen Storage Premium-Datenträger von einer vorhandenen virtuellen Festplatte mit einer Größe von 80 GB erstelle?**

	Ein Storage Premium-Datenträger, der von einer virtuellen Festplatte mit einer Größe von 80 GB erstellt wird, wird nach der nächsten verfügbaren Größe für Storage Premium-Datenträger berechnet, in diesem Fall als P10-Datenträger. Für Sie fallen die Preise für einen P10-Datenträger an.

4. **Fallen bei Verwendung von Storage Premium Transaktionskosten an?**

	Es gibt Festkosten für jede Datenträgergröße, die mit bestimmten IOPS- und Durchsatzwerten bereitgestellt wird. Gegebenenfalls fallen nur für die ausgehende Bandbreite und die Momentaufnahmenkapazität weitere Kosten an. Weitere Informationen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

5. **Wo kann ich die Startdiagnose für meinen virtuellen Computer der DS- oder GS-Serie speichern?**

	Erstellen Sie ein Standardspeicherkonto, um die Startdiagnose Ihres virtuellen Computers der DS- oder GS-Serie zu speichern.

6. **Welche IOPS- und Durchsatzwerte erhalte ich über den Datenträgercache?**

	Die kombinierte Beschränkung für den Cache und die lokale SSD für virtuelle Computer der DS-Serie liegt bei 4.000 IOPS pro Kern und 33 MB pro Sekunde und Kern. Virtuelle Computer der GS-Serie bieten 5.000 IOPS pro Kern und 50 MB pro Sekunde und Kern.

7. **Was ist die lokale SSD in einem virtuellen Computer der DS- oder GS-Serie?**

	Bei der lokalen SSD handelt es sich um einen temporären Speicher, der in einem virtuellen Computer der DS- oder GS-Serie enthalten ist. Für diesen temporären Speicher fallen keine zusätzlichen Kosten an. Es wird davon abgeraten, dass Sie diesen temporären Speicher (die lokale SSD) zum Speichern Ihrer Anwendungsdaten verwenden, da die Daten in Azure Blob Storage nicht permanent gespeichert werden.

8. **Kann ich mein Standard-Speicherkonto in ein Storage Premium-Konto umwandeln?**

	Nein. Es ist nicht möglich, ein Standard-Speicherkonto in ein Storage Premium-Konto umzuwandeln oder umgekehrt. Sie müssen ein neues Speicherkonto mit dem gewünschten Typ erstellen und gegebenenfalls Daten in das neue Speicherkonto kopieren.

9. **Wie kann ich einen virtuellen Computer der D-Serie in einen virtuellen Computer der DS-Serie ändern?**

	Im Migrationsleitfaden [Migrieren zu Azure Storage Premium](storage-migration-to-premium-storage.md) finden Sie Anweisungen zum Verschieben Ihrer Workload von einem virtuellen Computer der D-Serie mit einem Standard-Speicherkonto auf einen virtuellen Computer der DS-Serie mit einem Storage Premium-Konto.

## Nächste Schritte

Weitere Informationen zu Azure Storage Premium finden Sie in den folgenden Artikeln.

### Entwurf und Implementierung mit Azure Storage Premium

- [Hohe Anwendungsleistung mit Storage Premium](storage-premium-storage-performance.md)
- [Verwenden von Blob-Dienstvorgängen mit dem Azure Premium-Speicher](http://go.microsoft.com/fwlink/?LinkId=521969)

### Leitfaden

- [Migrieren zu Azure Premium-Speicher](storage-migration-to-premium-storage.md)


### Blogbeiträge

- [Azure Premium Storage Generally Available](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) (Azure Storage Premium allgemein verfügbar)
- [Announcing the GS-Series: Adding Premium Storage Support to the Largest VMs in the Public Cloud](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/) (Ankündigung der GS-Serie: Unterstützung von Storage Premium für die größten virtuellen Computer in der öffentlichen Cloud)


[Image1]: ./media/storage-premium-storage/Azure_pricing_tier.png
[Image2]: ./media/storage-premium-storage/Azure_attach_premium_disk.png

<!---HONumber=AcomDC_0323_2016-->