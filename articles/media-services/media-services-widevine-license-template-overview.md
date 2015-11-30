<properties 
	pageTitle="Übersicht über die Widevine-Lizenzvorlage" 
	description="Dieses Thema bietet einen Überblick über eine Widevine-Lizenzvorlage, mit der Widevine-Lizenzen konfiguriert werden können." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015"
	ms.author="juliako"/>

#Übersicht über die Widevine-Lizenzvorlage

##Übersicht

Azure Media Services ermöglicht jetzt das Konfigurieren und Anfordern von Widevine-Lizenzen. Wenn der Endbenutzer-Player versucht, Ihre durch Widevine geschützten Inhalte wiederzugeben, wird eine Anforderung zum Erwerben einer Lizenz an den Lizenzbereitstellungsdienst gesendet. Wenn der Lizenzdienst die Anforderung genehmigt, wird die Lizenz ausgegeben. Diese wird an den Client gesendet und kann zum Entschlüsseln und Wiedergeben des angegebenen Inhalts verwendet werden.

Die Widevine-Lizenzanforderung ist als JSON-Nachricht formatiert.

Beachten Sie, dass Sie die Möglichkeit haben, eine leere Nachricht ohne Werte nur mit „{}“ zu erstellen, und eine Lizenzvorlage mit allen Standardeinstellungen wird erstellt.

	{  
	   “payload”:“<license challenge>”,
	   “content_id”: “<content id>” 
	   “provider”: ”<provider>”
	   “allowed_track_types”:“<types>”,
	   “content_key_specs”:[  
	      {  
	         “track_type”:“<track type 1>”
	      },
	      {  
	         “track_type”:“<track type 2>”
	      },
	      …
	   ],
	   “policy_overrides”:{  
	      “can_play”:<can play>,
	      “can persist”:<can persist>,
	      “can_renew”:<can renew>,
	      “rental_duration_seconds”:<rental duration>,
	      “playback_duration_seconds”:<playback duration>,
	      “license_duration_seconds”:<license duration>,
	      “renewal_recovery_duration_seconds”:<renewal recovery duration>,
	      “renewal_server_url”:”<renewal server url>”,
	      “renewal_delay_seconds”:<renewal delay>,
	      “renewal_retry_interval_seconds”:<renewal retry interval>,
	      “renew_with_usage”:<renew with usage>
	   }
	}

##JSON-Nachricht

Name | Wert | Beschreibung
---|---|---
payload |Base64-codierte Zeichenfolge |Die von einem Client gesendete Lizenzanforderung. 
content\_id | Base64-codierte Zeichenfolge|Bezeichner, der zum Ableiten von Schlüssel-IDs und Inhaltsschlüsseln für „content\_key\_specs.track\_type“ verwendet wird.
Anbieter |string |Wird zum Nachschlagen von Inhaltsschlüsseln und Richtlinien verwendet. Erforderlich.
policy\_name | string |Der Name einer zuvor registrierten Richtlinie. Optional
allowed\_track\_types | enum | SD\_ONLY oder SD\_HD. Steuert, welche Inhaltsschlüssel in eine Lizenz aufgenommen werden müssen
content\_key\_specs | Array von JSON-Strukturen, siehe **Spezifikationen für Inhaltsschlüssel** weiter unten | Eine feiner abgestimmte Steuerung der zurückzugebenden Inhaltsschlüssel. Einzelheiten finden Sie unter „Spezifikationen für Inhaltsschlüssel“ weiter unten. Nur entweder „allowed\_track\_types“ oder „content\_key\_specs“ kann angegeben werden. 
use\_policy\_overrides\_exclusively | Boolescher Wert: true oder false | Verwenden Sie Richtlinienattribute, die von „policy\_overrides“ angegeben werden, und lassen Sie alle zuvor gespeicherten Richtlinien weg.
policy\_overrides | JSON-Struktur, siehe **Außerkraftsetzungen von Richtlinien** weiter unten | Richtlinieneinstellungen für diese Lizenz. Falls dieses Medienobjekt über eine vorab definierte Richtlinie verfügt, werden diese angegebenen Werte verwendet. 
session\_init | JSON-Struktur, siehe **Sitzungsinitialisierung** weiter unten | Optionale Daten, die an die Lizenz übergeben werden.
parse\_only | Boolescher Wert: true oder false | Die Lizenzanforderung wird analysiert, jedoch wird keine Lizenz ausgestellt. Allerdings werden Werte aus der Lizenzanforderung in der Antwort zurückgegeben.  

