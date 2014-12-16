<properties title="Split and Merge Service Tutorial" pageTitle="Azure SQL - Lernprogramm für den Split-Merge-Dienst" description="Splitting and Merging with Elastic Scale" metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Elastic Scale - Lernprogramm für den Split-Merge-Dienst

## Herunterladen der Split-Merge-Pakete 
1. Laden Sie die neueste NuGet-Version von [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget) herunter. 
2. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Verzeichnis, in das Sie "nuget.exe" heruntergeladen haben. 
3. Laden Sie das neueste Split-Merge-Paket mit dem folgenden Befehl in das aktuelle Verzeichnis: 
"nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge"  

Mit den oben genannten Schritten werden die Split-Merge-Dateien in das aktuelle Verzeichnis heruntergeladen. Die Dateien werden in einem Verzeichnis namens **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** abgelegt, wobei *x.x.xxx.x* für die Versionsnummer steht. Suchen Sie die Split-Merge-Dienstdateien im Unterverzeichnis**content\splitmerge\service** und die Split-Merge PowerShell-Skripts (und erforderlichen Client-DLLs) im Unterverzeichnis **content\splitmerge\powershell**.

##Voraussetzungen 

1. Erstellen Sie eine Azure SQL-Datenbank, die als Split-Merge-Statusdatenbank verwendet wird. Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com). Klicken Sie unten links auf **Neu**, und klicken Sie dann auf **Datendienste** -> **SQL Database** -> **Benutzerdefiniert erstellen**. Geben Sie den Namen der Datenbank ein und erstellen Sie ein neues Benutzerkonto und Kennwort. Achten Sie darauf, dass Sie den Namen und das Kennwort für die spätere Verwendung aufzeichnen.

2. Stellen Sie sicher, dass Ihr Azure SQL-Datenbankserver die Verbindung mit Windows Azure-Diensten zulässt. Gehen Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com) und klicken Sie im linken Bereich auf **SQL-Datenbanken**. Klicken Sie dann im Menüband oben im Bildschirm auf **Server**, und wählen Sie den Server aus. Klicken Sie dann oben auf **Konfigurieren**, und vergewissern Sie sich, dass die Einstellung **Windows Azure Services** auf **Ja** festgelegt ist.

    ![Allowed services][1]

3. Erstellen Sie ein Speicherkonto in Azure, das für die Diagnoseausgabe verwendet wird. Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com). Klicken Sie unten links auf **Neu**, klicken Sie auf **Datendienste**, **Speicher** und dann auf **Schnellerstellung**. 

4. Erstellen Sie einen Azure-Cloud-Dienst, der Ihren Split-Merge-Dienst enthält.  Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com). Klicken Sie unten links auf **Neu**, dann auf **Berechnen**, **Cloud-Dienst** und **Schnellerstellung**. 


## Konfiguration des Split-Merge-Diensts 

### Split-Merge-Dienstkonfiguration 

1. Erstellen Sie in dem Ordner, in den Sie die Split/Merge-Bits heruntergeladen haben, eine Kopie der Datei **ServiceConfiguration.Template.cscfg**, die in **SplitMergeService.cspkg** enthalten war, und nennen Sie sie **ServiceConfiguration.cscfg**.

2. Öffnen Sie "ServiceConfiguration.cscfg" in einem Texteditor. Es wird empfohlen, Visual Studio zu verwenden, da damit Eingaben wie das Format der Zertifikatfingerabdrücke überprüft werden können. 

3. Erstellen Sie entweder eine neue Datenbank, oder wählen Sie eine vorhandene Datenbank als Statusdatenbank für Split/Merge-Vorgänge aus, und rufen Sie die Verbindungszeichenfolge aus der Datenbank ab. Bei einer Azure SQL-Datenbank hat die Verbindungszeichenfolge in der Regel folgendes Format:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Geben Sie diese Verbindungszeichenfolge in der CSCFG-Datei jeweils im Rollenabschnitt **SplitMergeWeb** und **SplitMergeWorker** in der Einstellung "ElasticScaleMetadata" ein.

