<properties 
	pageTitle="Erste Schritte mit der benutzerdefinierten Authentifizierung | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Benutzer mit einem Benutzernamen und einem Kennwort authentifizieren." 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="mahender"/>

# Erste Schritte mit der benutzerdefinierten Authentifizierung

Dieses Thema zeigt, wie Benutzer im Azure Mobile Services .NET-Back-End mithilfe eines von Ihnen herausgegebenen Mobile Services Authentifizierungstokens authentifiziert werden. In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe eines benutzerdefinierten Benutzernamens und Kennworts zum Schnellstartprojekt hinzu.

>[AZURE.NOTE] Dieses Lernprogramm erläutert eine erweiterte Methode zur Authentifizierung von Mobile Services mit benutzerdefinierten Anmeldeinformationen. Viele Apps eignen sich stattdessen am besten für die Verwendung des integrierten Social-Identitätsanbieters, sodass sich Benutzer über Facebook, Twitter, Google, ein Microsoft-Konto und Azure Active Directory anmelden können. Falls Sie noch keine Erfahrung mit der Authentifizierung in Mobile Services haben, sollten Sie das Lernprogramm [Erste Schritte mit Benutzern] abschließen.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Einrichten der Kontentabelle]
2. [Erstellen des Registrierungsendpunkts]
3. [Erstellen des LoginProvider]
4. [Erstellen des Anmeldeendpunkts]
5. [Konfigurieren des mobilen Diensts zur Verwendung der Authentifizierung]
6. [Testen des Anmeldeablaufs mit dem Testclient]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen. 

>[AZURE.NOTE] Mit diesem Lernprogramm wird gezeigt, wie man einen Authentifizierungstoken für Mobile Services herausgibt. Dies ist nicht als Sicherheitsanleitung zu verstehen. Bei der Entwicklung einer App müssen Sie sich der Sicherheitsauswirkungen einer Kennwortspeicherung bewusst sein und eine Strategie für den Umgang mit Brute-Force-Angriffen haben.


## <a name="table-setup"></a>Einrichten der Kontentabelle

Weil Sie eine benutzerdefinierte Authentifizierung verwenden und sich nicht auf einen anderen Identitätsanbieter stützen, müssen Sie die Anmeldeinformationen von Benutzern speichern. In diesem Abschnitt werden Sie eine Tabelle für Ihre Konten erstellen und die grundlegenden Sicherheitsmechanismen einrichten. Die Kontentabelle enthält die Benutzernamen und Kennwörter, die salted und gehasht sind, und Sie können bei Bedarf auch weitere Benutzerinformationen einschließen.

1. Erstellen Sie im `DataObjects`-Ordner Ihres Back-End-Projekts eine neue Entität namens `Account`:

            public class Account : EntityData
            {
                public string Username { get; set; }
                public byte[] Salt { get; set; }
                public byte[] SaltedAndHashedPassword { get; set; }
            }
    
    Das stellt eine Zeile in unserer neuen Tabelle dar, und es enthält den Benutzernamen, das Salt des Benutzers und das sicher gespeicherte Kennwort.

2. Im Ordner `Models` finden Sie eine `DbContext`-Klasse, die nach Ihrem mobilen Dienst benannt ist. Der Rest dieses Lernprogramms verwendet `todoContext` als Beispiel, und Sie müssen die Codeausschnitte entsprechend ändern. Öffnen Sie Ihren Kontext und fügen Sie die Kontentabelle Ihrem Datenmodell hinzu, indem Sie Folgendes aufnehmen:

        public DbSet<Account> Accounts { get; set; }

3. Als Nächstes richten Sie die Sicherheitsfunktionen für die Arbeit mit diesen Daten ein. Sie benötigen Mittel, um ein neues, langes Salt zu generieren, die Möglichkeit, ein salted Kennwort zu hashen, und eine sichere Möglichkeit, zwei Hashs zu vergleichen. Erstellen Sie eine Klasse mit dem Namen `CustomLoginProviderUtils`, und fügen Sie ihr die folgenden Methoden hinzu:


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


