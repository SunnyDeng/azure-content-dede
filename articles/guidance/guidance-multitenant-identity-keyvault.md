<properties
   pageTitle="Verwenden von Key Vault zum Schützen von Anwendungsgeheimnissen | Microsoft Azure"
   description="Informationen zur Verwendung des Schlüsseltresordiensts zum Speichern von Anwendungsgeheimnissen"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Verwenden des Azure Key Vault zum Schützen von Anwendungsgeheimnissen

Dieser Artikel ist [Teil einer Serie]. Es gibt auch eine vollständige [Beispielanwendung], die diese Serie begleitet.

## Übersicht

In der Regel sind einige Anwendungseinstellungen vertraulich und müssen geschützt werden, wie z. B.:

- Datenbankverbindungszeichenfolgen
- Kennwörter
- Kryptografische Schlüssel

Aus Sicherheitsgründen sollten Sie diese vertraulichen Daten niemals in der Quellcodeverwaltung speichern. Sie sind dort nicht genügend vor Zugriff durch Dritte geschützt, auch wenn Ihr Quellcoderepository privat ist. Es geht aber nicht nur darum, geheime Daten vor Fremden zu schützen. Bei größeren Projekten möchten Sie vielleicht einschränken, welche Entwickler und Operatoren Zugriff auf die Produktionsgeheimnisse haben. (Die Einstellungen für Test- und Entwicklungsumgebungen weichen ab.)

Sicherer ist es, diese vertraulichen Daten in [Azure Key Vault][KeyVault] zu speichern. Key Vault ist ein in der Cloud gehosteter Dienst für die Verwaltung von kryptografischen Schlüsseln und anderer geheimer Schlüssel. Dieser Artikel zeigt, wie Key Vault verwendet wird, um Konfigurationseinstellungen für Ihre App zu speichern.

Folgende Einstellungen in der Anwendung [Tailspin Surveys][Surveys] sind geheim:

- die Datenbankverbindungszeichenfolge
- die Redis-Datenbankverbindungszeichenfolge
- der geheime Clientschlüssel für die Webanwendung

Zum Speichern von geheimen Konfigurationsinformationen im Schlüsseltresor implementiert die Surveys-Anwendung einen benutzerdefinierten Konfigurationsanbieter, der sich in das [Konfigurationssystem][configuration] von ASP.NET Core 1.0 einbindet. Der benutzerdefinierte Anbieter liest die Konfigurationseinstellungen beim Start von Key Vault.

Die Surveys-Anwendung lädt die Konfigurationseinstellungen aus den folgenden Quellen:

- aus der Datei „appsettings.json“
- aus dem [user secrets store][user-secrets] (nur Entwicklungsumgebung; für Testzwecke)
- aus der Hostingumgebung (App-Einstellungen in Azure-Web-Apps)
- Schlüsseltresor

Jede dieser Einstellungen überschreibt jeweils die vorherige. Alle Einstellungen, die in Key Vault gespeichert sind, haben also Vorrang.

> [AZURE.NOTE] Standardmäßig ist der Key Vault-Konfigurationsanbieter deaktiviert. Er ist nicht erforderlich, um die Anwendung lokal auszuführen. Sie müssten ihn in einer Produktionsbereitstellung aktivieren.

> [AZURE.NOTE] Der Key Vault-Anbieter wird derzeit nicht für .NET Core unterstützt, da er das [Microsoft.Azure.KeyVault][Microsoft.Azure.KeyVault]-Paket erfordert.

Beim Start liest die Anwendung die Einstellungen aller registrierten Konfigurationsanbieter, und verwendet diese zum Auffüllen eines stark typisierten Optionenobjekts. (Weitere Informationen finden Sie unter [Using Options and configuration objects][options] (Verwenden von Optionen und Konfigurationsobjekten).)

## Implementierung

Die [KeyVaultConfigurationProvider][KeyVaultConfigurationProvider]-Klasse ist ein Konfigurationsanbieter, der mit dem [Konfigurationssystem][configuration] von ASP.NET Core 1.0 verknüpft wird.

Um `KeyVaultConfigurationProvider` zu verwenden, rufen Sie die Erweiterungsmethode `AddKeyVaultSecrets` in der Startklasse auf:

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

Beachten Sie, dass `KeyVaultConfigurationProvider` einige Konfigurationseinstellungen erfordert, die in einer der anderen Konfigurationsquellen gespeichert werden müssen.

