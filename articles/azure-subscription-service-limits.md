<properties linkid="azure-subscription-service-limits" urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Provides a list of common Azure subscription and service limits along with maximum values." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen

Dieses Dokument beschreibt einige der wichtigsten Einschränkungen in Microsoft Azure. Beachten Sie, dass dieses Dokument nicht alle Azure-Dienste behandelt. Mit der Zeit wird diese Liste der Einschränkungen erweitert, um größere Teile der Plattform abzudecken.

-   [Einschränkungen für Abonnements][]
-   [Einschränkungen für Web- und Workerrollen][]
-   [Einschränkungen für virtuelle Computer][]
-   [Netzwerkeinschränkungen][]
-   [Speichereinschränkungen][]
-   [Einschränkungen für SQL-Datenbanken][]

> [WACOM.NOTE] Falls Sie ein Limit über das **Standardlimit** anheben möchten, müssen Sie ein [Kundensupport][]-Ticket erstellen. Die Limits können nicht über die Werte unter **Maximales Limit** in der folgenden Tabelle angehoben werden. Falls keine Spalte **Maximales Limit** existiert, bedeutet dies, dass für die entsprechende Ressource keine änderbaren Limits existieren.

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
<td align="left"><p>Prozessorkerne pro <a href="http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx">Abonnement</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx">Co-Administratoren</a> pro Abonnement</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/">Speicherkonten</a> pro Abonnement</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">Clouddienste</a> pro Abonnement</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Virtuelle Netzwerke</a> pro Abonnement<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/jj157100.aspx">Lokale Netzwerke</a> pro Abonnement</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
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
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx">Affinitätsgruppen</a> pro Abonnement</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup> Zusätzliche kleine Instanzen zählen als ganzer Prozessorkern im Sinn des Limits, obwohl nur ein Teil eines Kerns verwendet wird.

<sup>2</sup> Jedes virtuelle Netzwerk unterstützt ein einziges virtuelles Netzwerkgateway.

## <a name="webworkerlimits"></a>Einschränkungen für Web- und Workerrollen

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
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">Web-/Workerrollen pro Bereitstellung<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint">Instanz-Eingabeendpunkte</a> pro Bereitstellung</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint">Eingabeendpunkte</a> pro Bereitstellung</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint">Interne Endpunkte</a> pro Bereitstellung</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup> Jeder Clouddienst mit Web-/Workerrollen kann zwei Bereitstellungen haben: je eine für Produktions- und Stagingumgebung.

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
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/services/virtual-machines/">Virtuelle Computer</a> pro Clouddienst<sup>1</sup></p></td>
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

<sup>2</sup> Jedes virtuelle Netzwerk unterstützt ein einziges [virtuelles Netzwerkgateway][].

<sup>3</sup>ACL wird auf Eingabeendpunkten für virtuelle Computer unterstützt. Für Web-/Workerrollen wird ACL auf Eingabe- und Instanz-Eingabeendpunkte unterstützt.

## <a name="storagelimits"></a>Speichereinschränkungen<sup>1</sup>

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource</th>
<th align="left">Standardlimit</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>TB pro Speicherkonto<sup>2</sup></p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>Max. IOPS für persistente Laufwerke</p></td>
<td align="left"><p>500<sup>3</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>Max. IOPS pro Speicherkonto</p></td>
<td align="left"><p>20,000</p></td>
</tr>
<tr class="even">
<td align="left"><p>Max. Eingang pro Speicherkonto (US-Regionen)</p></td>
<td align="left"><p>10 Gbps mit aktiviertem GRS<sup>4</sup>, 20 Gbps mit deaktiviertem GRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Max. Eingang pro Speicherkonto (Europa- und Asien-Regionen)</p></td>
<td align="left"><p>5 Gbps mit aktiviertem GRS<sup>4</sup>, 10 Gbps mit deaktiviertem GRS</p></td>
</tr>
<tr class="even">
<td align="left"><p>Max. Ausgang pro Speicherkonto (US-Regionen)</p></td>
<td align="left"><p>20 Gbps mit aktiviertem GRS<sup>4</sup>, 30 Gbps mit deaktiviertem GRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Max. Ausgang pro Speicherkonto (Europa- und Asien-Regionen)</p></td>
<td align="left"><p>10 Gbps mit aktiviertem GRS<sup>4</sup>, 15 Gbps mit deaktiviertem GRS</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Weitere Informationen zu diesen Einschränkungen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure-Speicher][].

