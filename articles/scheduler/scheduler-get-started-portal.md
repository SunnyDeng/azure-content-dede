<properties
 pageTitle="Erste Schritte mit Azure Scheduler im Azure-Portal | Microsoft Azure"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# Erste Schritte mit Azure Scheduler im klassischen Azure-Portal

## Erste Schritte

Sie können ganz einfach Aufträge und Auftragssammlungen für Azure Scheduler erstellen. Dieses Tutorial erläutert das Erstellen der Auftragssammlung zum Speichern von Aufträgen sowie das Erstellen eines Auftrags in einer Auftragssammlung und bietet eine Übersicht über die verfügbaren Aufgaben für die Auftragsüberwachung und -verwaltung. In diesem Lernprogramm werden keine Azure-Vorkenntnisse vorausgesetzt.

Wenn Sie das klassische Azure-Portal zum ersten Mal öffnen, ist automatisch die Registerkarte **ALLE ELEMENTE** geöffnet. Die Spalten auf der Registerkarte **ALL ITEMS** können sortiert werden. Klicken Sie zum Anzeigen Ihrer Scheduler-Aufträge und -Auftragssammlungen auf die Registerkarte **SCHEDULER**.

![][1]

## Erstellen einer Auftragssammlung und eines Auftrags

1.  Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) an.  

2.  Klicken Sie auf **App Services** > **Neu erstellen** > **Scheduler** und dann auf **Benutzerdefiniert erstellen**. <br /><br /> ![][2]

3.  Wählen Sie unter **Auftragssammlung** den Namen der vorhandenen Auftragssammlung in der Dropdownliste **Auftragssammlung** aus. Wenn noch keine Auftragssammlung vorhanden ist, der Sie den Auftrag hinzufügen möchten, wählen Sie **Neu erstellen**, und geben Sie einen Namen für Ihre neue Auftragssammlung ein.<br /><br /> ![][3]

4.  Wählen Sie unter **Region** die geografische Region für die Auftragssammlung aus.

5.  Klicken Sie auf den Pfeil, um die Auftragssammlung zu erstellen und mit der Auftragserstellung zu beginnen.

6.  In diesem Beispiel erstellen wir einen einfachen Auftrag mit einer GET-Anforderung für „http://www.microsoft.com/“. Legen Sie auf dem Bildschirm **Auftragsaktion** die folgenden Werte für die angeforderten Formularfelder fest:

    1.  **Name:** ` getmicrosoft`  

    2.  **Aktionstyp:** ` HTTP`

    3.  **Methode:** ` GET`

    4.  **URI:** ` http://www.microsoft.com`

   	![][4]

7.  Legen Sie nach dem Erstellen eines Auftrags einen Zeitplan fest. Der Auftrag kann natürlich auch als einmaliger Auftrag definiert werden, in diesem Beispiel verwenden wir aber einen Wiederholungszeitplan. Auf einigen Screenshots in diesem Lernprogramm ist zwar zur Veranschaulichung eine Wiederholung von einer Minute angegeben, wir verwenden aber eine Wiederholung von 12 Stunden.

    1.  **Wiederholen alle:** ` 12 Hours`  

    2.  **Wird gestartet:** ` Now`

    3.  **Endet am:** ` Select date 2 days after current day and any time`

   	![][5]

8.  Klicken Sie auf **OK**. Die Erstellung des Auftrags und der Auftragssammlung kann eine Weile dauern. Sie können die Benachrichtigungen unten im Portal überwachen, um den Status zu überprüfen.

   	![][6]

   	Nach der Erstellung des Auftrags und der Auftragssammlung erhalten Sie jeweils eine Benachrichtigung, dass der Auftrag bzw. die Auftragssammlung erstellt wurde. Der Auftrag wird im Scheduler-Abschnitt unter „Aufträge“ aufgeführt. Die Auftragssammlung wird im Abschnitt „Auftragssammlungen“ aufgeführt. Informationen zum Konfigurieren zusätzlicher erweiterter Einstellungen für den Auftrag finden Sie weiter unten im Abschnitt „Konfigurieren eines Auftrags“.

   	![][7]

## Verwalten und Überwachen von Auftragssammlungen und Aufträgen

Erstellte Auftragssammlungen werden im Hauptverwaltungsbildschirm von Scheduler angezeigt.

![][8]

Wenn Sie auf eine Auftragssammlung klicken, wird ein neues Fenster mit folgenden Optionen angezeigt:

1.  Dashboard  

2.  Skalieren

3.  Verlauf

4.  Aufträge

Diese Registerkarten werden im Anschluss ausführlicher beschrieben:

### Dashboard

Wenn Sie auf den Namen Ihrer Auftragssammlung klicken, wird die Registerkarte **Dashboard** angezeigt. Auf dem Dashboard werden die folgenden Informationen angezeigt:

![][9]

#### „Überblick Auftragsnutzung“ und „Überblick über Ausführung und Nutzung“

Eine Tabelle und eine Reihe von Diagrammen mit einer festen Liste von Metriken. Diese Metriken liefern Echtzeitwerte zum Status Ihrer Auftragssammlung, wie etwa:

1.  Aktuelle Aufträge  

2.  Abgeschlossene Aufträge

3.  Fehlerhafte Aufträge

4.  Aktivierte Aufträge

5.  Deaktivierte Aufträge

6.  Auftragsausführungen

#### Auf einen Blick

Eine Tabelle mit einer festen Liste mit Status- und Einstellungsmetriken. Diese Metriken liefern Echtzeitwerte zu Status und Einstellungen für Ihre Auftragssammlung, wie etwa:

1.  Status  

2.  Region

3.  Anzahl von Fehlern

4.  Anzahl von Vorkommensfehlern

