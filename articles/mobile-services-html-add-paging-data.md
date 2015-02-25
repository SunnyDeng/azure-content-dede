<properties pageTitle="Hinzufügen von Paging für Daten (HTML 5) | Mobile Developer Center" description="Erfahren Sie, wie Sie mithilfe des Auslagerns die Menge der von Ihrer HTML-App von Mobile Services zurückgegebenen Daten verwalten können." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"/>

# Verfeinern von Mobile Services-Abfragen mit Paging

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre HTML-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Take** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

> [AZURE.NOTE] Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten]. 

1. Führen Sie eine der folgenden Befehlsdateien aus dem **server**-Unterordner des Projekts aus, das Sie geändert haben, als Sie das Lernprogramm [Erste Schritte mit Daten] abgeschlossen haben.

	+ **launch-windows** (Windows-Computer) 
	+ **launch-mac.command** (Mac OS X-Computer)
	+ **launch-linux.sh** (Linux-Computer)

	> [AZURE.NOTE] Bei einem Windows-Computer geben Sie "R" ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.

	Daraufhin wird ein Webserver auf Ihrem lokalen Computer zum Hosten der App gestartet.

1. Öffnen Sie <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in einem Webbrowser, geben Sie Text unter **Neue Aufgabe hinzufügen** ein, und klicken Sie auf **Hinzufügen**.

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält. 

2. Ersetzen Sie in der Datei app.js die Definition der  `query`-Variablen in der **refreshTodoItems**-Methode durch den folgenden Code:

       
        var query = todoItemTable.where({ complete: false }).take(3);

  	Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

3. Aktualisieren Sie die Seite in Ihrem Webbrowser.

  	Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden. 

4. (Optional) Sie können die URI der Anfrage an den mobilen Dienst anzeigen, indem Sie Software zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler]. 

   	Beachten Sie, dass die **take(3)**-Methode im Abfrage-URI in die Abfrageoption **$top=3** übersetzt wurde.

5. Ersetzen Sie die Abfrage durch den folgenden Code:
            
        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

3. Aktualisieren Sie die Seite in Ihrem Webbrowser.

   	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    > [AZURE.NOTE] Dieses Lernprogramm übergibt zur Vereinfachung fest codierte Werte an die **Take**- und **Skip**-Methoden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.  Alternativ können Sie die **includeTotalCount**-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

6. (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an. 

   	Beachten Sie, dass die **skip(3)**-Methode im Abfrage-URI in die Abfrageoption **$skip=3** übersetzt wurde.

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogrammreihe über Grundlagen des Arbeitens mit Daten in Mobile Services. Lernen Sie unter [Erste Schritte mit der Authentifizierung] , wie Sie Benutzer Ihrer App authentifizieren können. Erfahren Sie mehr über die Verwendung von Mobile Services mit HTML/JavaScript unter [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz].

<!-- Anchors. -->

[Nächste Schritte]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-html
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-html
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-html


[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-html-js-client



<!--HONumber=42-->
