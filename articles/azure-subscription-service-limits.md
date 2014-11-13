<properties urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure-Abonnements und Diensteinschr&auml;nkungen, Kontingente und Einschr&auml;nkungen" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Stellt eine Liste allgemeiner Azure-Abonnements und Diensteinschr&auml;nkungen zusammen mit den Maximalwerten bereit." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen

Dieses Dokument beschreibt einige der wichtigsten Einschränkungen in Microsoft Azure. Beachten Sie, dass dieses Dokument nicht alle Azure-Dienste behandelt. Mit der Zeit wird diese Liste der Einschränkungen erweitert, um größere Teile der Plattform abzudecken.

-   [Einschränkungen für Abonnements][Einschränkungen für Abonnements]
-   [Cloud-Diensteinschränkungen][Cloud-Diensteinschränkungen]
-   [Einschränkungen für virtuelle Computer][Einschränkungen für virtuelle Computer]
-   [Website-Einschränkungen][Website-Einschränkungen]
-   [Netzwerkeinschränkungen][Netzwerkeinschränkungen]
-   [Speichereinschränkungen][Speichereinschränkungen]
-   [DocumentDB-Vorschau-Einschränkungen][DocumentDB-Vorschau-Einschränkungen]
-   [Einschränkungen für SQL-Datenbanken][Einschränkungen für SQL-Datenbanken]
-   [Media Services-Einschränkungen][Media Services-Einschränkungen]
-   [Servicebus-Einschränkungen][Servicebus-Einschränkungen]

> [WACOM.NOTE] Falls Sie ein Limit über das **Standardlimit** anheben möchten, können Sie [eine gebührenfreie Online-Kundensupport-Anforderung öffnen][eine gebührenfreie Online-Kundensupport-Anforderung öffnen]. Die Limits können nicht über die Werte unter **Maximales Limit** in der folgenden Tabelle angehoben werden. Falls keine Spalte **Maximales Limit** existiert, bedeutet dies, dass für die entsprechende Ressource keine änderbaren Limits existieren.

## <a name="subscription"></a>Einschränkungen für Abonnements

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource</th>
<th align="left">Standardlimit</th>
<th align="left">Maximales Limit</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Prozessorkerne pro <a href="http://msdn.microsoft.com/de-de/library/azure/hh531793.aspx">Abonnement</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/de-de/library/azure/gg456328.aspx">Co-Administratoren</a> pro Abonnement</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/de-de/documentation/articles/storage-whatis-account/">Speicherkonten</a> pro Abonnement</p></td>
<td align="left"><p>100</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://azure.microsoft.com/de-de/documentation/articles/cloud-services-what-is/">Clouddienste</a> pro Abonnement</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Virtuelle Netzwerke</a> pro Abonnement<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/de-de/library/jj157100.aspx">Lokale Netzwerke</a> pro Abonnement</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>SQL-Datenbanken pro Abonnement</p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>Logische SQL-Datenbankserver pro Abonnement</p></td>
<td align="left"><p>6</p></td>
<td align="left"><p>150</p></td>
</tr>
<tr class="odd">
<td align="left"><p>DNS-Server pro Abonnement</p></td>
<td align="left"><p>9</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>Reservierte IPs pro Abonnement</p></td>
<td align="left"><p>5</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Zertifikate für gehostete Dienste pro Abonnement</p></td>
<td align="left"><p>400</p></td>
<td align="left"><p>400</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/de-de/library/azure/jj156085.aspx">Affinitätsgruppen</a> pro Abonnement</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup> Zusätzliche kleine Instanzen zählen als ganzer Prozessorkern im Sinn des Limits, obwohl nur ein Teil eines Kerns verwendet wird.

<sup>2</sup> Jedes virtuelle Netzwerk unterstützt ein einziges virtuelles Netzwerkgateway.

## <a name="webworkerlimits"></a>Cloud-Diensteinschränkungen

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource</th>
<th align="left">Standardlimit</th>
<th align="left">Maximales Limit</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/de-de/documentation/articles/cloud-services-what-is/">Web-/Workerrollen pro Bereitstellung<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/de-de/library/gg557552.aspx#InstanceInputEndpoint">Instanz-Eingabeendpunkte</a> pro Bereitstellung</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/de-de/library/gg557552.aspx#InputEndpoint">Eingabeendpunkte</a> pro Bereitstellung</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/de-de/library/gg557552.aspx#InternalEndpoint">Interne Endpunkte</a> pro Bereitstellung</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup> Jeder Clouddienst mit Web-/Workerrollen kann zwei Bereitstellungen haben: je eine für Produktions- und Stagingumgebung. Beachten Sie außerdem, dass sich dieses Limit auf die Anzahl der individuellen Rollen (Konfiguration) und nicht auf die Anzahl der Instanzen pro Rolle (Skalierung) bezieht.

