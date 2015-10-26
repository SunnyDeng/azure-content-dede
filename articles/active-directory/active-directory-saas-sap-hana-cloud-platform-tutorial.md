<properties pageTitle="Tutorial: Azure Active Directory-Integration mit SAP HANA Cloud Platform | Microsoft Azure" description="Erfahren Sie, wie Sie SAP HANA Cloud Platform mit Azure Active Directory verwenden können, um einmalige Anmeldung, automatisierte Bereitstellung und mehr zu aktivieren!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Azure Active Directory-Integration mit SAP HANA Cloud Platform
>[AZURE.TIP]Klicken Sie [hier](http://go.microsoft.com/fwlink/?LinkId=529793), um Feedback abzugeben.
  
In diesem Tutorial wird die Integration von Azure und SAP HANA Cloud Platform erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein SAP HANA Cloud Platform-Konto
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie SAP HANA Cloud Platform zugewiesen haben, wie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586) beschrieben mittels einmaliger Anmeldung bei der Anwendung anmelden.

>[AZURE.IMPORTANT]Sie müssen Ihre eigene Anwendung bereitstellen oder eine Anwendung auf Ihrem SAP HANA Cloud Platform-Konto abonnieren, um sich zu Testzwecken einmalig anzumelden. In diesem Tutorial wird eine Anwendung auf dem Konto bereitgestellt.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für SAP HANA Cloud Platform
2.  Konfigurieren der einmaligen Anmeldung
3.  Zuweisen einer Rolle an einen Benutzer
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Szenario")
##Aktivieren der Anwendungsintegration für SAP HANA Cloud Platform
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für SAP HANA Cloud Platform aktivieren.

