<properties
    pageTitle="Automatisierte SaaS-Benutzerbereitstellung in Azure AD | Microsoft Azure"
    description="Einführung in die Verwendung von Azure AD für die automatisierte Bereitstellung, Bereitstellungsaufhebung und fortlaufende Aktualisierung von Benutzerkonten für verschiedene SaaS-Drittanbieteranwendungen."
    services="active-directory"
    documentationCenter=""
    authors="liviodlc"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/02/2016"
    ms.author="liviodlc"/>

#Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory

##Was ist eine automatisierte Benutzerbereitstellung für SaaS-Anwendungen?

Azure Active Directory (Azure AD) ermöglicht Ihnen das automatisierte Erstellen, Warten und Entfernen von Benutzeridentitäten in Cloudanwendungen (SaaS), wie z. B. Dropbox, Salesforce, ServiceNow und weiteren.

**Nachstehend finden Sie einige Beispiele für Aufgaben, die Sie mit diesem Feature ausführen können:**

- Automatisches Erstellen neuer Konten in den richtigen SaaS-Apps für neue Benutzer, wenn diese Ihr Team verstärken.
- Automatisches Deaktivieren von Konten aus SaaS-Apps, wenn Benutzer das Team verlassen.
- Sicherstellen, dass die Identitäten in Ihren SaaS-Apps basierend auf Änderungen im Verzeichnis auf dem aktuellen Stand gehalten werden.
- Bereitstellen von Nicht-Benutzerobjekten, z. B. Gruppen, für SaaS-Apps, die diese unterstützen.

**Die automatisierte Benutzerbereitstellung bietet außerdem folgende Funktionalität:**

- Möglichkeit zum Abgleich vorhandener Identitäten zwischen Azure AD und SaaS-Apps.
- Optionen zur Anpassung von Azure AD an die aktuellen Konfigurationen der SaaS-Apps, die Ihre Organisation zurzeit ausführt.
- Optionale E-Mail-Warnungen zu Bereitstellungsfehlern.
- Berichterstellung und Aktivitätsprotokolle unterstützen Sie bei der Überwachung und Problembehandlung.

##Warum sollte eine automatisierte Bereitstellung eingesetzt werden?

Nachfolgend werden einige Gründe aufgeführt, die für die Verwendung dieser Funktion sprechen:

- Vermeiden von Kosten, Ineffizienz und Fehlern, die in Zusammenhang mit manuellen Bereitstellungsvorgängen auftreten.
- Sicherstellen, dass Ihre Organisation Benutzeridentitäten sofort aus wichtigen SaaS-Apps entfernen kann, wenn Benutzer die Organisation verlassen.
- Einfacher Massenimport von Benutzern in eine bestimmte SaaS-Anwendung.
- Mit dieser Bereitstellungslösung werden dieselben App-Zugriffsrichtlinien angewendet wie diejenigen, die Sie für die einmalige Azure AD-Anmeldung (SSO) definiert haben.

##Häufig gestellte Fragen

**Wie häufig schreibt Azure AD Verzeichnisänderungen in die SaaS-Apps?**

Azure AD prüft alle fünf bis zehn Minuten auf Änderungen. Wenn die SaaS-App mehrere Fehler zurückgibt (beispielsweise bei ungültigen Administratoranmeldeinformationen), verringert Azure AD die Häufigkeit auf eine Abfrage pro Tag, bis die Fehler behoben wurden.

**Wie lange dauert die Bereitstellung meiner Benutzer?**

Inkrementelle Änderungen werden nahezu sofort angewendet, wenn Sie jedoch versuchen, den Großteil Ihres Verzeichnisses bereitzustellen, richtet sich die benötigte Zeit nach der Anzahl von Benutzern und Gruppen, über die Sie verfügen. Kleine Verzeichnisse erfordern nur einige wenige Minuten, Verzeichnisse mittlerer Größe erfordern mehrere Minuten, und für sehr große Verzeichnisse kann der Vorgang mehrere Stunden in Anspruch nehmen.

**Wie kann ich den Fortschritt des aktuellen Bereitstellungsauftrags verfolgen?**

Sie können den Bericht "Kontobereitstellung" im Abschnitt "Berichte" Ihres Verzeichnisses prüfen. Eine weitere Option besteht darin, die Registerkarte "Dashboard" für die SaaS-Anwendung anzuzeigen, für die Sie die Bereitstellung durchführen. Sie finden die entsprechenden Informationen im Abschnitt "Integrationsstatus" im unteren Bereich der Seite.

**Woher weiß ich, dass Benutzer nicht ordnungsgemäß bereitgestellt wurden?**

Auf der letzten Seite des Assistenten für die Bereitstellungskonfiguration können Sie eine Option aktivieren, um E-Mail-Benachrichtigungen zu Bereitstellungsfehlern zu erhalten. Sie können auch den Bericht zu Bereitstellungsfehlern prüfen um zu sehen, bei welchen Benutzern es zu Fehlern bei der Bereitstellung gekommen ist und warum.

**Kann Azure AD Änderungen von der SaaS-App zurück in das Verzeichnis schreiben?**