##Spezifikationen für Inhaltsschlüssel 

Wenn eine Richtlinie bereits vorhanden ist, müssen keine Werte in der Spezifikation für Inhaltsschlüssel angegeben werden. Mit der bereits vorhandenen Richtlinie, die diesem Inhalt zugeordnet ist, wird der Schutz der Ausgabe, z. B. HDCP und CGMS, bestimmt. Wenn eine vorhandene Richtlinie nicht beim Widevine-Lizenzserver registriert ist, kann der Inhaltsanbieter die Werte in die Lizenzanforderung einfügen.


Jeder content\_key\_specs-Wert muss für alle Titel angegeben werden, unabhängig von der Option „use\_policy\_overrides\_exclusively“.


Name | Wert | Beschreibung
---|---|---
content\_key\_specs. track\_type | string | Der Name eines Titeltyps. Wenn „content\_key\_specs“ in der Lizenzanforderung angegeben ist, sollten Sie unbedingt alle Titeltypen explizit angeben. Andernfalls wird die Wiedergabe nach 10 Sekunden beendet. 
content\_key\_specs <br/> security\_level | UInt32 | Definiert die Clientstabilitätsanforderungen für die Wiedergabe. <br/> 1 – Softwarebasierte White-Box-Verschlüsselung ist erforderlich. <br/> 2 – Softwareverschlüsselung und ein verborgener Decoder sind erforderlich. <br/> 3 – Die zentralen Vorgänge für Daten und Verschlüsselung müssen innerhalb einer hardwaregestützten vertrauenswürdigen Ausführungsumgebung ausgeführt werden. <br/> 4 – Verschlüsselung und Decodierung müssen innerhalb einer hardwaregestützten vertrauenswürdigen Ausführungsumgebung ausgeführt werden. <br/> 5 – Verschlüsselung, Decodierung und Verarbeitung von Medien (komprimiert und nicht komprimiert) müssen innerhalb einer hardwaregestützten vertrauenswürdigen Ausführungsumgebung verarbeitet werden.  
content\_key\_specs <br/> required\_output\_protection.hdc | Zeichenfolge: HDCP\_NONE, HDCP\_V1 oder HDCP\_V2 | Gibt an, ob HDCP erforderlich ist.
content\_key\_specs <br/>key | Base64-<br/>codierte Zeichenfolge|Der Inhaltsschlüssel, der für diesen Titel verwendet werden soll. Wenn ein Wert angegeben wird, ist „track\_type“ oder „key\_id“ erforderlich. Mit dieser Option können Inhaltsanbieter den Inhaltsschlüssel für diesen Titel einfügen, statt einen Schlüssel durch den Widevine-Lizenzserver zu generieren oder zu suchen.
content\_key\_specs.key\_id| Base64-codierte Zeichenfolge, binär, 16 Bytes | Eindeutiger Bezeichner für den Schlüssel. 


##Außerkraftsetzungen von Richtlinien 

