# Verwenden von CDN für Azure

Das Azure Content Delivery Network (CDN) bietet Entwicklern eine
globale Lösung für die Übermittlung breitbandiger Inhalte. Dazu
werden Blobs und statische Inhalte von Serverinstanzen an physischen
Knotenpunkten in den USA, Europa, Asien, Australien und Südamerika zwischengespeichert. Eine aktuelle Liste
der CDN-Knotenstandorte finden Sie unter [Standorte der Azure-CDN-Knoten][Standorte der Azure-CDN-Knoten].

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Speicherkonto erstellen][Schritt 1: Speicherkonto erstellen]
-   [Schritt 2: Neuen CDN-Endpunkt für das Speicherkonto erstellen][Schritt 2: Neuen CDN-Endpunkt für das Speicherkonto erstellen]
-   [Schritt 3: Auf die CDN-Inhalte zugreifen][Schritt 3: Auf die CDN-Inhalte zugreifen]
-   [Schritt 4: Die CDN-Inhalte löschen][Schritt 4: Die CDN-Inhalte löschen]

Die Verwendung von CDN zum Zwischenspeichern von Azure-Daten bietet folgende Vorteile:

-   Bessere Leistung und Benutzerfreundlichkeit für Endbenutzer, die von einer Inhaltsquelle weit entfernt sind und Anwendungen verwenden, die viele Verbindungen im Internet benötigen, um Inhalte zu laden.
-   Große verteilte Skalierung, um hohe Lasten etwa zu Beginn eines Ereignisses, z. B. bei einer Produkteinführung, besser verarbeiten zu können

Aktuelle CDN-Kunden können das Windows Azure CDN jetzt im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] verwenden. Das CDN ist eine Zusatzfunktion Ihres Abonnements und hat einen eigenen [Abrechnungsplan][Abrechnungsplan].

<span id="Step1"></span> </a>

## Schritt 1: Speicherkonto erstellen

</p>
Gehen Sie folgendermaßen vor, um für ein Azure-Abonnement ein
neues Speicherkonto zu erstellen. Ein Speicherkonto ermöglicht den
Zugriff auf Speicherdienste von Azure. Das Speicherkonto stellt die höchste Ebene des
Namespace für den Zugriff auf die verschiedenen Komponenten der
Speicherdienste von Azure dar: Blob-Dienste, Warteschlangendienste und Tabellenspeicherdienste. Weitere
Informationen über die Speicherdienste von Azure finden Sie
unter [Verwenden der Speicherdienste von Azure].

Um ein Speicherkonto zu erstellen, müssen Sie entweder der
Dienstadministrator oder ein Co-Administrator für das zugehörige Abonnement sein.

<div class="dev-callout">

**Hinweis**
Informationen zum Ausführen dieses Vorgangs mit der Azure-Service-Verwaltungs-API finden Sie im Thema [Speicherkonto erstellen][Speicherkonto erstellen].

</div>

**So erstellen Sie ein Speicherkonto für ein Azure-Abonnement**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten links auf **Neu** und dann auf **Speicher**.
3.  Klicken Sie auf **Schnellerfassung**.

    Das Dialogfeld **Speicherkonto erstellen** wird angezeigt.

    ![Speicherkonto erstellen][1]

4.  Geben Sie in das Feld **URL** den Namen einer Unterdomäne ein. Dieser Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten.

    Dieser Wert wird zum Hostnamen im URI, der zum Adressieren von
    Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird. Zum
    Adressieren einer Containerressource im Blob-Dienst verwenden
    Sie einen URI im folgenden Format, wobei sich *\<Speicherkontobezeichnung\>*
    auf den Wert bezieht, den Sie in **URL eingeben** eingegeben haben:

    <http://>*\<Speicherkontobezeichnung\>*.blob.core.windows.net/*\<meincontainer\>*

    **Wichtig:** Die URL-Bezeichnung bildet die Unterdomäne
    des Speicherkonto-URI und muss unter allen gehosteten
    Diensten in Azure eindeutig sein.

    Dieser Wert wird auch als Name für dieses Speicherkonto im Portal verwendet oder beim programmatischen Zugriff auf dieses Konto.

