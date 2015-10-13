4. Navigieren Sie zum Gateway-Blatt Ihrer API-App.

	![Klicken Sie auf "Gateway"](./media/app-service-api-gateway-config-auth/gateway.png)

7. Klicken Sie auf dem Blatt **Gateway** auf **Einstellungen** und anschließend auf **Identität**.

	![Klicken Sie auf "Einstellungen"](./media/app-service-api-gateway-config-auth/clicksettingsingateway.png)

	![Klicken Sie auf "Identität"](./media/app-service-api-gateway-config-auth/clickidentity.png)

	Vom Blatt **Identität** aus können Sie zu unterschiedlichen Blättern zum Konfigurieren der Authentifizierung mithilfe von Azure Active Directory und verschiedenen anderen Anbietern navigieren.

	![Blatt "Identität"](./media/app-service-api-gateway-config-auth/identityblade.png)
  
3. Wählen Sie den gewünschten Identitätsanbieter, und führen Sie die Schritte zum Konfigurieren Ihrer API-App für diesen Anbieter aus, die im entsprechenden Artikel aufgeführt sind. Diese Artikel wurden für mobile Apps geschrieben, die Vorgehensweise für API-Apps ist jedoch identisch. Für einige der Verfahren ist das [Azure-Portal] erforderlich.

 - [Microsoft-Konto](../articles/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Facebook-Anmeldung](../articles/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Twitter-Anmeldung](../articles/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Google-Anmeldung](../articles/app-service-mobile/app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Azure Active Directory](../articles/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

Die nachfolgenden Screenshots zeigen beispielhaft, welche Seiten im [Azure-Portal] und welche Blätter im [Azure-Vorschauportal] angezeigt werden sollten, nachdem Sie die Azure Active Directory-Authentifizierung eingerichtet haben.

Im Azure-Vorschauportal zeigt das Blatt **Azure Active Directory** eine **Client-ID** aus der Anwendung, die Sie auf der Registerkarte "Azure Active Directory" im Azure-Portal erstellt haben. Die Einstellung **Zulässige Mandanten** enthält Ihren Azure Active Directory-Mandanten (z. B. "contoso.onmicrosoft.com").

![Blatt "Azure Active Directory"](./media/app-service-api-gateway-config-auth/tdinaadblade.png)

Im Azure-Portal enthält die Registerkarte **Konfiguration** der Anwendung, die Sie auf der Registerkarte **Azure Active Directory** erstellt haben, die Einstellungen **Anmelde-URL**, **App-ID-URI** und **Antwort-URL** aus dem Blatt **Azure Active Directory** im Azure-Vorschauportal.

![](./media/app-service-api-gateway-config-auth/oldportal1.png)

![](./media/app-service-api-gateway-config-auth/oldportal2.png)

![](./media/app-service-api-gateway-config-auth/oldportal3.png)

![](./media/app-service-api-gateway-config-auth/oldportal4.png)

(Die Antwort-URL im Screenshot zeigt zweimal dieselbe URL, einmal mit `http:` und einmal mit `https:`.)

<!----HONumber=August15_HO6-->