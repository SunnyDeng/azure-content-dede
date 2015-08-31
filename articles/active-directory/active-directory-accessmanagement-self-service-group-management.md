<properties
	pageTitle="Einrichten von Azure AD zur Self-Service-Verwaltung des Anwendungszugriffs | Microsoft Azure"
	description="Ein Thema mit Erläuterungen zum Verwalten von Gruppen in Azure AD."
	services="active-directory"
	documentationCenter=""
    authors="femila"
	manager="swadhwa" 
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="femila"/>

#Einrichten von Azure AD zur Self-Service-Verwaltung des Anwendungszugriffs

Die Self-Service-Gruppenverwaltung ermöglicht Benutzern das Erstellen und Verwalten von Sicherheitsgruppen in Microsoft Azure Active Directory (AD) und bietet die Möglichkeit, die Mitgliedschaft in Sicherheitsgruppen anzufordern, die anschließend vom Besitzer der Gruppe genehmigt oder abgelehnt werden kann. Mithilfe der Funktionen für die Self-Service-Gruppenverwaltung kann die tagtägliche Steuerung der Gruppenmitgliedschaft an Personen delegiert werden, die den Geschäftskontext für die betreffende Mitgliedschaft verstehen.

Die Self-Service-Gruppenverwaltung unterstützt zurzeit zwei grundlegende Szenarien: delegierte Gruppenverwaltung und Self-Service-Gruppenverwaltung.


- **Delegierte Gruppenverwaltung** – Beispiel: Ein Administrator verwaltet den Zugriff auf eine SaaS-Anwendung, die im Unternehmen verwendet wird. Die Verwaltung dieser Zugriffsrechte wird immer mühsamer, und der Administrator bittet den Geschäftsinhaber, eine neue Gruppe zu erstellen. Der Administrator weist den Zugriff auf die Anwendung einer neuen Gruppe zu, die der Geschäftsinhaber erstellt hat, und fügt dieser Gruppe alle Personen hinzu, die gegenwärtig Zugriff auf die Anwendung haben. Der Geschäftsinhaber kann dann weitere Benutzer hinzufügen, und die Benutzer erhalten wenige Augenblicke später automatisch zugriff auf die Anwendung. Der Geschäftsinhaber muss nicht warten, bis der Administrator diese Aufgabe erledigt, sondern kann den Zugriff für seine Benutzer selbst verwalten. Der Administrator kann den gleichen Prozess für einen Verwaltungsmitarbeiter einer anderen Geschäftsgruppe ausführen. Sowohl der Geschäftsinhaber als auch der Verwaltungsmitarbeiter können jetzt den Zugriff für ihre Benutzer verwalten, ohne dass sie die Benutzer des jeweils anderen anzeigen oder bearbeiten können. Der Administrator kann weiterhin alle Benutzer anzeigen, die auf die Anwendung zugreifen können, und kann die Zugriffsrechte bei Bedarf blockieren.


- **Self-Service-Gruppenverwaltung** – Beispiel: Zwei Benutzer verfügen über SharePoint Online-Websites, die sie unabhängig voneinander eingerichtet haben. Um die Arbeit für alle Beteiligten zu erleichtern, möchten sie gern dem jeweils anderen Team Zugriff auf ihre Website gewähren. Sie erstellen eine Gruppe in Azure AD, und in SharePoint Online wählt jeder der beiden Benutzer diese Gruppe, um Zugriff auf ihre Websites bereitzustellen. Wenn ein weiterer Benutzer Zugriff benötigt, fordert er diesen einfach über den Zugriffsbereich an und erhält nach Genehmigung automatisch Zugriff auf beide SharePoint Online-Websites. Später entscheidet einer der Hauptbenutzer, dass alle Personen, die auf seine Website zugreifen, auch Zugriff auf eine bestimmte SaaS-Anwendung erhalten sollen. Er bittet den Administrator dieser SaaS-Anwendung, Zugriffsrechte für diese Anwendung auf seiner Website hinzuzufügen. Anschließend können alle Personen, deren Zugriff genehmigt wird, auf die beiden SharePoint Online-Websites und diese SaaS-Anwendung zugreifen.



##Einrichten einer Gruppe für Self-Service durch Endbenutzer

Legen Sie im Azure-Verwaltungsportal auf der Registerkarte "Konfigurieren" sowohl die Option "Delegierte Gruppenverwaltung" als auch die Option "Benutzer können Gruppen erstellen" auf "Aktiviert" fest.

Wenn die Option **Benutzer können Gruppen erstellen** auf **Aktiviert** festgelegt ist, können alle Benutzer in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen und diesen Gruppen Mitglieder hinzuzufügen. Beachten Sie, dass diese neuen Gruppen auch im Zugriffsbereich für alle anderen Benutzer angezeigt werden und dass andere Benutzer Anforderungen zum Beitritt zu diesen Gruppen erstellen können, wenn die Richtlinieneinstellung für die Gruppe dies zulässt. Wenn diese Option auf "Deaktiviert" festgelegt ist, können Benutzer keine Gruppen erstellen und vorhandene Gruppen, deren Besitzer sie sind, nicht ändern. Sie können jedoch weiterhin die Mitgliedschaften in diesen Gruppen verwalten und Anforderungen anderer Benutzer zum Beitritt zu ihren Gruppen genehmigen.

Sie können auch mithilfe der Option "Benutzer, die Self-Service für Sicherheitsgruppen verwenden" eine genauer abgestufte Steuerung des Zugriffs auf die Self-Service-Gruppenverwaltungsfunktionen für Ihre Benutzer einrichten. Wenn die Option "Benutzer können Gruppen erstellen" auf "Aktiviert" festgelegt ist, können alle Benutzer in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen und diesen Gruppen Mitglieder hinzuzufügen. Indem Sie die Option "Benutzer, die Self-Service für Sicherheitsgruppen verwenden" auf "Einige" festlegen, können Sie die Verwaltung der Sicherheitsgruppe auf eine begrenzte Gruppe von Benutzern einschränken. Wenn diese Option auf "Einige" festgelegt ist, wird eine Gruppe namens "SSGMSecurityGroupsUsers" in Ihrem Verzeichnis erstellt, und nur diejenigen Benutzer, die Sie als Mitglieder dieser Gruppe hinzugefügt haben, können in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen und diesen Mitglieder hinzufügen. Indem Sie die Option "Benutzer, die Self-Service für Sicherheitsgruppen verwenden" auf "Alle" festlegen, können alle Benutzer in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen.

Sie können auch mithilfe der Option "Gruppen, die Self-Service für Sicherheitsgruppen verwenden" (standardmäßig auf "SSGMSecurityGroupsUsers" festgelegt) einen benutzerdefinierten Namen für eine Gruppe angeben, die alle Benutzer enthält, welche die Self-Service-Funktionen nutzen und neue Sicherheitsgruppen in Ihrem Verzeichnis erstellen dürfen.

Hier finden Sie einige Themen, die zusätzliche Informationen zu Azure Active Directory bieten:

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO8-->