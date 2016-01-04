<properties
	pageTitle="Erste Schritte mit vorkonfigurierten Lösungen | Microsoft Azure"
	description="In diesem Tutorial erlernen Sie das Bereitstellen einer vorkonfigurierten Azure IoT Suite-Lösung."
	services=""
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/03/2015"
     ms.author="dobett"/>

# Tutorial: Erste Schritte mit den vorkonfigurierten IoT-Lösungen

## Einführung

[Vorkonfigurierte Lösungen][lnk-preconfigured-solutions] der Azure IoT- Suite kombinieren mehrere Azure IoT-Dienste, um durchgängige Lösungen bereitzustellen, die allgemeine IoT-Unternehmensszenarien implementieren.

Dieses Tutorial zeigt, wie Sie eine vorkonfigurierte Lösung für die *Remoteüberwachung* bereitstellen. Außerdem lernen Sie die grundlegenden Funktionen der vorkonfigurierten Lösung für die Remoteüberwachung kennen.

Sie benötigen ein aktives Azure-Abonnement, um dieses Lernprogramm auszuführen.

> [AZURE.NOTE]Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion][lnk_free_trial].

## Bereitstellen einer vorkonfigurierten Lösung für die Remoteüberwachung

1.  Melden Sie sich mit den Anmeldeinformationen für Ihr Azure-Konto bei [azureiotsuite.com][lnk-azureiotsuite] an, und klicken Sie auf **+**, um eine neue Lösung zu erstellen.

2.  Klicken Sie auf der Kachel **Remoteüberwachung** auf **Auswählen**.

3.  Geben Sie einen **Lösungsnamen** für Ihre vorkonfigurierte Remoteüberwachungslösung ein.

4.  Wählen Sie die **Region** und das **Abonnement**, die Sie zum Bereitstellen der Lösung verwenden möchten.

5.  Klicken Sie auf **Lösung erstellen**, um den Bereitstellungsprozess zu beginnen. Dies dauert normalerweise einige Minuten.

## Anzeigen des Dashboards der Remoteüberwachungslösung

1.  Wenn die Bereitstellung abgeschlossen ist und auf der Kachel für die vorkonfigurierte Lösung **Bereit** angezeigt wird, klicken Sie auf **Starten**, um das Portal der Remoteüberwachungslösung auf einer neuen Registerkarte zu öffnen.

    ![][img-launch-solution]

2.  Standardmäßig zeigt das Lösungsportal das *Lösungsdashboard* an. Sie können im linken Menü andere Ansichten auswählen.

    ![][img-dashboard]

## Anzeigen der Lösungsgeräteliste

1.  Klicken Sie im linken Menü auf **Geräte**, um die *Geräteliste* für diese Lösung anzuzeigen.

    ![][img-devicelist]

2.  Wie Sie sehen, gibt es vier simulierte Geräte, die im Rahmen des Bereitstellungsprozesses erstellt werden.

3.  Klicken Sie auf ein Gerät in der Geräteliste, um die Gerätedetails anzuzeigen.

    ![][img-devicedetails]

## Senden eines Befehls an ein Gerät

1.  Klicken Sie im Bereich mit den Gerätedetails für das ausgewählte Gerät auf **Befehle**.

    ![][img-devicecommands]

2.  Wählen Sie in der Befehlsliste **PingDevice** aus.

3.  Klicken Sie auf **Befehl senden**.

4.  Sie sehen den Status des Befehls im Befehlsverlauf.

    ![][img-pingcommand]

## Hinzufügen eines neuen simulierten Geräts

1.  Navigieren Sie zurück zur Geräteliste.

2.  Klicken Sie in der unteren linken Ecke auf **+ Gerät hinzufügen**, um ein neues Gerät hinzuzufügen.

    ![][img-adddevice]

3.  Klicken Sie auf der Kachel **Simuliertes Gerät** auf **Neues hinzufügen**.

    ![][img-addnew]

4.  Wählen Sie **Eigene Geräte-ID definieren** aus, und geben Sie einen eindeutigen Geräte-ID-Namen wie **mydevice\_01** ein.

5.  Klicken Sie auf **Erstellen**.

    ![][img-definedevice]

6. Klicken Sie in Schritt 3 von **Simuliertes Gerät hinzufügen** auf **Fertig**, um zur Geräteliste zurückzukehren.

7.  Ihr Gerät wird mit **Wird ausgeführt** in der Geräteliste angezeigt.

    ![][img-runningnew]

## Anzeigen und Bearbeiten von Lösungsregeln

1.  Kehren Sie zum Lösungsdashboard zurück, und zeigen Sie die Kachel **Alarmverlauf** an.

    ![][img-alarmhistory]

2.  Die Regel **AlarmTemp** löst diese Alarme aus.

3.  Klicken Sie im linken Menü auf **Regeln**, um die Regeln für diese Lösung anzuzeigen.

    ![][img-rules]

4.  Die vorkonfigurierte Lösung stellt zwei Regeln bereit.

5.  Klicken Sie in der Liste der Regeln auf **Temperatur**, um die Regeleigenschaften anzuzeigen.

6.  Klicken Sie im Eigenschaftenbereich der Regel auf **Bearbeiten**.

    ![][img-displayrule]

7.  Ändern Sie den **Schwellenwert** in 30, und lassen Sie alle anderen Eigenschaften unverändert.

8.  Klicken Sie auf **Save and View Rules**.

    ![][img-editrule]

9.  Kehren Sie zur Tabelle **Alarmverlauf** im **Lösungsdashboard** zurück, und beachten Sie die durch die aktualisierte Regel verursachte Verhaltensänderung.

    ![][img-newhistory]

## Nächste Schritte

Nachdem Sie jetzt eine funktionsfähige vorkonfigurierte Lösung erstellt haben, können Sie mit den folgenden Szenarios fortfahren:

-   [Anleitung zum Anpassen vorkonfigurierter Lösungen][lnk-customize]
-   [Übersicht über die vorkonfigurierte Lösung für vorhersagbaren Wartungsbedarf][lnk-predictive]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-alarmhistory]: media/iot-suite-getstarted-preconfigured-solutions/alarmhistory.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-displayrule]: media/iot-suite-getstarted-preconfigured-solutions/displayrule.png
[img-editrule]: media/iot-suite-getstarted-preconfigured-solutions/editrule.png
[img-newhistory]: media/iot-suite-getstarted-preconfigured-solutions/newhistory.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_1210_2015-->