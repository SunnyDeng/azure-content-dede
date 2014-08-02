<properties linkid="manage-services-import-export" urlDisplayName="Azure Import/Export Service" pageTitle="Using import/export to transfer data to Blob Storage | Microsoft Azure" metaKeywords="" description="Learn how to create import and export jobs in the Azure Management Portal to transfer data to blob storage." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Using the Azure Import/Export Service to Transfer Data to Blob Storage" authors="tamram" />

Verwendung des Azure Import-/Export-Diensts für die Übertragung von Daten an Blob-Speicher
==========================================================================================

Mithilfe des Azure-Import-/Export-Diensts können Sie große Mengen an Dateidaten an Azure Blob-Speicher übertragen, beispielsweise wenn das Hochladen über das Netzwerk untragbar teuer oder nicht machbar ist. Außerdem können Sie mit dem Import-/Export-Dienst auch große Mengen an Daten auf Blob-Speicher schnell und kostengünstig auf Ihre lokalen Installationen übertragen.

Sie können eine oder mehrere Festplatten mit Daten an ein Azure-Datacenter schicken, um eine große Menge an Dateidaten auf Blob-Speicher zu übertragen. In diesem Rechenzentrum werden Ihre Daten in Ihr Speicherkonto hochgeladen. Ähnlich funktioniert das Exportieren von Daten auf Blob-Speicher: Sie können leere Festplatten an ein Azure-Datacenter schicken. Dort werden Blob-Daten von Ihrem Speicherkonto auf Ihre Festplatten kopiert und an Sie zurückgesendet. Bevor Sie ein Laufwerk einsenden, das Daten enthält, verschlüsseln Sie die Daten auf dem Laufwerk. Wenn der Import-/Export-Dienst Ihre Daten exportiert, um Ihnen diese zu senden, werden die Daten vor dem Versand ebenfalls verschlüsselt.

Es gibt zwei Möglichkeiten für die Erstellung und die Verwaltung von Import- und Exportaufträgen:

-   Über das Azure Verwaltungsportal
-   Über eine REST-Schnittstelle zum Dienst

