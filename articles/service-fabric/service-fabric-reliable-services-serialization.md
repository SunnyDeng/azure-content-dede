<properties
   pageTitle="Reliable Service-Serialisierung | Microsoft Azure"
   description="Dokumentation zum Serialisieren von Reliable Services mit Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson,tyadam"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="mcoskun"/>

# Serialisierung in Reliable Services
Reliable State Manager kann mehrere Reliable Objects enthalten. Einige dieser Reliable Objects können generische Datenstrukturen sein, wie z. B. das vorkonfigurierte zuverlässige Wörterbuch und die zuverlässige Warteschlange. Da dies zuverlässige generische Datenstrukturen sind, müssen die darin enthaltenen generischen Objekte serialisiert werden, um repliziert und auf dem Datenträger persistent gespeichert werden zu können.

Reliable State Manager unterstützt drei Arten von Serialisierungsprogrammen: * benutzerdefinierte Serialisierungsprogramme, * integrierte Serialisierungsprogramme für eine begrenzte Anzahl von Typen und * DataContractSerializer

Wenn ein Reliable Object ein Objekt serialisieren muss, ist der Reliable State Manager für ein Serialisierungsprogramm des entsprechenden Typs erforderlich. Reliable State Manager überprüft zuerst, ob ein benutzerdefiniertes Serialisierungsprogramm für den Eingabetyp registriert ist. Ist dies nicht der Fall, wird überprüft, ob eines der integrierten Serialisierungsprogramme den Typ serialisieren kann. Reliable State Manager verfügt über integrierte Serialisierungsprogramme für die folgenden Typen: „guid“, „bool“, „byte“, „sbyte“, „char“, „decimal“, „double“, „float“, „int“, „uint“, „long“, „ulong“, „short“, „ushort“ und „string“. Ist dies nicht der Fall, wird DataContractSerializer zurückgegeben.

Dieser Artikel konzentriert sich darauf, wann und wie benutzerdefinierte Serialisierung verwendet wird.

## Wann wird die benutzerdefinierte Serialisierung verwendet?
Es gibt zwei häufige Gründe für die Verwendung der benutzerdefinierten Serialisierung: * Leistung und * Verschlüsselung.

Für Typen, die von den integrierten Serialisierungsprogrammen nicht unterstützt werden, sind durch Verwenden benutzerdefinierter Serialisierungsprogramme anstelle von DataContractSerializer erhebliche Leistungssteigerungen zu erzielen. Ein Grund hierfür ist, dass Typinformationen von benutzerdefinierten Serialisierungsprogrammen nicht serialisiert werden müssen. Service Fabric stellt sicher, dass einem Zustandsserialisierungsprogramm für einen bestimmten Typ nur dieser Typ zur Serialisierung und Deserialisierung übergeben wird.

Die von den Serialisierungsprogrammen generierten serialisierten Daten werden repliziert und auf dem Datenträger in unveränderter Form persistent gespeichert. Für vertrauliche Daten kann es wünschenswert sein, sicherzustellen, dass die Bits bei der Übertragung und auf dem Datenträger verschlüsselt werden.

## Wie wird die benutzerdefinierte Serialisierung verwendet?
Um ein benutzerdefiniertes Serialisierungsprogramm für einen bestimmten Typ zu verwenden, müssen Sie * ein benutzerdefiniertes Zustandsserialisierungsprogramm erstellen und * das benutzerdefinierte Zustandsserialisierungsprogramm in Reliable Services registrieren

### Implementieren eines benutzerdefinierten Serialisierungsprogramms
Benutzerdefinierte Serialisierungsprogramme müssen die IStateSerializer<T>-Schnittstelle implementieren. Die beiden wichtigsten Methoden in dieser Schnittstelle sind * T Read(BinaryReader binaryReader); * void Write(T value, BinaryWriter binaryWriter).

Ersteres wird vom ReliableObject verwendet, um das serialisierte Objekt mit einem BinaryReader aus einem Stream zu lesen. Letztes wird für den umgekehrten Vorgang verwendet: nämlich, um das Objekt mit einem BinaryWriter in einen Stream zu schreiben.

Es folgt ein Beispiel für eine benutzerdefinierte Klasse und ein entsprechendes Serialisierungsprogramm.

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }
    public short District { get; set; }
    public int Customer { get; set; }
    public long Order { get; set; }
