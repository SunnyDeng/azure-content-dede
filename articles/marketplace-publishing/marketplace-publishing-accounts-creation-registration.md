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
   ms.date="12/06/2015"
   ms.author="hascipio"/>

# Erstellen eines Microsoft-Verkäuferkontos
In diesem Artikel werden die Schritte zur Erstellung und Registrierung eines Kontos erläutert, die notwendig sind, um anerkannter Microsoft-Verkäufer im Azure Marketplace zu werden.

## 1\. Erstellen eines Microsoft-Kontos
> [AZURE.WARNING]Zum Starten des Veröffentlichungsprozesses müssen Sie ein Microsoft-Konto erstellen. Dieses Konto wird verwendet, um sich für das **Microsoft Developer Center** und das **Azure-Veröffentlichungsportal** zu registrieren und die Anmeldung dafür durchzuführen. Sie sollten für Ihre Azure Marketplace-Angebote nur ein Microsoft-Konto verwenden. Verwenden Sie keine unterschiedlichen Konten für verschiedene Dienste oder Angebote.

Die Adresse, die für den Benutzernamen maßgeblich ist, sollte sich in Ihrer Domäne befinden und von Ihrem IT-Team gesteuert werden (z. B. publishing@example.com). Informationen zu Bezahlung, Steuern und Berichterstellung werden an dieses Konto gesendet.

  >[AZURE.WARNING]Wörter wie „Azure“ und „Microsoft“ werden bei der Registrierung eines Microsoft-Kontos nicht unterstützt. Vermeiden Sie diese Wörter, um den Prozess der Kontoerstellung und -registrierung abzuschließen.

### Anleitung

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
4. Geben Sie bei der Registrierung eine gültige Telefonnummer an. Falls notwendig, erhalten Sie einen Prüfcode per SMS oder einen automatischen Telefonanruf zur Überprüfung Ihrer Identität.
5. Verifizieren Sie die an die Verteilerliste gesendete E-Mail-Adresse.
6. Sie können das neue Microsoft-Konto jetzt im Microsoft Developer Center verwenden.

> [AZURE.TIP]Durch Verwendung einer Verteilerliste können mehrere Personen E-Mail-Benachrichtigungen mit wichtigen Zahlungsinformationen empfangen. Gleichzeitig ist sichergestellt, dass das Eigentum am Microsoft-Konto übertragen werden kann und nicht an eine Einzelperson geknüpft ist.

## 2\. Erstellen des Microsoft Developer Center-Kontos
Das Microsoft Developer Center wird für die einmalige Registrierung der Unternehmensinformationen verwendet. Die Person, die das Konto registriert, muss ein offizieller Vertreter des Unternehmens sein und ihre persönlichen Informationen zum Zweck der Identitätsüberprüfung bereitstellen. Diese Person muss ein Microsoft-Konto verwenden, das für das Unternehmen freigegeben ist. **Das gleiche Konto muss im Azure-Veröffentlichungsportal verwendet werden.** Sie sollten überprüfen, ob Ihr Unternehmen nicht bereits über ein Microsoft Developer Center-Konto verfügt, bevor Sie ein Konto erstellen. Während des Prozesses erfassen wir Informationen zu Unternehmensadresse, Bankdaten und Steuer. Diese Informationen erhalten Sie üblicherweise über Bank- oder Geschäftskontakte.

> [AZURE.IMPORTANT]Sie müssen folgende Komponenten des Verkäuferprofils ausfüllen, um die verschiedenen Phasen der Angebotserstellung und -bereitstellung durchlaufen zu können.


| Verkäuferprofil | Entwurf starten | Staging | Kostenlos veröffentlichen und Lösungsvorlage | Für den Erwerb veröffentlichen |
|----|----|----|----|----|
|Unternehmensregistrierung | Verpflichtend | Verpflichtend | Verpflichtend | Verpflichtend |
|Steuer-ID | Optional | Optional | Optional | Verpflichtend |
|Bankkonto | Optional | Optional | Optional | Verpflichtend |