## <a name="vmlimits"></a>Einschränkungen für virtuelle Computer

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource</th>
<th align="left">Standardlimit</th>
<th align="left">Maximales Limit</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/de-de/documentation/services/virtual-machines/">Virtuelle Computer</a> pro Clouddienst<sup>1</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Eingabeendpunkte pro Clouddienst<sup>2</sup></p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>150</p></td>
</tr>
</tbody>
</table>

<sup>1</sup> Wenn Sie einen virtuellen Computer erstellen, wird automatisch auch ein Clouddienst erzeugt, der diesen Computer enthält. Sie können anschließend mehrere virtuelle Computer in diesem Clouddienst hinzufügen.

<sup>2</sup> Eingabeendpunkte dienen zur Kommunikation mit virtuellen Computern außerhalb des aktuellen Clouddiensts. Virtuelle Computer innerhalb des gleichen Clouddiensts erlauben automatisch die Kommunikation zwischen allen UDP- und TCP-Ports für die interne Kommunikation.

## <a name="websiteslimits"></a>Website-Einschränkungen

[WACOM.INCLUDE [Azure-Website-Einschränkungen](../includes/azure-websites-limits.md)]

## <a name="networkinglimits"></a>Netzwerkeinschränkungen

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource</th>
<th align="left">Standardlimit</th>
<th align="left">Maximales Limit</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Computer insgesamt<sup>1</sup> pro <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">virtuellem Netzwerk</a><sup>2</sup></p></td>
<td align="left"><p>2048</p></td>
<td align="left"><p>2048</p></td>
</tr>
<tr class="even">
<td align="left"><p>Parallele TCP-Verbindungen für einen virtuellen Computer bzw. eine Rolleninstanz</p></td>
<td align="left"><p>500K</p></td>
<td align="left"><p>500K</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Access Control Lists (ACLs) pro Endpunkt<sup>3</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Lokale Netzwerkstandorte pro virtuellem Netzwerk</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>10</p></td>
</tr>
</tbody>
</table>

<sup>1</sup> Die Gesamtzahl der Computer beinhaltet virtuelle Computer und Web-/Workerrolleninstanzen.

<sup>2</sup> Jedes virtuelle Netzwerk unterstützt ein einziges [virtuelles Netzwerkgateway][virtuelles Netzwerkgateway].

<sup>3</sup>ACL wird auf Eingabeendpunkten für virtuelle Computer unterstützt. Für Web-/Workerrollen wird ACL auf Eingabe- und Instanz-Eingabeendpunkte unterstützt.

## <a name="storagelimits"></a>Speichereinschränkungen

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource<sup>1</sup></th>
<th align="left">Standardlimit</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>TB pro Speicherkonto</p></td>
<td align="left"><p>500 TB</p></td>
</tr>
<tr class="even">
<td align="left"><p>Maximale Größe eines einzelnen Blob-Containers, einer Tabelle oder einer Warteschlange</p></td>
<td align="left"><p>500 TB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Maximale Anzahl an Blob-Containern, Blobs, Dateifreigaben, Tabellen, Warteschlangen, Entitäten oder Meldungen pro Speicherkonto</p></td>
<td align="left"><p>Die einzige Einschränkung besteht in der Speicherkontokapazität von 500 TB.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Maximale Größe einer Dateifreigabe</p></td>
<td align="left"><p>5 TB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Maximale Anzahl an Dateien in einer Dateifreigabe</p></td>
<td align="left"><p>Die einzige Einschränkung besteht in der Gesamtkapazität der Dateifreigabe von 5 TB.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Maximal 8 KB IOPS pro persistentem Laufwerk (Basisstufe)</p></td>
<td align="left"><p>300<sup>2</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>Maximal 8 KB IOPS pro persistentem Laufwerk (Standardstufe)</p></td>
<td align="left"><p>500<sup>2</sup></p></td>
</tr>
<tr class="even">
<td align="left"><p>Gesamtanfragerate (ausgehend von einer Objektgröße von 1 KB) pro Speicherkonto</p></td>
<td align="left"><p>Bis zu 20.000 Entitäten oder Meldungen pro Sekunde</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Zieldurchsatz bei Einzel-Blob</p></td>
<td align="left"><p>Bis zu 60 MB pro Sekunde, oder bis zu 500 Anforderungen pro Sekunde</p></td>
</tr>
<tr class="even">
<td align="left"><p>Zieldurchsatz bei Einzelwarteschlange (Meldungen mit 1 KB)</p></td>
<td align="left"><p>Bis zu 2000 Meldungen pro Sekunde</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Zieldurchsatz bei einzelner Tabellenpartition (Entitäten mit 1 KB)</p></td>
<td align="left"><p>Bis zu 2000 Entitäten pro Sekunde</p></td>
</tr>
<tr class="even">
<td align="left"><p>Max. Eingang pro Speicherkonto (US-Regionen)</p></td>
<td align="left"><p>10 Gbps mit aktiviertem GRS<sup>3</sup>, 20 Gbps bei LRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Max. Ausgang pro Speicherkonto (US-Regionen)</p></td>
<td align="left"><p>20 Gbps mit aktiviertem GRS<sup>3</sup>, 30 Gbps bei LRS</p></td>
</tr>
<tr class="even">
<td align="left"><p>Max. Eingang pro Speicherkonto (Europa- und Asien-Regionen)</p></td>
<td align="left"><p>5 Gbps mit aktiviertem GRS<sup>3</sup>, 10 Gbps bei LRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Max. Ausgang pro Speicherkonto (Europa- und Asien-Regionen)</p></td>
<td align="left"><p>10 Gbps mit aktiviertem GRS<sup>3</sup>, 15 Gbps bei LRS</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Weitere Informationen zu diesen Einschränkungen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure-Speicher][Skalierbarkeits- und Leistungsziele für Azure-Speicher].

