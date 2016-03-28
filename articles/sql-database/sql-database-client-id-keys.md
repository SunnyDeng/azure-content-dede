<properties
   pageTitle="Registrieren Ihrer App und Abrufen der Client-ID und des Schlüssels für die Verbindung mit der SQL-Datenbank aus dem Code | Microsoft Azure"
   description="Abrufen der Client-ID und des Schlüssels für den Zugriff auf die SQL-Datenbank aus dem Code."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="03/15/2016"
   ms.author="sstein"/>

# Abrufen der Client-ID und des Schlüssels für die Verbindung mit der SQL-Datenbank aus dem Code

Zum Erstellen und Verwalten der SQL-Datenbank über den Code müssen Sie Ihre App in der Azure Active Directory-Domäne (AAD) registrieren, die dem Abonnement zugeordnet ist, in dem die Azure-Ressourcen erstellt wurden. Wenn Sie Ihre Anwendung registrieren, generiert Azure eine Client-ID und einen Schlüssel, die im Code zum Authentifizieren der App erforderlich sind. Weitere Informationen finden Sie unter [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

## Registrieren einer nativen Clientanwendung und Abrufen der Client-ID

Führen Sie die folgenden Schritte aus, um eine neue Anwendung zu erstellen und zu registrieren:

1. Melden Sie sich beim [klassischen Portal](https://manage.windowsazure.com/) an (derzeit muss das Registrieren von Anwendungen im klassischen Portal ausgeführt werden).
1. Suchen Sie im Menü nach **Active Directory**, und wählen Sie diese Option aus.

    ![AAD][1]

2. Wählen Sie das Verzeichnis zum Authentifizieren Ihrer Anwendung aus, und klicken Sie auf seinen **Namen**.

    ![Verzeichnisse][4]

3. Klicken Sie auf der Verzeichnisseite auf **ANWENDUNGEN**.

    ![Anwendungen][5]

4. Klicken Sie auf **HINZUFÜGEN**, um eine neue Anwendung zu erstellen.

    ![Anwendung hinzufügen][6]

5. Geben Sie im Feld **NAME** einen Namen für die App ein, und wählen Sie **SYSTEMEIGENE CLIENTANWENDUNG** aus.

    ![Anwendung hinzufügen][7]

6. Geben Sie einen **UMLEITUNGS-URI** an. Es muss kein tatsächlicher Endpunkt sein, lediglich ein gültiger URI.

    ![Anwendung hinzufügen][8]

7. Schließen Sie das Erstellen der App ab, klicken Sie auf **KONFIGURIEREN**, und kopieren Sie die **CLIENT-ID** (diesen Wert benötigen Sie in Ihrem Code).

    ![Client-ID abrufen][9]


1. Scrollen Sie auf der Seite nach unten, und klicken Sie auf **Anwendung hinzufügen**.
1. Wählen Sie **Microsoft-Apps** aus.
1. Wählen Sie **Windows Azure-Service-Verwaltungs-API** aus, und schließen Sie dann den Assistenten ab.
2. Suchen Sie im Abschnitt **Berechtigungen für andere Anwendungen** nach **Windows Azure-Service-Verwaltungs-API**, und klicken Sie auf **Delegierte Berechtigungen**.
3. Wählen Sie **Auf Azure-Service-Verwaltung zugreifen** aus.

    ![Berechtigungen][2]

2. Klicken Sie unten auf der Seite auf **Speichern**.



## Registrieren einer Web-App (oder Web-API) und Abrufen der Client-ID und des Schlüssels

Um eine neue Anwendung zu erstellen und im richtigen Active Directory zu registrieren, führen Sie die folgenden Schritte aus:

1. Melden Sie sich beim [klassischen Portal](https://manage.windowsazure.com/) an.
1. Suchen Sie im Menü nach **Active Directory**, und wählen Sie diese Option aus.

    ![AAD][1]

2. Wählen Sie das Verzeichnis zum Authentifizieren Ihrer Anwendung aus, und klicken Sie auf seinen **Namen**.

    ![Verzeichnisse][4]

3. Klicken Sie auf der Verzeichnisseite auf **ANWENDUNGEN**.

    ![Anwendungen][5]

4. Klicken Sie auf **HINZUFÜGEN**, um eine neue Anwendung zu erstellen.

    ![Anwendung hinzufügen][6]

5. Geben Sie im Feld **NAME** einen Namen für die App ein, und wählen Sie **WEBANWENDUNG UND/ODER WEB-API** aus.

    ![Anwendung hinzufügen][10]

6. Geben Sie eine **ANMELDE-URL** und einen **APP-ID-URI** ein. Es muss kein tatsächlicher Endpunkt sein, lediglich ein gültiger URI.

    ![Anwendung hinzufügen][11]

7. Schließen Sie das Erstellen der App ab, und klicken Sie dann **KONFIGURIEREN**.

    ![Konfigurieren][12]

8. Scrollen Sie zum Abschnitt **Schlüssel**, und wählen Sie **1 Jahr** in der Liste **Dauer auswählen** aus. Der Schlüsselwert wird nach dem Speichern angezeigt. Daher kehren wir später zurück und kopieren den Schlüssel.

    ![Dauer für den Schlüssel festlegen][13]



1. Scrollen Sie auf der Seite nach unten, und klicken Sie auf **Anwendung hinzufügen**.
1. Wählen Sie **Microsoft-Apps** aus.
1. Suchen Sie nach **Windows Azure-Service-Verwaltungs-API**, und wählen Sie sie aus. Schließen Sie dann den Assistenten ab.
2. Suchen Sie im Abschnitt **Berechtigungen für andere Anwendungen** nach **Windows Azure-Service-Verwaltungs-API**, und klicken Sie auf **Delegierte Berechtigungen**.
3. Wählen Sie **Auf Azure-Service-Verwaltung zugreifen** aus.

    ![Berechtigungen][2]

2. Klicken Sie unten auf der Seite auf **Speichern**.
3. Wenn der Speichervorgang abgeschlossen ist, suchen und speichern Sie die CLIENT-ID und den Schlüssel:

    ![Geheime Schlüssel der Web-App][14]



## Ermitteln des Domänennamens

Der Domänenname ist manchmal für den Authentifizierungscode erforderlich. Es folgt eine einfache Möglichkeit zum Ermitteln des richtigen Domänennamens:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Zeigen Sie auf Ihren Namen in der oberen rechten Ecke, und notieren Sie sich die Domäne, die im Popupfenster angezeigt wird.

    ![Domänenname ermitteln][3]




## Beispielkonsolenanwendung


Rufen Sie die erforderlichen Verwaltungsbibliotheken ab, indem Sie über die [Paket-Manager-Konsole](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio (**Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**) die folgenden Pakete installieren:


    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


Erstellen Sie eine Konsolenanwendung mit dem Namen **SqlDbAuthSample**, und ersetzen Sie den Inhalt von **Program.cs** durch Folgendes:

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbAuthSample
    {
    class Program
    {
        static void Main(string[] args)
        {
            token = GetAccessTokenforNativeClient();
            // token = GetAccessTokenUsingUserCredentials(new UserCredential("<email address>"));
            // token = GetAccessTokenForWebApp();

            Console.WriteLine("Signed in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Press Enter to continue...");
            Console.ReadLine();
        }


        // authentication variables (native)
        static string nclientId = "<your client id>";
        static string nredirectUri = "<your redirect URI>";
        static string ndomainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        private static AuthenticationResult GetAccessTokenforNativeClient()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + ndomainName /* Tenant ID or AAD domain */);

            token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    nclientId,
            new Uri(nredirectUri),
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }


        // authentication variables (web)
        static string wclientId = "<your client id>";
        static string wkey = "<your key>";
        static string wdomainName = "<i.e. microsoft.onmicrosoft.com>";

        private static AuthenticationResult GetAccessTokenForWebApp()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + wdomainName /* Tenant ID or AAD domain */);

            ClientCredential cc = new ClientCredential(wclientId, wkey);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                cc);

            return token;
        }


        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + ndomainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                nclientId /* application client ID from AAD*/,
                new Uri(nredirectUri) /* redirect URI */,
                PromptBehavior.Auto,
                new UserIdentifier(userCredential.UserName, UserIdentifierType.RequiredDisplayableId));

            return token;
        }
    }
    }


