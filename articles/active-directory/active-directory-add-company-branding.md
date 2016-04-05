<properties
	pageTitle="Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite"
	description="Erfahren Sie, wie Sie Unternehmensbranding der Anmelde- und Zugriffsbereichsseite hinzufügen."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/23/2016" 
	ms.author="MarkVi"/>

# Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite

Um Verwirrung zu vermeiden, möchten viele Unternehmen über alle verwalteten Websites und Dienste hinweg für ein einheitliches Erscheinungsbild sorgen. Dies ist mit Azure Active Directory möglich, da Sie die Anpassung der Darstellung folgender Websites mit Ihrem Firmenlogo und Ihren benutzerdefinierten Farbschemas durchführen können:

- **Anmeldeseite:** Diese Seite wird angezeigt, wenn Sie sich an Office 365 oder anderen webbasierten Anwendungen anmelden, die Azure AD als Identitätsanbieter verwenden. Sie interagieren mit dieser Seite entweder während einer Startbereichserkennung oder per Eingabe Ihrer Anmeldeinformationen. Mit der Startbereichserkennung können Partnerbenutzer an ihren lokalen Sicherheitstokendienst (z.B. AD FS) weitergeleitet werden.

- **Zugriffsbereichsseite:** Der Zugriffsbereich ist ein webbasiertes Portal, mit dem Sie die cloudbasierten Anwendungen anzeigen und starten können, für die Ihr Azure AD-Administrator Ihnen Zugriff gewährt hat. Verwenden Sie zum Zugreifen auf den Zugriffsbereich die folgende URL: [https://myapps.microsoft.com](https://myapps.microsoft.com).

In diesem Thema wird beschrieben, wie Sie die Anmeldeseite und die Zugriffsbereichsseite anpassen können.

> [AZURE.NOTE]
>
- Unternehmensbranding ist ein Feature, das nur verfügbar ist, wenn Sie Ihr Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).
- Die Azure Active Directory Premium und Basic Editions stehen für Kunden in China zur Verfügung, die mit der weltweit verfügbaren Instanz von Azure Active Directory arbeiten. Allerdings werden die Azure Active Directory-Editionen Premium und Basic derzeit durch den in China von 21Vianet betriebenen Microsoft Azure-Dienst nicht unterstützt. Wenn Sie weitere Informationen benötigen, kontaktieren Sie uns im [Azure Active Directory-Forum](https://feedback.azure.com/forums/169401-azure-active-directory/).



## Anpassen der Anmeldeseite

Normalerweise verwenden Sie die Anmeldeseite, wenn Sie browserbasierten Zugriff auf Ihre Cloud-Apps und -dienste benötigen, die von Ihrer Organisation abonniert wurden. <br> Wenn Sie Änderungen auf Ihre Anmeldeseite angewendet haben, kann es bis zu einer Stunde dauern, bis die Änderungen angezeigt werden.

Eine Anmeldeseite mit Branding wird nur angezeigt, wenn Sie einen Dienst mit einer mandantenspezifischen URL besuchen, z.B. https://outlook.com/**contoso**.com oder https://mail.**contoso**.com.

Wenn Sie einen Dienst mit mandantenspezifischen URLs besuchen (z.B. https://mail.office365.com), wird eine Anmeldeseite ohne Branding angezeigt. In diesem Fall wird Ihr Branding angezeigt, nachdem Sie die Benutzer-ID eingegeben oder eine Benutzerkachel ausgewählt haben.

> [AZURE.NOTE]
>
- Ihr Domänenname muss im Abschnitt **Active Directory** > **Verzeichnis** > **Domänen** im klassischen Azure-Portal, in dem Sie das Branding konfiguriert haben, als „Aktiv“ angezeigt werden.
- Die Anmeldeseite mit dem Branding wird nicht auf die Verbraucheranmeldeseite von Microsoft übertragen. Wenn Sie sich mit einem persönlichen Microsoft-Konto (früher Windows Live ID) anmelden, wird eine Liste mit von Azure AD gerenderten Benutzerkacheln mit Branding angezeigt, aber das Branding Ihrer Organisation wird nicht auf die Microsoft-Kontoanmeldeseite übertragen.


Wenn Sie Ihre Unternehmensmarke, -farben und andere anpassbare Elemente auf dieser Seite anzeigen möchten, schauen Sie sich die folgenden Bilder an, um den Unterschied zwischen den beiden Eindrücken zu verstehen.

Der folgende Screenshot zeigt ein Beispiel für die Office 365-Anmeldeseite auf einem Desktopcomputer *vor* einer Anpassung:

![Office 365-Anmeldeseite vor der Anpassung][1]

Nach einer Anpassung würde diese Seite wie folgt aussehen:

![Office 365-Anmeldeseite nach der Anpassung][2]

Der folgende Screenshot zeigt ein Beispiel für die Office 365-Anmeldeseite auf einem mobilen Gerät vor einer Anpassung:

![Office 365-Anmeldeseite vor der Anpassung][3]

Nach einer Anpassung würde diese Seite wie folgt aussehen:

![Office 365-Anmeldeseite nach der Anpassung][4]


Während der Größenänderung eines Browserfensters wird die große Abbildung (wie die zuvor gezeigte) fast immer so zugeschnitten, dass verschiedene Bildschirm-Seitenverhältnisse möglich sind. Vor diesem Hintergrund sollten Sie die wichtigsten visuellen Elemente in der Abbildung beibehalten, sodass sie immer in der oberen linken Ecke erscheinen (oben rechts für Rechts-nach-links-Sprachen). Dies ist wichtig, da die Größenänderung normalerweise von der rechten unteren Ecke nach oben links oder von unten nach oben erfolgt.

Die folgende Abbildung zeigt, wie die Abbildung zugeschnitten wird, wenn die Größe des Browsers nach links verschoben wird:

![][6]

Hier wird verdeutlicht, wie der Browser nach Erweiterung der Größe nach oben aussieht:

![][7]

## Welche Elemente auf der Seite können angepasst werden?

Sie können die folgenden Elemente auf der Anmeldeseite anpassen:

![][5]

 Seitenelement | Position auf der Seite
	------------- | -------------
Bannerlogo | Rechts oben auf der Seite angezeigt. Ersetzt das Logo, das normalerweise von der Zielwebsite angezeigt wird, bei der Sie sich anmelden (z.B. Office 365 oder Azure).
Große Abbildung / Hintergrundfarbe | Am linken Rand der Seite angezeigt. Ersetzt das Bild, das normalerweise von der Zielwebsite angezeigt wird, bei der Sie sich anmelden. Die Hintergrundfarbe wird möglicherweise bei Verbindungen mit geringer Bandbreite oder auf sehr kleinen Bildschirmen anstelle der großen Abbildung angezeigt.
Text der Anmeldeseite | Wird über der Fußzeile angezeigt, wenn Sie hilfreiche Informationen vermitteln möchten, bevor eine Anmeldung mit einem Geschäfts- oder Schulkonto durchgeführt wird. Ein Beispiel: Sie möchten die Telefonnummer zu Ihrem Helpdesk oder einen rechtlichen Hinweis einfügen.

> [AZURE.NOTE]
Alle Elemente sind optional. Wenn Sie z.B. ein Bannerlogo angeben, aber keine große Abbildung, werden auf der Anmeldeseite Ihr Logo und die Abbildung für den Zielstandort (d.h. das Office 365-Bild mit dem kalifornischen Highway) angezeigt.

Sie können auch alle Elemente auf dieser Seite lokalisieren. Sobald Sie einen "Standard"-Satz an Anpassungselementen konfiguriert haben, können Sie zusätzliche Versionen für verschiedene Gebietsschemas konfigurieren. Sie können auch verschiedene Elemente miteinander kombinieren. Dazu zählen z. B.:

- Erstellen Sie eine große "Standard"-Abbildung, die für alle Kulturen funktioniert, und erstellen Sie dann spezifische Versionen für Englisch und Französisch. Wenn Sie Ihre Browser auf eine dieser Sprachen festlegen, wird das jeweilige Bild angezeigt, während das Standardbild für alle anderen Sprachen angezeigt wird.
- Konfigurieren Sie verschiedene Logos für Ihre Organisation (z. B. japanische oder hebräische Versionen).



## Anpassung der Zugriffsbereichsseite

Die Zugriffsbereichsseite ist im Wesentlichen eine Portalseite für den schnellen Zugriff auf die Cloud-Apps, für die Ihnen vom Administrator der Zugriff gewährt wurde. Auf dieser Seite werden Ihre Apps als Anwendungskacheln aufgeführt, auf die Sie klicken können.


Der folgende Screenshot zeigt ein Beispiel für eine Zugriffsbereichsseite nach der Anpassung:

![][8]

## Konfigurieren Sie Ihr Verzeichnis mit Unternehmensbranding

Sie können einen Standardsatz mit anpassbaren Elementen pro Verzeichnis im klassischen Azure-Portal konfigurieren. Nachdem die Standardeinstellungen gespeichert wurden, hat ein Administrator auch die Möglichkeit, lokalisierte Versionen jedes Elements für verschiedene Sprachen bzw. Gebietsschemas hinzuzufügen. Alle anpassbaren Elemente sind optional.

Wenn Sie z.B. ein Standardbannerlogo konfigurieren, aber keine große Abbildung, wird Ihr Logo auf der Anmeldeseite in der oberen rechten Ecke und ansonsten die Standardabbildung angezeigt.

Stellen Sie sich die folgende Konfiguration vor:

- Standardbannerlogo und Anmeldeseitentext in Englisch 
- Sprachspezifischer Anmeldeseitentext für Deutsch 

Wenn Ihre bevorzugte Sprache Deutsch ist, erhalten Sie das Standardbannerlogo und den deutschen Text.

Während Sie technisch gesehen einen anderen Satz für jede von Azure AD unterstützte Sprache konfigurieren könnten, empfehlen wir, dass die Anzahl der Varianten aus Wartungs- und Leistungsgründen so klein wie möglich bleibt.

**Führen Sie die folgenden Schritte aus, um Ihrem Verzeichnis Unternehmensbranding hinzuzufügen:**

1. Melden Sie sich am [klassischen Azure-Portal](https://manage.windowsazure.com) als Administrator des Verzeichnisses an, das Sie anpassen möchten.
2. Wählen Sie das Verzeichnis, das Sie anpassen möchten.
3. Klicken Sie in der Symbolleiste oben auf **Konfigurieren**.
4. Klicken Sie auf **Branding anpassen**.
4. Ändern Sie die Elemente, die Sie anpassen möchten. Beachten Sie, dass alle Felder optional sind.
5. Klicken Sie auf **Speichern**.

Es kann bis zu einer Stunde dauern, bis Neuerungen angezeigt werden, die Sie am Branding der Anmeldeseite vorgenommen haben.

**Führen Sie die folgenden Schritte aus, um sprachspezifisches Unternehmensbranding hinzuzufügen:**

1. Melden Sie sich am [klassischen Azure-Portal](https://manage.windowsazure.com) als Administrator des Verzeichnisses an, das Sie anpassen möchten.
2. Wählen Sie das Verzeichnis, das Sie anpassen möchten.
3. Klicken Sie in der Symbolleiste oben auf **Konfigurieren**.
4. Klicken Sie auf **Branding anpassen**.
2. Klicken Sie auf **Brandingeinstellungen für eine bestimmte Sprache hinzufügen**.
3. Wählen Sie die Sprache, für die Sie das Logo anpassen möchten, und klicken Sie dann auf **Weiter**.
3. Bearbeiten Sie nur die Elemente, für die Sie sprachspezifische Überschreibungen konfigurieren möchten. Beachten Sie, dass alle Felder optional sind. Wenn ein Feld leer bleibt, wird stattdessen der benutzerdefinierte Standardwert angezeigt (oder die Microsoft-Standardeinstellung, wenn kein benutzerdefinierter Standardwert konfiguriert ist).
4. Klicken Sie auf **Speichern**.

**Führen Sie die folgenden Schritte aus, um das Unternehmensbranding für Ihr Verzeichnis zu entfernen:**

1. Melden Sie sich am [klassischen Azure-Portal](https://manage.windowsazure.com) als Administrator des Verzeichnisses an, das Sie anpassen möchten.
2. Wählen Sie das Verzeichnis, das Sie anpassen möchten.
3. Klicken Sie in der Symbolleiste oben auf **Konfigurieren**.
4. Klicken Sie auf **Branding anpassen**.
5. Wählen Sie auf der Seite Branding anpassen **Vorhandene Brandingeinstellungen bearbeiten** und fahren Sie anschließend mit der nächsten Seite fort.
3. Abhängig von den Elementen, die Sie entfernen möchten, führen Sie eine oder mehrere der folgenden Aktionen aus:

	a. Wählen Sie unter **Bannerlogo** die Option **Hochgeladenes Logo entfernen**.

    b. Wählen Sie unter **Kachellogo** die Option **Hochgeladenes Logo entfernen**.

    c. Entfernen Sie den Text aus allen Textfeldern.

    d. Klicken Sie auf **Weiter**.

    e. Entfernen Sie den Text aus allen Textfeldern.

4. Klicken Sie auf **Speichern**, um die Elemente zu entfernen.
5. Klicken Sie ggf. erneut auf **Branding anpassen**, und wiederholen Sie diese Schritte für das gesamte sprachspezifische Branding, das entfernt werden soll. Alle Brandingeinstellungen wurden entfernt, wenn Sie auf **Branding anpassen** klicken und das Formular **Standardmäßiges Branding anpassen** ohne konfigurierte Einstellungen angezeigt wird.

## Testen und Beispiele

Es wird empfohlen, dass Sie mit einem Testmandanten experimentieren, bevor Sie Änderungen in der Produktionsumgebung vornehmen.<br> **Überprüfen Sie wie folgt, ob Ihr Branding angewendet wurde:**

1. Öffnen Sie eine InPrivate- oder Inkognito-Browsersitzung. 
2. Greifen Sie auf https://outlook.com/contoso.com zu, indem Sie „contoso.com“ durch die von Ihnen angepasste Domäne ersetzen. 

Dies funktioniert auch für Domänen, die ein Format wie „contoso.onmicrosoft.com“ haben.

Um Sie bei der Erstellung effektiver Anpassungssätze zu unterstützen, haben wir die folgenden beiden fiktiven Anmeldeseiten erstellt und angepasst:

- [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

Um die sprachspezifischen Einstellungen zu testen, müssen Sie die Standard-Spracheinstellungen in Ihrem Webbrowser in eine Sprache ändern, die Sie in Ihrer Anpassung festgelegt haben. In Internet Explorer wird dies im Menü **Internetoptionen** konfiguriert.

## Anpassbare Elemente

Einige anpassbare Elemente in Azure AD dienen mehreren Verwendungszwecken. Sie können Firmenlogos einmal pro Verzeichnis konfigurieren, um es dann sowohl auf der Anmeldeseite als auch auf der Zugriffsbereichsseite zu verwenden. Einige anpassbare Elemente gelten nur speziell für die Anmeldeseite. Die folgende Tabelle enthält die Details für die verschiedenen anpassbaren Elemente.

Name | Beschreibung | Einschränkungen | Empfehlungen
	------------- | ------------- | ------------- | -------------
Bannerlogo | Das Bannerlogo wird auf der Anmeldeseite und im Zugriffsbereich angezeigt. | <p>JPG oder PNG-</p><p>60 x 280 Pixel</p><p>10 KB</p> | <p>Verwenden Sie das vollständige Logo Ihrer Organisation (einschließlich Piktogramm und Firmenschriftzug)</p><p>Halten Sie es unter 30 Pixel, um Bildlaufleisten auf mobilen Geräten zu vermeiden</p><p>Halten Sie es kleiner als 4 KB</p><p>Verwenden Sie eine transparente PNG-Datei (gehen Sie nicht davon aus, dass die Anmeldeseite immer einen weißen Hintergrund hat)</p>
Kachellogo | (zurzeit nicht auf der Anmeldeseite verwendet) Dieser Text kann in Zukunft verwendet werden, um das generische „Geschäfts- oder Schulkonto“-Piktogramm an unterschiedlichen Stellen zu platzieren. | <p>JPG oder PNG-</p><p>120 x 120 Pixel</p><p>10 KB</p> | <p>Halten Sie es einfach (kein kleiner Text), da dieses Abbild um 50 % verkleinert werden kann
</p> |
Benutzernamenbezeichnung auf der Anmeldeseite | (zurzeit nicht auf der Anmeldeseite verwendet) Dieser Text kann in Zukunft verwendet werden, um die generische „Geschäfts- oder Schulkonto“-Zeichenfolge an unterschiedlichen Stellen zu platzieren. Sie können ihn beispielsweise auf "Contoso-Konto" oder "Contoso-ID" festlegen. | <p>Unicode-Text, bis zu 50 Zeichen</p><p>Ausschließlich Nur-Text (keine Links oder HTML-Tags)</p> | <p>Halten Sie es kurz und einfach</p><p>Fragen Sie die Benutzer, wie Sie sich in der Regel auf das Geschäfts- oder Schulkonto beziehen, das Sie zur Verfügung stellen.</p>
Text der Anmeldeseite | Dieser „Textbaustein“ wird unter dem Anmeldeseitenformular angezeigt und kann verwendet werden, um zusätzliche Anweisungen zu kommunizieren oder mitzuteilen, wo es Hilfe und Support gibt. | <p>Unicode-Text, bis zu 256 Zeichen</p><p>Ausschließlich Nur-Text (keine Links oder HTML-Tags)</p> | Verwenden Sie maximal 250 Zeichen (ungefähr drei Zeilen Text)
Abbildung auf der Anmeldeseite | Die Abbildung zeigt ein großes Bild, das auf der Anmeldeseite links neben dem Anmeldeseitenformular angezeigt wird. | <p>JPG oder PNG-</p><p>1420 x 1200</p><p>500 KB</p> | <p>1420 x 1200 Pixel</p><p>Wichtig: Halten Sie es so klein wie möglich, idealerweise unter 200 KB. Falls dieses Bild zu groß ist, beeinflusst dies die Leistung der Anmeldeseite, wenn das Bild nicht zwischengespeichert wird</p><p>Dieses Bild wird fast immer abgeschnitten, um verschiedene Bildschirm-Seitenverhältnisse zu ermöglichen. Halten die visuellen Hauptelemente in der oberen linken Ecke (oben rechts für RTL-Sprachen), weil die Größenänderung von der Ecke unten rechts nach oben links erfolgt, wenn das Browserfenster verkleinert wird.</p>
Hintergrundfarbe auf der Anmeldeseite | Die Hintergrundfarbe auf der Anmeldeseite wird im Bereich links neben dem Anmeldeseitenformular verwendet. Dies wird sichtbar, wenn keine Abbildung auf der Anmeldeseite vorhanden ist. | Muss eine RGB-Farbe im hexadezimalen Format sein (Beispiel: #FFFFFF) | <p>Die Hintergrundfarbe wird bei Verbindungen mit geringer Bandbreite unter Umständen anstelle der großen Abbildung angezeigt</p><p>Wir empfehlen, die Grundfarbe des Bannerlogos auszuwählen</p>


## Nächste Schritte

- [Erste Schritte mit Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Anzeigen Ihrer Zugriffs- und Nutzungsberichte](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png

<!---HONumber=AcomDC_0330_2016-->