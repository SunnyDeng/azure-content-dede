<properties
	pageTitle="Azure Active Directory B2C-Vorschau: Benutzerdefinierte Attribute | Microsoft Azure"
	description="Informationen zur Verwendung von benutzerdefinierten Attributen in Azure Active Directory B2C zum Erfassen von Informationen über Ihre Kunden"
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
	ms.date="01/04/2016"
	ms.author="swkrish"/>

#  Azure Active Directory B2C-Vorschau: Verwenden benutzerdefinierter Attribute zum Erfassen von Informationen über Ihre Kunden

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Das Azure Active Directory (AD) B2C-Verzeichnis bietet einen integrierten Satz von Attributen wie Vorname, Nachname, Ort, Postleitzahl usw. Allerdings hat jede kundenorientierte Anwendung eigene Anforderungen an die Informationen (Attribute), die von Kunden erfasst werden sollen. Mit Azure AD B2C können Sie Ihr Verzeichnis (insbesondere den Satz von Attributen, die für die einzelnen Kundenkonten gespeichert werden) erweitern. Im [Azure-Portal](https://portal.azure.com/) können Sie benutzerdefinierte Attribute erstellen und wie unten dargestellt in Ihren Anmelderichtlinien verwenden. Außerdem können Sie diese Attribute mit der Azure AD Graph-API lesen und schreiben, wie [hier](active-directory-b2c-devquickstarts-graph-dotnet.md) dargestellt.

> [AZURE.NOTE]Für benutzerdefinierte Attribute werden intern die [Verzeichnisschemaerweiterungen der Azure AD Graph-API](https://msdn.microsoft.com/library/azure/dn720459.aspx) verwendet.

## Erstellen eines benutzerdefinierten Attributs

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf **Benutzerattribute**.
3. Klicken Sie oben auf dem Blatt auf **+Hinzufügen**.
4. Geben Sie im Feld **Name** den Namen für das benutzerdefinierte Attribut (z. B. "ShoeSize") und optional eine **Beschreibung** ein. Klicken Sie auf **Erstellen**. Fertig.

    > [AZURE.NOTE]Derzeit ist nur der **Datentyp** "String" verfügbar. Weitere Datentypen (DateTime, Integer usw.) kommen in Zukunft hinzu.

Das benutzerdefinierte Attribut steht jetzt in der Liste der **Benutzerattribute** zur Verfügung und kann in Registrierungsrichtlinien verwendet werden.

## Verwenden eines benutzerdefinierten Attributs in der Registrierungsrichtlinie

1. [Führen Sie diese Schritte aus, um im Azure-Portal zum Blatt „B2C-Funktionen“ zu navigieren](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Klicken Sie auf **Registrierungsrichtlinien**.
3. Öffnen Sie die Registrierungsrichtlinie (z. B. "B2C\_1\_SiUp"), indem Sie darauf klicken. Klicken Sie oben auf dem Blatt auf **Bearbeiten**.
4. Klicken Sie auf **Registrierungsattribute**, und wählen Sie das benutzerdefinierte Attribut aus (z. B. "ShoeSize"). Klicken Sie auf **OK**.
5. Klicken Sie auf **Anwendungsansprüche**, und wählen Sie das benutzerdefinierte Attribut aus. Klicken Sie auf **OK**. 
6. Klicken Sie oben auf dem Blatt auf **Speichern**. Fertig.

Mit dem Feature "Jetzt ausführen" für die Richtlinie können Sie die Benutzererfahrung überprüfen. "ShoeSize" sollte jetzt in der Liste der Attribute angezeigt werden, die während der Registrierung von Kunden erfasst werden, sowie in dem Token, das zurück an die Anwendung gesendet wird.

<!---HONumber=AcomDC_0107_2016-->