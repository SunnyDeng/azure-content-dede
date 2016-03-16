<properties
	pageTitle="Verwenden von CDN | Microsoft Azure"
	description="Erfahren Sie, wie das Azure Content Delivery Network (CDN) für die Übermittlung breitbandiger Inhalte eingesetzt wird, indem Blobs und statische Inhalte zwischengespeichert werden."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>


# Integrieren eines Speicherkontos in CDN

Zum Zwischenspeichern von Inhalten aus Ihrem Azure-Speicher kann CDN aktiviert werden. Das Azure Content Delivery Network (CDN) bietet Entwicklern eine globale Lösung für die Übermittlung von Inhalten mit hohen Breitbandenanforderungen. Dazu werden Blobs und statische Inhalte von Compute-Instanzen an physischen Knotenpunkten in den USA, Europa, Asien, Australien und Südamerika zwischengespeichert.


## Schritt 1: Erstellen eines Speicherkontos

Gehen Sie folgendermaßen vor, um für ein Azure-Abonnement ein neues Speicherkonto zu erstellen. Ein Speicherkonto ermöglicht den Zugriff auf Speicherdienste von Azure. Das Speicherkonto stellt die höchste Ebene des Namespace für den Zugriff auf die verschiedenen Komponenten der Speicherdienste von Azure dar: Blob-, Warteschlangen- und Tabellenspeicherdienste. Weitere Informationen finden Sie unter [Einführung in Microsoft Azure Storage](../storage/storage-introduction.md).

Um ein Speicherkonto zu erstellen, müssen Sie entweder der Dienstadministrator oder ein Co-Administrator für das zugehörige Abonnement sein.

> [AZURE.NOTE] Es gibt mehrere Methoden zum Erstellen eines Speicherkontos, einschließlich Azure-Portal und PowerShell. In diesem Tutorial wird das Azure-Portal verwendet.

**So erstellen Sie ein Speicherkonto für ein Azure-Abonnement**

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2.  Klicken Sie unten links auf **Neu**. Wählen Sie im Dialogfeld **Neu** die Option **Daten und Speicher** aus, und klicken Sie dann auf **Speicherkonto**. Belassen Sie das **klassische** Bereitstellungsmodell aktiviert, und klicken Sie dann auf **Erstellen**.

    Das Blatt **Speicherkonto** wird angezeigt.

    ![Speicherkonto erstellen][create-new-storage-account]

4. Geben Sie in das Feld **Speicher** den Namen einer Unterdomäne ein. Dieser Eintrag kann 3 bis 24 Kleinbuchstaben und Zahlen enthalten.

    Dieser Wert wird der Hostname im URI, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird. Zum Adressieren einer Containerressource im Blobdienst verwenden Sie einen URI im folgenden Format; dabei bezieht sich *&lt;Speicherkontobezeichnung&gt;* auf den Wert, den Sie in **URL eingeben** eingegeben haben:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Wichtig:** Die URL-Bezeichnung bildet die Unterdomäne des Speicherkonto-URI und muss unter allen gehosteten Diensten in Azure eindeutig sein.

	Dieser Wert wird auch als Name für dieses Speicherkonto im Portal verwendet oder beim programmatischen Zugriff auf dieses Konto.

5.  Wählen Sie einen **Tarif** aus, oder verwenden Sie den Standardtarif. Weitere Informationen zu den Tarifen finden Sie unter [Preise für Azure Storage](../../pricing/details/storage).

6.  Wählen Sie eine **Ressourcengruppe** aus, oder erstellen Sie eine. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview/#resource-groups).

7. Wählen Sie das **Abonnement** aus, das für das Speicherkonto verwendet werden soll.

8.  Klicken Sie auf **Erstellen**. Das Erstellen des Speicherkontos kann einige Minuten dauern.

9.  Um zu überprüfen, ob das Speicherkonto erstellt wurde, prüfen Sie, ob das Konto in den unter **Storage** aufgeführten Elementen mit dem Status **Online** angezeigt wird.


## Schritt 2: Erstellen eines neuen CDN-Profils

Ein CDN-Profil ist eine Sammlung von CDN-Endpunkten. Jedes Profil enthält mindestens einen CDN-Endpunkt. Sie können mehrere Profile verwenden, um Ihre CDN-Endpunkte nach Internetdomäne, Webanwendung oder anderen Kriterien zu organisieren.

