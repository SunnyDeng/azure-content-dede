<properties
	pageTitle="Anpassen ausgestellter Ansprüche im SAML-Token für bereits in Azure Active Directory integrierte Apps | Microsoft Azure"
	description="Erfahren Sie, wie Sie im SAML-Token für bereits in Azure Active Directory integrierte Apps ausgestellte Ansprüche anpassen."
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
	ms.date="02/26/2016"
	ms.author="asmalser"/>

#Anpassen ausgestellter Ansprüche im SAML-Token für bereits in Azure Active Directory integrierte Apps

Azure Active Directory unterstützt heute Tausende von bereits integrierten Anwendungen im Azure AD-Anwendungskatalog, darunter über 150, die einmaliges Anmelden mithilfe des SAML 2.0-Protokolls unterstützen. Wenn ein Benutzer sich über Azure AD per SAML bei einer Anwendung authentifiziert, sendet Azure AD (über eine HTTP 302-Umleitung) ein Token an die Anwendung, welches die Anwendung dann überprüft und anstelle von Benutzername und Kennwort für die Anmeldung des Benutzers verwendet. Diese SAML-Token enthalten Informationen über den Benutzer, die als „Ansprüche“ bezeichnet werden.

Im technischen Jargon bezeichnet ein Anspruch (Claim) Informationen über den Benutzer, die ein Identitätsanbieter im Benutzertoken übergibt. Bei einem [SAML-Token](http://en.wikipedia.org/wiki/SAML_2.0) befinden sich diese Informationen i. d. R. in der SAML-Attributanweisung, und die eindeutige ID des Benutzers wird durch den SAML-Betreff dargestellt.

Standardmäßig stellt Azure AD der Anwendung ein SAML-Token aus, das einen NameIdentifier-Anspruch enthält, dessen Wert dem Benutzernamen in Azure AD entspricht (über den der Benutzer eindeutig identifiziert ist). Das SAML-Token enthält auch zusätzliche Ansprüche, die E-Mail-Adresse des Benutzers, Vorname und Nachname enthält.

Zum Anzeigen oder Bearbeiten der im SAML-Token an die Anwendung ausgestellten Ansprüche öffnen Sie den Anwendungsdatensatz im Azure-Verwaltungsportal, und wählen Sie Registerkarte **Attribute** unterhalb der Anwendung.

![][1]

Es kann aus zwei Gründen nötig werden, die im SAML-Token ausgestellten Ansprüche zu bearbeiten: • Die Anwendung wurde so geschrieben, das sie eine andere Gruppe von Anspruchs-URIs oder Anspruchswerten verlangt • Die Anwendung wurde so bereitgestellt, dass sie als NameIdentifier-Anspruch etwas anderes verlangt als den Benutzernamen (den Benutzerprinzipalnamen), der in Azure Active Directory gespeichert ist.

Jeder der standardmäßigen Anspruchswerte lässt sich durch Auswählen des Stiftsymbols bearbeiten, das auf der rechten Seite angezeigt wird, wenn sich der Mauszeiger über einer der Zeilen in der Tabelle der SAML-Tokenattribute befindet. Ansprüche (mit Ausnahme von „NameIdentifier“) lassen sich mithilfe des Symbols **X** auch entfernen, und es können neue Ansprüche über die Schaltfläche **Benutzerattribut hinzufügen** erstellt werden.

##Bearbeiten des NameIdentifier-Anspruchs

Falls die Anwendung mit einem anderen Benutzernamen bereitgestellt wurde, klicken Sie auf das Stiftsymbol neben dem NameIdentifier-Anspruch. Dadurch wird ein Dialogfeld mit mehreren Optionen angezeigt:

![][2]

Wählen Sie im Menü **Attributwert** die Option **user.mail** aus, um den NameIdentifier-Anspruch auf die E-Mail-Adresse des Benutzers im Verzeichnis festzulegen, oder wählen Sie **user.onpremisessamaccountname** aus, um den SAM-Kontonamen des Benutzers zu verwenden, der aus dem lokalen Azure AD synchronisiert wurde.

Sie können auch die spezielle ExtractMailPrefix()-Funktion verwenden, um das Domänensuffix von der E-Mail-Adresse des Benutzers bzw. vom Prinzipalnamen zu entfernen, wodurch nur der erste Bestandteil des Benutzernamens durchgeleitet wird (z. B. „joesmith“ statt joesmith@contoso.com).

![][3]

##Hinzufügen von Ansprüchen

Beim Hinzufügen eines neuen Anspruchs kann der Attributname angegeben werden (der nicht unbedingt einem URI-Muster gemäß SAML-Spezifikation entsprechen muss), und der Wert kann auf jedes im Verzeichnis gespeicherte Benutzerattribut festgelegt werden.

![][4]

Wenn beispielsweise die Abteilung des Benutzers innerhalb seiner Organisation (z. B. Vertrieb) als Anspruch gesendet werden soll, kann ein beliebiger von der Anwendung erwarteter Anspruchswert eingegeben und dann **user.department** als Wert ausgewählt werden.

Wenn für einen bestimmten Benutzer kein Wert für ein ausgewähltes Attribut gespeichert ist, dann wird dieser Anspruch nicht in dem Token ausgestellt.

**Hinweis**: Die Optionen **user.onpremisesecurityidentifier** und **user.onpremisesamaccountname** werden nur beim Synchronisieren von Benutzerdaten aus dem lokalen Active Directory mithilfe des Tools [Azure AD Connect](active-directory-aadconnect.md) unterstützt.

## Verwandte Artikel

- [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
- [Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind](active-directory-saas-custom-apps.md)
- [Problembehandlung bei SAML-basiertem einmaligem Anmelden](active-directory-saml-debugging.md)
	
<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png

<!---HONumber=AcomDC_0302_2016-->