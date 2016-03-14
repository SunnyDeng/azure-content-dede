<properties
   pageTitle="Nutzungs- und RateCard-APIs von Microsoft Azure ermöglichen Cloudyn die ITFM-Bereitstellung für Kunden | Microsoft Azure"
   description="Enthält eine einzigartige Sicht auf den Microsoft Azure-Abrechnungspartner Cloudyn und dessen Umgebungen mit Integration der Azure-Abrechnungs-APIs in das Produkt. Dies ist besonders für Azure- und Cloudyn-Kunden hilfreich, die daran interessiert sind, Cloudyn für Azure-Dienste zu verwenden oder auszuprobieren."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="02/19/2016"
   ms.author="mobandyo;bryanla"/>

# Nutzungs- und Gebührenkarten-APIs von Microsoft Azure ermöglichen Cloudyn die ITFM-Bereitstellung für Kunden 

Cloudyn, Microsoft Development Partner und führender Anbieter von Cloudverwaltungslösungen, wurde für eine private Vorschau der neuen Ressourcennutzungs- und Gebührenkarten-APIs von Microsoft Azure ausgewählt. Die Nutzungs-API ermöglicht den Zugriff auf die geschätzten Azure-Nutzungsdaten für ein Abonnement. Mit der RateCard-API werden vollständige Preisinformationen für alle Azure-Dienste von Kunden bereitgestellt, bei denen es sich nicht um Enterprise Agreement (EA)-Kunden handelt. Zusammen liefern diese APIs eine umfassende Informationsbasis für die Eingabe in IT Financial Management (ITFM)-Tools, wie sie beispielsweise von Cloudyn angeboten werden.

## Einführung 

Mit der so genannten „Multiplikation“ der Daten aus der Nutzungs-API mit den Daten aus der RateCard-API (usage [units] price[$unit] = Ausführliche Nutzung und Kosten) werden die präzisesten, genauesten und zuverlässigsten Abrechnungsinformationen bereitgestellt, die für Azure jemals verfügbar waren.

![Übersicht über ITFM][1]

Der Einsatz dieser APIs liefert wichtige Informationen zur Nutzung und zu den Kosten der Kunden. So kann Cloudyn Kundenkonten auf einfache und programmgesteuerte Weise analysieren und verschiedene ITFM-Aufgaben für seine Kunden durchführen.

