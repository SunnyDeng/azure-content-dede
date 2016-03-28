<properties
	pageTitle="Azure Active Directory Identity Protection Abläufe| Microsoft Azure"
	description="Bietet eine Übersicht über die Anmeldungen, wenn Identity Protection einen Benutzer migriert oder bereinigt hat, oder wenn die mehrstufige Authentifizierung durch eine Richtlinie gefordert wird."
	services="active-directory"
	keywords="Azure Active Directory Identity Protection, Cloud App Discovery, Verwalten von Anwendungen, Sicherheit, Risiko, Risikostufe, Sicherheitsrisiko, Sicherheitsrichtlinie"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection- Abläufe

Mit Azure Active Directory Identity Protection können Sie:

- Benutzern zwingen sich für die mehrstufige Authentifizierung anzumelden

- riskante Anmeldungen und kompromittierte Benutzer behandeln

Die Reaktion des Systems auf diese Probleme hat Auswirkungen auf die Anmeldung eines Benutzers, da die direkt Anmeldung bei der lediglich der Benutzername und das Kennwort eingegeben werden, nicht mehr möglich sein wird. Zusätzliche Schritte sind erforderlich, um einen Benutzer wieder sicher anzumelden.

In diesem Thema erhalten Sie eine Übersicht über die Anmeldungen eines Benutzers für alle Fälle die auftreten können.

**Multi-Factor Authentication**

- Registrierung für Multi-Factor Authentication



**Anmeldung gefährdet**

- Wiederherstellung risikobehafteter Anmeldungen

- Risikobehaftete Anmeldung blockiert

- Multi-Factor Authentication-Registrierung während einer risikobehafteten Anmeldung
 

**Benutzer gefährdet**

- Wiederherstellung kompromittierter Konten

- Kompromittiertes Konto blockiert




## Registrierung für Multi-Factor Authentication

Die Benutzerfreundlichkeit ist für den Wiederherstellungsablauf für kompromittierte Konten und den Ablauf für risikobehaftete Anmeldungen jeweils am höchsten, wenn der Benutzer die Wiederherstellung selbst durchführen kann. Wenn Benutzer für die mehrstufige Authentifizierung registriert sind, sind ihren Konten bereits jeweils eine Telefonnummer zugeordnet, die für die erfolgreiche Beantwortung von Sicherheitsabfragen verwendet werden kann. Es ist keine Beteiligung des Helpdesks oder eines Administrators erforderlich, um für ein kompromittiertes Konto die Wiederherstellung durchzuführen. Daher ist es dringend zu empfehlen, dass Sie für Ihre Benutzer die Registrierung für Multi-Factor Authentication vornehmen.

Administratoren haben folgende Möglichkeiten:

- Festlegen einer Richtlinie, die es für Benutzer erforderlich macht, ihre Konten für die zusätzliche Sicherheitsüberprüfung einzurichten 
- Zulassen des Überspringens der Multi-Factor Authentication-Registrierung für bis zu 30 Tage, falls Benutzern vor dem Registrieren eine Schonfrist eingeräumt werden soll

**Die Registrierung für die mehrstufige Authentifizierung umfasst drei Schritte:**

1. Im ersten Schritt erhält der Benutzer eine Benachrichtigung über die Anforderung das Konto für die mehrstufige Authentifizierung einzurichten. <br><br> ![Korrektur](./media/active-directory-identityprotection-flows/140.png "Korrektur") <br>


2. Um mehrstufige Authentifizierung einzurichten, müssen Sie dem System mitteilen, wie Sie kontaktiert werden möchten. <br><br> ![Korrektur](./media/active-directory-identityprotection-flows/141.png "Korrektur") <br>
 
3. Das System sendet Ihnen eine Abfrage, und Sie müssen reagieren. <br><br> ![Korrektur](./media/active-directory-identityprotection-flows/142.png "Korrektur") <br>

 



## Wiederherstellung risikobehafteter Anmeldungen

Wenn ein Administrator eine Richtlinie für Anmelderisiken konfiguriert hat, werden die betroffenen Benutzer benachrichtigt, sobald sie versuchen, sich anzumelden.

**Der Ablauf für risikobehaftete Anmeldungen besteht aus zwei Schritten:**

1. Der Benutzer wird informiert, dass im Zusammenhang mit seiner Anmeldung eine Unregelmäßigkeit erkannt wurde, z. B. das Anmelden von einem neuen Standort, mit einem neuen Gerät oder mit einer neuen App. <br><br> ![Korrektur](./media/active-directory-identityprotection-flows/120.png "Korrektur") <br>

