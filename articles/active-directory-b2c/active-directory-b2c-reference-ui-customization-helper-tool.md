<properties
	pageTitle="Azure Active Directory B2C-Vorschau: Hilfsprogramm für die Anpassung der Seiten-UI | Microsoft Azure"
	description="Ein Hilfsprogramm, mit dem das Anpassungsfeature für die Seiten-UI in Azure Active Directory B2C veranschaulicht werden kann"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Ein Hilfsprogramm, mit dem das Anpassungsfeature für die Seitenbenutzeroberfläche (UI) veranschaulicht werden kann

Dies ist ein Begleitartikel zum [Hauptartikel zur Anpassung der Benutzeroberfläche](active-directory-b2c-reference-ui-customization.md) in Azure Active Directory B2C. In den unten angegebenen Schritten wird beschrieben, wie Sie das Feature zum Anpassen der Seitenbenutzeroberfläche verwenden. Zu diesem Zweck haben wir HTML- und CSS-Beispielinhalte bereitgestellt.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Beschaffen eines B2C-Mandanten

Bevor Sie Anpassungen vornehmen können, müssen Sie sich einen [Azure AD B2C-Mandanten beschaffen](active-directory-b2c-get-started.md), falls Sie diesen Schritt noch nicht durchgeführt haben.

## Erstellen einer Registrierungsrichtlinie

