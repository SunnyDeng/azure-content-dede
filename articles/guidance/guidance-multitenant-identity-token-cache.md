<properties
   pageTitle="Zwischenspeichern von Zugangstoken in einer mehrmandantenfähigen Anwendung | Microsoft Azure"
   description="Informationen zum Zwischenspeichern von Zugriffstoken zum Aufrufen einer Back-End-Web-API."
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


# Zwischenspeichern von Zugangstoken in einer mehrmandantenfähigen Anwendung

Dieser Artikel ist [Teil einer Reihe]. Es gibt auch eine vollständige [Beispielanwendung], die diese Reihe begleitet.

Es ist relativ aufwendig, ein OAuth-Zugriffstokens abzurufen, da dafür eine HTTP-Anforderung an den Tokenendpunkt erforderlich ist. Daher ist es ratsam, Token nach Möglichkeit in einem Cache zwischenzuspeichern. Die [Azure AD Authentication Library][ADAL] (ADAL) speichert von Azure AD abgerufene Token automatisch zwischen, so auch Aktualisierungstoken.

Die ADAL bietet eine Standardimplementierung eines Tokencaches. Doch dieser Tokencache ist für native Client-Apps vorgesehen und für Web-Apps _nicht_ geeignet:

-	Es handelt sich um eine statische Instanz, die nicht threadsicher ist.
-	Eine Skalierung auf eine große Anzahl von Benutzern ist nicht möglich, da Token aller Benutzer in dasselbe Verzeichnis übertragen werden.
-	Eine gemeinsame Nutzung auf den Webservern einer Farm ist ebenfalls nicht möglich.

Stattdessen müssen Sie einen benutzerdefinierten Tokencache implementieren, der von der ADAL-Klasse `TokenCache` abgeleitet ist, sich aber für eine Serverumgebung eignet und den gewünschten Grad an Isolation zwischen Token verschiedener Benutzer bietet.

Die `TokenCache`-Klasse speichert ein Wörterbuch mit Token, das anhand von Aussteller, Ressource, Client-ID und Benutzer indiziert ist. Ein benutzerdefinierter Tokencache muss dieses Wörterbuch in einen Sicherungsspeicher schreiben, z. B. in einen Redis-Cache.

In der Tailspin-Anwendung „Surveys“ implementiert die `DistributedTokenCache`-Klasse den Tokencache. Diese Implementierung verwendet die [IDistributedCache][distributed-cache]-Abstraktion von ASP.NET Core 1.0. Deshalb können alle `IDistributedCache`-Implementierungen als Sicherungsspeicher verwendet werden.

-	Standardmäßig verwendet die App „Surveys“ einen Redis-Cache.
-	Bei einem Webserver mit Einzelinstanz können Sie den [In-Memory-Cache][in-memory-cache] von ASP.NET 1.0 Core nutzen. (Dies ist auch eine gute Wahl für die lokale Ausführung der App während der Entwicklung.)

> [AZURE.NOTE] Derzeit wird der Redis-Cache für .NET Core nicht unterstützt.

`DistributedTokenCache` speichert die Cachedaten als Schlüssel-Wert-Paare im Sicherungsspeicher. Der Schlüssel besteht aus Benutzer-ID und Client-ID, damit der Sicherungsspeicher separate Cachedaten für jede eindeutige Benutzer-Client-Kombination enthält.

![Tokencache](media/guidance-multitenant-identity/token-cache.png)

Der Sicherungsspeicher ist benutzerbezogen aufgeteilt. Für jede HTTP-Anforderung werden die Token für den jeweiligen Benutzer aus dem Sicherungsspeicher gelesen und in das `TokenCache`-Wörterbuch geladen. Wenn Redis als Sicherungsspeicher verwendet wird, nutzen alle Serverinstanzen in einer Serverfarm denselben Cache für Lese- und Schreibvorgänge. Dieser Ansatz lässt sich auf viele Benutzer skalieren.

## Verschlüsseln zwischengespeicherter Token

Token sind vertrauliche Daten, da sie den Zugriff auf Ressourcen eines Benutzers gewähren. (Darüber hinaus kann im Gegensatz zu einem Benutzerkennwort nicht bloß ein Hash des Tokens gespeichert werden.) Daher ist es wichtig, die Sicherheit von Token zu gewährleisten. Der Redis-gestützte Cache wird durch ein Kennwort geschützt. Doch wenn sich jemand das Kennwort verschafft, können alle zwischengespeicherten Zugriffstoken abgerufen werden. Aus diesem Grund verschlüsselt der `DistributedTokenCache` alle Elemente, die in den Sicherungsspeicher geschrieben werden. Die Verschlüsselung erfolgt mithilfe der [Datenschutz][data-protection]-APIs von ASP.NET Core 1.0.

> [AZURE.NOTE] Bei Bereitstellung in Azure Websites werden die Verschlüsselungsschlüssel im Netzwerkspeicher gesichert und auf allen VMs synchronisiert (siehe [Schlüsselverwaltung][key-management]). Bei Ausführung in Azure Websites werden Schlüssel standardmäßig nicht verschlüsselt. Doch Sie können die [Verschlüsselung mit einem X.509-Zertifikat aktivieren][x509-cert-encryption].