Beim Starten der Anwendung listet `KeyVaultConfigurationProvider` alle geheimen Schlüssel im Schlüsseltresor auf. Für jeden geheimen Schlüssel sucht dieser nach einem Tag mit dem Namen „ConfigKey“. Der Wert des Tags ist der Name der Konfigurationseinstellung.

> [AZURE.NOTE] [Tags][Schlüsseltags] sind optionale Metadaten, die mit einem Schlüssel gespeichert sind. Hier werden Tags verwendet, da Schlüsselnamen keine Doppelpunkte (:) enthalten dürfen.

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] Weitere Informationen finden Sie unter [KeyVaultConfigurationProvider.cs].

## Einrichten von Key Vault in der Surveys-App

Voraussetzungen:

- Installieren Sie die [Azure-Ressourcen-Manager-Cmdlets][azure-rm-cmdlets].
- Konfigurieren Sie die Surveys-Anwendung wie in [Running the Surveys application][readme] (Ausführen der Surveys-Anwendung) beschrieben.

Allgemeine Schritte:

1. Richten Sie einen Benutzer mit Administratorrechten im Mandanten ein.
2. Richten Sie ein Clientzertifikat ein.
3. Erstellen eines Schlüsseltresors
4. Fügen Sie Ihrem Schlüsseltresor Konfigurationseinstellungen hinzu.
5. Entfernen Sie den Code, der den Schlüsseltresor aktiviert.
6. Aktualisieren Sie die geheimen Benutzerschlüssel der Anwendung.

### Richten Sie einen Benutzer mit Administratorrechten ein.

> [AZURE.NOTE] Um einen Schlüsseltresor zu erstellen, müssen Sie ein Konto verwenden, das Ihr Azure-Abonnement verwalten kann. Jede Anwendung, der Sie erlauben, Daten aus dem Schlüsseltresor auszulesen, muss im gleichen Mandanten wie dieses Konto registriert sein.

In diesem Schritt stellen Sie sicher, dass Sie einen Schlüsseltresor erstellen können, während Sie als Benutzer im Mandanten angemeldet sind, in dem auch die Surveys-App registriert ist.

Ändern Sie zuerst das Verzeichnis, das mit Ihrem Azure-Abonnement verknüpft ist.

1. Melden Sie sich im [Microsoft Azure-Verwaltungsportal][azure-management-portal] an.

2. Klicken Sie auf **Einstellungen**.

    ![Einstellungen](media/guidance-multitenant-identity/settings.png)

3. Wählen Sie Ihr Azure-Abonnement.

4. Klicken Sie auf **Verzeichnis bearbeiten** am unteren Rand des Portals.

    ![Einstellungen](media/guidance-multitenant-identity/edit-directory.png)

5. Wählen Sie in „Zugeordnetes Verzeichnis ändern“ den Azure AD-Mandanten, in dem die Surveys-Anwendung registriert ist.

    ![Einstellungen](media/guidance-multitenant-identity/edit-directory2.png)

6. Klicken Sie auf den Pfeil, und schließen Sie das Dialogfeld.

Erstellen Sie einen Benutzer mit Administratorrechten im Azure AD-Mandanten, in dem die Surveys-Anwendung registriert ist.

1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-management-portal] an.

2. Wählen Sie den Azure AD-Mandanten, in dem Ihre Anwendung gespeichert ist.

3. Klicken Sie auf **Benutzer** > **Benutzer hinzufügen**.

4. Weisen Sie im Dialogfeld **Benutzer hinzufügen** den Benutzer der globalen Administratorrolle zu.

Fügen Sie für Ihr Azure-Abonnement den Benutzer mit Administratorrechten als Co-Administrator hinzu.

1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-management-portal] an.

2. Klicken Sie auf **Einstellungen** und wählen Sie Ihr Azure-Abonnement.

3. Klicken Sie auf **Administratoren**.

4. Klicken Sie auf **Hinzufügen** am unteren Rand des Portals.

5. Geben Sie die E-Mail-Adresse des Benutzers mit Administratorrechten ein, den Sie zuvor erstellt haben.

6. Aktivieren Sie das Kontrollkästchen für das Abonnement.

7. Klicken Sie auf das Häkchen, um das Dialogfeld zu schließen.

![Hinzufügen eines Co-Administrators](media/guidance-multitenant-identity/co-admin.png)


### Einrichten eines Clientzertifikats

1. Führen Sie das PowerShell-Skript [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] wie folgt aus:
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    Geben Sie für den Parameter `Subject` einen Namen ein, z. B. „Surveysapp“. Das Skript generiert ein selbstsigniertes Zertifikat und speichert es im Zertifikatspeicher unter „Aktueller Benutzer/Eigene Zertifikate“.