2. Der Benutzer muss seine Identität nachweisen, indem er eine Sicherheitsabfrage richtig beantwortet. Wenn der Benutzer für die Multi-Factor Authentication registriert ist, muss er mit einem Sicherheitscode einen Roundtripvorgang für seine Telefonnummer durchführen. Da es sich nur um eine risikobehaftete Anmeldung und nicht um ein kompromittiertes Konto handelt, muss der Benutzer sein Kennwort bei diesem Ablauf nicht ändern. <br><br> ![Korrektur](./media/active-directory-identityprotection-flows/121.png "Korrektur") <br>



 
## Risikobehaftete Anmeldung blockiert
Administratoren können auch eine Richtlinie für Anmelderisiken festlegen, um Benutzer bei der Anmeldung je nach Risikostufe zu blockieren. Um die Blockierung aufzuheben, müssen Endbenutzer sich an einen Administrator oder den Helpdesk wenden, oder sie können versuchen, sich von einem vertrauten Standort oder mit einem vertrauten Gerät anzumelden. Die selbst durchgeführte Wiederherstellung per Multi-Factor Authentication ist in diesem Fall nicht möglich. <br><br> ![Korrektur](./media/active-directory-identityprotection-flows/200.png "Korrektur") <br>



## Multi-Factor Authentication-Registrierung während einer risikobehafteten Anmeldung

Es ist wichtig, dass sich Benutzer für Multi-Factor Authentication registrieren, damit sie vorbereitet sind und richtig auf Sicherheitsabfragen reagieren können. Wenn ein Benutzer nicht für Multi-Factor Authentication registriert ist, dies für die Richtlinie aber erforderlich ist, kann er während einer risikobehafteten Anmeldung zum Registrieren aufgefordert werden. Dies bedeutet, dass ein Angreifer anstelle des richtigen Benutzers zum Hinzufügen einer Telefonnummer aufgefordert wird.

Zur Vermeidung dieser Situation sollten Sie für Benutzer die Registrierung für mehrstufige Authentifizierung so bald wie möglich obligatorisch machen. Die Telefonnummer ist dem Konto dann bereits zugeordnet, wenn es ggf. kompromittiert wird. Alternativ hierzu können Administratoren kompromittierte Benutzer, die nicht für Multi-Factor Authentication registriert sind, auch vollständig blockieren.

**Die Registrierung der mehrstufige Authentifizierung während einer risikobehafteten Anmeldung hat zwei Schritte:**

1. Dem Benutzer wird mitgeteilt, dass das Konto gefährdet ist. <br><br> ![Korrektur](./media/active-directory-identityprotection-flows/150.png "Korrektur") <br>

2. Der Registrierungsprozess für die mehrstufige Authentifizierung wird initiiert. <br><br> ![Korrektur](./media/active-directory-identityprotection-flows/151.png "Korrektur") <br>

Die nächsten Schritte finden Sie unter [Multi-factor authentication registration](#multi-factor-authentication-registration) (Registrierung für mehrstufige Authentifizierung)




## Wiederherstellung kompromittierter Konten

Wenn eine Sicherheitsrichtlinie für das Benutzerrisiko konfiguriert wurde, müssen Benutzer, für die die in der Richtlinie angegebene Benutzerrisikostufe erfüllt ist (und daher als kompromittiert angesehen werden), den Wiederherstellungsablauf für kompromittierte Benutzer durchlaufen, bevor sie sich anmelden können.

**Der Wiederherstellungsablauf für kompromittierte Benutzer umfasst drei Schritte:**

1. Der Benutzer wird darüber informiert, dass die Sicherheit des Kontos aufgrund von verdächtigen Aktivitäten oder kompromittierten Anmeldeinformationen gefährdet ist.

<br> ![Korrektur](./media/active-directory-identityprotection-flows/101.png "Korrektur") <br>

2.	Der Benutzer muss seine Identität nachweisen, indem er eine Sicherheitsabfrage richtig beantwortet. Wenn der Benutzer für Multi-Factor Authentication registriert ist, kann er die Wiederherstellung selbst durchführen. Hierfür muss der Benutzer mit einem Sicherheitscode einen Roundtripvorgang für seine Telefonnummer durchführen. 

<br> ![Korrektur](./media/active-directory-identityprotection-flows/110.png "Korrektur") <br>


3.	Am Ende des Vorgangs wird der Benutzer gezwungen, sein Kennwort zu ändern, da eine andere Person unter Umständen Zugriff auf sein Konto hatte. Screenshots dieser Benutzeroberfläche finden Sie weiter unten.
 
<br> ![Korrektur](./media/active-directory-identityprotection-flows/111.png "Korrektur") <br>



## Kompromittiertes Konto blockiert 

Um die Blockierung für einen Benutzer aufzuheben, der aufgrund einer Benutzerrisiko-Sicherheitsrichtlinie blockiert wurde, muss sich der Benutzer an einen Administrator oder den Helpdesk wenden. Die selbst durchgeführte Wiederherstellung per Multi-Factor Authentication ist in diesem Fall nicht möglich.

<br> ![Korrektur](./media/active-directory-identityprotection-flows/104.png "Korrektur") <br>



 
## Kennwort zurücksetzen

Wenn die Anmeldung für einen kompromittierten Benutzer gesperrt ist, kann ein Administrator ein temporäres Kennwort für ihn generieren. Die Benutzer müssen ihr Kennwort während der nächsten Anmeldung ändern.

<br> ![Korrektur](./media/active-directory-identityprotection-flows/160.png "Korrektur") <br>


 




 

## Weitere Informationen

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

<!---HONumber=AcomDC_0316_2016-->