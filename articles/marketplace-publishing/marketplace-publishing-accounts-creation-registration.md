<properties
   pageTitle="Erstellen und Registrieren eines Kontos für die Veröffentlichung | Microsoft Azure"
   description="Anweisungen zum Erstellen eines Microsoft-Verkäuferkontos, mit dem – nach Genehmigung des Kontos – verschiedene Arten von Angeboten im Azure Marketplace veröffentlicht werden können."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/17/2015"
   ms.author="hascipio"/>

# Erstellen eines Microsoft-Verkäuferkontos
In diesem Artikel werden die Schritte zur Erstellung und Registrierung eines Kontos erläutert, die notwendig sind, um anerkannter Microsoft-Verkäufer im Azure Marketplace zu werden.

## 1\. Erstellen eines Microsoft-Kontos
> [AZURE.WARNING]Um den Veröffentlichungsprozess abzuschließen, müssen Sie ein Microsoft-Konto erstellen. Über dieses Konto können Sie sich sowohl beim Veröffentlichungsportal als auch im Verkäuferdashboard registrieren und anmelden. Sie sollten für Ihre Azure Marketplace-Angebote nur ein Microsoft-Konto verwenden. Verwenden Sie keine unterschiedlichen Konten für verschiedene Dienste oder Angebote.

Die Adresse, die für den Benutzernamen maßgeblich ist, sollte sich in Ihrer Domäne befinden und von Ihrem IT-Team gesteuert werden (z. B. publishing@example.com)). Informationen zu Bezahlung, Steuern und Berichterstellung werden an dieses Konto gesendet.

  >[AZURE.WARNING]Wörter wie „Azure“ und „Microsoft“ werden bei der Registrierung eines Microsoft-Kontos nicht unterstützt. Vermeiden Sie diese Wörter, um den Prozess der Kontoerstellung und -registrierung abzuschließen.

1. Erstellen Sie eine Verteilerliste (Distribution List, DL) oder Sicherheitsgruppe (Security Group, SG) in der Domäne Ihres Unternehmens.
  - Fügen Sie Ihr Onboardingteam zur Verteilerliste hinzu.
  - Die Verteilerliste muss live geschaltet sein, um eine Bestätigungs-E-Mail zu empfangen.
  - Verwenden Sie marketplace@example.com als E-Mail-Adresse für die Verteilerliste.
  - Diese Einrichtung muss in Ihren internen Systemen erfolgen.
2. Öffnen Sie eine neue Chrome-Inkognito- oder Internet Explorer-InPrivate-Browsersitzung, um sicherzustellen, dass Sie nicht bei einem vorhandenen Konto angemeldet sind.
3. Registrieren Sie sich mithilfe der E-Mail-Adresse der Verteilerliste für ein Microsoft-Konto.
 - Sie können sich unter [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx) für ein Microsoft-Konto registrieren.
 - Verwenden Sie marketplace@example.com als E-Mail-Adresse.
 - Die ID Ihres Microsoft-Kontos ist jetzt marketplace@example.com.

    <!--[drawing][img-msalive]-->

4. Geben Sie bei der Registrierung eine gültige Telefonnummer an. Falls notwendig, erhalten Sie einen Prüfcode per SMS oder einen automatischen Telefonanruf zur Überprüfung Ihrer Identität.
5. Verifizieren Sie die an die Verteilerliste gesendete E-Mail-Adresse.

    <!--[drawing][img-email]-->

6. Nun können Sie das neue Microsoft-Konto im Verkäuferdashboard verwenden.

> [AZURE.IMPORTANT]Durch Verwendung einer Verteilerliste können mehrere Personen E-Mail-Benachrichtigungen mit wichtigen Zahlungsinformationen empfangen. Gleichzeitig ist sichergestellt, dass das Eigentum am Microsoft-Konto übertragen werden kann und nicht an eine Einzelperson geknüpft ist.