5.  URI

### Skalieren

Auf der Registerkarte **Skalieren** können Sie die Einstellungen und die Dienstebene für Scheduler ändern.

![][10]

#### Allgemein

Hier sehen Sie, ob Sie den Tarif **Free** oder den Tarif **Standard** verwenden.

#### Kontingente

Azure Scheduler implementiert Kontingente auf der Grundlage mehrerer Bedingungen. In diesem Abschnitt können Sie die Kontingentschwellenwerte anzeigen und ändern. Standardmäßig ist eine Gruppe von Kontingenten konfiguriert. Die Grenzwerte dieser Kontingenteinstellungen werden durch Ihren Plan beschränkt. Eine Änderung des Plans kann sich auf den Preis auswirken. Kontingente können zur Skalierung von Scheduler geändert werden. Folgende Optionen sind verfügbar:

1.  Maximale Auftragsanzahl  

2.  Maximale Häufigkeit

3.  Maximales Intervall

### Verlauf

Die Registerkarte **Verlauf** enthält folgende Informationen für den ausgewählten Auftrag:

![][11]

#### Verlaufstabelle

Eine Tabelle mit ausgewählten Metriken für jede Auftragsausführung im System für den ausgewählten Auftrag. Diese Metriken liefern Echtzeitwerte zum Status von Scheduler.

#### Verfügbare Metriken

Die folgenden Leistungsindikatoren/Metriken stehen zur Verfügung:

1.  Status  

2.  Details

3.  Wiederholungsversuch

4.  Anzahl von Ausführungen (1., 2., 3. usw..)

5.  Zeitstempel der Ausführung

Durch Klicken auf **Verlaufsdetails anzeigen** können Sie die vollständige Antwort für jede Ausführung anzuzeigen. In diesem Dialogfeld können Sie auch die Antwort in die Zwischenablage kopieren.

![][12]

### Aufträge

Die Registerkarte „Aufträge“ zeigt folgende Informationen zur Überwachung des Ausführungsverlaufs von Aufträgen:

![][13]

#### Auftragstabelle

Eine Tabelle mit ausgewählten Metriken für jeden Auftrag im System. Diese Metriken liefern Echtzeitwerte zum Status von Scheduler.

#### Deaktivieren, Aktivieren oder Löschen eines Auftrags

Wenn Sie auf einen Auftragsnamen klicken, können Sie den Auftrag aktivieren, deaktivieren oder löschen. Gelöschte Aufträge können unter Umständen nicht wiederhergestellt werden.

#### Verfügbare Metriken

Folgende Leistungsindikatoren und Metriken stehen zur Verfügung:

1.  Name  

2.  Letzte Ausführung

3.  Nächste Ausführung

4.  Status

5.  Häufigkeit

6.  Fehler

7.  Ausfälle

8.  Ausführungen

9.  Aktionstyp

### Konfigurieren eines Auftrags

Wenn Sie auf dem Bildschirm **Aufträge** auf einen Auftrag klicken, können Sie diesen konfigurieren. So können Sie zusätzliche erweiterte Einstellungen konfigurieren, die im Schnellerstellungs-Assistenten nicht zur Verfügung stehen. Klicken Sie zum Konfigurieren eines Auftrags auf dem Bildschirm **Aufträge** neben dem Namen des Auftrags auf den Pfeil nach rechts.

Auf der Auftragskonfigurationsseite können Sie die Einstellungen für den Auftrag aktualisieren. Im Anschluss sehen Sie die Auftragskonfigurationsseite für HTTP- und HTTPS-Aufträge. Bei HTTP- und HTTPS Auftragsaktionstypen können Sie für die Methode ein beliebiges zulässiges HTTP-Verb festlegen. Darüber hinaus können Sie die Header sowie grundlegende Authentifizierungsinformationen hinzufügen, löschen oder ändern.

![][14]

Im Anschluss sehen Sie die Auftragskonfigurationsseite für Speicherwarteschlangenaufträge. Bei Speicherwarteschlangen-Aktionstypen können Sie das Speicherkonto, den Warteschlangennamen, das SAS-Token und den Text ändern. Der (hier nicht dargestellte) Zeitplanabschnitt entspricht dem Zeitplanabschnitt für HTTP-/HTTPS-Auftragsaktionstypen.

![][15]

Bei allen Aktionstypen können Zeitplan und Wiederholungsverhalten geändert werden. Sie können die Startzeit (Datum und Uhrzeit), den Wiederholungszeitplan und die Endzeit (Datum und Uhrzeit) ändern (sofern es sich um einen wiederkehrenden Auftrag handelt). Klicken Sie anschließend auf **Speichern**, um die vorgenommenen Änderungen zu speichern, oder auf **Verwerfen**, um die Änderungen zu verwerfen.

## Weitere Informationen

 [Was ist Azure Scheduler?](scheduler-intro.md)

 [Scheduler-Konzepte, -Terminologie und -Entitätshierarchie](scheduler-concepts-terms.md)

 [Pläne und Abrechnung in Azure Scheduler](scheduler-plans-billing.md)

 [Erstellen komplexer Zeitpläne und erweiterter Serien mit Azure Scheduler](scheduler-advanced-complexity.md)

 [Scheduler-REST-API – Referenz](https://msdn.microsoft.com/library/mt629143)

 [Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)

 [Scheduler – hohe Verfügbarkeit und Zuverlässigkeit](scheduler-high-availability-reliability.md)

 [Einschränkungen, Standardwerte und Fehlercodes für Scheduler](scheduler-limits-defaults-errors.md)

 [Ausgehende Authentifizierung von Scheduler](scheduler-outbound-authentication.md)



[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

<!---HONumber=AcomDC_0128_2016-->