```

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
    void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
    {
        writer.Write(value.Warehouse);
        writer.Write(value.District);
        writer.Write(value.Customer);
        writer.Write(value.Order);
    }

    OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
    {
        OrderKey value = new OrderKey();
        value.Warehouse = reader.ReadByte();
        value.District = reader.ReadInt16();
        value.Customer = reader.ReadInt32();
        value.Order = reader.ReadInt64();

        return value;
    }

    void IStateSerializer<OrderKey>.Write(OrderKey currentValue, OrderKey newValue, BinaryWriter writer)
    {
        ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
    }

    OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
    {
        return ((IStateSerializer<OrderKey>)this).Read(reader);
    }
}
```
>[AZURE.NOTE]Im oben stehenden Beispiel wird durch die Implementierung der Lese- und Schreibüberladungen einfach die Überladung des Pendants aufgerufen, weil die folgenden beiden Methoden für eine Funktion verwendet werden, die noch nicht verfügbar ist.

### Registrieren eines benutzerdefinierten Serialisierungsprogramms
Um ein benutzerdefiniertes Serialisierungsprogramm zu registrieren, benötigen wir zuerst eine Methode, die alle benutzerdefinierten Serialisierungsprogramme registrieren kann. Diese Methode soll keine Argumente aufweisen und muss eine Task zurückgeben können. Bei dieser Methode muss IReliableStateManager.TryAddStateSerializer<T> verwendet werden, um alle benutzerdefinierten Serialisierungsprogramme für Reliable Service zu registrieren.

```C#
protected Task InitializeStateSerializers()
{
    this.StateManager.TryAddStateSerializer(new OrderKeySerializer());
    return Task.FromResult(false);
}
```

Als Nächstes wird die oben dargestellte Methode als ein Delegat registriert, der von Reliable State Manager aufgerufen wird, wenn alle benutzerdefinierten Zustandsserialisierungsprogramme registriert werden sollen. Diese Methode wird nur beim Start des Reliable Service-Replikats aufgerufen, bevor die lokale Wiederherstellung gestartet wird, da Serialisierungsprogramme möglicherweise benötigt werden, um die serialisierten Daten vom Datenträger zu lesen. Nachdem das Serialisierungsprogramm registriert wurde, verwendet der relevante Typ von allen Reliable Objects dieses Serialisierungsprogramm zum Serialisieren und Deserialisieren ihrer Objekte.

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            onInitializeStateSerializersEvent : this.InitializeStateSerializers));
}
```
### Versionsverwaltung
Service Fabric erwartet, dass die Serialisierungsprogramme uneingeschränkt aufwärts- und abwärtskompatibel sind. Für die Typen, die integrierte Serialisierungsprogramme verwenden, stellt Service Fabric Aufwärts- und Abwärtskompatibilität sicher. Für die Typen, die DataContractSerializer oder ein benutzerdefiniertes Serialisierungsprogramm verwenden, darf der Benutzer niemals grundlegende Änderungen vornehmen. Informationen zur DataContract-Versionsverwaltung finden Sie unter [Datenvertragsversionsverwaltung](https://msdn.microsoft.com/library/ms731138.aspx). Wenn eine grundlegende Änderung erforderlich ist, muss der Zustand auf Anwendungsebene von einer Dienstinstanz mit der alten Datenversion in einen Dienst mit der neuen Datenversion verschoben werden.

### Wann wird ein Serialisierungsprogramm verwendet?
 * Schreiben von Vorgängen für Reliable Objects, durch die diese serialisiert, repliziert und in einem Protokoll gespeichert werden.
 * Nachdem genügend Vorgänge protokolliert wurden, werden die neuesten Daten aus dem Arbeitsspeicher serialisiert und mit einem Prüfpunkt auf dem Datenträger gespeichert.
 * Für die Neuerstellung eines Replikats werden die Prüfpunktdateien auf dem Datenträger und die aktuellen Daten aus dem Protokoll direkt Byte für Byte (d. h. die Daten werden nicht erneut serialisiert) aus einem primären Replikat gesendet. Diese Bytes werden in C#-Objekte im sekundären Replikat deserialisiert.
 * Während der Wiederherstellung werden die Prüfpunktdateien und das aktuelle Datenprotokoll deserialisiert.
 * Während der Sicherung werden die Prüfpunktdateien und die aktuellen Protokolldaten Byte für Byte kopiert.
 * Während der Wiederherstellung werden die zuvor gesicherten Prüfpunktdateien und Protokolldaten wieder an Ort und Stelle kopiert und deserialisiert.

## Nächste Schritte
 * [Erweiterte Verwendung des Reliable Services-Programmiermodells](service-fabric-reliable-services-advanced-usage.md)

<!---HONumber=AcomDC_1203_2015-->