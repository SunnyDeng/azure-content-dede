<properties 
	pageTitle="Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite" 
	description="Bei diesem Thema wird erklärt, wie viele Unternehmen ein einheitliches Erscheinungsbild für all ihre verwalteten Websites und Dienste anstreben, damit ihre Endbenutzer beim Besuch dieser Websites nicht verwirrt werden." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite

> [AZURE.NOTE]
> 
- Unternehmensbranding ist ein Feature, das nur verfügbar ist, wenn Sie Ihr Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).
- Die Azure Active Directory Premium und Basic Editions stehen für Kunden in China zur Verfügung, die mit der weltweit verfügbaren Instanz von Azure Active Directory arbeiten. Allerdings werden die Azure Active Directory-Editionen Premium und Basic derzeit durch den in China von 21Vianet betriebenen Microsoft Azure-Dienst nicht unterstützt. Wenn Sie weitere Informationen benötigen, kontaktieren Sie uns im [Azure Active Directory-Forum](http://feedback.azure.com/forums/169401-azure-active-directory).

Viele Unternehmen streben ein einheitliches Erscheinungsbild für all ihre verwalteten Websites und Dienste an, damit ihre Endbenutzer beim Besuch dieser Websites nicht verwirrt werden. Azure Active Directory ermöglicht dies, indem Sie die Anpassung der Darstellung folgender Websites für Endbenutzer vornehmen können, sodass Firmenlogo und Farbschemas enthalten sind:

- **Anmeldeseite** – Auf diese Seite werden Benutzer weitergeleitet, wenn sie sich bei Office 365 oder anderen webbasierten, modernen Anwendungen anmelden, die Azure AD als Identitätsanbieter verwenden. Die meisten Benutzer interagieren mit dieser Seite entweder beim Durchlaufen der Startbereichserkennung, was dem System das Umleiten von Verbundbenutzern auf ihren lokalen STS (z. B. AD FS) ermöglicht, oder bei der Eingabe ihrer Anmeldeinformationen.

- **Seite "Zugriffsbereich"** – Der Zugriffsbereich ist ein webbasiertes Portal, das einem Endbenutzer die Arbeit mit einem Geschäfts- oder Schulkonto in einem Azure AD-Verzeichnis ermöglicht, um cloudbasierte Anwendungen anzuzeigen und zu starten, zu denen sie vom Azure AD-Administrator Zugriff erhalten haben. Der Zugriffsbereich ist für alle Benutzer in Ihrer Organisation über myapps.microsoft.com zugänglich.

## Anpassung der Anmeldeseite

Die Anmeldeseite ist in der Regel die am häufigsten von Endbenutzern verwendete Webseite, die browserbasierten Zugriff auf die Cloudanwendungen und -dienste benötigen, die Ihre Organisation abonniert. Daher ist das Aussehen dieser Seite äußerst wichtig. Wenn Sie die Standardanmeldeseite ohne Branding verwenden möchten, ist keine weitere Aktion erforderlich.

### Wie lange dauert es, bis die Brandingänderungen auf den Anmeldeseiten sichtbar werden?

Es kann bis zu einer Stunde dauern, bis Benutzer Neuerungen sehen, die Sie am Branding der Anmeldeseite vorgenommen haben.

### Wann wird den Benutzern eine Anmeldeseite mit Branding angezeigt?

Benutzern wird eine Anmeldeseite mit Branding angezeigt, wenn sie einen Dienst mit einer mandantenspezifischen URL besuchen, wie https://outlook.com/**contoso**.com oder https://mail.**contoso**.com (wenn Sie einen CNAME erstellt haben).

Wenn sie einen Dienst ohne mandantenspezifische URLs besuchen (z. B. https://mail.office365.com), sehen Sie eine Anmeldeseite ohne Branding. Der Anmeldeseite wird aktualisiert, um Ihr Branding anzuzeigen, sobald die Benutzer ihre Benutzer-ID eingegeben oder eine Benutzerkachel ausgewählt haben.

> [AZURE.NOTE]
> 
- Ihr Domänenname muss im Abschnitt **Active Directory** > **Verzeichnis** > **Domänen** im Azure-Verwaltungsportal, wo Sie das Branding konfiguriert haben, als "Aktiv" angezeigt werden.
- Die Anmeldeseite mit dem Branding wird nicht auf die Verbraucheranmeldeseite von Microsoft übertragen. Dies bedeutet, dass Benutzer, die sich mit einem persönlichen Microsoft-Konto (früher Windows Live ID) anmelden, eine Liste an von Azure AD gerenderten Benutzerkacheln mit Branding sehen, aber das Branding Ihrer Organisation nicht auf die Microsoft-Kontoanmeldeseite übertragen wird.

### Was wird meinen Endbenutzern angezeigt, nachdem ich die Anmeldeseite angepasst habe?

Wenn Sie Ihre Unternehmensmarke, -farben und andere anpassbare Elemente auf dieser Seite anzeigen möchten, schauen Sie sich die folgenden Bilder an, um den Unterschied zwischen den beiden Eindrücken zu verstehen.

Für einen Benutzer, der sich über einen Desktop-Computer anmeldet, ist hier ein Beispiel für die dem Benutzer angezeigte Office 365-Anmeldeseite *vor* der Anpassung:

![][1]

Und dies sieht derselbe Benutzer *nach* der Anpassung:

![][2]

Für einen Benutzer, der sich über mobiles Gerät anmeldet, ist hier ein Beispiel für die dem Benutzer angezeigte Office 365-Anmeldeseite *vor* der Anpassung:

![][3]

Und dies sieht derselbe Benutzer *nach* der Anpassung:

![][4]

### Welche Elemente auf der Seite können angepasst werden?

Sie können die folgenden Elemente auf der Anmeldeseite anpassen:

![][5]

 Seitenelement | Position auf der Seite
	------------- | -------------
Bannerlogo | Rechts oben auf der Seite angezeigt. Ersetzt das Logo, das normalerweise von der Zielwebsite angezeigt wird, bei der sich die Benutzer anmelden (z. B. Office 365 oder Azure).
Große Abbildung / Hintergrundfarbe | Am linken Rand der Seite angezeigt. Ersetzt das Bild, das normalerweise von der Zielwebsite angezeigt wird, bei der sich die Benutzer anmelden. Die Hintergrundfarbe wird möglicherweise bei Verbindungen mit geringer Bandbreite oder auf sehr kleinen Bildschirmen anstelle der großen Abbildung angezeigt.
Text der Anmeldeseite | Über dem Seitenfuß angezeigt, wenn Sie hilfreiche Informationen an Ihre Benutzer vermitteln wollen, bevor sie sich mit Ihrem Geschäfts- oder Schulkonto anmelden. Ein Beispiel: Sie möchten die Telefonnummer zu Ihrem Helpdesk oder einen rechtlichen Hinweis einfügen.

> [AZURE.NOTE]Alle Elemente sind optional. Wenn Sie z. B. ein Bannerlogo, jedoch keine große Abbildung angeben, wird die Anmeldeseite Ihr Logo und die Abbildung für den Zielstandort (d. h. das Office 365-Bild mit dem kalifornischen Highway) anzeigen.

Sie können auch alle Elemente auf dieser Seite lokalisieren. Sobald Sie einen "Standard"-Satz an Anpassungselementen konfiguriert haben, können Sie zusätzliche Versionen für verschiedene Gebietsschemas konfigurieren. Sie können auch verschiedene Elemente miteinander kombinieren. Dazu zählen z. B.:

- Erstellen Sie eine große "Standard"-Abbildung, die für alle Kulturen funktioniert, und erstellen Sie dann spezifische Versionen für Englisch und Französisch. Benutzer mit Browsern, die auf eine dieser beiden Sprachen festgelegt sind, sehen das spezifische Bild, während allen anderen das Standardbild angezeigt wird.
- Konfigurieren Sie verschiedene Logos für Ihre Organisation (z. B. japanische oder hebräische Versionen).

### Wie wird die Abbildung angezeigt, nachdem die Größe des Browsers geändert wurde?

Während der Größenänderung eines Browserfensters wird die große Abbildung (wie die zuvor gezeigte) fast immer so zugeschnitten, dass verschiedene Bildschirm-Seitenverhältnisse möglich sind. Vor diesem Hintergrund sollten Sie die wichtigsten visuellen Elemente in der Abbildung beibehalten, sodass Sie immer in der oberen linken Ecke (oben rechts für Rechts-nach-links-Sprachen) erscheinen. Dies ist wichtig, da die Größenänderung normalerweise über die rechte untere Ecke nach oben links oder von unten nach oben erfolgt.

Die folgende Abbildung zeigt, wie die Abbildung zugeschnitten wird, wenn die Größe des Browsers nach links verschoben wird:

![][6]

Und hier wird deutlich, wie der Browser nach Verschiebung der Größe nach oben aussehen wird:

![][7]

## Anpassung der Zugriffsbereichsseite

Die Seite "Zugriffsbereich" ist im Wesentlichen eine Portalseite für alle Endbenutzer, die schnellen Zugriff über klickbare Anwendungskacheln auf verschiedene Cloud-Anwendungen benötigen, auf die Sie Zugriff erteilt haben. Wenn Sie die Standardzugriffsbereichsseite ohne Branding verwenden möchten, ist keine weitere Aktion erforderlich.

### Was sehen die Endbenutzer, nachdem die Zugriffsbereichsseite angepasst wurde?

![][8]

## Konfigurieren Sie Ihr Verzeichnis mit Unternehmensbranding

Ein Standardsatz von anpassbaren Elementen kann pro Verzeichnis im Verwaltungsportal konfiguriert werden. Nachdem die Standardeinstellungen gespeichert wurden, hat ein Administrator auch die Möglichkeit, lokalisierte Versionen jedes Elements für verschiedene Sprachen/Gebietsschemas hinzuzufügen. Alle anpassbaren Elemente sind optional.

Wenn Sie z. B. ein Standardbannerlogo konfigurieren, jedoch keine große Abbildung, so wird Ihr Logo auf der Anmeldeseite in der oberen rechten Ecke angezeigt und ansonsten die Standardabbildung. Wenn Sie ein Standardbannerlogo und den Text der Anmeldeseite auf Englisch konfigurieren sowie eine sprachspezifische Anmeldeseite für Deutsch, wird Benutzern mit einer deutscher Spracheinstellung das Standardbannerlogo und deutscher Text angezeigt. Während Sie technisch gesehen einen anderen Satz für jede von Azure AD unterstützte Sprache konfigurieren könnten, empfehlen wir, dass die Anzahl der Varianten aus Wartungs- und Leistungsgründen so klein wie möglich bleibt.

So fügen Sie Ihrem Verzeichnis Unternehmensbranding hinzu:

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) als Administrator des Verzeichnisses an, das Sie anpassen möchten.
2. Wählen Sie das Verzeichnis, das Sie anpassen möchten.
3. Wählen Sie die Registerkarte **Konfigurieren** und anschließend **Branding anpassen**.
4. Ändern Sie die Elemente, die Sie anpassen möchten. Beachten Sie, dass alle Felder optional sind.
5. Klicken Sie auf **Speichern**.

Es kann bis zu einer Stunde dauern, bis Benutzer Neuerungen sehen, die Sie am Branding der Anmeldeseite vorgenommen haben.

So fügen Sie sprachspezifisches Unternehmensbranding hinzu:

1. Wählen Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) unter der Registerkarte **Konfigurieren** **Branding anpassen** aus.
2. Wählen Sie **Hinzufügen von Branding für eine bestimmte Sprache**, wählen Sie dann die Sprache aus, für die Sie das Logo anpassen möchten, und klicken Sie anschließend auf **Weiter**.
3. Bearbeiten Sie nur die Elemente, für die Sie sprachspezifische Überschreibungen konfigurieren möchten. Beachten Sie, dass alle Felder optional sind. Wenn ein Feld leer bleibt, wird stattdessen der benutzerdefinierte Standardwert angezeigt (oder die Microsoft-Standardeinstellung, wenn kein benutzerdefinierter Standardwert konfiguriert ist).
4. Klicken Sie auf **Speichern**.

