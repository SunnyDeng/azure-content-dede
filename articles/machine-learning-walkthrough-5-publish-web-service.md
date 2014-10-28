<properties title="Step 5: Publish the Azure Machine Learning web service" pageTitle="Step 5: Publish the Machine Learning web service | Azure" description="Step 5: Publish a scoring experiment in Azure Machine Learning Studio as an ML API web service" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

Dies ist der fünfte Teil der exemplarischen Vorgehensweise [Entwickeln einer Vorhersagelösung mit Azure ML][Entwickeln einer Vorhersagelösung mit Azure ML]:

1.  [Erstellen eines ML-Arbeitsbereichs][Erstellen eines ML-Arbeitsbereichs]
2.  [Hochladen vorhandener Daten][Hochladen vorhandener Daten]
3.  [Erstellen eines neuen Experiments][Erstellen eines neuen Experiments]
4.  [Trainieren und Bewerten der Modelle][Trainieren und Bewerten der Modelle]
5.  **Veröffentlichen des Webdiensts**
6.  [Zugreifen auf den Webdienst][Zugreifen auf den Webdienst]

----------

# Schritt 5: Veröffentlichen des Azure Machine Learning-Webdiensts

Damit das Vorhersagemodell für Dritte von Nutzen sein kann, wird es als Webdienst in Azure veröffentlicht. Ein Benutzer kann eine Reihe von Kreditantragsdaten an den Dienst senden, und der Dienst gibt die Vorhersage des Kreditrisikos zurück.

Gehen Sie hierzu wie folgt vor:

- Bereiten Sie das Experiment auf die Veröffentlichung vor.
- Veröffentlichen Sie es auf einem Staging-Server, wo es getestet werden kann.
- Stufen Sie es auf den Live-Server hoch, wenn es dafür bereit ist.

Bevor Sie fortfahren, sollten Sie eine Kopie dieses Experiments erstellen, um sie zu bearbeiten. So können Sie jederzeit wieder zum Trainingsexperiment zurückkehren, wenn Sie weiter mit den Modellen arbeiten möchten.

1. Klicken Sie unter dem Bereich auf **Speichern unter**.
2. Geben Sie der Experimentkopie einen aussagekräftigen Namen. Standardmäßig wird dem Originalnamen des Experiments „- Kopie“ angehängt. Wählen Sie in diesem Beispiel den Namen „Kreditrisikovorhersage - Bewertungsexperiment“.
3. Klicken Sie auf **OK**.


Sie können jetzt sowohl das Originalexperiment als auch die Kopie in der Liste EXPERIMENTE von ML Studio anzeigen.

![Liste der Experimente][Liste der Experimente]

## Vorbereiten des Bewertungsexperiments
Zwei Vorgänge sind nötig, um das Modell auf die Veröffentlichung als Webdienst vorzubereiten.

Zuerst muss das Experiment von einem *Trainingsexperiment* in ein *Bewertungsexperiment* konvertiert werden. Bis zu diesem Zeitpunkt wurde mit dem Trainieren des Modells experimentiert. Der veröffentlichte Dienst hat jedoch nichts mehr mit Training zu tun – er bewertet die Benutzereingaben. Daher speichern wir eine Kopie des trainierten Modells und löschen dann alle Komponenten des Experiments, die mit dem Training zusammenhängen.

Zweitens akzeptiert der Azure ML-Webdienst Eingaben des Benutzers und gibt ein Ergebnis zurück. Also müssen diese Eingabe- und Ausgabepunkte in unserem Experiment identifiziert werden.

### Konvertieren von einem Trainingsexperiment in ein Bewertungsexperiment
In unserem Beispiel entscheiden wir, dass das Boosted Tree-Modell sich besser zur Verwendung eignet. Als erstes werden also die SVM-Trainingsmodelle entfernt.

1. Löschen Sie **Zweiklassige Support Vector Machine**.
2. Löschen Sie die damit verbundenen Module **Modell trainieren** und **Modell bewerten**.
3. Löschen Sie das Modul **Daten durch Skalierung transformieren**.

Jetzt wird das trainierte Boosted Tree-Modell gespeichert. Anschließend können die verbleibenden Module im Experiment, die zum Trainieren verwendet wurden, gelöscht und mit dem trainierten Modell ersetzt werden.

1. Klicken Sie mit der rechten Maustaste auf den Ausgabeport des verbleibenden Moduls **Modell trainieren**, und wählen Sie **Als trainiertes Modell speichern** aus.
2. Geben Sie einen Namen und eine Beschreibung für das trainierte Modell ein. In diesem Beispiel erhält es den Namen „Vorhersage des Kreditrisikos“.

	>**Hinweis**: Nach dem Speichern des trainierten Modells wird es in der Modulpalette angezeigt und steht für andere Experimente zur Verfügung.

3. Suchen Sie dieses Modell in der Modulpalette, indem Sie „Kreditrisiko“ im Feld **Suche** eingeben, und ziehen Sie dann das trainierte Modell **Vorhersage des Kreditrisikos** in den Experimentbereich.
4. Löschen Sie die Module **Zweiklassiger Boosted Decision Tree** und **Modell trainieren**.
5. Verbinden Sie die Ausgabe des Modells **Vorhersage des Kreditrisikos** mit der linken Eingabe des Moduls **Modell bewerten**.

