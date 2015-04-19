<properties 
	pageTitle="Verwenden von Import/Export zum Übertragen von Daten in den Blob-Speicher | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Import- und Exportaufträge im Azure-Verwaltungsportal erstellen, um Daten in den Blob-Speicher zu übertragen." 
	authors="tamram" 
	manager="adinah" 
	editor="" 
	services="storage" 
	documentationCenter=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="tamram"/>


# Verwenden des Microsoft Azure Import-/Export-Diensts zum Übertragen von Daten in den Blob-Speicher

Sie können den Microsoft Azure Import-/Exportdienst verwenden, um große Mengen an Dateidaten in den Azure-Blobspeicher zu übertragen, wenn das Hochladen über das Netzwerk sehr teuer oder nicht praktikabel ist. Außerdem können Sie mit dem Import-/Export-Dienst auch große Mengen an Daten auf Blob-Speicher schnell und kostengünstig auf Ihre lokalen Installationen übertragen.

Sie können eine oder mehrere Festplatten mit Daten an ein Azure-Datacenter schicken, um eine große Menge an Dateidaten auf Blob-Speicher zu übertragen. In diesem Rechenzentrum werden Ihre Daten in Ihr Speicherkonto hochgeladen. Ähnlich funktioniert das Exportieren von Daten auf Blob-Speicher: Sie können leere Festplatten an ein Azure-Datacenter schicken. Dort werden Blob-Daten von Ihrem Speicherkonto auf Ihre Festplatten kopiert und an Sie zurückgesendet. Bevor Sie ein Laufwerk einsenden, das Daten enthält, verschlüsseln Sie die Daten auf dem Laufwerk. Wenn der Import-/Export-Dienst Ihre Daten exportiert, um Ihnen diese zu senden, werden die Daten vor dem Versand ebenfalls verschlüsselt.

Es gibt zwei Möglichkeiten für die Erstellung und die Verwaltung von Import- und Exportaufträgen:

- Über das Azure Verwaltungsportal
- Über eine REST-Schnittstelle zum Dienst