Spezifische Codebeispiele für die Azure AD-Authentifizierung finden Sie im [SQL Server Security Blog](http://blogs.msdn.com/b/sqlsecurity/) (in englischer Sprache) auf MSDN.

## Weitere Informationen

- [Erstellen einer SQL-Datenbank mit C#](sql-database-get-started-csharp.md)
- [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md)



<!--Image references-->
[1]: ./media/sql-database-client-id-keys/aad.png
[2]: ./media/sql-database-client-id-keys/permissions.png
[3]: ./media/sql-database-client-id-keys/getdomain.png
[4]: ./media/sql-database-client-id-keys/aad2.png
[5]: ./media/sql-database-client-id-keys/aad-applications.png
[6]: ./media/sql-database-client-id-keys/add.png
[7]: ./media/sql-database-client-id-keys/add-application.png
[8]: ./media/sql-database-client-id-keys/add-application2.png
[9]: ./media/sql-database-client-id-keys/clientid.png
[10]: ./media/sql-database-client-id-keys/add-application-web.png
[11]: ./media/sql-database-client-id-keys/add-application-app-properties.png
[12]: ./media/sql-database-client-id-keys/configure.png
[13]: ./media/sql-database-client-id-keys/key-duration.png
[14]: ./media/sql-database-client-id-keys/web-secrets.png

<!---HONumber=AcomDC_0316_2016-->