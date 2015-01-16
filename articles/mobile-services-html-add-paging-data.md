<properties urlDisplayName="Add paging to data (HTML5)" pageTitle="Hinzufügen von Paging für Daten (HTML 5) | Mobile Developer Center" metaKeywords="" description="Erfahren Sie, wie Sie mithilfe des Auslagerns die Menge der von Ihrer HTML-App von Mobile Services zurückgegebenen Daten verwalten können." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Verfeinern von Mobile Services-Abfragen mit Paging

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre HTML-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Take** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

<div class="dev-callout"><b>Hinweis</b>
<p>Um einen Datenüberlauf auf mobilen Geräteclients zu verhindern, implementiert Mobile Services eine automatische Seitenbegrenzung, die standardmäßig die Antwort auf 50 Elemente begrenzt. Durch Angeben der Seitengröße können Sie explizit bis zu 1.000 Elemente in der Antwort anfordern.</p>
</div>

Dieses Lernprogramm baut auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten] auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst mindestens das erste Lernprogramm der Reihe Arbeiten mit Daten abschließen, [Erste Schritte mit Daten]. 

1. Starten Sie eine der folgenden Befehlsdateien aus dem Unterordner **server** des Projekts aus, das Sie während der Bearbeitung des Lernprogramms [Erste Schritte mit Daten] geändert haben.

	+ **launch-windows** (Windows-Computer) 
	+ **launch-mac.command** (Mac OS X-Computer)
	+ **launch-linux.sh** (Linux-Computer)

	<div class="dev-callout"><b>Hinweis</b>
		<p>Bei einem Windows-Computer geben Sie `R` ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.</p>
	</div>

	Daraufhin wird ein Webserver auf Ihrem lokalen Computer zum Hosten der App gestartet.

1. Navigieren Sie in einem Webbrowser zu <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, geben Sie in **Add new task** Text ein, und klicken Sie auf **Add**.

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält. 

2. Ersetzen Sie in app.js die vorhandene Definition der `query`-Variable in der **refreshTodoItems**-Methode durch den folgenden Code:

       
        var query = todoItemTable.where({ complete: false }).take(3);

  	Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

3. Aktualisieren Sie die Seite in Ihrem Webbrowser.

  	Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden. 

4. (Optional) Zeigen Sie den URI der mithilfe der Nachrichtenprüfsoftware an den mobilen Dienst gesendeten Anforderung an, z. B. als Browser-Entwicklerltools oder [Fiddler]. 

   	Beachten Sie, dass die **take(3)**-Methode in der Abfrage-URI in die Abfrageoption **$top=3** übersetzt wurde.

5. Ersetzen Sie die Abfrage durch den folgenden Code:
            
        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

3. Aktualisieren Sie die Seite in Ihrem Webbrowser.

   	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die drei darauffolgenden zurück. Dabei handelt es sich effektiv um die zweite "Seite" von Daten, die Seitengröße beträgt dabei drei Elemente.

    <div class="dev-callout"><b>Hinweis</b>
    <p>In diesem Lernprogramm werden der Einfachheit halber fest codierte Werte an die Methoden<strong>Take</strong> und <strong>Skip</strong> übergeben. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.  Alternativ können Sie die   <strong>includeTotalCount</strong>-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.</p>
    </div>

6. (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an. 

   	Beachten Sie, dass die **Überspringen(3)**-Methode in der Abfrage-URI in die Abfrageoption **$skip=3** übersetzt wurde.

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogrammreihe über Grundlagen des Arbeitens mit Daten in Mobile Services. Lernen Sie unter [Erste Schritte mit der Authentifizierung ], wie Sie Benutzer Ihrer App authentifizieren können. Weitere Informationen zur Verwendung von mobilen Diensten mit HTML/JavaScript finden Sie unter [Mobile Services HTML/JavaScript How-to Conceptual Reference]

<!-- Anchors. -->

[Nächste Schritte]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-html
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-html
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-html


[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-html-js-client