5.    Die Konfiguration des Ziels für die Diagnoseprotokolle erfordert ein Speicherkonto in Azure. Für die Konfiguration von Split/Merge ist die Verbindungszeichenfolge für Ihr Azure-Speicherkonto erforderlich. Die Verbindungszeichenfolge sollte folgendem Format entsprechen:

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
Um den Zugriffsschlüssel zu bestimmen, gehen Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com), klicken Sie links auf die Registerkarte **Speicher**, wählen Sie den Namen Ihres Speicherkontos aus und klicken Sie unten auf **Zugriffsschlüssel verwalten**. Klicken Sie auf die **Kopierschaltfläche** für **Primärer Zugriffsschlüssel**.

![manage access keys][2]

6.    Geben Sie den Namen des Speicherkontos und einen der bereitgestellten Zugriffsschlüssel in die Platzhalter in der Speicherverbindungszeichenfolge ein. Diese Verbindungszeichenfolge wird in den beiden Rollenabschnitten **SplitMergeWeb** und **SplitMergeWorker** in der Einstellung **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString** verwendet. Sie können unterschiedliche Speicherkonten für unterschiedliche Rollen verwenden. 

### Konfigurieren der Sicherheit 
Ausführliche Anweisungen zum Konfigurieren der Sicherheit des Diensts finden Sie unter [Sicherheitskonfigurationen für Elastic Scale](./sql-database-elastic-scale-configure-security.md).

Im Rahmen einer einfachen Testbereitstellung, die geeignet ist, um dieses Lernprogramm abzuschließen, wird ein minimaler Satz von Konfigurationsschritte durchgeführt, damit der Dienst ausgeführt werden kann. Mit diesen Schritten wird nur dem einen Computer/Konto, das sie ausführt, die Kommunikation mit dem Dienst ermöglicht.

### Erstellen eines selbstsignierten Zertifikats 

Erstellen Sie ein neues Verzeichnis, und führen Sie aus diesem Verzeichnis den folgenden Befehl mithilfe eines [Entwickler-Eingabeaufforderungsfensters für Visual Studio](http://msdn.microsoft.com/de-de/library/ms229859.aspx) aus:

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Sie werden aufgefordert, ein Kennwort zum Schutz des privaten Schlüssels anzugeben. Geben Sie ein sicheres Kennwort ein, und bestätigen Sie es. Sie werden dann aufgefordert, das Kennwort noch einmal zu verwenden. Klicken Sie am Ende auf **Ja**, um es den Stammspeicher für vertrauenswürdige Zertifizierungsstellen hinzuzufügen.

###    Erstellen einer PFX-Datei 

Führen Sie den folgenden Befehl im gleichen Fenster aus, in dem Makecert ausgeführt wurde; verwenden Sie das gleiche Kennwort, das Sie zum Erstellen des Zertifikats verwendet haben:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Importieren des Clientzertifikats in den persönlichen Speicher
1. Doppelklicken Sie in Windows- Explorer auf **MyCert.pfx**.
2. Wählen Sie im **Zertifikatimport-Assistent** die Option **Aktueller Benutzer** aus, und klicken Sie dann auf **Weiter**.
3. Bestätigen Sie den Dateipfad, und klicken Sie auf **Weiter**.
4. Geben Sie das Kennwort ein, lassen Sie die Option **Alle erweiterten Eigenschaften mit einbeziehen** aktiviert, und klicken Sie auf **Weiter**.
5. Lassen Sie **Zertifikatspeicher automatisch auswählen[...]** aktiviert, und klicken Sie auf **Weiter**.
6. Klicken Sie auf **Fertig stellen** und auf **OK**.

### Hochladen der PFX-Datei in den Cloud-Dienst

Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com).

1. Wählen Sie **Cloud-Dienste** aus.
2. Wählen Sie den Cloud-Dienst, den Sie soeben erstellt haben, als Split/Merge-Dienst aus.
3. Klicken Sie im oberen Menü auf **Zertifikate**.
4. Klicken Sie in der unteren Leiste auf **Hochladen**.
5. Wählen Sie die PFX-Datei aus, und geben Sie dasselbe Kennwort wie oben ein.
6. Nach Abschluss kopieren Sie den Zertifikatfingerabdruck aus den neuen Eintrag in der Liste.

### Aktualisieren der Dienstkonfigurationsdatei

Fügen Sie den oben kopierten Zertifikatfingerabdruck über das thumbprint/value-Attribut dieser Einstellungen ein:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Beachten Sie, dass für Produktionsbereitstellungen getrennte Zertifikate für die Zertifizierungsstelle, das Serverzertifikat und die Clientzertifikate verwendet werden sollten. Ausführliche Anweisungen dazu finden Sie in der [Sicherheitskonfiguration](./sql-database-elastic-scale-configure-security.md).

