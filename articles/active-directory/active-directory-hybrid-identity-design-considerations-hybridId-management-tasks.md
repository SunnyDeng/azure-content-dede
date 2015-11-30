<properties
	pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrid-Identität – Ermitteln der Aufgaben für die Hybrid-Identitätsverwaltung | Microsoft Azure"
	description="Mit der bedingten Zugriffssteuerung überprüft Azure Active Directory die besonderen Bedingungen, die Sie beim Authentifizieren des Benutzers und vor dem Gewähren des Zugriffs auf die Anwendung auswählen. Nachdem diese Bedingungen erfüllt sind, wird der Benutzer authentifiziert und erhält Zugriff auf die Anwendung."
	documentationCenter=""
	services="active-directory"
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="femila"/>

# Planen des Hybrid-Identitätslebenszyklus 

Die Identität ist eine der Grundlagen Ihrer Strategie für Enterprise Mobility und Anwendungszugriff. Ihre Identität ist der Schlüssel zur Erlangung des allgemeinen Zugriffs – unabhängig davon, ob Sie sich auf Ihrem mobilen Gerät oder an Ihrer SaaS-App anmelden. Auf der höchsten Ebene umfasst eine Lösung für die Identitätsverwaltung die Vereinheitlichung und Synchronisierung zwischen Ihren Identitätsrepositorys, was auch das Automatisieren und Zentralisieren der Bereitstellung von Ressourcen einschließt. Die Identitätslösung sollte eine zentralisierte Identität für lokale Speicherorte und die Cloud sein und auch eine Form des Identitätsverbunds nutzen, um eine zentralisierte Authentifizierung sowie das sichere Teilen und Zusammenarbeiten mit externen Benutzern und Unternehmen zu ermöglichen. Die Ressourcen reichen von Betriebssystemen und Anwendungen bis zu Personen, die Teil des Unternehmens sind oder über eine Verbindung dazu verfügen. Die Organisationsstruktur kann geändert werden, um die Bereitstellungsrichtlinien und -verfahren abzudecken.

Es ist auch wichtig, die Identitätslösung darauf auszurichten, dass sie eine Unterstützung für die Benutzer darstellt. Sie sollte Self-Service-Funktionen enthalten, damit die Produktivität gefördert wird. Ihre Identitätslösung ist robuster, wenn sie das einmalige Anmelden für Benutzer über alle Ressourcen hinweg ermöglicht, für die Zugriff benötigt wird. Administratoren aller Ebenen können standardisierte Verfahren zum Verwalten von Benutzeranmeldeinformationen verwenden. Einige Administrationsebenen können reduziert oder herausgenommen werden. Dies richtet sich nach der Breite der Lösung für die Bereitstellungsverwaltung. Außerdem können Sie Verwaltungsfunktionen manuell oder automatisch auch sicher auf verschiedene Unternehmen verteilen. Ein Domänenadministrator kann beispielsweise nur die Personen und Ressourcen in der jeweiligen Domäne bearbeiten. Dieser Benutzer kann Verwaltungs- und Bereitstellungsaufgaben übernehmen, aber er ist nicht berechtigt, Konfigurationsaufgaben auszuführen, z. B. die Erstellung von Workflows.


## Ermitteln der Aufgaben für die Hybrid-Identitätsverwaltung
Durch die Verteilung von administrativen Aufgaben in Ihrem Unternehmen werden die Genauigkeit und Effektivität der Verwaltung sowie die Workload-Balance eines Unternehmens verbessert. Im Folgenden sind die wichtigsten Punkte beschrieben, die ein robustes System für die Identitätsverwaltung definieren.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


Zum Definieren von Aufgaben der Hybrid-Identitätsverwaltung müssen Sie einige wesentliche Merkmale des Unternehmens verstehen, für das die Hybrid-Identität eingeführt werden soll. Es ist wichtig zu verstehen, welche Repositorys derzeit für Identitätsquellen verwendet werden. Wenn Sie diese Kernelemente kennen, verfügen Sie über die grundlegenden Anforderungen. Basierend auf diesen Informationen müssen Sie noch genauere Fragen stellen, um für Ihre Identitätslösung eine bessere Entwurfsentscheidung treffen zu können.

Stellen Sie beim Definieren dieser Anforderungen sicher, dass mindestens die folgenden Fragen beantwortet werden:

- Bereitstellungsoptionen: 
 - Unterstützt die Hybrid-Identitätslösung ein robustes System für die Kontozugriffsverwaltung und -bereitstellung?
 - Wie sollen Benutzer, Gruppen und Kennwörter verwaltet werden?
 - Ist die Verwaltung des Identitätslebenszyklus ausreichend reaktionsfähig? 
      - Wie lange dauert die Kontosperrung bei der Aktualisierung von Kennwörtern?
      
- Lizenzverwaltung:
 - Kann die Hybrid-Identitätslösung die Lizenzverwaltung durchführen?
     - Wenn ja: Welche Funktionen sind verfügbar?
- Kann die Lösung die gruppenbasierte Lizenzverwaltung durchführen? 
      - Wenn ja: Ist es möglich, hierfür eine Sicherheitsgruppe zuzuweisen? 
       - Wenn ja: Weist das Cloudverzeichnis automatisch allen Mitgliedern der Gruppe Lizenzen zu? 
        - Was passiert, wenn ein Benutzer der Gruppe nachträglich hinzugefügt oder aus der Gruppe entfernt wird? Wird automatisch eine Lizenz zugewiesen bzw. entfernt? 

- Integration in andere Drittanbieter-Identitätsanbieter:
- Kann diese Hybrid-Lösung in Drittanbieter-Identitätsanbieter integriert werden, um das einmalige Anmelden zu implementieren?
- Ist es möglich, alle unterschiedlichen Identitätsanbieter in einem zusammenhängenden Identitätssystem zu vereinen?
- Wenn ja: Welche sind dies, und welche Funktionen sind verfügbar?

## Synchronisierungsverwaltung
Eines der Ziele eines Identitäts-Managers besteht darin, alle Identitätsanbieter zusammenzufassen und in synchronisiertem Zustand zu halten. Sie halten die Daten anhand eines autoritativen Master-Identitätsanbieters synchronisiert. In einem Hybrid-Identitätsszenario mit einem synchronisierten Verwaltungsmodell verwalten Sie alle Benutzer- und Geräteidentitäten auf einem lokalen Server und synchronisieren die Konten und optional die Kennwörter mit der Cloud. Die Benutzer geben lokal dasselbe Kennwort wie in der Cloud ein, und bei der Anmeldung wird das Kennwort von der Identitätslösung überprüft. Für dieses Modell wird ein Verzeichnissynchronisierungstool verwendet.
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png) Stellen Sie sicher, dass die folgenden Fragen beantwortet werden, um die Synchronisierung Ihrer Hybrid-Identitätslösung richtig zu entwerfen: • Welche Synchronisierungslösungen sind für die Hybrid-Identitätslösung verfügbar? • Welche Funktionen für das einmalige Anmelden sind verfügbar? • Welche Optionen sind für den Identitätsverbund zwischen B2B und B2C verfügbar?

## Nächste Schritte
[Ermitteln der Strategie für die Einführung der Hybrid-Identitätsverwaltung](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## Weitere Informationen
[Überlegungen zum Entwurf – Übersicht](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=Nov15_HO4-->