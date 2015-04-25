<properties 
	pageTitle="Premium-Speicher: Hochleistungsspeicher für Azure Virtual Machine-Arbeitsauslastungen" 
	description="Erfahren Sie mehr über den Azure Premium-Speicher für Datenträger. Erfahren Sie, wie Sie ein Premium-Speicherkonto erstellen." 
	services="storage" 
	documentationCenter="" 
	authors="Selcin" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="selcint"/>


# Premium-Speicher: Hochleistungsspeicher für Azure Virtual Machine-Arbeitsauslastungen

Willkommen bei der öffentlichen Preview von **Azure Premium-Speicher für Datenträger**!

Mit der Einführung des neuen Premium-Speichers bietet Microsoft Azure jetzt zwei Arten von permanentem Speicher: **Premium-Speicher** und **Standardspeicher**. Der Premium-Speicher speichert Daten auf SSDs (Solid State Drives) der neuesten Technologie, während der Standardspeicher Daten auf HDDs (Hard Disk Drives) speichert. 

Der Premium-Speicher bietet Datenträgerunterstützung für hohe Leistung mit geringe Latenz für E/A-intensive Arbeitsauslastungen, die unter Azure Virtual Machines ausgeführt werden. Sie können mehrere Datenträger des Premium-Speichers an einen virtuellen Computer (VM) anfügen. Mit dem Premium-Speicher können Ihre Anwendungen bis zu 32 TB Speicher pro virtuellem Computer nutzen und 50.000 IOPS (Input/Output Operations Per Second, E/A-Vorgänge pro Sekunde) pro virtuellem Computer mit äußerst niedriger Latenz für Lesevorgänge erzielen. Der Premium-Speicher steht zurzeit nur zum Speichern von Daten auf Datenträgern zur Verfügung, die von Azure Virtual Machines verwendet werden. 