### Bereitstellen des Split-Merge-Diensts
1. Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com).
2. Klicken Sie oben links auf die Registerkarte **Cloud-Dienste**, und wählen Sie den zuvor erstellten Cloud-Dienst aus. 
3. Klicken Sie auf **Dashboard**. 
4. Wählen Sie die Stagingumgebung aus, und klicken Sie dann auf **Laden Sie eine neue Stagingbereitstellung hoch**.

    ![Staging][3]

5. Geben Sie im Dialogfeld eine Bereitstellungsbezeichnung ein. Wählen Sie für "Paket" und "Konfiguration" die Option "Aus lokaler Ressource", und wählen Sie die Datei **SplitMergeService.cspkg** und die zuvor konfigurierte CSCFG-Datei.
6. Vergewissern Sie sich, dass das Kontrollkästchen **Auch dann bereitstellen, wenn für eine oder mehrere Rollen nur eine Instanz vorhanden ist** aktiviert ist.
7. Drücken Sie die Schaltfläche mit dem Häkchen unten rechts, um mit der Bereitstellung zu beginnen. Der Vorgang kann einige Minuten dauern.

![Upload][4]


## Problembehandlung bei der Bereitstellung
Wenn die Web-Rolle nicht online geschaltet werden kann, liegt wahrscheinlich ein Problem mit der Sicherheitskonfiguration vor. Überprüfen Sie, dass SSL wie oben beschrieben konfiguriert ist.

Wenn die Worker-Rolle nicht online geschaltet werden kann, aber die Web-Rolle erfolgreich ist, liegt sehr wahrscheinlich ein Problem mit dem Herstellen einer Verbindung mit der zuvor erstellten Statusdatenbank vor. 

* Stellen Sie sicher, dass die Verbindungszeichenfolge in der CSCFG-Datei korrekt ist. 
* Überprüfen Sie, dass der Server und die Datenbank vorhanden sind und die Benutzer-ID und das Kennwort korrekt sind.
* Für eine Azure SQL-Datenbank sollte die Verbindungszeichenfolge folgendes Format haben: 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Stellen Sie sicher, dass der Servername nicht mit **https://** beginnt.
* Stellen Sie sicher, dass Ihr Azure SQL-Datenbankserver die Verbindung mit Windows Azure-Diensten zulässt. Zu diesem Zweck öffnen Sie "https://manage.windowsazure.com", klicken Sie auf der linken Seite auf "SQL-Datenbanken", klicken Sie oben auf "Server", und wählen Sie den Server aus. Klicken Sie oben auf **Konfigurieren** und vergewissern Sie sich, dass die Einstellung **Windows Azure Services** auf "Ja" gesetzt ist. (Siehe den Abschnitt "Voraussetzungen" am Anfang dieses Artikels).

