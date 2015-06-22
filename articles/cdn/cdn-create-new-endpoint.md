<properties 
	 pageTitle="Aktivieren des Content Delivery Network (CDN, Netzwerk für die Inhaltsübermittlung) für Azure" 
	 description="In diesem Thema zeigt, wie Sie das Content Delivery Network (CDN) für Azure aktivieren." 
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
	 ms.date="03/05/2015" 
	 ms.author="mazha"/>



# Aktivieren des Content Delivery Network (CDN, Netzwerk für die Inhaltsübermittlung) für Azure  

Sobald Sie einen CDN-Endpunkt für Ihren Ursprung, z. B. ein Speicherkonto oder einen Cloud-Dienst aktivieren, kommen alle öffentlich verfügbaren Objekte für die CDN-Edgezwischenspeichung in Frage.  


## So erstellen Sie einen neuen CDN-Endpunkt  

1.	Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.
2.	Klicken Sie im Navigationsbereich auf **CDN**.
3.	Klicken Sie im Menüband auf **Neu**. Klicken Sie im Dialogfeld **Neu** auf **App Services**, dann auf **CDN** und schließlich auf **Schnellerfassung**.
4.	Wählen Sie in der Dropdownliste **Ursprungsdomäne** in der Liste der verfügbaren Ursprungstypen das gewünschte Ziel aus.
5.	Klicken Sie auf die Schaltfläche **Erstellen**, um den neuen Endpunkt zu erstellen.




> Hinweis: Die für den Endpunkt erstellte Konfiguration ist nicht sofort verfügbar. Die Verteilung der Registrierung über das CDN-Netzwerk kann bis zu 60 Minuten dauern. Benutzer, die den CDN-Domänennamen sofort zu verwenden versuchen, sehen u. U. den Statuscode 400 (Unzulässige Anforderung), bis die Inhalte über das CDN verfügbar sind.

# Siehe auch
[Zuordnen von Content Delivery Network (CDN)-Inhalten zu einer benutzerdefinierten Domäne](cdn-map-content-to-custom-domain.md)

<!--HONumber=49--> 