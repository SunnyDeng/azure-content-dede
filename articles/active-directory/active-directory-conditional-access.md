<properties 
	pageTitle="Verwalten von Risiken mit bedingtem Zugriff" 
	description="In diesem Thema erfahren Sie, wie Sie den Zugriff von überall auf bestimmte Ressourcen mit bekannten Geräten ermöglichen, die die erforderlichen Richtlinien erfüllen, und den Zugriff für verlorene, gestohlene und nicht geeignete Geräte verhindern." 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.devlang="na" 
	ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>


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

- [Azure Active Directory-Geräteregistrierung – Übersicht](https://msdn.microsoft.com/library/azure/dn903763.aspx)
- [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](https://msdn.microsoft.com/library/azure/dn788908.aspx)
- [Geräterichtlinien für den bedingten Zugriff für Office 365-Dienste](https://msdn.microsoft.com/library/azure/dn903766.aspx)
- [Bedingter Zugriff unter Azure – Vorschau für SaaS-Apps](https://msdn.microsoft.com/library/azure/dn906877.aspx)


<!--Image references-->
[1]: ./media/active-directory-conditional-access/condaccoverviewvsdx1.png

<!---HONumber=58--> 