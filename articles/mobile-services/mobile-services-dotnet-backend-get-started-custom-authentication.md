<properties
	pageTitle="Erste Schritte mit der benutzerdefinierten Authentifizierung | Microsoft Azure"
	description="Erfahren Sie, wie Sie Benutzer mit einem Benutzernamen und einem Kennwort authentifizieren."
	documentationCenter="Mobile"
	authors="mattchenderson"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="mahender"/>

# Erste Schritte mit der benutzerdefinierten Authentifizierung

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


## Übersicht
Dieses Thema zeigt, wie Benutzer im Azure Mobile Services .NET-Back-End mithilfe eines von Ihnen herausgegebenen Mobile Services Authentifizierungstokens authentifiziert werden. In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe eines benutzerdefinierten Benutzernamens und Kennworts zum Schnellstartprojekt hinzu.

>[AZURE.NOTE] Dieses Lernprogramm erläutert eine erweiterte Methode zur Authentifizierung von Mobile Services mit benutzerdefinierten Anmeldeinformationen. Viele Apps eignen sich stattdessen am besten für die Verwendung des integrierten Social-Identitätsanbieters, sodass sich Benutzer über Facebook, Twitter, Google, ein Microsoft-Konto und Azure Active Directory anmelden können. Falls Sie noch keine Erfahrung mit Authentifizierung in Mobile Services haben, sollten Sie das Lernprogramm [Hinzufügen von Authentifizierung zur App] bearbeiten.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

>[AZURE.IMPORTANT] Mit diesem Lernprogramm wird gezeigt, wie man einen Authentifizierungstoken für Mobile Services herausgibt. Dies ist nicht als Sicherheitsanleitung zu verstehen. Bei der Entwicklung einer App müssen Sie sich der Sicherheitsauswirkungen einer Kennwortspeicherung bewusst sein und eine Strategie für den Umgang mit Brute-Force-Angriffen haben.

## Einrichten der Kontentabelle

Weil Sie eine benutzerdefinierte Authentifizierung verwenden und sich nicht auf einen anderen Identitätsanbieter stützen, müssen Sie die Anmeldeinformationen von Benutzern speichern. In diesem Abschnitt werden Sie eine Tabelle für Ihre Konten erstellen und die grundlegenden Sicherheitsmechanismen einrichten. Die Kontentabelle enthält die Benutzernamen und Kennwörter, die salted und gehasht sind, und Sie können bei Bedarf auch weitere Benutzerinformationen einschließen.

1. Erstellen Sie im Ordner **DataObjects** des Back-End-Projekts eine neue Entität namens `Account`.

2. Fügen Sie die folgende `using`-Anweisung hinzu:

		using Microsoft.WindowsAzure.Mobile.Service;

3. Ersetzen Sie die Klassendefinition durch den folgenden Code:

	    public class Account : EntityData
	    {
	        public string Username { get; set; }
	        public byte[] Salt { get; set; }
	        public byte[] SaltedAndHashedPassword { get; set; }
	    }

    Dies stellt eine Zeile in einer neuen Tabelle namens Account dar, die den Benutzernamen, die salt-Zeichenfolge des Benutzers und das auf sichere Weise gespeicherte Kennwort enthält.

2. Im Ordner **Modelle** finden Sie eine **DbContext**-Klasse, die nach dem mobilen Dienst benannt ist. Öffnen Sie Ihren Kontext und fügen Sie die Kontentabelle Ihrem Datenmodell hinzu, indem Sie Folgendes aufnehmen:

        public DbSet<Account> Accounts { get; set; }

	>[AZURE.NOTE]In den Codeausschnitten in diesem Lernprogramm wird `todoContext` als Kontextname verwendet. Sie müssen die Codeausschnitte für den Kontext Ihres Projekts aktualisieren. Als Nächstes richten Sie die Sicherheitsfunktionen für die Arbeit mit diesen Daten ein.

5. Erstellen Sie eine Klasse mit dem Namen `CustomLoginProviderUtils`, und fügen Sie ihr die folgende `using`-Anweisung hinzu:

		using System.Security.Cryptography;

6. Fügen Sie der neuen Klasse die folgenden Methoden hinzu:


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }

	Damit können Sie ein neues, langes Salt generieren, den Hashwert des salted Kennworts erzeugen und zwei Hashwerte auf sichere Weise vergleichen.

## Erstellen des Registrierungssendpunkts