## <a name="register-endpoint"></a>Erstellen des Registrierungsendpunkts

Sie haben an diesem Punkt alles Nötige, um Benutzerkonten anlegen zu können. In diesem Abschnitt richten Sie einen Registrierungsendpunkt ein, der neue Registrierungsanfragen bearbeitet. Hier setzen Sie neue Regeln für Benutzername und Kennwort durch und stellen sicher, dass der Benutzername noch nicht vergeben ist. Dann speichern Sie sicher die Benutzerinformation in der Datenbank.

1. Erstellen Sie ein Objekt, das einen eingehendes Registrierungsereignis darstellt:

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Wenn Sie bei der Registrierung andere Informationen erfassen wollen, können Sie diese hier einschließen.

1. Fügen Sie Ihrem Mobile Services Back-End-Projekt einen neuen benutzerdefinierten Controller namens CustomRegistrationController hinzu und fügen Sie Folgendes ein:

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
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Username already exists");
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

    Achten Sie darauf, dass Sie allen Traffic zu diesem Endpunkt erlauben, indem Sie den Controller wie folgt dekorieren:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

## <a name="login-provider"></a>Erstellen des LoginProvider

Eines der fundamentalen Konstrukte in der Mobile Services-Authentifizierungspipeline ist der `LoginProvider`. In diesem Abschnitt erstellen Sie Ihren eigenen `CustomLoginProvider`. Er wird nicht in die Pipeline eingebaut wie die integrierten Anbieter, sondern ermöglicht Ihnen ein paar praktische Funktionen.

1. Erstellen Sie eine neue Klasse namens `CustomLoginProvider`, die sich von `LoginProvider` ableitet:

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

       `LoginProvider` hat drei andere abstrakte Methoden, die Sie später implementieren werden.

2. Erstellen Sie eine neue Klasse namens `CustomLoginProviderCredentials`. Dies stellt Informationen über Ihren Benutzer dar und steht Ihnen im Back-End über `ServiceUser.getIdentitiesAsync()` zur Verfügung. Wenn Sie benutzerdefinierte Ansprüche hinzufügen, achten Sie darauf, das sie in diesem Objekt erfasst sind.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

3. Fügen Sie die folgende Implementierung der abstrakten Methode `ConfigureMiddleware` zu `CustomLoginProvider` hinzu. Diese Methode ist hier eine leere Methode (No-op-Methode), da `CustomLoginProvider` sich nicht in die Authentifizierungspipeline integriert.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

4. Fügen Sie die folgende Implementierung der abstrakten Methode `ParseCredentials` zu `CustomLoginProvider` hinzu. Diese Methode ermöglicht es dem Back-End, Benutzerinformationen von einem eingehenden Authentifizierungstoken zu deserialisieren.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }


5. Fügen Sie die folgende Implementierung der abstrakten Methode `CreateCredentials` zu `CustomLoginProvider` hinzu. Diese Methode übersetzt eine `ClaimsIdentity` in ein `ProviderCredentials`-Objekt, das in der Ausstellungsphase im Authentifizierungstoken verwendet wird. Es empfiehlt sich wieder, hier alle etwaigen zusätzlichen Ansprüche hier zu erfassen.

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

## <a name="login-endpoint"></a>Erstellen des Anmeldeendpunkts

Als Nächstes erstellen Sie einen Endpunkt für Ihren Benutzer, an dem er sich anmelden kann. Der Benutzername und das Kennwort, das Sie erhalten, wird anhand der Datenbank geprüft. Dabei wird zuerst das Salt des Benutzers angewandt, das Kennwort gehasht und sichergestellt, dass der eingehende Wert dem der Datenbank entspricht. Ist das der Fall, dann können Sie eine `ClaimsIdentity` erstellen und diese an den `CustomLoginProvider` weitergeben. Die Client-App erhält dann eine Benutzer-ID und einen Authentifizierungstoken für den weiteren Zugriff auf Ihren mobilen Dienst.

1. Erstellen Sie in Ihrem Mobile Services Back-End-Projekt ein Projekt, das einen eingehenden Anmeldeversuch repräsentiert:

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