2. Die Ausgabe des Skripts ist ein JSON-Fragment. Fügen Sie dieses wie folgt in das Anwendungsmanifest der Web-App ein:

    1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-management-portal] an, und navigieren Sie zu Ihrem Azure AD-Verzeichnis.

    2. Klicken Sie auf **Anwendungen**.

    3. Wählen Sie die Surveys-Anwendung.

    4.	Klicken Sie auf **Manifest verwalten** und wählen Sie **Manifest herunterladen**.

    5.	Öffnen Sie die JSON-Manifestdatei in einem Texteditor. Fügen Sie die Ausgabe des Skripts in die Eigenschaft `keyCredentials` ein. Es sollte in etwa wie folgt aussehen:
    ```
            "keyCredentials": [
                {
                  "type": "AsymmetricX509Cert",
                  "usage": "Verify",
                  "keyId": "29d4f7db-0539-455e-b708-....",
                  "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
                  "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
                }
              ],
    ```          
    6.	Speichern Sie die Änderungen an der JSON-Datei.

    7.	Gehen Sie zurück zum Portal. Klicken Sie auf **Manifest verwalten** > **Manifest hochladen** und laden Sie die JSON-Datei hoch.

3. Fügen Sie dasselbe JSON-Fragment in das Anwendungsmanifest der Web-API (Surveys.WebAPI) ein.

4. Führen Sie den folgenden Befehl aus, um den Fingerabdruck des Zertifikats abzurufen
    ```
    certutil -store -user my [subject]
    ```
    ,wobei `[subject]` der Wert ist, den Sie im PowerShell-Skript für Betreff angegeben haben. Der Fingerabdruck wird unter „Cert Hash(sha1)“ aufgeführt. Entfernen Sie die Leerzeichen zwischen den hexadezimalen Zahlen.

Sie werden den Fingerabdruck später benötigen.

### Erstellen eines Schlüsseltresors

1. Führen Sie das PowerShell-Skript [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] wie folgt aus:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, melden Sie sich mit den Informationen des Azure AD-Benutzers an, die Sie zuvor erstellt haben. Das Skript erstellt eine neue Ressourcengruppe und einen neuen Schlüsseltresor innerhalb dieser Ressourcengruppe.

    Hinweis: für den Parameter „-Location“ können Sie folgenden PowerShell-Befehl verwenden, um eine Liste der gültigen Regionen zu erhalten:

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. Führen Sie „SetupKeyVault.ps“ mit den folgenden Parametern erneut aus:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    Hierbei gilt:

    - Schlüsseltresorname = der Name, den Sie dem Schlüsseltresor im vorherigen Schritt zugewiesen haben
    - Web-App-Client-ID = die Client-ID für die Surveys-Webanwendung
    - Web-App-Client-ID = die Client-ID für die Surveys.WebAPI-Anwendung

    Beispiel:
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] Die Client-IDs erhalten Sie im [Azure-Verwaltungsportal][azure-management-portal]. Wählen Sie den Azure AD-Mandanten, wählen Sie die Anwendung und klicken Sie auf **Konfigurieren**.

    Dieses Skript autorisiert die Web-App und die Web-API, geheime Schlüssel aus Ihrem Schlüsseltresor abzurufen. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit Azure Key Vault][authorize-app].

### Hinzufügen von Konfigurationseinstellungen zu Ihrem Schlüsseltresor

1. Führen Sie „SetupKeyVault.ps“ wie folgt aus:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    Hierbei gilt:

    - Schlüsseltresorname = der Name, den Sie dem Schlüsseltresor im vorherigen Schritt zugewiesen haben
    - Redis-DNS-Name = der DNS-Name Ihrer Redis Cache-Instanz
    - Redis-Zugriffsschlüssel = der Zugriffsschlüssel für Ihre Redis Cache-Instanz

    Dieser Befehl fügt einen geheimen Schlüssel zu Ihrem Schlüsseltresor hinzu. Der geheime Schlüssel ist ein Name/Wert-Paar plus ein Tag:

    -	Der Name des Schlüssels wird nicht von der Anwendung verwendet, er muss jedoch im Schlüsseltresor eindeutig sein.
    -	Der Wert ist der Wert der Konfigurationsoption, in diesem Fall der Redis-Verbindungszeichenfolge.
    -	Der Tag „ConfigKey“ enthält den Namen der Konfigurationsschlüssel.

