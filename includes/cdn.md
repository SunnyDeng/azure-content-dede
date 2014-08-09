
# Verwenden von CDN für Azure

Das Azure Content Delivery Network (CDN) bietet Entwicklern eine globale Lösung für die Übermittlung breitbandiger Inhalte. Dazu werden Blobs und statische Inhalte von Serverinstanzen an physischen Knotenpunkten in den USA, Europa, Asien, Australien und Südamerika zwischengespeichert. Eine aktuelle Liste von CDN-Knotenstandorten finden Sie unter [Standorte der Azure-CDN-Knoten][1].

Diese Aufgabe umfasst die folgenden Schritte:

* [Schritt 1: Speicherkonto erstellen](#Step1)
* [Schritt 2: Neuen CDN-Endpunkt für das Speicherkonto
  erstellen](#Step2)
* [Schritt 3: Auf die CDN-Inhalte zugreifen](#Step3)
* [Schritt 4: Die CDN-Inhalte löschen](#Step4)

Die Verwendung von CDN zum Zwischenspeichern von Azure-Daten bietet folgende Vorteile:

* Bessere Leistung und Benutzerfreundlichkeit für Endbenutzer, die von
  einer Inhaltsquelle weit entfernt sind und Anwendungen verwenden, die
  viele Verbindungen im Internet benötigen, um Inhalte zu laden.
* Große verteilte Skalierung, um hohe Lasten etwa zu Beginn eines
  Ereignisses, z. B. bei einer Produkteinführung, besser verarbeiten zu
  können

Aktuelle CDN-Kunden können das Windows Azure CDN jetzt im
[Azure-Verwaltungsportal][2] verwenden. Das CDN ist eine Zusatzfunktion
Ihres Abonnements und hat einen eigenen
[Abrechnungsplan](/en-us/pricing/calculator/?scenario=full).

<a id="Step1"> </a>

<h2>Schritt 1: Speicherkonto erstellen</h2>


Gehen Sie folgendermaßen vor, um für ein Azure-Abonnement ein neues Speicherkonto zu erstellen. Ein Speicherkonto ermöglicht den Zugriff auf Speicherdienste von Windows Azure. Das Speicherkonto stellt die höchste Ebene des Namespace für den Zugriff auf die verschiedenen Komponenten der Speicherdienste von Azure dar: Blob-Dienste, Warteschlangendienste und Tabellenspeicherdienste. Weitere Informationen über die Speicherdienste von Azure finden Sie unter [Verwenden der Speicherdienste von Azure][3].

Um ein Speicherkonto zu erstellen, müssen Sie entweder der Dienstadministrator oder ein Co-Administrator für das zugehörige Abonnement sein.
<div class="dev-callout">
<strong>Hinweis</strong>
<p>Informationen zum Ausführen dieses Vorgangs mit der Dienstverwaltungs-API von Azure finden Sie unter dem Referenzthema <a href="http://msdn.microsoft.com/de-de/library/windowsazure/hh264518.aspx">Create Storage Account</a> (Erstellen eines Speicherkontos; in englischer Sprache).</p>
</div>

 **So erstellen Sie ein Speicherkonto für ein Azure-Abonnement**

1.  Melden Sie sich im [Azure-Verwaltungsportal][2] an. 2.  Klicken Sie unten links auf **Neu** und dann auf **Storage**. 3.  Klicken Sie auf **Schnellerfassung**.
    
    Das Dialogfeld **Speicherkonto erstellen** wird angezeigt.
    
    ![Speicherkonto erstellen](./media/cdn/CDN_CreateNewStorageAcct.png)

4.  Geben Sie in das Feld **URL** den Namen einer Unterdomäne ein.
    Dieser Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen
    enthalten.
    
    Dieser Wert wird der Hostname im URI, der zum Adressieren von Blob-,
    Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement
    verwendet wird. Zum Adressieren einer Containerressource im
    Blob-Dienst verwenden Sie einen URI im folgenden Format; dabei
    bezieht sich *<Speicherkontobezeichnung>* auf den Wert, den
    Sie in **URL eingeben** eingegeben haben:
    
    http://*<Speicherkontobezeichnung>*.blob.core.windows.net/*<meincontainer>*
    
    **Wichtig:** Die URL-Bezeichnung bildet die Unterdomäne des
    Speicherkonto-URI und muss unter allen gehosteten Diensten in
    Windows Azure eindeutig sein.
    
    Dieser Wert wird auch als Name für dieses Speicherkonto im Portal
    verwendet oder beim programmatischen Zugriff auf dieses Konto.

5.  Wählen Sie in der Dropdownliste **Region/Affinitätsgruppe** einen
    geografischen Standort für das Speicherkonto aus. Verwenden Sie
    alternativ eine Affinitätsgruppe. Anweisungen zum Erstellen einer
    Affinitätsgruppe finden Sie unter [Erstellen einer Affinitätsgruppe
    in Azure][4].
6.  Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus,
    das für das Speicherkonto verwendet werden soll.
7.  Klicken Sie auf **Speicherkonto erstellen**. Das Erstellen des
    Speicherkontos kann einige Minuten dauern.
8.  Um zu überprüfen, ob das Speicherkonto erstellt wurde, prüfen Sie,
    ob das Konto in den unter **Storage** aufgeführten Elementen mit dem
    Status **Online** angezeigt wird.

<a id="Step2"> </a>

<h2>Schritt 2: Neuen CDN-Endpunkt für das Speicherkonto erstellen</h2>


Sobald Sie den CDN-Zugriff auf ein Speicherkonto oder einen gehosteten Dienst aktivieren, kommen alle öffentlich verfügbaren Objekte für den CDN-Edgezwischenspeicher in Frage. Wenn Sie ein Objekt ändern, das derzeit im CDN zwischengespeichert ist, stehen die neuen Inhalte erst dann über das CDN zur Verfügung, wenn das CDN seine Inhalte aktualisiert, wenn die Lebensdauer der zwischengespeicherten Inhalte abläuft.

**So erstellen Sie einen neuen CDN-Endpunkt für das Speicherkonto**

1.  Klicken Sie im [Azure-Verwaltungsportal][2] im Navigationsbereich
    auf **CDN**.

2.  Klicken Sie im Menüband auf **Neu**. Klicken Sie im Dialogfeld
    **Neu** auf **App Services**, dann auf **CDN** und schließlich auf
    **Schnellerfassung**.

3.  Wählen Sie in der Dropdownliste **Ursprungsdomäne** das
    Speicherkonto aus, das Sie im vorherigen Abschnitt aus der Liste
    verfügbarer Speicherkonten erstellt haben.

4.  Klicken Sie auf die Schaltfläche **Erstellen**, um den neuen
    Endpunkt zu erstellen.

5.  Sobald der Endpunkt erstellt wurde, wird er in einer Liste von
    Endpunkten für das Abonnement angezeigt. In der Listenansicht werden
    der URL für den Zugriff auf zwischengespeicherte Inhalte sowie die
    Ursprungsdomäne angezeigt.
    
    Die Ursprungsdomäne ist der Standort, von dem das CDN Inhalte
    zwischenspeichert. Bei der Ursprungsdomäne kann es sich um ein
    Speicherkonto oder einen Clouddienst handeln. In diesem Beispiel
    wird ein Speicherkonto verwendet. Speicherinhalte werden
    entsprechend einer von Ihnen festgelegten Cache-Steuereinstellung
    oder entsprechend der Standardheuristik des
    Zwischenspeicherungsnetzwerks auf Edgeservern zwischengespeichert.
    Weitere Informationen finden Sie unter [Verwalten der Ablaufdauer
    von BLOB-Inhalten][5].
<div class="dev-callout">

<b>Hinweis</b>
Die für den Endpunkt erstellte Konfiguration ist nicht sofort verfügbar. Die Verteilung der Registrierung über das CDN-Netzwerk kann bis zu 60 Minuten dauern. Benutzer, die den CDN-Domänennamen sofort zu verwenden versuchen, sehen u. U. den Statuscode 400
(Unzulässige Anforderung), bis die Inhalte über das CDN verfügbar
sind.
</div>


<a id="Step3"> </a>

<h2>Schritt 3: Auf CDN-Inhalte zugreifen</h2>


Verwenden Sie für den Zugriff auf die im CDN zwischengespeicherten Inhalte den im Portal bereitgestellten CDN-URL. Die Adresse eines zwischengespeicherten Blobs hat etwa folgendes Format:

http://<*CDNNamespace*>.vo.msecnd.net/<*meinOeffentlicherContainer*>/<*BlobName*>

<a id="Step4"> </a>

<h2>Schritt 4: Inhalte aus dem CDN entfernen</h2>


Wenn ein Objekt nicht mehr im Azure Content Delivery Network (CDN) zwischengespeichert werden soll, können Sie einen der folgenden Schritte ausführen:

* Ein Azure-Blob können Sie aus dem öffentlichen Container löschen.
* Sie können den öffentlichen Container zu einem privaten machen.
  Weitere Informationen finden Sie unter [Beschränken des Zugriffs auf
  Container und BLOBs ][6].
* Sie können den CDN-Endpunkt mit dem Verwaltungsportal deaktivieren
  oder löschen.
* Sie können den gehosteten Dienst so ändern, dass er nicht mehr auf
  Anforderungen des Objekts antwortet.

Ein bereits im CDN zwischengespeichertes Objekt bleibt zwischengespeichert, bis die Lebensdauer des Objekts abgelaufen ist. Wenn die Lebensdauer abläuft, prüft das CDN, ob der CDN-Endpunkt weiterhin gültig ist und ob weiterhin anonym auf das Objekt zugegriffen werden kann. Ist dies nicht der Fall, wird das Objekt nicht mehr zwischengespeichert.

Für das Azure CDN steht zurzeit kein explizites "Bereinigungstool" zur Verfügung.

## Zusätzliche Ressourcen

* [Erstellen einer Affinitätsgruppe in Azure][4]
* [Gewusst wie: Verwalten von Speicherkonten für ein
  Azure-Abonnement][7]
* [Über die Dienstverwaltungs-API][8]
* [Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne][9]



[1]: http://msdn.microsoft.com/de-de/library/windowsazure/gg680302.aspx
[2]: https://manage.windowsazure.com/
[3]: http://msdn.microsoft.com/de-de/library/windowsazure/ee924681.aspx
[4]: http://msdn.microsoft.com/de-de/library/windowsazure/hh531560.aspx
[5]: http://msdn.microsoft.com/de-de/library/gg680306.aspx
[6]: http://msdn.microsoft.com/de-de/library/dd179354.aspx
[7]: http://msdn.microsoft.com/de-de/library/windowsazure/hh531567.aspx
[8]: http://msdn.microsoft.com/de-de/library/windowsazure/ee460807.aspx
[9]: http://msdn.microsoft.com/de-de/library/windowsazure/gg680307.aspx