* Überprüfen Sie die Diagnoseprotokolle für Ihre Split/Merge-Dienstinstanz. Öffnen Sie eine Visual Studio-Instanz, und klicken Sie in der Menüleiste auf **Ansicht** und **Server-Explorer**. Klicken Sie auf das **Windows Azure**-Symbol, um eine Verbindung zum Azure-Abonnement herzustellen. Navigieren Sie zu Windows Azure -> Speicher -> <Ihr Speicherkonto> -> Tabellen -> WADLogsTable. Weitere Informationen finden Sie unter [Durchsuchen von Speicherressourcen mit Server-Explorer](http://msdn.microsoft.com/de-de/library/azure/ff683677.aspx) 

    ![][5]

    ![][6]

## Testen der Bereitstellung des Split-Merge-Diensts
### Herstellen einer Verbindung mit einem Webbrowser

Bestimmen Sie den Webendpunkt des Split-Merge-Diensts. Die Informationen hierzu finden Sie im Azure-Verwaltungsportal im **Dashboard** Ihres Cloud-Diensts unter **Site-URL** auf der rechten Seite. Ersetzen Sie **http://** durch **https://**, da die Standardsicherheitseinstellungen den HTTP-Endpunkt deaktivieren. Laden Sie die Seite für diese URL in Ihren Browser.

### Testen mit PowerShell-Skripts

Die Bereitstellung und Ihre Umgebung können durch Ausführen des beiliegenden PowerShell-Beispielskripts getestet werden.

Die enthaltenen Skriptdateien sind:

1. **SetupSampleSplitMergeEnvironment.ps1** - richtet eine Testdatenebene für Split/Merge ein (siehe Tabelle unten für eine detaillierte Beschreibung)
2. **ExecuteSampleSplitMerge.ps1** - führt Testvorgänge auf der Testdatenebene aus (siehe Tabelle unten für eine detaillierte Beschreibung)
3. **GetMappings.ps1** - Beipsielskript der obersten Ebene, mit dem der aktuelle Status der Shard-Zuordnungen ausgedruckt wird.
4. **ShardManagement.psm1**  - Hilfsskript, das die ShardManagement-API umschließt
5. **SqlDatabaseHelpers.psm1** - Hilfsskript zum Erstellen und Verwalten von SQL-Datenbanken

<table style="width:100%">
  <tr>
    <th>PowerShell-Datei</th>
    <th>Schritte</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1.    Erstellt eine Shard-Zuordnungs-Manager-Datenbank</td>
  </tr>
  <tr>
    <td>2.    Erstellt 2 Shard-Datenbanken. 
  </tr>
  <tr>
    <td>3.    Erstellt eine Shard-Zuordnung für diese Datenbank (löscht alle vorhandenen Shard-Zuordnungen für diese Datenbanken). </td>
  </tr>
  <tr>
    <td>4.    Erstellt eine kleine Beispieltabelle in beiden Shards und füllt die Tabelle in einem der Shards.</td>
  </tr>
  <tr>
    <td>5.    Deklariert die SchemaInfo für die Shard-Tabelle.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>PowerShell-Datei</th>
    <th>Schritte</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1.    Sendet eine Split-Anforderung an das Web-Front-End des Split-Merge-Diensts. Dadurch wird die Hälfte der Daten des ersten Shard abgetrennt und dem zweiten Shard zugewiesen.</td>
  </tr>
  <tr>
    <td>2.    Ruft im Web-Front-End den Split-Anforderungsstatus ab und wartet, bis die Anforderung abgeschlossen ist.</td>
  </tr>
  <tr>
    <td>3.    Sendet eine Merge-Anforderung an das Web-Front-End des Split-Merge-Diensts, wodurch die Daten vom zweiten Shard zurück zum ersten Shard verschoben werden.</td>
  </tr>
  <tr>
    <td>4.    Ruft im Web-Front-End den Merge-Anforderungsstatus ab und wartet, bis die Anforderung abgeschlossen ist.</td>
  </tr>
</table>

##Überprüfen der Bereitstellung mithilfe von PowerShell

1.    Öffnen Sie ein neues PowerShell-Fenster und navigieren Sie zu dem Verzeichnis, in das Sie das Split-Merge-Paket heruntergeladen haben. Navigieren Sie dann zum Verzeichnis "powershell".
2.    Erstellen einen Azure SQL-Datenbankserver (oder wählen Sie einen vorhandenen Server aus), in dem der Shard-Zuordnungs-Manager und Shards erstellt werden. 

Hinweis: Das SetupSampleSplitMergeEnvironment.ps1-Skript erstellt alle diese Datenbanken standardmäßig auf dem gleichen Server, um das Skript einfach zu halten. Dies ist keine Einschränkung des Split-Merge-Diensts selbst.

    Eine SQL-Authentifizierungsanmeldung mit Lese-/Schreibzugriff auf die Datenbanken ist erforderlich, damit der Split-Merge-Dienst Daten verschieben und die Shard-Zuordnung aktualisieren kann. Da der Split-Merge-Dienst in der Cloud ausgeführt wird, unterstützt er derzeit die integrierte Authentifizierung nicht.

    Stellen Sie sicher, dass der SQL Azure-Server für den Zugriff auf die IP-Adresse des Computers konfiguriert ist, auf dem diese Skripts ausgeführt werden. Diese Einstellung finden Sie unter "Azure SQL-Server / Konfiguration / Zulässige IP-Adressen.

3.    Führen Sie das SetupSampleSplitMergeEnvironment.ps1-Skript aus, um die Beispielumgebung zu erstellen. 

    Beim Ausführen dieses Skripts werden alle vorhandenen Datenstrukturen der Shard-Zuordnungsverwaltung in der Datenbank des Shard-Zuordnungs-Managers und den Shards gelöscht. Sie können das Skript erneut ausführen, wenn Sie die Shard-Zuordnung oder Shards reinitialisieren möchten.

    Beispielbefehlszeile:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    Führen Sie das Getmappings.ps1-Skript aus, um die Zuordnungen anzuzeigen, die derzeit in der Beispielumgebung vorhanden sind.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Führen Sie das ExecuteSampleSplitMerge.ps1-Skript aus, um einen Split-Vorgang auszuführen (die Hälfte der Daten werden vom ersten Shard zum zweiten Shard verschoben), gefolgt von einem Merge-Vorgang (die Daten werden zurück auf das erste Shard verschoben). Wenn Sie SSL konfiguriert haben und den HTTP-Endpunkt deaktiviert belassen haben, stellen Sie sicher, dass Sie stattdessen den https://-Endpunkt verwenden.

    Beispielbefehlszeile:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    Wenn Sie folgenden Fehler erhalten, liegt wahrscheinlich ein Problem mit dem Zertifikat des Webendpunkts vor. Versuchen Sie die Verbindung des Webendpunkts mit Ihrem bevorzugten Webbrowser und überprüfen Sie, ob ein Zertifikatfehler vorliegt.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    Bei erfolgreicher Ausführung sieht die Ausgabe wie folgt aus:

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

6.    Experimentieren Sie mit anderen Datentypen! Alle diese Skripts nehmen einen optionalen ShardKeyType-Parameter an, mit dem Sie den Typ des Schlüssels angeben können. Der Standardwert ist "Int32", aber Sie können auch "Int64", "Guid" oder "Binary" angeben. 

## Erstellen Ihrer eigenen Anforderungen 

Der Dienst kann mithilfe das Web-Benutzeroberfläche oder durch Importieren und Verwenden des SplitMerge.psm1-PowerShell-Moduls verwendet werden.

Der Split-Merge-Dienst kann Daten in Sharding-Tabellen und in Verweistabellen verschieben. Eine Sharding Tabelle verfügt über eine Sharding-Schlüsselspalte und verschiedene Zeilendaten für jedes Shard. Eine Verweistabelle umfasst kein Sharding, sodass sie Daten aus der gleichen Zeile für jedes Shard enthält. Verweistabellen sind hilfreich für Daten, die nicht oft geändert werden, und werden verwendet, um die Verknüpfung mit Sharding Tabellen in Abfragen herzustellen.

Um einen Split-Merge-Vorgang durchführen zu können, müssen Sie die Sharding-Tabellen und Verweistabellen deklarieren, die verschoben werden sollen. Dies erfolgt mit der **SchemaInfo**-API. Diese API befindet sich im **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**-Namespace.

1.    Erstellen Sie für jede Sharding-Tabelle ein **ShardedTableInfo**-Objekt, in dem der Name des übergeordneten Tabellenschemas (optional, der Standardwert ist "dbo"), der Tabellenname und der Spaltenname in der Tabelle mit dem Sharding-Schlüssel beschrieben sind.
2.    Erstellen Sie für jede Verweistabelle ein **ReferenceTableInfo**-Objekt, in dem der Name des übergeordneten Tabellenschemas (optional, der Standardwert ist "dbo") und der Tabellenname beschrieben sind.
3.    Fügen Sie die obigen TableInfo-Objekte einem neuen **SchemaInfo**-Objekt hinzu.
4.    Rufen Sie einen Verweis auf ein **ShardMapManager**-Objekt ab, und rufen Sie **GetSchemaInfoCollection** auf.
5.    Fügen Sie **SchemaInfo** zu **SchemaInfoCollection** hinzu, und geben Sie den Shard-Zuordnungsnamen an.

Ein Beispiel hierfür finden Sie im SetupSampleSplitMergeEnvironment.ps1-Skript.

Beachten Sie, dass der Split-Merge-Dienst nicht die Zieldatenbank (oder das Schema für Tabellen in der Datenbank) für Sie erstellt. Sie müssen vorab erstellt werden, bevor eine Anforderung an den Dienst gesendet wird.


## Problembehandlung
Die folgende Meldung kann angezeigt werden, wenn Sie das Powershell-Beispielskripts ausführen:

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
