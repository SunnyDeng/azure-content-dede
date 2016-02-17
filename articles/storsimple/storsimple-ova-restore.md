<properties
   pageTitle="Wiederherstellen aus einer Sicherung des StorSimple Virtual Array (Vorschau)"
   description="Erfahren Sie mehr dazu, wie Sie eine Sicherung des StorSimple Virtual Array wiederherstellen."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/27/2016"
   ms.author="alkohli"/>

# Wiederherstellen aus einer Sicherung des StorSimple Virtual Array (Vorschau)

## Übersicht 

Dieser Artikel bezieht sich nur auf Microsoft Azure StorSimple Virtual Array (auch als „lokales virtuelles StorSimple-Gerät“ oder „virtuelles StorSimple-Gerät“ bezeichnet) mit Version 1.1.1.0 (öffentliche Vorschau). In diesem Artikel wird Schritt für Schritt beschrieben, wie Sie die Wiederherstellung aus einem Sicherungssatz Ihrer Freigaben oder Volumes für das StorSimple Virtual Array durchführen. Im Artikel wird auch ausführlich beschrieben, wie die Wiederherstellung auf Elementebene auf Ihrem StorSimple Virtual Array funktioniert, wenn dies als Dateiserver konfiguriert ist.

> [AZURE.IMPORTANT]
> 
> - Diese öffentliche Vorschauversion ist nur für Evaluierungs- und Planungszwecke im Rahmen einer Bereitstellung bestimmt. Die Installation dieser Vorschau in einer Produktionsumgebung wird nicht unterstützt.
> - Wenn Probleme mit StorSimple Virtual Array auftreten, posten Sie sie im [StorSimple-MSDN-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=StorSimple).


## Wiederherstellen von Freigaben aus einem Sicherungssatz


Bevor Sie versuchen, die Freigaben wiederherzustellen, sollten Sie sich vergewissern, dass auf dem Gerät ausreichend Speicherplatz zum Durchführen dieses Vorgangs vorhanden ist. Führen Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) die folgenden Schritte aus, um die Wiederherstellung aus einer Sicherung durchzuführen.

#### So führen Sie die Wiederherstellung von einer Freigabe durch

1.  Navigieren Sie zum **Sicherungskatalog**. Filtern Sie nach dem entsprechenden Geräte- und Uhrzeitbereich, um nach Ihren Sicherungen zu suchen. Klicken Sie auf das Häkchensymbol ![](./media/storsimple-ova-restore/image1.png), um die Abfrage durchzuführen.


1.  Klicken Sie in der angezeigten Liste mit den Sicherungssätzen auf eine Sicherung, um sie auszuwählen. Erweitern Sie die Sicherung, um die darunter angeordneten Freigaben anzuzeigen. Klicken Sie auf eine Freigabe, um sie für die Wiederherstellung auszuwählen.

2.  Klicken Sie unten auf der Seite auf **Als neu wiederherstellen**.

3.  Der Assistent **Als neue Freigabe wiederherstellen** wird initiiert. Auf der Seite **Namen und Speicherort angeben**:


	1.  Überprüfen Sie den Namen des Quellgeräts. Dies sollte das Gerät mit der wiederherzustellenden Freigabe sein. Die Geräteauswahl ist deaktiviert. Um ein anderes Quellgerät auszuwählen, müssen Sie den Assistenten beenden und den Sicherungssatz erneut auswählen.

	2.  Geben Sie einen Freigabenamen an. Der Freigabename kann 3 bis 127 Zeichen lang sein.

	3.  Überprüfen Sie Größe, Typ und Berechtigungen der Freigabe, die Sie wiederherstellen möchten. Sie können die Freigabeeigenschaften mit dem Windows-Explorer ändern, nachdem die Wiederherstellung abgeschlossen ist.

	4.  Klicken Sie auf das Häkchensymbol ![](./media/storsimple-ova-restore/image1.png).

		![](./media/storsimple-ova-restore/image9.png)

1.  Nachdem der Wiederherstellungsauftrag abgeschlossen ist, wird die Wiederherstellung gestartet, und es wird eine weitere Benachrichtigung angezeigt. Klicken Sie auf **Auftrag anzeigen**, um den Fortschritt der Wiederherstellung zu überwachen. Sie gelangen auf die Seite **Aufträge**.