> [AZURE.TIP] Wenn Sie bereits über ein CDN-Profil verfügen, das Sie für dieses Tutorial verwenden möchten, fahren Sie mit [Schritt 3](#step-3-create-a-new-cdn-endpoint) fort.

**So erstellen Sie ein neues CDN-Profil**

1. Klicken Sie im [Azure-Verwaltungsportal](https://portal.azure.com) oben links auf **Neu**. Wählen Sie auf dem Blatt **Neu** erst **Medien + CDN** und dann **CDN** aus.

    Das Blatt für das neue CDN-Profil wird angezeigt.

    ![Neues CDN-Profil][new-cdn-profile]

2. Geben Sie einen Namen für das CDN-Profil ein.

3. Wählen Sie einen **Tarif** aus, oder verwenden Sie den Standardtarif.

4. Wählen Sie eine **Ressourcengruppe** aus, oder erstellen Sie eine. Dies muss nicht dieselbe Ressourcengruppe wie beim Speicherkonto sein.

5. Wählen Sie das **Abonnement** für dieses CDN-Profil aus. Dies muss für dieses Tutorial dasselbe Abonnement wie für das Speicherkonto sein.

6. Wählen Sie einen **Speicherort** aus. Dies ist der Azure-Speicherort, an dem Ihre CDN-Profilinformationen gespeichert werden. Dies hat keine Auswirkung auf die Speicherorte von CDN-Endpunkten. Es muss nicht derselbe Speicherort wie für das Speicherkonto sein.

7. Klicken Sie auf die Schaltfläche **Erstellen**, um das neue Profil zu erstellen.

## Schritt 3: Erstellen eines neuen CDN-Endpunkts

**So erstellen Sie einen neuen CDN-Endpunkt für das Speicherkonto**

1. Navigieren Sie im [Azure-Verwaltungsportal](https://portal.azure.com) zu Ihrem CDN-Profil. Eventuell haben Sie es im vorherigen Schritt an das Dashboard angeheftet. Andernfalls können Sie es ermitteln, indem Sie auf **Durchsuchen**, auf **CDN-Profile** und dann auf das Profil klicken, dem Sie den Endpunkt hinzufügen möchten.

    Das Blatt für das CDN-Profil wird angezeigt.

    ![CDN-Profil][cdn-profile-settings]

2. Klicken Sie auf die Schaltfläche **Endpunkt hinzufügen**.

    ![Schaltfläche „Endpunkt hinzufügen“][cdn-new-endpoint-button]

    Das Blatt **Endpunkt hinzufügen** wird angezeigt.

    ![Blatt „Endpunkt hinzufügen“][cdn-add-endpoint]

3. Geben Sie einen **Namen** für diesen CDN-Endpunkt ein. Dieser Name wird für den Zugriff auf die zwischengespeicherten Ressourcen in der Domäne `<EndpointName>.azureedge.net` verwendet.

4. Wählen Sie in der Dropdownliste **Ursprungstyp** die Option *Speicher* aus.

5. Wählen Sie in der Dropdownliste **Origin hostname** Ihr Speicherkonto aus.

6. Behalten Sie die Standardwerte für **Ursprünglicher Pfad**, **Header des Ursprungshosts** und **Protocol/Origin port** bei. Sie müssen mindestens ein Protokoll (HTTP oder HTTPS) angeben.

    > [AZURE.NOTE] Mit dieser Konfiguration werden alle Ihre öffentlich sichtbaren Container in Ihrem Speicherkonto für die Zwischenspeicherung im CDN aktiviert. Wenn Sie den Gültigkeitsbereich auf einen einzelnen Container beschränken möchten, verwenden Sie **Ursprünglicher Pfad**. Beachten Sie, dass dieser Container als öffentlich sichtbar festgelegt sein muss.

7. Klicken Sie auf die Schaltfläche **Hinzufügen**, um den neuen Endpunkt zu erstellen.

8. Sobald der Endpunkt erstellt wurde, wird er in einer Liste von Endpunkten für das Profil angezeigt. In der Listenansicht werden der URL für den Zugriff auf zwischengespeicherte Inhalte sowie die Ursprungsdomäne angezeigt.

    ![CDN-Endpunkt][cdn-endpoint-success]

    > [AZURE.NOTE] Der Endpunkt kann nicht sofort verwendet werden. Es dauert bis zu 90 Minuten, bis die Registrierung über das CDN-Netzwerk weitergegeben wurde. Benutzer, die den CDN-Domänennamen sofort zu verwenden versuchen, sehen u. U. den Statuscode 404, bis die Inhalte über das CDN verfügbar sind.


## Schritt 4: Zugriff auf CDN-Inhalte

Verwenden Sie für den Zugriff auf die im CDN zwischengespeicherten Inhalte den im Portal bereitgestellten CDN-URL. Die Adresse eines zwischengespeicherten Blobs hat etwa folgendes Format:

http://<*EndpunktName*>.azureedge.net/<*meinÖffentlicherContainer*>/<*BlobName*>

> [AZURE.NOTE] Sobald Sie den CDN-Zugriff auf ein Speicherkonto oder einen gehosteten Dienst aktivieren, kommen alle öffentlich verfügbaren Objekte für den CDN-Edgezwischenspeicher in Frage. Wenn Sie ein Objekt ändern, das derzeit im CDN zwischengespeichert ist, stehen die neuen Inhalte erst dann über das CDN zur Verfügung, wenn das CDN seine Inhalte aktualisiert, wenn die Lebensdauer der zwischengespeicherten Inhalte abläuft.

## Schritt 5: Entfernen von Inhalten aus dem CDN

Wenn ein Objekt nicht mehr im Azure Content Delivery Network (CDN) zwischengespeichert werden soll, können Sie einen der folgenden Schritte ausführen:

-   Sie können den öffentlichen Container zu einem privaten machen. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../storage-manage-access-to-resources/).
-   Sie können den CDN-Endpunkt mit dem Verwaltungsportal deaktivieren oder löschen.
-   Sie können den gehosteten Dienst so ändern, dass er nicht mehr auf Anforderungen des Objekts antwortet.

Ein bereits im CDN zwischengespeichertes Objekt bleibt zwischengespeichert, bis die Lebensdauer des Objekts abgelaufen ist oder der Endpunkt gelöscht wird. Wenn die Lebensdauer abläuft, prüft das CDN, ob der CDN-Endpunkt weiterhin gültig ist und ob weiterhin anonym auf das Objekt zugegriffen werden kann. Ist dies nicht der Fall, wird das Objekt nicht mehr zwischengespeichert.


## Zusätzliche Ressourcen

-   [Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[new-cdn-profile]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png

<!---HONumber=AcomDC_0302_2016-->