Sie haben an diesem Punkt alles Nötige, um Benutzerkonten anlegen zu können. In diesem Abschnitt richten Sie einen Registrierungsendpunkt ein, der neue Registrierungsanfragen bearbeitet. Hier setzen Sie neue Regeln für Benutzername und Kennwort durch und stellen sicher, dass der Benutzername noch nicht vergeben ist. Dann speichern Sie sicher die Benutzerinformation in der Datenbank.

1. Erstellen Sie die folgende neue Klasse, die einen eingehenden Registrierungserversuch darstellt:

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Wenn weitere Informationen während der Registrierung erfasst und gespeichert werden sollen, sollten Sie dies hier tun.

2. Klicken Sie im Back-End-Projekt für den mobilen Dienst mit der rechten Maustaste auf **Controller**, klicken Sie auf **Hinzufügen** und **Controller**, erstellen Sie einen neuen **Microsoft Azure Mobile Services benutzerdefinierten Controller** mit dem Namen `CustomRegistrationController`, und fügen Sie dann die folgenden `using`-Anweisungen hinzu:

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Text.RegularExpressions;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

	Ersetzen Sie den Platzhalter im obigen Code durch den Namespace des Projekts.

4. Ersetzen Sie die Klassendefinition durch den folgenden Code:

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomRegistrationController : ApiController
	    {
	        public ApiServices Services { get; set; }

	        // POST api/CustomRegistration
	        public HttpResponseMessage Post(RegistrationRequest registrationRequest)
	        {
	            if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
	            }
	            else if (registrationRequest.password.Length < 8)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
	            }

	            todoContext context = new todoContext();
	            Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "That username already exists.");
	            }
	            else
	            {
	                byte[] salt = CustomLoginProviderUtils.generateSalt();
	                Account newAccount = new Account
	                {
	                    Id = Guid.NewGuid().ToString(),
	                    Username = registrationRequest.username,
	                    Salt = salt,
	                    SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
	                };
	                context.Accounts.Add(newAccount);
	                context.SaveChanges();
	                return this.Request.CreateResponse(HttpStatusCode.Created);
	            }
	        }
	    }

    Vergessen Sie nicht, die *todoContext*-Variable durch den Namen Ihres Projekts zu ersetzen: **DbContext**. Beachten Sie, dass dieser Controller das folgende Attribut verwendet, um sämtlichen Datenverkehr an diesen Endpunkt zuzulassen:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]Auf diesen Registrierungsendpunkt kann von jedem Client über HTTP zugegriffen werden. Bevor Sie diesen Dienst in einer Produktionsumgebung veröffentlichen, sollten Sie ein Schema zum Überprüfen der Registrierungen implementieren, z. B. SMS-basierte oder E-Mail-basierte Überprüfung. Damit können Sie verhindern, dass ein böswilliger Benutzer betrügerische Registrierungen erstellt.

## Erstellen des LoginProvider

Eines der fundamentalen Konstrukte in der Mobile Services-Authentifizierungspipeline ist der **LoginProvider**. In diesem Abschnitt erstellen Sie Ihren eigenen `CustomLoginProvider`. Er wird nicht in die Pipeline eingebaut wie die integrierten Anbieter, sondern ermöglicht Ihnen ein paar praktische Funktionen. Bei Verwendung von Visual Studio 2013 müssen Sie unter Umständen das `WindowsAzure.MobileServices.Backend.Security`-NuGet-Paket installieren, um die Verweise auf die `LoginProvider`-Klasse hinzuzufügen.

1. Erstellen Sie eine neue Klasse namens `CustomLoginProvider`, die von **LoginProvider** abgeleitet ist, und fügen Sie die folgenden `using`-Anweisungen hinzu:

	    using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Newtonsoft.Json.Linq;
		using Owin;
		using System.Security.Claims;

3. Ersetzen Sie die **CustomLoginProvider**-Klassendefinition durch den folgenden Code:

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       Wenn Sie versuchen, das Projekt zu erstellen, schlägt dies fehl. `LoginProvider` verfügt über drei abstrakte Methoden, die Sie implementieren müssen. Sie werden dies später tun.

2. Erstellen Sie eine neue Klasse namens `CustomLoginProviderCredentials` in derselben Codedatei.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

	Diese stellt Informationen über den Benutzer dar und wird dem Back-End über [GetIdentitiesAsync](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.serviceuser.getidentitiesasync.aspx) verfügbar gemacht. Wenn Sie benutzerdefinierte Ansprüche hinzufügen, achten Sie darauf, das sie in diesem Objekt erfasst sind.

