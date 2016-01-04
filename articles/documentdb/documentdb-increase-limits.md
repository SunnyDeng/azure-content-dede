<properties
	pageTitle="Anfordern von erhöhten DocumentDB-Kontolimits | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine Anpassung der DocumentDB-Limits wie z. B. der Anzahl der zulässigen Sammlungen, gespeicherten Prozeduren und Abfrageklauseln anfordern."
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/22/2015"
	ms.author="anhoh"/>

# Anfordern von erhöhten DocumentDB-Kontolimits

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) verfügt über einen Satz standardmäßiger Grenzwerte und Kontingente. Verschiedene Kontingente können durch Kontaktaufnahme mit dem Azure-Support angepasst werden. In diesem Artikel wird veranschaulicht, wie Sie die Erhöhung eines Kontolimits anfordern.

Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

-	Welche DocumentDB-Kontokontingente können durch Kontaktaufnahme mit dem Azure-Support angepasst werden?
-	Wie kann ich die Anpassung eines DocumentDB-Kontokontingents anfordern?

##<a id="AdjustableQuotas"></a> Anpassbare DocumentDB-Kontokontingente

Die folgende Tabelle beschreibt die DocumentDB-Kontingente, die durch Kontaktaufnahme mit dem Azure-Support angepasst werden können:

|Entität |Kontingent (Standardangebot)|
|-------|--------|
|Datenbankkonten |5
|Anzahl gespeicherter Prozeduren, Trigger und UDFs pro Sammlung |Jeweils 25
|Maximale Sammlungen pro Datenbankkonto |100
|Maximaler Dokumentspeicher pro Datenbank (100 Sammlungen) |1 TB
|Maximale Anzahl von UDFs pro Abfrage |2
|Maximale Anzahl von JOINs pro Abfrage |5
|Maximale Anzahl von UND-Klauseln pro Abfrage |20
|Maximale Anzahl von ODER-Klauseln pro Abfrage |10
|Maximale Anzahl von Werten pro IN-Ausdruck |100
|Maximale Anzahl von Punkten in einem Polygon-Argument in einer ST\_WITHIN-Abfrage |16
|Maximale Anzahl von Sammlungserstellungen pro Minute |5
|Maximale Anzahl von Skalierungsvorgängen pro Minute |5

##<a id="RequestQuotaIncrease"></a>Anfordern einer Kontingentanpassung
Die folgenden Schritte zeigen, wie Sie eine Kontingentanpassung anfordern.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Durchsuchen** und dann auf **Hilfe & Support**.

	![Screenshot: Start von Hilfe & Support](media/documentdb-increase-limits/helpsupport.png)

2. Klicken Sie auf dem Blatt **Hilfe & Support** auf **Support erhalten**.

	![Screenshot der Erstellung eines Supporttickets](media/documentdb-increase-limits/getsupport.png)

3. Klicken Sie im Blatt **Neue Supportanfrage** auf **Grundlagen**. Legen Sie anschließend **Problemtyp** als **Kontingent** fest und wählen Sie für **Abonnement** Ihr Abonnement, das Ihr DocumentDB-Konto hostet. Legen Sie **Dienst** als **DocumentDB** fest und **Supportplan** als **Kontingentsupport – enthalten**. Klicken Sie anschließend auf **Next**.

	![Screenshot des Anforderungstyps für das Supportticket](media/documentdb-increase-limits/supportrequest1.png)

4. Wählen Sie im Blatt **Problem** einen Schweregrad. Legen Sie **Problemtyp** auf **DocumentDB** und fügen Sie in **Details** Informationen über Ihre Kontingenterhöhung ein. Klicken Sie auf **Weiter**.

	![Screenshot der Abonnementauswahl für das Supportticket](media/documentdb-increase-limits/supportrequest2.png)

5. Geben Sie abschließend Ihre Kontaktinformationen im Blatt **Kontaktinformationen** ein.

	![Screenshot der Ressourcenauswahl für das Supportticket](media/documentdb-increase-limits/supportrequest3.png)

Sobald das Supportticket erstellt wurde, sollten Sie per E-Mail eine Supportanforderungsnummer erhalten. Sie können die Supportanforderung auch anzeigen, indem Sie auf dem Blatt **Hilfe & Support** auf **Supportanforderungen verwalten** klicken.

![Screenshot des Blatts für Supportanforderungen](media/documentdb-increase-limits/supportrequest4.png)


##<a name="NextSteps"></a> Nächste Schritte
- Um weitere Informationen zu DocumentDB zu erhalten, klicken Sie [hier](http://azure.com/docdb).

<!---HONumber=AcomDC_1203_2015-->