<properties 
	pageTitle="Azure Multi-Factor Authentication – Funktionsweise" 
	description="Azure Multi-Factor Authentication hilft beim Schutz des Zugriffs auf Daten und Anwendungen und erfüllt gleichzeitig die Anforderungen von Benutzern an ein einfaches Anmeldeverfahren. Indem eine zweite Form der Authentifizierung verlangt wird, bietet das Verfahren zusätzliche Sicherheit und eine zuverlässige Authentifizierung über verschiedene einfache Überprüfungsoptionen." 
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
	ms.topic="article" 
	ms.date="08/24/2015" 
	ms.author="billmath"/>

#Funktionsweise von Azure Multi-Factor Authentication

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

##Verfügbare Methoden für Multi-Factor Authentication
Wenn ein Benutzer sich anmeldet, wird eine zusätzliche Bestätigung an den Benutzer gesendet. In der folgenden Liste sind die verschiedenen Methoden zusammengefasst, die für diese zweite Überprüfung verwendet werden können.

Überprüfungsmethode | Beschreibung 
------------- | ------------- |
Telefonanruf | Es wird ein automatisierter Anruf an das Smartphone des Benutzers ausgelöst. Der Benutzer bestätigt den Anmeldevorgang durch Drücken der #-Taste. Dadurch wird der Überprüfungsprozess abgeschlossen. Statt der #-Taste kann bei dieser Option wahlweise die Eingabe eines vorher festgelegten Codes verlangt werden.
Textnachricht | Eine Textnachricht mit einem sechsstelligen Code wird an das Smartphone des Benutzers gesendet. Dieser Code muss eingegeben werden, um die Überprüfung abzuschließen.
Benachrichtigung über mobile App | Es wird eine Überprüfungsanforderung an das Smartphone des Benutzers gesendet. Der Benutzer bestätigt die Anforderung in der mobilen App, um die Überprüfung abzuschließen. Diese Variante wird verwendet, wenn Benutzer das Senden einer App-Benachrichtigung als primäre Überprüfungsmethode ausgewählt haben. Wenn Benutzer eine Anforderung erhalten, obwohl sie derzeit keinen Anmeldevorgang durchführen, können sie diese als Betrugsversuch melden.
Überprüfungscode in der mobilen App | Es wird ein Überprüfungscode an die mobile App auf dem Smartphone des Benutzers gesendet. Diese Variante wird verwendet, wenn Benutzer das Senden eines Überprüfungscodes als primäre Überprüfungsmethode ausgewählt haben.


##Verfügbare Versionen von Azure Multi-Factor Authentication
Azure Multi-Factor Authentication ist in drei verschiedenen Versionen verfügbar. In der folgenden Tabelle werden die Versionen ausführlicher beschrieben.

Version | Beschreibung 
------------- | ------------- |
Multi-Factor Authentication für Office 365 | Diese Version arbeitet ausschließlich mit Office 365-Anwendungen und wird über das Office 365-Portal verwaltet. Administratoren haben somit die Möglichkeit, ihre Office 365-Ressourcen mit Multi-Factor Authentication zusätzlich zu sichern. Diese Version wird zusammen mit einem Office 365-Abonnement bereitgestellt.
Multi-Factor Authentication für Azure-Administratoren | Dieselbe Teilmenge von Multi-Factor Authentication-Funktionen für Office 365 wird allen Azure-Administratoren kostenlos zur Verfügung gestellt. Jedes Administratorkonto eines Azure-Abonnements kann jetzt durch das Aktivieren der Multi-Factor Authentication-Funktion zusätzlich geschützt werden. Somit kann ein Administrator, der auf das Azure-Portal zugreifen möchte, um einen virtuellen Computer oder eine Website zu erstellen bzw. Speicher, Mobile Services oder einen anderen Azure-Dienst zu verwalten, Multi-Factor Authentication zu seinem Administratorkonto hinzufügen.
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication bietet von allen Versionen den größten Funktionsumfang. Über das Azure-Verwaltungsportal sind zusätzliche Konfigurationsoptionen und erweiterte Funktionen für die Berichterstellung verfügbar. Zudem stellt diese Version Unterstützung für verschiedene lokale und Cloud-Anwendungen bereit. Azure Multi-Factor Authentication ist im Lieferumfang von Azure Active Directory Premium enthalten.

##Funktionsvergleich der Versionen
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


##Beziehen von Azure Multi-Factor Authentication

Azure Multi-Factor Authentication ist im Lieferumfang von Azure Active Directory Premium und Enterprise Mobility Suite enthalten. Wenn Sie über eines dieser Produkte verfügen, können Sie Azure Multi-Factor Authentication bereits jetzt verwenden.

Wenn Sie als Office 365-Benutzer oder als Azure-Abonnent die zusätzlichen Funktionen von Azure Multi-Factor Authentication nutzen möchten, lesen Sie die folgenden Informationen.

Wenn Sie über keines der genannten Produkte verfügen, benötigen Sie zuerst ein Azure-Abonnement oder ein [Azure-Testabonnement](http://azure.microsoft.com/pricing/free-trial/), um Azure Multi-Factor Authentication verwenden zu können.

Für die Verwendung von Azure Multi-Factor Authentication stehen zwei Abrechnungsoptionen zur Verfügung:

- **Pro Benutzer**. Diese Option ist für Unternehmen geeignet, die Multi-Factor Authentication für eine feste Anzahl von Mitarbeitern aktivieren möchten, die regelmäßig eine Authentifizierung benötigen.
- **Pro Authentifizierung**. Diese Option ist für Unternehmen geeignet, die Multi-Factor Authentication für eine große Gruppe externer Benutzer aktivieren möchten, die seltener oder unregelmäßig eine Authentifizierung benötigen.

Ausführliche Preisinformationen finden Sie unter [Preise für Azure MFA](http://azure.microsoft.com/pricing/details/multi-factor-authentication/).

Wählen Sie das Modell, das am besten für Ihre Organisation geeignet ist. Lesen Sie anschließend die Informationen unter [Erste Schritte](multi-factor-authentication-get-started.md).



 

<!---HONumber=Oct15_HO3-->