3. Fügen Sie **CustomLoginProvider** die folgende Implementierung der abstrakten Methode `ConfigureMiddleware` hinzu.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

	Diese Methode ist nicht implementiert, da **CustomLoginProvider** nicht in die Authentifizierungspipeline integriert wird.

4. Fügen Sie **CustomLoginProvider** die folgende Implementierung der abstrakten Methode `ParseCredentials` hinzu.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

	Diese Methode ermöglicht es dem Back-End, Benutzerinformationen von einem eingehenden Authentifizierungstoken zu deserialisieren.

5. Fügen Sie **CustomLoginProvider** die folgende Implementierung der abstrakten Methode `CreateCredentials` hinzu.

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

	Diese Methode übersetzt eine [ClaimsIdentity] in ein [ProviderCredentials]-Objekt, das in der Ausstellungsphase des Authentifizierungstoken verwendet wird. Es empfiehlt sich wieder, in dieser Methode alle etwaigen zusätzlichen Ansprüche zu erfassen.

6. Öffnen Sie die Projektdatei "WebApiConfig.cs" im Ordner "App\_Start". Die folgende Codezeile wird nach **ConfigOptions** erstellt:

		options.LoginProviders.Add(typeof(CustomLoginProvider));



## Erstellen des Anmeldungsendpunkts

Als Nächstes erstellen Sie einen Endpunkt für die Benutzeranmeldung. Der Benutzername und das Kennwort, das Sie erhalten, werden anhand der Datenbank geprüft. Dabei wird zuerst das Salt des Benutzers angewandt, der Hashwert des Kennworts generiert und sichergestellt, dass der eingehende Wert dem der Datenbank entspricht. Ist das der Fall, dann können Sie eine [ClaimsIdentity] erstellen und diese an den **CustomLoginProvider** weitergeben. Die Client-App erhält dann eine Benutzer-ID und einen Authentifizierungstoken für den weiteren Zugriff auf Ihren mobilen Dienst.

1. Im Back-End-Projekt für den mobilen Dienst erstellen Sie die folgende neue `LoginRequest`-Klasse:

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

	Diese Klasse stellt einen eingehenden Anmeldeversuch dar.

2. Erstellen Sie die folgende neue `CustomLoginResult`-Klasse:

	    public class CustomLoginResult
	    {
	        public string UserId { get; set; }
	        public string MobileServiceAuthenticationToken { get; set; }

	    }

	Diese Klasse stellt eine erfolgreiche Anmeldung mit der Benutzer-ID und dem Authentifizierungstoken dar. Beachten Sie, dass diese Klasse die gleiche Form wie die MobileServiceUser-Klasse auf dem Client das, was die Weitergabe der Anmeldung an einen stark typisierten Client erleichtert.

2. Klicken Sie der rechten Maustaste auf **Controller**, klicken Sie auf **Hinzufügen** und **Controller**, erstellen Sie einen neuen **Microsoft Azure Mobile Services benutzerdefinierten Controller** mit dem Namen `CustomLoginController`, und fügen Sie dann die folgenden `using`-Anweisungen hinzu:

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Security.Claims;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

3. Ersetzen Sie die **CustomLoginController**-Klassendefinition durch den folgenden Code:

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomLoginController : ApiController
	    {
	        public ApiServices Services { get; set; }
	        public IServiceTokenHandler handler { get; set; }

	        // POST api/CustomLogin
	        public HttpResponseMessage Post(LoginRequest loginRequest)
	        {
	            todoContext context = new todoContext();
	            Account account = context.Accounts
	                .Where(a => a.Username == loginRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                byte[] incoming = CustomLoginProviderUtils
	                    .hash(loginRequest.password, account.Salt);

	                if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
	                {
	                    ClaimsIdentity claimsIdentity = new ClaimsIdentity();
	                    claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
	                    LoginResult loginResult = new CustomLoginProvider(handler)
	                        .CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
	                    var customLoginResult = new CustomLoginResult()
	                    {
	                        UserId = loginResult.User.UserId,
	                        MobileServiceAuthenticationToken = loginResult.AuthenticationToken
	                    };
	                    return this.Request.CreateResponse(HttpStatusCode.OK, customLoginResult);
	                }
	            }
	            return this.Request.CreateResponse(HttpStatusCode.Unauthorized,
	                "Invalid username or password");
	        }
	    }

       Vergessen Sie nicht, die *todoContext*-Variable durch den Namen Ihres Projekts zu ersetzen: **DbContext**. Beachten Sie, dass dieser Controller das folgende Attribut verwendet, um sämtlichen Datenverkehr an diesen Endpunkt zuzulassen:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT] Ihr `CustomLoginController` für die Produktion sollte außerdem eine Strategie zur Erkennung von Brute-Force-Angriffen enthalten. Ansonsten kann Ihre Anmeldelösung anfällig für Angriffe sein.

## Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## Testen des Anmeldeablaufs mit dem Testclient

Sie müssen in Idrer Client-Anwendung einen angepassten Anmeldebildschirm definieren, der Benutzernamen und Kennwörter als JSON-Nutzlast an die Registrierungs- und Anmeldungsendpunkte schickt. Für das Abschließen dieses Lernprogramms verwenden Sie aber stattdessen den integrierten Testclient für Mobile Services .NET-Back-End.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt für den mobilen Dienst, und klicken Sie dann auf **Debuggen** und **Neue Instanz starten**.

	Dadurch wird eine neue Debuginstanz des Back-End-Projekts für den mobilen Dienst gestartet. Nachdem der Dienst erfolgreich gestartet wurde, sehen Sie eine Startseite mit der Meldung **Dieser mobile Dienst wird nun ausgeführt**.

2. Klicken Sie auf der Startseite des Diensts auf **Ausprobieren**, geben Sie das Kennwort, die Sie zum Festlegen der App-Einstellung **MS\_ApplicationKey** in der Datei "web.config" mit einem leeren Benutzernamen festgelegt haben, in das Dialogfeld "Authentifizierung" ein.

3. Klicken Sie auf der Hilfeseite auf den **CustomRegistration**-Endpunkt, klicken Sie dann auf **Ausprobieren**.

    ![][2]

4. Ersetzen Sie im Text die Beispielzeichenfolgen durch einen Benutzernamen und ein Kennwort, die den von Ihnen vorher festgelegten Kriterien entsprechen, und klicken Sie dann auf **Senden**.

    ![][3]

	Die Antwort sollte **201/Created** lauten.

5. Klicken Sie im Browser auf die Schaltfläche "Zurück", und wiederholen Sie die Schritte 2 und 3 für den **CustomLogin**-Endpunkt, wobei Sie wieder den Benutzernamen und das Kennwort, die Sie im vorherigen Schritt registriert haben, verwenden.

    ![][4]

	Sie sollten eine Antwortnachricht mit einem Text empfangen, der ein JSON-Objekt namens **user** mit *userId* und *authenticationToken* enthält, wobei Letzteres das von der benutzerdefinierte Authentifizierung generierte Mobile Services-Authentifizierungstoken ist. Dieses Token ist ausreichend, um den Client Zugriff auf den TodoItem-Endpunkt zu gewähren.

	Erstellen Sie eine Kopie des *authenticationToken*-Werts. Sie verwenden diesen Wert, um auf den eingeschränkten TodoItem-Endpunkt zuzugreifen.

6. Klicken Sie im Browser auf die Schaltfläche "Zurück", und klicken Sie dann auf der API-Dokumentationsseite auf **GetTables** und anschließend auf **Ausprobieren **.

7. Klicken Sie im Dialogfeld für die GET-Anforderung auf das Pluszeichen neben **Header**, geben Sie den Wert `X-ZUMO-AUTH` in das linke Feld ein, fügen Sie den kopierten *authenticationToken*-Wert in das Feld rechts ein, und klicken Sie auf **Senden**.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-endpoint.png)

	Der mobile Dienst sollte Zugriff auf den Endpunkt gewähren und den Status **200/OK** sowie eine Liste der TodoItems in der Tabelle zurückgeben.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-success.png)

>[AZURE.IMPORTANT] Wenn Sie dieses Projekt für den mobilen Dienst auch in Azure veröffentlichen möchten, zum testen möchten, denken Sie daran, dass die Anmeldungs- und Authentifizierungsanbieter anfällig für Angriffe sind. Stellen Sie sicher, dass sie entweder mit entsprechenden verstärkten Sicherheitsfunktionen ausgestattet werden oder dass die zu schützenden Testdaten nicht für Sie wichtig sind. Gehen Sie äußerst vorsichtig vor, wenn Sie ein benutzerdefiniertes Authentifizierungsschema zum Sichern einer Produktionsumgebung verwenden möchten.

