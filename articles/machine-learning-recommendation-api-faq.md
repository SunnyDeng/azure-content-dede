<properties title="FAQ for setting up and using the Machine Learning Recommendations API" pageTitle="Häufig gestellte Fragen zur Einrichtung und Verwendung der Machine Learning Recommendations API | Azure" description="FAQ zur mit Azure Machine Learning erstellten Microsoft RECOMMENDATIONS API" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="jaymathe" /> 

# Häufig gestellte Fragen zur Einrichtung und Verwendung der Machine Learning RECOMMENDATIONS-API
Version 1.0

Inhaltsverzeichnis

* Was ist RECOMMENDATIONS?	
* Was kann ich mit RECOMMENDATIONS tun?	
* Gibt es Einschränkungen während der Startphase?	
* Einkauf und Abrechnung	
	* Wie hoch sind die Kosten für RECOMMENDATIONS während der Startphase?	
	* Sind alle Kosten im Zusammenhang mit RECOMMENDATIONS nachverfolgen und / oder Speichern von Benutzeraktivitäten für mich?	
	* Gibt es eine kostenlose Testversion von RECOMMENDATIONS ?	
	* Wann ist die Startphase beendet?	
	* Wie viel kostet es, wenn es allgemein verfügbar (GA) wird?	
	* Wann erhalte ich eine Rechnung für RECOMMENDATIONS?	
	* Wie aktualisiere ich auf einen Dienst höherer Ebene?	
	* Wann läuft mein RECOMMENDATIONS-Abonnement ab?	
	* Wie kündige ich mein RECOMMENDATIONS-Abonnement?	
* Erste Schritte mit RECOMMENDATIONS	
	* Lohnt sich RECOMMENDATIONS für mich?	
	* Was sind die Voraussetzungen für das Einrichtung von RECOMMENDATIONS ?	
	* Wie richte ich RECOMMENDATIONS zum ersten Mal ein?	
	* Wo finde ich die API-Dokumentation?	
	* Welche Optionen habe ich, um Katalog- und Verwendungsdaten auf RECOMMENDATIONS hochzuladen?	
* Wartung und Support	
	* Wie groß dürfen meine Datensätze sein?	
	* Wo erhalte ich technischen Support für RECOMMENDATIONS ?	
	* Wo finde ich die Nutzungsbedingungen?	
* Rechtliches	


## Was ist RECOMMENDATIONS?
Für Organisationen und Unternehmen, die für Cross-Selling und Up-Selling auf RECOMMENDATIONS für ihre Kunden vertrauen, ist RECOMMENDATIONS von Microsoft Azure Machine Learning ein Self-Service-Modul. Es ist eine Implementierung von zusammenarbeitenden Filern mittles Matrix Faktorisierung als Kernalgorithmus. Es ist zugängich für Anwendungsentwickler als  API über eine RESTful-HTTP-Schnittstelle. RECOMMENDATIONS ist derzeit öffentlich zugänglich und auf dem Weg, allgemein verfügbar zu werden.

## Was kann ich mit RECOMMENDATIONS tin?
RECOMMENDATIONS akzeptiert als Eingabe ein Element oder einen Satz von Elementen und gibt eine Liste der relevanten Empfehlungen. Beispiel: ein Kunde von einen Online-Einzelhandelsunternehmen klickt auf ein Produkt. Das Online-Einzelhandelsunternehmen sendet dieses Produkt als Eingabe für RECOMMENDATIONS, ruft eine Liste der Produkte im Gegenzug auf und entscheidet, welches dieser Produkte dem Kunden angezeigt wird. Sie wollen RECOMMENDATIONS nutzen, um Ihr Online-Geschäft zu optimieren oder um Ihre Verkaufsabteilung oder Ihr Call Center zu informieren.


## Gibt es Einschränkungen während der Startphase?

* Maximale Anzahl von Modellen pro Abonnement: 10
* Maximale Anzahl von Elementen, die ein Katalog aufnehmen können: 100,000
* Die maximale Größe der Daten, die in POST gesendet werden können (z. B. Katalogdaten importieren, Import von Verwendungsdaten) beträgt 200MB
* Die Anzahl der Transaktionen pro Sekunde für die Erstellung des Empfehlungsmodells, das nicht aktiv ist beträgt ~ 2TPS, die Erstellung eines Empfehlungsmodells, das aktiv ist um 20TPS

## Einkauf und Abrechnung 
Weitere Informationen zu den Abonnements finden Sie hier.