Informationen zur Registrierung für die Vorschau des Azure Premium-Speichers finden Sie auf der Seite [Azure-Vorschau](http://azure.microsoft.com/services/preview/).

Dieser Artikel enthält eine ausführliche Übersicht über den Azure Premium-Speicher.

**Inhaltsverzeichnis:**


* [Wichtige Informationen zum Premium-Speicher](#important)

* [Verwenden des Premium-Speichers für Datenträger](#using)

* [Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers](#scale)	

* [Einschränkungen bei der Verwendung des Premium-Speichers](#throttling)	

* [Momentaufnahmen und "Copy Blob" bei der Verwendung des Premium-Speichers](#restapi)	

* [Preise und Abrechnung bei der Verwendung des Premium-Speichers](#pricing)	

* [Erstellen und Verwenden des Premium-Speicherkontos für Datenträger](#howto1)	

* [Zusätzliche Ressourcen](#see)

###<a id="important">Wichtige Informationen zum Premium-Speicher</a>


Die folgende Liste führt wichtige Informationen auf, die vor oder bei der Verwendung des Premium-Speichers berücksichtigt werden müssen:

- Damit Sie den Premium-Speicher verwenden können, benötigen Sie ein Premium-Speicherkonto. Informationen zum Erstellen eines Premium-Speicherkontos finden Sie unter [Erstellen und Verwenden des Premium-Speicherkontos für Datenträger](#howto1).

- Der Premium-Speicher ist derzeit im [Microsoft Azure Preview Portal](https://portal.azure.com/) verfügbar und kann über die folgenden SDK-Bibliotheken aufgerufen werden: [Storage-REST-API](http://msdn.microsoft.com//library/azure/dd179355.aspx) Version 2014-02-14 oder höher; [Service Management REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) Version 2014-10-01 oder höher; und [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) Version 0.8.10 oder höher. 

- Der Premium-Speicher ist als eingeschränkte Preview in den folgenden Regionen verfügbar: Westen USA, Osten USA 2 und Westeuropa.

- Der Premium-Speicher unterstützt nur Azure-Seiten-BLOBs, die verwendet werden, um persistente Datenträger für Azure Virtual Machines (VMs) zu speichern. Weitere Informationen zu Azure-Seiten-BLOBs finden Sie unter [Grundlegendes zu Block-BLOBs und Seiten-BLOBs](http://msdn.microsoft.com/library/azure/ee691964.aspx). Der Premium-Speicher unterstützt keine Azure-Block-BLOBs, Azure-Dateien, Azure-Tabellen oder Azure-Warteschlangen.

- Ein Premium-Speicherkonto ist lokal redundant (LRS) und verwaltet drei Kopien der Daten innerhalb einer einzelnen Region.  Überlegungen zur Georeplikation bei der Verwendung von Premium-Speicher finden Sie im Abschnitt [Momentaufnahmen und "Copy Blob" bei der Verwendung des Premium-Speichers](#restapi) in diesem Artikel.

- Wenn Sie ein Premium-Speicherkonto für Ihre VM-Datenträger verwenden möchten, müssen Sie die DS-Serie der virtuellen Computer verwenden. Mit der DS-Serie der virtuellen Computer können Sie Datenträger des Standard- und Premium-Speichers verwenden. Sie können jedoch keine Datenträger des Premium-Speichers mit virtuellen Computern verwenden, die nicht zur DS-Serie gehören. Weitere Informationen zu den verfügbaren Typen und Größen von Azure-VM-Datenträgern finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 

- Der Vorgang zum Einrichten von Datenträgern des Premium-Speichers für einen virtuellen Computer ist vergleichbar mit dem Vorgang für Standarddatenträger. Sie müssen die am besten geeignete Option für Premium-Speicher für Ihre Azure-Datenträger und virtuellen Azure-Computer auswählen. Die Größe des virtuellen Computers sollte für Ihre Arbeitsauslastung basierend auf den Leistungsmerkmalen der Premium-Angebote geeignet sein. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers](#scale).
 
- Ein Premium-Speicherkonto kann keinem benutzerdefinierten Domänennamen zugeordnet werden.

- Speicheranalyse wird zurzeit für Premium-Speicher nicht unterstützt.

###<a id="using">Verwenden des Premium-Speichers für Datenträger</a>
Sie können den Premium-Speicher für Datenträger auf zwei Arten verwenden:

- Erstellen Sie zunächst ein neues Premium-Speicherkonto, und verwenden Sie es dann beim Erstellen des virtuellen Computers.
- Erstellen Sie einen neuen virtuellen Computer der DS-Serie. Sie können beim Erstellen des virtuellen Computers ein zuvor erstelltes Premium-Speicherkonto auswählen, ein neues Konto erstellen oder das Azure-Portal ein Premium-Standardkonto erstellen lassen.

Azure verwendet das Speicherkonto als Container für Ihr Betriebssystem und die Datenträger. Wenn Sie einen virtuellen Azure-Computer der DS-Serie erstellen und ein Azure Premium--Speicherkonto auswählen, werden also Ihr Betriebssystem und die Datenträger in diesem Speicherkonto gespeichert.

Damit Sie die Vorteile des Premium-Speichers nutzen können, erstellen Sie zuerst ein Premium-Speicherkonto vom Typ  *Premium_LRS*. Zu diesem Zweck können Sie das [Microsoft Azure Preview-Portal](https://portal.azure.com/), [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) oder die [Dienstverwaltungs-REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) verwenden. Schrittweise Anleitungen finden Sie unter [Erstellen und Verwenden des Premium-Speicherkontos für Datenträger](#howto1).

<h4>Wichtige Hinweise:</h4>

- Informationen hinsichtlich der Einschränkungen der Preview für Kapazität und Bandbreite für Premium-Speicherkonten finden Sie im Abschnitt [Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers](#scale) . Wenn die Anforderungen Ihrer Anwendung die Skalierbarkeitsziele eines einzelnen Speicherkontos überschreiten, erstellen Sie die Anwendung so, dass mehrere Speicherkonten verwendet werden, und partitionieren Sie Ihre Daten in diesen Speicherkonten. Wenn Sie z. B. Datenträger mit einer Kapazität von 51 TB an mehrere virtuelle Computer anfügen möchten, verteilen Sie diese auf zwei Speicherkonten, da 32 TB der Grenzwert für ein einzelnes Premium-Speicherkonto ist.
- Standardmäßig ist die Richtlinie für das Zwischenspeichern für alle Premium-Datenträger "Schreibgeschützt" und für die Premium-Betriebssystem-Datenträger, die an den virtuellen Computer angeschlossen sind, "Lesen/Schreiben". Diese Konfigurationseinstellung wird empfohlen, um die optimale Leistung für die E/A Ihrer Anwendung zu erreichen. Für Datenträger mit hohem oder ausschließlichem Schreibzugriff (z. B. SQL Server-Protokolldateien) deaktivieren Sie das Zwischenspeichern, sodass Sie eine bessere Anwendungsleistung erzielen können.
- Stellen Sie sicher, dass auf Ihrem virtuellen Computer ausreichend Bandbreite zum Steuern des Datenverkehrs des Datenträgers verfügbar ist. Weitere Informationen zu der verfügbaren Datenträgerbandbreite für die einzelnen Größen der virtuellen Computer finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).
- Mit der gleichen DS-Serie der virtuellen Computer können Sie Datenträger des Premium- und Standardspeichers verwenden.
- Mit dem Premium-Speicher können Sie einen virtuellen Computer der DS-Serie bereitstellen und mehrere permanente Datenträger an einen virtuellen Computer anschließen. Bei Bedarf können Sie Daten über die Datenträger verteilen, um die Kapazität und die Leistung des Volumens zu erhöhen. Wenn Sie Daten über Premium-Speicher-Datenträger mithilfe von [Speicherplätze](http://technet.microsoft.com/library/hh831739.aspx) verteilen, sollten Sie sie mit einer Spalte für jeden verwendeten Datenträger konfigurieren. Andernfalls kann die Gesamtleistung des Stripesetvolume aufgrund ungleicher Verteilung des Datenverkehrs auf die Datenträger niedriger sein als erwartet. Standardmäßig können Sie auf der Server-Manager-Benutzeroberfläche Spalten für bis zu 8 Datenträger einrichten. Wenn Sie jedoch mehr als 8 Datenträger besitzen, müssen Sie PowerShell verwenden, um das Volumen zu erstellen. Außerdem müssen Sie die Anzahl der Spalten manuell angeben. Andernfalls verwendet die Server-Manager-Benutzeroberfläche weiterhin 8 Spalten, auch wenn Sie mehr Datenträger besitzen. Wenn Sie beispielsweise 32 Datenträger in einem einzelnen Stripeset besitzen, sollten Sie 32 Spalten angeben. Sie können die *NumberOfColumns* Parameter des PowerShell-Cmdlet [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) verwenden, um die Anzahl der Spalten anzugeben, die von dem virtuellen Datenträger verwendet werden. Weitere Informationen finden Sie unter [Übersicht über Speicherplätze](http://technet.microsoft.com/library/jj822938.aspx) und [Häufig gestellte Fragen zu Speicherplätzen](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).


###<a id="scale">Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers</a>
Wenn Sie einen Datenträger für ein Premium-Speicherkonto bereitstellen, hängen die Anzahl der E/A-Vorgänge pro Sekunde (IOPS) und der Durchsatz (Bandbreite) von der Größe des Datenträgers ab. Zurzeit sind drei Typen von Datenträgern des Premium-Speichers verfügbar: P10, P20 und P30. Jeder Typ weist bestimmte Grenzwerte für IOPS und den Durchsatz auf, wie in der folgenden Tabelle angegeben:

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
	<td>1024 GB (1 TB)</td>
</tr>
<tr>
	<td><strong>IOPS pro Datenträger</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>Durchsatz pro Datenträger</strong></td>
	<td>100 MB pro Sekunde</td>
	<td>150 MB pro Sekunde</td>
	<td>200 MB pro Sekunde</td>
</tr>
</tbody>
</table>

Azure ordnet die Datenträgergröße (aufgerundet) der nächsten Option für Datenträger des Premium-Speichers wie in der Tabelle angegeben zu. Ein Datenträger mit der Größe 100 GB wird z. B. als Option P10 klassifiziert. Er kann bis zu 500 E/A-Einheiten pro Sekunde mit einem Durchsatz von bis zu 100 MB pro Sekunde ausführen. Analog wird ein Datenträger mit der Größe 400 GB als Option P20 klassifiziert. Er kann bis zu 2.300 E/A-Einheiten pro Sekunde mit einem Durchsatz von bis zu 150 MB pro Sekunde ausführen.

Die Größe der Eingabe-/Ausgabeeinheit (E/A) liegt bei 256 KB. Wenn die Daten, die übertragenen werden, kleiner als 256 KB sind, werden sie als einzelne E/A-Einheit betrachtet. Die größeren E/A-Größen werden als mehrere Ein- bzw. Ausgaben der Größe 256 KB gezählt. Eine EA von 1.100 KB wird beispielsweise als fünf E/A-Einheiten gezählt.

Der Grenzwert für den Durchsatz enthält sowohl Lese- als auch Schreibvorgänge auf dem Datenträger. Beispielsweise sollte die Summe der Lese- und Schreibvorgänge kleiner oder gleich 100 MB pro Sekunde für einen Datenträger des Typs P10 sein.  Analog kann ein Datenträger des Typs P10 beispielsweise Lesevorgänge mit 100 MB pro Sekunde oder Schreibvorgänge mit 100 MB pro Sekunde bzw. Lesevorgänge mit 60 MB pro Sekunde und Schreibvorgänge mit 40 MB pro Sekunde aufweisen.
 
Wenn Sie Ihre Datenträger in Azure erstellen, wählen Sie das am besten geeignete Datenträgerangebot für Premium-Speicher basierend auf den Anforderungen Ihrer Anwendung bezüglich Kapazität, Leistung, Skalierbarkeit und Spitzenlasten aus.

Die folgende Tabelle beschreibt die Skalierbarkeitsziele für Premium-Speicherkonten:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Gesamtkapazität des Kontos</strong></td>
	<td><strong>Gesamtbandbreite für ein lokal redundantes Speicherkonto</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Kapazität des Datenträgers: 32 TB</li>
       <li type=round>Kapazität für Momentaufnahmen: 10 TB</li>
    </ul>
	</td>
	<td>Bis zu 50 GB pro Sekunde für eingehenden und ausgehenden Datenverkehr</td>
</tr>
</tbody>
</table>

- "Eingehend" bezieht sich auf alle Daten (Anforderungen), die an ein Speicherkonto gesendet werden.
- "Ausgehend" bezieht sich auf alle Daten (Antworten), die von einem Speicherkonto empfangen werden.

Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungszielen für Azure Storage](http://msdn.microsoft.com/library/dn249410.aspx).


###<a id="throttling">Einschränkungen bei der Verwendung des Premium-Speichers</a>
Ggf. erfolgt eine Einschränkung der IOPS oder des Durchsatzes, wenn Ihre Anwendung die zugewiesenen Grenzwerte für einen Datenträger des Premium-Speichers überschreitet. Damit Einschränkungen vermieden werden, wird empfohlen, die Anzahl der ausstehenden E/A-Anforderungen für Datenträger auf der Grundlage der Skalierbarkeits- und Leistungsziele für den Datenträger einzuschränken, den Sie bereitgestellt haben.  

Ihre Anwendung kann die geringste Latenz erzielen, wenn sie so entworfen wird, dass Einschränkungen vermieden werden. Wenn die Anzahl der ausstehenden E/A-Anforderungen für den Datenträger zu klein ist, kann Ihre Anwendung andererseits die maximalen IOPS- und Durchsatzebenen nicht nutzen, die für den Datenträger verfügbar sind.

Die folgenden Beispiele veranschaulichen, wie die Einschränkungsebenen berechnet werden:

<h4>Beispiel 1:</h4>
Ihre Anwendung hat 495 E/A-Vorgänge mit 16 KB Datenträgergröße (dies entspricht 495 E/A-Einheiten) in einer Sekunde auf einem Datenträger des Typs P10 ausgeführt. Wenn Sie eine E/A von 2 MB in der gleichen Sekunde versuchen, entspricht die Summe der E/A-Einheiten 495 + 8. Dies liegt daran, dass 2 MB E/A zu 2048 KB / 256 KB = 8 E/A-Einheiten führt, wenn die E/A-Einheitengröße 256 KB beträgt. Da die Summe von 495 + 8 die IOPS-Grenze von 500 für den Datenträger überschreitet, tritt eine Einschränkung auf. 

<h4>Hinweis:</h4> 
Alle Berechnungen basieren auf der E/A-Einheitengröße von 256 KB.

<h4>Beispiel 2:</h4>
Ihre Anwendung hat 400 E/A-Vorgänge mit 256 KB Datenträgergröße auf einem Datenträger des Typs P10 ausgeführt. Die verwendete Gesamtbandbreite beträgt (400 * 256) / 1.024 = 100 MB/Sek. Beachten Sie, dass 100 MB pro Sekunde das Durchsatzlimit eines P10-Datenträgers ist. Wenn Ihre Anwendung versucht, weitere E/A-Vorgänge in dieser Sekunde auszuführen, wird sie eingeschränkt, weil der zugewiesene Grenzwert überschritten wird.

<h4>Hinweis:</h4>
Wenn der Datenverkehr des Datenträgers vor allem aus kleinen E/A-Größen besteht, ist es sehr wahrscheinlich, dass Ihre Anwendung den IOPS-Grenzwert vor dem Durchsatzgrenzwert erreicht. Wenn der Datenverkehr des Datenträgers vor allem aus großen E/A-Größen besteht, ist es andererseits sehr wahrscheinlich, dass Ihre Anwendung den Durchsatzgrenzwert und nicht den IOPS-Grenzwert erreicht. Sie können die IOPS Ihrer Anwendung und die Durchsatzkapazität mithilfe optimaler E/A-Größen und auch durch das Einschränken der Anzahl der ausstehenden E/A-Anforderungen für den Datenträger maximieren.


###<a id="restapi">Momentaufnahmen und Kopieren von Blob bei der Verwendung des Premium-Speichers</a>
Sie können eine Momentaufnahme für den Premium-Speicher auf die gleiche Weise erstellen, wie Sie eine Momentaufnahme bei der Verwendung von Standardspeicher erstellen. Da der Premium-Speicher lokal redundant ist, wird empfohlen, Momentaufnahmen zu erstellen und diese dann in ein georedundantes Standardspeicherkonto zu kopieren. Weitere Informationen finden Sie unter [Redundanzoptionen für Azure Storage](http://msdn.microsoft.com/library/azure/dn727290.aspx).

Wenn ein Datenträger an einen virtuellen Computer angefügt ist, sind bestimmte API-Vorgänge für den Seiten-BLOB unzulässig, der den Datenträger sichert. Sie können beispielsweise den Vorgang [Kopieren von Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) auf dem Blob nicht durchführen, solange der Datenträger an den virtuellen Computer angeschlossen ist. Erstellen Sie stattdessen zunächst eine Momentaufnahme dieses Blobs, indem Sie die REST-API-Methode [Momentaufnahme-Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) verwenden und anschließend den Vorgang [Kopieren von Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) der Momentaufnahme durchführen, um den angeschlossenen Datenträger zu kopieren. Alternativ können Sie den Datenträger auch trennen und dann alle erforderlichen Vorgänge für das zugrunde liegende BLOB ausführen.

<h4>Wichtige Hinweise:</h4>

- Wenn der "Copy Blob"-Vorgang für den Premium-Speicher ein vorhandenes BLOB am Ziel überschreibt, darf das überschriebene BLOB keine Momentaufnahmen aufweisen.  Für einen Kopiervorgang in oder zwischen Premium-Speicherkonten ist es erforderlich, dass das Ziel-BLOB keine Momentaufnahmen aufweist, wenn der Kopiervorgang eingeleitet wird.
- Die Anzahl der Momentaufnahmen für ein einzelnes BLOB beträgt 100. Eine Momentaufnahme kann höchstens alle 10 Minuten erstellt werden.
- 10 TB ist die maximale Kapazität für Momentaufnahmen pro Premium-Speicherkonto. Beachten Sie, dass die Kapazität für Momentaufnahmen die eindeutigen Daten sind, die in den Momentaufnahmen vorhanden sind. Anders ausgedrückt, umfasst die Kapazität für Momentaufnahmen nicht die Größe des Basis-BLOBs.
- Sie können Momentaufnahmen aus einem Premium-Speicherkonto mithilfe von "AzCopy" oder "Copy Blob" in ein georedundantes Standardspeicherkonto kopieren. Weitere Informationen finden Sie unter [Verwendung von AzCopy mit Microsoft Azure Storage](http://azure.microsoft.com/documentation/articles/storage-use-azcopy/) und [Kopieren von Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).
- Detaillierte Informationen zum Durchführen von REST-Vorgängen für Seiten-Blobs in Premium-Speicherkonten finden Sie unter [Verwenden von Blob-Dienstvorgängen mit Azure Premium-Speicher](http://go.microsoft.com/fwlink/?LinkId=521969) in der MSDN Library.


###<a id="pricing">Preisgestaltung und Abrechnung bei der Verwendung des Premium-Speichers</a>
Bei der Verwendung des Premium-Speichers gelten die folgenden Abrechnungserwägungen:

- Die Abrechnung für einen Datenträger des Premium-Speichers hängt von der bereitgestellten Größe des Datenträgers ab. Azure ordnet die Datenträgergröße (aufgerundet) der nächsten Option für Datenträger des Premium-Speichers wie in der Tabelle unter [Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers] angegeben zu(#scale) . Die Abrechnung für bereitgestellte Datenträger erfolgt anteilig auf Stundenbasis unter Verwendung des monatlichen Preises für das Premium-Speicherangebot. Wenn Sie z. B. einen Datenträger des Typs P10 bereitgestellt und diesen nach 20 Stunden gelöscht haben, erfolgt die Abrechnung für das P10-Angebot anteilig für 20 Stunden. Dies ist unabhängig von der tatsächlichen Menge von Daten, die auf den Datenträger geschrieben wurden, oder dem verwendeten IOPS/Durchsatz.
- Für Momentaufnahmen für den Premium-Speicher wird die zusätzliche Kapazität in Rechnung gestellt, die von den Momentaufnahmen verwendet wird. Informationen zu Momentaufnahmen finden Sie unter [Erstellen einer Momentaufnahme eines BLOBs](http://msdn.microsoft.com/library/azure/hh488361.aspx).
- [Ausgehende Datenübertragungen](http://azure.microsoft.com/pricing/details/data-transfers/) (Daten, die von den Azure-Rechenzentren ausgehen) verursachen die Abrechnung für die Bandbreitenverwendung. 

Ausführliche Informationen zu den Preisen für den Premium-Speicher und für virtuelle Computern der DS-Serie finden Sie unter:

- [Preisgestaltung des Azure-Speichers](http://azure.microsoft.com/pricing/details/storage/)
- [Preisgestaltung für virtuelle Computer](http://azure.microsoft.com/pricing/details/virtual-machines/)

###<a id="howto1">Erstellen und Verwenden eines Premium-Speicherkontos für Datenträger</a>
In diesem Abschnitt wird veranschaulicht, wie ein Premium-Speicherkonto mit dem Azure Preview-Portal und Azure PowerShell erstellt wird. Darüber hinaus wird ein Beispielanwendungsfall für Premium-Speicherkonten gezeigt: Erstellen eines virtuellen Computers und Anfügen eines Datenträgers an einen virtuellen Computer bei Verwendung des Premium-Speichers.

Inhalt dieses Abschnitts:

* [Azure Preview-Portal: Erstellen eines Premium-Speicherkontos](#howto3)	

* [Azure PowerShell: Erstellen eines Premium-Speicherkontos und Verwenden dieses Kontos für grundlegende Vorgänge virtueller Computer](#howto2)	
 
###<a id="howto3">Azure-Vorschauportal: Erstellen eines Premium-Speicherkontos</a>
In diesem Abschnitt wird gezeigt, wie ein Premium-Speicherkonto mit dem Azure Preview-Portal erstellt wird.

1.	Melden Sie sich am [Azure Preview-Portal](https://portal.azure.com/) an. Testen Sie unser Angebot der [Kostenlosen Testversion](http://azure.microsoft.com/pricing/free-trial/), wenn Sie noch kein Abonnement besitzen. 


    >[AZURE.NOTE] Wenn Sie sich am Azure-Verwaltungsportal anmelden, klicken Sie auf den Namen Ihres Benutzerkontos in der oberen rechten Ecke des Portals. Klicken Sie dann auf **In das neue Portal wechseln**.
        

2.	Klicken Sie im Menü "Hub" auf **Neu**.

3.	Klicken Sie unter **Neu** auf **Alles**. Wählen Sie **Speicher, Cache und Sicherung** aus. Klicken Sie nun auf **Speicher** und dann auf **Erstellen**.

4.	Geben Sie im Bereich "Speicherkonto" einen Namen für Ihr Speicherkonto ein. Klicken Sie auf **Preisstufe**. Klicken Sie im Bereich **Empfohlene Preisstufen** auf **Allel Preisstufen durchsuchen**. Wählen Sie im Bereich **Preisstufe auswählen** die Option **Premium lokal redundant** aus. Klicken Sie auf **Auswählen**. Beachten Sie, dass der Bereich **Speicherkonto** standardmäßig **Standard-GRS** als **Preisstufe** anzeigt. Nachdem Sie auf **Auswählen** geklickt haben, wird die **Preisstufe** als **Premium LRS** angezeigt.
	
	![Pricing Tier][Image1]

	
5.	Behalten Sie im Bereich **Speicherkonto** die Standardwerte für **Ressourcengruppe**, **Abonnement**, **Standort** und **Diagnose** bei. Klicken Sie auf **Erstellen**.

Eine vollständige exemplarische Vorgehensweise in einer Azure-Umgebung finden Sie unter [Erstellen eines virtuellen Computers, der Windows ausführt, im Azure Preview-Portal](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/).

###<a id="howto2">Azure PowerShell: Erstellen eines Premium-Speicherkontos und Verwenden dieses Kontos für grundlegende Vorgänge virtueller Computer</a>
In diesem Abschnitt wird gezeigt, wie ein Premium-Speicherkonto erstellt und verwendet wird, wenn ein virtueller Computer erstellt und ein Datenträger an einen virtuellen Computer mithilfe von Azure PowerShell angefügt wird.

1. Richten Sie die PowerShell-Umgebung mithilfe der unter [Installieren und Konfigurieren von Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) beschriebenen Schritte ein.
2. Starten Sie die PowerShell-Konsole, stellen Sie eine Verbindung mit Ihrem Abonnement her, und führen Sie dann das folgende PowerShell-Cmdlet im Konsolenfenster aus. Wie diese PowerShell-Anweisung zeigt, müssen Sie den Parameter **Type** als **Premium_LRS** angeben, wenn Sie ein Premium-Speicherkonto erstellen.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. Erstellen Sie im nächsten Schritt einen neuen virtuellen Computer der DS-Serie, und geben Sie an, dass Sie Premium-Speicher wünschen, indem Sie die folgenden PowerShell-Cmdlets im Konsolenfenster ausführen:

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

4. Wenn Sie mehr Speicherplatz für Ihren virtuellen Computer benötigen, fügen Sie einen neuen Datenträger an einen vorhandenen virtuellen Computer der DS-Serie an, nachdem dieser erstellt wurde, indem Sie die folgenden PowerShell-Cmdlets im Konsolenfenster ausführen:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

###<a id="see">Zusätzliche Ressourcen</a>
[Verwenden von Blob-Dienstvorgängen mit dem Azure Premium-Speicher](http://go.microsoft.com/fwlink/?LinkId=521969)

[Erstellen eines virtuellen Computers unter Windows](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)

[Größen virtueller Computer und Cloud-Dienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

[Speicher-Dokumentation](http://azure.microsoft.com/documentation/services/storage/)

[MSDN-Referenz](http://msdn.microsoft.com/library/azure/gg433040.aspx)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png

<!--HONumber=42-->
