<properties
 pageTitle="Verwalten von IoT Hubs mit dem Azure-Portal | Microsoft Azure"
 description="Eine Übersicht zum Erstellen und Verwalten von Azure IoT Hubs über das Azure-Portal"
 services="iot-hub"
 documentationCenter=""
 authors="nasing"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="nasing"/>

# Verwalten von IoT Hubs über das Azure-Portal

## Einführung:

Dieses Thema beschreibt Informationen zu den ersten Schritten mit Azure IoT Hub über das Azure-Portal, zum Finden von IoT Hubs sowie zum Erstellen und Verwalten von IoT Hubs.

## Hier finden Sie IoT Hubs:

IoT Hubs finden Sie an vielen Stellen.

1. **+ Neu** : Azure IoT Hub ist ein IoT-Dienst, den Sie wie andere Dienste unter der Kategorie „Internet der Dinge“ unter **+ Neu** finden.

2. Auf IoT Hubs können Sie auch über den Marketplace zugreifen – wie der Hero-Dienst unter **Internet der Dinge**.

## Erstellen eines IoT Hubs

Sie können einen Azure IoT Hub mit den im vorherigen Abschnitt beschriebenen Methoden erstellen.

1. Wenn Sie einen IoT Hub über die Option **+ Neu** erstellen, wird das folgende Blatt angezeigt. Beim Erstellen des IoT Hubs mit dieser Methode gehen Sie genauso vor wie beim Erstellen über den Marketplace.

2. Erstellen eines IoT Hubs über den Marketplace: Wenn Sie auf **Erstellen** klicken, wird ein Blatt geöffnet, das mit dem vorherigen Blatt der Option **+ Neu** identisch ist. Die Erstellung eines IoT Hubs umfasst mehrere Schritte, die in den nächsten Abschnitten aufgeführt sind:

### Auswählen des Namen des IoT Hubs

Um einen IoT Hub zu erstellen, muss der Benutzer den Hub benennen. Bitte beachten Sie, dass dieser Name für die Hubs eindeutig sein muss. Da im Backend keine Duplizierung von Hubs zulässig ist, wird eine möglichst eindeutige Benennung dieses Hubs empfohlen.

### Auswählen des Tarifs

Kunden können zwischen 3 Tarifen wählen: **Free**, **Standard 1** und **Standard 2**. Mit dem Free-Tarif können nur 10 Geräte mit dem IoT Hub verbunden sein.

**S1 (Low Frequency)**: IoT Hubs S1 (Low Frequency) ist auf IoT-Lösungen ausgelegt, die eine große Anzahl von Geräten umfassen, die relativ kleine Datenmengen pro Gerät generieren. Jede Einheit von S1 (Low Frequency) ermöglicht die Konnektivität von bis zu 500 Geräten oder bis zu 50.000 Nachrichten pro Tag auf allen verbundenen Geräten.

**S2 (High Frequency)**: IoT Hub S2 (High Frequency) ist auf IoT-Lösungen ausgelegt, in denen Geräte große Datenmengen generieren. Jede Einheit von S2 (Low Frequency) ermöglicht die Konnektivität von bis zu 500 Geräten oder bis zu 1,5 Millionen Nachrichten pro Tag auf allen verbundenen Geräten.

![][4]

> [AZURE.NOTE]IoT Hub ermöglicht nur einen Free-Hub pro Abonnement.

### IoT-Hub-Einheiten

Eine IoT-Einheit enthält 500 Geräte. Die Anzahl der IoT-Einheiten bedeutet also, dass die Gesamtanzahl der für diesen Hub unterstützten Geräte der Anzahl der Einheiten multipliziert mit 500 entspricht. Soll der IoT Hub beispielsweise 1000 Geräte unterstützen, so wählen Sie 2 Einheiten.

### Gerät für Cloud-Partitionen und Ressourcengruppe

Sie können die Anzahl der Partitionen für einen IoT Hub ändern. Standardmäßig sind 4 Partitionen festgelegt, Sie können jedoch eine andere Anzahl von Partitionen aus einer Dropdownliste auswählen.

Für Ressourcengruppen muss nicht zwingend eine leere Ressourcengruppe erstellt werden. Beim Erstellen einer neuen Ressource können Sie entweder eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe verwenden.

![][5]

### Auswählen von Abonnements

Azure IoT Hub zeigt automatisch die Liste der Abonnements an, mit denen das Benutzerkonto verknüpft ist. Sie können eine dieser Optionen auswählen, um den IoT Hub diesem Abonnement zuzuordnen.

### Wählen des Standorts

Die Option „Standort“ enthält eine Liste der Regionen, in denen IoT Hub angeboten wird. Für die öffentliche Vorabversion wird der Hub nur in 3 Regionen angeboten: USA Ost, Nordeuropa und Ostasien.

### Erstellen des IoT Hubs

Wenn alle oben aufgeführten Schritte abgeschlossen sind, kann der IoT Hub erstellt werden. Wenn Sie auf **Erstellen** klicken, startet der Back-End-Prozess zur Erstellung dieses IoT Hubs mit den festgelegten Optionen und stellt diese am angegebenen Ort bereit.

Bitte beachten Sie, dass die Erstellung des IoT Hubs einige Minuten in Anspruch nehmen kann, da es etwas dauert, bis die Back-End-Bereitstellung in den jeweiligen Servern angezeigt werden

## Ändern der Einstellungen des IoT Hubs

Sie können die Einstellungen eines vorhandenen IoT Hub nach dessen Erstellung ändern. Durch Klicken und Auswählen des IoT Hubs wird die Seite „Einstellungen“ geöffnet.

![][8]