Dieser Artikel bietet eine Übersicht über den Import-/Export-Dienst und beschreibt die Verwendung des Verwaltungsportals für den Import-/Export-Dienst. Weitere Informationen zur REST-API finden Sie unter [Import/Export Service REST API Reference](http://go.microsoft.com/fwlink/?LinkID=329099) (Import-/Export-Dienst REST-API-Verweis, in englischer Sprache).

Übersicht über den Import-/Export-Dienst
----------------------------------------

Zunächst erstellen Sie einen *Auftrag*, um mit dem Importieren oder Exportieren von Blob-Speicher zu beginnen. Ein Auftrag kann ein *Importauftrag* oder ein *Exportauftrag* sein:

-   Erstellen Sie einen Importauftrag, wenn Sie lokal gespeicherte Daten auf Blobs in Ihrem Azure-Speicherkonto übertragen möchten.
-   Erstellen Sie einen Exportauftrag, wenn Sie Daten, die derzeit als Blobs in Ihrem Speicherkonto gespeichert sind, auf Festplatten übertragen möchten, die dann an Sie geschickt werden.

Wenn Sie einen Auftrag erstellen, benachrichtigen Sie den Import-/Export-Dienst, dass Sie eine oder mehrere Festplatten an ein Azure-Datacenter schicken werden. Bei einem Importauftrag verschicken Sie Festplatten, die Dateidaten enthalten. Bei einem Exportauftrag verschicken Sie leere Festplatten.

Führen Sie das Tool **WAImportExport** aus, um Ihr Laufwerk für den Versand für einen Importauftrag vorzubereiten. Dieses Tool vereinfacht das Kopieren von Daten auf das Laufwerk, das Verschlüsseln der Daten auf dem Laufwerk mit BitLocker und das Generieren von Laufwerkprotokolldateien, die untern erläutert werden.

**Hinweis**

Die Daten auf dem Laufwerk müssen mit BitLocker-Laufwerkverschlüsselung verschlüsselt sein. So sind Ihre Daten beim Transport geschützt. Bei einem Exportauftrag verschlüsselt der Import-/Export-Dienst Ihre Daten, bevor das Laufwerk an Sie zurückgesendet wird.

Für die Erstellung eines Import- oder Exportauftrags benötigen Sie außerdem die *Laufwerk-ID*. Dies ist die Seriennummer, die einer bestimmten Festplatte durch den Laufwerkhersteller zugewiesen wurde. Die Laufwerk-ID ist außen auf dem Laufwerk abgebildet.

### Anforderungen und Umfang

1.  **Abonnement und Speicherkonten:** Sie müssen über ein Azure-Abonnement und ein oder mehrere Speicherkonten verfügen, um den Import-/Export-Dienst nutzen zu können. Bei jedem Auftrag können lediglich Daten auf ein oder von einem Speicherkonto übertragen werden. In anderen Worten: Ein Auftrag kann nicht mehrere Speicherkonten umfassen. Weitere Informationen zum Erstellen eines neuen Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](http://www.windowsazure.com/en-us/manage/services/storage/how-to-create-a-storage-account/).
2.  **Festplatten:** Für den Import-/Export-Dienst werden lediglich 3,5-Zoll-SATA II-Festplatten unterstützt. Bei der Vorschauversion werden Festplatten mit mehr als 4 TB nicht unterstützt. Bei Importaufträgen wird nur das erste Datenvolume auf dem Laufwerk verarbeitet. Das Datenvolume muss mit NTFS formatiert sein. An die meisten Computer können Sie eine SATA II-Festplatte extern mithilfe eines SATA II-USB-Adapters anschließen.
3.  **BitLocker-Verschlüsselung:** Alle auf den Festplatten gespeicherten Daten müssen mit BitLocker verschlüsselt sein. Dabei müssen die Verschlüsselungsschlüssel durch numerische Kennwörter geschützt sein.
4.  **Blob-Speicherziele:** Daten können auf Blockblobs und Seitenblobs hochgeladen bzw. von dort heruntergeladen werden.
5.  **Anzahl der Aufträge:** Ein Kunde kann pro Abonnement 20 aktive Aufträge haben.
6.  **Maximale Größe eines Auftrags:** Die Größe eines Auftrags wird durch die Kapazität der verwendeten Festplatten und der maximalen Menge an Daten, die in einem Speicherkonto gespeichert werden kann, bestimmt. Jeder Auftrag kann maximal zehn Festplatten umfassen.

Erstellen eines Importauftrags im Verwaltungsportal
---------------------------------------------------

Erstellen Sie einen Importauftrag, um den Import-/Export-Dienst darüber zu informieren, dass Sie ein oder mehrere Laufwerke mit Daten an das Datacenter schicken werden, um deren Inhalte in Ihr Speicherkonto zu importieren.

### Vorbereiten Ihrer Laufwerke

Bevor Sie einen Importauftrag erstellen, bereiten Sie Ihre Laufwerke mit dem [WAImportExport-Tool](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) vor. Weitere Informationen zur Verwendung des WAImportExport-Tools finden Sie unter [WAImportExport Tool Reference](http://go.microsoft.com/fwlink/?LinkId=329032) (WAImportExport-Tool-Referenz, in englischer Sprache).

Führen Sie die folgenden drei Schritte durch, um Ihre Laufwerke vorzubereiten:

1.  Bestimmen Sie die zu importierenden Daten und die Anzahl der benötigten Laufwerke.
2.  Geben Sie die Ziel-Blobs für Ihre Daten im Azure Blob-Dienst an.
3.  Kopieren Sie Ihre Daten mithilfe des WAImportExport-Tools auf eine oder mehrere Festplatten.

Das WAImportExport-Tool generiert bei der Vorbereitung eine *Laufwerkprotokoll*datei für jedes Laufwerk. Die Laufwerkprotokolldatei wird auf Ihrem lokalen Computer gespeichert, nicht auf dem Laufwerk selbst. Beim Erstellen des Importauftrags laden Sie die Protokolldatei hoch. Eine Laufwerkprotokolldatei enthält die Laufwerk-ID und die BitLocker-Schlüssel sowie weitere Informationen über das Laufwerk.

### Importauftrag erstellen

1.  Sobald Sie Ihr Laufwerk vorbereitet haben, navigieren Sie zum Speicherkonto im Verwaltungsportal, und zeigen Sie das Dashboard an. Klicken Sie unter **Schnelleinsicht** auf **Create an Import Job**.

2.  Geben Sie im ersten Schritt des Assistenten an, dass Sie Ihr Laufwerk vorbereitet haben und dass Sie die Laufwerkprotokolldatei zur Hand haben.

3.  Geben Sie im zweiten Schritt die Kontaktinformationen des Ansprechpartners für diesen Importauftrag an. Wenn Sie ausführliche Protokolldaten für den Importauftrag speichern möchten, wählen Sie die Option **Save the verbose log in my 'waimportexport' blob container**.

4.  Laden Sie im dritten Schritt die Protokolldateien hoch, die Sie während der Vorbereitung des Laufwerks erhalten haben. Sie müssen pro vorbereitetem Laufwerk eine Datei hochladen.

    ![Importauftrag erstellen – Schritt 3](./media/storage-import-export-service/import-job-03.png)

5.  Geben Sie im vierten Schritt einen beschreibenden Namen für den Importauftrag ein. Beachten Sie, dass der eingegebene Name nur Kleinbuchstaben, Ziffern, Trennstriche und Unterstriche enthalten darf, mit einem Buchstaben beginnen muss und keine Leerzeichen enthalten darf. Mithilfe des ausgewählten Namens können Sie Ihre Aufträge während und nach der Bearbeitung nachverfolgen.

    Die Datacenter-Region gibt das Datacenter an, an das Sie Ihr Paket schicken müssen. Weitere Informationen finden Sie unten in den FAQ.

    Wenn Sie bereits Ihre FedEx-Tracking-Nummer haben, wählen Sie **I have my tracking number and want to enter it now**, und fahren Sie mit dem nächsten Schritt fort. Wenn Sie noch keine Tracking-Nummer haben, wählen Sie **I will provide my shipping information for this import job once I have shipped my package**, und schließen Sie den Importprozess ab.

6.  Wenn Sie bereits eine Tracking-Nummer haben, geben Sie diese im fünften Schritt ein, und bestätigen Sie sie.

Erstellen eines Exportauftrags im Verwaltungsportal
---------------------------------------------------

Erstellen Sie einen Exportauftrag, um den Import-/Export-Dienst darüber zu informieren, dass Sie ein oder mehrere leere Laufwerke an das Datacenter schicken. So können die Daten von Ihrem Speicherkonto auf die Laufwerke exportiert werden, und die Laufwerke werden Ihnen dann zugeschickt.

1.  Navigieren Sie zum Speicherkonto im Verwaltungsportal, und zeigen Sie das Dashboard an, um einen Exportauftrag zu erstellen. Klicken Sie unter **Schnelleinsicht** auf **Create an Export Job**, und fahren Sie mit dem Assistenten fort.

2.  Geben Sie im zweiten Schritt die Kontaktinformationen des Ansprechpartners für diesen Exportauftrag an. Wenn Sie ausführliche Protokolldaten für den Exportauftrag speichern möchten, wählen Sie die Option **Save the verbose log in my 'waimportexport' blob container**.

3.  Legen Sie im dritten Schritt fest, welche Blob-Daten Sie von Ihrem Speicherkonto auf Ihr leeres Laufwerk oder Ihre Laufwerke exportieren möchten. Sie können alle Blob-Daten des Speicherkontos exportieren, oder Sie legen fest, welche Blobs oder Blob-Sätze exportiert werden sollen.

    ![Exportauftrag erstellen – Schritt 3](./media/storage-import-export-service/export-job-03.png)

    -   Verwenden Sie die Auswahl **Equal To**, und geben Sie den relativen Pfad zu dem Blob an, beginnend mit dem Containernamen, um den zu exportierenden Blob festzulegen. Verwenden Sie *$root*, um den Stammcontainer festzulegen.
    -   Verwenden Sie die Auswahl **Starts With**, und legen Sie das Präfix beginnend mit einem Schrägstrich "/" fest, um alle Blobs festzulegen, die mit einem Präfix beginnen. Bei dem Präfix kann es sich um das Präfix des Containernamens, den vollständigen Containernamen oder den vollständigen Containernamen gefolgt vom Präfix des Blob-Namens handeln.

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
				<td>Exportiert alle Blobs im Container <strong>music,</strong> die mit dem Präfix <strong>love</strong> beginnen</td>
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


4.  Geben Sie im vierten Schritt einen beschreibenden Namen für den Exportauftrag ein. Der eingegebene Name darf nur Kleinbuchstaben, Ziffern, Trennstriche und Unterstriche enthalten, muss mit einem Buchstaben beginnen und darf keine Leerzeichen enthalten.  

    Die Datacenter-Region gibt das Datacenter an, an das Sie Ihr Paket schicken müssen. Weitere Informationen finden Sie unten in den FAQ.

    Wenn Sie bereits Ihre FedEx-Tracking-Nummer haben, wählen Sie **I have my tracking number and want to enter it now**, und fahren Sie mit dem nächsten Schritt fort. Wenn Sie noch keine Tracking-Nummer haben, wählen Sie **I will provide my shipping information for this export job once I have shipped my package**, und schließen Sie den Importprozess ab.

5.  Geben Sie im fünften Schritt Ihre Tracking-Nummer ein, und bestätigen Sie sie.


Auftragsstatus im Verwaltungsportal verfolgen
---------------------------------------------

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
			<td>Wird versendet</td>
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

BitLocker-Schlüssel für einen Exportauftrag anzeigen
----------------------------------------------------

Bei Exportaufträgen können Sie die BitLocker-Schlüssel, die vom Dienst für Ihr Laufwerk erstellt wurden, anzeigen und kopieren, damit Sie Ihre exportierten Daten entschlüsseln können, sobald Sie die Laufwerke vom Azure-Datacenter erhalten. Navigieren Sie im Verwaltungsportal zu Ihrem Speicherkonto, und klicken Sie auf die Registerkarte **Import/Export**. Wählen Sie Ihren Exportauftrag in der Liste aus, und klicken Sie auf die Schaltfläche **View Keys**. Die BitLocker-Schlüssel werden wie abgebildet angezeigt:

![BitLocker-Schlüssel für einen Exportauftrag anzeigen](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Häufig gestellte Fragen
-----------------------

### Allgemein

**Was kostet der Import-/Export-Dienst?**

-   Weitere Informationen hierzu finden Sie unter [Import/Export Preisdetails](http://go.microsoft.com/fwlink/?LinkId=329033).

**Wie lange dauert der Import oder Export meiner Daten?**

-   Man muss die Zeit für den Versand der Festplatten berücksichtigen sowie pro TB an Daten mehrere Stunden zum Kopieren der Daten.

**Welche Schnittstellentypen werden unterstützt?**

-   Der Import-/Export-Dienst unterstützt 3,5-Zoll-SATA II-Festplatten (HDDs). Sie können die folgenden Konverter verwenden, um Daten von USB-Geräten vor dem Versand auf SATA zu übertragen:
    -   Anker 68UPSATAA-02BU
    -   Anker 68UPSHHDS-BU
    -   Startech SATADOCK22UE

**Was soll ich tun, wenn ich mehr als zehn Laufwerke importieren oder exportieren möchte?**

-   Bei der Vorschauversion des Import-/Export-Diensts können maximal zehn Laufwerke in einem Import- oder Exportauftrag verarbeitet werden. Wenn Sie mehr als zehn Laufwerke verschicken möchten, können Sie mehrere Aufträge erstellen.

**Was geschieht, wenn ich aus Versehen eine HDD verschicke, die den unterstützten Anforderungen nicht entspricht?**

-   Das Azure-Datacenter schickt das Laufwerk, das den unterstützten Anforderungen nicht entspricht, an Sie zurück. Wenn nur einige der Laufwerke in dem Paket die Anforderungen erfüllen, werden diese Laufwerke verarbeitet, und die Laufwerke, die die Anforderungen nicht erfüllen, werden an Sie zurückgeschickt.

### Verwaltung von Import-/Exportaufträgen

**Was geschieht mit meinen Import-/Exportaufträgen, wenn ich mein Azure-Speicherkonto lösche?**

-   Wenn Sie Ihr Speicherkonto löschen, werden damit auch all Ihre Azure Import-/Exportaufträge gelöscht.

**Kann ich meinen Auftrag stornieren?**

-   Sie können einen Auftrag stornieren, solange dieser den Status "Wird erstellt" oder "Wird versendet" hat.

**Wie lange kann ich den Status abgeschlossener Aufträge im Verwaltungsportal anzeigen?**

-   Den Status abgeschlossener Aufträge können Sie bis zu 90 Tage lang anzeigen. Nach 90 Tagen werden alle abgeschlossenen Aufträge archiviert. Wenn Sie den Status abgeschlossener Aufträge nach 90 Tagen abrufen müssen, können Sie sich an den Kundensupport wenden.

### Versand

**Welche Kurierdienste können verwendet werden?**

-   Bei dem Vorschauprodukt wird nur Federal Express (FedEx) unterstützt.
-   Pakete für einen Importauftrag können entweder mit FedEx Express oder FedEx Ground verschickt werden.
-   Alle Pakete werden über FedEx Ground zurückgeschickt.

    **Wichtig**

    Sie müssen Ihre Tracking-Nummer an den Azure Import-/Export-Dienst übertragen, ansonsten kann Ihr Auftrag nicht verarbeitet werden.

**Entstehen Kosten für den Rückversand?**

-   Der Rückversand ist beim Vorschauprodukt kostenlos.

**Von wo aus kann ich meine Daten verschicken bzw. wohin kann ich sie schicken lassen?**

-   Der Import-/Export-Dienst akzeptiert nur Lieferungen von **Standorten** in den USA, und Rücksendungen an Adressen in den USA. Der Dienst unterstützt das Importieren von Daten in und das Exportieren von Daten aus Speicherkonten in den folgenden Regionen:

    -   Osten USA
    -   Westen USA
    -   USA Nord Mitte
    -   USA Süd Mitte
    -   Nordeuropa
    -   Westeuropa
    -   Asien (Osten)
    -   Asien (Südosten)
-   Wenn sich Ihr Speicherkonto in einem Datacenter in den USA befindet, müssen Sie möglicherweise Ihre Laufwerke an ein Datacenter in einer anderen Region senden, da momentan nicht alle Datacenter den Import-/Export-Dienst unterstützen. Möglicherweise fallen Kosten für Ausgangsverkehr für Sie an, wenn Ihr Auftrag nicht in der Region verarbeitet wird, in der sich Ihr Speicherkonto befindet.

-   Wenn sich Ihr Speicherkonto in einem Datacenter in Europa oder Asien befindet, müssen Sie Ihr Laufwerk in eine der unterstützten Regionen in den USA schicken und der Versand muss aus den USA erfolgen. Der Import-/Export-Dienst kopiert dann die Daten von Ihrem oder auf Ihr Speicherkonto in Europa oder Asien.

    -   Bei einem Importauftrag fallen für das Kopieren keine Gebühren für den Eingangsverkehr an.
    -   Bei einem Exportauftrag entstehen Kosten für das Kopieren von Daten zwischen Azure-Datacentern. Wenn sich Ihr Speicherkonto beispielsweise in Westeuropa befindet und Sie Ihr Laufwerk zu einem Datacenter im Osten der USA schicken, fallen Kosten für den Ausgangsverkehr an, und zwar für das Verschieben der Daten von Westeuropa in den Osten der USA, wo sie exportiert werden.

    **Wichtig**

    Azure-Datacenter können keine Laufwerke annehmen, die von Standorten außerhalb der USA verschickt werden. Die Annahme solcher Lieferungen wird abgelehnt.

**Kann ich bei Microsoft Laufwerke für Import-/Exportaufträge kaufen?**

-   Nein, Sie müssen sowohl für den Import als auch den Export Ihre eigenen Laufwerke einsenden.

### Sicherheit

**Ist BitLocker zwingend erforderlich?**

-   Ja. Alle Laufwerke müssen mit einem BitLocker-Schlüssel verschlüsselt sein.

**Formatieren Sie die Laufwerke, bevor Sie sie zurückschicken?**

-   Nein, alle Laufwerke müssen mit BitLocker vorbereitet sein.