5.  Wählen Sie aus der Dropdownliste **Region/Affinitätsgruppe** eine Region oder Affinitätsgruppe für das Speicherkonto aus. Wählen Sie eine Affinitätsgruppe statt einer Region aus, wenn sich Ihre Speicherdienste im selben Rechenzentrum befinden sollen, wie Ihre anderen Azure-Dienste. Dadurch wird möglicherweise die Leistung verbessert, und es fallen keine Kosten für Ausgangsverkehr an.

    **Hinweis:**Um eine Affinitätsgruppe zu erstellen, öffnen Sie den Bereich **Einstellungen** im Verwaltungsportal, klicken auf **Affinitätsgruppen** und anschließend entweder auf **Affinitätsgruppe hinzufügen** oder **Hinzufügen**. Sie können Affinitätsgruppen auch mithilfe der Azure-Service-Verwaltungs-API erstellen und verwalten. Weitere Informationen finden Sie unter [Vorgänge für Affinitätsgruppen][Vorgänge für Affinitätsgruppen].

6.  Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, das für das Speicherkonto verwendet werden soll.
7.  Klicken Sie auf **Speicherkonto erstellen**. Das Erstellen des Speicherkontos kann einige Minuten dauern.
8.  Um sicherzustellen, dass das Speicherkonto erstellt wurde, prüfen Sie, ob das Konto in den unter **Speicher** aufgeführten Elementen mit dem Status **Online** angezeigt wird.

<span id="Step2"></span> </a>

## Schritt 2: Neuen CDN-Endpunkt für das Speicherkonto erstellen

</p>
Sobald Sie den CDN-Zugriff auf ein Speicherkonto oder einen gehosteten
Dienst aktivieren, kommen alle öffentlich verfügbaren Objekte für den CDN-Edgezwischenspeicher in Frage. Wenn
Sie ein Objekt ändern, das derzeit im CDN zwischengespeichert ist,
stehen die neuen Inhalte erst dann über das CDN zur Verfügung, wenn
das CDN seine Inhalte aktualisiert, nachdem die Lebensdauer der zwischengespeicherten Inhalte abgelaufen ist.

**So erstellen Sie einen neuen CDN-Endpunkt für das Speicherkonto**

1.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] im Navigationsbereich auf **CDN**.

2.  Klicken Sie im Menüband auf **Neu**. Klicken Sie im Dialogfeld **Neu** auf **App-Dienste**, dann auf **CDN** und schließlich auf **Schnellerfassung**.

3.  Wählen Sie aus der Dropdownliste **Ursprüngliche Domäne** das Speicherkonto aus, das Sie im vorherigen Abschnitt aus der Liste der verfügbaren Speicherkonten erstellt haben.

4.  Klicken Sie auf die Schaltfläche **Erstellen**, um den neuen Endpunkt zu erstellen.

5.  Sobald der Endpunkt erstellt wurde, wird er in einer Liste von Endpunkten für das Abonnement angezeigt. In der Listenansicht werden der URL für den Zugriff auf zwischengespeicherte Inhalte sowie die ursprüngliche Domäne angezeigt.

    Die ursprüngliche Domäne ist der Standort, an dem das CDN
    Inhalte zwischenspeichert. Bei der ursprünglichen Domäne kann es sich um ein Speicherkonto oder einen Cloud-Dienst handeln. In diesem Beispiel wird ein Speicherkonto verwendet. Speicherinhalte werden entsprechend einer von Ihnen festgelegten Cache-Steuereinstellung oder entsprechend der Standardheuristik des Zwischenspeicherungsnetzwerks auf Edgeservern zwischengespeichert. Weitere Informationen finden Sie unter [Verwalten der Ablaufdauer von BLOB-Inhalten][Verwalten der Ablaufdauer von BLOB-Inhalten].

    <div class="dev-callout">

    **Hinweis**
    Die für den Endpunkt erstellte Konfiguration steht nicht sofort zur Verfügung. Es kann bis zu 60 Minuten dauern, bis die Registrierung über das CDN-Netzwerk verteilt wurde. Benutzer, die versuchen, den CDN-Domänennamen sofort zu verwenden, erhalten u. U. den Statuscode 400 (ungültige Anforderung), bis die Inhalte über das CDN verfügbar sind.

    </div>

