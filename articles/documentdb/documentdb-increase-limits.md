<properties 
	pageTitle="Anfordern von erhöhten DocumentDB-Kontolimits | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Anpassung der DocumentDB-Limits wie z. B. der Anzahl der zulässigen Sammlungen, gespeicherten Prozeduren und Abfrageklauseln anfordern." 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="stbaro"/>


# Anfordern von erhöhten DocumentDB-Kontolimits

[Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) verfügt über einen Satz standardmäßiger Grenzwerte und Kontingente. Verschiedene Kontingente können durch Kontaktaufnahme mit dem Azure-Support angepasst werden. In diesem Artikel wird veranschaulicht, wie Sie die Erhöhung eines Kontolimits anfordern.

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
|Maximale Anzahl von UDFs pro Abfrage |1
|Maximale Anzahl von JOINs pro Abfrage |2
|Maximale Anzahl von UND-Klauseln pro Abfrage |5
|Maximale Anzahl von ODER-Klauseln pro Abfrage |5
|Maximale Anzahl von Werten pro IN-Ausdruck |100
|Maximale Anzahl von Sammlungserstellungen pro Minute |5
|Maximale Anzahl von Skalierungsvorgängen pro Minute |5

##<a id="RequestQuotaIncrease"></a>Anfordern einer Kontingentanpassung
Die folgenden Schritte zeigen, wie Sie eine Kontingentanpassung anfordern.

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com) auf **Durchsuchen** und dann auf **Hilfe & Support**.

	![Screenshot: Start von Hilfe & Support](media/documentdb-increase-limits/helpsupport.png)

2. Klicken Sie auf dem Blatt **Hilfe & Support** auf **Support erhalten**.

	![Screenshot der Erstellung eines Supporttickets](media/documentdb-increase-limits/getsupport.png)

3. Klicken Sie auf dem Blatt **Neue Supportanforderung** auf **Anforderungstyp**, und klicken Sie auf dem Blatt **Anforderungstyp** auf **Kontingente**.

	![Screenshot des Anforderungstyps für das Supportticket](media/documentdb-increase-limits/supportrequest1.png)

4. Wählen Sie auf dem Blatt **Abonnement** das Abonnement, das Ihr DocumentDB-Konto hostet.

	![Screenshot der Abonnementauswahl für das Supportticket](media/documentdb-increase-limits/supportrequest2.png)

5. Wählen Sie auf dem Blatt **Ressourcen** die Option **DocumentDB-Konten**.

	![Screenshot der Ressourcenauswahl für das Supportticket](media/documentdb-increase-limits/supportrequest3.png)

6. Wählen Sie auf dem Blatt **Supportplan** die Option **Support ohne Kontingente**.

	![Screenshot der Supportplanauswahl für das Supportticket](media/documentdb-increase-limits/supportrequest4.png)

7. Wählen Sie auf dem Blatt **Problem** die Problemkategorie **Anforderungen zum Erhöhen von Kontingenten oder des Kernspeichers – DocumentDB**.

	![Screenshot der Problemkategorieauswahl für das Supportticket](media/documentdb-increase-limits/supportrequest5.png)

8. Geben Sie auf dem Blatt **Beschreibung** eine Beschreibung der Anforderung ein. Stellen Sie sicher, dass Sie die anzufordernden spezifischen Kontingentanpassungen sowie das Konto bzw. die Konten angeben, für das/die die Anpassungen vorgenommen werden sollen.

	![Screenshot des Beschreibungstextfelds für das Supportticket](media/documentdb-increase-limits/supportrequest6.png)

9. Klicken Sie auf **Erstellen**.

	![Screenshot der Erstellungsschaltfläche für das Supportticket](media/documentdb-increase-limits/supportrequest7.png)

Sobald das Supportticket erstellt wurde, sollten Sie per E-Mail eine Supportanforderungsnummer erhalten. Sie können die Supportanforderung auch anzeigen, indem Sie auf dem Blatt **Hilfe & Support** auf **Supportanforderungen** klicken.

![Screenshot des Blatts für Supportanforderungen](media/documentdb-increase-limits/supportrequest8.png)
  

##<a name="NextSteps"></a> Nächste Schritte
- Um weitere Informationen zu DocumentDB zu erhalten, klicken Sie [hier](http://azure.com/docdb).
 

<!---HONumber=August15_HO6-->