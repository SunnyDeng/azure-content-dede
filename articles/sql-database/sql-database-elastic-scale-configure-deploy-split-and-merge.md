<properties
	pageTitle="Lernprogramm zum Split-Merge-Tool für elastische Datenbanken | Microsoft Azure"
	description="Aufteilen und Zusammenführen mit Tools für elastische Datenbanken"
	services="sql-database"  
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="sidneyh" />

# Lernprogramm zum Split-Merge-Tool für elastische Datenbanken

## Herunterladen der Split-Merge-Pakete
1. Laden Sie die neueste NuGet-Version von [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget) herunter.
2. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Verzeichnis, in das Sie „nuget.exe“ heruntergeladen haben.
3. Laden Sie das neueste Split-Merge-Paket mit folgendem Befehl in das aktuelle Verzeichnis herunter: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`  

Mit den oben genannten Schritten werden die Split-Merge-Dateien in das aktuelle Verzeichnis heruntergeladen. Die Dateien werden in einem Verzeichnis mit dem Namen **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** abgelegt, wobei *x.x.xxx.x* der Versionsnummer entspricht. Die Split-Merge-Dienstdateien befinden sich im Unterverzeichnis **content\\splitmerge\\service** und die Split-Merge-PowerShell-Skripts (und erforderlichen Client-DLLs) im Unterverzeichnis **content\\splitmerge\\powershell**.

## Voraussetzungen

1. Erstellen Sie eine Azure SQL-Datenbank, die als Split-Merge-Statusdatenbank verwendet wird. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com). Erstellen Sie eine neue **SQL-Datenbank**. Geben Sie den Namen der Datenbank ein, und erstellen Sie einen neuen Benutzer und ein neues Kennwort. Achten Sie darauf, dass Sie den Namen und das Kennwort zur späteren Verwendung notieren.

2. Achten Sie darauf, dass Ihr Azure SQL-Datenbankserver Verbindungen mit Azure-Diensten zulässt. Stellen Sie im Portal in den **Firewalleinstellungen** sicher, dass die Einstellung **Zugriff auf Azure-Dienste erlauben** auf **Ein** eingestellt ist. Klicken Sie auf das Symbol "Speichern".

    ![Zulässige Dienste][1]

3. Erstellen Sie ein Azure Storage-Konto, das für die Diagnoseausgabe verwendet wird. Öffnen Sie das Azure-Portal. Klicken Sie in der linken Leiste auf **Neu**, klicken Sie auf **Daten + Speicher**, und anschließend auf **Speicher**.

4. Erstellen Sie einen Azure-Cloud-Dienst, der den Split-Merge-Dienst enthält. Öffnen Sie das Azure-Portal. Klicken Sie in der linken Leiste auf **Neu**, dann auf **Berechnen**, **Clouddienst**, und**Erstellen**.


## Konfigurieren des Split-Merge-Diensts

### Konfiguration des Split-Merge-Diensts

1. Erstellen Sie in dem Ordner, in den Sie die Split/Merge-Assemblys heruntergeladen haben, eine Kopie der Datei **ServiceConfiguration.Template.cscfg**, die zusammen mit **SplitMergeService.cspkg** bereitgestellt wurde, und benennen Sie sie in **ServiceConfiguration.cscfg** um.

2. Öffnen Sie **ServiceConfiguration.cscfg** in einem Texteditor wie z. B. Visual Studio, der Eingaben wie das Format von Zertifikatfingerabdrücken überprüft.

3. Erstellen Sie eine neue Datenbank, oder wählen Sie eine vorhandene Datenbank als Statusdatenbank für Teilungs-/Zusammenführungsvorgänge (Split/Merge) aus, und rufen Sie die Verbindungszeichenfolge dieser Datenbank ab.

	**Wichtig** Zu diesem Zeitpunkt muss die Statusdatenbank die Sortierreihenfolge "Latin" verwenden (SQL\_Latin1\_General\_CP1\_CI\_AS). Weitere Informationen finden Sie unter [Name der Windows-Sortierreihenfolge (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).

	Bei Azure SQL-Datenbanken hat die Verbindungszeichenfolge in der Regel folgendes Format:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Geben Sie die Verbindungszeichenfolge in der CSCFG-Datei sowohl im Abschnitt für die **SplitMergeWeb**-Rolle als auch für die **SplitMergeWorker**-Rolle der Einstellung „ElasticScaleMetadata“ ein.

5.    Geben Sie für die **SplitMergeWorker**-Rolle eine gültige Verbindungszeichenfolge für den Azure-Speicher für die Einstellung **WorkerRoleSynchronizationStorageAccountConnectionString** ein.
        
### Konfigurieren der Sicherheit
Ausführliche Anweisungen zum Konfigurieren der Dienstsicherheit finden Sie unter [Split-Merge-Sicherheitskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md).

Für eine einfache Testbereitstellung, die für dieses Tutorial geeignet ist, führen Sie einige wenige Konfigurationsschritte aus, um den Dienst zu aktivieren und auszuführen. Durch diese Schritte werden nur ein Computer und Konto für die Kommunikation mit dem Dienst aktiviert.

### Erstellen eines selbstsignierten Zertifikats

Erstellen Sie ein neues Verzeichnis, und führen Sie aus diesem Verzeichnis über ein Fenster [Developer-Eingabeaufforderung für Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) folgenden Befehl aus:

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Sie werden aufgefordert, ein Kennwort zum Schutz des privaten Schlüssels anzugeben. Geben Sie ein sicheres Kennwort ein, und bestätigen Sie es. Danach werden Sie aufgefordert, das zu verwendende Kennwort noch einmal einzugeben. Klicken Sie zum Schluss auf **Ja**, um das Kennwort in den Speicher vertrauenswürdiger Stammzertifizierungsstellen zu importieren.

### Erstellen einer PFX-Datei

Führen Sie den folgenden Befehl im gleichen Fenster aus, in dem „makecert“ ausgeführt wurde. Verwenden Sie das gleiche Kennwort, das Sie zum Erstellen des Zertifikats verwendet haben:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Importieren des Clientzertifikats in den persönlichen Speicher
1. Doppelklicken Sie im Windows-Explorer auf **MyCert.pfx**.
2. Wählen Sie im **Zertifikatimport-Assistenten** die Option **Aktueller Benutzer** aus, und klicken Sie auf **Weiter**.
3. Bestätigen Sie den Dateipfad, und klicken Sie auf **Weiter**.
4. Geben Sie das Kennwort ein, lassen Sie **Alle erweiterten Eigenschaften mit einbeziehen** aktiviert, und klicken Sie auf **Weiter**.
5. Lassen Sie **Zertifikatspeicher automatisch auswählen[…]** aktiviert, und klicken Sie auf **Weiter**.
6. Klicken Sie auf **Fertig stellen** und auf **OK**.

### Hochladen der PFX-Datei in den Cloud-Dienst

Öffnen Sie das [Azure-Portal](https://portal.azure.com).

1. Wählen Sie **Cloud-Dienste**.
2. Wählen Sie den oben für den Split-Merge-Dienst erstellten Cloud-Dienst aus.
3. Klicken Sie im oberen Menü auf **Zertifikate**.
4. Klicken Sie in der unteren Leiste auf **Hochladen**.
5. Wählen Sie die PFX-Datei aus, und geben Sie dasselbe Kennwort wie oben ein.
6. Nach Abschluss kopieren Sie den Zertifikatfingerabdruck aus dem neuen Eintrag in der Liste.

### Aktualisieren der Dienstkonfigurationsdatei

Fügen Sie den oben kopierten Zertifikatfingerabdruck in das thumbprint-Attribut bzw. value-Attribut der folgenden Einstellungen ein. Für die Workerrolle:

    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

Für die Webrolle:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />


Beachten Sie, dass in Produktionsbereitstellungen für die Zertifizierungsstelle, die Verschlüsselung, das Serverzertifikat und die Clientzertifikate getrennte Zertifikate verwendet werden müssen. Ausführliche Anweisungen finden Sie unter [Sicherheitskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md).

### Bereitstellen des Split-Merge-Diensts

1. Öffnen Sie das [Azure-Portal](https://manage.windowsazure.com).
2. Klicken Sie links auf die Registerkarte **Cloud-Dienste** , und wählen Sie den zuvor erstellten Cloud-Dienst aus.
3. Klicken Sie auf **Dashboard**.
4. Wählen Sie die Stagingumgebung aus, und klicken Sie dann auf **Laden Sie eine neue Stagingbereitstellung hoch**.

    ![Wird bereitgestellt][3]

5. Geben Sie im Dialogfeld eine Bezeichnung für die Bereitstellung ein. Klicken Sie sowohl unter „Paket“ als auch „Konfiguration“ auf „Aus lokaler Ressource“, und wählen Sie die Datei **SplitMergeService.cspkg** und die zuvor konfigurierte CSCFG-Datei aus.
6. Stellen Sie sicher, dass das Kontrollkästchen **Auch dann bereitstellen, wenn für eine oder mehrere Rollen nur eine Instanz vorhanden ist** aktiviert ist.
7. Aktivieren Sie die Schaltfläche mit dem Häkchen unten rechts, um die Bereitstellung zu starten. Die Ausführung dauert einige Minuten.

![Hochladen][4]


## Problembehandlung bei der Bereitstellung

Wenn Ihre Webrolle nicht online geschaltet wird, liegt möglicherweise ein Problem mit der Sicherheitskonfiguration vor. Überprüfen Sie, ob SSL wie oben beschrieben konfiguriert ist.

Wenn die Workerrolle nicht online geschaltet wird, während der Vorgang bei der Webrolle erfolgreich ist, liegt sehr wahrscheinlich ein Problem beim Herstellen der Verbindung mit der Statusdatenbank vor, die Sie zuvor erstellt haben.

* Stellen Sie sicher, dass die Verbindungszeichenfolge in der CSCFG-Datei richtig ist.
* Vergewissern Sie sich, dass der Server und die Datenbank vorhanden sind und dass die Benutzer-ID und das Kennwort korrekt sind.
* Bei einer Azure SQL-Datenbank sollte die Verbindungszeichenfolge folgendes Format aufweisen:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Stellen Sie sicher, dass der Servername nicht mit ****https://** beginnt.
* Achten Sie darauf, dass Ihr Azure SQL-Datenbankserver Verbindungen mit Azure-Diensten zulässt. Zu diesem Zweck öffnen Sie https://manage.windowsazure.com, klicken links auf „SQL-Datenbanken“, klicken oben auf „Server“ und wählen Ihren Server aus. Klicken Sie oben auf **Konfigurieren**, und stellen Sie sicher, dass die Einstellung für **Azure-Dienste** auf „Ja“ festgelegt ist. (Siehe den Abschnitt „Voraussetzungen“ am Anfang dieses Artikels.)

## Testen der Split-Merge-Dienstbereitstellung

### Herstellen einer Verbindung mit einem Webbrowser

Ermitteln Sie den Webendpunkt Ihres Split-Merge-Diensts. Sie finden diesen im klassischen Azure-Portal im **Dashboard** Ihres Cloud-Dienstes unter **Website-URL** auf der rechten Seite. Ersetzen Sie ****http://** durch ****https://**, da der HTTP-Endpunkt durch die Standardsicherheitseinstellungen deaktiviert wird. Laden Sie die Seite für diese URL in Ihrem Browser.

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
    <td>3. Erstellt eine Shard Map für diese Datenbanken (löscht alle vorhandenen Shard Maps für diese Datenbanken). </td>
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
    <td>1. Sendet eine Teilungsanforderung an das Web-Front-End des Split-Merge-Diensts, wodurch eine Hälfte der Daten vom ersten auf den zweiten Shard verteilt wird.</td>
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

1.    Öffnen Sie ein neues PowerShell-Fenster, und navigieren Sie zu dem Verzeichnis, in das Sie das Split-Merge-Paket heruntergeladen haben, und wechseln Sie dann in das Verzeichnis „powershell“.
2.    Erstellen Sie einen Azure SQL-Datenbankserver (oder wählen Sie einen vorhandenen Server aus), auf dem ShardMapManager und Shards erstellt werden.

    Hinweis: Um das Skript einfach zu halten, werden alle diese Datenbanken vom Skript „SetupSampleSplitMergeEnvironment.ps1“ standardmäßig auf demselben Server erstellt. Dies ist keine Einschränkung des Split-Merge-Diensts selbst.

    Damit der Split-Merge-Dienst Daten verschieben und die Shard Map aktualisieren kann, ist eine Anmeldung mit SQL-Authentifizierung und Lese-/Schreibzugriff auf die Datenbanken erforderlich. Da der Split-Merge-Dienst in der Cloud ausgeführt wird, bietet er derzeit keine Unterstützung für die integrierte Authentifizierung.

    Stellen Sie sicher, dass der Azure SQL-Server so konfiguriert ist, dass er den Zugriff über die IP-Adresse des Computers zulässt, auf dem diese Skripts ausgeführt werden. Sie finden diese Einstellung auf dem Azure SQL-Server unter „Konfiguration/Zulässige IP-Adressen“.

3.    Führen Sie das Skript „SetupSampleSplitMergeEnvironment.ps1“ aus, um die Beispielumgebung zu erstellen.

    Durch die Ausführung dieses Skripts werden alle vorhandenen Datenstrukturen der Shard Map-Verwaltung in der ShardMapManager-Datenbank und auf den Shards gelöscht. Möglicherweise ist es hilfreich, das Skript erneut auszuführen, wenn Sie die Shard Map oder Shards erneut initialisieren möchten.

    Beispiel für eine Befehlszeile:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

4.    Führen Sie das Skript „Getmappings.ps1“ aus, um die derzeit in der Beispielumgebung vorhandenen Zuordnungen anzuzeigen.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Führen Sie das Skript „ExecuteSampleSplitMerge.ps1“ aus, um einen Teilungsvorgang auszuführen (die Hälfte der Daten vom ersten Shard auf den zweiten Shard zu verschieben) und dann einen Zusammenführungsvorgang auszuführen (die Daten wieder zurück auf den ersten Shard zu verschieben). Wenn Sie SSL konfiguriert und den http-Endpunkt deaktiviert gelassen haben, stellen Sie sicher, dass Sie stattdessen den https://-Endpunkt verwenden.

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

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' –CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
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

6.    Probieren Sie andere Datentypen aus. Alle diese Skripts akzeptieren einen optionalen -ShardKeyType-Parameter, mit dem Sie den Schlüsseltyp angeben können. Der Standardwert ist „Int32“, Sie können aber auch „Int64“, „Guid“ oder „Binary“ angeben.

## Erstellen eigener Anforderungen

Der Dienst kann entweder über die Web-Benutzeroberfläche verwendet werden, oder indem Sie das PowerShell-Modul „SplitMerge.psm1“ importieren und verwenden, das Ihre Anforderungen über die Webrolle übermittelt.

Der Split-Merge-Dienst kann Daten sowohl in Shardtabellen als auch in Verweistabellen verschieben. Eine Shardtabelle verfügt über eine Shardingschlüsselspalte und weist auf jedem Shard unterschiedliche Zeilendaten auf. Eine Verweistabelle ist keine Shardtabelle, sodass sie auf jedem Shard dieselben Zeilendaten enthält. Verweistabellen sind hilfreich für Daten, die sich nicht häufig ändern, und werden für JOIN-Vorgänge mit Shardtabellen in Abfragen verwendet.

Um einen Teilungs-/Zusammenführungsvorgang auszuführen, müssen Sie die zu verschiebenden Shardtabellen und Verweistabellen deklarieren. Sie verwenden dazu die **SchemaInfo**-API. Diese API befindet sich im **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**-Namespace.

1.    Erstellen Sie für jede Shardtabelle ein **ShardedTableInfo**-Objekt, das den Namen des übergeordneten Schemas der Tabelle (optional, standardmäßig „dbo“), den Tabellennamen und den Spaltennamen in der Tabelle angibt, die den Shardingschlüssel enthält.
2.    Erstellen Sie für jede Verweistabelle ein **ReferenceTableInfo**-Objekt, das den Namen des übergeordneten Schemas der Tabelle (optional, standardmäßig „dbo“) und den Tabellennamen angibt.
3.    Fügen Sie die oben beschriebenen TableInfo-Objekte einem neuen **SchemaInfo**-Objekt hinzu.
4.    Rufen Sie einen Verweis auf ein **ShardMapManager**-Objekt ab, und rufen Sie **GetSchemaInfoCollection** auf.
5.    Fügen Sie **SchemaInfo** unter Angabe des Namens der Shard Map zu **SchemaInfoCollection** hinzu.

Ein Beispiel dazu finden Sie im Skript „SetupSampleSplitMergeEnvironment.ps1“.

Beachten Sie, dass durch den Split-Merge-Dienst keine Zieldatenbank (bzw. kein Schema für Tabellen in der Datenbank) für Sie erstellt wird. Diese müssen erstellt werden, bevor eine Anforderung an den Dienst gesendet wird.


## Problembehandlung
Beim Ausführen der PowerShell-Beispielskripts kann folgende Meldung angezeigt werden:

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

Dieser Fehler weist darauf hin, dass das SSL-Zertifikat nicht ordnungsgemäß konfiguriert ist. Folgen Sie den Anweisungen im Abschnitt „Herstellen einer Verbindung mit einem Webbrowser“.

Wenn Sie keine Anforderungen übermitteln können, wird möglicherweise Folgendes angezeigt:

 [Exception] System.Data.SqlClient.SqlException (0x80131904): Die gespeicherte Prozedur 'dbo.InsertRequest' wurde nicht gefunden.

Überprüfen Sie in diesem Fall Ihre Konfigurationsdatei, insbesondere die Einstellung für **WorkerRoleSynchronizationStorageAccountConnectionString**. Dieser Fehler weist normalerweise darauf hin, dass die Workerrolle die Metadaten-Datenbank bei der ersten Verwendung nicht erfolgreich initialisieren konnte.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
 

<!---HONumber=AcomDC_0224_2016-->