<properties
   pageTitle="Service Fabric-Anwendungsupgrade: Datenserialisierung"
   description="Bewährte Methoden für die Datenserialisierung zur Sicherstellung erfolgreicher Anwendungsupgrades."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2015"
   ms.author="jesseb"/>


# Service Fabric-Anwendungsupgrade: Datenserialisierung

Bei einem [parallelen Anwendungsupgrade](service-fabric-application-upgrade.md) wird das Upgrade auf eine Teilmenge von Knoten angewendet. Dabei werden die einzelnen Upgradedomänen nacheinander abgearbeitet. Während dieses Vorgangs weisen einige Upgradedomänen die neuere Version Ihrer Anwendung auf und andere Upgradedomänen die ältere Version der Anwendung. Zu diesem Zeitpunkt muss die neue Version der Anwendung die alte Version Ihrer Daten sowie die alte Version der Anwendung die neue Version der Daten lesen können. Wenn das Datenformat nicht aufwärts- und abwärtskompatibel ist, kann das Upgrade fehlschlagen, oder es gehen möglicherweise Daten verloren. In diesem Artikel werden die Zusammensetzung des Datenformats sowie bewährte Methoden zum Sicherstellen der Aufwärts- und Abwärtskompatibilität der Daten erörtert.


## Woraus besteht das Datenformat?

In Service Fabric stammen die Daten, die persistent gespeichert und repliziert werden, aus den C#-Klassen. Bei Anwendungen mit [zuverlässige Auflistungen](service-fabric-reliable-services-reliable-collections.md) sind dies die Objekte in den zuverlässigen Wörterbüchern und Warteschlangen. Bei Anwendungen mit [zustandsbehafteten zuverlässigen Akteuren](service-fabric-reliable-actors-introduction.md) ist dies der unterstützende Zustand für den Akteur. Diese C#-Klassen müssen serialisierbar sein, um persistent gespeichert und repliziert zu werden. Aus diesem Grund wird das Datenformat durch die Felder und Eigenschaften definiert, die serialisiert werden, sowie dadurch, wie sie serialisiert werden. In `IReliableDictionary<int, MyClass>` sind die Daten beispielsweise ein serialisiertes `int` und eine serialisierte `MyClass`.

### Änderungen des Datenformats

Da das Datenformat durch C#-Klassen bestimmt wird, ziehen Änderungen an den Klassen möglicherweise eine Änderung des Datenformats nach sich. Es muss sorgfältig sichergestellt werden, dass ein paralleles Upgrade die Änderung des Datenformats verarbeiten kann. Beispiele, die Änderungen des Datenformats zur Folge haben können:

- Hinzufügen oder Entfernen von Feldern oder Eigenschaften
- Umbenennen von Feldern oder Eigenschaften
- Ändern der Feld- oder Eigenschaftentypen
- Ändern des Klassennamens oder Namespaces

### Standardserialisierungsprogramm

Das Serialisierungsprogramm liest i. Allg. die Daten und deserialisiert sie in die aktuelle Version, selbst wenn die Daten eine ältere oder *neuere* Version aufweisen. Das Standardserialisierungsprogramm ist der [Datenvertragsserialisierer](https://msdn.microsoft.com/library/ms733127.aspx), der klar definierte Versionsregeln aufweist. Mit zuverlässigen Auflistungen kann das Serialisierungsprogramm überschrieben werden, mit zuverlässigen Akteuren derzeit jedoch nicht. Der Datenvertragsserialisierer spielt eine wichtige Rolle bei der Aktivierung von parallelen Upgrades. Der Datenvertragsserialisierer wird als Serialisierungsprogramm für Service Fabric-Anwendungen empfohlen.


## Auswirkungen des Datenformats auf das parallele Upgrade

Während eines parallelen Upgrades gibt es zwei Hauptszenarios, bei denen das Serialisierungsprogramm auf eine ältere oder *neuere* Version Ihrer Daten treffen kann:

1. Nachdem ein Knoten aktualisiert wurde und wieder gestartet wird, lädt das neue Serialisierungsprogramm die Daten, die persistent auf Festplatte gespeichert waren, mit der neuen Version.
2. Während des parallelen Upgrades kann der Cluster eine Mischung aus der alten und neuen Version des Codes enthalten. Da Replikate in verschiedenen Upgradedomänen platziert werden können, kann das Serialisierungsprogramm auf die neue und alte Version Ihrer Daten treffen (das Serialisierungsprogramm kann selbst die neue oder die alte Version aufweisen).

> [AZURE.NOTE]Die "neue Version" und die "alte Version" beziehen sich hier auf die Version des ausgeführten Codes. Das "neue Serialisierungsprogramm" bezieht sich auf den Serialisierercode, der in der neuen Version der Anwendung ausgeführt wird. Die "neuen Daten" beziehen sich auf die serialisierte C#-Klasse aus der neuen Version der Anwendung.

Die beiden Versionen des Codes und Datenformats müssen aufwärts- und abwärtskompatibel sein. Wenn sie nicht kompatibel sind, kann das parallele Upgrade fehlschlagen, oder es gehen möglicherweise Daten verloren. Das parallele Upgrade kann fehlschlagen, weil der Code oder das Serialisierungsprogramm bei der jeweils anderen Version Ausnahmen oder Fehler ausgibt. Daten können verloren gehen, wenn beispielsweise eine neue Eigenschaft hinzugefügt wurde, das alte Serialisierungsprogramm sie jedoch während der Deserialisierung verwirft.


## Datenverträge

Datenverträge sind die empfohlene Lösung zum Sicherstellen der Kompatibilität Ihrer Daten. Sie verfügen über klar definierte Versionsregeln zum Hinzufügen, Entfernen und Ändern von Feldern. Sie unterstützen außerdem die Behandlung von unbekannten Feldern, die Hookfunktion für den Serialisierungs- und Deserialisierungsvorgang und die Klassenvererbung. Weitere Informationen finden Sie unter [Verwenden von Datenverträgen](https://msdn.microsoft.com/library/ms733127.aspx).


## Nächste Schritte

[Lernprogramm für Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md)

[Anwendungsupgradeparameter](service-fabric-application-upgrade-parameters.md)

[Weiterführende Themen](service-fabric-application-upgrade-advanced.md)
 

<!---HONumber=July15_HO4-->