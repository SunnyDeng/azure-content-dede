<properties
	pageTitle="Was ist Azure Multi-Factor Authentication? | Microsoft Azure"
	description="In diesem Thema wird erläutert, was Multi-Factor Authentication (MFA) ist und warum diese Art der Authentifizierung verwendet wird. Außerdem wird der Client für die Multi-Factor Authentication vorgestellt, und die verschiedenen verfügbaren Methoden und Versionen werden erklärt. Azure Multi-Factor Authentication ist eine Methode zum Überprüfen der Identität, für die mehr als nur ein Benutzername und ein Kennwort erforderlich ist. Sie bietet eine zusätzliche Sicherheitsebene für Benutzeranmeldungen und -transaktionen."
	keywords="Einführung in die MFA, MFA-Übersicht, Was ist MFA"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="01/25/2016"
	ms.author="billmath"/>

# Was ist Azure Multi-Factor Authentication?
Multi-Factor Authentication (MFA) ist eine Authentifizierungsmethode, die die Verwendung von mehr als einer Verifizierungsmethode erfordert und eine wichtige zweite Sicherheitsebene für Benutzeranmeldungen und Transaktionen darstellt. Dies funktioniert durch das Anfordern von zwei oder mehr der folgenden Verifizierungsmethoden:

- Etwas, das Sie wissen (normalerweise ein Kennwort)
- Etwas, das Sie haben (ein vertrautes Gerät, das nicht leicht dupliziert werden kann, wie ein Telefon)
- Etwas, das Sie sind (biometrisch)

<center>![Benutzername und Kennwort](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Zertifikate](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smartphone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smartcard](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtuelle Smartcard](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Benutzername und Kennwort](./media/multi-factor-authentication/cert.png)</center>



Azure Multi-Factor Authentication ist eine Methode zum Überprüfen der Identität, für die mehr als nur ein Benutzername und ein Kennwort erforderlich ist. Sie bietet eine zweite Sicherheitsebene für Benutzeranmeldungen und -transaktionen.

Azure Multi-Factor Authentication hilft beim Schutz des Zugriffs auf Daten und Anwendungen und erfüllt gleichzeitig die Anforderungen von Benutzern an ein einfaches Anmeldeverfahren. Sie bietet leistungsfähige Authentifizierung mittels einiger einfacher Überprüfungsoptionen – Telefonanruf, SMS oder per Benachrichtigung bzw. Überprüfungscode in einer mobilen Anwendung sowie OATH-Tokens von Drittanbietern.

Eine Übersicht über die Funktionsweise von Azure Multi-Factor Authentication finden Sie im folgenden Video.


<center>[AZURE.VIDEO multi-factor-authentication-overview]</center>

##Warum sollten Sie Azure Multi-Factor Authentication verwenden?

Heute sind mehr Menschen immer häufiger verbunden als je zuvor. Mit Smartphones, Tablet-PCs, Laptops und PCs verfügen Benutzer über mehrere unterschiedliche Verbindungsoptionen, die es ermöglichen, ständig verbunden zu bleiben. Benutzer können überall auf ihre Konten und Anwendungen zugreifen, was zu einer produktiveren Arbeitsweise und besserem Kundenservice führt.

Azure Multi-Factor Authentication ist eine benutzerfreundliche, skalierbare und zuverlässige Lösung, die eine zweite Methode zur Authentifizierung bereitstellt, sodass die Benutzer immer geschützt sind.

