<properties
	pageTitle="Einrichten von Azure Active Directory zur Self-Service-Verwaltung des Anwendungszugriffs | Microsoft Azure"
	description="Die Self-Service-Gruppenverwaltung ermöglicht Benutzern das Erstellen und Verwalten von Sicherheitsgruppen oder Office 365-Gruppen in Microsoft Azure Active Directory sowie das Anfordern der Mitgliedschaft in Sicherheitsgruppen oder Office 365-Gruppen."
	services="active-directory"
	documentationCenter=""
  authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/17/2016"
	ms.author="curtand"/>

# Einrichten von Azure Active Directory zur Self-Service-Gruppenverwaltung

Die Self-Service-Gruppenverwaltung ermöglicht Benutzern das Erstellen und Verwalten von Sicherheitsgruppen oder Office 365-Gruppen in Microsoft Azure Active Directory (Azure AD) und bietet die Möglichkeit, die Mitgliedschaft in Sicherheitsgruppen oder Office 365-Gruppen anzufordern, die anschließend vom Besitzer der Gruppe genehmigt oder abgelehnt werden kann. Mithilfe der Funktionen für die Self-Service-Gruppenverwaltung kann die tagtägliche Steuerung der Gruppenmitgliedschaft an Personen delegiert werden, die den Geschäftskontext für die betreffende Mitgliedschaft verstehen. Self-Service-Funktionen zur Gruppenverwaltung sind nur für Sicherheitsgruppen und Office 365-Gruppen, und nicht für E-Mail-aktivierte Sicherheitsgruppen oder Verteilerlisten verfügbar.

Die Self-Service-Gruppenverwaltung unterstützt derzeit zwei grundlegende Szenarien: delegierte Gruppenverwaltung und Self-Service-Gruppenverwaltung.


- **Delegierte Gruppenverwaltung** – Beispielsweise ein Administrator, der den Zugriff auf eine SaaS-Anwendung verwaltet, die im Unternehmen verwendet wird. Die Verwaltung dieser Zugriffsrechte wird immer mühsamer, und der Administrator bittet den Geschäftsinhaber, eine neue Gruppe zu erstellen. Der Administrator weist den Zugriff auf die Anwendung einer neuen Gruppe zu, die der Geschäftsinhaber erstellt hat, und fügt dieser Gruppe alle Personen hinzu, die gegenwärtig Zugriff auf die Anwendung haben. Der Geschäftsinhaber kann dann weitere Benutzer hinzufügen, und die Benutzer erhalten wenige Augenblicke später automatisch zugriff auf die Anwendung. Der Geschäftsinhaber muss nicht warten, bis der Administrator diese Aufgabe erledigt, sondern kann den Zugriff für seine Benutzer selbst verwalten. Der Administrator kann den gleichen Prozess für einen Verwaltungsmitarbeiter einer anderen Geschäftsgruppe ausführen. Sowohl der Geschäftsinhaber als auch der Verwaltungsmitarbeiter können jetzt den Zugriff für ihre Benutzer verwalten, ohne dass sie die Benutzer des jeweils anderen anzeigen oder bearbeiten können. Der Administrator kann weiterhin alle Benutzer anzeigen, die auf die Anwendung zugreifen können, und kann die Zugriffsrechte bei Bedarf blockieren.


- **Self-Service-Gruppenverwaltung** – Ein Beispiel für dieses Szenario sind zwei Benutzer mit SharePoint Online-Websites, die sie unabhängig voneinander eingerichtet haben, dem jeweils anderen Team jedoch Zugriff auf ihre Website gewähren möchten. Dazu erstellen können sie eine Gruppe in Azure AD erstellen, und in SharePoint Online wählt jeder der beiden Benutzer diese Gruppe aus, um Zugriff auf ihre Websites bereitzustellen. Wenn ein weiterer Benutzer Zugriff benötigt, fordert er diesen einfach über den Zugriffsbereich an und erhält nach Genehmigung automatisch Zugriff auf beide SharePoint Online-Websites. Später entscheidet einer der Hauptbenutzer, dass alle Personen, die auf seine Website zugreifen, auch Zugriff auf eine bestimmte SaaS-Anwendung erhalten sollen. Er bittet den Administrator dieser SaaS-Anwendung, Zugriffsrechte für diese Anwendung auf seiner Website hinzuzufügen. Anschließend können alle Personen, deren Zugriff genehmigt wird, auf die beiden SharePoint Online-Websites und diese SaaS-Anwendung zugreifen.

## Einrichten einer Gruppe für Self-Service durch Endbenutzer

Aktivieren Sie im [klassischen Azure-Portal](https://manage.windowsazure.com) auf der Registerkarte **Konfigurieren** die Option **Delegierte Gruppenverwaltung** und anschließend die Option **Benutzer können Sicherheitsgruppen erstellen** oder **Benutzer können Office-Gruppen erstellen**.

Wenn die Option **Benutzer können Sicherheitsgruppen erstellen** aktiviert ist, können alle Benutzer in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen und diesen Gruppen Mitglieder hinzuzufügen. Diese neuen Gruppen werden auch im Zugriffsbereich für alle anderen Benutzer angezeigt und andere Benutzer können Beitrittsanfragen für diese Gruppen erstellen, wenn die Richtlinieneinstellung für die Gruppe dies zulässt. Wenn die Option **Benutzer können Sicherheitsgruppen erstellen** deaktiviert ist, können Benutzer keine Gruppen erstellen und vorhandene Gruppen, deren Besitzer sie sind, nicht ändern. Sie können jedoch weiterhin die Mitgliedschaften in diesen Gruppen verwalten und Anforderungen anderer Benutzer zum Beitritt zu ihren Gruppen genehmigen.

Sie können auch mithilfe der Option **Benutzer, die Self-Service für Sicherheitsgruppen verwenden** eine genauer abgestufte Steuerung des Zugriffs auf die Self-Service-Gruppenverwaltungsfunktionen für Ihre Benutzer einrichten. Wenn die Option **Benutzer können Gruppen erstellen** aktiviert ist, können alle Benutzer in Ihrem Verzeichnis neue Gruppen erstellen und diesen Mitglieder hinzufügen. Indem Sie die Option **Benutzer, die Self-Service für Sicherheitsgruppen verwenden** auf „Einige“ festlegen, können Sie die Gruppenverwaltung auf eine begrenzte Benutzergruppe einschränken. Wenn diese Option auf "Einige" festgelegt ist, wird eine Gruppe namens "SSGMSecurityGroupsUsers" in Ihrem Verzeichnis erstellt, und nur diejenigen Benutzer, die Sie als Mitglieder dieser Gruppe hinzugefügt haben, können in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen und diesen Mitglieder hinzufügen. Wenn Sie die Option **Benutzer, die Self-Service für Sicherheitsgruppen verwenden** auf „Alle“ festlegen, können alle Benutzer in Ihrem Verzeichnis neue Gruppen erstellen.

Sie können auch im Feld **Gruppen, die Self-Service für Sicherheitsgruppen verwenden** (standardmäßig auf „SSGMSecurityGroupsUsers“ festgelegt) einen benutzerdefinierten Namen für eine Gruppe angeben, die alle Benutzer enthält, welche die Self-Service-Funktionen nutzen und neue Gruppen in Ihrem Verzeichnis erstellen dürfen.

## Zusätzliche Informationen

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0330_2016-->