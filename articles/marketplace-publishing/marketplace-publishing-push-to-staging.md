<properties
   pageTitle="Vorbereiten und Testen Ihres Angebots für die Bereitstellung im Azure Marketplace | Microsoft Azure"
   description="Detaillierte Informationen zum Bereitstellen von Marketinginhalten, Konfigurieren von Preisplänen und Testen des Angebots vor der Bereitstellung im Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="11/30/2015"
   ms.author="hascipio"/>

# Abschließen der Angebotserstellung mit Marketinginhalten
In diesem Schritt des Veröffentlichungsprozesses müssen Sie bestimmte Marketinginhalte sowie Details zu Ihrem Angebot und/oder SKUs im Azure Marketplace bereitstellen. So stellen Sie beispielsweise eine Beschreibung Ihres Produkts, Firmenlogos, Preispläne, detaillierte Informationen zu Plänen sowie weitere Informationen bereit, die zum Überführen Ihres Angebots und/oder Ihrer SKU in der Stagingumgebung erforderlich sind. Diese Informationen werden im Azure-Portal als Marketinginhalte verwendet. Sie starten diesen Prozess im [Veröffentlichungsportal][link-pubportal].

## Schritt 1: Bereitstellen von Marketinginhalten im Marketplace
**Englisch ist die Standardsprache und die einzige unterstützte Sprache.** Geben Sie daher bitte alle Informationen in den Feldern auf Englisch ein. Alle Informationen können vor der Überführung in die Stagingumgebung jederzeit bearbeitet werden.

  1. Wechseln Sie zum Veröffentlichungsportal: [https://publish.windowsazure.com](https://publish.windowsazure.com).
  2. Klicken Sie im linken Menü auf die Registerkarte **Marketing**.
  3. Klicken Sie im Hauptbereich auf die Schaltfläche **English (US)**.

### Details
1. Geben Sie die Angebotsübersicht, die ausführliche Übersicht und eine Beschreibung Ihres Angebots ein.
2.	Laden Sie Abbildungen gemäß den erforderlichen Spezifikationen (wie im Veröffentlichungsportal erläutert) im PNG-Format hoch. Laden Sie für jede gewünschte Größe ein Bild hoch.

  ![Abbildung](media/marketplace-publishing-push-to-staging/pubportal-02.png)

  *Titel, Beschreibung und Logos für das Angebot*

### Links
Geben Sie auf der Registerkarte **Links** auf der linken Seite Links mit Informationen an, die Kunden weiterhelfen könnten. Geben Sie für jeden Link einen Namen und eine URL an.

![Abbildung](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### Beispielbilder (optional)
> [AZURE.NOTE]Das Hinzufügen von Beispielbildern ist ein optionaler Schritt. Sie können auch nur die Marketinginhalte angeben, um die Voraussetzungen für die Überführung in die Stagingumgebung zu erfüllen.

Laden Sie auf der Registerkarte **Beispielbilder** im Menü auf der linken Seite ein neues Bild hoch, indem Sie auf **Neues Bild hochladen** klicken. Wenn Sie ein vorhandenes Bild austauschen möchten, klicken Sie auf **Bild ersetzen**.

![Abbildung](media/marketplace-publishing-push-to-staging/pubportal-03.png)

### Pläne
![Abbildung](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### Rechtliche Hinweise
Geben Sie auf der Registerkarte **Rechtliche Hinweise** einen Link zu Ihren Richtlinien/Nutzungsbedingungen an. Geben Sie die Bedingungen im großen Feld **Nutzungsbedingungen** ein, oder kopieren Sie sie in das Feld.

> [AZURE.IMPORTANT]Alle Felder müssen ausgefüllt sein, einschließlich der Abbildungen, um das Angebot in die Stagingumgebung zu überführen.


## Schritt 2: Festlegen der Preise
### Preismodelle
|Preismodell |Beschreibung |
|---------------|------------------------------------------|
|Basis| Monatliche Pauschale, Bezahlung zum Zeitpunkt des Kaufs, z. B. 10 USD/Monat.|
|Verbrauch (auch als Nutzung oder Verwendung bezeichnet) | Nutzungsbasierte Bezahlung, die vom Herausgeber definiert wird. Eine Überschreitung kann nicht pro Arbeitsplatz, pro Benutzer usw. definiert werden, da es kein Konzept eines Bruchteils von Benutzern oder Kapazitäten gibt, um die Nutzung anteilig zu verrechnen. Die Nutzung wird vom Partner auf Stundenbasis berechnet. Der Kunde zahlt die Gebühren monatlich im Nachhinein, im Gegensatz zur Vorauszahlung bei Monatsplänen. |
|Kostenlose Testversion | Der Kunde kann das Produkt oder den Dienst eine Zeit lang kostenlos testen und zahlt danach die normalen Gebühren. |
|Free-Tarif | Dieser Plan ist immer kostenlos. |
| Migration (Umwandlung oder Upgrade/Downgrade eines Plans) | Hierbei wechselt ein Benutzer aus dem aktuellen Plan in einen anderen akzeptablen Plan; wird vom Partner definiert. |

**Verfügbare Preismodelle nach Angebotstyp**

> [AZURE.IMPORTANT]Die Verfügbarkeit bestimmten Preismodelle variiert je nach Art Angebotstyp. Siehe hierzu die nachstehende Tabelle.

| | Nur Basis | Nur Verbrauch | Basis und Verbrauch |
|---|---|---|---|
| VM-Image | Nein | Ja | Nein|
| Entwicklerdienst | Ja | Ja | Ja |
| Datendienst | Ja | Nein | Nein |

### 2\.1. Festlegen der VM-Preise
> [AZURE.NOTE]BYOL (Bring Your Own License, Verwendung eigener Lizenzen) wird nur für virtuelle Computer unterstützt.

1.	Auf der Registerkarte **Preise** sind alle unterstützten Märkte aufgeführt. Wählen Sie den entsprechenden Markt aus, um zu den Feldern für die Preisgestaltung zu gelangen.
2.	Der bereitgestellte Link im Veröffentlichungsportal enthält Preisinformationen, mit denen Sie die Preise für Ihre eigene(n) SKU(s) festlegen können.
3.	Wenn Ihre SKU BYOL unterstützt, aktivieren Sie das Kontrollkästchen für die Verfügbarkeit extern per BYOL lizenzierter SKUs.
4.	Wenn Ihre SKU auf Stundenbasis abgerechnet wird, geben Sie die Preise für Ihre Software ein. SKUs ohne Preis stehen nicht zum Kauf oder zur Verwendung zur Verfügung.

  >[AZURE.NOTE]Wenn Sie sowohl über BYOL-SKUs als auch über auf Stundenbasis abgerechnete SKUs verfügen, stellen Sie sicher, dass beide Optionen angegeben sind.

5.	Es wird ein Assistent zur Preisgestaltung geöffnet, mit dem Sie Ihre Preise festlegen können. Hier können Sie auch Preise für andere Länder angeben, falls Sie Käufe in anderen Märkten als dem von Ihnen angegebenen Markt zulassen möchten.
6.	Einige Länder sind ISV-Abführungsländer. Um in einem ISV-Abführungsland Produkte verkaufen zu können, müssen Sie sowohl Steuern für Ihre SKUs berechnen und einziehen als auch Steuern abrechnen und an die Behörden in diesem Land zahlen können. Microsoft kann keine rechtliche oder steuerliche Beratung anbieten. Weitere Informationen finden Sie im Abschnitt zu Käuferländern des Angebots in der Einführung zu diesem Dokument.

### 2\.2. Festlegen der Preise für Entwicklerdienste
Pläne können aus einer beliebigen Kombination aus Basis- und Verbrauchskonzepten bestehen. „Basis“ ist eine Monatspauschale, „Überschreitung“ ein nutzungsbasierter Preis. (Weitere Informationen siehe unten.)

**Beispiel**: Angebot eines Contoso-Entwicklerdiensts

| Plan | Preis | Umfang | Migrationspfad |
|-------|------|-------|-------|
|Free|0 USD/Monat|Grundlegende Funktionalität.|Migration zu jedem anderen Plan möglich|
|Bronze|10 USD/Monat|Grundlegende Funktionalität und ein Kontingent von 1.000 Nutzungseinheiten für Feature X.|Migration zu den Plänen Bronze Plus, Silber und Gold möglich|
|Bronze Plus|Kostenloser Testzeitraum: 0 USD/Monat + 0 USD/meter01 |Grundlegende Funktionalität und ein Kontingent von 10.000 Nutzungseinheiten für Feature X. Sobald das Kontingent für Feature X verbraucht ist, kann der Kunde über "meter01" zur nutzungsbasierten Zahlung wechseln.|Migration zu den Plänen Silber Plus und Gold möglich|
|Bronze Plus| Zahlungszeitraum (kostenloser Testzeitraum ist abgelaufen): 10 USD/Monat + 0,05 USD/meter01.|Grundlegende Funktionalität und ein Kontingent von 10.000 Nutzungseinheiten für Feature X. Sobald das Kontingent für Feature X verbraucht ist, kann der Kunde über "meter01" zur nutzungsbasierten Zahlung wechseln.|Migration zu den Plänen Silber Plus und Gold möglich|
|Silber|0,15 USD/meter01|Der Kunde kann nutzungsbasiert per „meter01“ bezahlen, dies gilt für Feature X.|Migration zu den Plänen Bronze und Gold möglich|
|Silber Plus|20 USD/Monat + 0,15 USD/meter01 + 0,01 USD/meter02|Grundlegende Funktionalität und ein Kontingent von 10.000 Nutzungseinheiten für Feature X sowie 100 Nutzungseinheiten für Feature Y. Sobald das Kontingent für Feature Y verbraucht ist, kann der Kunde basierend auf „meter01“ nutzungsbasiert bezahlen. Sobald das Kontingent für Feature Y verbraucht ist, dann der Kunde basierend auf „meter02“ nutzungsbasiert bezahlen.|Migration zu den Plänen Bronze Plus und Gold möglich|
|Gold|1\.000 USD/Monat|Kontingent von 10.000 Nutzungseinheiten für Feature X, 1.000 Nutzungseinheiten für Feature Y und unbegrenzte Nutzung von Feature Z.|Migration zu allen Plänen außer zum kostenlosen Tarif möglich|

## Schritt 3: Angeben von Supportinformationen
Einige dieser Informationen haben Sie bereits im Schritt für die Zertifizierung eingegeben. Mit den nachfolgenden Schritten können Sie Informationen hinzufügen oder bearbeiten. Die Kontaktinformationen werden nur für die interne Kommunikation zwischen dem Partner und Microsoft verwendet. Für die Endkunden steht eine Support-URL zur Verfügung.

1.	Klicken Sie auf die Überschrift **Support** auf der linken Seite des Veröffentlichungsportals.
2.	Füllen Sie die Felder für **Engineering-Kontaktdaten** aus.
3.	Füllen Sie die Felder für **Kundendienst** aus. Wenn Sie nur E-Mail-Support anbieten, geben Sie eine Pseudotelefonnummer ein. Stattdessen wird dann Ihre bereitgestellte E-Mail-Adresse verwendet.
4.	Geben Sie die Support-URL ein.

## Schritt 4: Auswählen von Azure Marketplace-Kategorien
Auf der Registerkarte **Kategorien** stehen verschiedene Möglichkeiten zur Auswahl. Wenn Ihr Angebot sich hier einordnen lässt, können Sie bis zu fünf Kategorien auswählen.

## Verwendung Ihrer Marketinginhalte
Im Folgenden finden Sie eine ausführliche Übersicht darüber, wie die Marketinginhalte des Angebots auf der [Azure Marketplace-Website](http://azure.microsoft.com/marketplace) und im [Azure-Vorschauportal](https://ms.portal.azure.com) verwendet werden.

### Azure Marketplace-Website
![Abbildung](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![Abbildung](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Auflisten der Angebote auf der Azure Marketplace-Website*

![Abbildung](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Beschreibung der Angebote auf der Azure Marketplace-Website*

![Abbildung](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

* Preisübersicht zu den Angeboten auf der Azure Marketplace-Website*

### Azure-Vorschauportal
![Abbildung](media/marketplace-publishing-push-to-staging/portal-catalog-01.png)

*Auflisten der Angebote im Azure-Vorschauportal*

![Abbildung](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

*Beschreibung der Angebote im Azure-Vorschauportal*

## Nächste Schritte
Nachdem nun Ihre Marketplace-Inhalte geladen wurden, geht es weiter mit dem Testen des Angebots in der Stagingumgebung. Da die Schritte jedoch je nach Angebotstyp unterschiedlich sind, müssen Sie den entsprechenden Angebotstyp in der Liste unten auswählen.

||VM-Image | Entwicklerdienst | Datendienst | Lösungsvorlage |
|----|----|----|----|----|
| **Schritt 3. Überführen des Angebots in die Stagingumgebung** | [Testen Ihres VM-Angebots in der Stagingumgebung](marketplace-publishing-vm-image-test-in-staging.md) | Testen Ihres Entwicklungsdiensts in der Stagingumgebung | Testen Ihres Datendiensts in der Stagingumgebung | [Testen Ihrer Lösungsvorlage in der Stagingumgebung](marketplace-publishing-solution-template-test-in-staging.md) |

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots im Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_1203_2015-->