2.  Hier können Sie den Fortschritt des Wiederherstellungsauftrags verfolgen. Wenn die Wiederherstellung 100 % erreicht hat, navigieren Sie zurück zur Seite **Freigaben** auf Ihrem Gerät.

3.  Sie können nun die neu wiederhergestellte Freigabe in der Liste mit den Freigaben auf Ihrem Gerät anzeigen. Beachten Sie, dass bei der Wiederherstellung derselbe Freigabentyp verwendet wird. Eine mehrstufige Freigabe wird auch mehrstufig wiederhergestellt, und eine lokale Freigabe wird als lokale Freigabe wiederhergestellt.

Sie haben die Gerätekonfiguration nun abgeschlossen und gelernt, wie Sie eine Freigabe sichern und wiederherstellen.


## Wiederherstellen von Volumes aus einem Sicherungssatz


Führen Sie im klassischen Azure-Portal die folgenden Schritte aus, um die Wiederherstellung aus einer Sicherung durchzuführen. Beim Wiederherstellungsvorgang wird die Sicherung auf einem neuen Volume desselben virtuellen Geräts wiederhergestellt. Die Wiederherstellung auf einem anderen Gerät ist nicht möglich.

#### So stellen Sie ein Volume wieder her

1.  Navigieren Sie zum **Sicherungskatalog**. Filtern Sie nach dem entsprechenden Geräte- und Uhrzeitbereich, um nach Ihren Sicherungen zu suchen. Klicken Sie auf das Häkchensymbol ![](./media/storsimple-ova-restore/image1.png), um die Abfrage durchzuführen.

2.  Klicken Sie in der angezeigten Liste mit den Sicherungssätzen auf eine Sicherung, um sie auszuwählen. Erweitern Sie die Sicherung, um die darunter angeordneten Volumes anzuzeigen. Sie müssen diese Volumes auf dem Host und Gerät offline schalten, bevor sie wiederhergestellt werden können. Greifen Sie auf der Seite **Volumes** auf die Volumes zu, und versetzen Sie sie in den Offlinezustand.

3.  Navigieren Sie zurück zur Registerkarte **Sicherungskatalog**, und wählen Sie einen Sicherungssatz aus.

5.  Klicken Sie unten auf der Seite auf **Als neu wiederherstellen**. Der Assistent **Als neues Volumes wiederherstellen** wird gestartet.

1.  Auf der Seite **Namen und Speicherort angeben**:


	1.  Überprüfen Sie den Namen des Quellgeräts. Dies sollte das Gerät mit dem wiederherzustellenden Volume sein. Die Geräteauswahl ist nicht verfügbar. Um ein anderes Quellgerät auszuwählen, müssen Sie den Assistenten beenden und den Sicherungssatz erneut auswählen.

	2.  Geben Sie einen Namen für das Volume an. Der Volumename kann 3 bis 127 Zeichen lang sein.

	3.  Klicken Sie auf das Pfeilsymbol.

		![](./media/storsimple-ova-restore/image12.png)

1.  Wählen Sie auf der Seite **Hosts angeben, die dieses Volume verwenden können** in der Dropdownliste die passenden ACRs aus.

	![](./media/storsimple-ova-restore/image13.png)

1.  Klicken Sie auf das Häkchensymbol ![](./media/storsimple-ova-restore/image1.png). Es wird ein Wiederherstellungsauftrag initiiert, und es wird die folgende Benachrichtigung mit dem Hinweis angezeigt, dass der Auftrag ausgeführt wird.

2.  Nachdem der Wiederherstellungsauftrag abgeschlossen ist, wird die Wiederherstellung gestartet, und es wird eine weitere Benachrichtigung angezeigt. Klicken Sie auf **Auftrag anzeigen**, um den Fortschritt der Wiederherstellung zu überwachen. Sie gelangen auf die Seite **Aufträge**.

3.  Hier können Sie den Fortschritt des Wiederherstellungsauftrags verfolgen. Wenn die Wiederherstellung 100 % erreicht hat, navigieren Sie zurück zur Seite **Volumes** auf Ihrem Gerät.