## 2\. Erstellen Ihres Kontos im Verkäuferdashboard
Das Microsoft-Verkäuferdashboard wird zur einmaligen Registrierung der Unternehmensinformationen verwendet. Die Person, die das Konto registriert, muss ein offizieller Vertreter des Unternehmens sein und ihre persönlichen Informationen zum Zweck der Identitätsüberprüfung bereitstellen. Diese Person muss ein Microsoft-Konto verwenden, das für das Unternehmen freigegeben ist. Das gleiche Konto muss im Azure-Veröffentlichungsportal verwendet werden. Sie sollten überprüfen, ob Ihr Unternehmen nicht bereits über ein Konto im Verkäuferdashboard verfügt, bevor Sie ein Konto erstellen. Während des Prozesses erfassen wir Bankdaten, Steuerinformationen und Adressdaten Ihres Unternehmens. Diese Informationen erhalten Sie üblicherweise über Bank- oder Geschäftskontakte.

> [AZURE.IMPORTANT]Sie müssen folgende Komponenten des Verkäuferprofils ausfüllen, um die verschiedenen Phasen der Angebotserstellung und -bereitstellung durchlaufen zu können.


| Verkäuferprofil | Entwurf starten | Staging | Kostenlos veröffentlichen und Lösungsvorlage | Für den Erwerb veröffentlichen |
|----|----|----|----|----|
|Unternehmensregistrierung | Verpflichtend | Verpflichtend | Verpflichtend | Verpflichtend |
|Steuer-ID | Optional | Optional | Optional | Verpflichtend |
|Bankkonto | Optional | Optional | Optional | Verpflichtend |


> [AZURE.NOTE]Bring Your Own License (BYOL) wird nur für virtuelle Computer unterstützt und gilt als ein **kostenloses** Angebot.


### Registrieren Ihres Unternehmenskontos
1. Öffnen Sie eine neue Internet Explorer-InPrivate- oder Chrome-Inkognito-Browsersitzung, um sicherzustellen, dass Sie nicht bei einem persönlichen Konto angemeldet sind.