So entfernen Sie das Unternehmensbranding aus Ihrem Verzeichnis

1. Wählen Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) unter der Registerkarte **Konfigurieren** **Branding anpassen** aus.
2. Wählen Sie auf der Seite Branding anpassen **Vorhandene Brandingeinstellungen bearbeiten** und fahren Sie anschließend mit der nächsten Seite fort.
3. Abhängig von den Elementen, die Sie entfernen möchten, führen Sie eine oder mehrere der folgenden Aktionen aus:
	1. Für das Banner-Logo klicken Sie auf das Kontrollkästchen **Hochgeladenes Logo entfernen**.
    2. Für das Kachel-Logo klicken Sie auf das Kontrollkästchen **Hochgeladenes Logo entfernen**.
    3. Für die Benutzernamenbezeichnung auf der Anmeldeseite löschen Sie den gesamten Text.
    4. Für den Text auf der Anmeldeseite löschen Sie den gesamten Text.
    5. Für die Abbildung auf der Anmeldeseite klicken Sie auf das Kontrollkästchen **Abbildung entfernen**.
    6. Für die Hintergrundfarbe auf der Anmeldeseite löschen Sie den gesamten Text.
4. Klicken Sie auf **Speichern**, um die Elemente zu entfernen.
5. Klicken Sie ggf. erneut auf **Branding anpassen**, und wiederholen Sie diese Schritte für das gesamte sprachspezifische Branding, das entfernt werden soll. Alle Brandingeinstellungen wurden entfernt, wenn Sie auf **Branding anpassen** klicken und das Formular **Standardmäßiges Branding anpassen** ohne konfigurierte Einstellungen angezeigt wird.