4.  Sie können nun das neu wiederhergestellte Volume in der Liste mit den Volumes auf Ihrem Gerät anzeigen.

	> [AZURE.NOTE] Beachten Sie, dass bei der Wiederherstellung derselbe Volumetyp verwendet wird. Ein mehrstufiges Volume wird auch als mehrstufiges Volume wiederhergestellt, und ein lokales Volume bleibt ein lokales Volume.

## Wiederherstellung auf Elementebene


Mit dieser Version wird die Wiederherstellung auf Elementebene (Item-Level Recovery, ILR) auf einem virtuellen StorSimple-Gerät eingeführt, das als Dateiserver konfiguriert ist. Mit diesem Feature können Sie die Dateien und Ordner einer Cloudsicherung für alle Freigaben des StorSimple-Geräts präzise wiederherstellen. Benutzer können gelöschte Dateien aus kürzlich erfolgten Sicherungen per Self-Service wiederherstellen.

Jede Freigabe verfügt über den Ordner *.backups*, der die letzten Sicherungen enthält. Der Benutzer kann zur gewünschten Sicherung navigieren, relevante Dateien und Ordner aus der Sicherung kopieren und diese dann wiederherstellen. Es ist dann nicht mehr erforderlich, sich wegen der Wiederherstellung von Dateien aus Sicherungen an den Administrator zu wenden.

1.  Wenn Sie die Wiederherstellung auf Elementebene durchführen, können Sie die Sicherungen per Windows-Explorer anzeigen. Klicken Sie auf die jeweilige Freigabe, für die Sie sich die Sicherungen ansehen möchten. Sie sehen den Ordner *.backups*, der unter der Freigabe erstellt wurde und alle Sicherungen enthält. Erweitern Sie den Ordner *.backups*, um die Sicherungen anzuzeigen. Im Ordner wird dann die Explosionsansicht der gesamten Sicherungshierarchie angezeigt. Diese Ansicht wird bei Bedarf erstellt, was normalerweise nur wenige Sekunden dauert.

	Auf diese Weise werden die letzten fünf Sicherungen angezeigt. Hierzu gehören sowohl die standardmäßig geplanten als auch die manuellen Sicherungen.

	
	-   **Geplante Sicherungen** haben die Bezeichnung „&lt;Gerätename&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC“.

	-   **Manuelle Sicherungen** haben die Bezeichnung „Ad-hoc-YYYYMMDD-HHMMSS-UTC“.
	
		![](./media/storsimple-ova-restore/image14.png)

1.  Identifizieren Sie die Sicherung mit der letzten Version der gelöschten Datei. Der Ordnername enthält zwar in allen obigen Fällen einen UTC-Zeitstempel, aber der Zeitpunkt der Ordnererstellung ist die eigentliche Geräteuhrzeit, zu der die Sicherung gestartet wurde. Verwenden Sie den Ordnerzeitstempel, um die Sicherungen zu finden und zu identifizieren.

2.  Suchen Sie nach dem Ordner oder der Datei, den bzw. die Sie in der im vorherigen Schritt identifizierten Sicherung wiederherstellen möchten. Beachten Sie, dass Sie nur die Dateien oder Ordner anzeigen können, für die Sie Berechtigungen besitzen. Wenn Sie nicht auf bestimmte Dateien oder Ordner zugreifen können, müssen Sie sich an einen Freigabeadministrator wenden. Der Administrator kann Windows-Explorer verwenden, um die Freigabeberechtigungen zu bearbeiten und Ihnen den Zugriff auf die Datei oder den Ordner zu gewähren. Es ist eine empfohlene bewährte Methode, dass es sich beim Freigabeadministrator um eine Benutzergruppe handelt, nicht um einen einzelnen Benutzer.

3.  Kopieren Sie die Datei bzw. den Ordner auf die entsprechende Freigabe auf dem StorSimple-Dateiserver.

![video\_icon](./media/storsimple-ova-restore/video_icon.png) **Video verfügbar**

In diesem Video wird gezeigt, wie Sie Freigaben erstellen, Freigaben sichern und Daten auf einem StorSimple Virtual Array wiederherstellen.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## Nächste Schritte

Erfahren Sie mehr darüber, wie Sie das [StorSimple Virtual Array mit der lokalen Webbenutzeroberfläche verwalten](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0128_2016-->