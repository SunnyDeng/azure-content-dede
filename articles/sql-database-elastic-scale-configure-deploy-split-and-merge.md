<properties title="Split and Merge Service Tutorial" pageTitle="Lernprogramm zum Azure SQL Split-Merge-Dienst" description="Splitting and Merging with Elastic Scale" metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Lernprogramm zum Split-Merge-Dienst für elastische Skalierung

## Herunterladen der Split-Merge-Pakete 
1. Laden Sie die neueste NuGet-Version von [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget) herunter. 
2. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Verzeichnis, in das Sie "nuget.exe" heruntergeladen haben. 
3. Laden Sie das neueste Split-Merge-Paket mit folgendem Befehl in das aktuelle Verzeichnis herunter: 
'nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge'  

Mit den oben genannten Schritten werden die Split-Merge-Dateien in das aktuelle Verzeichnis heruntergeladen. Die Dateien werden in einem Verzeichnis mit dem Namen **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** abgelegt, wobei *x.x.xxx.x* der Versionsnummer entspricht. Die Split-Merge-Dienstdateien befinden sich im Unterverzeichnis **content\splitmerge\service** und die Split-Merge-PowerShell-Skripts (und erforderlichen Client-DLLs) im Unterverzeichnis **content\splitmerge\powershell**.

##Voraussetzungen 

1. Erstellen Sie eine Azure SQL-Datenbank, die als Split-Merge-Statusdatenbank verwendet wird. Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com). Klicken Sie unten links auf **Neu**, und klicken Sie dann auf **Data Services** -> **SQL-Datenbank** -> **Benutzerdefiniert erstellen**. Geben Sie den Namen der Datenbank ein, und erstellen Sie einen neuen Benutzer und ein neues Kennwort. Achten Sie darauf, dass Sie den Namen und das Kennwort zur späteren Verwendung notieren.

2. Achten Sie darauf, dass Ihr Azure SQL-Datenbankserver Verbindungen mit Windows Azure-Diensten zulässt. Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com), und klicken Sie links auf **SQL-Datenbanken**. Klicken Sie dann auf dem Menüband am oberen Bildschirmrand auf **Server**, und wählen Sie Ihren Server aus. Klicken Sie dann oben auf **Konfigurieren**, und stellen Sie sicher, dass die Einstellung **Windows Azure-Dienste** auf **Ja** festgelegt ist.

    ![Allowed services][1]

3. Erstellen Sie ein Azure Storage-Konto, das für die Diagnoseausgabe verwendet wird. Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com). Klicken Sie unten links auf **Neu**, und klicken Sie dann auf **Data Services**, **Storage** und **Schnellerfassung**. 

4. Erstellen Sie einen Azure-Cloud-Dienst, der den Split-Merge-Dienst enthält.  Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com). Klicken Sie unten links auf **Neu**, und klicken Sie dann auf **Server**, **Cloud-Dienst** und **Schnellerfassung**. 


## Konfigurieren des Split-Merge-Diensts 

### Konfiguration des Split-Merge-Diensts 

1. Erstellen Sie in dem Ordner, in den Sie die Split/Merge-Komponenten heruntergeladen haben, eine Kopie der Datei **ServiceConfiguration.Template.cscfg**, die zusammen mit **SplitMergeService.cspkg** bereitgestellt wurde, und nennen Sie sie **ServiceConfiguration.cscfg**.

2. Öffnen Sie "ServiceConfiguration.cscfg" in Ihrem bevorzugten Text-Editor. Wir empfehlen die Verwendung von Visual Studio, da Eingaben, z. B. das Format von Zertifikatfingerabdrücken, von der Software überprüft werden. 

3. Erstellen Sie entweder eine neue Datenbank, oder wählen Sie eine vorhandene Datenbank als Statusdatenbank für Teilungs-/Zusammenführungsvorgänge (Split/Merge) aus, und rufen Sie die Verbindungszeichenfolge dieser Datenbank ab. Bei Azure SQL-Datenbanken hat die Verbindungszeichenfolge in der Regel folgendes Format:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Geben Sie die Verbindungszeichenfolge in der CSCFG-Datei sowohl im Abschnitt für die **SplitMergeWeb**-Rolle als auch für die **SplitMergeWorker**-Rolle der Einstellung "ElasticScaleMetadata" ein.

