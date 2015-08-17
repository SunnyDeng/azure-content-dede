<properties
	pageTitle="Veröffentlichen von Apps mit Azure AD-Anwendungsproxy"
	description="Erläutert die Veröffentlichung lokaler Anwendungen mit dem Azure AD-Anwendungsproxy."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="rkarlin"/>


# Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy

> [AZURE.NOTE]Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](https://msdn.microsoft.com/library/azure/dn532272.aspx).

Nachdem Sie Microsoft Azure AD-Anwendungsproxy aktiviert haben, können Sie Anwendungen veröffentlichen, um sie für Ihre Benutzer von außerhalb Ihres privaten Netzwerks zugänglich zu machen.

Dieser Abschnitt führt Sie durch die erforderlichen Schritte zum Veröffentlichen von Anwendungen, die in Ihrem lokalen Netzwerk ausgeführt werden, und für die Sie sicheren Remotezugriff von außerhalb Ihres Netzwerks aktivieren möchten.

> [AZURE.NOTE]Um sicherzustellen, dass der Connector ordnungsgemäß ausgeführt wird, sollte die erste Anwendung, die Sie veröffentlichen, eine Website innerhalb Ihres privaten Netzwerks sein. So können Sie sicherstellen, dass Benutzer über das Internet darauf zugreifen können, bevor Sie eine tatsächliche Anwendung veröffentlichen.

## Veröffentlichen einer App mithilfe des Assistenten

1. Öffnen Sie einen Browser Ihrer Wahl, und wechseln Sie zum Azure-Verwaltungsportal.
2. Klicken Sie im linken Bereich des Azure-Verwaltungsportals auf die Registerkarte "Active Directory".
3. Klicken Sie auf das Verzeichnis, in dem Sie den Anwendungsproxy aktiviert haben, und für das Sie eine Anwendung (z. B. Wingtip Toys) veröffentlichen möchten.
4. Klicken Sie auf die Registerkarte **Anwendungen** und dann am unteren Bildschirmrand ![Anwendung hinzufügen](http://i.imgur.com/wEeZ7IR.png) auf **Hinzufügen**.
5. Klicken Sie im Dialogfeld "Was möchten Sie tun?" auf **Veröffentlichen einer Anwendung, auf die von außerhalb Ihres Netzwerks zugegriffen werden kann**. ![Neue Anwendung, auf die von außerhalb Ihres Netzwerks zugegriffen werden kann](http://i.imgur.com/Wj3vkyD.png)

6. Folgen Sie den Anweisungen auf dem Bildschirm, um die folgenden Informationen über Ihre Anwendung bereitzustellen: ![Anwendungseigenschaften](http://i.imgur.com/MkPNc93.png)

**Einstellung** | **Details**
---|---
Externe URL | Dies ist die URL des Clouddiensts, die zum Zugriff auf die Anwendung von außerhalb Ihres privaten Netzwerks verwendet wird. Die URL wird basierend auf dem bereitgestellten Namen automatisch mit dem Suffix "msappproxy.net" generiert.
Präauthentifizierungsmethode | <p>Legen Sie die Art der Präauthentifizierungsmethode fest, die die Anwendung verwenden soll:</p><p>a. Microsoft Azure Active Directory (Microsoft Azure AD) – legt die Vorauthentifizierungsmethode auf "Microsoft Azure Active Directory (Microsoft Azure AD)" fest. Wenn ein Benutzer versucht, auf eine Anwendung zuzugreifen, leitet der Anwendungsproxy den Benutzer zur Anmeldung bei Azure AD um. Dort wird der Benutzer authentifiziert, um sicherzustellen, dass er die erforderlichen Berechtigungen für das Verzeichnis und die Anwendung besitzt.</p><p>b. Passthrough – Es findet keine Präauthentifizierung statt.</p>
Externes URL-Protokoll | <p>Standardmäßig werden Anwendungen mithilfe des HTTPS-Protokolls veröffentlicht.</p> <p>Zur Aktivierung von HTTP für eine interne Anwendung müssen Sie die Präauthentifizierung auf "Passthrough" setzen. Dann können Sie das externe URL-Protokoll von HTTPS in HTTP ändern.</p> <p>Beachten Sie, dass das Veröffentlichen von Anwendungen mithilfe von HTTP Sicherheitsprobleme für Ihre Anwendung und Ihre Benutzer hervorrufen kann.</p> <p>Sie können eine benutzerdefinierte Domäne einfügen, statt die Standarddomäne "msappproxy.net" zu verwenden. Weitere Informationen finden Sie in den nachfolgenden Abschnitten.</p>
Interne URL | Dies ist die interne URL, die der Anwendungsproxyconnector verwendet, um intern auf die Anwendung zuzugreifen. Dies sollte die URL der veröffentlichten Anwendung sein, die von innerhalb Ihres privaten Netzwerks zum Zugriff auf die Anwendung verwendet wird. Dies ist eine gültige URL ohne Pfadangabe, Leerzeichen und Symbole.

Um den Assistenten zu beenden, klicken Sie auf das Häkchen unten im Bildschirm. Die Anwendung ist jetzt in Azure AD definiert.



## Zuweisen von Benutzern und Gruppen zur Anwendung

1. Für präauthentifizierte Apps müssen Sie Benutzer und Gruppen zuweisen, die Zugriff auf die Anwendung haben. <p>Der Zugriff auf Passthrough-Apps ist für alle Benutzer verfügbar. Damit jedoch ein Benutzer die App in seiner Anwendungsliste sehen kann, müssen Sie die App diesem Benutzer zuweisen.
2. Nach dem Beenden des Assistenten für das Hinzufügen von Apps wird die Schnellstartseite für den Anwendungsproxy angezeigt. Um Benutzer zuzuweisen, klicken Sie auf **Benutzer zuweisen**. ![Schnellstartbildschirm für den Anwendungsproxy](http://i.imgur.com/OmuWElV.png)
3. Wählen Sie die einzelnen Benutzer oder Gruppen aus, die Sie dieser App zuweisen möchten, und klicken Sie auf **Zuweisen**. 

> [AZURE.NOTE]Für Apps unter "Integrierte Windows-Authentifizierung" können Sie nur Benutzer und Gruppen zuweisen, die über Ihr lokales Active Directory synchronisiert werden. Benutzer, die sich über ein Microsoft-Konto anmelden, und Gäste können nicht für Apps zugewiesen werden, die mit dem Azure Active Directory-Anwendungsproxy veröffentlicht werden. Stellen Sie sicher, dass die zugewiesenen Benutzer sich mit den Anmeldeinformationen anmelden, die Teil der gleichen Domäne sind wie die Anwendung, die Sie veröffentlichen.

## Erweiterte Konfiguration

1. Auf der Seite **Konfigurieren** können Sie veröffentlichte Apps ändern oder erweiterte Optionen konfigurieren, wie z. B. SSO für lokale Anwendungen. ![Erweiterte Konfiguration](http://i.imgur.com/FhbRvoq.png)

2. Wählen Sie die App aus, und klicken Sie auf **Konfigurieren**. Folgende Optionen sind verfügbar:

**Einstellung** | **Details**
---|---
Name | Geben Sie einen beschreibenden Namen für Ihre Anwendung ein.
Externe URL | Dies ist die URL des Clouddiensts, die zum Zugriff auf die Anwendung von außerhalb Ihres privaten Netzwerks verwendet wird. Die URL wird basierend auf dem bereitgestellten Namen automatisch mit dem Suffix **msappproxy.net** generiert.
Präauthentifizierungsmethode | Legen Sie den Typ der Präauthentifizierungsmethode fest, den die Anwendung verwenden soll:<p>Active Directory – legt die Präauthentifizierungsmethode auf Active Directory fest. Wenn ein Benutzer versucht, auf eine Anwendung zuzugreifen, leitet der Anwendungsproxy den Benutzer zur Anmeldung beim Anwendungsproxy um. Dort wird der Benutzer authentifiziert, um sicherzustellen, dass er die erforderlichen Berechtigungen für das Verzeichnis und die Anwendung besitzt.</p><p> Passthrough – Es findet keine Präauthentifizierung statt.</p>
Externe URL | Standardmäßig werden Anwendungen mithilfe des HTTPS-Protokolls veröffentlicht. Zur Aktivierung von HTTP für eine interne Anwendung müssen Sie die Präauthentifizierung auf "Passthrough" setzen. Dann können Sie das externe URL-Protokoll von HTTPS in HTTP ändern. Beachten Sie, dass das Veröffentlichen von Anwendungen mithilfe von HTTP Sicherheitsprobleme für Ihre Anwendung und Ihre Benutzer hervorrufen kann. Sie können eine benutzerdefinierte Domäne festlegen, statt die Standarddomäne (msappproxy.net) zu verwenden. Weitere Informationen finden Sie unter "Arbeiten mit benutzerdefinierten Domänen".
Interne URL | Dies ist die interne URL, die der Anwendungsproxyconnector verwendet, um intern auf die Anwendung zuzugreifen. Dies sollte die URL der veröffentlichten Anwendung sein, die von innerhalb Ihres privaten Netzwerks zum Zugriff auf die Anwendung verwendet wird. Dies ist eine gültige URL ohne Pfadangabe, Leerzeichen und Symbole.
URL in Headern übersetzen | Für Anwendungen (z. B. einige SharePoint-Konfigurationen), die erfordern, dass die HTTP-Hostheader nicht übersetzt werden, setzen Sie diesen Wert auf "Nein". So wird die Übersetzung sowohl für Anforderungs- als auch für Antwortheader deaktiviert.
Interne Authentifizierungsmethode | Wenn Sie den Anwendungsproxy für die Präauthentifizierung verwenden, können Sie eine interne Authentifizierungsmethode festlegen, um Ihren Benutzern die einmalige Anmeldung (SSO) für diese Anwendung zu ermöglichen. <p> Wählen Sie **Integrierte Windows-Authentifizierung (IWA)**, wenn Ihre Anwendung IWA verwendet. Außerdem können Sie die eingeschränkte Kerberos-Delegierung zum Aktivieren von SSO für diese Anwendung konfigurieren. <p> Wählen Sie **Keine**, wenn Ihre Anwendung IWA nicht verwendet. <p> Anwendungen, die IWA verwenden, müssen mit der eingeschränkten Kerberos-Delegierung konfiguriert werden. Andernfalls kann der Anwendungsproxy diese Anwendungen nicht veröffentlichen. <p> Weitere Informationen finden Sie unter "Einmaliges Anmelden für IWA-Anwendungen unter Verwendung der eingeschränkten Kerberos-Delegierung mit Anwendungsproxy".
Interner Anwendungs-SPN | Dies ist der Dienstprinzipalname (SPN) der internen Anwendung gemäß Konfiguration im lokalen Anwendungsproxy. Der SPN wird vom Anwendungsproxyconnector verwendet, um Kerberos-Token für die Anwendung mit der eingeschränkten Kerberos-Delegierung abzurufen. <p> Weitere Informationen finden Sie unter "Einmaliges Anmelden für IWA-Anwendungen unter Verwendung der eingeschränkten Kerberos-Delegierung mit Anwendungsproxy".

Nachdem Sie Anwendungen mit dem Azure Active Directory-Anwendungsproxy veröffentlicht haben, werden sie in der Liste der Anwendungen in Azure AD aufgeführt, und Sie können sie dort verwalten. Wenn Sie Anwendungsproxydienste deaktivieren, nachdem Sie Anwendungen veröffentlicht haben, werden die Anwendungen zwar nicht gelöscht, aber der Zugriff darauf von außerhalb Ihres privaten Netzwerks ist nicht mehr möglich. Um eine Anwendung anzeigen und den Zugriff darauf sicherzustellen, doppelklicken Sie auf den Namen der Anwendung. Wenn der Anwendungsproxydienst deaktiviert und die Anwendung nicht verfügbar ist, wird am oberen Bildschirmrand eine Warnmeldung angezeigt. Um eine Anwendung zu löschen, wählen Sie eine Anwendung in der Liste aus, und klicken Sie dann auf **Löschen**.

## Zusätzliche Ressourcen

* [Informationen zur eingeschränkten Kerberos-Delegierung](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=August15_HO6-->