<span id="Step3"></span> </a>

## Schritt 3: Auf CDN-Inhalte zugreifen

</p>
Verwenden Sie für den Zugriff auf die im CDN zwischengespeicherten Inhalte den im Portal bereitgestellten CDN-URL. Die Adresse eines zwischengespeicherten Blobs hat etwa folgendes Format:

<http://>\<*CDNNamespace*\>.vo.msecnd.net/\<*meinÖffentlicherContainer*\>/\<*BlobName*\>

<span id="Step4"></span> </a>

## Schritt 4: Inhalte aus dem CDN entfernen

</p>
Wenn ein Objekt nicht mehr im Azure Content Delivery Network (CDN)
zwischengespeichert werden soll, haben Sie folgende Möglichkeiten:

-   Sie können einen Azure-Blob aus dem öffentlichen Container
    löschen.
-   Sie können den öffentlichen Container zu einem privaten machen. Weitere Informationen finden Sie unter [Beschränken des Zugriffs auf Container und BLOBs][Beschränken des Zugriffs auf Container und BLOBs].
-   Sie können den CDN-Endpunkt über das Verwaltungsportal deaktivieren
    oder löschen.
-   Sie können den gehosteten Dienst so ändern, dass er nicht mehr auf Anforderungen
    für das Objekt antwortet.

Ein bereits im CDN zwischengespeichertes Objekt bleibt zwischengespeichert,
bis die Lebensdauer des Objekts abgelaufen ist. Wenn die Lebensdauer abläuft,
prüft das CDN, ob der CDN-Endpunkt weiterhin gültig ist und ob
weiterhin anonym auf das Objekt zugegriffen werden kann. Ist dies nicht der
Fall, wird das Objekt nicht mehr zwischengespeichert.

Für das Azure CDN steht zurzeit kein explizites "Löschtool"
zur Verfügung.

## Zusätzliche Ressourcen

-   [Erstellen einer Affinitätsgruppe in Azure]
-   [Gewusst wie: Verwalten von Speicherkonten für ein Azure-Abonnement][Gewusst wie: Verwalten von Speicherkonten für ein Azure-Abonnement]
-   [Über die Dienstverwaltungs-API][Über die Dienstverwaltungs-API]
-   [Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne][Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne]

  [Standorte der Azure-CDN-Knoten]: http://msdn.microsoft.com/de-de/library/windowsazure/gg680302.aspx
  [Schritt 1: Speicherkonto erstellen]: #Step1
  [Schritt 2: Neuen CDN-Endpunkt für das Speicherkonto erstellen]: #Step2
  [Schritt 3: Auf die CDN-Inhalte zugreifen]: #Step3
  [Schritt 4: Die CDN-Inhalte löschen]: #Step4
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Abrechnungsplan]: /de-de/pricing/calculator/?scenario=full
  [Speicherkonto erstellen]: http://msdn.microsoft.com/de-de/library/windowsazure/hh264518.aspx
  [1]: ./media/cdn/CDN_CreateNewStorageAcct.png
  [Vorgänge für Affinitätsgruppen]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Verwalten der Ablaufdauer von BLOB-Inhalten]: http://msdn.microsoft.com/de-de/library/gg680306.aspx
  [Beschränken des Zugriffs auf Container und BLOBs]: http://msdn.microsoft.com/de-de/library/dd179354.aspx
  [Gewusst wie: Verwalten von Speicherkonten für ein Azure-Abonnement]: http://msdn.microsoft.com/de-de/library/windowsazure/hh531567.aspx
  [Über die Dienstverwaltungs-API]: http://msdn.microsoft.com/de-de/library/windowsazure/ee460807.aspx
  [Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne]: http://msdn.microsoft.com/de-de/library/windowsazure/gg680307.aspx