> [AZURE.NOTE]Bring Your Own License (BYOL) wird nur für virtuelle Computer unterstützt und gilt als ein **kostenloses** Angebot.


### Registrieren Ihres Unternehmenskontos
1. Öffnen Sie eine neue Internet Explorer-InPrivate- oder Chrome-Inkognito-Browsersitzung, um sicherzustellen, dass Sie nicht bei einem persönlichen Konto angemeldet sind.

2. Wechseln Sie zu [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure).

3. Melden Sie sich mit dem Microsoft-Konto an, das Sie für Ihr Unternehmen registriert haben (z. B. marketplace@example.com).

    ![Abbildung][img-signin]

4. Führen Sie den Assistenten "Unterstützen Sie uns beim Schutz Ihres Kontos" vollständig durch. Damit wird Ihre Identität anhand einer Telefonnummer oder E-Mail-Adresse überprüft.

    ![Abbildung][img-verify]

5. Wählen Sie im Abschnitt „Registrierungskontoinformationen“ im Dropdownmenü **Land/Region des Kontos** die für Sie zutreffende Option aus.

    > [AZURE.WARNING]**Verkäuferländer:** Um Ihre Dienste im Azure Marketplace verkaufen zu können, muss die registrierte Organisation in einem der obigen genehmigten Verkäuferländer ansässig sein. Diese Einschränkung hat auszahlungstechnische und steuerliche Gründe. Die Länderliste wird in Kürze erweitert, prüfen Sie sie also regelmäßig. Weitere Informationen finden Sie in den [Marketplace-Teilnahmerichtlinien](http://go.microsoft.com/fwlink/?LinkID=526833).

6. Wählen Sie Ihren „Kontotyp“ oder die Option **Einzelperson** oder **Unternehmen** aus.

    <!-- Insert screenshot -->

    > [AZURE.IMPORTANT]Sehen Sie sich die Seite [Kontotypen, Standorte und Gebühren](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx) an, um mehr Informationen zu Kontotypen zu erhalten und die bestmögliche Wahl treffen zu können.

7. Geben Sie den **Anzeigenamen des Herausgebers** ein. Dies ist normalerweise der Name Ihres Unternehmens.

    > [AZURE.TIP]Derzeit wird im Azure-Veröffentlichungsportal kein Anzeigename des Herausgebers verwendet. Sie müssen dieses Feld aber ausfüllen, um den Registrierungsprozess abzuschließen.

8. Geben Sie die Kontaktinformationen für das Konto ein.

    > [AZURE.IMPORTANT]Es ist erforderlich, dass Sie genaue Kontaktinformationen angeben. Sie werden in unserem Überprüfungsprozess für Ihr Unternehmen und seine Genehmigung im Developer Center verwendet.

9. Für ein Unternehmenskonto müssen Sie auch Kontaktinformationen für den **Genehmiger des Unternehmens** angeben. Dies ist die Person, die bestätigen kann, dass Sie zum Erstellen des Kontos im Namen Ihres Unternehmens berechtigt sind. Klicken Sie nach Abschluss dieses Vorgangs auf **Weiter**, um mit dem Abschnitt **Zahlung** fortzufahren.

10. Geben Sie Ihre Zahlungsinformationen für Ihr Konto ein. Wenn Sie einen Aktionscode haben, der die Kosten der Registrierung abdeckt, können Sie ihn hier eingeben. Geben Sie andernfalls Ihre Kreditkartendaten an (oder PayPal-Daten, wenn dies für Ihren Markt unterstützt wird). Klicken Sie nach Abschluss dieses Vorgangs auf **Weiter**, um mit dem Bildschirm **Überprüfen** fortzufahren.

11. Überprüfen Sie Ihre Kontoinformationen, und bestätigen Sie, dass alles korrekt ist. Lesen und akzeptieren Sie dann die Nutzungsbedingungen des **Microsoft Azure Marketplace-Herausgebervertrags**. Aktivieren Sie das Kontrollkästchen, um anzugeben, dass Sie die Bedingungen gelesen und akzeptiert haben.

12. Klicken Sie auf **Fertig stellen**, um die Registrierung zu bestätigen. Wir senden Ihnen eine Bestätigungsnachricht an Ihre Entwickler-E-Mail-Adresse.

13. Wenn Sie planen, nur kostenlose Angebote zu veröffentlichen, klicken Sie auf **Zum Azure Marketplace-Veröffentlichungsportal wechseln**. Sie können außerdem Abschnitt 3 dieses Dokuments überspringen ([Registrieren Ihres Kontos im Veröffentlichungsportal](#3-register-your-account-in-the-publishing-portal)).

14. Wenn Sie die Veröffentlichung von kommerziellen Angeboten planen, klicken Sie auf **Kontoinformationen aktualisieren**. Unter dieser Option müssen Sie die Steuer- und Bankdaten für Ihr Developer Center-Konto angeben.

> [AZURE.IMPORTANT]Ohne vollständige Angabe der Steuer- und Bankdaten können Sie Ihre Angebote in der Stagingumgebung nicht ordnungsgemäß testen, und Sie können Ihre Angebote nicht in die Produktionsumgebung überführen.

Wenn Sie Ihre Steuer- und Bankdaten später aktualisieren möchten, können Sie mit Abschnitt 3, [Registrieren Ihres Kontos im Veröffentlichungsportal](#3-register-your-account-in-the-publishing-portal), fortfahren und später über die Links im Azure-Veröffentlichungsportal zurückkehren.

### Hinzufügen von Steuer- und Bankinformationen
 Wenn Sie Angebote für den Erwerb veröffentlichen möchten, müssen Sie auch Auszahlungs- und Steuerinformationen hinzufügen und zur Validierung im Developer Center absenden. Wenn Sie nur kostenlose Angebote (oder BYOL-Angebote) veröffentlichen möchten, müssen Sie diese Informationen nicht hinzufügen. Sie können dieses Informationen später eingeben, es dauert jedoch einige Zeit, bis die Steuerinformationen validiert werden können. Wenn Sie wissen, dass Sie Angebote für den Erwerb veröffentlichen werden, sollten Sie diese Informationen so schnell wie möglich bereitstellen.

**Bankinformationen**

1. Melden Sie mit Ihrem Microsoft-Konto am [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) an.

2. Klicken Sie im Menü auf der linken Seite auf **Auszahlungskonto**, und klicken Sie unter **Zahlungsmethode wählen** auf **Bankkonto** oder **PayPal**.

    > [AZURE.IMPORTANT]Wenn Sie über kommerzielle Angebote verfügen, die Kunden im Marketplace erwerben, wird dieses Konto für die entsprechenden Auszahlungen an Sie verwendet.

3. Geben Sie die Zahlungsinformationen ein, und klicken Sie auf **Speichern**, wenn Sie zufrieden sind.

    > [AZURE.IMPORTANT]Wenn Sie das Auszahlungskonto aktualisieren oder ändern müssen, können Sie die obigen Schritte verwenden und die aktuellen Informationen durch neue Informationen ersetzen. Wenn Sie Ihr Zahlungskonto ändern, kann dies dazu führen, dass sich Ihre Zahlungen um maximal einen Zahlungszyklus verzögern. Diese Verzögerung tritt auf, weil wir die Kontoänderung überprüfen müssen. Dies ist der gleiche Vorgang wie bei der ersten Einrichtung des Zahlungskontos. Nachdem das Konto bestätigt wurde, erhalten Sie trotzdem den vollen Betrag. Zahlungen, die für den aktuellen Zahlungszyklus fällig sind, werden im nächsten Zyklus hinzugefügt.

4. Klicken Sie auf **Weiter**.

**Steuerinformationen**

1. Melden Sie mit Ihrem Microsoft-Konto am [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) an (falls erforderlich).

2. Klicken Sie im Menü auf der linken Seite auf **Steuerprofil**.

3. Wählen Sie auf der Seite **Richten Sie Ihr Steuerformular ein** das Land oder die Region aus, in dem bzw. der sich Ihr ständiger Wohnsitz befindet. Wählen Sie anschließend das Land oder die Region aus, dessen bzw. deren erste Staatsbürgerschaft Sie besitzen. Klicken Sie auf **Weiter**.

4. Geben Sie Ihre Steuerdaten ein, und klicken Sie auf **Weiter**.

> [AZURE.WARNING]Ohne vollständige Angabe der Steuer- und Bankinformationen in Ihrem Microsoft Developer Center-Konto können Sie Ihre kommerziellen Angebote nicht in die Produktionsumgebung überführen.

## 3\. Registrieren Ihres Kontos im Veröffentlichungsportal
Das Azure-Veröffentlichungsportal dient zum Veröffentlichen und Verwalten Ihrer Angebote. Das Portal bietet einige nützliche Informationen, die Sie durch den Prozess der Angebotserstellung führen.

> [AZURE.WARNING]Sie müssen hier das gleiche Microsoft-Konto verwenden, das zur Registrierung beim Microsoft Developer Center verwendet wurde. Nachdem das Masterkonto für die Veröffentlichung erstellt wurde, können diesem Konto weitere Benutzer hinzugefügt werden.

1.	Öffnen Sie eine neue Chrome-Inkognito- oder Internet Explorer-InPrivate-Browsersitzung, um sicherzustellen, dass Sie nicht bei einem persönlichen Konto angemeldet sind.

2.	Wechseln Sie zu [http://publish.windowsazure.com](http://publish.windowsazure.com).

3.	Melden Sie sich mit dem Microsoft-Konto an, das Sie für Ihr Unternehmen registriert haben (also marketplace@example.com), und fügen Sie nach Bedarf Co-Admins hinzu.

  >[AZURE.TIP]Die Teilnahmerichtlinien werden auf der [Azure-Website](http://azure.microsoft.com/support/legal/marketplace/participation-policies/) beschrieben.

  > Wenn Sie Probleme beim Abschließen der Developer Center-Registrierung haben, öffnen Sie wie nachfolgend beschrieben ein Supportticket:
  1. Wenden Sie sich an den [Support](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=de-DE&pesid=15635&ccsid=635847950577064286).
  2. Wählen Sie **Developer Center**.
  3. Wählen Sie **Profil**.
  4. Wählen Sie die Methode für die Kontaktaufnahme aus.






## Nächste Schritte
Nachdem Ihr Konto erstellt und registriert wurde, klicken Sie auf den Artefakttyp (virtueller Computer, Entwicklerdienst, Datendienst oder Lösungsvorlage), den Sie im Azure Marketplace veröffentlichen möchten. Informationen zum Veröffentlichen des jeweiligen Angebots finden Sie in den folgenden Artikeln:

|| VM-Image | Entwicklerdienst | Datendienst | Lösungsvorlage |
|----|-----|-----|-----|-----|
|**Schritt 2: Erstellen Ihres Angebots** | [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md) | [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md) | [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md) | [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md) |
|| [Technische Voraussetzungen für VM-Images ][link-single-vm-prereq]| Technische Voraussetzungen für Entwicklerdienste | [Technische Voraussetzungen für Datendienste](marketplace-publishing-data-service-creation-prerequisites.md)  | [Technische Voraussetzungen für Lösungsvorlagen](marketplace-publishing-solution-template-creation-prerequisites.md) |
|| [Veröffentlichungsleitfaden für VM-Image][link-single-vm] | Veröffentlichungsleitfaden für Entwicklerdienste | [Veröffentlichungsleitfaden für Datendienste](marketplace-publishing-data-service-creation.md) | [Veröffentlichungsleitfaden für Lösungsvorlagen](marketplace-publishing-solution-template-creation.md) |
|| [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] | [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] | [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] | [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] |

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

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
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

<!----HONumber=AcomDC_1210_2015-->
