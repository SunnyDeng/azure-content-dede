<properties 
	 pageTitle="Aktivieren des CDN (Content Delivery Network) für Azure" 
	 description="Dieses Thema zeigt, wie Sie das Content Delivery Network (CDN) für Azure aktivieren." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="camsoper" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="12/02/2015" 
	 ms.author="casoper"/>



#Aktivieren des Content Delivery Network (CDN, Netzwerk für die Inhaltsübermittlung) für Azure  

Das CDN kann für Ihren Ursprung über das Azure-Verwaltungsportal aktiviert werden. Es werden verschiedene integrierte Azure-Ursprünge unterstützt, darunter Web-Apps, Blob Storage und Cloud Storage. Sie können das CDN auch für Ihre Azure Media Services-Streamingendpunkte aktivieren. Wenn der Ursprung keiner dieser Azure-Dienste ist oder an anderer Stelle außerhalb von Azure gehostet wird, können Sie auch einen benutzerdefinierten Ursprung erstellen. Sobald Sie einen CDN-Endpunkt für Ihren Ursprung aktivieren, kommen alle öffentlich verfügbaren Objekte für das CDN-Edgecaching in Frage.

## Erstellen eines neuen CDN-Profils

Ein CDN-Profil ist eine Sammlung von CDN-Endpunkten. Jedes Profil enthält mindestens einen CDN-Endpunkt. Sie können mehrere Profile verwenden, um Ihre CDN-Endpunkte nach Internetdomäne, Webanwendung oder anderen Kriterien zu organisieren.

**So erstellen Sie ein neues CDN-Profil**

1. Klicken Sie im [Azure-Verwaltungsportal](https://portal.azure.com) oben links auf **Neu**. Wählen Sie auf dem Blatt **Neu** erst **Medien + CDN** und dann **CDN** aus.

    Das Blatt für das neue CDN-Profil wird angezeigt.
    
    ![Neues CDN-Profil][new-cdn-profile]

2. Geben Sie einen Namen für das CDN-Profil ein.

3. Wählen Sie einen **Tarif** aus, oder verwenden Sie den Standardtarif.

4. Wählen Sie eine **Ressourcengruppe** aus, oder erstellen Sie eine. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview/#resource-groups).

5. Wählen Sie das **Abonnement** für dieses CDN-Profil aus.

6. Wählen Sie einen **Speicherort** aus. Dies ist der Azure-Speicherort, an dem Ihre CDN-Profilinformationen gespeichert werden. Dies hat keine Auswirkung auf die Speicherorte von CDN-Endpunkten. Es muss nicht derselbe Speicherort wie für das Speicherkonto sein.

7. Klicken Sie auf die Schaltfläche **Erstellen**, um das neue Profil zu erstellen.

## Erstellen eines neuen CDN-Endpunkts

**So erstellen Sie einen neuen CDN-Endpunkt für das Speicherkonto**

1. Navigieren Sie im [Azure-Verwaltungsportal](https://portal.azure.com) zu Ihrem CDN-Profil. Eventuell haben Sie es im vorherigen Schritt an das Dashboard angeheftet. Andernfalls können Sie es ermitteln, indem Sie auf **Durchsuchen**, auf **CDN-Profile** und dann auf das Profil klicken, dem Sie den Endpunkt hinzufügen möchten.

    Das Blatt für das CDN-Profil wird angezeigt.
    
    ![CDN-Profil][cdn-profile-settings]
    
2. Klicken Sie auf die Schaltfläche **Endpunkt hinzufügen**.

    ![Schaltfläche „Endpunkt hinzufügen“][cdn-new-endpoint-button]

    Das Blatt **Endpunkt hinzufügen** wird angezeigt.
    
    ![Blatt „Endpunkt hinzufügen“][cdn-add-endpoint]

3. Geben Sie einen **Namen** für diesen CDN-Endpunkt ein. Dieser Name wird für den Zugriff auf die zwischengespeicherten Ressourcen in der Domäne `<EndpointName>.azureedge.net` verwendet.

4. Wählen Sie in der Dropdownliste **Ursprungstyp** Ihren Ursprungstyp aus.
	
	![CDN-Ursprung](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. Wählen Sie in der Dropdownliste **Origin hostname** Ihre Ursprungsdomäne aus. In der Dropdownliste werden alle verfügbaren Ursprungstypen aufgelistet, die Sie in Schritt 4 angegeben haben. Bei Auswahl von *Benutzerdefinierter Ursprung* als **Ursprungstyp** geben Sie die Domäne Ihres benutzerdefinierten Ursprungs ein.

6. Geben Sie im Textfeld **Ursprünglicher Pfad** den Pfad zu den Ressourcen ein, die Sie zwischenspeichern möchten, oder lassen Sie das Feld leer, um das Zwischenspeichern aller Ressourcen in der Domäne zuzulassen, die Sie in Schritt 5 angegeben haben.

7. Geben Sie unter **Header des Ursprungshosts** den Hostheader ein, den das CDN bei jeder Anforderung senden soll, oder übernehmen Sie den Standardwert.

8. Geben Sie unter **Protokoll** und **Origin port** die Protokolle und Ports an, über die auf die Ressourcen am Ursprung zugegriffen werden soll. Die Clients nutzen weiterhin dieselben Protokolle und Ports für den Zugriff auf Ressourcen im CDN. Sie müssen mindestens ein Protokoll (HTTP oder HTTPS) auswählen.

9. Klicken Sie auf die Schaltfläche **Hinzufügen**, um den neuen Endpunkt zu erstellen.

10. Sobald der Endpunkt erstellt wurde, wird er in einer Liste von Endpunkten für das Profil angezeigt. In der Listenansicht werden der URL für den Zugriff auf zwischengespeicherte Inhalte sowie die Ursprungsdomäne angezeigt.

    ![CDN-Endpunkt][cdn-endpoint-success]

    > [AZURE.NOTE]Der Endpunkt kann nicht sofort verwendet werden. Es dauert bis zu 90 Minuten, bis die Registrierung über das CDN-Netzwerk weitergegeben wurde. Benutzer, die den CDN-Domänennamen sofort zu verwenden versuchen, sehen u. U. den Statuscode 404, bis die Inhalte über das CDN verfügbar sind.

##Weitere Informationen
[Zuordnen von CDN-Inhalten (Content Delivery Network) zu einer benutzerdefinierten Domäne](cdn-map-content-to-custom-domain.md)

[new-cdn-profile]: ./media/cdn-create-new-endpoint/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
 

<!---HONumber=AcomDC_1203_2015-->