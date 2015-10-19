<properties
	pageTitle="Erste-Schritte-Tutorial für vorkonfigurierte Microsoft Azure IoT Suite-Lösungen | Microsoft Azure"
	description="In diesem Tutorial erlernen Sie das Bereitstellen einer vorkonfigurierten Azure IoT Suite-Lösung."
	services=""
	documentationCenter=".net"
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="araguila"/>

# Erste Schritte mit den vorkonfigurierten IoT-Lösungen

## Einführung

Die vorkonfigurierten Lösungen in der Azure IoT Suite kombinieren eine Reihe von Azure IoT-Diensten, um eine umfassende Lösung für ein Geschäftsszenario des Internets der Dinge (Internet of Things, IoT) zu veranschaulichen.

In diesem Lernprogramm wird gezeigt, wie Sie eine vorkonfigurierte Lösung für die **Remoteüberwachung** bereitstellen. In ihm wird außerdem gezeigt, wie Sie die grundlegenden Funktionen der vorkonfigurierten Lösung für die Remoteüberwachung anzeigen.

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

-   Ein aktives Azure-Abonnement.

    Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion][].

## Bereitstellen einer vorkonfigurierten Lösung für die Remoteüberwachung

1.  Melden Sie sich bei https://www.azureiotsuite.com an, und klicken Sie auf **+**, um eine neue Lösung zu erstellen.

2.  Wählen Sie als Lösungstyp **Remoteüberwachung** aus.

3.  Geben Sie einen **Lösungsnamen** für die vorkonfigurierte Remoteüberwachungslösung ein.

4.  Überprüfen Sie die **Region** und das **Abonnement** für die Bereitstellung der Lösung.

5.  Klicken Sie auf **Lösung erstellen**.

## Anzeigen des Dashboards der Remoteüberwachungslösung

1.  Wenn die Bereitstellung abgeschlossen ist und auf der Kachel für die vorkonfigurierte Lösung **Bereit** angezeigt wird, klicken Sie auf **Starten**, um das Dashboard der Remoteüberwachungslösung auf einer neuen Registerkarte zu öffnen.

2.  Standardmäßig ist das **Dashboard** im linken Menü ausgewählt. Dies ist das Dashboard der Lösung.

## Anzeigen der Lösungsgeräteliste

1.  Klicken Sie im linken Menü auf **Geräte**, um zur Geräteliste für diese Lösung zu navigieren.

2.  Bei der Bereitstellung werden vier simulierte bereitgestellte Geräte angezeigt.

3.  Klicken Sie auf ein **einzelnes Gerät** in der Geräteliste, um die Details dieses Geräts anzuzeigen.

## Senden eines Befehls an ein Gerät

1.  Klicken Sie im Bereich mit den Gerätedetails für das ausgewählte simulierte Gerät auf **Befehl senden**.

2.  Wählen Sie in der Befehlsliste **PingDevice** aus.

3.  Klicken Sie auf **Befehl senden**.

4.  Beachten Sie den Status des Befehls, der im Befehlsverlauf angezeigt wird.

## Hinzufügen eines neuen simulierten Geräts

1.  Klicken Sie auf **←** (Rückwärtspfeil), um zur Geräteliste zurückzukehren.

2.  Klicken Sie in der unteren linken Ecke auf **+ Gerät hinzufügen**, um ein neues Gerät hinzuzufügen.

3.  Klicken Sie für **Simuliertes Gerät** auf **Neues hinzufügen**.

4.  Wählen Sie **Let me define my own Device ID** aus, und fügen Sie einen eindeutigen Geräte-ID-Namen hinzu.

5.  Klicken Sie auf **Erstellen**.

6.  Klicken Sie auf **←** (Rückwärtspfeil), um zur Geräteliste zurückzukehren.

7.  Beachten Sie in der Geräteliste den Status **Wird ausgeführt**.

## Anzeigen und Bearbeiten von Lösungsregeln

1.  Beachten Sie im **Solution Dashboard** die Tabelle **Alarm History**.

2.  Diese Alarme werden durch die in **Regeln** angegebene Ausgabe der Regel **AlarmTemp** ausgelöst.

3.  Klicken Sie im linken Menü auf **Regeln**, um zu den Regeln für diese Lösung zu navigieren.

4.  Bei der Bereitstellung ist eine Regel bereits aktiviert.

5.  Klicken Sie in der Liste der Regeln auf die **Regel**, um die zugehörigen Regeleigenschaften anzuzeigen.

6.  Klicken Sie im Eigenschaftenbereich der Regel auf **Bearbeiten**.

7.  Ändern Sie den **Schwellenwert** in 30, und lassen Sie alle anderen Eigenschaften unverändert.

8.  Klicken Sie auf **Save and View Rules**.

9.  Kehren Sie zur Tabelle **Alarm History** im **Solution Dashboard** zurück, und beachten Sie die durch die aktualisierte Regel verursachte Änderung beim Auslöser.

## Nächste Schritte

Nachdem Sie jetzt eine funktionsfähige vorkonfigurierte Lösung erstellt haben, können Sie mit den folgenden Szenarios fortfahren:

-   [Anleitung zum Anpassen vorkonfigurierter Lösungen][]

-   [Übersicht über IoT Suite][]

[Kostenlose Azure-Testversion]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target=

[Anleitung zum Anpassen vorkonfigurierter Lösungen]: https://azure.microsoft.com/documentation/articles/iot-suite-guidance-on-customizing-preconfigured-solutions/
[Übersicht über IoT Suite]: https://azure.microsoft.com/documentation/articles/iot-suite-overview/

<!---HONumber=Oct15_HO2-->