In den bereitgestellten Beispielinhalten werden zwei Seiten in einer [Registrierungsrichtlinie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) angepasst: die [IDP-Auswahlseite](active-directory-b2c-reference-ui-customization.md#identity-provider-selection-page) und die [Registrierungsseite für lokales Konto](active-directory-b2c-reference-ui-customization.md#local-account-sign-up-page). Fügen Sie beim [Erstellen der Registrierungsrichtlinie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) das lokale Konto (E-Mail-Adresse), Facebook und Google+ als **Identitätsanbieter** hinzu. Dies sind die einzigen Identitätsanbieter (IDPs), die für die HTML-Beispielinhalte akzeptiert werden.

## Registrieren einer Anwendung

Sie müssen das [Registrieren einer Anwendung](active-directory-b2c-app-registration.md) in Ihrem B2C-Mandanten durchführen, der zum Ausführen Ihrer Richtlinie verwendet werden kann. Nach dem Registrieren der Anwendung stehen Ihnen einige Optionen zum eigentlichen Ausführen der Registrierungsrichtlinie zur Verfügung:

- Erstellen Sie eine der [hier](active-directory-b2c-overview.md#getting-started) aufgeführten Azure AD B2C-Schnellstartanwendungen.
- Verwenden Sie die fertige Anwendung [Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net). Wenn Sie die Playground-Anwendung wählen, müssen Sie eine Anwendung mit dem **Umleitungs-URI** `https://aadb2cplayground.azurewebsites.net/` im B2C-Mandanten registrieren.
- Verwenden Sie die Schaltfläche **Jetzt ausführen** für Ihre Richtlinie im [Azure-Vorschauportal](https://portal.azure.com).

## Anpassen der Richtlinie

Um das Aussehen und Verhalten Ihrer Richtlinien anzupassen, müssen Sie zuerst HTML- und CSS-Dateien erstellen, indem Sie die speziellen Konventionen von Azure AD B2C verwenden. Anschließend können Sie Ihre statischen Inhalte an einen öffentlich zugänglichen Speicherort hochladen, damit von Azure AD B2C darauf zugegriffen werden kann. Dies kann ein eigener dedizierter Webserver, Azure-BLOB-Speicher, Azure CDN oder ein anderer Hostinganbieter für statische Ressourcen sein. Die einzigen Voraussetzungen sind, dass Ihre Inhalte per HTTPS zugänglich sind und dass darauf per CORS zugegriffen werden kann. Nachdem Sie Ihre statischen Inhalte im Web verfügbar gemacht haben, können Sie die Richtlinie so ändern, dass sie auf diesen Speicherort verweist, und die Inhalte für Ihre Endbenutzer bereitstellen. Im [Hauptartikel zur Anpassung der Benutzeroberfläche](active-directory-b2c-reference-ui-customization.md) wird ausführlich beschrieben, wie das Azure AD B2C-Feature für die Anpassung funktioniert.

Für dieses Lernprogramm haben wir bereits einige Beispielinhalte erstellt und auf einem Azure-BLOB-Speicher gehostet. Die Beispielinhalte umfassen eine sehr einfache Anpassung des Designs unseres fiktiven Unternehmens „Contoso B2C“. Gehen Sie folgendermaßen vor, um dies für Ihre eigene Richtlinie auszuprobieren:

1. Melden Sie sich bei Ihrem Mandanten im [Azure-Vorschauportal](https://portal.azure.com) an, und navigieren Sie zum Blatt mit den B2C-Features.
2. Klicken Sie auf **Registrierungsrichtlinien**, und klicken Sie dann auf Ihre Registrierungsrichtlinie (z. B. „b2c\_1\_sign\_up“).
3. Klicken Sie auf **Anpassung der Seiten-UI** und dann auf **Auswahlseite für den Identitätsanbieter**.
4. Stellen Sie die Option **Benutzerdefinierte Vorlage** verwenden auf **Ja** ein. Geben Sie im Feld **Benutzerdefinierter Seiten-URI** den Linktext `https://contosob2c.blob.core.windows.net/static/Index.html` ein. Klicken Sie auf **OK**.
5. Klicken Sie auf **Registrierungsseite für lokales Konto**. Stellen Sie die Option **Benutzerdefinierte Vorlage verwenden** auf **Ja** ein. Geben Sie im Feld **Benutzerdefinierter Seiten-URI** den Linktext `https://contosob2c.blob.core.windows.net/static/EmailVerification.html` ein. Klicken Sie zweimal auf **OK**, um die Blätter für die UI-Anpassung zu schließen.
6. Klicken Sie auf **Speichern**.

Nun können Sie Ihre angepasste Richtlinie ausprobieren. Sie können eine eigene Anwendung oder die Anwendung „AAD B2C Playground“ verwenden, aber Sie können im Blatt für die Richtlinie auch einfach auf den Befehl **Jetzt ausführen** klicken. Wählen Sie Ihre Anwendung in der Dropdownliste sowie den entsprechenden Umleitungs-URI aus. Klicken Sie auf die Schaltfläche **Jetzt ausführen**. Eine neue Browserregisterkarte wird geöffnet, und Sie können die Benutzeroberfläche für die Registrierung bei Ihrer Anwendung mit dem neuen Inhalt verwenden.

## Hochladen der Beispielinhalte in den Azure-BLOB-Speicher

Wenn Sie den Azure-BLOB-Speicher zum Hosten Ihrer Beispielinhalte verwenden möchten, können Sie ein eigenes Speicherkonto erstellen und unser B2C-Hilfstool zum Hochladen Ihrer Dateien nutzen.

#### Erstellen Sie ein Speicherkonto.

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.
2. Klicken Sie auf **+ Neu** -> **Daten und Speicher** -> **Speicherkonto**. Sie benötigen ein Azure-Abonnement, um ein Azure-Blobspeicherkonto zu erstellen. Sie können sich [hier](https://azure.microsoft.com/de-DE/pricing/free-trial/) für eine kostenlose Testversion registrieren.
3. Geben Sie einen **Namen** für das Speicherkonto an (z. B. „contoso“), und treffen Sie die entsprechende Auswahl für **Tarif**, **Ressourcengruppe** und **Abonnement**. Stellen Sie sicher, dass die Option **An Startmenü anheften** aktiviert ist. Klicken Sie auf **Erstellen**.
4. Wechseln Sie zurück zum Startmenü, und klicken Sie auf das Speicherkonto, das Sie gerade erstellt haben.
5. Klicken Sie im Abschnitt **Zusammenfassung** auf **Container** und dann auf **+ Hinzufügen**.
6. Geben Sie einen **Namen** für den Container an (z. B. „b2c“), und wählen Sie **Blob** als **Zugriffstyp** aus. Klicken Sie auf **OK**.
7. Der Container, den Sie erstellt haben, wird in der Liste auf dem Blatt **Blobs** angezeigt. Notieren Sie sich die URL des Containers. Sie sieht beispielsweise wie folgt aus: `https://contoso.blob.core.windows.net/b2c`. Schließen Sie das Blatt **Blobs**.
8. Klicken Sie auf dem Blatt für das Speicherkonto auf **Schlüssel**, und notieren Sie sich die Werte der Felder **Speicherkontoname** und **Primärer Zugriffsschlüssel**.

> [AZURE.NOTE]Der **primäre Zugriffsschlüssel** ist eine wichtige Anmeldeinformation.

#### Herunterladen des Hilfstools und der Beispieldateien

Sie können das [Azure-Blobspeicher-Hilfstool und die Beispieldateien hier als ZIP-Datei herunterladen](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) oder von GitHub klonen:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Dieses Repository enthält das Verzeichnis `sample_templates\contoso` mit HTML- und CSS-Beispieldaten und Bildern. Damit diese Vorlagen auf Ihr eigenes Azure-BLOB-Speicherkonto verweisen, müssen Sie die HTML-Dateien bearbeiten. Öffnen Sie `Index.htnml` und `EmailValidation.html`, und ersetzen Sie alle Instanzen von `https://localhost` durch die URL Ihres eigenen Containers, den Sie in den obigen Schritten kopiert haben. Es ist erforderlich, den absoluten Pfad der HTML-Dateien zu verwenden, da die HTML-Daten in diesem Fall von Azure AD unter der Domäne `https://login.microsoftonline.com` bereitgestellt werden.

#### Hochladen der Beispieldateien

Entzippen Sie in demselben Repository die Datei `B2CAzureStorageClient.zip`, und führen Sie die darin enthaltene Datei `B2CAzureStorageClient.exe` aus. Mit diesem Programm werden alle Dateien im von Ihnen angegebenen Verzeichnis einfach in Ihr Speicherkonto hochgeladen, und der CORS-Zugriff für diese Dateien wird aktiviert. Wenn Sie die obigen Schritte befolgt haben, wird in den HTML- und CSS-Dateien jetzt auf Ihr Speicherkonto verwiesen. Beachten Sie, dass der Name Ihres Speicherkontos der Teil ist, der vor `blob.core.windows.net` kommt, z. B. `contoso`. Sie können prüfen, ob der Inhalt ordnungsgemäß hochgeladen wurde, indem Sie versuchen, auf `https://{storage-account-name}.blob.core.windows.net/{container-name}/Index.html` in einem Browser zuzugreifen. Sie können auch [http://test-cors.org/](http://test-cors.org/) verwenden, um sicherzustellen, dass der Inhalt jetzt für CORS aktiviert ist (suchen Sie im Ergebnis nach „XHR status: 200“).

#### Erneutes Anpassen der Richtlinie

Nachdem Sie die Beispielinhalte in Ihr eigenes Speicherkonto hochgeladen haben, müssen Sie die Registrierungsrichtlinie so ändern, dass darauf verwiesen wird. Wiederholen Sie die Schritte aus dem obigen Abschnitt [Anpassen der Richtlinie](#customize-your-policy), und verwenden Sie dabei jetzt die URLs Ihres eigenen Speicherkontos. Der Speicherort der Datei `Index.html` würde beispielsweise `<url-of-your-container>/Index.html` lauten.
        
Sie können nun die Schaltfläche **Jetzt ausführen** oder Ihre eigene Anwendung verwenden, um die Richtlinie erneut auszuführen. Das Ergebnis sollte nahezu identisch sein, da Sie in beiden Fällen dieselben HTML- und CSS-Beispieldaten verwendet haben. Die Richtlinien verweisen jetzt aber auf Ihre eigene Instanz des Azure-BLOB-Speichers, und Sie können die Dateien wie gewünscht bearbeiten und neu hochladen. Weitere Informationen zum Anpassen der HTML- und CSS-Daten finden Sie im [Hauptartikel zur Anpassung der Benutzeroberfläche](active-directory-b2c-reference-ui-customization.md).

<!---HONumber=Oct15_HO1-->