<sup>2</sup>Legen Sie bei virtuellen Computern auf der Basisstufe nicht mehr als 66 stark benutzte VHDs in einem Speicherkonto ab, um ein Erreichen der Grenze von 20.000 bei der Gesamtanfragerate (20.000/300) zu verhindern. Legen Sie bei virtuellen Computern auf der Standardstufe nicht mehr als 40 stark benutzte VHDs in einem Speicherkonto ab (20.000/500). Weitere Informationen finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Azure][Größen virtueller Computer und Cloud-Dienste für Azure].

<sup>3</sup>GRS stellt einen [georedundanten Speicher][georedundanten Speicher] dar. LRS stellt einen [lokal redundanten Speicher][lokal redundanten Speicher] dar. Beachten Sie, dass GRS ebenfalls lokal redundant ist.

## <a name="documentdblimits"></a>DocumentDB-Vorschau-Einschränkungen

[WACOM.INCLUDE [Azure-Documentdb-Einschränkungen](../includes/azure-documentdb-limits.md)]

## <a name="sqldblimits"></a>Einschränkungen für SQL-Datenbanken

Weitere Informationen zu Einschränkungen für SQL-Datenbanken finden Sie in den folgenden Themen:

-   [Dienstebenen für Azure SQL-Datenbanken (Editionen)][Dienstebenen für Azure SQL-Datenbanken (Editionen)]
-   [Dienst- und Leistungsebenen für Azure SQL-Datenbanken][Dienst- und Leistungsebenen für Azure SQL-Datenbanken]
-   [Kontingente der Datenbankdurchsatzeinheit (DTU = Database Throughput Unit)][Kontingente der Datenbankdurchsatzeinheit (DTU = Database Throughput Unit)]
-   [Ressourceneinschränkungen für SQL-Datenbanken][Ressourceneinschränkungen für SQL-Datenbanken]

## <a name="mediaserviceslimits"></a>Media Services-Einschränkungen

[WACOM.INCLUDE [Azure-Media Services-Einschränkungen](../includes/azure-mediaservices-limits.md)]

## <a name="servicebuslimits"></a>Servicebus-Einschränkungen

[WACOM.INCLUDE [Azure-Servicebus-Einschränkungen](../includes/azure-servicebus-limits.md)]

## <a name="seealso"></a>Siehe auch

[Grundlegendes zu Azure-Einschränkungen und -Steigerungen][eine gebührenfreie Online-Kundensupport-Anforderung öffnen]

[Größen virtueller Computer und Clouddienste für Azure][Größen virtueller Computer und Cloud-Dienste für Azure]

  [Einschränkungen für Abonnements]: #subscription
  [Cloud-Diensteinschränkungen]: #webworkerlimits
  [Einschränkungen für virtuelle Computer]: #vmlimits
  [Website-Einschränkungen]: #websiteslimits
  [Netzwerkeinschränkungen]: #networkinglimits
  [Speichereinschränkungen]: #storagelimits
  [DocumentDB-Vorschau-Einschränkungen]: #documentdblimits
  [Einschränkungen für SQL-Datenbanken]: #sqldblimits
  [Media Services-Einschränkungen]: #mediaserviceslimits
  [Servicebus-Einschränkungen]: #servicebuslimits
  [eine gebührenfreie Online-Kundensupport-Anforderung öffnen]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [virtuelles Netzwerkgateway]: http://msdn.microsoft.com/de-de/library/azure/jj156210.aspx
  [Skalierbarkeits- und Leistungsziele für Azure-Speicher]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Größen virtueller Computer und Cloud-Dienste für Azure]: http://msdn.microsoft.com/de-de/library/azure/dn197896.aspx
  [georedundanten Speicher]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [lokal redundanten Speicher]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [Dienstebenen für Azure SQL-Datenbanken (Editionen)]: http://msdn.microsoft.com/de-de/library/azure/dn741340.aspx
  [Dienst- und Leistungsebenen für Azure SQL-Datenbanken]: http://msdn.microsoft.com/de-de/library/azure/dn741336.aspx
  [Kontingente der Datenbankdurchsatzeinheit (DTU = Database Throughput Unit)]: http://msdn.microsoft.com/de-de/library/azure/ee336245.aspx#DTUs
  [Ressourceneinschränkungen für SQL-Datenbanken]: http://msdn.microsoft.com/de-de/library/azure/dn338081.aspx
