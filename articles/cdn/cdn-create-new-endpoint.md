<properties 
	 pageTitle="Aktivieren des CDN (Content Delivery Network) für Azure" 
	 description="Dieses Thema zeigt, wie Sie das Content Delivery Network (CDN) für Azure aktivieren." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="09/01/2015" 
	 ms.author="mazha"/>



#Aktivieren des Content Delivery Network (CDN, Netzwerk für die Inhaltsübermittlung) für Azure  

Das CDN kann für Ihren Ursprung über das Azure-Verwaltungsportal aktiviert werden. Zurzeit sind folgende Ursprungstypen verfügbar: Web-Apps, Speicher, Clouddienste. Sie können das CDN auch für Ihre Azure Media Services-Streamingendpunkte aktivieren. Sobald Sie einen CDN-Endpunkt für Ihren Ursprung aktivieren, kommen alle öffentlich verfügbaren Objekte für das CDN-Edgecaching in Frage.

Beachten Sie, dass Sie auch einen benutzerdefinierten Ursprung erstellen können, der nicht zu Azure gehören muss.

##So erstellen Sie einen neuen CDN-Endpunkt  

1.	Melden Sie sich auf dem [Windows Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.
2.	Klicken Sie im Navigationsbereich auf **CDN**.
3.	Klicken Sie im Menüband auf **Neu**. Klicken Sie im Dialogfeld **Neu** auf **APP SERVICES**, dann auf **CDN** und schließlich auf **SCHNELLERFASSUNG**.
4.	Wählen Sie in der Dropdownliste **URSPRUNGSTYP** einen der verfügbaren Ursprungstypen aus.
	
	Die verfügbaren Ursprungs-URLs werden in der Dropdownliste **URSPRUNGS-URL** angezeigt.
	

	![createnew][createnew]

	Wenn Sie **Benutzerdefinierter Ursprung** wählen, können Sie eine benutzerdefinierte Ursprungs-URL eingeben. Hierbei muss es sich nicht um einen Azure-Ursprung handeln.

	![customorigin][customorigin]

	>[AZURE.NOTE]Zurzeit wird als Ursprung nur HTTP unterstützt, und Sie müssen die Media Services-Erweiterung verwenden, um Azure CDN für einen Azure Media Services-Streamingendpunkt zu aktivieren.
	
5.	Klicken Sie auf die Schaltfläche **Erstellen**, um den neuen Endpunkt zu erstellen.


>[AZURE.NOTE]Die für den Endpunkt erstellte Konfiguration ist nicht sofort verfügbar. Die Verteilung der Registrierung über das CDN-Netzwerk kann bis zu 60 Minuten dauern. Benutzer, die den CDN-Domänennamen sofort zu verwenden versuchen, sehen u. U. den Statuscode 400 (Unzulässige Anforderung), bis die Inhalte über das CDN verfügbar sind.

##Weitere Informationen
[Zuordnen von CDN-Inhalten (Content Delivery Network) zu einer benutzerdefinierten Domäne](cdn-map-content-to-custom-domain.md)

[createnew]: ./media/cdn-create-new-endpoint/cdn-create-new-account.png

[customorigin]: ./media/cdn-create-new-endpoint/cdn-custom-origin.png
 

<!---HONumber=Oct15_HO3-->