<properties
	pageTitle="Self-Service-Anwendungszugriff und delegierte Verwaltung mit Azure Active Directory | Microsoft Azure"
	description="Dieser Artikel beschreibt, wie der Self-Service-Anwendungszugriff und die delegierte Verwaltung mit Azure Active Directory aktiviert werden."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="asmalser"/>

#Self-Service-Anwendungszugriff und delegierte Verwaltung mit Azure Active Directory

Das Aktivieren von Self-Service-Funktionen für Endbenutzer ist ein häufiges Szenario in IT-Unternehmen. Dort gibt es viele Benutzer und viele Anwendungen, und die am besten informierte Person, die über das Erteilen von Zugriffsrechten entscheidet, ist nicht unbedingt der Verzeichnisadministrator. Oft ist die Person, die am besten darüber entscheiden kann, wer Zugriff auf eine Anwendung erhält, ein Teamleiter oder ein anderer delegierter Administrator. Doch letzten Endes ist es der Benutzer, der die Anwendung benutzt, und nur er weiß genau, was er zum Ausführen seiner Aufgaben benötigt.

Der Self-Service-Anwendungszugriff ist eine Funktion von [Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/), mit der Verzeichnisadministratoren folgende Möglichkeiten haben:

* Es Benutzern ermöglichen, mithilfe der Kachel „Weitere Anwendungen abrufen“ im [Azure AD-Zugriffsbereich](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) den Zugriff auf Anwendungen anzufordern.
* Festlegen, für welche Anwendungen Benutzer den Zugriff anfordern können.
* Festlegen, ob Benutzer eine Genehmigung benötigen, um sich selbst den Zugriff auf eine Anwendung zuweisen zu können.
* Festlegen, wer die Anforderungen genehmigt und den Zugriff für jede Anwendung verwaltet.

Derzeit wird diese Funktion für alle bereits integrierten und benutzerdefinierten Anwendungen unterstützt, die die einmalige Verbundanmeldung oder die kennwortbasierte einmalige Anwendung im [Azure Active Directory-Anwendungskatalog](https://azure.microsoft.com/marketplace/active-directory/all/) unterstützen, einschließlich Apps wie Salesforce, Dropbox, Google Apps usw. Dieser Artikel beschreibt, wie Sie:

* den Self-Service-Anwendungszugriff für Endbenutzer konfigurieren, einschließlich der Konfiguration eines optionalen Genehmigungsworkflows. 
* die Zugriffsverwaltung für bestimmte Anwendungen auf die am besten geeigneten Personen in Ihrer Organisation delegieren und ihnen ermöglichen, den Azure AD-Zugriffsbereich zum Genehmigen von Zugriffsanforderungen, dem direkten Zuweisen von Zugriff für ausgewählte Benutzer oder (optional) zum Festlegen von Anmeldeinformationen für den Anwendungszugriff verwenden, wenn die kennwortbasierte einmalige Anwendung konfiguriert ist.


##Konfigurieren des Self-Service-Anwendungszugriffs

Um den Self-Service-Anwendungszugriff zu aktivieren und zu konfigurieren, welche Anwendungen von den Endbenutzern hinzugefügt oder angefordert werden können, befolgen Sie die nachstehenden Anweisungen.

**1:** Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) an.

**2:** Wählen Sie im Abschnitt **Active Directory** Ihr Verzeichnis und wählen Sie dann die Registerkarte **Anwendungen**.

**3:** Wählen Sie die Schaltfläche **Hinzufügen** und verwenden Sie die Katalogoption zum Auswählen und Hinzufügen einer Anwendung.

**4:** Nachdem die Anwendung hinzugefügt wurde, erhalten Sie die Seite „Schnellstart“ für diese Anwendung. Klicken Sie auf **Einmaliges Anmelden konfigurieren**, wählen Sie den gewünschten SSO-Modus aus und speichern Sie die Konfiguration.

**5:** Wählen Sie als Nächstes die Registerkarte **Konfigurieren**. Um es Benutzern zu ermöglichen, den Zugriff auf diese Anwendung aus dem Azure AD-Zugriffsbereich anzufordern, legen Sie **Self-Service-Anwendungszugriff zulassen** auf **Ja** fest.

![][1]

**6:** Um optional einen Genehmigungsworkflow für Zugriffsanforderungen zu konfigurieren, legen Sie **Zustimmung vor Gewähren des Zugriffs** auf **Ja** fest. Anschließend kann eine oder mehrere Personen mit der Schaltfläche **Genehmigende Personen** ausgewählt werden.