## „DistributedTokenCache“-Implementierung

Die [DistributedTokenCache][DistributedTokenCache]-Klasse leitet sich von der ADAL- Klasse [TokenCache][tokencache-class] ab.

Im Konstruktor erstellt die `DistributedTokenCache`-Klasse einen Schlüssel für den aktuellen Benutzer und lädt den Cache aus dem Sicherungsspeicher:

```csharp
public DistributedTokenCache(
    ClaimsPrincipal claimsPrincipal,
    IDistributedCache distributedCache,
    ILoggerFactory loggerFactory,
    IDataProtectionProvider dataProtectionProvider)
    : base()
{
    _claimsPrincipal = claimsPrincipal;
    _cacheKey = BuildCacheKey(_claimsPrincipal);
    _distributedCache = distributedCache;
    _logger = loggerFactory.CreateLogger<DistributedTokenCache>();
    _protector = dataProtectionProvider.CreateProtector(typeof(DistributedTokenCache).FullName);
    AfterAccess = AfterAccessNotification;
    LoadFromCache();
}
```

Der Schlüssel wird durch Verkettung von Benutzer-ID und Client-ID erstellt. Beide Angaben stammen aus den Ansprüchen im `ClaimsPrincipal` des Benutzers:

```csharp
private static string BuildCacheKey(ClaimsPrincipal claimsPrincipal)
{
    string clientId = claimsPrincipal.FindFirstValue("aud", true);
    return string.Format(
        "UserId:{0}::ClientId:{1}",
        claimsPrincipal.GetObjectIdentifierValue(),
        clientId);
}
```

Um zwischengespeicherte Daten zu laden, lesen Sie das serialisierte Blob aus dem Sicherungsspeicher und rufen `TokenCache.Deserialize` zum Konvertieren des Blobs in Cachedaten auf.

```csharp
private void LoadFromCache()
{
    byte[] cacheData = _distributedCache.Get(_cacheKey);
    if (cacheData != null)
    {
        this.Deserialize(_protector.Unprotect(cacheData));
    }
}
```

Wenn die ADAL auf den Cache zugreift, wird ein `AfterAccess`-Ereignis ausgelöst. Wenn sich die Daten im Cache geändert haben, ist die `HasStateChanged`-Eigenschaft „true“. Aktualisieren Sie in diesem Fall den Sicherungsspeicher, um die Änderung zu übernehmen, und legen Sie dann `HasStateChanged` auf „false“ fest.

```csharp
public void AfterAccessNotification(TokenCacheNotificationArgs args)
{
    if (this.HasStateChanged)
    {
        try
        {
            if (this.Count > 0)
            {
                _distributedCache.Set(_cacheKey, _protector.Protect(this.Serialize()));
            }
            else
            {
                // There are no tokens for this user/client, so remove the item from the cache.
                _distributedCache.Remove(_cacheKey);
            }
            this.HasStateChanged = false;
        }
        catch (Exception exp)
        {
            _logger.WriteToCacheFailed(exp);
            throw;
        }
    }
}
```

„TokenCache“ sendet zwei weitere Ereignisse:

- `BeforeWrite`. Wird aufgerufen, unmittelbar bevor die ADAL Daten in den Cache schreibt. Es dient zum Implementieren einer Strategie für Parallelität.
- `BeforeAccess`. Wird aufgerufen, unmittelbar bevor ADAL Daten aus dem Cache liest. Hier können Sie den Cache erneut laden, um die neueste Version abzurufen.

In unserem Fall haben wir entschieden, diese beiden Ereignisse nicht zu behandeln.

- Zu Parallelitätszwecken hat der letzte Schreibzugriff Vorrang. Das ist in Ordnung, weil Token für jede Benutzer-Client-Kombination unabhängig voneinander gespeichert werden. Es käme also nur zu einem Konflikt, wenn derselbe Benutzer zwei parallele Anmeldesitzungen gestartet hätte.
- Für Lesevorgänge wird der Cache bei jeder Anforderung geladen. Anforderungen sind kurzlebig. Wenn der Cache in diesem Zeitraum geändert wird, wählt die nächste Anforderung den neuen Wert.

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[data-protection]: https://docs.asp.net/en/latest/security/data-protection/index.html
[distributed-cache]: https://docs.asp.net/en/latest/fundamentals/distributed-cache.html
[DistributedTokenCache]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.TokenStorage/DistributedTokenCache.cs
[key-management]: https://docs.asp.net/en/latest/security/data-protection/configuration/default-settings.html
[in-memory-cache]: https://docs.asp.net/en/latest/fundamentals/caching.html
[tokencache-class]: https://msdn.microsoft.com/library/azure/microsoft.identitymodel.clients.activedirectory.tokencache.aspx
[x509-cert-encryption]: https://docs.asp.net/en/latest/security/data-protection/implementation/key-encryption-at-rest.html#x-509-certificate
[Teil einer Reihe]: guidance-multitenant-identity.md
[Beispielanwendung]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->