5.    Die Konfiguration des Ziels für die Diagnoseprotokolle erfordert ein Azure Storage-Konto. Die Split/Merge-Konfiguration erfordert die Verbindungszeichenfolge für Ihr Azure Storage-Konto. Die Verbindungszeichenfolge sollte folgendem Format entsprechen:

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
Zur Bestimmung des Zugriffsschlüssels wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com), klicken links auf die Registerkarte **Storage**, wählen den Namen Ihres Speicherkontos aus und klicken unten auf **Zugriffsschlüssel verwalten**. Klicken Sie für **Primärer Zugriffsschlüssel** auf die Schaltfläche **Kopieren**.

![manage access keys][2]

6.    Geben Sie den Namen des Speicherkontos und einen der bereitgestellten Zugriffsschlüssel in die Platzhalter der Speicher-Verbindungszeichenfolge ein. Diese Verbindungszeichenfolge wird sowohl im Abschnitt für die **SplitMergeWeb**-Rolle als auch für die **SplitMergeWorker**-Rolle in der Einstellung **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString** verwendet. Sie können unterschiedliche Speicherkonten für die unterschiedlichen Rollen verwenden. 

### Konfigurieren der Sicherheit 
Ausführliche Anweisungen zum Konfigurieren der Dienstsicherheit finden Sie unter [Sicherheitskonfiguration bei elastischer Skalierung](./sql-database-elastic-scale-configure-security.md).

Für eine einfache Testbereitstellung, die zum Ausführen dieses Lernprogramms geeignet ist, führen Sie einige wenige Konfigurationsschritte aus, um den Dienst zu aktivieren und auszuführen. Durch diese Schritte werden nur ein Computer und Konto für die Kommunikation mit dem Dienst aktiviert.

### Erstellen eines selbstsignierten Zertifikats 

Erstellen Sie ein neues Verzeichnis, und führen Sie aus diesem Verzeichnis über ein Fenster [Developer-Eingabeaufforderung für Visual Studio](http://msdn.microsoft.com/en-us/library/ms229859.aspx) folgenden Befehl aus:

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Sie werden aufgefordert, ein Kennwort zum Schutz des privaten Schlüssels anzugeben. Geben Sie ein sicheres Kennwort ein, und bestätigen Sie es. Danach werden Sie aufgefordert, das zu verwendende Kennwort noch einmal einzugeben. Klicken Sie zum Schluss auf **Ja**, um das Kennwort in den Speicher vertrauenswürdiger Stammzertifizierungsstellen zu importieren.

###    Erstellen einer PFX-Datei 

Führen Sie den folgenden Befehl im gleichen Fenster aus, in dem "makecert" ausgeführt wurde. Verwenden Sie das gleiche Kennwort, das Sie zum Erstellen des Zertifikats verwendet haben:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Importieren des Clientzertifikats in den persönlichen Speicher
1. Doppelklicken Sie im Windows-Explorer auf **MyCert.pfx**.
2. Wählen Sie im **Zertifikatimport-Assistenten** die Option **Aktueller Benutzer** aus, und klicken Sie auf **Weiter**.
3. Bestätigen Sie den Dateipfad, und klicken Sie auf **Weiter**.
4. Geben Sie das Kennwort ein, lassen Sie **Alle erweiterten Eigenschaften mit einbeziehen** aktiviert, und klicken Sie auf **Weiter**.
5. Lassen Sie **Zertifikatspeicher automatisch auswählen[...]** aktiviert, und klicken Sie auf **Weiter**.
6. Klicken Sie auf **Fertig stellen** und auf **OK**.

### Hochladen der PFX-Datei in den Cloud-Dienst

Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com).

1. Wählen Sie **Cloud Services** aus.
2. Wählen Sie den oben für den Split-Merge-Dienst erstellten Cloud-Dienst aus.
3. Klicken Sie im oberen Menü auf **Zertifikate**.
4. Klicken Sie in der unteren Leiste auf **Hochladen**.
5. Wählen Sie die PFX-Datei aus, und geben Sie dasselbe Kennwort wie oben ein.
6. Nach Abschluss kopieren Sie den Zertifikatfingerabdruck aus dem neuen Eintrag in der Liste.

### Aktualisieren der Dienstkonfigurationsdatei

Fügen Sie den oben kopierten Zertifikatfingerabdruck in das thumbprint-Attribut bzw. value-Attribut der folgenden Einstellungen ein:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Beachten Sie, dass in Produktionsbereitstellungen für die Zertifizierungsstelle, das Serverzertifikat und die Clientzertifikate getrennte Zertifikate verwendet werden müssen. Ausführliche Anweisungen finden Sie unter [Sicherheitskonfiguration](./sql-database-elastic-scale-configure-security.md).