Eine genehmigende Person kann jeder Benutzer in der Organisation mit einem Azure AD-Konto sein. Er kann für die Kontobereitstellung, Lizenzierung oder einen beliebigen anderen Geschäftsprozess verantwortlich sein, den Ihre Organisation vor Gewähren des Zugriffs auf eine Anwendung erfordert. Die genehmigende Person kann sogar der Besitzer der Gruppe für eine oder mehrere freigegebene Kontogruppen sein und kann Benutzer einer dieser Gruppen zuweisen, um ihnen den Zugriff auf ein freigegebenes Konto zu gewähren.

Wenn keine Genehmigung erforderlich ist, wird die Anwendung sofort zum Azure AD-Zugriffsbereich der Benutzer hinzugefügt. Dies ist geeignet, wenn die [automatische Benutzerbereitstellung](active-directory-saas-app-provisioning.md) für die Anwendung festgelegt wurde oder der [„benutzerverwaltete“ Kennwort-SSO-Modus](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) eingerichtet wurde, bei dem der Benutzer bereits über ein eigenes Konto verfügt und das Kennwort weiß.

**7:** Wenn die Anwendung für das kennwortbasierte einmalige Anmelden konfiguriert wurde, ist die Option ebenfalls verfügbar, dass die genehmigende Person die SSO-Anmeldeinformationen für jeden Benutzer festlegen kann. Weitere Informationen finden Sie im folgenden Abschnitt zum Delegieren der Zugriffsverwaltung.

**8:** Schließlich zeigt die **Gruppe für selbst-zugewiesene Benutzer** den Namen der Gruppe an, der zum Speichern der Benutzer verwendet wird, denen der Zugriff auf die Anwendung zugewiesen oder gewährt wurde. Die Person, die den Zugriff genehmigt, wird zum Besitzer dieser Gruppe. Wenn der angezeigte Gruppenname nicht vorhanden ist, wird er automatisch erstellt. Optional kann der Gruppenname auf den Namen einer vorhandenen Gruppe festgelegt werden.

**9:** Klicken Sie am unteren Bildschirmrand auf **Speichern**, um die Konfiguration zu speichern. Jetzt können Benutzer den Zugriff auf diese Anwendung über den Zugriffsbereich anfordern.

**10:** Melden Sie sich zum Testen der Endbenutzerumgebung unter https://myapps.microsoft.com am Azure AD-Zugriffsbereich Ihrer Organisation an, vorzugsweise mit einem Konto, das keinem Anwendungsgenehmiger gehört.

**11:** Klicken Sie unter der Registerkarte **Anwendungen** auf die Kachel **Weitere Anwendungen abrufen**. Dadurch wird ein Katalog mit allen Anwendungen angezeigt, für die der Self-Service-Anwendungszugriff im Verzeichnis aktiviert wurde. Sie haben die Möglichkeit, auf der linken Seite nach Anwendungskategorie zu suchen und zu filtern.

**12:** Durch Klicken auf eine Anwendung wird der Anforderungsprozess gestartet. Wenn kein Genehmigungsprozess erforderlich ist, wird die Anwendung nach einer kurzen Bestätigung sofort unter der Registerkarte **Anwendungen** hinzugefügt. Wenn eine Genehmigung erforderlich ist, sehen Sie ein entsprechendes Dialogfeld, und eine E-Mail-Nachricht wird an die genehmigenden Personen gesendet. (Kurzer Hinweis: Sie müssen im Zugriffsbereich als nicht-genehmigende Person angemeldet sein, damit dieser Anforderungsprozess angezeigt wird.)

**13:** Die E-Mail leitet die genehmigenden Personen zur Anmeldung am Azure AD-Zugriffsbereich und zum Bestätigen der Anforderung weiter. Nachdem die Anforderung genehmigt wurde (und alle speziellen Prozesse, die von Ihnen definiert wurden, von der genehmigenden Person durchgeführt wurden), sieht der Benutzer die Anwendung unter der Registerkarte **Anwendungen**, wo er sich daran anmelden kann.

##Delegierte Verwaltung des Anwendungszugriffs

Eine genehmigende Person kann jeder Benutzer in der Organisation sein, die am besten dafür geeignet ist, den Zugriff auf die betreffende Anwendung freizugeben oder zu verweigern. Dieser Benutzer kann für die Kontobereitstellung, Lizenzierung oder einen beliebigen anderen Geschäftsprozess verantwortlich sein, den Ihre Organisation vor Gewähren des Zugriffs auf eine Anwendung erfordert.
 
Beim Konfigurieren des oben beschriebenen Self-Service-Anwendungszugriffs sehen alle zugewiesenen Anwendungsgenehmiger die zusätzliche Kachel **Anwendungen verwalten** im Azure AD-Zugriffsbereich, die anzeigt, für welche Anwendungen sie der Zugriffsadministrator sind. Durch Klicken auf die Anwendung erscheint ein Bildschirm mit mehreren Optionen.

