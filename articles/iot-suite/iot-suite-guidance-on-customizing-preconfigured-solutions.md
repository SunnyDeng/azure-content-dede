<properties
	pageTitle="Anpassen von vorkonfigurierten Lösungen | Microsoft Azure"
	description="Dieses Dokument bietet eine Anleitung zum Anpassen vorkonfigurierter Azure IoT Suite-Lösungen."
	services=""
    suite="iot-suite"
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/02/2016"
     ms.author="stevehob"/>

# Anpassen vorkonfigurierter Lösungen

Die vorkonfigurierten Lösungen in der Azure IoT Suite veranschaulichen das Bereitstellen einer umfassenden Lösung basierend auf dem Zusammenwirken der Dienste in der Suite. Von hier ausgehend gibt es verschiedene Möglichkeiten zur Erweiterung und Anpassung der Lösung für bestimmte Szenarios. Diese allgemeinen Anpassungsmöglichkeiten werden in den folgenden Abschnitten beschrieben.

## Suchen des Quellcodes

Der Quellcode für die vorkonfigurierten Lösungen ist auf GitHub in den folgenden Repositorys verfügbar:

- Remoteüberwachung: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Vorhersagbarer Wartungsbedarf: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

Der Quellcode für die vorkonfigurierten Lösungen wird bereitgestellt, um Muster und Verfahren zum Implementieren der umfassenden Funktionalität von IoT-Lösungen mithilfe von Azure IoT Suite zu veranschaulichen. Weitere Informationen zur Erstellung und Bereitstellung von Lösungen finden Sie in den GitHub-Repositorys.

## Verwalten der Berechtigungen in einer vorkonfigurierten Lösung
Das Lösungs-Portal für jede vorkonfigurierte Lösung wird als eine neue Azure Active Directory-Anwendung erstellt. Sie können die Berechtigungen für das Lösungs-Portal (AAD-Anwendung) wie folgt verwalten:

1. Öffnen Sie das [klassische Azure-Portal](https://manage.windowsazure.com).
2. Navigieren Sie zur AAD-Anwendung, indem Sie **Anwendungen im Besitz meines Unternehmens** auswählen und dann auf das Häkchen klicken.
3. Navigieren Sie zu **Benutzer**, und weisen Sie Mitglieder in Ihrem Azure Active Directory-Mandanten einer Rolle zu. 

Die Anwendung wird standardmäßig mit den Rollen **Administrator**, **Read Only** und **Implicit Read Only** bereitgestellt. **Implicit Read Only** wird Benutzern gewährt, die Mitglied des Azure Active Directory-Mandanten sind, denen jedoch keine Rolle zugewiesen wurde. Sie können die Datei [RolePermissions.cs](https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs) ändern, nachdem Sie das GitHub-Repository verzweigt haben, und dann die Projektmappe erneut bereitstellen.

## Ändern der vorkonfigurierten Regeln

Die Remoteüberwachungslösung enthält drei [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)-Aufträge, um die Geräteinformationen, Telemetriedaten und Regellogik für die Lösung zu implementieren.

Die drei Stream Analytics-Aufträge und ihre Syntax werden ausführlich unter [Exemplarische Vorgehensweise für die vorkonfigurierte Lösung zur Remoteüberwachung](iot-suite-remote-monitoring-sample-walkthrough.md) beschrieben.

Sie können diese Aufträge direkt bearbeiten, um die Logik zu ändern oder spezifische Logik für das Szenario hinzuzufügen. Sie finden die Stream Analytics-Aufträge wie folgt:
 
1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Navigieren Sie zu der Ressourcengruppe mit demselben Namen wie Ihre IoT-Lösung. 
3. Wählen Sie den Azure Stream Analytics-Auftrag aus, den Sie ändern möchten. 
4. Beenden Sie den Auftrag, indem Sie aus den Befehlen **Beenden** auswählen. 
5. Bearbeiten Sie die Eingaben, die Abfrage und die Ausgaben.

    Eine einfache Änderung besteht darin, die Abfrage für den **Rules**-Auftrag so zu ändern, dass **„<“** anstelle von **„>“** verwendet wird. Das Lösungs-Portal zeigt weiterhin **„>“** an, wenn Sie eine Regel bearbeiten, aber Sie werden feststellen, dass sich das Verhalten aufgrund der Änderung am zugrunde liegenden Auftrag umkehrt.

6. Starten des Auftrags

> [AZURE.NOTE] Das Remoteüberwachungs-Dashboard hängt von bestimmten Daten ab. Deshalb kann das Ändern der Aufträge dazu führen, dass das Dashboard fehlschlägt.

## Hinzufügen eigener Regeln

Sie können nicht nur die vorkonfigurierten Azure Stream Analytics-Aufträge ändern, sondern auch im Azure-Portal neue Aufträge hinzufügen oder vorhandenen Aufträgen neue Abfragen hinzufügen.

## Anpassen von Geräten

Eine der am häufigsten Erweiterungsaktivitäten ist das Arbeiten mit speziellen Geräten für Ihr Szenario. Es gibt mehrere Methoden zum Arbeiten mit Geräten. Dazu gehören das Anpassen eines simulierten Geräts an Ihr Szenario und das Verknüpfen des physischen Geräts mit der Lösung mithilfe des [IoT-Geräte-SDK][].

Unter [Verbinden von Geräten mit der IoT Suite](iot-suite-connecting-devices.md) finden Sie eine schrittweise Anleitung zum Hinzufügen von Geräten zur vorkonfigurierten Remoteüberwachungslösung.

### Erstellen eines eigenen simulierten Geräts

Der Quellcode der Remoteüberwachungslösung (auf den oben verwiesen wird) enthält einen .NET-Simulator. Dieser Simulator wird als Teil der Lösung bereitgestellt und kann geändert werden, um andere Metadaten oder Telemetrie zu senden oder auf andere Befehle zu reagieren.

Der vorkonfigurierte Simulator in der vorkonfigurierten Lösung zur Remoteüberwachung ist ein Kühlgerät, das Telemetriedaten zu Temperatur und Feuchtigkeit ausgibt. Sie können den Simulator im Projekt [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) ändern, wenn Sie das GitHub-Repository verzweigt haben.

Darüber hinaus stellt Azure IoT ein [C-SDK-Beispiel](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) bereit, das mit der vorkonfigurierten Remoteüberwachungslösung verwendet werden kann.

### Erstellen und Verwenden eines eigenen (physischen) Geräts

Die [Azure IoT-SDKs](https://github.com/Azure/azure-iot-sdks) bieten Bibliotheken zum Verbinden zahlreicher Gerätetypen (Sprachen und Betriebssysteme) mit IoT-Lösungen.

## Feedback

Haben Sie Anpassungsvorschläge für dieses Dokument? Fügen Sie Vorschläge für neue Funktionen im [Benutzerforum](https://feedback.azure.com/forums/321918-azure-iot) hinzu, oder kommentieren Sie diesen Artikel weiter unten.

## Nächste Schritte

Weitere Informationen zu IoT-Geräten finden Sie auf der [Azure IoT-Entwicklerwebsite](https://azure.microsoft.com/develop/iot/), die entsprechende Links und Dokumente enthält.

[IoT-Geräte-SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_0323_2016-->