1. Fügen Sie einen neuen benutzerdefinierten Controller namens `CustomLoginController` hinzu, und fügen Sie Folgendes ein:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomLoginController : ApiController
        {
            public ApiServices Services { get; set; }
            public IServiceTokenHandler handler { get; set; }

            // POST api/CustomLogin
            public HttpResponseMessage Post(LoginRequest loginRequest)
            {
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == loginRequest.username).SingleOrDefault();
                if (account != null)
                {
                    byte[] incoming = CustomLoginProviderUtils.hash(loginRequest.password, account.Salt);

                    if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
                    {
                        ClaimsIdentity claimsIdentity = new ClaimsIdentity();
                        claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
                        LoginResult loginResult = new CustomLoginProvider(handler).CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
                        return this.Request.CreateResponse(HttpStatusCode.OK, loginResult);
                    }
                }
                return this.Request.CreateResponse(HttpStatusCode.Unauthorized, "Invalid username or password");
            }
        }

    Achten Sie darauf, dass Sie allen Traffic zu diesem Endpunkt erlauben, indem Sie den Controller wie folgt dekorieren:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.NOTE] Ihr `CustomLoginController` für die Produktion sollte außerdem eine Strategie zur Brute-Force-Erkennung enthalten. Ansonsten kann Ihre Anmeldelösung anfällig für Angriffe sein.

## <a name="require-authentication"></a>Konfigurieren des mobilen Diensts zur Verwendung der Authentifizierung

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## <a name="test-login"></a>Testen des Anmeldeablaufs mit dem Testclient

Sie müssen in Ihrer Client-Anwendung einen angepassten Anmeldebildschirm definieren, der Benutzernamen und Kennwörter als JSON-Nutzlast an Ihre Registrierung und die Anmeldungsendpunkte schickt. Für das Abschließen dieses Lernprogramms verwenden Sie aber stattdessen den integrierten Testclient für Mobile Services .NET-Back-End.

>[AZURE.NOTE] Die Mobile Services-SDKs kommunizieren mit dem Dienst über HTTPS. Wenn Sie planen, an dieser Stelle über einen direkten REST-Aufruf auf diesen Endpunkt zuzugreifen, müssen Sie sicherstellen, dass HTTPS für das Ansprechen des mobilen Diensts verwendet wird, da Kennwörter im Klartext gesandt werden.

1. Starten Sie in Visual Studio eine Debugging-Instanz Ihres Mobile Services-Projekts, indem Sie mit der rechten Maustaste auf das Projekt klicken und **Debuggen->Neue Instanz starten** auswählen.

    ![][0]

2. Klicken Sie auf **Testen Sie es**.

    ![][1]

3. Wählen Sie den Registrierungsendpunkt. Sie sehen eine grundlegende Dokumentation für Ihre API. Klicken Sie auf **Testen Sie es**.

    ![][2]

4. Ersetzen Sie im Text die Beispiel-Zeichenfolgen mit Benutzernamen und Kennwort, die den von Ihnen vorher festgelegten Kriterien entsprechen. Klicken Sie dann auf **Senden**. Die Antwort sollte **201/Created** lauten.

    ![][3]

5. Wiederholen Sie diesen Prozess für Ihren Anmeldungsendpunkt. Nachdem Sie den von Ihnen registrierten Benutzernamen und das Kennwort gesandt haben, sollten Sie Ihre Benutzer-ID und einen Authentifizierungstoken erhalten.

    ![][4]


<!-- Anchors. -->
[Einrichten der Kontentabelle]: #table-setup
[Erstellen des Registrierungsendpunkts]: #register-endpoint
[Erstellen des LoginProvider]: #login-provider
[Erstellen des Anmeldeendpunkts]: #login-endpoint
[Konfigurieren des mobilen Diensts zur Verwendung der Authentifizierung]: #require-authentication
[Testen des Anmeldeablaufs mit dem Testclient]: #test-login


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Erste Schritte mit Benutzern]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started

<!--HONumber=42-->
