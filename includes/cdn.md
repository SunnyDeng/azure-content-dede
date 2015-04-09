# Verwenden von CDN für Azure

Das Azure Content Delivery Network (CDN) bietet Entwicklern eine
globale Lösung für die Übermittlung breitbandiger Inhalte. Dazu werden Blobs
und statische Inhalte von Serverinstanzen an physischen Knotenpunkten in den USA,
Europa, Asien, Australien und Südamerika zwischengespeichert. Eine aktuelle Liste von
CDN-Knotenstandorten finden Sie unter [Standorte der Azure-CDN-Knoten].

Diese Aufgabe umfasst die folgenden Schritte:

* [Schritt 1: Erstellen eines Speicherkontos](#Step1)
* [Schritt 2: Erstellen eines neuen CDN-Endpunkts für das Speicherkonto](#Step2)
* [Schritt 3: Auf die CDN-Inhalte zugreifen](#Step3)
* [Schritt 4: Entfernen von CDN-Inhalten](#Step4)

Die Verwendung von CDN zum Zwischenspeichern von Azure-Daten bietet folgende Vorteile:

-   Bessere Leistung und Benutzerfreundlichkeit für Endbenutzer, die von einer Inhaltsquelle weit entfernt sind und Anwendungen verwenden, die viele 'internet trips' benötigen, um Inhalte zu laden.
-   Große verteilte Skalierung, um hohe Lasten etwa zu Beginn eines Ereignisses, z. B. bei einer Produkteinführung, besser verarbeiten zu können

Aktuelle CDN-Kunden können das Windows Azure CDN jetzt im [Azure-Verwaltungsportal] verwenden. Das CDN ist eine Zusatzfunktion Ihres Abonnements und hat einen eigenen [Abrechnungsplan].

<a id="Step1"> </a>
<h2>Schritt 1: Speicherkonto erstellen</h2>

Gehen Sie folgendermaßen vor, um für ein Azure-Abonnement ein neues
Speicherkonto zu erstellen. Ein Speicherkonto ermöglicht den Zugriff auf 
Azure-Speicherdienste. Das Speicherkonto stellt die höchste Ebene
des Namespace für den Zugriff auf die verschiedenen Komponenten der Speicherdienste von Azure
dar: Blob-Dienste, Warteschlangendienste und Tabellenspeicherdienste. Weitere
Informationen über die Speicherdienste von Azure finden Sie unter [Verwenden der
Azure-Speicherdienste](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Um ein Speicherkonto zu erstellen, müssen Sie entweder der
Dienstadministrator oder ein Co-Administrator für das zugehörige Abonnement sein.

> [AZURE.NOTE] Informationen zum Ausführen dieses Vorgangs mit der
Dienstverwaltungs-API von Azure finden Sie unter dem Referenzthema [Speicherkonto erstellen](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx).

**So erstellen Sie ein Speicherkonto für ein Azure-Abonnement**

1.  Melden Sie sich beim [Azure-Verwaltungsportal] an.
2.  Klicken Sie unten links auf **Neu**. Wählen Sie im Dialogfeld **Neu** die Option **Datendienste** aus, und klicken Sie dann auf **Speicher** und **Schnellerfassung**.

    Das Dialogfeld **Speicherkonto erstellen** wird angezeigt.

    ![Speicherkonto erstellen][create-new-storage-account]

4. Geben Sie in das Feld **URL** den Namen einer Unterdomäne ein. Dieser Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten.

    Dieser Wert wird der Hostname in dem URI, der
    zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird. Zum
    Adressieren einer Containerressource im Blob-Dienst verwenden Sie einen
    URI im folgenden Format. Dabei bezieht sich *&lt;Speicherkontobezeichnung&gt;* auf
    den Wert, den Sie in **URL eingeben** eingegeben haben:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Wichtig:** Die URL-Bezeichnung bildet die Unterdomäne des
    Speicherkonto-URI und muss unter allen gehosteten Diensten in 
    Azure eindeutig sein.

	Dieser Wert wird auch als Name für dieses Speicherkonto im Portal verwendet oder beim programmatischen Zugriff auf dieses Konto.

5.  Wählen Sie aus der Dropdownliste **Region/Affinitätsgruppe** eine Region oder Affinitätsgruppe für das Speicherkonto aus. Wählen Sie eine Affinitätsgruppe statt einer Region aus, wenn sich Ihre Speicherdienste im selben Rechenzentrum befinden sollen, wie Ihre anderen Azure-Dienste. Dadurch wird möglicherweise die Leistung verbessert, und es fallen keine Kosten für Ausgangsverkehr an.  

    **Hinweis:** Um eine Affinitätsgruppe zu erstellen, öffnen Sie den Bereich **Einstellungen** im Verwaltungsportal, klicken auf **Affinitätsgruppen** und anschließend entweder auf **Affinitätsgruppe hinzufügen** oder **Hinzufügen**. Sie können Affinitätsgruppen auch mithilfe der Azure-Service-Verwaltungs-API erstellen und verwalten. Weitere Informationen finden Sie unter [Vorgänge für Affinitätsgruppen].

6. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, das für das Speicherkonto verwendet werden soll.
7.  Klicken Sie auf **Speicherkonto erstellen**. Das Erstellen des Speicherkontos kann einige Minuten dauern.
8.  Um sicherzustellen, dass das Speicherkonto erstellt wurde, prüfen Sie, ob das Konto in den unter **Speicher** aufgeführten Elementen mit dem Status **Online** angezeigt wird.

<a id="Step2"> </a>
<h2>Schritt 2: Erstellen eines neuen CDN-Endpunkts für das Speicherkonto</h2>

Nachdem Sie den CDN-Zugriff auf ein Speicherkonto oder einen gehosteten Dienst aktivieren, kommen alle
öffentlich verfügbaren Objekte für den CDN-Edgezwischenspeicher infrage. Wenn Sie
ein Objekt ändern, das derzeit im CDN zwischengespeichert ist, stehen die neuen Inhalte
erst dann über das CDN zur Verfügung, wenn das CDN seine Inhalte aktualisiert,
wenn die Lebensdauer der zwischengespeicherten Inhalte abläuft.

**So erstellen Sie einen neuen CDN-Endpunkt für das Speicherkonto**

1. Klicken Sie im [Azure-Verwaltungsportal] im Navigationsbereich auf **CDN**.

2. Klicken Sie im Menüband auf **Neu**. Klicken Sie im Dialogfeld **Neu** auf **App-Dienste**, dann auf **CDN** und schließlich auf **Schnellerfassung**.

3. Wählen Sie aus der Dropdownliste **Ursprüngliche Domäne** das Speicherkonto aus, das Sie im vorherigen Abschnitt aus der Liste der verfügbaren Speicherkonten erstellt haben. 

4. Klicken Sie auf die Schaltfläche **Erstellen**, um den neuen Endpunkt zu erstellen.

5. Sobald der Endpunkt erstellt wurde, wird er in einer Liste von Endpunkten für das Abonnement angezeigt. In der Listenansicht werden der URL für den Zugriff auf zwischengespeicherte Inhalte sowie die ursprüngliche Domäne angezeigt. 

	Die Ursprungsdomäne ist der Standort, von dem das CDN Inhalte
    zwischenspeichert. Bei der ursprünglichen Domäne kann es sich um ein Speicherkonto oder einen Cloud-Dienst handeln. In diesem Beispiel wird ein Speicherkonto verwendet. Speicherinhalte werden entsprechend einer von Ihnen festgelegten Cache-Steuereinstellung oder entsprechend der Standardheuristik des Zwischenspeicherungsnetzwerks auf Edgeservern zwischengespeichert. Weitere Informationen finden Sie unter [Verwalten der Ablaufdauer von Blob-Inhalten](http://msdn.microsoft.com/library/gg680306.aspx). 


    > [AZURE.NOTE] Die für den Endpunkt erstellte Konfiguration steht nicht
    sofort zur Verfügung. Es kann bis zu 60 Minuten dauern, bis
    die Registrierung über das CDN-Netzwerk verteilt wurde. Benutzer, die versuchen,
    den CDN-Domänennamen sofort zu verwenden, erhalten u. U. den Statuscode 400
    (ungültige Anforderung), bis die Inhalte über das CDN verfügbar sind.

<a id="Step3"> </a>
<h2>Schritt 3: Auf CDN-Inhalte zugreifen</h2> 

Verwenden Sie für den Zugriff auf die im CDN zwischengespeicherten Inhalte den im Portal bereitgestellten CDN-URL. Die Adresse eines zwischengespeicherten Blobs hat etwa folgendes Format:

http://<*CDNNamespace*\>.vo.msecnd.net/<*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Schritt 4: Inhalte aus dem CDN entfernen</h2>

Wenn Sie ein Objekt nicht länger im Azure Content
Delivery Network (CDN) zwischenspeichern möchten, können Sie einen der folgenden Schritte ausführen:

-   Ein Azure-Blob können Sie aus dem öffentlichen Container
    löschen.
-   Sie können den öffentlichen Container zu einem privaten machen. Weitere Informationen finden Sie unter [Beschränken des Zugriffs auf Container und Blobs](http://msdn.microsoft.com/library/dd179354.aspx).
-   Sie können den CDN-Endpunkt mit dem Verwaltungsportal deaktivieren oder
    löschen.
-   Sie können den gehosteten Dienst so ändern, dass er nicht mehr auf Anforderungen des
    Objekts antwortet.

Ein bereits im CDN zwischengespeichertes Objekt bleibt zwischengespeichert, bis die
Lebensdauer des Objekts abgelaufen ist. Wenn die Lebensdauer abläuft,
prüft das CDN, ob der CDN-Endpunkt weiterhin gültig ist
und ob weiterhin anonym auf das Objekt zugegriffen werden kann. Ist dies nicht der Fall,
wird das Objekt nicht mehr zwischengespeichert.

Die Möglichkeit zum sofortigen Löschen von Inhalten wird im Azure-Verwaltungsportal momentan nicht unterstützt. Wenden Sie sich an den [Azure-Support](http://azure.microsoft.com/support/options/), wenn Sie Inhalte sofort löschen müssen. 

## Zusätzliche Ressourcen

-   [Erstellen einer Affinitätsgruppe in Azure]
-   [Gewusst wie: Verwalten von Speicherkonten für ein Azure-Abonnement]
-   [Über die Dienstverwaltungs-API]
-   [Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne]

  [Speicherkonto erstellen]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
  [Standorte der Azure-CDN-Knoten]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Abrechnungsplan]: /pricing/calculator/?scenario=full
  [Registrieren eines benutzerdefinierten Unterdomänennamens für den Zugriff auf Blobs in Azure]: http://msdn.microsoft.com/library/windowsazure/ee795179.aspx
  [Erstellen einer Affinitätsgruppe in Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Übersicht über das Azure-CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [Gewusst wie: Verwalten von Speicherkonten für ein Azure-Abonnement]: http://msdn.microsoft.com/library/windowsazure/hh531567.aspx
  [Über die Dienstverwaltungs-API]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[Erstellen eines neuen Speicherkontos]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Vorheriges Verwaltungsportal]: ../../Shared/Media/previous-portal.png

<!--HONumber=49-->