<properties pageTitle="Tutorial: Azure Active Directory-Integration mit PolicyStat | Microsoft Azure" description="Hier erfahren Sie, wie Sie PolicyStat mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit PolicyStat
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=616313), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und PolicyStat erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen PolicyStat-Mandanten
  
Nach Abschluss dieses Tutorials können sich die PolicyStat zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer PolicyStat-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können Sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für PolicyStat
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-policystat-tutorial/IC808662.png "Szenario")
##Aktivieren der Anwendungsintegration für PolicyStat
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für PolicyStat aktivieren.

###So aktivieren Sie die Anwendungsintegration für PolicyStat

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-policystat-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-policystat-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-policystat-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **PolicyStat** ein.

    ![Anwendungskatalog](./media/active-directory-saas-policystat-tutorial/IC808627.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **PolicyStat** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei PolicyStat zu authentifizieren. Die PolicyStat-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der **SAML-Tokenattribute** benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

![Attribute](./media/active-directory-saas-policystat-tutorial/IC808628.png "Attribute")

###Führen Sie zum Konfigurieren des einmaligen Anmeldens die folgenden Schritte aus:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **PolicyStat** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-policystat-tutorial/IC808629.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei PolicyStat anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-policystat-tutorial/IC808630.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-Einstellungen konfigurieren** im Textfeld **Anmelde-URL** die von den Benutzern für die Anmeldung bei PolicyStat verwendete URL ein (z. B. *https://demo-azure.policystat.com*), und klicken Sie dann auf **Weiter**.

    ![Konfigurieren von App-Einstellungen](./media/active-directory-saas-policystat-tutorial/IC808631.png "Konfigurieren von App-Einstellungen")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für PolicyStat** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-policystat-tutorial/IC808632.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der PolicyStat-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf die Registerkarte **Administrator** und anschließend links im Navigationsbereich auf **Einmaliges Anmelden – Konfiguration**.

    ![Administratormenü](./media/active-directory-saas-policystat-tutorial/IC808633.png "Administratormenü")

7.  Wählen Sie im Abschnitt **Einrichtung** die Option **Integration des einmaligen Anmeldens aktivieren**.

    ![Einmaliges Anmelden – Konfiguration](./media/active-directory-saas-policystat-tutorial/IC808634.png "Einmaliges Anmelden – Konfiguration")

8.  Klicken Sie auf **Attribute konfigurieren**, und führen Sie anschließend im Abschnitt **Attribute konfigurieren** die folgenden Schritte aus:

    ![Einmaliges Anmelden – Konfiguration](./media/active-directory-saas-policystat-tutorial/IC808635.png "Einmaliges Anmelden – Konfiguration")

    1.  Geben Sie im Textfeld **Benutzernamen-Attribut** die Zeichenfolge **uid** ein.
    2.  Geben Sie im Textfeld **Vornamen-Attribut** die Zeichenfolge **firstname** ein.
    3.  Geben Sie im Textfeld **Nachnamen-Attribut** die Zeichenfolge **lastname** ein.
    4.  Geben Sie im Textfeld **E-Mail-Attribut** die Zeichenfolge **emailaddress** ein.
    5.  Klicken Sie auf **Änderungen speichern**.

9.  Klicken Sie auf **Ihre IDP-Metadaten**, und führen Sie anschließend im Abschnitt **Ihre IDP-Metadaten** die folgenden Schritte aus:

    ![Einmaliges Anmelden – Konfiguration](./media/active-directory-saas-policystat-tutorial/IC808635.png "Einmaliges Anmelden – Konfiguration")

    1.  Öffnen Sie die heruntergeladene Metadatendatei, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld **Ihre Identitätsanbietermetadaten** ein.
    2.  Klicken Sie auf **Änderungen speichern**.

10. Wählen Sie im Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-policystat-tutorial/IC771723.png "Einmaliges Anmelden konfigurieren")

11. 12. Klicken Sie oben im Menü auf **Attribute**, um das Dialogfeld **SAML-Tokenattribute** zu öffnen.

    ![Attribute](./media/active-directory-saas-policystat-tutorial/IC795920.png "Attribute")

13. So fügen Sie die erforderlichen Attributzuordnungen hinzu:

    ![Attribute](./media/active-directory-saas-policystat-tutorial/IC804823.png "Attribute")

    1.  Klicken Sie auf **Benutzerattribut hinzufügen**.
    2.  Geben Sie im Textfeld **Attributname** die Zeichenfolge **uid** ein.
    3.  Wählen Sie im Textfeld **Attributwert** die Option **ExtractMailPrefix()** aus.
    4.  Wählen Sie in der Liste **E-Mail** die Option **User.mail** aus.
    5.  Klicken Sie auf **Fertig stellen**.
##Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei PolicyStat anmelden können, müssen sie in PolicyStat bereitgestellt werden. PolicyStat unterstützt die bedarfsabhängige Benutzerbereitstellung. Das bedeutet, dass Sie die Benutzer nicht manuell zu PolicyStat hinzufügen müssen. Die Benutzer werden bei der ersten Anmeldung mittels einmaligem Anmelden automatisch hinzugefügt.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von PolicyStat-Benutzerkonten oder mithilfe der von PolicyStat bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie PolicyStat Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **PolicyStat** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-policystat-tutorial/IC808636.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-policystat-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->