###Wie hoch sind die Kosten für RECOMMENDATIONS während der Startphase?
RECOMMENDATIONS basiert auf einem Abonnementdienst. Das Aufladen erfolgt basierend auf der Anzahl von Transaktionen pro Monat. Während der Startphase ist der Dienst kostenlos und ist auf 100.000 Transaktionen pro Monat beschränkt.

###Sind alle Kosten im Zusammenhang mit RECOMMENDATIONS nachverfolgen und / oder Speichern von Benutzeraktivitäten für mich?
Nicht im Moment.

###Gibt es eine kostenlose Testversion von RECOMMENDATIONS ?
Während der Startphase ist der Dienst kostenlos und ist auf 100.000 Transaktionen pro Monat beschränkt.

###Wann ist die Startphase beendet?
Die Startphase dauert mehrere Monate. Wir werden Ihre angemeldeten Benutzer über die bevorstehende Änderungen bezüglich Status und Preise benachrichtigen.

###Wie viel kostet es, wenn es allgemein verfügbar (GA) wird?
Preise für RECOMMENDATIONS, wenn es allgemein verfügbar wird, ist noch ermittelt werden. Im Fall von Preisänderungen werden Abonnenten benachrichtigt.

###Wann erhalte ich eine Rechnung für RECOMMENDATIONS?
Ein bezahltes Abonnement ist jedes Abonnement, für das eine monatliche Gebühr fällig wird. Beim Kauf eines kostenpflichtigen Abonnements wird Ihnen sofort der erste Monat in Rechnung gestellt. Sie bezahlen den Betrag entsprechend den Angaben auf dem Angebot auf der Abonnementseite (plus Steuern). Diese Gebühr ist monatlich fällig, immer zu den Tag, an dem Sie Ihr Abonnement bestellten, und zwar solange bis Sie das Abonnement kündigen. Klicken Sie hier für weitere Informationen zu den Abonnements.

###Wie aktualisiere ich auf einen Dienst höherer Ebene?
Während der Startphase ist der Dienst auf 100.000 Transaktionen pro Monat beschränkt und kann nicht in eine höhere Stufe aktualisiert werden.

Wenn RECOMMENDATIONS allgemeinen verfügbar wird und Sie feststellen, dass Ihr Abonnement Ihnen nicht genügend Transaktionen gibt, können Sie zu einem Abonnement mit einer höheren Limite wechseln.

Wenn Sie ein Abonnement aktualisieren:

* Ihre im alten Abonnement verbleibenden Transaktionen werden nicht auf Ihr neues Abonnement übertragen. 
* Sie bezahlen den Preis für das neue Abonnement, auch wenn Sie nicht verwendete Transaktionen auf Ihrem alten Abonnement haben

Vorgang zum Aktualisieren eines Abonnements

* Melden Sie sich auf dem Marketplace an.
* Klicken Sie auf die Registerkarte Meine Daten.
* Klicken Sie auf das Abonnement, das Sie aktualisieren möchten. 
* Im rechten Fensterbereich werden alle verfügbaren Abonnements aufgeführt und Ihre  Abonnements entsprechend markiert. Klicken Sie auf das Optionsfeld für die Abonnements, die Sie aktualisieren möchten.
* Zum Aktualisieren klicken Sie auf OK im Dialogfeld. Wenn Sie nicht Aktualisieren möchten, klicken Sie auf "Abbrechen".

WICHTIG: Lesen Sie den Text im Dialogfeld vor der Aktualisierung genau durch, das es Auswirkungen auf die Abrechnung und die Verwendung haben kann.

