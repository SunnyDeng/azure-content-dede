<properties
	pageTitle="Azure Active Directory B2C-Vorschau: Erweiterbares Richtlinienframework | Microsoft Azure"
	description="Ein Thema über das erweiterbare Richtlinienframework von Azure Active Directory B2C und die Erstellung verschiedener Richtlinientypen"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Erweiterbares Richtlinienframework

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Die Grundlagen

Das erweiterbare Richtlinienframework von Azure Active Directory (AD) B2C ist der Hauptvorteil des Diensts. Richtlinien beschreiben Benutzeroberflächen im Zusammenhang mit der Consumeridentität, z. B. Registrierung, Anmeldung oder Profilbearbeitung. Eine Registrierungsrichtlinie ermöglicht es z. B., das Verhalten zu steuern, indem die folgenden Einstellungen konfiguriert werden:

- Kontotypen (Konten sozialer Netzwerke wie Facebook und Google und/oder lokale Konten wie E-Mail-Adresse/Benutzername und Kennwort), die Consumer zum Registrieren für die Anwendung verwenden können.
- Attribute (z. B. Vorname, Postleitzahl, Schuhgröße usw.), die der Consumer während des Anmeldeprozesses angeben muss. 
- Verwendung der Multi-Factor Authentication.
- Das Aussehen und Verhalten aller Registrierungsseiten.
- Informationen (die als Ansprüche im Token ausgedrückt werden), die die Anwendung nach Abschluss der Richtlinienausführung erhält.

Sie können mehrere Richtlinien verschiedener Typen in Ihrem Mandanten erstellen und bei Bedarf in Ihrer Anwendung verwenden. Richtlinien können anwendungsübergreifend wieder verwendet werden. Dadurch können Entwickler Benutzeroberflächen im Zusammenhang mit der Consumeridentität mit minimalen oder ganz ohne Änderungen an ihrem Code definieren und ändern. Der Dienst wird nach und nach mit umfangreicheren Richtlinientypen erweitert.

Richtlinien können über eine einfache Entwicklerschnittstelle verwendet werden. Ihre Anwendung löst eine Richtlinie mithilfe einer standardmäßigen HTTP-Authentifizierungsanforderung aus (übergibt einen Richtlinienparameter in der Anforderung) und empfängt ein benutzerdefiniertes Token als Antwort. Der einzige Unterschied zwischen Anforderungen, die eine Registrierungsrichtlinie und eine Anmelderichtlinie aufrufen, ist beispielsweise der Richtlinienname, der im Parameter "p" der Abfragezeichenfolge verwendet wird:

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Wenn Sie an einer ausführlichen Beschreibung des Richtlinienframeworks interessiert sind, sollten Sie diesen [Blogbeitrag](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx) lesen.

## Erstellen einer Registrierungsrichtlinie

