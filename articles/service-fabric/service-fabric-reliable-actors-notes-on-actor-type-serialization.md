<properties
   pageTitle="Hinweise zur Actor-Typserialisierung von Reliable Actors"
   description="Es werden grundlegende Anforderungen für das Definieren von serialisierbaren Klassen erörtert, die zum Definieren von Status und Schnittstellen für Service Fabric Reliable Actor verwendet werden können"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="claudioc"/>



# Hinweise zur Typserialisierung von Service Fabric Reliable Actors

Es gibt nur wenige wichtige Aspekte, die beim Definieren der Schnittstelle(n) und des Status des Actors zu berücksichtigen sind: Typen müssen Datenvertrag-serialisierbar sein. Weitere Informationen zu Datenverträgen finden Sie unter [MSDN](https://msdn.microsoft.com/library/ms731923.aspx).

## In Actor-Schnittstellen verwendete Typen

Die Argumente aller Methoden und der Ergebnistyp des Tasks, der von jeder in der [Actor-Schnittstelle](service-fabric-reliable-actors-introduction.md#actors) definierten Methode zurückgegeben wird, müssen Datenvertrag-serialisierbar sein. Dies gilt auch für die Argumente von Methoden, die in [Actor- Ereignisschnittstellen](service-fabric-reliable-actors-events.md#actor-events) definiert werden. (In Actor-Ereignisschnittstellen definierte Methoden geben immer "void" zurück). Wenn z. B. die `IVoiceMail`-Schnittstelle eine Methode definiert als:

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` ist ein .NET-Standardtyp, der bereits Datenvertrag-serialisierbar ist. Der `Voicemail`-Typ muss Datenvertrag-serialisierbar sein.

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

## Actor-Status-Klasse

Der Actor-Status muss Datenvertrag-serialisierbar sein. Wenn z. B. eine Actor-Klassendefinition vorhanden ist, die wie folgt aussieht:

```csharp

public class VoiceMailActor : Actor<VoicemailBox>, IVoiceMail
{
...

```

Die Status-Klasse wird mit der Klasse definiert, und ihre Member werden jeweils mit den Attributen "DataContract" und "DataMember" versehen.

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

<!---HONumber=August15_HO6-->