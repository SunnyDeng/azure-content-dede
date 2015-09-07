<properties
	pageTitle="Verwalten von Risiken mit bedingtem Zugriff"
	description="Ermöglichen Sie von überall innerhalb des Unternehmensnetzwerks den Zugriff auf bestimmte Ressourcen mit bekannten Geräten, die die erforderlichen Richtlinien erfüllen, und verhindern Sie den Zugriff für verlorene, gestohlene und nicht geeignete Geräte."
	services="active-directory, virtual-network"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="08/19/2015"
	ms.author="femila"/>


# Verwalten von Risiken mit bedingtem Zugriff

Die aktuellen Trends bei der Arbeitsweise von Mitarbeitern, die erreichbare Produktivität und die Arbeitsmittel ändern sich rasant. Mitarbeiter bringen ihre eigenen Geräte mit zur Arbeit. Auf diesen eigenen Geräten nutzen sie Apps für ihr persönliches digitales Leben. Außerdem haben sich die Benutzer an die Freiheit und die vielen Funktionen gewöhnt, die damit möglich sind. Sie möchten dieselben Anwendungen auch bei der Arbeit nutzen und so flexibel vorgehen können, wie dies auch in ihrem persönlichen digitalen Leben der Fall ist. Die heutigen Mitarbeiter von Unternehmen erwarten, dass sie an jedem Ort mit Geräten ihrer Wahl arbeiten können und nahtlos auf Geschäftsanwendungen zugreifen und die erforderlichen Verbindungen herstellen können.

Die Flexibilität, dass Benutzer überall und zu jeder Zeit auf die gewünschte Weise arbeiten können, ist mit einem erhöhten Risiko verbunden. Es kann vorkommen, dass diese Geräte gestohlen werden, verlegt werden oder verloren gehen. Auf vielen Smartphones und Tablets befindet sich eine große Menge an sensiblen und vertraulichen Kunden- und Unternehmensdaten. Dies ist der schmale Grat, auf dem sich IT-Architekten, Sicherheitsexperten und IT-Administratoren bewegen. Benutzer müssen auf allen von ihnen bevorzugten Geräten produktiv arbeiten können, und gleichzeitig müssen die Unternehmensdaten auf diesen Geräten ausreichend gesichert und geschützt sein.

Mit den vielen Funktionen für den bedingten Zugriff, die mit Azure Active Directory, Office 365 und Microsoft Intune möglich sind, können IT-Administratoren folgende Aufgaben ausführen:

- Ermöglichen des Zugriffs für Mitarbeiter von jedem Ort aus, ohne dass über das Internet Zugriff für andere Personen besteht
- Ermöglichen des Zugriffs auf bestimmte Ressourcen mit bekannten Geräten, die die entsprechenden Richtlinien erfüllen, von jedem Ort aus
- Verweigern des Zugriffs von Geräten, die verloren gegangen sind, gestohlen wurden oder die Richtlinien nicht erfüllen

![][1]

## Nächste Schritte

In den folgenden Themen werden die unterschiedlichen Mechanismen beschrieben, die zum Festlegen von Richtlinien für den bedingten Zugriff in Ihrer Organisation verfügbar sind.

- [Azure Active Directory-Geräteregistrierung – Übersicht](active-directory-conditional-access-device-registration-overview.md)
- [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](active-directory-conditional-access-on-premises-setup.md)
- [Geräterichtlinien für den bedingten Zugriff für Office 365-Dienste](active-directory-conditional-access-device-policies.md)
- [Bedingter Zugriff unter Azure – Vorschau für SaaS-Apps](active-directory-conditional-access-azuread-connected-apps.md)


<!--Image references-->
[1]: ./media/active-directory-conditional-access/condaccoverviewvsdx1.png

<!---HONumber=August15_HO9-->