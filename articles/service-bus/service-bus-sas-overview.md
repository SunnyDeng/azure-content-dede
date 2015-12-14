<properties
   pageTitle="SAS (Shared Access Signatures, freigegebene Zugriffssignaturen) – Übersicht | Microsoft Azure"
   description="Informieren Sie sich über SAS, deren Funktionsweise und die Verwendung in Node, PHP und C#."
   services="service-bus,event-hubs"
   documentationCenter="na"
   authors="djrosanova"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/04/2015"
   ms.author="darosa"/>

# Shared Access Signatures

*SAS (Shared Access Signatures)* sind der primäre Sicherheitsmechanismus für Service Bus mit Event Hubs, Brokermessaging (Warteschlangen und Themen) und Relaymessaging. Dieser Artikel beschreibt SAS, ihre Funktionsweise und die plattformunabhängige Verwendung.

## Übersicht über SAS

SAS (Shared Access Signatures) sind ein Authentifizierungsmechanismus, der auf sicheren Hashes (SHA-256) oder URIs basiert. SAS ist äußerst leistungsstark und wird von allen Service Bus-Diensten verwendet. In der Praxis bestehen SAS aus zwei Komponenten: einer *SAS-Richtlinie* und einer *SAS*(häufig auch als *Token* bezeichnet).

Ausführlichere Informationen zu SAS mit Service Bus finden Sie unter [SAS-Authentifizierung mit Service Bus](service-bus-shared-access-signature-authentication.md).

## SAS-Richtlinie

Bei SAS beginnt alles mit einer Richtlinie. Jede Richtlinie erfordert drei Angaben: **Name**, **Bereich** und**Berechtigungen**. Der Name ist ein eindeutiger Name innerhalb des Bereichs. Der Bereich ist der URI der betreffenden Ressource. Für einen Service Bus-Namespace wird als Bereich der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) angegeben (Beispiel: **`https://<yournamespace>.servicebus.windows.net/`**).

Die verfügbaren Berechtigungen für eine Richtlinie sind größtenteils selbsterklärend:

  + Senden
  + Empfangen
  + Verwalten

Nach Erstellung der Richtlinie werden ihr ein *Primärschlüssel* und ein *Sekundärschlüssel* zugewiesen. Hierbei handelt es sich um kryptografisch starke Schlüssel. Achten Sie darauf, dass sie Ihnen nicht abhanden kommen: Sie sind immer im [klassischen Azure-Portal][] verfügbar. Sie können einen beliebigen der generierten Schlüssel verwenden und die Schlüssel jederzeit erneut generieren. Wenn Sie allerdings den Primärschlüssel neu generieren oder in der Richtlinie ändern, werden alle auf deren Grundlage erstellten SAS ungültig.

Wenn Sie einen Service Bus-Namespace erstellen, wird automatisch eine Richtlinie namens **RootManageSharedAccessKey** für den gesamten Namespace erstellt. Diese Richtlinie verfügt über alle Berechtigungen. Da Sie sich nicht als **Root** anmelden, verwenden Sie diese Richtlinie nur, wenn ein wirklich guter Grund dafür vorliegt. Im Portal können Sie auf der Registerkarte **Konfigurieren** weitere Richtlinien für den Namespace konfigurieren. Beachten Sie, dass pro Ebene der Service Bus-Struktur (Namespace, Warteschlange, Event Hub usw.) maximal 12 Richtlinien zugeordnet werden können.

## SAS (Token)

Die Richtlinie selbst ist nicht das Zugriffstoken für Service Bus. Sie ist vielmehr das Objekt, auf dessen Grundlage das Zugriffstoken unter Verwendung des Primär- oder Sekundärschlüssels generiert wird. Zur Tokengenerierung wird eine Zeichenfolge im folgenden Format erstellt:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

`signature-string` ist der SHA-256-Hash des Tokenbereichs (**Bereich** im Sinne der Beschreibung aus dem vorherigen Abschnitt), gefolgt von einem CRLF und einer Ablaufzeit (in Sekunden seit der folgenden Epoche: `00:00:00 UTC` am 1. Januar 1970).