Name | Wert | Beschreibung
---|---|---
policy\_overrides. can\_play | Boolescher Wert: true oder false | Gibt an, dass die Wiedergabe des Inhalts zulässig ist. Die Standardeinstellung ist "false".
policy\_overrides. can\_persist | Boolescher Wert: true oder false |Gibt an, dass die Lizenz in einem permanenten Speicher für die Offlineverwendung beibehalten werden kann. Die Standardeinstellung ist "false".
policy\_overrides. can\_renew | Boolescher Wert: true oder false |Gibt an, dass die Verlängerung dieser Lizenz zulässig ist. Bei „true“ kann die Dauer der Lizenz über den Takt verlängert werden. Die Standardeinstellung ist "false". 
policy\_overrides. license\_duration\_seconds | int64 | Gibt das Zeitfenster für diese bestimmte Lizenz an. Der Wert 0 gibt an, dass es keine Beschränkung für die Dauer gibt. Der Standardwert ist 0. 
policy\_overrides. rental\_duration\_seconds | int64 | Gibt das Zeitfenster an, in dem die Wiedergabe zulässig ist. Der Wert 0 gibt an, dass es keine Beschränkung für die Dauer gibt. Der Standardwert ist 0. 
policy\_overrides. playback\_duration\_seconds | int64 | Das Anzeigezeitfenster, sobald die Wiedergabe innerhalb der Lizenzdauer beginnt. Der Wert 0 gibt an, dass es keine Beschränkung für die Dauer gibt. Der Standardwert ist 0. 
policy\_overrides. renewal\_server\_url |string | Alle Taktanforderungen (Verlängerung) für diese Lizenz werden an die angegebene URL weitergeleitet. Dieses Feld wird nur verwendet, wenn „can\_renew“ auf „true“ festgelegt ist.
policy\_overrides. renewal\_delay\_seconds |int64 |Die Anzahl der Sekunden nach „license\_start\_time“, bevor der erste Versuch einer Verlängerung unternommen wird. Dieses Feld wird nur verwendet, wenn „can\_renew“ auf „true“ festgelegt ist. Der Standardwert ist 0. 
policy\_overrides. renewal\_retry\_interval\_seconds | int64 | Gibt die Verzögerung in Sekunden zwischen den nachfolgenden Anforderungen zu Lizenzverlängerung an, falls ein Fehler auftritt. Dieses Feld wird nur verwendet, wenn „can\_renew“ auf „true“ festgelegt ist. 
policy\_overrides. renewal\_recovery\_duration\_seconds | int64 | Das Zeitfenster, für das eine Fortsetzung der Wiedergabe zugelassen wird, während der Versuch einer Verlängerung unternommen wird, aber aufgrund von Back-End-Problemen mit dem Lizenzserver noch nicht erfolgreich war. Der Wert 0 gibt an, dass es keine Beschränkung für die Dauer gibt. Dieses Feld wird nur verwendet, wenn „can\_renew“ auf „true“ festgelegt ist.
policy\_overrides. renew\_with\_usage | Boolescher Wert: true oder false |Gibt an, dass die Lizenz zur Verlängerung gesendet werden muss, wenn die Verwendung gestartet wird. Dieses Feld wird nur verwendet, wenn „can\_renew“ auf „true“ festgelegt ist. 

##Sitzungsinitialisierung

Name | Wert | Beschreibung
---|---|---
provider\_session\_token | Base64-codierte Zeichenfolge |Dieses Sitzungstoken wird wieder in der Lizenz übergeben und ist in nachfolgenden Verlängerungen vorhanden. Das Sitzungstoken bleibt nicht über Sitzungen hinaus erhalten. 
provider\_client\_token | Base64-codierte Zeichenfolge | Clienttoken, das in der Lizenzantwort zurück gesendet wird. Wenn die Lizenzanforderung ein Clienttoken enthält, wird dieser Wert ignoriert. Das Clienttoken bleibt über Lizenzsitzungen hinaus erhalten.
override\_provider\_client\_token | Boolescher Wert: true oder false |Wenn „false“ festgelegt wird und die Lizenzanforderung ein Clienttoken enthält, verwenden Sie das Token aus der Anforderung, selbst wenn ein Clienttoken in dieser Struktur angegeben wurde. Verwenden Sie bei „true“ immer das Token, das in dieser Struktur angegeben ist.

##Konfigurieren Ihrer Widevine-Lizenzen mit .NET-Typen

Media Services bietet .NET-APIs, mit denen Sie Ihre Widevine-Lizenzen konfigurieren können.

###Klassen, wie im Media Services .NET SDK definiert

Im Folgenden finden Sie die Definitionen dieser Typen.

	public class WidevineMessage
	{
	    public WidevineMessage();
	
	    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
	    public AllowedTrackTypes? allowed_track_types { get; set; }
	    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
	    public ContentKeySpecs[] content_key_specs { get; set; }
	    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
	    public object policy_overrides { get; set; }
	}

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###Beispiel

Das folgende Beispiel zeigt, wie Sie .NET-APIs verwenden, um eine einfache Widevine-Lizenz zu konfigurieren.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Weitere Informationen

[Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-drm.md)

<!---HONumber=Nov15_HO4-->