2. Wechseln Sie zu [http://sellerdashboard.microsoft.com](http://sellerdashboard.microsoft.com).

    ![Abbildung][img-sd-url]

3. Melden Sie sich mit dem Microsoft-Konto an, das Sie für Ihr Unternehmen registriert haben (z. B. marketplace@example.com).

    ![Abbildung][img-signin]

4. Führen Sie den Assistenten "Unterstützen Sie uns beim Schutz Ihres Kontos" vollständig durch. Damit wird Ihre Identität anhand einer Telefonnummer oder E-Mail-Adresse überprüft.

    ![Abbildung][img-verify]

5. Wechseln Sie zu **Kontodetails**. Hier geben Sie Ihre persönlichen Daten ein, die nur zur Überprüfung Ihrer Identität herangezogen werden. Geben Sie Ihren Namen, Ihre E-Mail-Adresse, Ihren Wohnsitz und Ihre telefonische Durchwahl ein. Diese Daten werden nicht an Dritte außerhalb von Microsoft weitergegeben.

    ![Abbildung][img-sd-top]

    <!--[drawing][img-sd-info]-->

6. Registrieren Sie sich im Auftrag Ihres Unternehmens, indem Sie den Kontotyp auf **Unternehmen** festlegen, nicht auf **Einzelperson**. Klicken Sie auf **Weiter**.

    ![Abbildung][img-sd-type]

7. Geben Sie die Details zu Ihrem Unternehmen ein. Diese Informationen – z. B. Hauptniederlassung und Referenzperson – müssen richtig angegeben werden, da sie von einem Microsoft-Team geprüft werden.

    ![Abbildung][img-sd-mktg1]

8. Der Firmenname wird im Veröffentlichungsportal verwendet, und sollte deshalb richtig sein.

    <!--[drawing][img-sd-mktg2]-->

9. Verwenden Sie die Anschrift der Hauptniederlassung Ihres Unternehmens.

    ![Abbildung][img-sd-addr]

10. Geben Sie eine Referenzperson an, die erreichbar und als Vertreter des Unternehmens erkennbar ist.

    ![Abbildung][img-sd-legal]

11. Klicken Sie auf **Zur Genehmigung senden**. Fertig!

    ![Abbildung][img-sd-submit]


Wenn Sie planen, nur kostenlose Angebote zu veröffentlichen, können Sie mit Abschnitt 3, „Registrieren Ihres Kontos im Veröffentlichungsportal“, fortfahren.

Wenn Sie auch Angebote für den Erwerb veröffentlichen möchten, müssen Sie Ihre vollständigen Steuerinformationen und Bankdaten im Konto für das Verkäuferdashboard angeben.

> [AZURE.IMPORTANT]Ohne vollständige Angabe der Steuer- und Bankdaten können Sie Ihre Angebote in der Stagingumgebung nicht ordnungsgemäß testen, und Sie können Ihre Angebote nicht in die Produktionsumgebung überführen.

Wenn Sie Ihre Steuer- und Bankdaten später aktualisieren möchten, können Sie mit Abschnitt 3, „Registrieren Ihres Kontos im Veröffentlichungsportal“, fortfahren und später über die Links im Veröffentlichungsportal zurückkehren.

### Hinzufügen von Steuer- und Bankinformationen
 Wenn Sie Angebote für den Erwerb veröffentlichen möchten, müssen Sie auch Auszahlungs- und Steuerinformationen hinzufügen und zur Validierung im Verkäuferdashboard absenden. Wenn Sie nur kostenlose Angebote (oder BYOL-Angebote) veröffentlichen möchten, müssen Sie diese Informationen nicht hinzufügen. Sie können dieses Informationen später eingeben, es dauert jedoch einige Zeit, bis die Steuerinformationen validiert werden können. Wenn Sie wissen, dass Sie Angebote für den Erwerb veröffentlichen werden, sollten Sie diese Informationen so schnell wie möglich bereitstellen.

1. Melden Sie sich mit Ihrem Microsoft-Konto beim [Verkäuferdashboard](http://sellerdashboard.microsoft.com) an.

2. Klicken Sie auf die Registerkarte **KONTO**, und klicken Sie auf **Auszahlung und Steuer**.

3. Klicken Sie auf **AUSZAHLUNGS- UND STEUERINFORMATIONEN HINZUFÜGEN**.

4. Klicken Sie auf der Seite **Zahlungsmethode auswählen** unter **Neue Zahlungsmethode** auf **Bankkonto** oder **PayPal**.

> [AZURE.IMPORTANT]Wenn Sie über kommerzielle Angebote verfügen, die Kunden im Marketplace erwerben, wird dieses Konto für die entsprechenden Auszahlungen an Sie verwendet.

5. Geben Sie die Informationen zu einem Bankkonto oder einem PayPal-Konto ein.

6. Klicken Sie auf **WEITER**.

7. Wählen Sie auf der Seite **Steuerinformationen** das Land oder die Region aus, in dem bzw. der sich Ihr ständiger Wohnsitz befindet. Wählen Sie anschließend das Land oder die Region aus, dessen bzw. deren erste Staatsbürgerschaft Sie besitzen. Klicken Sie auf **WEITER**.

8. Geben Sie Ihre Steuerdaten ein, und klicken Sie auf **WEITER**.

9. Klicken Sie auf **Senden**. Wenn Sie noch nicht bereit sind, Ihre Steuerinformationen zur Überprüfung zu übermitteln, können Sie auf **Speichern** oder **Speichern und Beenden** klicken. Es dauert einige Zeit, bis die Steuerinformationen validiert werden können, daher empfiehlt es sich, die Daten so schnell wie möglich zu übermitteln.

> [AZURE.WARNING]Ohne vollständige Angabe der Steuer- und Bankdaten in Ihrem Verkäuferdashboard können Sie Ihre kommerziellen Angebote nicht in die Produktionsumgebung überführen.

## 3\. Registrieren Ihres Kontos im Veröffentlichungsportal
Das Azure-Veröffentlichungsportal dient zum Veröffentlichen und Verwalten Ihrer Angebote. Das Portal bietet einige nützliche Informationen, die Sie durch den Prozess der Angebotserstellung führen.

> [AZURE.WARNING]Sie müssen hier das gleiche Microsoft-Konto verwenden, das zur Registrierung beim Verkäuferdashboard verwendet wurde. Nachdem das Masterkonto für die Veröffentlichung erstellt wurde, können diesem Konto weitere Benutzer hinzugefügt werden.

1.	Öffnen Sie eine neue Chrome-Inkognito- oder Internet Explorer-InPrivate-Browsersitzung, um sicherzustellen, dass Sie nicht bei einem persönlichen Konto angemeldet sind.
2.	Wechseln Sie zu [http://publish.windowsazure.com](http://publish.windowsazure.com).
3.	 Melden Sie sich mit dem Microsoft-Konto an, das Sie für Ihr Unternehmen registriert haben (also marketplace@example.com), und fügen Sie nach Bedarf Co-Admins hinzu.
4.	Lesen und akzeptieren Sie die Nutzungsbedingungen der Herausgebervereinbarung (bei der ersten Anmeldung beim Veröffentlichungsportal). Mehr ist nicht zu tun.

  >[AZURE.TIP]Die Teilnahmerichtlinien werden auf der [Azure-Website](http://azure.microsoft.com/support/legal/marketplace/participation-policies/) beschrieben.

  > Wenn Sie Probleme beim Abschließen der Verkäuferregistrierung haben, öffnen Sie wie nachfolgend beschrieben ein Supportticket: 1. Wenden Sie sich an den [Support](http://go.microsoft.com/fwlink?LinkId=272975). 2. Wählen Sie **Verkäuferdashboard-Registrierung und Ihr Konto** aus. 3. Wählen Sie **Für ein Entwicklerkonto registrieren** aus. 4. Wählen Sie die Methode für die Kontaktaufnahme aus.

### Verkäuferländer

> [AZURE.WARNING]Um Ihre Dienste im Azure Marketplace verkaufen zu können, muss die registrierte Organisation in einem der genehmigten Verkäuferländer ansässig sein. Diese Einschränkung hat auszahlungstechnische und steuerliche Gründe. Die Länderliste wird in Kürze erweitert, prüfen Sie sie also regelmäßig. Die vollständige Liste finden Sie in Abschnitt 1b der [Teilnahmerichtlinien für den Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).




## Nächste Schritte
Nachdem Ihr Konto erstellt und registriert wurde, klicken Sie auf den Artefakttyp (virtueller Computer, Entwicklerdienst, Datendienst oder Lösungsvorlage), den Sie im Azure Marketplace veröffentlichen möchten. Informationen zum Veröffentlichen des jeweiligen Angebots finden Sie in den folgenden Artikeln:

|| VM-Image | Entwicklerdienst | Datendienst | Lösungsvorlage | |----|-----|-----|-----|-----| |**Schritt 2: Erstellen Ihres Angebots** | [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md) | [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md) | [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md) | [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md) | || [Technische Voraussetzungen für VM-Images ][link-single-vm-prereq]| Technische Voraussetzungen für Entwicklerdienste | [Technische Voraussetzungen für Datendienste](marketplace-publishing-data-service-creation-prerequisites.md) | [Technische Voraussetzungen für Lösungsvorlagen](marketplace-publishing-solution-template-creation-prerequisites.md) | || [Veröffentlichungsleitfaden für VM-Image][link-single-vm] | Veröffentlichungsleitfaden für Entwicklerdienste | [Veröffentlichungsleitfaden für Datendienste](marketplace-publishing-data-service-creation.md) | [Veröffentlichungsleitfaden für Lösungsvorlagen](marketplace-publishing-solution-template-creation.md) | || [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] | [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] | [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] | [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] |

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots im Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/de-DE/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md

<!---HONumber=AcomDC_1203_2015-->