## Testen und Beispiele

Es wird empfohlen, dass Sie mit einem Testmandanten experimentieren, bevor Sie Änderungen in der Produktionsumgebung vornehmen. Am einfachsten ist es, die Anwendung Ihres Brandings zu überprüfen, indem Sie eine InPrivate- oder Inkognito-Browsersitzung öffnen und dann auf https://outlook.com/contoso.com gehen. Dabei müssen Sie "contoso.com" mit der angepassten Domäne ersetzen. Beachten Sie, dass dies auch für Domänen funktioniert, die wie contoso.onmicrosoft.com aussehen.

Um Ihnen bei der Erstellung effektiver Anpasssätze zu unterstützen, haben wir die folgenden beiden fiktiven Anmeldeseiten angepasst:

- [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

Um die sprachspezifischen Einstellungen zu testen, müssen Sie die Standard-Spracheinstellungen in Ihrem Webbrowser auf eine Sprache ändern, die Sie in Ihrer Anpassung festgelegt haben. In Internet Explorer wird dies im Menü **Internetoptionen** konfiguriert.

## Anpassbare Elemente

Einige anpassbare Elemente in Azure AD dienen mehreren Verwendungszwecken. Firmenlogos können einmal pro Verzeichnis konfiguriert werden und werden sowohl auf der Anmeldeseite als auch auf der Zugriffsbereichsseite verwendet, wobei einige anpassbare Elemente speziell auf der Anmeldeseite dargestellt werden. Die folgende Tabelle enthält die Details für die verschiedenen anpassbaren Elemente.

Name | Beschreibung | Einschränkungen | Empfehlungen
	------------- | ------------- | ------------- | -------------
Bannerlogo | Das Bannerlogo wird auf der Anmeldeseite und im Zugriffsbereich angezeigt. | <p>JPG oder PNG-</p><p>60 x 280 Pixel</p><p>10 KB</p> | <p>Verwenden Sie das vollständige Logo Ihrer Organisation (einschließlich Piktogramm und Firmenschriftzug)</p><p>Halten Sie es unter 30 Pixel, um Bildlaufleisten auf mobilen Geräten zu vermeiden</p><p>Halten Sie es kleiner als 4 KB</p><p>Verwenden Sie eine transparente PNG-Datei (gehen Sie nicht davon aus, dass die Anmeldeseite immer einen weißen Hintergrund hat)</p>
Kachellogo | (zurzeit nicht auf der Anmeldeseite verwendet) Dieser Text kann in Zukunft verwendet werden, um das generische "Geschäfts- oder Schulkonto"-Piktogramm an unterschiedlichen Stellen zu platzieren. | <p>JPG oder PNG-</p><p>120 x 120 Pixel</p><p>10 KB</p> | <p>Halten Sie es einfach (kein kleiner Text), da dieses Abbild um 50 % verkleinert werden kann
</p> |
Benutzernamenbezeichnung auf der Anmeldeseite | (zurzeit nicht auf der Anmeldeseite verwendet) Dieser Text kann in Zukunft verwendet werden, um die generische "Geschäfts- oder Schulkonto"-Zeichenfolge an unterschiedlichen Stellen zu platzieren. Sie können ihn beispielsweise auf "Contoso-Konto" oder "Contoso-ID" festlegen. | <p>Unicode-Text, bis zu 50 Zeichen</p><p>Ausschließlich Nur-Text (keine Links oder HTML-Tags)</p> | <p>Halten Sie es kurz und einfach</p><p>Fragen Sie die Benutzer, wie Sie sich in der Regel auf das Geschäfts- oder Schulkonto beziehen, das Sie zur Verfügung stellen.</p>
Text der Anmeldeseite | Dieser "Textbaustein" wird unter dem Anmeldeseitenformular angezeigt und kann verwendet werden, um zusätzliche Anweisungen zu kommunizieren oder mitzuteilen, wo es Hilfe und Support gibt. | <p>Unicode-Text, bis zu 256 Zeichen</p><p>Ausschließlich Nur-Text (keine Links oder HTML-Tags)</p> | Verwenden Sie maximal 250 Zeichen (ungefähr drei Zeilen Text)
Abbildung auf der Anmeldeseite | Die Abbildung zeigt ein großes Bild, das auf der Anmeldeseite links neben dem Anmeldeseitenformular angezeigt wird. | <p>JPG oder PNG-</p><p>1420 x 1200</p><p>500 KB</p> | <p>1420 x 1200 Pixel</p><p>Wichtig: Halten Sie es so klein wie möglich, idealerweise unter 200 KB. Wenn dieses Bild zu groß ist, beeinflusst dies die Leistung der Anmeldeseite, wenn das Bild nicht zwischengespeichert wird</p><p>Dieses Bild WIRD fast immer abgeschnitten, um verschiedene Bildschirm-Seitenverhältnisse zu ermöglichen. Halten die visuellen Hauptelemente in der oberen linken Ecke (oben rechts für RTL-Sprachen), weil die Größenänderung von der Ecke unten rechts nach oben links erfolgt, wenn das Browserfenster verkleinert wird.</p>
Hintergrundfarbe auf der Anmeldeseite | Die Hintergrundfarbe auf der Anmeldeseite wird im Bereich links neben dem Anmeldeseitenformular verwendet. Dies wird sichtbar, wenn keine Abbildung auf der Anmeldeseite vorhanden ist. | Muss eine RGB-Farbe im hexadezimalen Format sein (Beispiel: #FFFFFF) | <p>Die Hintergrundfarbe wird möglicherweise bei Verbindungen mit geringer Bandbreite anstelle der großen Abbildung angezeigt</p><p>Wir empfehlen, die Grundfarbe des Bannerlogos zu auszuwählen</p>


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

 

<!---HONumber=August15_HO6-->