### Bereitstellen des Split-Merge-Diensts
1. Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com).
2. Klicken Sie links auf die Registerkarte **Cloud Services**, und wählen Sie den zuvor erstellten Cloud-Dienst aus. 
3. Klicken Sie auf **Dashboard**. 
4. Wählen Sie die Stagingumgebung aus, und klicken Sie dann auf **Laden Sie eine neue Stagingbereitstellung hoch**.

    ![Staging][3]

5. Geben Sie im Dialogfeld eine Bezeichnung für die Bereitstellung ein. Klicken Sie sowohl unter "Paket" als auch "Konfiguration" auf "Aus lokaler Ressource", und wählen Sie die Datei **SplitMergeService.cspkg** und die zuvor konfigurierte CSCFG-Datei aus.
6. Stellen Sie sicher, dass das Kontrollkästchen **Auch dann bereitstellen, wenn für eine oder mehrere Rollen nur eine Instanz vorhanden ist** aktiviert ist.
7. Aktivieren Sie die Schaltfläche mit dem Häkchen unten rechts, um die Bereitstellung zu starten. Die Ausführung dauert einige Minuten.

![Upload][4]


## Problembehandlung bei der Bereitstellung
Wenn Ihre Webrolle nicht online geschaltet wird, liegt möglicherweise ein Problem mit der Sicherheitskonfiguration vor. Überprüfen Sie, ob SSL wie oben beschrieben konfiguriert ist.

Wenn die Workerrolle nicht online geschaltet wird, während der Vorgang bei der Webrolle erfolgreich ist, liegt sehr wahrscheinlich ein Problem beim Herstellen der Verbindung mit der Statusdatenbank vor, die Sie zuvor erstellt haben. 

* Stellen Sie sicher, dass die Verbindungszeichenfolge in der CSCFG-Datei richtig ist. 
* Vergewissern Sie sich, dass der Server und die Datenbank vorhanden sind und dass die Benutzer-ID und das Kennwort korrekt sind.
* Bei einer Azure SQL-Datenbank sollte die Verbindungszeichenfolge folgendes Format aufweisen: 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Stellen Sie sicher, dass der Servername nicht mit **https://** beginnt.
* Achten Sie darauf, dass Ihr Azure SQL-Datenbankserver Verbindungen mit Windows Azure-Diensten zulässt. Zu diesem Zweck öffnen Sie "https://manage.windowsazure.com", klicken links auf "SQL-Datenbanken", klicken oben auf "Server" und wählen Ihren Server aus. Klicken Sie oben auf **Konfigurieren**, und stellen Sie sicher, dass die Einstellung **Windows Azure-Dienste** auf "Ja" festgelegt ist. (Siehe den Abschnitt "Voraussetzungen" am Anfang dieses Artikels.)

