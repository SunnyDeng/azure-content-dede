<properties 
	pageTitle="CDN – Überschreiben des HTTP-Standardverhaltens mithilfe des Regelmoduls" 
	description="Mit dem Regelmodul können Sie anpassen, wie HTTP-Anforderungen behandelt werden, z. B. Sperren der Übermittlung bestimmter Inhaltstypen, Definieren einer Zwischenspeicherungsrichtlinie oder Ändern von HTTP-Headern." 
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
	ms.date="12/02/2015" 
	ms.author="casoper"/>

# Überschreiben des HTTP-Standardverhaltens mithilfe des Regelmoduls

## Übersicht

Mit dem Regelmodul können Sie anpassen, wie HTTP-Anforderungen behandelt werden, z. B. Sperren der Übermittlung bestimmter Inhaltstypen, Definieren einer Zwischenspeicherungsrichtlinie und Ändern von HTTP-Headern. In diesem Tutorial wird die Erstellung einer Regel erläutert, die das Zwischenspeicherungsverhalten von CDN-Assets ändert.

> [AZURE.NOTE]Das Regelmodul ist ein Feature im CDN-Tarif „Premium“. Einen Vergleich der Features der CDN-Tarife „Standard“ und „Premium“ finden Sie in der [Übersicht über das Azure CDN](cdn-overview.md).

## Lernprogramm:

1. Klicken Sie auf dem Blatt „CDN-Profil“ auf die Schaltfläche **Verwalten**.

	![Schaltfläche „Verwalten“ auf dem CDN-Profilblatt](./media/cdn-rules-engine/cdn-rules-manage-btn.png)
	
	Das CDN-Verwaltungsportal wird geöffnet.
	
2. Klicken Sie auf die Registerkarte **HTTP Large**, gefolgt von **Regelmodul**.
	
	Die Optionen für eine neue Regel werden angezeigt.
	
	![Optionen für neue CDN-Regeln](./media/cdn-rules-engine/cdn-new-rule.png)

3. Geben Sie einen Namen in das Textfeld **Name/Beschreibung** ein.

4. Ermitteln Sie die Anforderungstypen, auf die die Regel angewendet wird. In der Standardeinstellung ist die Übereinstimmungsbedingung **Immer** ausgewählt. Für dieses Tutorial verwenden Sie **Immer**, lassen Sie die Option also ausgewählt.

	![CDN-Übereinstimmungsbedingung](./media/cdn-rules-engine/cdn-request-type.png)
	
	>[AZURE.TIP]In der Dropdownliste sind viele Typen von Übereinstimmungsbedingungen verfügbar. Durch Klicken auf das blaue Informationssymbol links neben der Übereinstimmungsbedingung wird die derzeit ausgewählte Bedingung ausführlicher erläutert.
	>
	>Eine vollständige Liste der Übereinstimmungsbedingungen finden Sie unter [CDN-Regelmodul – Übereinstimmungsbedingungen und Featuredetails](cdn-rules-engine-details.md#match-conditions).
	
5.  Klicken Sie auf die Schaltfläche **+** neben **Features**, um ein neues Feature hinzuzufügen. Wählen Sie in der Dropdownliste auf der linken Seite **Force Internal Max-Age** aus. Geben Sie **300** in das angezeigte Textfeld ein. Belassen Sie die übrigen Werte in ihrer Standardeinstellung.

	![CDN-Feature](./media/cdn-rules-engine/cdn-new-feature.png)

	>[AZURE.NOTE]Wie bei den Übereinstimmungsbedingungen können Sie durch Klicken auf das blaue Informationssymbol links neben dem neuen Feature Informationen zu diesem anzeigen lassen. Im Fall von **Force Internal Max-Age** überschreiben wir die Header **Cache-Control** und **Expires** des Assets, um zu steuern, wann der CDN-Edgeknoten das Asset aus dem Ursprung aktualisiert. Unser Beispiel von 300 Sekunden gibt an, dass der CDN-Edgeknoten das Asset für 5 Minuten zwischenspeichert, bevor es aus seinem Ursprung aktualisiert wird.
	>
	>Eine vollständige Liste der Features finden Sie unter [CDN-Regelmodul – Übereinstimmungsbedingungen und Featuredetails](cdn-rules-engine-details.md#features).
	
6.  Klicken Sie auf die Schaltfläche **Hinzufügen**, um die neue Regel zu speichern. Die neue Regel wartet jetzt auf ihre Genehmigung. Sobald sie genehmigt wurde, wird der Status von **Pending XML** in **Active XML** geändert.

## Überlegungen

- Die Reihenfolge, in der mehrere Regeln aufgelistet sind, beeinflusst deren Verarbeitung. Eine Regel kann die von einer vorherigen Regel angegebenen Aktionen überschreiben.

## Weitere Informationen
* [Regelmodul – Übereinstimmungsbedingungen und Featuredetails](cdn-rules-engine-details.md)
* [Übersicht über das Azure CDN](cdn-overview.md)
* [Echtzeitstatistiken in Microsoft Azure CDN](cdn-real-time-stats.md)
* [Erweiterte HTTP-Berichte](cdn-advanced-http-reports.md)
* [Analysieren der Edgeleistung](cdn-edge-performance.md)


	

<!---HONumber=AcomDC_1217_2015-->