![][2]

###Anforderungen genehmigen

Mit der Kachel **Anforderungen genehmigen** können genehmigende Personen alle zur Anwendung gehörenden ausstehenden Anforderungen sehen und werden zur Registerkarte „Genehmigungen“ weitergeleitet, auf der die Anforderungen bestätigt oder abgelehnt werden können. Beachten Sie, dass die genehmigende Person außerdem automatisierte E-Mail-Nachrichten mit Anweisungen empfängt, sobald eine Anforderung erstellt wurde.

###Benutzer hinzufügen

Mit der Kachel **Benutzer hinzufügen** können genehmigende Personen ausgewählten Benutzern direkt den Zugriff auf die Anwendung gewähren. Durch Klicken auf die Kachel wird genehmigenden Personen ein Dialogfeld angezeigt, durch das sie in ihrem Verzeichnis Benutzer anzeigen und diese suchen können. Durch Hinzufügen eines Benutzers wird die Anwendung im Azure AD-Zugriffsbereich oder im Office 365 dieses Benutzers angezeigt. Falls in der Anwendung ein manueller Prozess zur Benutzerbereitstellung erforderlich ist, bevor der Benutzer sich anmelden kann, sollte die genehmigende Person vor dem Zuweisen des Zugriffs zunächst diesen Prozess durchführen.

###Benutzer verwalten

Mit der Kachel **Benutzer verwalten** können genehmigende Personen durch Aktualisieren oder Entfernen direkt steuern, welche Benutzer Zugriff auf die Anwendung haben.

###Kennwortbasierte SSO-Anmeldeinformationen konfigurieren (falls zutreffend)

Die Kachel **Konfigurieren** wird nur angezeigt, wenn die Anwendung vom IT-Administrator zur Verwendung der kennwortbasierten einmaligen Anmeldung konfiguriert wurde und der Administrator der genehmigenden Person die Möglichkeit gewährt hat, die kennwortbasierten SSO-Anmeldeinformationen wie zuvor beschrieben festzulegen. Bei Auswahl dieser Möglichkeit erhält die genehmigende Person mehrere Optionen, wie die Anmeldeinformationen an zugewiesene Benutzer weitergegeben werden:

![][3]

* **Benutzer melden Sie sich mit ihrem eigenen Kennwort an** – In diesem Modus kennen die zugewiesenen Personen ihren Benutzernamen und das Kennwort für die Anwendung und werden dazu aufgefordert, sie bei der ersten Anmeldung an der Anwendung einzugeben. Dies entspricht dem Kennwort-SSO-Fall, bei dem [die Benutzer ihre Anmeldeinformationen verwalten](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

* **Benutzer werden automatisch mithilfe separater Konten angemeldet, die ich verwalte** – In diesem Modus werden die zugewiesenen Benutzer nicht dazu aufgefordert, ihre anwendungsspezifischen Anmeldeinformationen bei der Anmeldung an der Anwendung einzugeben oder diese zu kennen. Stattdessen legt die genehmigende Person die Anmeldeinformationen für jeden Benutzer nach dem Zuweisen des Zugriffs über die Kachel **Benutzer hinzufügen** fest. Klickt der Benutzer im Zugriffsbereich oder in Office 365 auf die Anwendung, wird er automatisch mit den von der genehmigenden Person festgelegten Anmeldeinformationen angemeldet. Dies entspricht dem Kennwort-SSO-Fall, bei dem die [Administratoren die Anmeldeinformationen verwalten](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

* **Benutzer werden automatisch mithilfe eines einzelnen Kontos angemeldet, das ich verwalte** – Dies ist ein Sonderfall, der dafür geeignet ist, allen zugewiesenen Benutzern den Zugriff mithilfe eines einzelnen freigegebenen Kontos zu erteilen. Am häufigsten tritt dies bei Social Media-Anwendungen auf, bei denen eine Organisation ein einzelnes „Unternehmenskonto“ besitzt und mehrere Benutzer darin Aktualisierungen vornehmen. Dies entspricht auch dem Kennwort-SSO-Fall, bei dem die [Administratoren die Anmeldeinformationen verwalten](active-directory-appssoaccess-whatis.md#password-based-single-sign-on). Nachdem diese Option ausgewählt wurde, wird die genehmigende Person jedoch dazu aufgefordert, den Benutzernamen und das Kennwort für das einzelne freigegebene Konto einzugeben. Nach Fertigstellung werden alle zugewiesenen Benutzer mit diesem Konto angemeldet, wenn sie in ihrem Azure AD-Zugriffsbereich oder in Office 365 auf die Anwendung klicken.

##Zusätzliche Ressourcen
- [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG

<!---HONumber=AcomDC_0211_2016-->