###So aktivieren Sie die Anwendungsintegration für SAP HANA Cloud Platform:

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Add**.

    ![Anwendung hinzufügen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie in das **Suchfeld** **SAP HANA Cloud Platform** ein.

    ![Anwendungskatalog](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **SAP HANA Cloud Platform** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei SAP HANA Cloud Platform zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei in Ihren SAP HANA Cloud Platform-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden:

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **SAP HANA Cloud Platform** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei SAP HANA Cloud Platform anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Einmaliges Anmelden konfigurieren")

3.  Melden Sie sich in einem anderen Webbrowserfenster auf der Registerkarte https://account.\<landscape host>.ondemand.com/cockpit (z. B.: **https://account.hanatrial.ondemand.com/cockpit*) beim SAP HANA Cloud Platform-Cockpit an.

4.  Klicken Sie auf die Registerkarte **Vertrauen**.

    ![Vertrauen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Vertrauen")

5.  Führen Sie im Verwaltungsabschnitt „Vertrauen“ die folgenden Schritte aus:

    ![Metadaten abrufen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Metadaten abrufen")

    1.  Klicken Sie auf die Registerkarte **Lokale Dienstanbieter**.
    2.  Klicken Sie zum Herunterladen der SAP HANA Cloud Platform-Metadatendatei auf **Metadaten abrufen**.

6.  Führen Sie im Azure Active Directory-Portal auf der Seite **App-URL konfigurieren** die folgenden Schritte aus, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "App-URL konfigurieren")

    1.  Geben Sie im Textfeld **Anmelde-URL** die URL ein, welche die Benutzer zur Anmeldung bei der** SAP HANA Cloud Platform**-Anwendung verwenden. Dies ist die kontospezifische URL einer geschützten Ressource in der SAP HANA Cloud Platform-Anwendung. Die URL basiert auf dem folgenden Muster: *https://\<applicationName><accountName>.<landscape host>.ondemand.com/<path\_to\_protected\_resource>* (z. B.: **https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

		>[AZURE.NOTE]Dies ist die URL in Ihrer SAP HANA Cloud Platform-Anwendung, für deren Authentifizierung der Benutzer erforderlich ist.

    2.  Öffnen Sie die heruntergeladene SAP HANA Cloud Platform-Metadatendatei, und suchen Sie die Registerkarte **Ns3:AssertionConsumerService**.
    3.  Kopieren Sie den Wert des Attributs **Speicherort**, und fügen Sie ihn in das Textfeld **Antwort-URL für die SAP HANA Cloud Platform** ein.

7.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für SAP HANA Cloud Platform** auf **Metadaten herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Einmaliges Anmelden konfigurieren")

8.  Führen Sie im SAP HANA Cloud Platform-Cockpit im Abschnitt **Lokale Dienstanbieter** die folgenden Schritte aus:

    ![Verwaltung von Vertrauensstellungen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Verwaltung von Vertrauensstellungen")

    1.  Klicken Sie auf **Bearbeiten**.
    2.  Wählen Sie als **Konfigurationstyp** die Option **Benutzerdefiniert**.
    3.  Belassen Sie als **Name des lokalen Dienstanbieters** den Standardwert.
    4.  Klicken Sie zum Generieren eines **Signaturschlüssel** und eines **-Signaturzertifikat**-Schlüsselpaars auf **Schlüsselpaar generieren**.
    5.  Wählen Sie als **Prinzipalweitergabe** die Option **Deaktiviert**.
    6.  Wählen Sie unter **Zwangsauthentifizierung** die Option **Deaktiviert**.
    7.  Klicken Sie auf **Speichern**.

9.  Klicken Sie auf die Registerkarte **Vertrauenswürdiger Identitätsanbieter**, und klicken Sie dann auf **Vertrauenswürdigen Identitätsanbieter hinzufügen**.

    ![Verwaltung von Vertrauensstellungen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Verwaltung von Vertrauensstellungen")

    >[AZURE.NOTE]Um die Liste der vertrauenswürdigen Identitätsanbieter verwalten zu können, müssen Sie den Konfigurationstyp „Benutzerdefinierte“ im Abschnitt „Lokale Dienstanbieter“ ausgewählt haben. Beim Standard-Konfigurationstyp haben Sie ein nicht-bearbeitbares und implizites Vertrauen gegenüber dem SAP ID-Dienst. Bei der Option „Keine“ haben Sie keine Vertrauenseinstellungen.

10. Klicken Sie auf die Registerkarte **Allgemein**, und klicken Sie dann auf **Durchsuchen**, um die heruntergeladene Metadatendatei hochzuladen.

    ![Verwaltung von Vertrauensstellungen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Verwaltung von Vertrauensstellungen")

    >[AZURE.NOTE]Nach dem Hochladen der Metadatendatei werden die Werte für **URL für einmaliges Anmelden**, **Einzelne Abmelde-URL** und **Signaturzertifikat** automatisch ausgefüllt.

11. Klicken Sie auf die Registerkarte **Attribute**.

12. Führen Sie im Abschnitt **Attribute** die folgenden Schritte aus:

    ![Attribute](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attribute")

    1.  Fügen Sie durch Klicken auf **Assertion-Attribut hinzufügen** die folgenden Assertion-Based-Attribute hinzu:

        |Assertion-Attribut| Prinzipal-Attribut|
		|-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname| firstname|--------------------|--------------------| |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname| LastName|---| |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|email|

    >[AZURE.NOTE]Die Konfiguration der Attribute hängt davon ab, wie die Anwendung(en) auf HPC entwickelt wurden, d. h. welche Attribute in der SAML-Antwort erwartet wird/werden und unter welchem Namen (Prinzipal-Attribut) sie auf dieses Attribut im Code zugreifen.
    >  
    >a. Das **Standard-Attribut** im Screenshot dient nur der Veranschaulichung. Es ist nicht erforderlich, damit das Szenario funktioniert.
    >
    >b. Die Namen und Werte für das im Screenshot gezeigte **Prinzipal Attribut** ist abhängig davon, wie die Anwendung entwickelt wird. Es ist möglich, dass die Anwendung andere Zuordnungen erfordert.

13. Wählen Sie im Azure-Portal auf der Dialogseite **Einmaliges Anmelden konfigurieren für SAP HANA Cloud Platform** die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Einmaliges Anmelden konfigurieren")
  
Als optionalen Schritt können Sie Assertion-Gruppen für Ihren Azure Active Directory-Identitätsanbieter konfigurieren.

>[AZURE.NOTE]Mithilfe von Gruppen auf SAP HANA Cloud Platform können Sie auf dynamische Weise eine oder mehrere Rollen in Ihren SAP HANA Cloud Platform-Anwendungen zuweisen, durch die Werte der Attribute in der SAML 2.0-Assertion definiert. Beispiel: Wenn die Assertion das Attribut „*contract=temporary*“ enthält, möchten Sie ggf. alle betroffenen Benutzer der Gruppe „*TEMPORÄR*“ hinzufügen. Die Gruppe „*Temporär*“ enthält möglicherweise eine oder mehrere Rollen aus einer oder mehreren Anmeldung(en), die auf Ihrem SAP HANA Cloud Platform-Konto bereitgestellt sind.
>  
>Verwenden Sie Assertion-Gruppen, wenn Sie viele Benutzer einer oder mehreren Rolle(n) auf Ihrem SAP HANA Cloud Platform-Konto zuweisen möchten. Wenn Sie nur einen einzelnen Benutzer oder eine geringe Anzahl an Benutzern zu einer (mehreren) speziellen Rolle(n) zuweisen möchten, empfehlen wir die direkte Zuweisung auf der Registerkarte „**Autorisierungen**“ des SAP HANA Cloud Platform-Cockpits.

##Zuweisen einer Rolle an einen Benutzer
  
Um Azure AD-Benutzer für die Anmeldung bei SAP HANA Cloud Platform zu aktivieren, müssen Sie ihnen Rollen in der SAP HANA Cloud Platform zuweisen.

###So weisen Sie einem Benutzer eine Rolle zu:

1.  Melden Sie sich bei Ihrem **SAP HANA Cloud Platform**-Cockpit an.

2.  Führen Sie die folgenden Schritte aus:

    ![Autorisierungen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Autorisierungen")

    1.  Klicken Sie auf **Autorisierung**.
    2.  Klicken Sie auf die Registerkarte **Benutzer**.
    3.  Geben Sie im Textfeld **Benutzer** die E-Mail-Adresse des Benutzers ein.
    4.  Klicken Sie auf **Zuweisen**, um den Benutzer einer Rolle zuzuweisen.
    5.  Klicken Sie auf **Speichern**.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie SAP HANA Cloud Platform Benutzer zu:

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **SAP HANA Cloud Platform** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, und klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->