* Überprüfen Sie die Diagnoseprotokolle für Ihre Split-Merge-Dienstinstanz. Öffnen Sie eine Visual Studio-Instanz, und klicken Sie in der Menüleiste auf **Ansicht** und **Server-Explorer**. Klicken Sie auf das **Windows Azure**-Symbol, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen. Navigieren Sie dann zu "Windows Azure -> Storage -> <Ihr Speicherkonto> -> Tabellen -> WADLogsTable". Weitere Informationen finden Sie unter [Durchsuchen von Speicherressourcen im Server-Explorer](http://msdn.microsoft.com/en-us/library/azure/ff683677.aspx).

    ![][5]

    ![][6]

## Testen der Split-Merge-Dienstbereitstellung
### Herstellen einer Verbindung mit einem Webbrowser

Ermitteln Sie den Webendpunkt Ihres Split-Merge-Diensts. Sie finden diesen im Azure-Verwaltungsportal im **Dashboard** Ihres Cloud-Diensts unter **Website-URL** auf der rechten Seite. Ersetzen Sie **http://** durch **https://**, da der HTTP-Endpunkt durch die Standardsicherheitseinstellungen deaktiviert wird. Laden Sie die Seite für diese URL in Ihrem Browser.

### Testen mit PowerShell-Skripts

Die Bereitstellung und Ihre Umgebung können getestet werden, indem Sie die im Paket enthaltenen PowerShell-Beispielskripts ausführen.

Die enthaltenen Skriptdateien lauten:

1. **SetupSampleSplitMergeEnvironment.ps1**: Richtet eine Testdatenebene für Split/Merge ein (eine ausführliche Beschreibung finden Sie in der Tabelle unten).
2. **ExecuteSampleSplitMerge.ps1**: Führt Testvorgänge auf der Testdatenebene aus (eine ausführliche Beschreibung finden Sie in der Tabelle unten).
3. **GetMappings.ps1**: Beispielskript auf oberster Ebene, das den aktuellen Status der Shardzuordnungen ausgibt.
4. **ShardManagement.psm1**: Hilfsskript, das die ShardManagement-API umschließt.
5. **SqlDatabaseHelpers.psm1**: Hilfsskript zum Erstellen und Verwalten von SQL-Datenbanken.

<table style="width:100%">
  <tr>
    <th>PowerShell-Datei</th>
    <th>Schritte</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1. Erstellt eine ShardMapManager-Datenbank.</td>
  </tr>
  <tr>
    <td>2. Erstellt zwei Sharddatenbanken. 
  </tr>
  <tr>
    <td>3. Erstellt eine Shardzuordnung für diese Datenbanken (löscht alle vorhandenen Shardzuordnungen für diese Datenbanken). </td>
  </tr>
  <tr>
    <td>4. Erstellt eine kleine Beispieltabelle in beiden Shards und füllt die Tabelle in einem der Shards auf.</td>
  </tr>
  <tr>
    <td>5. Deklariert SchemaInfo für die Shardtabelle.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>PowerShell-Datei</th>
    <th>Schritte</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1. Sendet eine Teilungsanforderung an das Web-Front-End des Split-Merge-Diensts, wodurch eine Hälfte der Daten vom ersten auf den zweiten Shard aufgeteilt wird.</td>
  </tr>
  <tr>
    <td>2. Ruft den Status der Teilungsanforderung vom Web-Front-End ab und wartet, bis die Anforderung abgeschlossen ist.</td>
  </tr>
  <tr>
    <td>3. Sendet eine Zusammenführungsanforderung an das Web-Front-End des Split-Merge-Diensts, wodurch die Daten vom zweiten wieder auf den ersten Shard verschoben werden.</td>
  </tr>
  <tr>
    <td>4. Ruft den Status der Zusammenführungsanforderung vom Web-Front-End ab und wartet, bis die Anforderung abgeschlossen ist.</td>
  </tr>
</table>

##Überprüfen der Bereitstellung mithilfe von PowerShell

1.    Öffnen Sie ein neues PowerShell-Fenster, und navigieren Sie zu dem Verzeichnis, in das Sie das Split-Merge-Paket heruntergeladen haben, und wechseln Sie dann in das Verzeichnis "powershell".
2.    Erstellen Sie einen Azure SQL-Datenbankserver (oder wählen Sie einen vorhandenen Server aus), auf dem ShardMapManager und Shards erstellt werden. 

Hinweis: Um das Skript einfach zu halten, werden alle diese Datenbanken vom Skript "SetupSampleSplitMergeEnvironment.ps1" standardmäßig auf demselben Server erstellt. Dies ist keine Einschränkung des Split-Merge-Diensts selbst.

Damit der Split-Merge-Dienst Daten verschieben und die Shardzuordnung aktualisieren kann, ist eine Anmeldung mit SQL-Authentifizierung und Lese-/Schreibzugriff auf die Datenbanken erforderlich. Da der Split-Merge-Dienst in der Cloud ausgeführt wird, bietet er derzeit keine Unterstützung für die integrierte Authentifizierung.

Stellen Sie sicher, dass der Azure SQL-Server so konfiguriert ist, dass er den Zugriff über die IP-Adresse des Computers zulässt, auf dem diese Skripts ausgeführt werden. Sie finden diese Einstellung auf dem Azure SQL-Server unter "Konfiguration/Zulässige IP-Adressen".

3.    Führen Sie das Skript "SetupSampleSplitMergeEnvironment.ps1" aus, um die Beispielumgebung zu erstellen. 

Durch die Ausführung dieses Skripts werden alle vorhandenen Datenstrukturen der Shardzuordnungsverwaltung in der ShardMapManager-Datenbank und auf den Shards gelöscht. Möglicherweise ist es hilfreich, das Skript erneut auszuführen, wenn Sie die Shardzuordnung oder Shards erneut initialisieren möchten.

    Beispiel für eine Befehlszeile:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    Führen Sie das Skript "Getmappings.ps1" aus, um die derzeit in der Beispielumgebung vorhandenen Zuordnungen anzuzeigen.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Führen Sie das Skript "ExecuteSampleSplitMerge.ps1" aus, um einen Teilungsvorgang auszuführen (die Hälfte der Daten vom ersten Shard auf den zweiten Shard zu verschieben) und dann einen Zusammenführungsvorgang auszuführen (die Daten wieder zurück auf den ersten Shard zu verschieben). Wenn Sie SSL konfiguriert und den http-Endpunkt deaktiviert gelassen haben, stellen Sie sicher, dass Sie stattdessen den https://-Endpunkt verwenden.

    Beispiel für eine Befehlszeile:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

Wenn Sie den folgenden Fehler erhalten, liegt sehr wahrscheinlich ein Problem mit dem Zertifikat Ihres Webendpunkts vor. Stellen Sie unter Verwendung Ihres bevorzugten Webbrowsers eine Verbindung mit dem Webendpunkt her, und überprüfen Sie, ob ein Zertifikatfehler auftritt.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    War die Verbindung erfolgreich, sollte die Ausgabe wie folgt aussehen:

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
        Sending split request
        Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
        Polling split-merge request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request. 
        Sending merge request
        Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
        Polling request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.

6.    Probieren Sie andere Datentypen aus. Alle diese Skripts akzeptieren einen optionalen -ShardKeyType-Parameter, mit dem Sie den Schlüsseltyp angeben können. Der Standardwert ist "Int32", Sie können aber auch "Int64", "Guid" oder "Binary" angeben. 

## Erstellen eigener Anforderungen 

Der Dienst kann entweder über die Web-Benutzeroberfläche verwendet werden, oder indem Sie das PowerShell-Modul "SplitMerge.psm1" importieren und verwenden.

Der Split-Merge-Dienst kann Daten sowohl in Shardtabellen als auch in Verweistabellen verschieben. Eine Shardtabelle verfügt über eine Shardingschlüsselspalte und weist auf jedem Shard unterschiedliche Zeilendaten auf. Eine Verweistabelle ist keine Shardtabelle, sodass sie auf jedem Shard dieselben Zeilendaten enthält. Verweistabellen sind hilfreich für Daten, die sich nicht häufig ändern, und werden für JOIN-Vorgänge mit Shardtabellen in Abfragen verwendet.

Um einen Teilungs-/Zusammenführungsvorgang auszuführen, müssen Sie die zu verschiebenden Shardtabellen und Verweistabellen deklarieren. Sie verwenden dazu die **SchemaInfo**-API. Die API befindet sich im **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**-Namespace.

1.    Erstellen Sie für jede Shardtabelle ein **ShardedTableInfo**-Objekt, das den Namen des übergeordneten Schemas der Tabelle (optional, standardmäßig "dbo"), den Tabellennamen und den Spaltennamen in der Tabelle angibt, die den Shardingschlüssel enthält.
2.    Erstellen Sie für jede Verweistabelle ein **ReferenceTableInfo**-Objekt, das den Namen des übergeordneten Schemas der Tabelle (optional, standardmäßig "dbo") und den Tabellennamen angibt.
3.    Fügen Sie die oben beschriebenen TableInfo-Objekte einem neuen **SchemaInfo**-Objekt hinzu.
4.    Rufen Sie einen Verweis auf ein **ShardMapManager**-Objekt ab, und rufen Sie **GetSchemaInfoCollection** auf.
5.    Fügen Sie **SchemaInfo** unter Angabe des Namens der Shardzuordnung **SchemaInfoCollection** hinzu.

Ein Beispiel dazu finden Sie im Skript "SetupSampleSplitMergeEnvironment.ps1".

Beachten Sie, dass durch den Split-Merge-Dienst keine Zieldatenbank (bzw. kein Schema für Tabellen in der Datenbank) für Sie erstellt wird. Diese müssen erstellt werden, bevor eine Anforderung an den Dienst gesendet wird.


## Problembehandlung
Beim Ausführen der PowerShell-Beispielskripts kann folgende Meldung angezeigt werden:

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

Dieser Fehler weist darauf hin, dass das SSL-Zertifikat nicht ordnungsgemäß konfiguriert ist. Folgen Sie den Anweisungen im Abschnitt "Herstellen einer Verbindung mit einem Webbrowser".

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/allowed-services.png
[2]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/manage.png
[3]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/staging.png
[4]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/upload.png
[5]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/storage.png
[6]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/logs.png