Für die Registrierung bei Ihrer Anwendung müssen Sie eine Registrierungsrichtlinie erstellen. Diese Richtlinie beschreibt die Benutzeroberflächen, die Consumer bei der Registrierung durchgehen, und den Inhalt der Token, die die Anwendung bei erfolgreichen Registrierungen erhält.

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf **Registrierungsrichtlinien**.
3. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
4. Der **Name** bestimmt den Namen der Registrierungsrichtlinie, die von der Anwendung verwendet wird. Geben Sie beispielsweise "SiUp" ein.
5. Klicken Sie auf **Identitätsanbieter**, und wählen Sie "E-Mail-Adresse" aus. Optional können Sie auch soziale Netzwerke als Identitätsanbieter auswählen, sofern bereits konfiguriert. Klicken Sie auf **OK**.

    > [AZURE.NOTE]Für lokale Konten werden unter den Anmelderichtlinien von Azure AD B2C "sichere" Kennwörter verwendet (sie sind so festgelegt, dass sie "nie ablaufen"). Weitere Einstellungen (die von Azure AD B2C derzeit nicht verwendet werden) finden Sie in der [Azure AD-Kennwortrichtlinie](https://msdn.microsoft.com/library/azure/jj943764.aspx).

6. Klicken Sie auf **Registrierungsattribute**. Hier wählen Sie die Attribute aus, die der Consumer bei der Registrierung angeben soll. Wählen Sie z. B. "Land/Region", "Anzeigename" und "Postleitzahl" aus. Klicken Sie auf **OK**.
7. Klicken Sie auf **Anwendungsansprüche**. Hier wählen Sie die Ansprüche aus, die in den zurückgegebenen Token nach einer erfolgreichen Registrierung an die Anwendung zurückgegeben werden sollen. Wählen Sie z. B. "Anzeigename", "Identitätsanbieter", "Postleitzahl", "Benutzer ist neu" und "Objekt-ID des Benutzers" aus.
8. Klicken Sie auf **Erstellen**. Beachten Sie, dass die soeben erstellte Richtlinie als **B2C\_1\_SiUp** (das **B2C\_1\_**-Fragment wird automatisch vorangestellt) auf dem Blatt **Registrierungsrichtlinien** angezeigt wird.
9. Öffnen Sie die Richtlinie, indem Sie auf **B2C\_1\_SiUp** klicken.
10. Wählen Sie "Contoso B2C-App" in der Dropdownliste **Anwendungen** und `https://localhost:44321/` in der Dropdownliste **Antwort-URL/Umleitungs-URI** aus. Klicken Sie auf die Schaltfläche **Jetzt ausführen**. Eine neue Browserregisterkarte wird geöffnet, und Sie können die Benutzeroberfläche für Consumer für die Registrierung bei Ihrer Anwendung durchgehen.

    > [AZURE.NOTE]Es dauert bis zu einer Minute, bis die Erstellung und Aktualisierung von Richtlinien wirksam wird.

## Erstellen einer Anmelderichtlinie

Für die Anmeldung bei Ihrer Anwendung müssen Sie eine Anmelderichtlinie erstellen. Diese Richtlinie beschreibt die Benutzeroberflächen, die Consumer bei der Anmeldung durchgehen, und den Inhalt der Token, die die Anwendung bei erfolgreichen Anmeldungen erhält.

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf **Anmelderichtlinien**.
3. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
4. Der **Name** bestimmt den Namen der Anmelderichtlinie, die von der Anwendung verwendet wird. Geben Sie beispielsweise "SiIn" ein.
5. Klicken Sie auf **Identitätsanbieter**, und wählen Sie "E-Mail-Adresse" aus. Optional können Sie auch soziale Netzwerke als Identitätsanbieter auswählen, sofern bereits konfiguriert. Klicken Sie auf **OK**.
6. Klicken Sie auf **Anwendungsansprüche**. Hier wählen Sie die Ansprüche aus, die in den zurückgegebenen Token nach einer erfolgreichen Anmeldung an die Anwendung zurückgegeben werden sollen. Wählen Sie z. B. "Anzeigename", "Identitätsanbieter", "Postleitzahl" und "Objekt-ID des Benutzers" aus. Klicken Sie auf **OK**.
7. Klicken Sie auf **Erstellen**. Beachten Sie, dass die soeben erstellte Richtlinie als **B2C\_1\_SiIn** (das **B2C\_1\_**-Fragment wird automatisch hinzugefügt) auf dem Blatt **Anmelderichtlinien** angezeigt wird.
8. Öffnen Sie die Richtlinie, indem Sie auf **B2C\_1\_SiIn** klicken.
9. Wählen Sie "Contoso B2C-App" in der Dropdownliste **Anwendungen** und `https://localhost:44321/` in der Dropdownliste **Antwort-URL/Umleitungs-URI** aus. Klicken Sie auf die Schaltfläche **Jetzt ausführen**. Eine neue Browserregisterkarte wird geöffnet, und Sie können die Benutzeroberfläche für Consumer für die Anmeldung bei Ihrer Anwendung durchgehen.

    > [AZURE.NOTE]Es dauert bis zu einer Minute, bis die Erstellung und Aktualisierung von Richtlinien wirksam wird.

## Erstellen einer Richtlinie für die Profilbearbeitung

Um die Profilbearbeitung in Ihrer Anwendung zu ermöglichen, müssen Sie eine Richtlinie für die Profilbearbeitung erstellen. Diese Richtlinie beschreibt die Benutzeroberflächen, die Consumer bei der Profilbearbeitung durchgehen, und den Inhalt der Token, die die Anwendung beim erfolgreichen Abschluss erhält.

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf **Richtlinien für die Profilbearbeitung**.
3. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
4. Der **Name** bestimmt den Namen der Richtlinie für die Profilbearbeitung, die von der Anwendung verwendet wird. Geben Sie beispielsweise "SiPe" ein.
5. Klicken Sie auf **Identitätsanbieter**, und wählen Sie "E-Mail-Adresse" aus. Optional können Sie auch soziale Netzwerke als Identitätsanbieter auswählen, sofern bereits konfiguriert. Klicken Sie auf **OK**.
6. Klicken Sie auf **Profilattribute**. Hier wählen Sie Attribute aus, die der Consumer anzeigen und bearbeiten kann. Wählen Sie z. B. "Land/Region", "Anzeigename" und "Postleitzahl" aus. Klicken Sie auf **OK**.
7. Klicken Sie auf **Anwendungsansprüche**. Hier wählen Sie die Ansprüche aus, die in den zurückgegebenen Token nach einer erfolgreichen Profilbearbeitung an die Anwendung zurückgegeben werden sollen. Wählen Sie z. B. "Anzeigename" und "Postleitzahl" aus.
8. Klicken Sie auf **Erstellen**. Beachten Sie, dass die soeben erstellte Richtlinie als **B2C\_1\_SiPe** (das **B2C\_1\_**-Fragment wird automatisch vorangestellt) auf dem Blatt **Richtlinien für die Profilbearbeitung** angezeigt wird.
9. Öffnen Sie die Richtlinie, indem Sie auf **B2C\_1\_SiPe** klicken.
10. Wählen Sie "Contoso B2C-App" in der Dropdownliste **Anwendungen** und `https://localhost:44321/` in der Dropdownliste **Antwort-URL/Umleitungs-URI** aus. Klicken Sie auf die Schaltfläche **Jetzt ausführen**. Eine neue Browserregisterkarte wird geöffnet, und Sie können die Benutzeroberfläche für Consumer für die Profilbearbeitung in Ihrer Anwendung durchgehen.

    > [AZURE.NOTE]Es dauert bis zu einer Minute, bis die Erstellung und Aktualisierung von Richtlinien wirksam wird.

<!---HONumber=AcomDC_0107_2016-->