![Benutzerfreundlich](./media/multi-factor-authentication/simple.png)| ![Skalierbar](./media/multi-factor-authentication/scalable.png)| ![Immer geschützt](./media/multi-factor-authentication/protected.png)|![Zuverlässig](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Benutzerfreundlich**|**Skalierbar**|**Immer geschützt**|**Zuverlässig**

- **Benutzerfreundlich**: Azure Multi-Factor Authentication ist einfach einzurichten und zu verwenden. Der zusätzliche Schutz, der mit Azure Multi-Factor Authentication einhergeht, ermöglicht Benutzern, ihre eigenen Geräte zu verwenden und zu verwalten, und in vielen Fällen ist die Einrichtung mit wenigen Klicks erledigt.
- **Skalierbar**: Azure Multi-Factor Authentication nutzt Cloudfunktionen und integriert sich nahtlos in Ihr lokales Active Directory und benutzerdefinierte Anwendungen. Dieser Schutz wirkt sich sogar auf Ihre umfangreichen unternehmenswichtigen Szenarios aus.
- **Immer geschützt**: Azure Multi-Factor Authentication bietet sichere Authentifizierung unter Verwendung der strengsten Branchenstandards.
- **Zuverlässig**: Wir garantieren 99,9 % Verfügbarkeit für Azure Multi-Factor Authentication. Der Dienst gilt als nicht verfügbar, wenn keine Authentifizierungsanforderungen für die Multi-Factor Authentication empfangen oder verarbeitet werden können.  

Weitere Informationen zu den Verwendungsgründen für Azure Multi-Factor Authentication finden Sie im folgenden Video.

<center>[AZURE.VIDEO windows-azure-multi-factor-authentication]</center>


## Funktionsweise von Azure Multi-Factor Authentication

Die Sicherheit der Multi-Factor Authentication liegt im Ebenenansatz. Die Faktoren der mehrfachen Authentifizierung zu überwinden stellt eine große Herausforderung für Angreifer dar. Selbst wenn ein Angreifer das Kennwort des Benutzers herausfinden kann, ist dies nutzlos, wenn er das vertraute Gerät nicht besitzt. Auch wenn der Benutzer das Gerät verliert, kann es der Finder des Geräts nicht verwenden, wenn er nicht gleichzeitig auch das Kennwort des Benutzers kennt.

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure Multi-Factor Authentication hilft beim Schutz des Zugriffs auf Daten und Anwendungen und erfüllt gleichzeitig die Anforderungen von Benutzern an ein einfaches Anmeldeverfahren. Indem eine zweite Form der Authentifizierung verlangt wird, bietet das Verfahren zusätzliche Sicherheit und eine zuverlässige Authentifizierung über verschiedene einfache Überprüfungsoptionen:

- Telefonanruf
- Textnachricht
- Benachrichtigung über mobile App – Benutzer können ihre bevorzugte Methode selbst wählen
- Überprüfungscode in der mobilen App
- Drittanbieter-OATH-Tokens

Weitere Informationen zur Funktionsweise erhalten Sie im folgenden Video.

[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## Verfügbare Methoden für Multi-Factor Authentication
Wenn ein Benutzer sich anmeldet, wird eine zusätzliche Bestätigung an den Benutzer gesendet. In der folgenden Liste sind die verschiedenen Methoden zusammengefasst, die für diese zweite Überprüfung verwendet werden können.

Überprüfungsmethode | Beschreibung
------------- | ------------- |
Telefonanruf | Es wird ein automatisierter Anruf an das Smartphone des Benutzers ausgelöst. Der Benutzer bestätigt den Anmeldevorgang durch Drücken der #-Taste. Dadurch wird der Überprüfungsprozess abgeschlossen. Statt der #-Taste kann bei dieser Option wahlweise die Eingabe eines vorher festgelegten Codes verlangt werden.
Textnachricht | Eine Textnachricht mit einem sechsstelligen Code wird an das Smartphone des Benutzers gesendet. Dieser Code muss eingegeben werden, um die Überprüfung abzuschließen.
Benachrichtigung über mobile App | Es wird eine Überprüfungsanforderung an das Smartphone des Benutzers gesendet. Der Benutzer bestätigt die Anforderung in der mobilen App, um die Überprüfung abzuschließen. Diese Variante wird verwendet, wenn Benutzer das Senden einer App-Benachrichtigung als primäre Überprüfungsmethode ausgewählt haben. Wenn Benutzer eine Anforderung erhalten, obwohl sie derzeit keinen Anmeldevorgang durchführen, können sie diese als Betrugsversuch melden.
Überprüfungscode in der mobilen App | Es wird ein Überprüfungscode an die mobile App auf dem Smartphone des Benutzers gesendet. Diese Variante wird verwendet, wenn Benutzer das Senden eines Überprüfungscodes als primäre Überprüfungsmethode ausgewählt haben.


## Verfügbare Versionen von Azure Multi-Factor Authentication
Azure Multi-Factor Authentication ist in drei verschiedenen Versionen verfügbar. In der folgenden Tabelle werden die Versionen ausführlicher beschrieben.

Version | Beschreibung
------------- | ------------- |
Multi-Factor Authentication für Office 365 | Diese Version arbeitet ausschließlich mit Office 365-Anwendungen und wird über das Office 365-Portal verwaltet. Administratoren haben somit die Möglichkeit, ihre Office 365-Ressourcen mit Multi-Factor Authentication zusätzlich zu sichern. Diese Version wird zusammen mit einem Office 365-Abonnement bereitgestellt.
Multi-Factor Authentication für Azure-Administratoren | Dieselbe Teilmenge von Multi-Factor Authentication-Funktionen für Office 365 wird allen Azure-Administratoren kostenlos zur Verfügung gestellt. Jedes Administratorkonto eines Azure-Abonnements kann jetzt durch das Aktivieren der Multi-Factor Authentication-Funktion zusätzlich geschützt werden. Somit kann ein Administrator, der auf das Azure-Portal zugreifen möchte, um einen virtuellen Computer oder eine Website zu erstellen bzw. Speicher, Mobile Services oder einen anderen Azure-Dienst zu verwalten, Multi-Factor Authentication zu seinem Administratorkonto hinzufügen.
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication bietet von allen Versionen den größten Funktionsumfang. Über das Azure-Verwaltungsportal sind zusätzliche Konfigurationsoptionen und erweiterte Funktionen für die Berichterstellung verfügbar. Zudem stellt diese Version Unterstützung für verschiedene lokale und Cloud-Anwendungen bereit. Azure Multi-Factor Authentication ist im Lieferumfang von Azure Active Directory Premium enthalten.

## Funktionsvergleich der Versionen
In der folgenden Tabelle werden die Funktionen aufgeführt, die in den verschiedenen Versionen von Azure Multi-Factor Authentication verfügbar sind.


Funktion | Multi-Factor Authentication für Office 365 (in Office 365-SKUs enthalten)|Multi-Factor Authentication für Azure-Administratoren (enthalten in Azure-Abonnement) | Azure Multi-Factor Authentication (enthalten in Azure AD Premium und Enterprise Mobility Suite)
------------- | :-------------: |:-------------: |:-------------: |
Administratoren können Konten mit MFA schützen.| * | * (nur für Azure-Administratorkonten verfügbar)|*
Mobile App als zweiter Faktor|* | * | *
Telefonanruf als zweiter Faktor|* | * | *
SMS als zweiter Faktor|* | * | *
App-Kennwörter für Clients, die MFA nicht unterstützen|* | * | *
Administrative Kontrolle über Authentifizierungsmethoden| | | *
PIN-Modus| | | *
Betrugswarnung| | | *
MFA-Berichte| | | *
Einmalumgehung| | | *
Benutzerdefinierte Begrüßungen für Telefonanrufe| | | *
Benutzerdefinierte Anrufer-ID für Telefonanrufe| | | *
Bestätigung von Ereignissen| | | *
Vertrauenswürdige IP-Adressen| | | *
Aussetzen der Multi-Factor Authentication für gespeicherte Geräte (öffentliche Vorschau)| | | *
MFA-SDK| | | *
MFA für lokale Anwendungen mit MFA-Server| | | *


## Beziehen von Azure Multi-Factor Authentication

Wenn Sie nicht nur die für Office 365-Benutzer und Azure-Administratoren verfügbaren Funktionen, sondern den vollen Funktionsumfang von Azure Multi-Factor Authentication nutzen möchten, haben Sie mehrere Optionen:

1.	Erwerben Sie Multi-Factor Authentication-Lizenzen, und weisen Sie diese Ihren Benutzern zu.
2.	Kaufen Sie Lizenzen, in denen Multi-Factor Authentication enthalten ist, z. B. Azure Active Directory Premium, Enterprise Mobility Suite oder Enterprise Cloud Suite, und weisen Sie sie Ihren Benutzern zu.
3.	Erstellen Sie einen Azure Multi-Factor Authentication-Anbieter in einem Azure-Abonnement. Wenn Sie noch über kein Azure-Abonnement verfügen, können Sie sich für ein kostenloses Testabonnement registrieren. Testabonnements müssen vor Ablauf in reguläre Abonnements umgewandelt werden.

Wenn Sie einen Azure Multi-Factor Authentication-Anbieter verwenden, sind zwei Nutzungsmodelle verfügbar, die über Ihr Azure-Abonnement abgerechnet werden:


- **Pro Benutzer**. Diese Option ist für Unternehmen geeignet, die Multi-Factor Authentication für eine feste Anzahl von Mitarbeitern aktivieren möchten, die regelmäßig eine Authentifizierung benötigen.
- **Pro Authentifizierung**. Diese Option ist für Unternehmen geeignet, die Multi-Factor Authentication für eine große Gruppe externer Benutzer aktivieren möchten, die seltener oder unregelmäßig eine Authentifizierung benötigen.

Ausführliche Preisinformationen finden Sie unter [Preise für Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

Wählen Sie das Pro-Arbeitsplatz-Modell oder ein verbrauchsorientiertes Modell, das am besten zu Ihrer Organisation passt. Lesen Sie anschließend die Informationen unter [Erste Schritte](multi-factor-authentication-get-started.md).

## Auswählen der richtigen mehrstufigen Sicherheitslösung

Da es mehrere Arten von Azure Multi-Factor Authentication gibt, müssen Sie einige Fragen klären, um herauszufinden, welche Version für Sie die richtige ist. Fragen:

-	[Was möchte ich sichern?](#what-am-i-trying-to-secure)
-	[Wo befinden sich die Benutzer?](#where-are-the-users-located)

In den folgenden Abschnitten erhalten Sie Informationen, die Ihnen helfen, die Antworten auf diese Fragen zu geben.

### Was möchte ich sichern?

Um die richtige Multi-Factor Authentication-Lösung zu ermitteln, müssen Sie zunächst die Frage beantworten, was Sie über die zweite Methode zur Authentifizierung sichern möchten. Handelt es sich um eine Anwendung in Azure? Oder ist Sie es z. B. ein RAS-System? Die Antwort auf die Frage, wo Multi-Factor Authentication aktiviert werden muss, richtet sich danach, was Sie sichern möchten.



Was möchte ich sichern?| Multi-Factor Authentication in der Cloud|Multi-Factor Authentication-Server
------------- | :-------------: | :-------------: |
Erstanbieter-Microsoft-Apps|* |* |
SaaS-Apps im Anwendungskatalog|* |* |
Über den Azure AD-App-Proxy veröffentlichte IIS-Anwendungen|* |* |
Nicht über den Azure AD-App-Proxy veröffentlichte IIS-Anwendungen | |* |
Remotezugriff, z. B. VPN, RDG| |* |



### Wo befinden sich die Benutzer?

Außerdem hängt die Entscheidung für die richtige Lösung – Multi-Factor Authentication in der Cloud oder lokal über den MFA-Server – davon ab, wo sich Ihre Benutzer befinden.



Benutzerstandort| Lösung
------------- | :------------- |
Azure Active Directory| Multi-Factor Authentication in der Cloud|
Azure AD und lokales AD über einen Verbund mit AD FS| Die verfügbaren Optionen sind MFA in der Cloud und MFA-Server.
Azure AD und lokales AD mit DirSync, Azure AD Sync, Azure AD Connect ohne Kennwortsynchronisierung|Die verfügbaren Optionen sind MFA in der Cloud und MFA-Server.
Azure AD und lokales AD mit DirSync, Azure AD Sync, Azure AD Connect mit Kennwortsynchronisierung|Multi-Factor Authentication in der Cloud
Lokales Active Directory|Multi-Factor Authentication-Server

In der folgenden Tabelle finden Sie einen Vergleich der Funktionen von Multi-Factor Authentication in der Cloud und dem Multi-Factor Authentication-Server.

 | Multi-Factor Authentication in der Cloud | Multi-Factor Authentication-Server
------------- | :-------------: | :-------------: |
Benachrichtigung in der mobilen App als zweite Stufe | ● | ● |
Bestätigungscode in der mobilen App als zweite Stufe | ● | ●
Telefonanruf als zweite Stufe | ● | ●
Unidirektionale SMS als zweite Stufe | ● | ●
Bidirektionale SMS als zweite Stufe | | ●
Hardwaretoken als zweite Stufe | | ●
App-Kennwörter für Clients, die MFA nicht unterstützen | ● |  
Administrative Kontrolle über Authentifizierungsmethoden | | ●
PIN-Modus | | ●
Betrugswarnung | ● | ●
MFA-Berichte | ● | ●
Einmalumgehung | ● | ●
Benutzerdefinierte Begrüßungen für Telefonanrufe | ● | ●
Benutzerdefinierte Anrufer-ID für Telefonanrufe | ● | ●
Vertrauenswürdige IP-Adressen | ● | ●
Aussetzen der Multi-Factor Authentication für gespeicherte Geräte (öffentliche Vorschau) | ● |  
Bedingter Zugriff | ● | ●
Cache | ● | ●

Sie haben nun ermittelt, ob Sie Multi-Factor Authentication in der Cloud oder den MFA-Server lokal verwenden möchten. Jetzt können Sie mit dem Einrichten und Verwenden von Azure Multi-Factor Authentication beginnen. **Wählen Sie das Symbol aus, das Ihrem Szenario entspricht.**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

<!---HONumber=AcomDC_0211_2016-->