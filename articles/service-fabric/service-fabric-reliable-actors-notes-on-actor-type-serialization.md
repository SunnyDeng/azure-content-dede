<properties
   pageTitle="Hinweise zur Serialisierung des Actor-Typs in Reliable Actors"
   description="Erörtert grundlegende Anforderungen für das Definieren serialisierbarer Klassen, die zum Definieren von Statusangaben und Schnittstellen für Service Fabric Reliable Actors verwendet werden können."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Hinweise zur Typserialisierung von Service Fabric Reliable Actors

Beim Definieren der Schnittstelle(n) und des Status eines Actors sollten Sie einige wichtige Aspekte im Hinterkopf behalten. Typen müssen Datenvertrag-serialisierbar sein. Weitere Informationen zu Datenverträgen finden Sie unter [MSDN](https://msdn.microsoft.com/library/ms731923.aspx).

## Actor-Schnittstellentypen

Die Argumente aller Methoden und die Ergebnistypen der Tasks, die von den einzelnen in der [Actor-Schnittstelle](service-fabric-reliable-actors-introduction.md#actors) definierten Methoden zurückgegeben werden, müssen Datenvertrag-serialisierbar sein. Dies gilt auch für die Argumente der Methoden, die in [Actor- Ereignisschnittstellen](service-fabric-reliable-actors-events.md#actor-events) definiert werden. (In Actor-Ereignisschnittstellen definierte Methoden geben immer „void“ zurück). Wenn z. B. die `IVoiceMail`-Schnittstelle eine Methode definiert als:

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` ist ein .NET-Standardtyp, der bereits Datenvertrag-serialisierbar ist. Auch der `Voicemail`-Typ muss Datenvertrag-serialisierbar sein.

```csharp

[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}

```

## Actor-Statusklasse

Der Actor-Status muss Datenvertrag-serialisierbar sein. Beispielsweise kann eine Actor-Klassendefinition wie folgt aussehen:

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

Die Statusklasse wird mit der Klasse definiert, und ihre Member werden jeweils mit den Attributen **DataContract** und **DataMember** versehen.

```csharp

[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}

```

<!---HONumber=AcomDC_0121_2016-->