Jetzt liegt eine gespeicherte, trainierte Version des Modells in unserem Experiment anstelle der ursprünglichen Trainingsmodule vor.

Das Experiment enthält noch weitere Komponenten, die nur zu Trainigszwecken und zum Evaluieren der beiden Modellalgorithmen hinzugefügt wurden. Sie können ebenfalls entfernt werden:

- **Aufteilen**
- Beide Module **R-Skript ausführen**
- **Modell evaluieren**

Noch etwas: Dies betrifft die Originalkreditkartendaten in der Spalte „Kreditrisiko“. Diese Spalte wurde durch das Modul **Modell trainieren** verarbeitet, um das Modell für die Vorhersage dieser Werte zu trainieren. Nachdem das Modell jetzt trainiert ist, soll diese Spalte aber nicht mehr verarbeitet werden – dieser Wert wird jetzt vom trainierten Modell vorhergesagt. Um die Spalte aus dem Datenfluss zu entfernen, wird das Modul **Projektspalte** verwendet.

1. Ziehen Sie das Modul **Projektspalte** in den Bereich.
2. Verbinden Sie dieses Modul mit der Ausgabe des Moduls **Metadaten-Editor** (nachdem jetzt die Module **Aufteilen** und **R-Skript ausführen** entfernt wurden).
3. Wählen Sie das Modul **Projektspalten** aus und klicken Sie auf **Spaltenauswahl starten**.
4. Belassen Sie „Alle Spalten“ in der Dropdown-Liste.
5. Klicken Sie auf das Pluszeichen (+), um wird eine neue Dropdown-Zeile zu erstellen.
6. Wählen Sie in dieser neuen Dropdown-Liste die Option „Spaltennamen ausschließen“, und geben Sie „Kreditrisiko“ in das Textfeld ein (Sie können die Spalte auch durch ihre Spaltennummer, 21, angeben).
7. Klicken Sie auf **OK**.

	>Tipp: Die Spaltenauswahl folgt der Logik der Dropdown-Listen in der Reihenfolge ihrer Anzeige. In diesem Fall wurde das Modul **Projektspalten** angewiesen, alle Spalten außer der Spalte „Kreditrisiko“ zu verarbeiten. Hätten wir die erste Dropdown-Liste ausgelassen, würde das Modul gar keine Spalte verarbeiten.

8. Verbinden Sie die Ausgabe des Moduls **Projektspalten** mit der rechten Eingabe des Moduls **Modell bewerten**.

Unser Experiment sollte nun wie folgt aussehen:

![Bewerten des trainierten Modells][Bewerten des trainierten Modells]

### Auswählen der Eingabe und Ausgabe des Diensts
Im Originalmodell wurden die zu bewertenden Daten am rechten Eingabeport („Dataset“) des Moduls **Modell bewerten** eingegeben, und das bewertete Ergebnis wurde am Ausgabeport angezeigt („Bewertetes Dataset“). Wenn der Dienst ausgeführt wird, sollen für die Benutzerdaten und die Ergebnisse die gleichen Ports verwendet werden..

1. Klicken Sie mit der rechten Maustaste auf den rechten Eingabeport des Moduls **Modell bewerten**, und wählen Sie **Als Veröffentlichungseingabe festlegen** aus. Die Benutzerdaten müssen alle Daten des Funktionsvektors enthalten.

	>**Tipp** - Wenn Sie die Benutzerdaten bearbeiten müssen, bevor sie in das Bewertungsmodul eingegeben werden (vergleichbar mit der Art und Weise, wie das Modul **Daten durch Skalierung transformieren** zum Vorbereiten der Daten für das SVM-Modell verwendet wurde), belassen Sie das Modul einfach im Webdienst, und legen Sie den Diensteingang auf den Eingangsport dieses Moduls fest.

2. Klicken Sie mit der rechten Maustaste auf den Ausgabeport, und wählen Sie **Als Veröffentlichungsausgabe festlegen** aus. Die Ausgabe des Moduls „Modell bewerten“ wird vom Dienst zurückgegeben. Dazu gehören der Funktionsvektor sowie die Kreditrisikovorhersage und der Bewertungswahrscheinlichkeitswert.

	>**Tipp** - Wenn der Webdienst nur einen Teil dieser Daten zurückgeben soll – wenn Sie z. B. nicht den ganzen Funktionsvektor zurückgeben möchten - können Sie ein Modul **Projektspalten** nach dem Modul **Modell bewerten** hinzufügen, es so konfigurieren, dass die nicht gewünschten Spalten ausgeschlossen werden, und dann die Ausgabe des Moduls **Projektspalten** auf die Webdienstausgabe festlegen.


>**Hinweis**: Vielleicht fragen Sie sich, weshalb das Dataset „UCI German Credit Card Data“ und seine zugeordneten Module in Verbindung mit dem Modul **Modell bewerten** belassen haben. Der Dienst verwendet die Daten des Benutzers, nicht das Originaldataset. Weshalb also die Verbindung belassen?

