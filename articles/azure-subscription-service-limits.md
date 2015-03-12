<properties 
	pageTitle="Microsoft Azure-Abonnements und Diensteinschränkungen, Kontingente und Einschränkungen" 
	description="Stellt eine Liste allgemeiner Azure-Abonnements und Diensteinschränkungen zusammen mit den Maximalwerten bereit." 
	services="" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="jroth"/>

# Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen

Dieses Dokument beschreibt einige der wichtigsten Einschränkungen in Microsoft Azure. Beachten Sie, dass dieses Dokument nicht alle Azure-Dienste behandelt. Mit der Zeit wird diese Liste der Einschränkungen erweitert, um größere Teile der Plattform abzudecken.

- [Einschränkungen für Abonnements](#subscription)
- [Cloud-Diensteinschränkungen](#webworkerlimits)
- [Einschränkungen für virtuelle Computer](#vmlimits)
- [Website-Einschränkungen](#websiteslimits)
- [Netzwerkeinschränkungen](#networkinglimits)
- [Speichereinschränkungen](#storagelimits)
- [Einschränkungen für die Batch-Vorschau](#batchlimits)
- [DocumentDB-Vorschau-Einschränkungen](#documentdblimits)
- [Einschränkungen für SQL-Datenbanken](#sqldblimits)
- [Media Services-Einschränkungen](#mediaserviceslimits)
- [Service Bus-Einschränkungen](#servicebuslimits)
- [Active Directory-Einschränkungen](#adlimits)

> [WACOM.NOTE] Falls Sie ein Limit über das **Standardlimit** anheben möchten, können Sie eine [gebührenfreie Online-Kundensupport-Anforderung öffnen][azurelimitsblogpost]. Die Limits können nicht über die Werte unter **Maximales Limit** in der folgenden Tabelle angehoben werden. Falls keine Spalte **Maximales Limit** existiert, bedeutet dies, dass für die entsprechende Ressource keine änderbaren Limits existieren.

##<a name="subscription"></a>Einschränkungen für Abonnements

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Standardlimit</th>
   <th align="left" valign="middle">Maximales Limit</th>
</tr>
<tr>
   <td valign="middle"><p>Kerne pro <a href="http://msdn.microsoft.com/library/azure/hh531793.aspx">Abonnement</a><sup>1</sup></p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>10.000</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/gg456328.aspx">Co-Administratoren</a> pro Abonnement</p></td>
   <td valign="middle"><p>200</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/storage-whatis-account/">Speicherkonten</a> pro Abonnement</p></td>
   <td valign="middle"><p>100</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">Cloud-Dienste</a> pro Abonnement</p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Virtuelle Netzwerke</a> pro Abonnement<sup>2</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/jj157100.aspx">Lokale Netzwerke</a> pro Abonnement</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>SQL-Datenbankserver pro Abonnement</p></td>
   <td valign="middle"><p>6</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
<tr>
   <td valign="middle"><p>SQL-Datenbanken pro Server</p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>500</p></td>
</tr>
<tr>
   <td valign="middle"><p>DNS-Server pro Abonnement</p></td>
   <td valign="middle"><p>9</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Reservierte IPs pro Abonnement</p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Zertifikate für gehostete Dienste pro Abonnement</p></td>
   <td valign="middle"><p>400</p></td>
   <td valign="middle"><p>400</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156085.aspx">Affinitätsgruppen</a> pro Abonnement</p></td>
   <td valign="middle"><p>256</p></td>
   <td valign="middle"><p>256</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/">Ressourcengruppen</a> pro Abonnement</p></td>
   <td valign="middle"><p>300</p></td>
   <td valign="middle"><p>300</p></td>
</tr>

<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/services/batch/">Batch-Vorschau</a> Konten pro Region und Abonnement</p></td>
   <td valign="middle"><p>1</p></td>
   <td valign="middle"><p>50</p></td>   
</tr>
</table>

<sup>1</sup>Zusätzliche kleine Instanzen zählen als ganzer Prozessorkern im Sinn des Limits, obwohl nur ein Teil eines Kerns verwendet wird.

<sup>2</sup>Jedes virtuelle Netzwerk unterstützt ein einziges virtuelles Netzwerkgateway.

##<a name="webworkerlimits"></a>Cloud-Diensteinschränkungen

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Standardlimit</th>
   <th align="left" valign="middle">Maximales Limit</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">Web-/Workerrollen pro Bereitstellung<sup>1</sup></a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint">Instanz-Eingabeendpunkte</a> pro Bereitstellung</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint">Eingabeendpunkte</a> pro Bereitstellung</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint">Interne Endpunkte</a> pro Bereitstellung</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>Jeder Cloud-Dienst mit Web-/Workerrollen kann zwei Bereitstellungen haben: je eine für Produktions- und Stagingumgebung. Beachten Sie außerdem, dass sich dieses Limit auf die Anzahl der individuellen Rollen (Konfiguration) und nicht auf die Anzahl der Instanzen pro Rolle (Skalierung) bezieht. 

##<a name="vmlimits"></a>Einschränkungen für virtuelle Computer

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Standardlimit</th>
   <th align="left" valign="middle">Maximales Limit</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">Virtuelle Computer</a> pro Cloud-Dienst<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Eingabeendpunkte pro Cloud-Dienst<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Wenn Sie einen virtuellen Computer erstellen, wird automatisch auch ein Clouddienst erzeugt, der diesen Computer enthält. Sie können anschließend mehrere virtuelle Computer in diesem Clouddienst hinzufügen.

<sup>2</sup>Eingabeendpunkte dienen zur Kommunikation mit virtuellen Computern außerhalb des aktuellen Cloud-Diensts. Virtuelle Computer innerhalb des gleichen Clouddiensts erlauben automatisch die Kommunikation zwischen allen UDP- und TCP-Ports für die interne Kommunikation.

##<a name="websiteslimits"></a>Website-Einschränkungen

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

##<a name="networkinglimits"></a>Netzwerkeinschränkungen

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Standardlimit</th>
   <th align="left" valign="middle">Maximales Limit</th>
</tr>
<tr>
   <td valign="middle"><p>Gesamtanzahl der Computer<sup>1</sup> pro <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Virtuelles Netzwerk</a><sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Parallele TCP-Verbindungen für einen virtuellen Computer bzw. eine Rolleninstanz</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>Zugriffssteuerungslisten (ACLs) pro Endpunkt<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Lokale Netzwerkstandorte pro virtuellem Netzwerk</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>Die Gesamtzahl der Computer beinhaltet virtuelle Computer und Web-/Workerrolleninstanzen.

<sup>2</sup>Jedes virtuelle Netzwerk unterstützt ein einziges [virtuelles Netzwerkgateway][gateway].

<sup>3</sup>ACL wird auf Eingabeendpunkten für virtuelle Computer unterstützt. Für Web-/Workerrollen wird ACL auf Eingabe- und Instanz-Eingabeendpunkte unterstützt.

##<a name="storagelimits"></a>Speichereinschränkungen

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource<sup>1</sup></th>
   <th align="left" valign="middle">Standardlimit</th>
</tr>
<tr>
   <td valign="middle"><p>TB pro Speicherkonto</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximale Größe eines einzelnen Blob-Containers, einer Tabelle oder einer Warteschlange</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximale Anzahl an Blob-Containern, Blobs, Dateifreigaben, Tabellen, Warteschlangen, Entitäten oder Meldungen pro Speicherkonto</p></td>
   <td valign="middle"><p>Die einzige Einschränkung besteht in der Speicherkontokapazität von 500 TB.</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximale Größe einer Dateifreigabe</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximale Anzahl an Dateien in einer Dateifreigabe</p></td>
   <td valign="middle"><p>Die einzige Einschränkung besteht in der Gesamtkapazität der Dateifreigabe von 5 TB.</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximal 8 KB IOPS pro persistentem Laufwerk (Basisstufe)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximal 8 KB IOPS pro persistentem Laufwerk (Standardstufe)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Gesamtanfragerate (ausgehend von einer Objektgröße von 1 KB) pro Speicherkonto</p></td>
   <td valign="middle"><p>Bis zu 20.000 Entitäten oder Meldungen pro Sekunde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Zieldurchsatz bei Einzel-Blob</p></td>
   <td valign="middle"><p>Bis zu 60 MB pro Sekunde, oder bis zu 500 Anforderungen pro Sekunde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Zieldurchsatz bei Einzelwarteschlange (Meldungen mit 1 KB)</p></td>
   <td valign="middle"><p>Bis zu 2000 Meldungen pro Sekunde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Zieldurchsatz bei einzelner Tabellenpartition (Entitäten mit 1 KB)</p></td>
   <td valign="middle"><p>Bis zu 2000 Entitäten pro Sekunde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Eingang pro Speicherkonto (US-Regionen)</p></td>
   <td valign="middle"><p>10 GBit/s, wenn GRS<sup>3</sup> aktiviert ist, 20 GBit/s für LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Ausgang pro Speicherkonto (US-Regionen)</p></td>
   <td valign="middle"><p>20 GBit/s, wenn GRS<sup>3</sup> aktiviert ist, 30 GBit/s für LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Eingang pro Speicherkonto (Europa- und Asien-Regionen)</p></td>
   <td valign="middle"><p>5 GBit/s, wenn GRS<sup>3</sup> aktiviert ist, 10 GBit/s für LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Ausgang pro Speicherkonto (Europa- und Asien-Regionen)</p></td>
   <td valign="middle"><p>10 GBit/s, wenn GRS<sup>3</sup> aktiviert ist, 15 GBit/s für LRS</p></td>
</tr>
</table>

<sup>1</sup>Weitere Informationen zu diesen Einschränkungen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure-Speicher][storagelimits]. 

<sup>2</sup>Legen Sie bei virtuellen Computern auf der Basisstufe nicht mehr als 66 stark benutzte VHDs in einem Speicherkonto ab, um ein Erreichen der Grenze von 20.000 bei der Gesamtanfragerate (20.000/300) zu verhindern. Legen Sie bei virtuellen Computern auf der Standardstufe nicht mehr als 40 stark benutzte VHDs in einem Speicherkonto ab (20.000/500). Weitere Informationen finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Azure][vmsizes]. 

<sup>3</sup>GRS stellt einen [georedundanten Speicher][georedundantstorage] dar. LRS stellt einen [lokal redundanten Speicher][locallyredundantstorage] dar. Beachten Sie, dass GRS ebenfalls lokal redundant ist.

##<a name="batchlimits"></a>Einschränkungen für die Batch-Vorschau

[WACOM.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

##<a name="documentdblimits"></a>DocumentDB-Vorschau-Einschränkungen

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

##<a name="sqldblimits"></a>Einschränkungen für SQL-Datenbanken

Weitere Informationen zu Einschränkungen für SQL-Datenbanken finden Sie in den folgenden Themen:

 - [Dienstebenen für Azure SQL-Datenbanken (Editionen)][sqltiers]
 - [Dienst- und Leistungsebenen für Azure SQL-Datenbanken][sqltiersperflevels]
 - [Kontingente der Datenbankdurchsatzeinheit (DTU = Database Throughput Unit)][sqlDTU]
 - [Ressourceneinschränkungen für SQL-Datenbanken][sqldblimits]

##<a name="mediaserviceslimits"></a>Media Services-Einschränkungen

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

##<a name="servicebuslimits"></a>Servicebus-Einschränkungen

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

##<a name="adlimits"></a>Active Directory-Einschränkungen

Informationen zu Azure Active Directory (AD) finden Sie unter dem folgenden Thema:

 - [Dienstlimits und -einschränkungen für Azure Active Directory][adlimitsandrestrictions]

##<a name="seealso"></a>Siehe auch

[Grundlegendes zu Azure-Einschränkungen und -Steigerungen][azurelimitsblogpost]

[Größen virtueller Computer und Cloud-Dienste für Azure][vmsizes]

  [customersupportfaq]: http://azure.microsoft.com/support/faq/
  [azurelimitsblogpost]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [gateway]: http://msdn.microsoft.com/library/azure/jj156210.aspx 
  [storagelimits]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [sqldblimits]: http://msdn.microsoft.com/library/azure/dn338081.aspx
  [sqltiers]: http://msdn.microsoft.com/library/azure/dn741340.aspx
  [sqltiersperflevels]: http://msdn.microsoft.com/library/azure/dn741336.aspx
  [sqlDTU]: http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs
  [vmsizes]: http://msdn.microsoft.com/library/azure/dn197896.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [locallyredundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [adlimitsandrestrictions]: http://msdn.microsoft.com/library/azure/dn764971.aspx

<!--HONumber=35.2-->

<!--HONumber=46--> 