**Freigegebene Zugriffsrichtlinien**: Hierbei handelt es sich um die Richtlinien, die die Berechtigungen für Geräte und Dienste zum Herstellen einer Verbindung mit dem IoT Hub definieren. Sie finden diese Richtlinien, indem Sie unter **Einstellungen** auf **Freigegebene Zugriffsrichtlinien** klicken. Auf diesem Blatt können Sie vorhandene Richtlinien ändern oder eine neue Richtlinie hinzufügen.

### Erstellen einer neuen Richtlinie

- Klicken Sie auf die Schaltfläche **Hinzufügen**, um ein Blatt zu öffnen, auf dem Sie den Namen der neuen Richtlinie und die Berechtigungen, die dieser Richtlinie zugeordnet werden sollen, wie in der folgenden Abbildung gezeigt eingeben können.

	Diesen freigegebenen Richtlinien können mehrere Berechtigungen zugeordnet werden. Die ersten beiden Richtlinien, **Registry lesen** und **Registry schreiben**, berechtigen zum Lesen und Schreiben im Geräteidentitätsspeicher oder Identitätsregistry. Bitte beachten Sie, dass beim Auswählen der Option mit Schreibzugriff die Option mit Lesezugriff automatisch ausgewählt wird.

 	Die Richtlinie für die Serververbindung gewährt der Consumergruppe Zugriff auf Dienste mit Verbindung zum IoT Hub, und die Geräteverbindung gewährt Zugriff auf Geräte des IoT Hubs.

- Klicken Sie auf „Richtlinie erstellen“, um der vorhandenen Liste diese neu erstellte Richtlinie hinzuzufügen.

![][10]

## Nachrichten

Klicken Sie auf die **Messaging**-Richtlinien, um eine Liste der Messaging-Eigenschaften für den IoT Hub anzuzeigen, der verändert wird. Zwei wichtige Eigenschaften können geändert oder kopiert werden: **C2D** (Cloud to device, Cloud zu Gerät) und **C2D** (Device to Cloud, Gerät zu Cloud).

- **C2D-Einstellungen**: Diese Option hat 2 untergeordnete Einstellungen: **C2D-TTL** (Time to Live, Gültigkeitsdauer) und **Aufbewahrungszeit** für Nachrichten. Bei der ersten Erstellung des IoT Hubs werden diese beiden Einstellungen mit einem Standardwert von 1 Stunde erstellt. Allerdings können Sie diese mithilfe der Schieberegler oder durch Eingeben der Werten anpassen.

- **D2C-Einstellungen**: Diese Option hat mehrere untergeordnete Einstellungen, von denen einige bei Erstellung des IoT Hubs benannt bzw. zugewiesen werden und nur in andere untergeordneten Einstellungen kopiert werden können, die angepasst werden können. Diese sind alle im nächsten Abschnitt aufgeführt.

**Partitionen**: Dieser Wert wird bei Erstellung des IoT Hubs festgelegt und kann durch diese Einstellung geändert werden.

**Event Hub – Kompatibler Name und Endpunkt**: Bei Erstellung des IoT Hubs wird intern ein Event Hub erstellt, auf den der Benutzer auf unter bestimmten Umständen zugreifen muss. Name und Endpunkt dieses Event Hubs können nicht angepasst werden, stehen jedoch über die Schaltfläche **Kopieren** zur Verfügung.

**Aufbewahrungszeit**: Standardmäßig auf 1 Tag festgelegt, kann aber mithilfe der Dropdownliste in andere Werte geändert werden. Beachten Sie, dass dieser Wert für D2C in Tagen statt Stunden angegeben ist und die Einstellungen für C2D identisch sind.

**Consumergruppen**: Consumergruppen sind Einstellung, die anderen Messaging-Systemen ähneln, die zum Abrufen von Daten auf bestimmte Weise verwendet werden können, um andere Programme oder Dienste mit IoT Hub zu verbinden. Jeder IoT Hub wird mit einer Standard-Consumergruppe erstellt. Sie können Ihren IoT Hubs jedoch Consumergruppen hinzufügen oder diese daraus löschen.

> [AZURE.NOTE]Die Standard-Consumergruppe kann nicht bearbeitet oder gelöscht werden.

![][11]

## Preise und Skalierung

Die Preise eines vorhandenen IoT Hubs können mit folgenden Ausnahmen über die Einstellungen **Preise** geändert werden:

- In der aktuellen Implementierung kann ein IOT Hub mit Free-SKU nicht in eine der kostenpflichtigen SKUs geändert werden oder umgekehrt.
- Im Abonnement kann nur ein IoT Hub des Free-Tarifs enthalten sein.

![][12]

Der Wechsel von einem hohen Tarif (S2) in einen niedrigen Tarif (S1) ist nur zulässig, wenn die Anzahl an Nachrichten des Tages nicht miteinander in Konflikt stehen. Hat die Anzahl der Nachrichten pro Tag beispielsweise 50.000 überschritten, kann der Tarif für den IoT Hub nicht von S2 zu S1 geändert werden.

## Löschen des IoT Hubs

Klicken Sie zum Suchen des zu löschenden IoT Hubs auf **Durchsuchen** und wählen Sie dann den löschenden Hub aus. Durch Klicken auf die Schaltfläche **Löschen** wird der HubName gelöscht.


  [4]: ./media/iot-hub-manage-through-portal/create-iothub.png
  [5]: ./media/iot-hub-manage-through-portal/location1.png
  [8]: ./media/iot-hub-manage-through-portal/portal-settings.png
  [10]: ./media/iot-hub-manage-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-manage-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-manage-through-portal/pricing-error.png

<!---HONumber=Oct15_HO3-->