Es stimmt zwar, dass der Dienst die Originalkreditkartendaten nicht benötigt. Er benötigt aber das Schema für diese Daten, darunter Angaben zur Anzahl der vorhandenen Spalten, und welche Spalten numerisch sind. Diese Schemainformationen sind erforderlich, um die Benutzerdaten zu interpretieren. Wir lassen diese Komponenten verbunden, damit das Bewertungsmodul über das Datasetschema verfügt, wenn der Dienst ausgeführt wird. Es werden nicht die Daten verwendet, sondern nur das Schema.

Führen Sie das Experiment ein letztes Mal aus (klicken Sie auf **AUSFÜHREN**). Wenn Sie überprüfen möchten, ob das Modell noch funktioniert, klicken Sie mit der rechten Maustaste auf die Ausgabe des Moduls **Modell bewerten**, und wählen Sie **Visualisieren** aus. Sie sehen, dass die Originaldaten zusammen mit dem Wert für das Kreditrisiko („Bewertete Beschriftungen“)' und dem Bewertungswahrscheinlichkeitswert („Bewertete Wahrscheinlichkeiten“) angezeigt werden.

## Veröffentlichen des Webdiensts
Um einen aus dem Experiment abgeleiteten Webdienst zu veröffentlichen, klicken Sie auf **WEBDIENST VERÖFFENTLICHEN** unter dem Bereich, und klicken Sie bei Aufforderung auf **JA**. ML Studio veröffentlicht das Experiment als Webdienst auf dem ML-Staging-Server und führt Sie zum Dienst-Dashboard.

>**Tipp** - Sie können den Webdienst nach der Veröffentlichung aktualisieren. Wenn Sie zum Beispiel das Modell ändern möchten, bearbeiten Sie einfach das zuvor gespeicherte Trainingsexperiment, passen Sie die Modellparameter an und speichern Sie das trainierte Modell (wodurch das zuvor gespeicherte Modell überschrieben wird). Wenn Sie das Bewertungsexperiment noch einmal öffnen, wird ein Hinweis angezeigt, dass etwas geändert wurde (Ihr Trainingsmodell), und Sie können das Experiment aktualisieren. Wenn Sie das Experiment erneut veröffentlichen, wird der Webdienst ersetzt und das aktualisierte Modell verwendet.

Sie können den Dienst konfigurieren, indem Sie auf die Registerkarte **KONFIGURATION** klicken. Hier können Sie den Dienstnamen ändern (er erhält standardmäßig den Namen des Experiments) und eine Beschreibung hinzufügen. Sie können auch benutzerfreundlichere Beschriftungen für die Eingabe- und Ausgabespalten festlegen.

Der Switch **BEREIT FÜR DIE PRODUKTION?** wird weiter unten besprochen.

## Testen des Webdiensts
Klicken Sie auf der Seite **DASHBOARD** auf den Link **Test** unter **Staging-Dienste**. Ein Dialogfeld wird geöffnet, das nach den Eingabedaten für den Dienst fragt. Dies sind die gleichen Spalten, die im Originaldataset „German Credit Risk“ angezeigt wurden.

Geben Sie eine Reihe von Daten ein, und klicken Sie auf **OK**.

Die vom Webdienst generierten Ergebnisse werden jetzt unten im Dashboard angezeigt. So, wie der Dienst konfiguriert wurde, werden die angezeigten Ergebnisse vom Bewertungsmodul generiert.

## Hochstufen des Webdiensts auf den Live-Server
Bis jetzt wurde der Dienst auf dem ML-Staging-Server ausgeführt. Wenn er live gehen soll, können Sie anfordern, dass er auf den Live-Server hochgestuft wird.

Klicken Sie auf der Registerkarte **KONFIGURATION** auf „JA“ neben **BEREIT FÜR DIE PRODUKTION?** Damit wird eine Benachrichtigung an den IT-Administrator gesendet, dass dieser Webdienst für den Live-Server bereit ist. Der Administrator kann ihn dann auf den Live-Server hochstufen.

![Hochstufen des Diensts in die Live-Umgebung][Hochstufen des Diensts in die Live-Umgebung]

----------

**Nächster Schritt: [Zugreifen auf den Webdienst][Zugreifen auf den Webdienst]**

[Entwickeln einer Vorhersagelösung mit Azure ML]: ../machine-learning-walkthrough-develop-predictive-solution/
[Erstellen eines ML-Arbeitsbereichs]: ../machine-learning-walkthrough-1-create-ml-workspace/
[Hochladen vorhandener Daten]: ../machine-learning-walkthrough-2-upload-data/
[Erstellen eines neuen Experiments]: ../machine-learning-walkthrough-3-create-new-experiment/
[Trainieren und Bewerten der Modelle]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[Zugreifen auf den Webdienst]: ../machine-learning-walkthrough-6-access-web-service/
[Liste der Experimente]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[Bewerten des trainierten Modells]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[Hochstufen des Diensts in die Live-Umgebung]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
