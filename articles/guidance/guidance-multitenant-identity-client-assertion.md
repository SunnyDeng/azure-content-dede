<properties
   pageTitle="Abrufen von Zugriffstoken aus Azure AD mithilfe der Clientassertion | Microsoft Azure "
   description="Informationen zum Abrufen von Zugriffstoken aus Azure AD mithilfe der Clientassertion."
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

# Abrufen von Zugriffstoken aus Azure AD mithilfe der Clientassertion

Dieser Artikel ist [Teil einer Reihe]. Es gibt auch eine vollständige [Beispielanwendung], die diese Reihe begleitet.

## Hintergrund

Bei Verwendung des Autorisierungscode- oder Hybridvorgangs in OpenID Connect tauscht der Client einen Autorisierungscode gegen ein Zugriffstoken. In diesem Schritt muss der Client sich beim Server authentifizieren.

![Geheimer Clientschlüssel](media/guidance-multitenant-identity/client-secret.png)

Eine Möglichkeit zum Authentifizieren des Clients bietet ein geheimer Clientschlüssel. Die [Tailspin-Anwendung „Surveys“][Surveys] ist standardmäßig so konfiguriert.

In dieser Beispielanforderung fordert der Client vom Identitätsanbieter ein Zugriffstoken an. Beachten Sie den `client_secret`-Parameter.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

Der Schlüssel ist lediglich eine Zeichenfolge. Daher müssen Sie sicherstellen, dass diese nicht preisgegeben wird. Die bewährte Methode ist, den geheimen Clientschlüssel von der Quellcodeverwaltung getrennt zu speichern. Wenn die Bereitstellung in Azure erfolgt, speichern Sie den geheimen Schlüssel in einer [App-Einstellung][configure-web-app].

Alle Benutzer mit Zugriff auf das Azure-Abonnement können jedoch die App-Einstellungen anzeigen. Darüber hinaus besteht immer die Versuchung, geheime Schlüssel in der Quellcodeverwaltung (z. B. in Bereitstellungsskripts) einzuchecken, sie per E-Mail zu verteilen usw.

Zur Erhöhung der Sicherheit können Sie die _Clientassertion_ anstelle eines geheimen Clientschlüssels verwenden. Bei der Clientassertion verwendet der Client ein X.509-Zertifikat zum Nachweis, dass die Anforderung des Tokens vom Client stammt. Das Clientzertifikat ist auf dem Webserver installiert. Im Allgemeinen ist es einfacher, den Zugriff auf das Zertifikat zu beschränken, anstatt sicherzustellen, dass niemand versehentlich einen geheimen Clientschlüssel preisgibt.

Hier eine Tokenanforderung mit Clientassertion:

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

Beachten Sie, dass der `client_secret`-Parameter nicht mehr verwendet wird. In diesem Fall enthält der `client_assertion`-Parameter ein JWT-Token, das mit dem Clientzertifikat signiert wurde. Der `client_assertion_type`-Parameter gibt den Typ der Assertion an. In diesem Fall ist es ein JWT-Token. Der Server überprüft das JWT-Token. Wenn das JWT-Token ungültig ist, gibt die Tokenanforderung einen Fehler zurück.

> [AZURE.NOTE] X.509-Zertifikate sind nicht die einzige Form der Clientassertion. Wir konzentrieren uns hier darauf, da sie von Azure AD unterstützt wird.

## Verwenden der Clientassertion in der Tailspin-Anwendung „Surveys“

In diesem Abschnitt wird gezeigt, wie die Tailspin-Anwendung „Surveys“ für das Verwenden der Clientassertion konfiguriert wird. In den folgenden Schritten generieren Sie ein selbstsigniertes Zertifikat, das für die Entwicklung, aber nicht für die Produktion geeignet ist.

1. Führen Sie das PowerShell-Skript [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] wie folgt aus:

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    Geben Sie für den `Subject`-Parameter einen Namen ein, z. B. „surveysapp“. Das Skript generiert ein selbstsigniertes Zertifikat und speichert es im Zertifikatspeicher des aktuellen Benutzers bzw. im persönlichen Zertifikatspeicher.

2. Die Ausgabe des Skripts ist ein JSON-Fragment. Fügen Sie dieses wie folgt in das Anwendungsmanifest der Web-App ein:

    1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-management-portal] an, und navigieren Sie zu Ihrer Azure AD-Anwendung.

    2. Klicken Sie auf **Anwendungen**.

    3. Wählen Sie die Anwendung „Surveys“ aus.

    4.	Klicken Sie auf **Manifest verwalten**, und wählen Sie **Manifest herunterladen** aus.

    5.	Öffnen Sie die JSON-Manifestdatei in einem Texteditor. Fügen Sie die Ausgabe des Skripts in die `keyCredentials`-Eigenschaft ein. Es sollte in etwa wie folgt aussehen:

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

    7.	Gehen Sie zum Portal zurück. Klicken Sie auf **Manifest verwalten** > **Manifest hochladen**, und laden Sie die JSON-Datei hoch.

3. Führen Sie den folgenden Befehl aus, um den Fingerabdruck des Zertifikats abzurufen.

    ```
    certutil -store -user my [subject]
    ```

    `[subject]` ist der Wert, den Sie im PowerShell-Skript für „Subject“ angegeben haben. Der Fingerabdruck wird unter „Cert Hash(sha1)“ aufgeführt. Entfernen Sie die Leerzeichen zwischen den Hexadezimalzahlen.

4. Aktualisieren Sie Ihre geheimen App-Schlüssel. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt „Tailspin.Surveys.Web“, und wählen Sie **Geheime Benutzerschlüssel verwalten** aus. Fügen Sie unter „AzureAd“ für „Asymmetric“ einen Eintrag hinzu (siehe unten):

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    Sie müssen `ValidationRequired` auf „false“ festlegen, da das Zertifikat nicht von einer Stammzertifizierungsstelle signiert ist. Verwenden Sie in der Produktion ein Zertifikat, das von einer Stammzertifizierungsstelle signiert wurde, und legen Sie `ValidationRequired` auf „true“ fest.

    Löschen Sie außerdem den Eintrag für `ClientSecret`, da dieser bei einer Clientassertion nicht notwendig ist.

5. Suchen Sie in „Startup.cs“ den Code, der `ICredentialService` registriert. Heben Sie die Auskommentierung der Zeile auf, die `CertificateCredentialService` verwendet, und kommentieren Sie die Zeile aus, die `ClientCredentialService` verwendet:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

Zur Laufzeit liest die Webanwendung das Zertifikat aus dem Zertifikatspeicher. Das Zertifikat muss auf dem gleichen Computer wie die Web-App installiert sein.

## Zusätzliche Ressourcen

- [Verwenden von Zertifikaten in Azure Websites-Anwendungen][using-certs-in-websites]
- [RFC 7521][RFC7521]. Definiert den allgemeinen Mechanismus zum Senden einer Clientassertion.
- [RFC 7523][RFC7523]. Definiert, wie JWT-Token für die Clientassertion verwendet werden.


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[RFC7521]: https://tools.ietf.org/html/rfc7521
[RFC7523]: https://tools.ietf.org/html/rfc7523
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[Teil einer Reihe]: guidance-multitenant-identity.md
[Beispielanwendung]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->