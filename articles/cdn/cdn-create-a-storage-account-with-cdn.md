<properties 
	pageTitle="Verwenden von CDN | Microsoft Azure"
	description="Erfahren Sie, wie das Azure Content Delivery Network (CDN) für die Übermittlung breitbandiger Inhalte eingesetzt wird, indem Blobs und statische Inhalte zwischengespeichert werden."
	services="cdn"
	documentationCenter=".net"
	authors="zhangmanling"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="mazha"/>


# Integrieren eines Speicherkontos in CDN

Zum Zwischenspeichern von Inhalten aus Ihrem Azure-Speicher kann CDN aktiviert werden. Das Azure Content Delivery Network (CDN) bietet Entwicklern eine globale Lösung für die Übermittlung von Inhalten mit hohen Breitbandenanforderungen. Dazu werden Blobs und statische Inhalte von Compute-Instanzen an physischen Knotenpunkten in den USA, Europa, Asien, Australien und Südamerika zwischengespeichert.


## Schritt 1: Erstellen eines Speicherkontos

Gehen Sie folgendermaßen vor, um für ein Azure-Abonnement ein neues Speicherkonto zu erstellen. Ein Speicherkonto ermöglicht den Zugriff auf Speicherdienste von Azure. Das Speicherkonto stellt die höchste Ebene des Namespace für den Zugriff auf die verschiedenen Komponenten der Speicherdienste von Azure dar: Blob-, Warteschlangen- und Tabellenspeicherdienste. Weitere Informationen über die Speicherdienste von Azure finden Sie unter [Verwenden der Speicherdienste von Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Um ein Speicherkonto zu erstellen, müssen Sie entweder der Dienstadministrator oder ein Co-Administrator für das zugehörige Abonnement sein.

> [AZURE.NOTE]Informationen zum Ausführen dieses Vorgangs mit der Dienstverwaltungs-API von Azure finden Sie unter dem Referenzthema [Create Storage Account](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) (Erstellen eines Speicherkontos; in englischer Sprache).

**So erstellen Sie ein Speicherkonto für ein Azure-Abonnement**

1.  Melden Sie sich beim [Azure-Verwaltungsportal] an.
2.  Klicken Sie unten links auf **Neu**. Wählen Sie im Dialogfeld **Neu** die Option **Data Services** aus, und klicken Sie dann auf **Storage** und **Schnellerfassung**.

    Das Dialogfeld **Speicherkonto erstellen** wird angezeigt.

    ![Speicherkonto erstellen][create-new-storage-account]

4. Geben Sie in das Feld **URL** den Namen einer Unterdomäne ein. Dieser Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten.

    Dieser Wert wird der Hostname im URI, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird. Zum Adressieren einer Containerressource im Blobdienst verwenden Sie einen URI im folgenden Format; dabei bezieht sich *&lt;Speicherkontobezeichnung&gt;* auf den Wert, den Sie in **URL eingeben** eingegeben haben:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Wichtig:** Die URL-Bezeichnung bildet die Unterdomäne des Speicherkonto-URI und muss unter allen gehosteten Diensten in Azure eindeutig sein.

	Dieser Wert wird auch als Name für dieses Speicherkonto im Portal verwendet oder beim programmatischen Zugriff auf dieses Konto.

5.  Wählen Sie aus der Dropdownliste **Region/Affinitätsgruppe** eine Region oder Affinitätsgruppe für das Speicherkonto aus. Wählen Sie eine Affinitätsgruppe statt einer Region aus, wenn sich Ihre Speicherdienste im selben Rechenzentrum befinden sollen wie Ihre weiteren Microsoft Azure-Dienste. Dadurch wird möglicherweise die Leistung verbessert, und es fallen keine Kosten für Ausgangsverkehr an.

    **Hinweis:** Um eine Affinitätsgruppe zu erstellen, öffnen Sie den Bereich **Einstellungen** im Verwaltungsportal, klicken auf **Affinitätsgruppen** und anschließend entweder auf **Affinitätsgruppe hinzufügen** oder **Hinzufügen**. Sie können Affinitätsgruppen auch mithilfe der Microsoft Azure Service Management-API erstellen und verwalten. Weitere Informationen finden Sie unter [Vorgänge für Affinitätsgruppen].

6. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, das für das Speicherkonto verwendet werden soll.
7.  Klicken Sie auf **Speicherkonto erstellen**. Das Erstellen des Speicherkontos kann einige Minuten dauern.
8.  Um zu überprüfen, ob das Speicherkonto erstellt wurde, prüfen Sie, ob das Konto in den unter **Storage** aufgeführten Elementen mit dem Status **Online** angezeigt wird.


## Schritt 2: Erstellen eines neuen CDN-Endpunkts für das Speicherkonto

Sobald Sie den CDN-Zugriff auf ein Speicherkonto oder einen gehosteten Dienst aktivieren, kommen alle öffentlich verfügbaren Objekte für den CDN-Edgezwischenspeicher in Frage. Wenn Sie ein Objekt ändern, das derzeit im CDN zwischengespeichert ist, stehen die neuen Inhalte erst dann über das CDN zur Verfügung, wenn das CDN seine Inhalte aktualisiert, wenn die Lebensdauer der zwischengespeicherten Inhalte abläuft.

**So erstellen Sie einen neuen CDN-Endpunkt für das Speicherkonto**

1. Klicken Sie im [Azure-Verwaltungsportal] im Navigationsbereich auf **CDN**.

2. Klicken Sie im Menüband auf **Neu**. Klicken Sie im Dialogfeld **Neu** auf **App Services**, dann auf **CDN** und schließlich auf **Schnellerfassung**.

3. Wählen Sie in der Dropdownliste **Ursprungsdomäne** das Speicherkonto aus, das Sie im vorherigen Abschnitt aus der Liste verfügbarer Speicherkonten erstellt haben.

4. Klicken Sie auf die Schaltfläche **Erstellen**, um den neuen Endpunkt zu erstellen.

5. Sobald der Endpunkt erstellt wurde, wird er in einer Liste von Endpunkten für das Abonnement angezeigt. In der Listenansicht werden der URL für den Zugriff auf zwischengespeicherte Inhalte sowie die Ursprungsdomäne angezeigt.

	Die Ursprungsdomäne ist der Standort, von dem das CDN Inhalte zwischenspeichert. Bei der Ursprungsdomäne kann es sich um ein Speicherkonto oder einen Clouddienst handeln. In diesem Beispiel wird ein Speicherkonto verwendet. Speicherinhalte werden entsprechend einer von Ihnen festgelegten Cache-Steuereinstellung oder entsprechend der Standardheuristik des Zwischenspeicherungsnetzwerks auf Edgeservern zwischengespeichert. Weitere Informationen finden Sie unter [Verwalten der Ablaufdauer von BLOB-Inhalten](http://msdn.microsoft.com/library/gg680306.aspx).


    > [AZURE.NOTE]Die für den Endpunkt erstellte Konfiguration ist nicht sofort verfügbar. Die Verteilung der Registrierung über das CDN-Netzwerk kann bis zu 60 Minuten dauern. Benutzer, die den CDN-Domänennamen sofort zu verwenden versuchen, sehen u. U. den Statuscode 400 (Unzulässige Anforderung), bis die Inhalte über das CDN verfügbar sind.


## Schritt 3: Zugriff auf CDN-Inhalte

Verwenden Sie für den Zugriff auf die im CDN zwischengespeicherten Inhalte den im Portal bereitgestellten CDN-URL. Die Adresse eines zwischengespeicherten Blobs hat etwa folgendes Format:

http://<*CDNNamespace*>.vo.msecnd.net/<*meinOeffentlicherContainer*>/<*BlobName*>


## Schritt 4: Entfernen von Inhalten aus CDN

Wenn ein Objekt nicht mehr im Azure Content Delivery Network (CDN) zwischengespeichert werden soll, können Sie einen der folgenden Schritte ausführen:

-   Sie können den öffentlichen Container zu einem privaten machen. Weitere Informationen finden Sie unter [Beschränken des Zugriffs auf Container und BLOBs ](http://msdn.microsoft.com/library/dd179354.aspx).
-   Sie können den CDN-Endpunkt mit dem Verwaltungsportal deaktivieren oder löschen.
-   Sie können den gehosteten Dienst so ändern, dass er nicht mehr auf Anforderungen des Objekts antwortet.

Ein bereits im CDN zwischengespeichertes Objekt bleibt zwischengespeichert, bis die Lebensdauer des Objekts abgelaufen ist. Wenn die Lebensdauer abläuft, prüft das CDN, ob der CDN-Endpunkt weiterhin gültig ist und ob weiterhin anonym auf das Objekt zugegriffen werden kann. Ist dies nicht der Fall, wird das Objekt nicht mehr zwischengespeichert.

Die Möglichkeit zum sofortigen Löschen von Inhalten wird im Azure-Verwaltungsportal momentan nicht unterstützt. Wenden Sie sich an den [Azure-Support](http://azure.microsoft.com/support/options/), wenn Sie Inhalte sofort löschen müssen.

## Zusätzliche Ressourcen

-   [Erstellen einer Affinitätsgruppe in Azure]
-   [Vorgehensweise: Verwalten von Speicherkonten für ein Azure-Abonnement]
-   [Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne]

[Create Storage Account]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure CDN Node Locations]: cdn-pop-locations.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[billing plan]: /pricing/calculator/?scenario=full
[How to Register a Custom Subdomain Name for Accessing Blobs in Azure]: ../storage-custom-domain-name.md
[Erstellen einer Affinitätsgruppe in Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
[Overview of the Azure CDN]: cdn-overview.md
[Vorgehensweise: Verwalten von Speicherkonten für ein Azure-Abonnement]: https://msdn.microsoft.com/de-DE/library/azure/hh531793.aspx
[Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne]: cdn-map-content-to-custom-domain.md


[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

 

<!---HONumber=September15_HO1-->