<sup>2</sup> Für Seitenblobs fällt nur für tatsächlich genutzte Seiten Kapazitätsnutzung an. Für einen virtuellen Computer mit einer VHD mit 127 GB, von denen das BS jedoch nur 30 GB belegt, werden z. B. nur die 30 GB innerhalb der VHD abgerechnet, und nicht die gesamten 127 GB.

<sup>3</sup> Verwenden Sie nicht mehr als 40 stark benutzte VHDs in einem Konto, um das Limit von 20.000 IOPS nicht zu überschreiten.

<sup>4</sup> Georedundantes Speicherkonto

## <a name="sqldblimits"></a>Einschränkungen für SQL-Datenbanken

Weitere Informationen zu Einschränkungen für SQL-Datenbanken finden Sie in den folgenden Themen:

-   [Dienstebenen für Azure SQL-Datenbanken (Editionen)][]
-   [Dienst- und Leistungsebenen für Azure SQL-Datenbanken][]
-   [Ressourceneinschränkungen für SQL-Datenbanken][]

## <a name="seealso"></a>Siehe auch

[Größen virtueller Computer und Clouddienste für Azure][]

[Skalierbarkeits- und Leistungsziele für Azure Storage][Skalierbarkeits- und Leistungsziele für Azure-Speicher]

[Dienstebenen für Azure SQL-Datenbanken (Editionen)][]

[Dienst- und Leistungsebenen für Azure SQL-Datenbanken][]

[Ressourceneinschränkungen für SQL-Datenbanken][]

  [Einschränkungen für Abonnements]: #subscription
  [Einschränkungen für Web- und Workerrollen]: #webworkerlimits
  [Einschränkungen für virtuelle Computer]: #vmlimits
  [Netzwerkeinschränkungen]: #networkinglimits
  [Speichereinschränkungen]: #storagelimits
  [Einschränkungen für SQL-Datenbanken]: #sqldblimits
  [Kundensupport]: http://azure.microsoft.com/en-us/support/faq/
  [Abonnement]: http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx
  [Co-Administratoren]: http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx
  [Speicherkonten]: http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/
  [Clouddienste]: http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/
  [Virtuelle Netzwerke]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [Lokale Netzwerke]: http://msdn.microsoft.com/en-us/library/jj157100.aspx
  [Affinitätsgruppen]: http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx
  [Instanz-Eingabeendpunkte]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint
  [Eingabeendpunkte]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint
  [Interne Endpunkte]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint
  [Virtuelle Computer]: http://azure.microsoft.com/en-us/documentation/services/virtual-machines/
  [virtuelles Netzwerkgateway]: http://msdn.microsoft.com/en-us/library/azure/jj156210.aspx
  [Skalierbarkeits- und Leistungsziele für Azure-Speicher]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Dienstebenen für Azure SQL-Datenbanken (Editionen)]: http://msdn.microsoft.com/en-us/library/azure/dn741340.aspx
  [Dienst- und Leistungsebenen für Azure SQL-Datenbanken]: http://msdn.microsoft.com/en-us/library/azure/dn741336.aspx
  [Ressourceneinschränkungen für SQL-Datenbanken]: http://msdn.microsoft.com/en-us/library/azure/dn338081.aspx
  [Größen virtueller Computer und Clouddienste für Azure]: http://msdn.microsoft.com/en-us/library/azure/dn197896.aspx