Der Hash ähnelt dem folgenden Pseudocode und gibt 32 Bytes zurück.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Die Werte ohne Hash befinden sich in der Zeichenfolge **SharedAccessSignature**, sodass der Empfänger den Hash mit den gleichen Parametern berechnen und somit sicherstellen kann, dass das gleiche Ergebnis zurückgegeben wird. Der URI gibt den Bereich und der Schlüsselname die Richtlinie an, der bzw. die bei der Hashberechnung verwendet werden soll. Dies ist ein wichtiger Sicherheitsaspekt. Stimmt die Signatur nicht mit dem Berechnungsergebnis des Empfängers (Service Bus) überein, wird der Zugriff verweigert. An diesem Punkt können wir sicher sein, dass der Absender Zugriff auf den Schlüssel hatte und ihm die in der Richtlinie angegebenen Rechte gewährt werden sollen.

## Generieren einer Signatur auf der Grundlage einer Richtlinie

In den folgenden Abschnitten erfahren Sie, wie Sie dies in der Praxis bewerkstelligen:

### NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time(); 	
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256', 			
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires . 		"&skn=" . $sasKeyName; 
return $token; 
}
```
 
### C&#35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## Verwenden einer SAS (auf HTTP-Ebene)
 
Nachdem Sie nun mit der SAS-Erstellung für beliebige Entitäten in Service Bus vertraut sind, können Sie einen Vorgang vom Typ „HTTP POST“ ausführen:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
Das funktioniert überall. Sie können SAS für eine Warteschlange, ein Thema, ein Abonnement, eine Event Hub-Instanz oder ein Relay erstellen. Wenn Sie eine Publisher-spezifische Identität für Event Hubs verwenden, fügen Sie einfach `/publishers/< publisherid>` an.

Wenn Sie einem Absender oder Client ein SAS-Token zuweisen, verfügt er nicht direkt über den Schlüssel, und er kann den Hash nicht umkehren und so den Schlüssel ermitteln. Dadurch haben Sie die Kontrolle darüber, worauf er wie lange Zugriff hat. Vergessen Sie nicht: Wenn Sie den Primärschlüssel neu generieren oder in der Richtlinie ändern, werden alle auf deren Grundlage erstellten SAS ungültig.

## Verwenden einer SAS (auf AMQP-Ebene)

Im vorherigen Abschnitt wurde gezeigt, wie Sie das SAS-Token mit einer HTTP POST-Anforderung zum Senden von Daten an den Service Bus verwenden. Wie Sie wissen, können Sie auf den Service Bus mit dem AMQ (Advanced Message Queue)-Protokoll zugreifen, das in zahlreichen Szenarien aus Leistungsgründen bevorzugt verwendet wird. Die Verwendung des SAS-Tokens mit AMQP wird im folgenden Dokument beschrieben: [Anspruchsbasierte Sicherheit mit AMQP Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc). Dieses Dokument ist seit 2013 eine funktionierende Entwurfsversion, die jedoch von Azure unterstützt wird.

Bevor Sie beginnen, Daten an den Service Bus zu senden, muss der Verleger das SAS-Token in einer AMQP-Nachricht an einen ordnungsgemäß definierten AMQP-Knoten mit dem Namen **"$cbs"** senden (dieser kann als spezielle Warteschlange betrachtet werden, die vom Dienst zum Abrufen und Überprüfen aller SAS-Token verwendet wird). Der Verleger muss das Feld **"ReplyTo"** in der AMQP-Nachricht angeben. Hierbei handelt es sich um den Knoten, den der Dienst für die Antwort an den Verleger mit dem Ergebnis der Tokenüberprüfung verwendet (einfaches Anforderungs-/Antwortmuster zwischen Verleger und Dienst). Dieser spontan erstellte Antwortknoten beschäftigt sich mit der dynamischen Erstellung von Remoteknoten (siehe AMQP 1.0-Spezifikation). Nachdem die Gültigkeit des SAS-Tokens überprüft wurde, kann der Verleger mit dem Senden von Daten an den Dienst beginnen.

In den folgenden Schritten erfahren Sie, wie das SAS-Token mit dem AMQ-Protokoll mithilfe der [AMQP.Net Lite](http://amqpnetlite.codeplex.com)-Bibliothek gesendet wird. Dies ist hilfreich, wenn beim Entwickeln mit C&#35; das offizielle Service Bus-SDK nicht verwendet werden kann (z. B. bei WinRT, .Net Compact Framework, .Net Micro Framework und Mono). Dank dieser nützlichen Bibliothek können Sie nachvollziehen, wie die anspruchsbasierte Sicherheit auf AMQP-Ebene funktioniert. Wie sie auf HTTP-Ebene funktioniert, haben Sie bereits gesehen (HTTP POST-Anforderung und SAS-Token werden im Autorisierungsheader gesendet). Machen Sie sich aber keine Sorgen! Wenn Sie nicht so genau über AMQP Bescheid wissen müssen, können Sie für .Net Framework-Anwendungen das offizielle Service Bus-SDK, das die erforderlichen Schritte für Sie ausführt, bzw. für alle anderen Plattformen die [Azure SB Lite](http://azuresblite.codeplex.com)-Bibliothek verwenden (siehe oben).

### C&#35;

```
/// <summary>
/// Send Claim Based Security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