2. Jetzt sollten Sie überprüfen, ob die geheimen Schlüssel erfolgreich im Schlüsseltresor gespeichert wurden. Führen Sie den folgenden PowerShell-Befehl aus:

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    Das Ergebnis sollte den geheimen Wert und einige Metadaten anzeigen:

    ![PowerShell-Ausgabe](media/guidance-multitenant-identity/get-secret.png)

3. Führen Sie „SetupKeyVault.ps“ erneut aus, um die Datenbankverbindungszeichenfolge hinzufügen:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    wobei `<<DB connection string>>` der Wert der Datenbankverbindungszeichenfolge ist.

    Für Tests mit der lokalen Datenbank, kopieren Sie die Verbindungszeichenfolge aus der Datei „Tailspin.Surveys.Web/appsettings.json“. Stellen Sie dabei sicher, dass Sie den doppelten umgekehrten Schrägstrich („\\\“) in einen einzelnen umgekehrten Schrägstrich ändern. Der doppelte umgekehrte Schrägstrich ist ein Escapezeichen in der JSON-Datei.

    Beispiel:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### Entfernen Sie den Code, der Key Vault aktiviert.

1. Öffnen Sie die Projektmappe „Tailspin.Surveys“.

2. Suchen Sie in [Tailspin.Surveys.Web/Startup.cs][web-startup] den folgenden Codeblock, und heben Sie die Auskommentierung auf.

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. Suchen Sie in [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup] den folgenden Codeblock, und heben Sie die Auskommentierung auf.

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. In [Tailspin.Surveys.Web/Startup.cs][web-startup], suchen Sie den Code, der den `ICredentialService` registriert. Heben Sie die Auskommentierung der Zeile auf, die `CertificateCredentialService` verwendet, und kommentieren Sie die Zeile aus, die `ClientCredentialService` verwendet:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    Durch diese Änderung kann die Web-App [Client-Assertion][client-assertion] verwenden, um OAuth-Zugriffstoken abzurufen. Mit Client-Assertion benötigen Sie keinen OAuth-Clientschlüssel. Alternativ könnten Sie den Clientschlüssel im Schlüsseltresor speichern. Allerdings nutzen der Schlüsseltresor und Client-Assertion beide ein Clientzertifikat. Wenn Sie also den Schlüsseltresor aktivieren, empfiehlt es sich, auch Client-Assertion zu aktivieren.

### Aktualisierung der geheimen Benutzerschlüssel

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Tailspin.Surveys.Web-Projekt, und wählen Sie **Benutzerschlüssel verwalten**. Löschen Sie in der Datei „secrets.json“ die vorhandene JSON-Datei, und fügen Sie Folgendes hinzu:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

Ersetzen Sie die Einträge in [eckigen Klammern] durch die korrekten Werte.

- `AzureAd:ClientId`: die Client-ID der Surveys-App
- `AzureAd:WebApiResourceId`: der App-ID-URI, den Sie angegeben haben, als Sie die Surveys.WebAPI-Anwendung in Azure AD erstellt haben
- `Asymmetric:CertificateThumbprint`: der Fingerabdruck des Zertifikats, den Sie erhalten haben, als Sie das Clientzertifikat erstellt haben
- `KeyVault:Name`: der Name des Ihres Schlüsseltresors

> [AZURE.NOTE] `Asymmetric:ValidationRequired` ist „false“, da das Zertifikat, das Sie zuvor erstellt haben, nicht von einer Stammzertifizierungsstelle signiert wurde. Verwenden Sie in der Produktion ein Zertifikat, das von einer Stammzertifizierungsstelle signiert wurde, und ändern Sie `ValidationRequired` auf „true“.

Speichern Sie die aktualisierte secrets.json-Datei.

Klicken Sie als nächstes im Projektmappen-Explorer mit der rechten Maustaste auf das Tailspin.Surveys.WebApi-Projekt, und wählen Sie **Geheime Benutzerschlüssel verwalten**. Löschen Sie die vorhandene JSON-Datei, und fügen Sie Folgendes hinzu:

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

Ersetzen Sie die Einträge in [eckigen Klammern], und speichern Sie die Datei „secrets.json“.

> [AZURE.NOTE] Stellen Sie für die Web-API sicher, dass Sie die Client-ID für die Surveys.WebAPI-Anwendung verwenden, und nicht für die Surveys-Anwendung.


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[Schlüsseltags]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[Teil einer Serie]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[Beispielanwendung]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->