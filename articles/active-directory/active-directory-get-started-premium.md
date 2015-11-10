<properties
	pageTitle="Erste Schritte mit Azure Active Directory Premium"
	description="In diesem Thema wird die Registrierung für die Premium Edition von Azure Active Directory beschrieben."
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
	ms.date="10/30/2015"
	ms.author="markvi"/>

# Erste Schritte mit Azure Active Directory Premium

Azure Active Directory ist in drei Editions verfügbar: Free, Basic und Premium. Die kostenlose Free Edition ist in einem Azure- oder Office 365-Abonnement enthalten. Die Basic und Premium Editions sind über ein [Microsoft Enterprise Agreement](https://www.microsoft.com/de-DE/licensing/licensing-programs/enterprise.aspx) oder das [Open Volume License](https://www.microsoft.com/de-DE/licensing/licensing-programs/open-license.aspx)-Programm erhältlich. Azure- und Office 365-Abonnenten können Active Directory Premium auch online erwerben. Für den Kauf [hier anmelden](https://portal.office.com/Commerce/Catalog.aspx).

> [AZURE.NOTE]Die Azure Active Directory Premium und Basic Editions stehen für Kunden in China zur Verfügung, die mit der weltweit verfügbaren Instanz von Azure Active Directory arbeiten. Allerdings werden die Azure Active Directory-Editionen Premium und Basic derzeit durch den in China von 21Vianet betriebenen Microsoft Azure-Dienst nicht unterstützt. Wenn Sie weitere Informationen benötigen, kontaktieren Sie uns im [Azure Active Directory-Forum](http://feedback.azure.com/forums/169401-azure-active-directory).

Azure Active Directory Premium ist auch in der **Enterprise Mobility Suite** enthalten. Enterprise Mobility Suite ist eine kostengünstige Lösung für Unternehmen, die Microsoft Intune, Azure Rights Management und die Active Directory Premium-Dienste gemeinsam unter einem Lizenzplan verwenden möchten. Weitere Informationen finden Sie auf der Website [Enterprise Mobility Suite](https://www.microsoft.com/de-DE/server-cloud/enterprise-mobility/overview.aspx).

Wenn Sie die Azure Active Directory Premium-Features noch heute verwenden möchten, führen Sie die folgenden Schritte aus. Die gleichen Schritte gelten auch für die Azure Active Directory Basic Edition.

## Schritt 1: Registrieren Sie sich für Active Directory Premium

Informationen zur Registrierung finden Sie auf der Website [Volume Licensing](http://www.microsoft.com/de-DE/licensing/how-to-buy/how-to-buy.aspx).

## Schritt 2: Aktivieren Sie Ihren Lizenzplan

Wenn dies das erste Mal ist, dass Sie einen Lizenzplan über das Enterprise Volume Licensing-Programm von Microsoft erwerben, müssen Sie den Lizenzplan zuerst aktivieren, bevor Sie mit der Zuweisung von Lizenzen aus Ihrem Azure Active Directory-Verzeichnis beginnen können. Dazu müssen Sie in der Bestätigungs-E-Mail, die Sie nach dem Kauf Ihres ersten Lizenzplans erhalten, auf den Anmelde- bzw. Registrierungslink klicken (siehe unten). Bei jedem nachfolgenden Kauf für dieses Verzeichnis werden die Lizenzen automatisch im gleichen Verzeichnis aktiviert.

![][1]

Wenn Sie bereits über einen Mandanten verfügen, klicken Sie auf den Link zum **Anmelden**, um sich bei Ihrem vorhandenen Administratorkonto anzumelden. Die Anmeldung muss über die Anmeldeinformationen des globalen Administrators des Verzeichnisses erfolgen, in dem die Lizenzen aktiviert werden sollen.

Wenn Sie für Ihren Lizenzplan einen neuen Azure Active Directory-Mandanten erstellen möchten, klicken Sie auf den Link zum **Registrieren**, der folgenden Bildschirm öffnet.

![][2]

Sobald Sie die aus der E-Mail veranlasste Anmeldung bzw. Registrierung abgeschlossen haben, sehen Sie folgenden Bildschirm, der Ihnen bestätigt, dass der Lizenzplan für Ihren Mandanten aktiviert wurde.

![][3]

## Schritt 3: Aktivieren Sie den Zugriff auf Azure Active Directory

Nach der Bereitstellung der Lizenzen in Ihrem Verzeichnis erhalten Sie eine Begrüßungs-E-Mail (siehe unten), die bestätigt, dass Sie nun mit der Verwaltung Ihrer Azure Active Directory Premium- oder Enterprise Mobility Suite-Lizenzen und Features beginnen können. Wenn Sie Microsoft Azure bereits verwendet haben, können Sie mit http://manage.windowsazure.com fortfahren, um die neuen Lizenzen zuzuweisen (Anweisungen hierzu finden Sie nachfolgend in Schritt 4). Wenn Sie noch nicht mit Microsoft Azure gearbeitet haben, werden Sie über den Anmeldelink der E-Mail bzw. über die Aktivierungsseite [Zugriff auf Azure Active Directory](https://account.windowsazure.com/signup?offer=MS-AZR-0110P) durch eine Reihe von Schritten geführt, die Ihnen dabei helfen, über das Azure-Verwaltungsportal auf Ihr Verzeichnis zuzugreifen.

![][4]

Nach der erfolgreichen Anmeldung müssen Sie einen Bildschirm für eine zweistufige Authentifizierung ausfüllen (siehe unten). Hier geben Sie Ihre Mobiltelefonnummer ein und bestätigen diese. Danach aktivieren Sie den Zugriff auf Azure Active Directory über den Link **Registrieren**.

![][5]

Die Aktivierung kann, wie unten dargestellt, einige Minuten dauern. Sobald aber der Zugriff aktiviert ist, verschwindet der braune Balken und Sie können in der oberen rechten Ecke auf den Link „Portal“ klicken oder zum [Azure-Verwaltungsportal](http://manage.windowsazure.com) navigieren.

![][6]

In diesem Fall ist Ihr Azure-Zugriff auf Azure Active Directory beschränkt.

![][7]

Möglicherweise hatten Sie aufgrund einer früheren Verwendung bereits Zugriff auf Azure. Außerdem können Sie Ihren Azure Active Directory-Zugriff durch Aktivierung weiterer Azure-Abonnements auf einen vollständigen Azure-Zugriff erweitern. In diesen Fällen bietet das Verwaltungsportal über die im Folgenden beschriebenen Funktionen hinaus weitere Funktionen.

![][8]

Wenn Sie versuchen, den Zugriff auf Azure Active Directory zu aktivieren, bevor Ihnen die oben erwähnte Begrüßungs-E-Mail zugestellt wurde, erhalten Sie möglicherweise folgende Fehlermeldung. Versuchen Sie die Aktivierung in diesem Fall erneut wenige Minuten nach Erhalt der E-Mail.

![][9]

Innerhalb Ihres Abonnements neue Administratoren können ihren Zugriff auf das Verwaltungsportal ebenfalls über diesen Link aktivieren.

## Schritt 4: Weisen Sie die Lizenzen Benutzerkonten zu

Bevor Sie den erworbenen Plan verwenden können, müssen Sie die Lizenzen manuell Benutzerkonten Ihres Unternehmens zuweisen, damit diese das reichhaltige Feature-Angebot der Premium Edition nutzen können. Führen Sie zur Zuweisung von Lizenzen zu Benutzern die folgenden Schritte aus, so dass diese die Features der Premium Edition von Azure Active Directory nutzen können.

Zuweisen von Lizenzen zu Benutzern:

1. Melden Sie sich beim Verwaltungsportal als globaler Administrator des Verzeichnisses an, das Sie anpassen möchten.
2. Klicken Sie auf **Active Directory**, und wählen Sie dann das Verzeichnis aus, in dem Sie Lizenzen zuweisen möchten.
3. Klicken Sie auf die Registerkarte **Lizenzen**, wählen Sie **Active Directory Premium** oder **Enterprise Mobility Suite** aus, und klicken Sie dann auf **Zuweisen**.

    ![][10]

4. Wählen Sie im Dialogfeld die Benutzer aus, denen Sie Lizenzen zuweisen möchten, und klicken Sie dann auf das Häkchen, um die Änderungen zu speichern.

    ![][11]

## Lizenzeinschränkungen

Es gibt Lizenzpläne, die in anderen Lizenzplänen enthalten sind oder andere Lizenzpläne enthalten. In den meisten Fällen kann einem Benutzer kein Lizenzplan zugewiesen werden, der ihm bereits zugewiesen wurde. Wenn Sie einem Benutzer einen Lizenzplan zuweisen möchten, der andere Lizenzpläne enthält, die dem Benutzer bereits zugewiesen sind, müssen Sie die bereits zugewiesenen Lizenzpläne zunächst entfernen.

## Lizenzanforderungen

Wenn Sie einem Benutzer eine Lizenz zuweisen, können Sie, wie unten gezeigt, in den Eigenschaften des Benutzerkontos einen primären Verwendungsort angeben. Wenn kein solcher Verwendungsort angegeben ist, wird dem Benutzer automatisch der Standort des Mandanten zugewiesen.

![][12]

Die Verfügbarkeit der Dienste und Features eines Microsoft Clouddiensts variieren je nach Land und Region. Ein Dienst wie Voice over IP (VoIP) kann in einem Land bzw. einer Region verfügbar sein, in einem anderen hingegen nicht. Die Features eines Diensts können aus rechtlichen Gründen in einigen Ländern oder Regionen eingeschränkt sein. Um festzustellen, ob ein Dienst oder ein Feature für Sie uneingeschränkt oder mit Einschränkungen verfügbar ist, suchen Sie Ihr Land bzw. Ihre Region auf der Site mit den Lizenzeinschränkungen des jeweiligen Diensts.

## Nächste Schritte

- [Hinzufügen Ihres Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](active-directory-add-company-branding.md)
- [Anzeigen Ihrer Zugriffs- und Nutzungsberichte](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

<!---HONumber=Nov15_HO2-->