<properties
   pageTitle="Leitfaden für das Erstellen eines Datendiensts für den Marketplace | Microsoft Azure"
   description="Detaillierte Anweisungen zum Erstellen, Zertifizieren und Bereitstellen eines Datendiensts für den Erwerb im Azure Marketplace."
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

# Leitfaden für die Veröffentlichung von Datendiensten im Azure Marketplace
Nach Abschluss von Schritt 1 [Erstellen und Registrieren eines Kontos][link-acct-creation] haben wir Sie durch [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-prerequisites.md) und [Technische Voraussetzungen](marketplace-publishing-data-service-creation-prerequisites.md) eines Datendienstangebots im Azure Marketplace geleitet. Jetzt führen wir Sie durch die Schritte, die Sie im [Veröffentlichungsportal][link-pubportal] zum Erstellen eines Datendienstangebots für den Azure Marketplace durchlaufen.

## 1\. Melden Sie sich beim Veröffentlichungsportal an.

Gehen Sie zu [https://publish.windowsazure.com](https://publish.windowsazure.com.).

**Verwenden Sie bei der erstmaligen Anmeldung beim Veröffentlichungsportal das Konto, mit dem das Verkäuferprofil Ihres Unternehmens im Verkäuferdashboard registriert wurde.** (Sie können später im Veröffentlichungsportal einen beliebigen Mitarbeiter Ihres Unternehmens als Co-Administrator hinzufügen.) Sie müssen der Herausgebervereinbarung zustimmen, bevor Sie mit der Erstellung Ihres Angebots im Veröffentlichungsportal beginnen können.

Klicken Sie auf die Kachel zur Veröffentlichung von Data Services, wenn dies Ihre erste Anmeldung beim Veröffentlichungsportal ist.

## 2\. Wählen Sie **Data Services** im Navigationsmenü auf der linken Seite aus.

  ![Abbildung](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## 3\. Erstellen eines neuen Datendiensts

Geben Sie den Titel für Ihr neues Datendienstangebot ein, und klicken Sie rechts auf das Pluszeichen ("+").

  ![Abbildung](media/marketplace-publishing-data-service-creation/step-3.png)

## 4\. Überprüfen Sie das Untermenü unter dem neu erstellten Datendienst im Navigationsmenü.

Klicken Sie auf die Registerkarte **Exemplarische Vorgehensweise**, und informieren Sie sich über alle notwendigen Schritte zur ordnungsgemäßen Veröffentlichung des Datendiensts im Azure Marketplace.

> [AZURE.TIP]Sie können immer auf die Links auf der Seite "Exemplarische Vorgehensweise" klicken oder die Registerkarten im Untermenü des Datendienstangebots auf der linken Seite verwenden.

## 5\. Erstellen Sie einen neuen Plan.

### Angebote, Pläne, Transaktionen

Jedes Angebot kann mehrere Pläne enthalten. Die Mindestanzahl liegt bei einem (1) Plan. Wenn Endbenutzer Ihr Angebot abonnieren, bedeutet dies, dass sie einen Plan des Angebots abonnieren. Durch jeden Plan wird definiert, auf welche Weise Endbenutzer den Dienst verwenden können.

Derzeit unterstützt Azure Marketplace nur das transaktionsbasierte monatliche Abonnementmodell für Data Services. Die Endbenutzer entrichten also eine monatliche Gebühr, die sich nach dem Tarif des abonnierten Plan richtet. Sie können daraufhin jeden Monat die Anzahl von Transaktionen nutzen, die durch den Plan vorgegeben ist.

Eine Transaktion entspricht grundsätzlich der Anzahl von Datensätzen, die Ihr Datendienst basierend auf der an den Dienst gesendeten Abfrage zurückgibt. Der Standardwert ist 100. Die Anzahl von Transaktionen, die im Rahmen jeder Abfrage zurückgegeben werden, entspricht der Anzahl der Datensätze geteilt durch 100 und gerundet auf die nächste ganze Zahl.

Die Azure Marketplace Service-Ebene ist dafür zuständig, die Anzahl der von den einzelnen Abfragen genutzten Transaktionen zu überwachen (messen).

> [AZURE.IMPORTANT]Endbenutzer, die während eines Monats die Transaktionsgrenze erreichen, werden bis zum Ende ihres monatlichen Abonnementzyklus an der Weiternutzung des Diensts gehindert.

> Der Plan oder einer der Pläne kann eine unbegrenzte Anzahl von Transaktionen enthalten (dies muss jedoch nicht der Fall sein).

### Erstellen Sie einen Plan.
1. Klicken Sie auf Pluszeichen **"+"** neben "Neuen Plan hinzufügen".

2. Wählen Sie unter den Optionen **Unbegrenzt** oder **Eingeschränkt** eine Nutzungsoption für den Plan aus. Bei "Eingeschränkt" geben Sie die Anzahl der Transaktionen an, die der Plan während eines Monats zulässt.

    ![Abbildung](media/marketplace-publishing-data-service-creation/step-5.1.png)

    Das Veröffentlichungsportal schlägt auch "Planbezeichner" vor, die verwendet werden, um den Namen des Plans in der UI gegenüber den Endbenutzern zu kommunizieren. Außerdem verwendet der Marketplace Service die Bezeichner zur Identifizierung des Plans. Sie können den "Planbezeichner" nach Ihren Wünschen ändern.

    > [AZURE.NOTE]Der "Planbezeichner" muss im Rahmen jedes Angebots eindeutig sein. Wie viele andere im Veröffentlichungsportal verwendeten Bezeichner werden Planbezeichner nach der ersten Veröffentlichung in der produktiven Umgebung gesperrt. Daraufhin kann der Bezeichner nicht mehr geändert werden.

3. Klicken Sie, um Ihre Auswahl zu bestätigen.

4. Anschließend müssen Sie einige zusätzliche Fragen zum neu erstellten Plan beantworten.

    ![Abbildung](media/marketplace-publishing-data-service-creation/step-5.2.png)


|Frage|Bedeutung|
|----|----|
|**Ist dieser Plan kostenlos und weltweit verfügbar?**|Sie können einen völlig kostenfreien Plan erstellen. Wenn dies der einzige Plan dieses Angebots ist, bedeutet dies, dass Sie ein "Kostenloses Angebot" im Marketplace veröffentlichen. Wenn das Angebot nur für einen Plan (von mehreren Plänen) gilt, können Sie Endbenutzern anbieten, unter Nutzung einer relativ kleinen Anzahl von Transaktionen pro Monat mehr über Ihren Dienst zu erfahren. Wenn die Antwort "Ja" ist, werden keine weiteren Fragen gestellt.|

> [AZURE.NOTE]Endbenutzer können immer auf kostenpflichtige Pläne upgraden.

|Frage|Bedeutung|
|----|----|
|**Gibt es eine kostenlose Testversion?**|Sie können zwischen "Keine Testversion" oder der Nutzungsoption "Ein Monat" für Ihren Plan wählen. Herausgeber bevorzugen diese Option, um Endbenutzern die Möglichkeit zu geben, sich einen Monat lang kostenlos über die Vorteile des Angebots zu informieren.|

> [AZURE.IMPORTANT]Endbenutzer können nur eine kostenlose Testversion erwerben, wenn sie ein Zahlungsmittel, z. B. eine Kreditkarte oder einen Enterprise Agreement, eingerichtet haben.

> Nach Ablauf der 1-monatigen kostenlosen Testversion beginnt Azure Marketplace mit der Berechnung von Gebühren in Höhe des bei Abschluss des Abonnements geltenden Tarifs, sofern der Kunde nicht die Stornierung des Abonnements eingeleitet hat. Der Endbenutzer erhält darüber hinaus keine gesonderte Benachrichtigung.

|Frage|Bedeutung|
|----|----|
|**Muss für diesen Plan ein Promocode erworben werden?**| Herausgeber haben die Möglichkeit, den Zugriff auf ihre Servicepläne zu beschränken, indem sie bestimmten Kunden einen speziellen Code (den Promocode) zur Verfügung stellen. Nur Endbenutzer, die über diesen Promocode verfügen, können den Plan abonnieren. Wenn Sie "Nein" auswählen, stimmen Sie zu, dass jeder Endbenutzer aus der Region, in der das Angebot verfügbar ist (weitere Details siehe den [Leitfaden zu Azure Marketplace-Marketinginhalten](marketplace-publishing-push-to-staging.md)), diesen Plan abonnieren kann. Es werden keine weiteren Fragen gestellt.|
|**Soll dieser Plan zudem für Kunden ohne gültigen Promocode ausgeblendet werden?**|Wenn die Antwort auf die vorherige Frage "Ja" lautete, hat der Herausgeber die Möglichkeit, diesen Plan vollständig aus der Marketplace-Benutzeroberfläche zu entfernen. Dies bedeutet, dass der Plan den Kunden auf der Detailseite des Angebots nicht angezeigt wird. Endbenutzer, die einen Promocode zum Erwerb des Plans erhalten, können den Plan mithilfe dieses Codes abonnieren.|

## 6\. Erstellen von Marketinginhalten im Marketplace
Informationen zur Bereitstellung der auf den Registerkarten **Marketing, Preise, Support und Kategorien** erforderlichen Informationen finden Sie im [Leitfaden zu Azure Marketplace-Marketinginhalten](marketplace-publishing-push-to-staging.md), der für alle im Azure Marketplace veröffentlichten Artefakte gilt.

## 7\. Verbinden Sie Ihr Angebot mit Ihrem Dienst (SQL Azure- oder webdienstbasiert).

Klicken Sie auf das Untermenü **Data Services**.

In der oberen Hälfte der Seite werden Sie aufgefordert, den **Namespace** des Angebots anzugeben.

  ![Abbildung](media/marketplace-publishing-data-service-creation/step-7.png)

Durch die folgende Frage wird festgelegt, wie der Herausgeber das neu erstellte Angebot im Azure Marketplace verfügbar macht. (Weitere Informationen finden Sie im [Leitfaden zu technischen Voraussetzungen für Data Services](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![Abbildung](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Veröffentlichen des datenbankbasierten Diensts**

Klicken Sie auf **Datenbank**. Die folgende Seite wird angezeigt.

  ![Abbildung](media/marketplace-publishing-data-service-creation/step-7.3.png)

So erstellen Sie eine CSDL-Zuordnung für das Dataset basierend auf der SQL Azure-Datenbank

  ![Abbildung](media/marketplace-publishing-data-service-creation/step-7.4.png)

Und dann für jede Tabelle

  ![Abbildung](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![Abbildung](media/marketplace-publishing-data-service-creation/step-7.6.png)

Bei Webdiensten

  ![Abbildung](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT]Lesen Sie [Zuordnen eines vorhandenen Webdiensts zu OData über CSDL](marketplace-publishing-data-service-creation-odata-mapping.md), um ausführliche Informationen und Beispiele zum Erstellen eines CSDL-Webdiensts zu erhalten.

## Nächste Schritte
Nachdem Sie nun Ihr Datendienstangebot erstellt haben, stellen Sie sicher, dass Sie die Anweisungen im [Leitfaden zu Azure Marketplace-Marketinginhalten](marketplace-publishing-push-to-staging.md) befolgt haben, bevor Sie mit [Testen Ihres Datendiensts in der Stagingumgebung](marketplace-publishing-data-service-test-in-staging.md) fortfahren.

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots im Azure Marketplace](marketplace-publishing-getting-started.md)
- Wenn Sie sich über den gesamten OData-Zuordnungsprozess und seinen Zweck informieren möchten, lesen Sie den Artikel [OData-Zuordnung im Datendienst](marketplace-publishing-data-service-creation-odata-mapping.md), um Definitionen, Strukturen und Anweisungen kennenzulernen.
- Wenn Sie mehr über bestimmte Knoten und ihre Parameter erfahren möchten, lesen Sie den Artikel [OData-Zuordnungsknoten für Datendienste](marketplace-publishing-data-service-creation-odata-mapping-nodes.md). Dort finden Sie Definitionen und Erläuterungen, Beispiele und Kontext für Anwendungsfälle.
- Wenn Sie an Beispielen interessiert sind, lesen Sie den Artikel [Beispiele für die OData-Zuordnung im Datendienst](marketplace-publishing-data-service-creation-odata-mapping-examples.md), um Beispielcode kennenzulernen sowie Codesyntax und Kontext zu verstehen.


[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=AcomDC_1203_2015-->