Klicken Sie [hier](http://msdn.microsoft.com/de-de/library/gg312164.aspx) für weitere Informationen zu den Abonnements.

###Wann läuft mein RECOMMENDATIONS-Abonnement ab?
Ihr Abonnement wird beendet, wenn Sie das Abonnement kündigen. Wenn Sie Ihre Abonnements kündigen möchten, finden Sie die Anweisungen dazu unten.

###Wie kündige ich mein RECOMMENDATIONS-Abonnement?
Wenn Sie Ihr Abonnement kündigen, führen Sie die Schritte unten durch. Wenn Ihr aktuelles Abonnement ein kostenpflichtiges Abonnement ist, bleibt Ihr Abonnement bis zum Ende des aktuellen Abrechnungszeitraums gültig. Wenn die Kündigung sofort wirksam sein soll, kontaktieren Sie uns [hier](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

Es erfolgt keine Erstattung, wenn Sie vor dem Ende eines Abrechnungszeitraums kündigen oder nicht verwendete Transaktionen haben.

* Melden Sie sich auf dem Marketplace an.
* Klicken Sie auf die Registerkarte Meine Daten.
* Suchen Sie das Abonnement, das Sie kündigen möchten.
* Klicken Sie auf "Abbrechen" rechts neben der Dataset-Namen und dem Status. Sie können dieses Abonnement bis an das Ende des aktuellen Abrechnungszeitraums oder  bis die Transaktionsgrenze erreicht ist, nutzen - je nachdem, was zuerst eintritt.

Wenn Sie Ihr Abonnement sofort löschen möchten, damit Sie ein neues Abonnement erwerben können, können Sie [hier](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn) ein Ticket beim Kundendienst eröffnen.


## Erste Schritte mit RECOMMENDATIONS

###Lohnt sich RECOMMENDATIONS für mich? 
Microsoft Azure Machine Learing RECOMMENDATIONS ist für Organisationen und Unternehmen, die auf Empfehlungen für Cross-Selling und Up-Selling für ihre Kunden vertrauen. Wenn Sie eine kundenorientierte Website, Verkaufspersonal, einen Vertriebsmitarbeiter oder ein Callcenter haben und wenn Sie einen Katalog mit mehr als Paar Dutzende von Produkten oder Dienstleistungen anbieten, kann Ihr Betriebsergebnis von RECOMMENDATIONS profitieren. Das Experimentieren mit RECOMMENDATIONS sollte ziemlich einfach sein. Die aktuelle Version der aktuellen API erfordert grundlegende Programmierkenntnisse. Für den Fall, dass Sie Unterstützung benötigen, wenden Sie sich an den Hersteller, der Ihre Website entwickelt haben. Wenn Sie eine interne IT-Abteilung oder einen internen Entwickler haben, sollten sie RECOMMENDATIONS erhalten, das für Sie geeignet sind. 

###Was sind die Voraussetzungen für das Einrichtung von RECOMMENDATIONS ?
RECOMMENDATIONS benötigt ein Protokoll der Benutzerauswahl in Bezug auf den Katalog. Falls Sie nicht über ein solches Protokoll verfügen aber eine Kundenwebsite haben, kann RECOMMENDATIONS möglicherweise Benutzeraktivitäten für Sie zusammentragen. RECOMMENDATIONS erfordert außerdem einen Katalog mit Produkten und Diensten. Für den Fall, dass Sie keinen Katalog haben, kann RECOMMENDATIONS die tatsächliche Nutzung der Kundendaten verwenden und einen Katalog destillieren. Ein "impliziter" Katalog berücksichtigt keine Elemente, die nicht "als Teil von Benutzertransaktionen gemeldet wurden.

###Wie richte ich RECOMMENDATIONS zum ersten Mal ein?
Nachdem Sie RECOMMENDATIONS abonniert haben, sollten Sie die API-Dokumentation   [hier](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ) verwenden, um den Dienst einzurichten.

###Wo finde ich die API-Dokumentation? 
Die API-Dokumentation finden Sie [hier](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ).

###Welche Optionen habe ich, um Katalog- und Verwendungsdaten auf RECOMMENDATIONS hochzuladen?
Zum Hochladen Ihrer Katalog- und Benutzerdaten haben Sie zwei Möglichkeiten: Entweder exportieren Sie diese Daten aus Ihrem CRM-System oder anderen Protokollen und laden Sie auf RECOMMENDATIONS hoch oder Sie fügen Ihrer Website Tags hinzu, um die Benutzeraktivitäten zu verfolgen. Im letzteren Fall werden die Daten auf Azure gespeichert.

##Wartung und Support

###Wie groß dürfen meine Datensätze sein?
Jedes Dataset können bis zu 100.000 Katalogelemente enthalten und bis zu 2048 MB mit Nutzungsdaten.
Darüber hinaus kann ein Abonnement bis zu 10 Datensätze (Modelle) enthalten.

###Wo erhalte ich technischen Support für RECOMMENDATIONS ?
Technischen Support erhalten Sie [hier](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=MachineLearning).

###Wo finde ich die Nutzungsbedingungen?

Nutzungsbedingungen finden Sie [hier](https://datamarket.azure.com/dataset/amla/recommendations#terms).

#Rechtliches
Dieses Dokument wird bereitgestellt "wie-es- ist". Informationen und Stellungnahmen, die in diesem Dokument enthalten sind, einschließlich URLs und andere Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. 
Einige der in diesem Dokument dargestellten Beispiele dienen nur zu Illustrationszwecken und sind frei erfunden. Keine Ähnlichkeit oder Verbindung ist beabsichtigt und ist rein zufällig. 
Dieses Dokument gibt keine Rechte an geistigem Eigentum an irgendeinem Microsoft-Produkt. Sie können dieses Dokument für interne Zwecke verwenden. 
(c) 2014 Microsoft. Alle Rechte vorbehalten. 