Die oben genannte *PutCbsToken()*-Methode empfängt die *Verbindung* (von der AMQP .Net Lite-Bibliothek bereitgestellte AMQP-Verbindungsklasseninstanz), die die TCP-Verbindung mit dem Dienst darstellt, und den *sasToken*-Parameter, bei dem es sich um das zu sendende SAS-Token handelt. HINWEIS: Beim Erstellen der Verbindung muss der **SASL-Authentifizierungsmechanismus auf EXTERNAL** festgelegt werden (es darf nicht der Standardmechanismus PLAIN mit dem Benutzernamen und dem Kennwort genutzt werden, der bzw. das verwendet wird, wenn Sie das SAS-Token nicht senden müssen).

Im nächsten Schritt erstellt der Verleger zwei AMQP-Links zum Senden des SAS-Tokens und zum Empfangen der Antwort (Ergebnis der Tokenüberprüfung) vom Dienst.

Die AMQP-Nachricht ist etwas komplex. Sie enthält zahlreiche Eigenschaften und mehr Informationen als eine einfache Nachricht. Das SAS-Token wird als Text der Nachricht (mit dem entsprechenden Konstruktor) verwendet. Als **"ReplyTo"**-Eigenschaft wird der Knotenname zum Empfangen des Überprüfungsergebnisses über den Empfängerlink festgelegt (Sie können den Namen nach Bedarf ändern, der Link wird vom Dienst dynamisch erstellt). Anhand der letzten drei Anwendungseigenschaften/benutzerdefinierten Eigenschaften vollzieht der Dienst nach, welche Art von Vorgang ausgeführt werden soll. Wie in der CBS-Entwurfsspezifikation beschrieben, müssen hier der **Vorgangsname** (d. h. „put-token“), der verwendete **Tokentyp** (d. h. „servicebus.windows.net:sastoken“) und schließlich der **„Name“ der Zielgruppe** angegeben werden, für die das Token gilt (die gesamte Entität).

Nach dem Senden des SAS-Tokens über den Senderlink muss der Verleger die Antwort über den Empfängerlink lesen. Bei der Antwort handelt es sich um eine einfache AMQP-Nachricht mit der Anwendungseigenschaft **"status-code"**, die dieselben Werte wie ein HTTP-Statuscode enthalten kann.

## Nächste Schritte

Weitere Informationen zu den Verwendungsmöglichkeiten für diese SAS-Token finden Sie in der [Service Bus REST-API-Referenz](https://msdn.microsoft.com/library/azure/hh780717.aspx).

Weitere Informationen zur Service Bus-Authentifizierung finden Sie unter [Service Bus-Authentifizierung und -Autorisierung](service-bus-authentication-and-authorization.md).

Weitere Beispiele für SAS in C# und Java Script finden Sie in [diesem Blogbeitrag](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx) (in englischer Sprache).

[klassischen Azure-Portal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1203_2015-->