Dieser Artikel bietet eine Übersicht über den Import-/Export-Dienst und beschreibt die Verwendung des Verwaltungsportals für den Import-/Export-Dienst. Weitere Informationen zur REST-API finden Sie unter [Azure Import/Export Service REST API Reference (Import-/Export-Dienst REST-API-Verweis, in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkID=329099).

## Übersicht über den Import-/Export-Dienst ##

Zunächst erstellen Sie einen  *job*, um mit dem Importieren oder Exportieren von Blob-Speicher zu beginnen. Ein Auftrag kann ein  *import job* oder ein  *export job* sein:

- Erstellen Sie einen Importauftrag, wenn Sie lokal gespeicherte Daten auf Blobs in Ihrem Azure-Speicherkonto übertragen möchten.
- Erstellen Sie einen Exportauftrag, wenn Sie Daten, die derzeit als Blobs in Ihrem Speicherkonto gespeichert sind, auf Festplatten übertragen möchten, die dann an Sie geschickt werden.

Wenn Sie einen Auftrag erstellen, benachrichtigen Sie den Import-/Export-Dienst, dass Sie eine oder mehrere Festplatten an ein Azure-Datacenter schicken werden. Bei einem Importauftrag verschicken Sie Festplatten, die Dateidaten enthalten. Bei einem Exportauftrag verschicken Sie leere Festplatten.

Um Ihr Laufwerk für den Versand für einen Importauftrag vorzubereiten, führen Sie das **Microsoft Azure Import-/Exporttool** aus. Dabei werden Ihre Daten auf das Laufwerk kopiert, die Daten auf dem Laufwerk mit BitLocker verschlüsselt und die Journaldateien für das Laufwerk erstellt, die weiter unten besprochen werden.

> [AZURE.NOTE] Die Daten auf dem Laufwerk müssen mit BitLocker-Laufwerkverschlüsselung verschlüsselt sein. So sind Ihre Daten beim Transport geschützt. Bei einem Exportauftrag verschlüsselt der Import-/Export-Dienst Ihre Daten, bevor das Laufwerk an Sie zurückgesendet wird.

Für die Erstellung eines Import- oder Exportauftrags benötigen Sie außerdem die  *drive ID*. Dies ist die Seriennummer, die einer bestimmten Festplatte durch den Laufwerkhersteller zugewiesen wurde. Die Laufwerk-ID ist außen auf dem Laufwerk abgebildet. 

<h3>Anforderungen und Umfang</h3>

1.	**Abonnement und Speicherkonten:** Für die Nutzung des Import-/Exportdienstes benötigen Sie ein gültiges Azure-Abonnement und mindestens ein Speicherkonto. Jeder Auftrag kann nur zur Übertragung von Daten auf oder von jeweils einem Speicherkonto verwendet werden. Dies bedeutet, dass sich ein Auftrag nicht über mehrere Speicherkonten erstrecken kann. Informationen zum Erstellen eines neuen Speicherkontos erhalten Sie unter [Vorgehensweise: Erstellen eines Speicherkontos](storage-create-storage-account.md).
2.	**Festplatten:** Nur 3,5 Zoll SATA II/III-Festplatten werden zur Verwendung mit dem Import-/Exportdienst unterstützt. Festplatten mit einer Größe von mehr als 4 TB werden nicht unterstützt. Für Importaufträge wird nur das erste Datenvolume auf der Festplatte verarbeitet. Das Datenvolume muss mit NTFS formatiert worden sein. Bei den meisten Computern können Sie mit einem SATA II/III-USB-Adapter ein externes SATA II/III-Laufwerk anfügen.
3.	**BitLocker-Verschlüsselung:** Alle auf den Festplatten gespeicherten Daten müssen mit BitLocker verschlüsselt sein. Dabei müssen die Verschlüsselungsschlüssel durch numerische Kennwörter geschützt sein.
4.	**Blob-Speicherziele:** Daten können auf Blockblobs und Seitenblobs hochgeladen bzw. von dort heruntergeladen werden.
5.	**Anzahl der Aufträge:** Jeder Kunde kann bis zu 20 aktive Aufträge pro Speicherkonto haben.
6.	**Maximale Größe eines Auftrags:** Die Größe eines Auftrags wird durch die Kapazität der verwendeten Festplatten und die maximale Menge an Daten, die in einem Speicherkonto gespeichert werden kann, bestimmt. Jeder Auftrag darf maximal 10 Festplatten enthalten.

## Erstellen eines Importauftrags im Verwaltungsportal ##

Erstellen Sie einen Importauftrag, um den Import-/Export-Dienst darüber zu informieren, dass Sie ein oder mehrere Laufwerke mit Daten an das Datacenter schicken werden, um deren Inhalte in Ihr Speicherkonto zu importieren.

<h3>Vorbereiten Ihrer Laufwerke</h3>

Bevor Sie einen Importauftrag erstellen, müssen Sie Ihre Laufwerke mit dem Microsoft Azure Import-/Exporttool vorbereiten. Weitere Details zum Microsoft Azure Import-/Exporttool finden Sie in der [Referenz zum Microsoft Azure Import-/Exporttool](http://go.microsoft.com/fwlink/?LinkId=329032). Sie können das [Microsoft Azure Import-/Exporttool](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) als eigenständiges Paket herunterladen.
  
Führen Sie die folgenden drei Schritte durch, um Ihre Laufwerke vorzubereiten: 

1.	Bestimmen Sie die zu importierenden Daten und die Anzahl der benötigten Laufwerke.
2.	Geben Sie die Ziel-Blobs für Ihre Daten im Azure Blob-Dienst an.
3.	Verwenden Sie das Microsoft Azure Import-/Exporttool, um Ihre Daten auf eine oder mehrere Festplatten zu kopieren.

Das Microsoft Azure-Import-/Exporttool generiert für jedes Laufwerk bei der Vorbereitung eine  *drive journal*-Datei. Die Laufwerkprotokolldatei wird auf Ihrem lokalen Computer gespeichert, nicht auf dem Laufwerk selbst. Beim Erstellen des Importauftrags laden Sie die Protokolldatei hoch. Eine Laufwerkprotokolldatei enthält die Laufwerk-ID und die BitLocker-Schlüssel sowie weitere Informationen über das Laufwerk.  

<h3>Importauftrag erstellen</h3>

1.	Sobald Sie Ihr Laufwerk vorbereitet haben, navigieren Sie zum Speicherkonto im Verwaltungsportal, und zeigen Sie das 	Dashboard an. Klicken Sie unter <strong>Schnelleinsicht</strong> auf <strong>Importauftrag erstellen</strong>. 
 
2.	Geben Sie im ersten Schritt des Assistenten an, dass Sie Ihr Laufwerk vorbereitet haben und dass Sie die Laufwerkprotokolldatei zur Hand haben.
 
3.	Geben Sie im zweiten Schritt die Kontaktinformationen des Ansprechpartners für diesen Importauftrag an. Wenn Sie ausführliche Protokolldaten für den Importauftrag speichern möchten, wählen Sie die Option <strong>Das ausführliche Protokoll im Blob-Container  'waimportexport' speichern</strong>.

4.	Laden Sie im dritten Schritt die Protokolldateien hoch, die Sie während der Vorbereitung des Laufwerks erhalten haben. Sie müssen pro vorbereitetem Laufwerk eine Datei hochladen.

	![Create import job - Step 3][import-job-03]

5.	Geben Sie im vierten Schritt einen beschreibenden Namen für den Importauftrag ein. Beachten Sie, dass der eingegebene Name nur Kleinbuchstaben, Ziffern, Trennstriche und Unterstriche enthalten darf, mit einem Buchstaben beginnen muss und keine Leerzeichen enthalten darf. 	Mithilfe des ausgewählten Namens können Sie Ihre Aufträge während und nach der Bearbeitung nachverfolgen.

	Wählen Sie anschließend Ihr Rechenzentrum aus der Liste aus. Unter Rechenzentrumsregion wird das Rechenzentrum und die Adresse angegeben, an die Sie Ihr Paket schicken müssen. Weitere Informationen finden Sie unten in den FAQ.

6. 	Wählen Sie in Schritt 5 Ihren Rücktransporteur aus und geben Sie Ihr Kundenkonto beim Transportunternehmen an. Microsoft verwendet diese Kontodaten, um Ihre Laufwerke nach Abschluss des Importauftrags an Sie zurückzuschicken.

	Falls Sie Ihre Nachverfolgungsnummer haben, wählen Sie Ihr Transportunternehmen in der Liste aus und geben Sie die Nummer ein. 

	Wenn Sie noch keine Tracking-Nummer haben, wählen Sie **Ich gebe meine Versandinformationen für diesen Importauftrag an, nachdem ich mein Paket versendet habe**, und schließen Sie den Importprozess ab.

7. Um Ihre Nachverfolgungsnummer einzugeben, nachdem Sie Ihr Paket versendet haben, kehren Sie zur Seite **Import/Export** für Ihr Speicherkonto im Verwaltungsportal zurück, wählen Ihren Auftrag in der Liste aus und klicken auf **Versandinformationen**. Navigieren Sie durch den Assistenten und geben Sie Ihre Nachverfolgungsnummer in Schritt 2 ein.

	Falls der Auftrag den Status "Erstellung", "Versand" oder "Übertragung" hat, können Sie außerdem Ihre Kontonummer beim Transportunternehmen in Schritt 2 des Assistenten eingeben. Falls der Auftrag den Status "Verpackung" hat, können Sie Ihre Kontonummer beim Transportunternehmen nicht mehr ändern. 

## Erstellen eines Exportauftrags im Verwaltungsportal ##

Erstellen Sie einen Exportauftrag, um den Import-/Export-Dienst darüber zu informieren, dass Sie ein oder mehrere leere Laufwerke an das Datacenter schicken. So können die Daten von Ihrem Speicherkonto auf die Laufwerke exportiert werden, und die Laufwerke werden Ihnen dann zugeschickt.

1. 	Navigieren Sie zum Speicherkonto im Verwaltungsportal, und zeigen Sie das Dashboard an, um einen Exportauftrag zu erstellen. 	Klicken Sie unter <strong>Schnelleinsicht</strong> auf <strong>Exportauftrag erstellen</strong>, und fahren Sie mit dem Assistenten fort.

2. 	Geben Sie im zweiten Schritt die Kontaktinformationen des Ansprechpartners für diesen Exportauftrag an. Wenn Sie 	ausführliche Protokolldaten für den Exportauftrag speichern möchten, wählen Sie die Option <strong>Das ausführliche Protokoll im Blob-Container  'waimportexport' speichern</strong>.

3.	Legen Sie im dritten Schritt fest, welche Blob-Daten Sie von Ihrem Speicherkonto auf Ihr leeres Laufwerk oder Ihre 	Laufwerke exportieren möchten. Sie können alle Blob-Daten des Speicherkontos exportieren, oder Sie legen fest, welche Blobs oder Blob-Sätze exportiert werden sollen.

	![Create export job - Step 3][export-job-03]

	- Verwenden Sie die Auswahl **Equal To**, und geben Sie den relativen Pfad zu dem Blob an, beginnend mit dem Containernamen, um den zu exportierenden Blob festzulegen. Verwenden Sie $root, um den Stammcontainer festzulegen.
	- Verwenden Sie die Auswahl **Starts With**, und geben Sie das Präfix beginnend mit einem Schrägstrich "/" an, um alle Blobs festzulegen, die mit einem Präfix beginnen. Bei dem Präfix kann es sich um das Präfix des Containernamens, den vollständigen Containernamen oder den vollständigen Containernamen gefolgt vom Präfix des Blob-Namens handeln.

	Die Tabelle zeigt Beispiele für gültige Blob-Pfade:

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
		<tbody>
			<tr>
				<td><strong>Auswahl</strong></td>
				<td><strong>Blob-Pfad</strong></td>
				<td><strong>Beschreibung</strong></td>
			</tr>
			<tr>
				<td>Beginnt mit</td>
				<td>/</td>
				<td>Exportiert alle Blobs im Speicherkonto</td>
			</tr>
			<tr>
				<td>Beginnt mit</td>
				<td>/$root/</td>
				<td>Exportiert alle Blobs im Stammcontainer</td>
			</tr>
			<tr>
				<td>Beginnt mit</td>
				<td>/book</td>
				<td>Exportiert alle Blobs in allen Containern mit dem Präfix <strong>book</strong></td>
			</tr>
			<tr>
				<td>Beginnt mit</td>
				<td>/music/</td>
				<td>Exportiert alle Blobs im Container <strong>music</strong></td>
			</tr>
			<tr>
				<td>Beginnt mit</td>
				<td>/music/love</td>
				<td>Exportiert alle Blobs im Container <strong>music</strong>, die mit dem Präfix <strong>love</strong> beginnen</td>
			</tr>
			<tr>
				<td>Entspricht</td>
				<td>$root/logo.bmp</td>
				<td>Exportiert das Blob <strong>logo.bmp</strong> im Stammcontainer</td>
			</tr>
			<tr>
				<td>Entspricht</td>
				<td>videos/story.mp4</td>
				<td>Exportiert das Blob <strong>story.mp4</strong> im Container <strong>videos</strong></td>
			</tr>
		</tbody>
	</table>


4.	Geben Sie im vierten Schritt einen beschreibenden Namen für den Exportauftrag ein. Der eingegebene Name darf nur 	Kleinbuchstaben, Ziffern, Trennstriche und Unterstriche enthalten, muss mit einem Buchstaben beginnen und darf keine Leerzeichen enthalten.

	Die Datacenter-Region gibt das Datacenter an, an das Sie Ihr Paket schicken müssen. Weitere Informationen finden Sie unten in den FAQ.

5. 	Wählen Sie in Schritt 5 Ihren Rücktransporteur aus und geben Sie Ihr Kundenkonto beim Transportunternehmen an. Microsoft verwendet diese Kontodaten, um Ihre Laufwerke nach Abschluss des Exportauftrags an Sie zurückzuschicken.

	Falls Sie Ihre Nachverfolgungsnummer haben, wählen Sie Ihr Transportunternehmen in der Liste aus und geben Sie die Nummer ein. 

	Falls Sie noch keine Nachverfolgungsnummer haben, wählen Sie die Option <strong>Ich gebe die Versandinformationen für diesen Exportauftrag an, nachdem ich mein Paket versendet habe</strong> aus und schließen Sie den Exportvorgang ab.

6. Um Ihre Nachverfolgungsnummer einzugeben, nachdem Sie Ihr Paket versendet haben, kehren Sie zur Seite **Import/Export** für Ihr Speicherkonto im Verwaltungsportal zurück, wählen Ihren Auftrag in der Liste aus und klicken auf **Versandinformationen**. Navigieren Sie durch den Assistenten und geben Sie Ihre Nachverfolgungsnummer in Schritt 2 ein.

	Falls der Auftrag den Status "Erstellung", "Versand" oder "Übertragung" hat, können Sie außerdem Ihre Kontonummer beim Transportunternehmen in Schritt 2 des Assistenten eingeben. Falls der Auftrag den Status "Verpackung" hat, können Sie Ihre Kontonummer beim Transportunternehmen nicht mehr ändern. 


## Auftragsstatus im Verwaltungsportal verfolgen ##

Sie können den Status Ihrer Import- oder Exportaufträge im Verwaltungsportal nachverfolgen. Navigieren Sie im Verwaltungsportal zu Ihrem Speicherkonto, und klicken Sie auf die Registerkarte **Import/Export**. Eine Liste Ihrer Aufträge wird auf der Seite angezeigt. Sie können die Liste nach Auftragsstatus, Auftragsname, Auftragstyp oder Tracking-Nummer filtern.

In der Tabelle sind die Bedeutungen der Auftragsstatus beschrieben:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td><strong>Auftragsstatus</strong></td>
			<td><strong>Beschreibung</strong></td>
		</tr>
		<tr>
			<td>Wird erstellt</td>
			<td>Ihr Auftrag wurde erstellt, aber Sie haben Ihre Versandinformationen noch nicht angegeben.</td>
		</tr>
		<tr>
			<td>Versand</td>
			<td>Ihr Auftrag wurde erstellt, und Sie haben Ihre Versandinformationen angegeben.</td>
		</tr>
		<tr>
			<td>Wird übertragen</td>
			<td>Ihre Daten werden von Ihrem Laufwerk (bei einem Importauftrag) oder auf Ihr Laufwerk (bei einem Exportauftrag) übertragen.</td>
		</tr>
		<tr>
			<td>Wird verpackt</td>
			<td>Die Übertragung Ihrer Daten ist abgeschlossen, und Ihre Festplatte wird für den Rückversand vorbereitet.</td>
		</tr>
		<tr>
			<td>Abgeschlossen</td>
			<td>Ihre Festplatte wurde an Sie zurückgeschickt.</td>
		</tr>
	</tbody>
</table>

## BitLocker-Schlüssel für einen Exportauftrag anzeigen ##

Bei Exportaufträgen können Sie die BitLocker-Schlüssel, die vom Dienst für Ihr Laufwerk erstellt wurden, anzeigen und kopieren, damit Sie Ihre exportierten Daten entschlüsseln können, sobald Sie die Laufwerke vom Azure-Datacenter erhalten. Navigieren Sie im Verwaltungsportal zu Ihrem Speicherkonto, und klicken Sie auf die Registerkarte **Import/Export**. Wählen Sie Ihren Exportauftrag in der Liste aus, und klicken Sie auf die Schaltfläche **Schlüssel anzeigen**. Die BitLocker-Schlüssel werden wie abgebildet angezeigt:

![View BitLocker keys for export job][export-job-bitlocker-keys]

## Häufig gestellte Fragen ##

<h3>Allgemein</h3>

**Wie viel kostet der Import-/Exportdienst?**

- Weitere Informationen hierzu finden Sie unter [Preisdetails](http://go.microsoft.com/fwlink/?LinkId=329033).

**Wie lange dauert der Import oder Export meiner Daten?**

- Man muss die Zeit für den Versand der Festplatten berücksichtigen sowie pro TB an Daten mehrere Stunden zum Kopieren der Daten.
 
**Welche Schnittstellentypen werden unterstützt?**

- Der Import-/Exportdienst unterstützt interne SATA II/III-Laufwerke (HDDs) im 3,5-Zoll-Format. Sie können die folgenden Konverter verwenden, um Daten von USB-Geräten vor dem Versand auf SATA zu übertragen:
	- Anker 68UPSATAA-02BU
	- Anker 68UPSHHDS-BU
	- Startech SATADOCK22UE 

> [AZURE.NOTE] Falls Sie einen Konverter haben, der nicht in der Liste enthalten ist, können Sie versuchen, das Microsoft Azure-Import-/Exporttool mit Ihrem Konverter zur Vorbereitung des Laufwerks auszuführen, bevor Sie einen unterstützten Konverter kaufen.

**Was soll ich tun, wenn ich mehr als 10 Laufwerke importieren oder exportieren möchte?**

- Jeder Import- oder Exportauftrag kann auf maximal 10 Laufwerke für den Import-/Exportdienst verweisen. Wenn Sie mehr als zehn Laufwerke verschicken möchten, können Sie mehrere Aufträge erstellen.

**Was geschieht, wenn ich aus Versehen eine HDD versende, die den unterstützten Anforderungen nicht entspricht?**

- Das Azure-Datacenter schickt das Laufwerk, das den unterstützten Anforderungen nicht entspricht, an Sie zurück. Wenn nur einige der Laufwerke in dem Paket die Anforderungen erfüllen, werden diese Laufwerke verarbeitet, und die Laufwerke, die die Anforderungen nicht erfüllen, werden an Sie zurückgeschickt.

<h3>Verwaltung von Import-/Exportaufträgen</h3>

**Was geschieht mit meinen Import-/Exportaufträgen, wenn ich mein Azure-Speicherkonto lösche?**

- Wenn Sie Ihr Speicherkonto löschen, werden damit auch all Ihre Azure Import-/Exportaufträge gelöscht.  

**Kann ich meinen Auftrag stornieren?**

- Sie können einen Auftrag stornieren, solange dieser den Status "Wird erstellt" oder "Wird versendet" hat.

**Wie lange kann ich den Status abgeschlossener Aufträge im Verwaltungsportal anzeigen?**

- Den Status abgeschlossener Aufträge können Sie bis zu 90 Tage lang anzeigen. Abgeschlossene Aufträge werden nach 90 Tagen gelöscht.

**Ist die BitLocker-Verschlüsselung zwingend erforderlich?**

- Ja. Alle Laufwerke müssen mit einem BitLocker-Schlüssel verschlüsselt sein.

**Formatieren Sie die Laufwerke, bevor Sie sie zurückschicken?**

- Nein, alle Laufwerke müssen mit BitLocker vorbereitet sein.

<h3>Versand</h3>

**Welche Kurierdienste werden unterstützt?**

- Für US- und europäische Regionen wird nur [Federal Express](http://www.fedex.com/us/oadr/) (FedEx) unterstützt. Alle Pakete werden per FedEx Ground oder FedEx International Economy zurückgeschickt.

- Für asiatische Regionen wird nur [DHL](http://www.dhl-welcome.com/Tutorial/) unterstützt. Alle Pakete werden per DHL Express Worldwide zurückgeschickt.

	> [AZURE.IMPORTANT] Sie müssen Ihre Tracking-Nummer an den Azure Import-/Export-Dienst übertragen, ansonsten kann Ihr Auftrag nicht verarbeitet werden.

**Entstehen Kosten für den Rückversand?**

- Microsoft verwendet die Kontonummer für das Transportunternehmen, die Sie bei der Auftragserstellung angegeben haben, um die Laufwerke vom Rechenzentrum an Ihre Absenderadresse zu schicken. Geben Sie daher unbedingt eine Kontonummer für ein Transportunternehmen an, das in der Region des Rechenzentrums unterstützt wird. Sie können ein Konto bei [FedEx](http://www.fedex.com/us/oadr/) (für USA und Europa) oder [DHL](http://www.dhl-welcome.com/Tutorial/) (Asien) erstellen, falls Sie kein solches Konto haben.

- Die Rücksendegebühren werden Ihrem Konto beim Transportunternehmen berechnet und hängen vom Transportunternehmen ab.

**Von wo aus kann ich meine Daten versenden bzw. wohin kann ich sie senden lassen?**

- Der Import-/Exportdienst unterstützt Import und Export von Daten in Speicherkonten in den folgenden Regionen:
	- USA (Ost) 
	- USA (West) 
	- USA (Mitte/Norden) 
	- USA (Mitte/Süden) 
	- Nordeuropa
	- Westeuropa
	- Ostasien
	- Südostasien

- Sie erhalten eine Lieferadresse in der Region, in der sich Ihr Speicherkonto befindet. Wenn Sie z. B. in den USA leben und sich Ihr Speicherkonto im Rechenzentrum in Westeuropa befindet, erhalten Sie für den Versand der Laufwerke eine Lieferadresse in Europa.

	> [AZURE.IMPORTANT] Beachten Sie, dass die physischen Medien beim Versand unter Umständen Ländergrenzen überqueren. Sie müssen sicherstellen, dass Ihre physischen Medien und Daten gemäß geltender Gesetze importiert bzw. exportiert werden. Prüfen Sie vor dem Versand der physischen Medien mit Ihren Rechtsberatern, ob Medien und Daten laut Gesetz an das entsprechende Rechenzentrum verschickt werden dürfen. So stellen Sie sicher, dass Ihre Daten zeitnah bei Microsoft eintreffen.

- Beim Versand Ihrer Pakete müssen Sie die Nutzungsbedingungen unter [Microsoft Azure-Nutzungsbedingungen] beachten(http://azure.microsoft.com/support/legal/services-terms/). 

**Kann ich bei Microsoft Laufwerke für Import-/Exportaufträge kaufen?**

- 	Nein, Sie müssen sowohl für den Import als auch den Export Ihre eigenen Laufwerke einsenden.

**Was muss mein Paket enthalten?**

- Bitte verschicken Sie nur Ihre Laufwerke. Legen Sie keine Gegenstände wie z. B. Strom- oder USB-Kabel bei.



[import-job-03]: ./media/storage-import-export-service/import-job-03.png
[export-job-03]: ./media/storage-import-export-service/export-job-03.png
[export-job-bitlocker-keys]: ./media/storage-import-export-service/export-job-bitlocker-keys.png
<!--HONumber=42-->
