<properties 
	pageTitle="Löschen eines Azure CDN-Endpunkts" 
	description="Erfahren Sie, wie alle zwischengespeicherten Inhalte aus einem CDN-Endpunkt gelöscht werden." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>
	
# Löschen eines Azure CDN-Endpunkts

## Übersicht 

Azure CDN-Edgeknoten speichern Assets zwischen, bis die Gültigkeitsdauer (Time-to-live, TTL) des Assets abläuft. Wenn ein Client nach Ablauf der TTL des Assets das Asset aus dem Edgeknoten anfordert, ruft der Edgeknoten eine neue aktualisierte Kopie des Assets ab, um die Clientanforderung zu erfüllen und den Cache zu aktualisieren.

Manchmal möchten Sie möglicherweise zwischengespeicherten Inhalt aus allen Edgeknoten löschen und sie zwingen, neue aktualisierte Assets abzurufen. Als Gründe hierfür kommen z. B. Updates Ihrer Webanwendung oder schnelle Aktualisierung von Assets, die falsche Informationen enthalten, infrage.

Dieses Lernprogramm führt Sie durch das Löschen von Assets aus allen Edgeknoten eines Endpunkts.

## Exemplarische Vorgehensweise

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem CDN-Profil mit dem Endpunkt, den Sie löschen möchten.

2. Klicken Sie auf dem CDN-Profilblatt auf die Schaltfläche zum Löschen.
	
	![CDN-Profilblatt](./media/cdn-purge-endpoint/cdn-profile-blade.png)
	
	Das Blatt „Löschen“ wird geöffnet.
	
	![CDN-Löschblatt](./media/cdn-purge-endpoint/cdn-purge-blade.png)
	
3. Wählen Sie auf dem Blatt „Löschen“ die Dienstadresse, die Sie in der URL-Dropdownliste löschen möchten.

	![Löschformular](./media/cdn-purge-endpoint/cdn-purge-form.png)
	
	> [AZURE.NOTE] Um das Blatt „Löschen“ aufzurufen, können Sie auch auf dem Blatt des CDN-Endpunkts auf die Schaltfläche **Löschen** klicken. In diesem Fall ist im **URL**-Feld die Adresse des Diensts dieses Endpunkts vorgegeben.
	
4. Wählen Sie, welche Assets Sie aus dem Edgeknoten löschen möchten. Wenn Sie alle Assets löschen möchten, klicken Sie auf das Kontrollkästchen **Alles löschen**. Geben Sie andernfalls den vollständigen Pfad jedes Assets, das Sie löschen möchten (z. B. */pictures/kitten.png*), in das Textfeld **Pfad** ein.

	> [AZURE.TIP] Nachdem Sie Text eingegeben haben, werden weitere **Pfad**-Textfelder angezeigt, damit Sie eine Liste mit mehreren Assets erstellen können. Sie können Assets aus der Liste löschen, indem Sie auf die Schaltfläche mit den Auslassungspunkten (...) klicken.
	>
	> Die Pfade müssen eine relative URL sein. Sternchen (*) können als Platzhalter verwendet werden.
	
5. Klicken Sie auf die Schaltfläche **Löschen**.

	![Löschschaltfläche](./media/cdn-purge-endpoint/cdn-purge-button.png)
	

## Weitere Informationen
- [Vorabladen von Assets auf einen Azure CDN-Endpunkt](cdn-preload-endpoint.md)
- [Azure CDN-REST-API-Referenz – Löschen oder Vorabladen eines Endpunkts](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0128_2016-->