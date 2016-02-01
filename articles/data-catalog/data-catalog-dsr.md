<properties
   pageTitle="Von Azure Data Catalog unterstützte Datenquellen | Microsoft Azure"
   description="Spezifikation für die derzeit unterstützten Datenquellen."
   services="data-catalog"
   documentationCenter=""
   authors="trhabe"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="01/15/2016"
   ms.author="trhabe"/>

# Von Azure Data Catalog unterstützte Datenquellen

Benutzer von Azure Data Catalog können Metadaten auf verschiedenem Wege veröffentlichen: Sie können eine öffentliche API nutzen, sie können ein ClickOnce-Registrierungstool verwenden, und sie können die Informationen direkt in das Data Catalog-Webportal eingeben. Die folgende Tabelle fasst alle derzeit vom Katalog unterstützten Quellen zusammen und benennt jeweils die Veröffentlichungsmöglichkeiten. Außerdem werden die externen Datentools aufgelistet, in denen die jeweiligen Quelle direkt aus dem Portal heraus geöffnet werden kann – wir nennen das die „Öffnen in“-Erfahrung im Portal. Weiter unten folgt eine zweite Tabelle mit technischen Spezifikationen für die Verbindungseigenschaften der Datenquellen.


## Liste der unterstützten Datenquellen

<table>

    <tr>
       <td><b>Datenquellenobjekt</b></td>
       <td><b>API</b></td>
       <td><b>Manuelle Eingabe</b></td>
       <td><b>Registrierungstool</b></td>
       <td><b>Tool für „Öffnen in“</b></td>
       <td><b>Hinweise</b></td>
    </tr>

    <tr>
      <td>Azure Data Lake-Speicherverzeichnis</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Data Lake-Speicherdatei</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Storage-Blob</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure-Speicherverzeichnis</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HDFS-Verzeichnis</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HDFS-Datei</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive-Tabelle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Struktur anzeigen</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL-Tabelle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL-Ansicht</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle Database-Tabelle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle Database-Ansicht</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Anderes (generisches Asset)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Data Warehouse-Tabelle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Data Warehouse-Sicht</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services-Dimension</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services-KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services-Measure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services-Tabelle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services-Bericht</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>"Browser"</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server-Tabelle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server-Ansicht</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power&#160;BI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata-Tabelle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata-Sicht</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP HANA-Sicht</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>DB2-Tabelle</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>DB2-Sicht</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Dateisystemdatei</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>FTP-Verzeichnis</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>FTP-Datei</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HTTP-Bericht</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HTTP-Endpunkt</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HTTP-Datei</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>OData-Entitätenmenge</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>OData-Funktion</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>PostgreSQL-Tabelle</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>PostgreSQL-Sicht</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP HANA-Sicht</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Salesforce-Objekt</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SharePoint-Liste </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>


<br> <br>
## Datenquellen-Referenzspezifikation