## Integrieren von Cloudyn in die RateCard- und Nutzungs-APIs
Die RateCard-API erfordert mehrere Eingabeparameter – z. B. Regionsinformationen, Währung und Gebietsschema –, aber der wichtigste Parameter ist OfferDurableID. Hiermit wird der Typ des Azure-Angebots angegeben, für den der Kunde bezahlt (Nutzungsbasierte Bezahlung, ältere 6- und 12-monatige Tarife mit verbindlicher Zusage bzw. Vertrag, MSDN-Angebote, MPN-Angebote, Werbeangebote usw.). Die OfferDurableID finden Sie im [Azure-Nutzungs- und Abrechnungsportal](https://account.windowsazure.com/Subscriptions) unter der „Angebots-ID“ für das jeweilige Abonnement.

Bei der Registrierung für [Cloudyn für Azure](https://www.cloudyn.com/microsoft-azure/)-Diensten können Kunden ihren OfferDurableID-Code hinzufügen. Hiermit kann Cloudyn ihre relevanten Preisinformationen über die RateCard-API abrufen. Informationen zu den verschiedenen Angebotstypen finden Sie auf der Seite [Microsoft Azure-Angebotsdetails](https://azure.microsoft.com/support/legal/offer-details/).

![Übersicht über das Cloudyn-ITFM-Modul][2]

Cloudyn verwendet die Nutzungs- und RateCard-API zusätzlich zur Azure-Performance-API, um weitere Ebenen in Bezug auf Visualisierung, Analyse, Warnungen, Berichterstellung, Kostenmanagement und nützliche Empfehlungen zu schaffen. So kann für Azure-Kunden ein zuverlässiges Enterprise Cloud-ITFM-Tool bereitgestellt werden.

## Cloudyn-ITFM-Anwendungsfälle per Integration von Nutzungs- und RateCard-API 
Zu den häufigen Cloudyn-ITFM-Anwendungsfällen, die mit Nutzungs- und RateCard-APIs ermöglicht werden, gehören:

+ **Kostenanalyse:** Cloudkosten können in beliebige systemeigene Identifizierungsdimensionen (Anbieter, Dienst, Konto, Region usw.) unterteilt werden. Mit den Azure-Nutzungs- und -RateCard-APIs ist dies eine einfache Aufgabe, indem die präziseste Unterteilung von Nutzungs- und Kostendaten pro Konto realisiert wird. Anschließend werden die Daten von Cloudyn gruppiert und gefiltert und dem Benutzer dann in grafischer oder tabellarischer Form präsentiert.

![Kreisdiagramm zur Kostenanalyse][3]

+ **Kostenzuweisung 360:** Ermöglicht Finanz- und IT-Managern Zugang zur tatsächlichen Kostenaufschlüsselung und den Einflussfaktoren und Trends ihrer Cloudbereitstellung. Außerdem können Manager Bereitstellungsausgaben leicht Unternehmenseinheiten, Abteilungen, Regionen usw. zuordnen, völlig neue Einblicke in Cloudkosten erlangen und Chargebacks und Showbacks für das Unternehmen realisieren. Die Azure-Nutzungs- und -RateCard-APIs dienen als Eingabe in das Kostenzuordnungsmodul von Cloudyn. Hiermit werden die APIs vervollständigt, indem Methoden und Geschäftslogik zum Zuordnen von nicht markierten oder nicht markierbaren Ressourcen definiert werden.

![Diagramm „Kostenzuteilung 360“][4]

+ **Kostengünstige Größenanpassung:** Hierbei werden Empfehlungen zur richtigen Größenanpassung für virtuelle Computer mit zu geringer Auslastung bereitgestellt, um die Ausgaben der Kunden für zu groß ausgelegte oder in zu großer Zahl bereitgestellte Computer zu reduzieren. Hierfür werden die CPU- und RAM-Metriken (mit der Performance-API), die Laufzeitstunden (mit der Nutzungs-API) und die Kosten (mit der RateCard-API) von virtuellen Computern untersucht. Anschließend stellt Cloudyn Empfehlungen zur richtigen Größenanpassung basierend auf CPU- oder RAM-Ressourcen mit zu geringer Auslastung bereit (Performance) und berechnet das geschätzte Einsparpotenzial, indem der Preisunterschied (RateCard) für die virtuellen Computer mit der tatsächlichen Auslastung (Nutzung) des nicht ausgelasteten Computers multipliziert wird. 

![Kostengünstige Größenanpassung][5]

+ **Empfehlungen zur Cloudportierung:** Liefert finanzielle Hinweise zur Cloudportierung. Die aktuellen Kosten für die Cloudressourcen von Benutzern, die bei großen Cloudanbietern bereitgestellt werden, werden untersucht und mit den Kosten einer gleichwertigen Bereitstellung unter Azure verglichen. Anschließend werden pro Ressource präzise und auf Finanzdaten basierende Portierungsempfehlungen für Azure bereitgestellt. Nachdem die erforderliche gleichwertige Bereitstellung für Azure bewertet wurde (anhand der Leistungsmetriken und Benutzerpräferenzen), nutzt Cloudyn die RateCard-API, um die Kosten für die gleichwertige Bereitstellung unter Azure zu ermitteln.

+ **Leistungsberichte:** Diese Berichte können mit der Performance-API von Azure erstellt werden und verfügen über viele Features – von der CPU- und RAM-Nutzung bis hin zu Optimierungsempfehlungen. Unten ist ein Beispiel für einen Bericht zur Instanznutzung angegeben, in dem die durchschnittliche CPU-Nutzung nach Instanzen unterteilt ist.

![Leistungsberichte][6]

+ **Kategorie-Manager:** Dies ist ein leistungsstarkes Feature von Cloudyn, mit dem Ordnung in unorganisierte Cloudressourcen gebracht werden kann. Benutzer haben hierbei die Freiheit, eigene eindeutige Kategorien (Tags) zum effektiven Messen und Erstellen von Berichten zu erstellen, und zwar unter Einhaltung der gängigen Geschäftspraktiken. Außerdem können Benutzer uneinheitliches Tagging (also Schreibfehler und andere Diskrepanzen) leicht regulieren und kategorisieren und nicht markierte Ressourcen automatisch erkennen, um eine genaue Kostenzuteilung zu ermöglichen.

![Kategorie-Manager][7]

## Video 

In diesem kurzen Video wird gezeigt, wie Azure-Kunden Cloudyn für Azure und die Azure-Abrechnungs-APIs einsetzen können, um Einblicke in ihre Azure-Verbrauchsdaten zu erhalten.
 
> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## Nächste Schritte

+ Bei einem kostenlosen Test von [Cloudyn für Azure](https://www.cloudyn.com/microsoft-azure/) können Sie ausprobieren, wie Sie mit der angepassten Berichterstellung und den Analysefunktionen für Ihre Microsoft Azure-Cloudbereitstellung Kostentransparenz schaffen können.
+ Eine Übersicht über die Azure-APIs zur Ressourcennutzung und Gebührenkarte (Resource Usage und RateCard) finden Sie unter [Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure](billing-usage-rate-card-overview.md). 
+ Ausführlichere Informationen zu beiden APIs finden Sie unter [Azure-Abrechnungs-REST-API – Referenz](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c). Sie sind Teil der APIs, die vom Azure-Ressourcen-Manager bereitgestellt werden.
+ Wenn Sie sich weiter mit Beispielcode beschäftigen möchten, sehen Sie sich die Codebeispiele für die Microsoft Azure-Abrechnungs-API unter [Azure-Codebeispiele](https://azure.microsoft.com/documentation/samples/?term=billing) an.

## Weitere Informationen
+ Weitere Informationen zu Enterprise Agreement (EA)-Angeboten für Microsoft Azure finden Sie unter [Lizenzierung von Azure für das Unternehmen](https://azure.microsoft.com/pricing/enterprise-agreement/).
+ Weitere Informationen zum Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).
+ Weitere Informationen zu den Tools, die zum Entwickeln eines Verständnisses der Cloudausgaben erforderlich sind, finden Sie im Gartner-Artikel [Market Guide for IT Financial Management (ITFM) Tools](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb) (Marktleitfaden für IT Financial Management (ITFM)-Tools).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png

<!---HONumber=AcomDC_0302_2016-->