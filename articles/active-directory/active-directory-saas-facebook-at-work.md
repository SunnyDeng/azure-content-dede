<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit Facebook at Work | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Facebook at Work konfigurieren."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="asmalser"/>


# Tutorial: Azure Active Directory-Integration mit Facebook at Work

In diesem Tutorial erfahren Sie, wie Sie Facebook at Work in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von Facebook at Work in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Facebook at Work hat. 
- Sie können automatisch Konten für Benutzer bereitstellen, denen Zugriff auf Facebook at Work erteilt wurde.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Facebook at Work anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten. 

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).


## Voraussetzungen 

Um die Azure AD-Integration mit CS Stars konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement.
- Facebook at Work mit aktivierter Funktion für einmaliges Anmelden

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 


## Hinzufügen von Facebook at Work aus dem Katalog
Zum Konfigurieren der Integration von Facebook at Work in Azure AD müssen Sie Facebook at Work aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Facebook at Work aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf **Active Directory**. <br><br>![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht im Hauptmenü der Verzeichnisansicht auf **Anwendungen**. <br><br>![Anwendungen][2]<br>

4. Klicken Sie unten auf der Seite auf **Hinzufügen**. <br><br>![Anwendungen][3]<br>

5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**. <br><br>![Anwendungen][4]<br>

6. Geben Sie im Suchfeld als Suchbegriff **Facebook at Work** ein.

7. Wählen Sie im Ergebnisbereich **Facebook at Work** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.


### Konfigurieren der einmaligen Anmeldung in Azure AD

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei Facebook at Work zu authentifizieren.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens in Azure AD in Facebook at Work die folgenden Schritte aus:**

1.	Wenn Sie Facebook at Work im Azure-Verwaltungsportal hinzugefügt haben, klicken Sie auf **Einmaliges Anmelden konfigurieren**.

2.	Geben Sie auf dem Bildschirm **App-URL konfigurieren** die URL ein, unter der sich Benutzer bei Facebook at Work anmelden. Hierbei handelt es sich um die URL Ihres Facebook at Work-Mandanten (Beispiel: https://example.facebook.com/). Klicken Sie anschließend auf **Weiter**.

3.	Melden Sie sich in einem anderen Webbrowserfenster bei der Facebook at Work-Unternehmenswebsite als Administrator an.

4. Führen Sie die Schritte unter der folgenden URL aus, um Facebook at Work für die Verwendung von Azure AD als Identitätsanbieter zu konfigurieren: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers).

5.	Kehren Sie anschließend zum Browserfenster mit dem Azure-Verwaltungsportal zurück, und aktivieren Sie das Kontrollkästchen, um zu bestätigen, dass Sie den Vorgang abgeschlossen haben. Klicken Sie dann auf **Weiter** und **Abschließen**.


## Automatische Bereitstellung von Benutzern in Facebook at Work

Azure AD unterstützt die Möglichkeit, die Kontodetails zugewiesener Benutzer mit Facebook at Work zu synchronisieren. Dank dieser automatischen Synchronisierung kann Facebook at Work die erforderlichen Daten für die Autorisierung von Benutzern für den Zugriff abzurufen, bevor sie sich zum ersten Mal anmelden. Sie hebt zudem die Bereitstellung von Benutzern in Facebook at Work auf, wenn der Zugriff in Azure AD widerrufen wurde.

Die automatische Bereitstellung kann durch Klicken auf **Benutzerbereitstellung konfigurieren** im Fenster des Azure-Verwaltungsportals eingerichtet werden.

Weitere Informationen zum Konfigurieren der automatischen Bereitstellung finden Sie unter [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).


## Zuweisen von Benutzern zu Facebook at Work

Damit bereitgestellten AAD-Benutzern Facebook at Work im Zugriffsbereich angezeigt wird, muss ihnen im Azure-Verwaltungsportal der Zugriff zugewiesen werden.

**So weisen Sie Facebook at Work Benutzer zu:**

1.	Klicken Sie auf der Startseite für Facebook at Work im Azure-Verwaltungsportal auf **Konten zuweisen**.

2.	Wählen Sie im Menü **Einblenden**, ob Sie einen Benutzer oder eine Gruppe zu Facebook at Work zuweisen möchten, und klicken Sie auf die Schaltfläche mit dem Häkchen.

3.	Wählen Sie in der angezeigten Liste die Benutzer oder Gruppen aus, denen Sie Facebook at Work zuweisen möchten.

4.	Klicken Sie in der Fußzeile auf die Schaltfläche **Zuweisen**.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png

<!---HONumber=Nov15_HO2-->