<table>
    <tr>
       <td><b>Quellentyp</b></td>
       <td><b>Stammtyp</b></td>
       <td><b>Objekttyp</b></td>
       <td><b>Enthalten in Objekttyp</b></td>
       <td><b>DSR-Struktur<b></td>
    </tr>


    <tr>
      <td>Azure Data Lake-Speicher</td>
      <td>Tabelle</td>
      <td>Verzeichnis</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocol: webhdfs
            <br>authentication:{basic, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Azure Data Lake-Speicher</td>
      <td>Tabelle</td>
      <td>Datei</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocol: webhdfs}
            <br>authentication:{basic, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Cosmos</td>
      <td>Container</td>
      <td>Virtueller Cluster</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: cosmos
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Container</td>
      <td>Server</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: reporting-services
            <br>authentication: {windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; version {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Azure Storage</td>
      <td>Tabelle</td>
      <td>Blob</td>
      <td>Container</td>
      <td>
        <font size=2> protocol:azure-blobs
            <br>authentication: {azure-access-key}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; container
            <br>&#160;&#160;&#160;&#160;&#160; name </font>
      </td>
    </tr>

    <tr>
      <td>Azure Storage</td>
      <td>Tabelle</td>
      <td>Verzeichnis</td>
      <td>Container</td>
      <td>
        <font size=2> protocol:azure-blobs
            <br>authentication: {azure-access-key}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; container
            <br>&#160;&#160;&#160;&#160;&#160; name </font>
      </td>
    </tr>

    <tr>
      <td>Azure Storage</td>
      <td>Tabelle</td>
      <td>Tabelle</td>
      <td>Azure Storage</td>
      <td>
        <font size=2> protocol:azure-tables
            <br>authentication: {azure-access-key}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; name </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Container</td>
      <td>Modell</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>
            <br>*basic/anonymous nur über HTTPS verfügbar </font>
      </td>
    </tr>

    <tr>
      <td>Cosmos</td>
      <td>Tabelle</td>
      <td>Datenstrom</td>
      <td>Virtueller Cluster</td>
      <td>
        <font size=2> protocol: cosmos
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Cosmos</td>
      <td>Tabelle</td>
      <td>Datenstromgruppe</td>
      <td>Virtueller Cluster</td>
      <td>
        <font size=2> protocol: cosmos
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Cosmos</td>
      <td>Tabelle</td>
      <td>Ansicht</td>
      <td>Virtueller Cluster</td>
      <td>
        <font size=2> protocol: cosmos
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>Container</td>
      <td>Datenbank</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: db2
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>Tabelle</td>
      <td>Tabelle</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: db2
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>Tabelle</td>
      <td>Ansicht</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: db2
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Dateisystem</td>
      <td>Tabelle</td>
      <td>Datei</td>
      <td>Server</td>
      <td>
        <font size=2> protocol: file
            <br>authentication : {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; path </font>
      </td>
    </tr>

    <tr>
      <td>FTP</td>
      <td>Tabelle</td>
      <td>Verzeichnis</td>
      <td>Server</td>
      <td>
        <font size=2> protocol: ftp
            <br>authentication : {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>FTP</td>
      <td>Tabelle</td>
      <td>Datei</td>
      <td>Server</td>
      <td>
        <font size=2> protocol: ftp
            <br>authentication : {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services-Tabelle</td>
      <td>Container</td>
      <td>Modell</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>
            <br>*basic/anonymous nur über HTTPS verfügbar </font>
      </td>
    </tr>

    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Tabelle</td>
      <td>Verzeichnis</td>
      <td>Cluster</td>
      <td>
        <font size=2> protocol:webhdfs
            <br>authentication:{basic, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Tabelle</td>
      <td>Datei</td>
      <td>Cluster</td>
      <td>
        <font size=2> protocol:webhdfs
            <br>authentication:{basic, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Azure Data Lake-Speicher</td>
      <td>Container</td>
      <td>Data Lake</td>
      <td>–</td>
      <td>
        <font size=2> protocol: webhdfs
            <br>authentication:{basic, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Tabelle</td>
      <td>Tabelle</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol:hive
            <br>authentication: {hdinsight, basic, username}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Tabelle</td>
      <td>Ansicht</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol:hive
            <br>authentication: {hdinsight, basic, username}
            <br>address: server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; Objekt
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Azure Storage</td>
      <td>Container</td>
      <td>Azure Storage</td>
      <td>–</td>
      <td>
        <font size=2> protocol:azure-tables
            <br>authentication: {azure-access-key}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Container</td>
      <td>Website</td>
      <td>–</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Bericht</td>
      <td>Bericht</td>
      <td>Website</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Tabelle</td>
      <td>Endpunkt</td>
      <td>Website</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Azure Storage</td>
      <td>Container</td>
      <td>Container</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol:azure-blobs
            <br>authentication: {Azure-Access-Key}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; container </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Tabelle</td>
      <td>Tabelle</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: mysql
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Tabelle</td>
      <td>Ansicht</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: mysql
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Container</td>
      <td>Cluster</td>
      <td>–</td>
      <td>
        <font size=2> protocol:webhdfs
            <br>authentication:{basic, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>HTTP</td>
      <td>Tabelle</td>
      <td>Datei</td>
      <td>Website</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>Container</td>
      <td>Entitätencontainer</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: odata
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>Container</td>
      <td>Datenbank</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: hiveserver2
            <br>authentication: {hdinsight, basic, username, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; port
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>Tabelle</td>
      <td>Entitätenmenge</td>
      <td>Entitätencontainer</td>
      <td>
        <font size=2> protocol: odata
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; resource </font>
      </td>
    </tr>

    <tr>
      <td>Oracle-Datenbank</td>
      <td>Tabelle</td>
      <td>Tabelle</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: oracle
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Oracle-Datenbank</td>
      <td>Tabelle</td>
      <td>Ansicht</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: oracle
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>Tabelle</td>
      <td>Funktion</td>
      <td>Entitätencontainer</td>
      <td>
        <font size=2> protocol: odata
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; resource </font>
      </td>
    </tr>

    <tr>
      <td>Andere</td>
      <td>Tabelle</td>
      <td>Andere</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: generic-asset
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>

    <tr>
      <td>PostgreSQL</td>
      <td>Container</td>
      <td>Datenbank</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: postgresql
            <br>authentication: {basic, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>PostgreSQL</td>
      <td>Tabelle</td>
      <td>Tabelle</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: postgresql
            <br>authentication: {basic, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>PostgreSQL</td>
      <td>Tabelle</td>
      <td>Ansicht</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: postgresql
            <br>authentication: {basic, windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>Container</td>
      <td>Datenbank</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: mysql
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Tabelle</td>
      <td>Ansicht</td>
      <td>Server</td>
      <td>
        <font size=2> protocol: sap-hana-sql
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Salesforce</td>
      <td>Tabelle</td>
      <td>Objekt</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: salesforce-com
            <br>authentication : {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>

    <tr>
      <td>SharePoint</td>
      <td>Tabelle</td>
      <td>Liste</td>
      <td>Website</td>
      <td>
        <font size=2> protocol: sharepoint-list
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabelle</td>
      <td>Tabelle</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {sql, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabelle</td>
      <td>Ansicht</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {sql, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Tabelle</td>
      <td>Tabelle</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {sql, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Tabelle</td>
      <td>Tabellenwertfunktion</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {sql, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Tabelle</td>
      <td>Ansicht</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {sql, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Oracle-Datenbank</td>
      <td>Container</td>
      <td>Datenbank</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: oracle
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>Modell</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Kpi}
            <br>
            <br>*basic/anonymous nur über HTTPS verfügbar </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Kennzahl</td>
      <td>Kennzahl</td>
      <td>Modell</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Measure} </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Tabelle</td>
      <td>Dimension</td>
      <td>Modell</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; objekt
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Dimension} </font>
      </td>
    </tr>

    <tr>
      <td>SAP Hana</td>
      <td>Container</td>
      <td>Server</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: sap-hana-sql
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services-Tabelle</td>
      <td>Tabelle</td>
      <td>Tabelle</td>
      <td>Modell</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Table}
            <br>
            <br>*basic/anonymous nur über HTTPS verfügbar </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>Container</td>
      <td>Datenbank</td>
      <td>–</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {sql, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>Container</td>
      <td>Datenbank</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {sql, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Bericht</td>
      <td>Bericht</td>
      <td>Server</td>
      <td>
        <font size=2> protocol: reporting-services
            <br>authentication: {windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; path
            <br>&#160;&#160;&#160;&#160;&#160; version {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>Container</td>
      <td>Datenbank</td>
      <td>N/V</td>
      <td>
        <font size=2> protocol: teradata
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>Tabelle</td>
      <td>Tabelle</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: teradata
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>Tabelle</td>
      <td>Ansicht</td>
      <td>Datenbank</td>
      <td>
        <font size=2> protocol: teradata
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object </font>
      </td>
    </tr>



</table>

<!---HONumber=AcomDC_0121_2016-->