Für diese meisten SaaS-Apps erfolgt die Bereitstellung ausschließlich ausgehend. Dies bedeutet, dass Benutzer vom Verzeichnis in die Anwendung geschrieben werden, Änderungen der Anwendung können jedoch nicht zurück in das Verzeichnis geschrieben werden. Für [Workday](https://msdn.microsoft.com/library/azure/dn762434.aspx) erfolgt die Bereitstellung jedoch ausschließlich eingehend. Dies bedeutet, dass Benutzer von Workday in das Verzeichnis importiert werden, während Änderungen im Verzeichnis nicht zurück an Workday geschrieben werden.

**Wie kann ich Feedback an das Entwicklerteam senden?**

Kontaktieren Sie uns über das [Azure Active Directory-Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/).

##Wie funktioniert die automatisierte Bereitstellung?

Azure AD stellt Benutzer für SaaS-Apps bereit, indem eine Verbindung mit Bereitstellungsendpunkten hergestellt wird, die vom jeweiligen Anwendungsanbieter zur Verfügung gestellt werden. Diese Endpunkte ermöglichen Azure AD das programmgesteuerte Erstellen, Aktualisieren und Entfernen von Benutzern. Nachstehend finden Sie einen kurzen Überblick über die verschiedenen Schritte, die Azure AD bei der automatisierten Bereitstellung ausführt.

1. Wenn Sie die Bereitstellung für eine Anwendung das erste Mal aktivieren, werden die folgenden Aktionen ausgeführt:
 - Azure AD versucht, vorhandene Benutzer in der SaaS-App mit den entsprechenden Identitäten im Verzeichnis abzugleichen. Wenn ein Benutzer dem Abgleich entspricht, wird ihm *nicht* automatisch das einmalige Anmelden ermöglicht. Damit ein Benutzer auf die Anwendung zugreifen kann, muss er explizit in Azure AD der App zugewiesen werden, entweder direkt oder über eine Gruppenmitgliedschaft.
 - Wenn Sie bereits angegeben haben, welche Benutzer der Anwendung zugewiesen werden sollen, aber Azure AD keine vorhandenen Konten für diese Benutzer findet, stellt Azure AD für die Benutzer neue Konten in der Anwendung bereit.
2. Nachdem die anfängliche Synchronisierung wie oben beschrieben abgeschlossen wurde, prüft Azure AD alle 10 Minuten auf die folgenden Änderungen:
 - Wenn der Anwendung neue Benutzer zugewiesen werden (entweder direkt oder über eine Gruppenmitgliedschaft), wird für diese ein neues Konto in der SaaS-App bereitgestellt.
 - Wenn der Zugriff für einen Benutzer entfernt wurde, wird das zugehörige Konto in der SaaS-App als deaktiviert gekennzeichnet (Benutzer werden niemals vollständig gelöscht, um Sie im Falle einer Fehlkonfiguration vor einem Datenverlust zu schützen).
 - Wenn ein Benutzer vor Kurzem der Anwendung zugewiesen wurde und bereits über ein Konto in der SaaS-App verfügt, wird das Konto als aktiviert gekennzeichnet, und bestimmte Benutzereigenschaften werden möglicherweise aktualisiert, wenn sie gegenüber dem Verzeichnis veraltet sind.
 - Änderungen an Benutzerinformationen (beispielsweise Telefonnummer, Bürostandort usw.) im Verzeichnis werden auch in der SaaS-Anwendung entsprechend aktualisiert.

Weitere Informationen dazu, wie Attribute zwischen Azure AD und Ihrer SaaS-App zugeordnet werden, finden Sie im Artikel [Anpassen von Attributzuordnungen](active-directory-saas-customizing-attribute-mappings.md).

##Liste der Apps, die eine automatisierte Benutzerbereitstellung unterstützen

Klicken Sie auf die App, um ein Lernprogramm zum Konfigurieren der automatisierten Bereitstellung für diese App anzuzeigen:

- [Box](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Concur](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Dropbox für Unternehmen](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Salesforce](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Salesforce Sandbox](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [Workday](http://go.microsoft.com/fwlink/?LinkId=690250) (eingehende Bereitstellung)

Damit eine Anwendung die automatisierte Benutzerbereitstellung unterstützt, müssen zunächst die erforderlichen Endpunkte bereitgestellt werden, die es externen Programmen erlauben, die Erstellung, Wartung und Entfernung von Benutzern zu automatisieren. Daher sind nicht alle SaaS-Apps mit diesem Feature kompatibel. Für Apps, die diese Unterstützung bieten, kann das Azure AD-Entwicklerteam anschließend einen Bereitstellungsconnector erstellen. Dieser Tätigkeit wird in Abhängigkeit von den Anforderungen bestehender und potenzieller Kunden Priorität eingeräumt.

Wenn Sie das Azure AD-Entwicklungsteam kontaktieren möchten, um Bereitstellungsunterstützung für zusätzliche Anwendungen anzufordern, senden Sie uns eine Nachricht über das [Azure Active Directory-Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/).

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!---HONumber=AcomDC_0204_2016-->