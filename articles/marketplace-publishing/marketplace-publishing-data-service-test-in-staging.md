<properties
   pageTitle="Testen des Datendienstangebots für den Marketplace | Microsoft Azure"
   description="Grundlegendes zum Testen Ihres Datendienstangebots für den Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/24/2015"
   ms.author="hascipio; avikova" />

# Testen des Datendienstangebots im Staging
Nach Abschluss der ersten beiden Schritte zum [Erstellen Ihrer Microsoft-Verkäuferkonten im Verkäuferdashboard](marketplace-publishing-accounts-creation-registration.md) und zum [Erstellen des Datendienstangebots im Veröffentlichungsportal](marketplace-publishing-data-service-creation.md) können Sie Ihr Angebot im Azure Marketplace verfügbar machen. Dieses Thema führt Sie durch den ersten Zwischenschritt namens „Staging“.

In der Stagingumgebung wird Ihr Angebot in einem privaten "Sandkasten" bereitgestellt, in dem Sie es testen und seine Funktionalität vor der Veröffentlichung überprüfen können. Das Angebot wird in der Stagingumgebung genau wie bei einem Kunden angezeigt, der es bereitgestellt hat.

## Schritt 1: Freigeben Ihres Angebot in der Stagingumgebung
Durch das Übertragen Ihres Angebots in die Stagingumgebung können Sie es testen, bevor es für zukünftige Abonnenten verfügbar wird. Sie können sehen, wie Ihr Angebot für Abonnenten Ihrer Daten dargestellt wird und wie es funktioniert.

  ![Abbildung](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.	Melden Sie sich beim [Veröffentlichungsportal](https://publish.windowsazure.com) an.
2.	Wählen Sie im linken Navigationsbereich die Option **Datendienste** aus.
3.	Wählen Sie das Angebot aus, das Sie zum Staging übertragen möchten. Der oben dargestellte Bildschirm wird angezeigt.
4.	Klicken Sie auf die Schaltfläche **Push To Staging**.  
5.	Treten Probleme mit dem Angebot auf, die vor der Übertragung in die Stagingumgebung gelöst werden müssen, werden diese in einer Liste angezeigt. Korrigieren Sie diese Probleme durch Klicken auf die einzelnen Elemente in der Liste. Wenn Sie sämtliche Korrekturen vorgenommen haben, klicken Sie erneut auf die Schaltfläche **Push to Staging**.

Wenn mit Ihrem Angebot keine Probleme auftreten, wird das unten dargestellte Popupfenster angezeigt.

Wenn Sie nicht planen bzw. nicht berechtigt sind, Ihr Angebot im Azure-Portal zu veröffentlichen (derzeit mit beschränkter Kapazität), schließen Sie einfach das Popupfenster.

Um den Datendienst im Azure-Portal (zusätzlich zum DataMarket-Portal) zu testen, benötigen Sie eine Azure-Abonnement-ID. Diese Abonnement-ID identifiziert das Konto, das zum Testen Ihres Angebots berechtigt ist.

Schneiden Sie Ihre Abonnement-ID aus, fügen Sie sie ein, und klicken Sie auf das Häkchen, um den Vorgang fortzusetzen.

  ![Abbildung](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE]Diese Azure-Abonnement-IDs sind nur für Tests und Staging im [Azure-Verwaltungsportal](https://manage.windowsazure.com) erforderlich. Für das Testen im Azure Marketplace sind sie erforderlich.

Der nächste Bildschirm zeigt durch das unten gelb markierte Symbol „In Bearbeitung“ an, dass die Veröffentlichung durchgeführt wird. Die Übertragung in die Stagingumgebung dauert zwischen 10 und 15 Minuten. Wenn es länger dauert, aktualisieren Sie zunächst Ihren Browser (drücken Sie in Internet Explorer F5). Wenn Ihr Angebot in seltenen Fällen nach einer Stunde immer noch in die Stagingumgebung übertragen wird, klicken Sie auf den Kontaktlink, um uns über das Problem zu informieren.

  ![Abbildung](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Nach Abschluss der Übertragung in die Stagingumgebung wird die Animation des Symbols „In Bearbeitung“ beendet, und der Status wird in „Bereitgestellt“ geändert. Sie können das Angebot jetzt testen.

## Schritt 2. Testen des bereitgestellten Angebots im DataMarket

Klicken Sie auf den Link hinter dem Text **See Your service offer at ...**, um den Bildschirm anzuzeigen, der Abonnenten angezeigt wird, wenn Ihr Angebot in die Produktionsumgebung wechselt und im DataMarket verfügbar ist.

  ![Abbildung](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Überprüfen Sie jedes der 12 oben gekennzeichneten Elemente, um sicherzustellen, dass alle Logos, Preise/Transaktionen, Texte, Bilder, Dokumentationen und Links richtig sind und ordnungsgemäß funktionieren. Dies ist auch ein günstiger Zeitpunkt, um zu überprüfen, ob alle Testwerte, die Sie beim Erstellen des Angebots eingegeben haben, durch tatsächliche Werte ersetzt wurden.

1. Angebotslogo
2. Angebotsname
3. Herausgebername/Link auf die Website Ihres Unternehmens
4. Suchkategorien für Ihr Angebot
5. Der Supportlink Ihres Angebots zur Unterstützung von Abonnenten
6. Kontextbezogene Beschreibung Ihres Angebots
7. Angebotsplan mit Abrechnungsinformationen
8. Link zu Implementierungscode
9. Beispielbilder zur Veranschaulichung der Verwendung der Angebotsdaten
10. Eingabe-/Ausgabemetadaten für jeden Dienst innerhalb des Angebots
11. Nutzungsbedingungen für das Angebot
12. Vorschau der Angebotsdaten


Überprüfen Sie abschließend, ob der Dienst über den DataMarket funktioniert, indem Sie auf den Link „EXPLORE THIS DATASET“ klicken. Im Tool wird das Fenster „Dienst-Explorer“ geöffnet, in dem Sie eine Vorschau der Ergebnisse einer Abfrage an Ihren Dienst anzeigen können. Sie können in diesem Fenster die erforderlichen Parameter eingeben. Die Ergebnisse einer Abfrage an Ihren Dienst werden daraufhin angezeigt. Außerdem wird die URL für die Abfrage angezeigt.

> [AZURE.NOTE]Überprüfen Sie unbedingt die oben angezeigte Beschreibung des Diensts. Wenn Ihr Angebot mehrere Dienste umfasst, klicken Sie auf die Registerkarten im unteren Bereich, um zwischen den Diensten zu wechseln und diese zu überprüfen und zu testen.



## Nächster Schritt
Wenn Probleme auftreten und Sie Hilfe benötigen, wenden Sie sich an den [Azure-Support für Herausgeber](http://go.microsoft.com/fwlink/?LinkId=272975).

Wenn Sie mit den Ergebnissen zufrieden und bereit für die Veröffentlichung Ihres Angebots sind, lesen Sie das Thema [Anfordern der Genehmigung für das Übertragen in die Produktion](marketplace-publishing-push-to-production.md).

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots im Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_1203_2015-->