## Anmelden unter Verwendung der benutzerdefinierten Authentifizierung vom Client

In diesem Abschnitt werden die Schritte beschrieben, die Sie ausführen müssen, um vom Client auf benutzerdefinierte Authentifizierungsendpunkte zuzugreifen, um das zum Zugriff auf den mobilen Dienst benötigte Authentifizierungstoken zu erhalten. Da von Ihrem Client abhängt, welcher Clientcode im Einzelnen benötigt wird, ist die hier bereitgestellte Anleitung plattformunabhängig.

>[AZURE.NOTE] Die Mobile Services-Clientbibliotheken kommunizieren über HTTPS mit dem Dienst. Da diese Lösung erfordert, dass Kennwörter als Klartext gesendet werden, müssen Sie sicherstellen, dass Sie beim Aufrufen dieser Endpunkt mithilfe von direkten REST-Anforderungen HTTPS verwenden.

1. Erstellen Sie die erforderliche Benutzeroberflächenelemente in Ihrer Clientanwendung, damit die Benutzer einen Benutzernamen und ein Kennwort eingeben können.

2. Verwenden Sie die entsprechende **invokeApi**-Methode für **mobileServiceClient** in der Clientbibliothek, um den **CustomRegistration**-Endpunkt aufzurufen, und übergeben Sie dabei im Nachrichtentext die zur Laufzeit bereitgestellten Angaben für Benutzername und Kennwort.

	Sie müssen den **CustomRegistration**-Endpunkt nur einmal aufrufen, um ein Konto für einen bestimmten Benutzer zu erstellen, sofern Sie die Anmeldeinformationen des Benutzers in der Tabelle Accounts speichern. Beispiele zum Aufrufen einer benutzerdefinierten API auf den verschiedenen unterstützten Clientplattformen finden Sie im Artikel [Benutzerdefinierte API in Azure Mobile Services – Client SDKs](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

	> [AZURE.IMPORTANT] Da dieser Schritt für die Benutzerbereitstellung nur einmal vorkommt, sollten Sie das Benutzerkonto auf irgendeine Weise extern erstellen. Für einen öffentlichen Registrierungsendpunkt sollten Sie die Implementierung einer SMS- oder e-Mail-basierten Überprüfung oder andere Schutzmaßnahmen in Betracht ziehen, um zu verhindern, dass Konten in betrügerischer Absicht generiert werden. Sie können die Twilio zum Senden von SMS-Nachrichten von Mobile Services verwenden. Sie können auch SendGrid verwenden, um E-Mail-Nachrichten von Mobile Services aus zu senden. Weitere Informationen zum Verwenden von SendGrid finden Sie unter [Senden von E-Mails in Mobile Services mit SendGrid](store-sendgrid-mobile-services-send-email-scripts.md).

3. Verwenden Sie die entsprechende **invokeApi** Methode erneut, um den **CustomLogin**-Endpunkt aufzurufen, und übergeben Sie die zur Laufzeit bereitgestellten Angaben für Benutzername und Kennwort im Nachrichtentext.

	Diesmal müssen Sie erfassen die Werte für *userId* und *authenticationToken* erfassen, die nach einer erfolgreichen Anmeldung im Antwortobjekt zurückgegeben werden.

4. Erstellen Sie unter Verwendung der zurückgegebenen Werte für *userId* und *authenticationToken* ein neues **MobileServiceUser**-Objekt, und legen Sie es als den aktuellen Benutzer für die **MobileServiceClient**-Instanz fest, wie im Thema [Hinzufügen von Authentifizierung zur vorhandenen Anwendung](mobile-services-dotnet-backend-ios-get-started-users.md) beschrieben. Da das CustomLogin-Ergebnis das gleiche Format wie das **MobileServiceUser**-Objekt hat, sollten Sie das Ergebnis direkt in den Objekttyp umwandeln können.

Damit ist dieses Lernprogramm abgeschlossen.


<!-- Anchors. -->


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Hinzufügen von Authentifizierung zur App]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

[ClaimsIdentity]: https://msdn.microsoft.com/library/system.security.claims.claimsidentity(v=vs.110).aspx
[ProviderCredentials]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.providercredentials.aspx

<!---HONumber=AcomDC_0211_2016-->