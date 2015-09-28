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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Ein Hilfsprogramm, mit dem das Anpassungsfeature für die Seitenbenutzeroberfläche (UI) veranschaulicht werden kann

Dies ist ein Begleitartikel zum [Hauptartikel](active-directory-b2c-reference-ui-customization) über das Anpassungsfeature für die Seitenbenutzeroberfläche (UI) in Azure Active Directory (AD) B2C.

Mit den unten angegebenen Schritte können Sie die Nutzung des Anpassungsfeatures für die Seiten-UI mit unserem Beispielinhalt üben. Nur zur Veranschaulichung ist unser Beispielinhalt **statisches** HTML5.

1. Laden Sie unseren Beispielinhalt in den [Azure-Blobspeicher](http://azure.microsoft.com/documentation/services/storage/) herunter, machen Sie ihn öffentlich über HTTPS zugänglich, und aktivieren Sie CORS (Cross-Origin Resource Sharing) für diese URLs.
2. Modifizieren Sie die Anpassungseinstellungen für die Seiten-UI in Ihrer vorhandenen Registrierungsrichtlinie, und geben Sie die oben aufgeführten URLs an.

Bevor Sie beginnen:

- Erstellen Sie eine der [hier](active-directory-b2c-overview.md) aufgeführten Azure AD B2C-Schnellstartanwendungen. Dabei erstellen Sie eine Registrierungsrichtlinie, die Sie hier ändern.
- Laden Sie das Hilfsprogramm von [hier](https://github.com/AzureADSamples/B2C-AzureBlobStorage-Client) herunter.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### Schritt 1: Hochladen unseres Beispielinhalts in die richtigen Speicherorte

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.
2. Klicken Sie auf **+ Neu** -> **Daten und Speicher** -> **Speicherkonto**. Sie benötigen ein Azure-Abonnement, um ein Azure-Blobspeicherkonto zu erstellen. Sie können sich [hier](https://azure.microsoft.com/de-DE/pricing/free-trial/) für eine kostenlose Testversion registrieren.
3. Geben Sie einen **Namen** für das Speicherkonto an (z. B. "contoso.core.windows.net"), und treffen Sie die entsprechende Auswahl für **Tarif**, **Ressourcengruppe** und **Abonnement**. Stellen Sie sicher, dass die Option **An Startmenü anheften** aktiviert ist. Klicken Sie auf **Erstellen**.
4. Wechseln Sie zurück zum Startmenü, und klicken Sie auf das Speicherkonto, das Sie gerade erstellt haben.
5. Klicken Sie im Abschnitt **Zusammenfassung** auf **Container** und dann auf **+ Hinzufügen**.
6. Geben Sie einen **Namen** für den Container an (z. B. "b2c"), und wählen Sie **Blob** als **Zugriffstyp** aus. Klicken Sie auf **OK**.
7. Der Container, den Sie erstellt haben, wird in der Liste auf dem Blatt **Blobs** angezeigt. Notieren Sie sich die URL des Containers. Sie sollte beispielsweise wie folgt aussehen: `https://contoso.blob.core.windows.net/b2c`. Schließen Sie das Blatt **Blobs**.
8. Klicken Sie auf dem Blatt für das Speicherkonto auf **Schlüssel**, und notieren Sie sich die Werte der Felder **Speicherkontoname** und **Primärer Zugriffsschlüssel**.

    > [AZURE.NOTE]Der **primäre Zugriffsschlüssel** ist eine wichtige Anmeldeinformation.

9. Führen Sie das Hilfsprogramm aus, und geben Sie im Programm die Werte für **Speicherkontoname** und **Primärer Zugriffsschlüssel** an, die Sie sich im vorherigen Schritt notiert haben. Dadurch wird unser Beispielinhalt in Ihr Speicherkonto hochgeladen.
10. Stellen Sie sicher, dass der Inhalt ordnungsgemäß hochgeladen wurde, bevor Sie versuchen, auf `https://contoso.blob.core.windows.net/b2c/idp.html` in einem Browser zuzugreifen. Sie können auch [http://test-cors.org/](http://test-cors.org/) verwenden, um sicherzustellen, dass der Inhalt jetzt für CORS aktiviert ist (suchen Sie im Ergebnis nach `XHR status: 200`).

### Schritt 2: Ändern der Anpassungsoptionen für die Seiten-UI in Ihrer Registrierungsrichtlinie

1. Melden Sie sich bei Ihrem Verzeichnis im [Azure-Vorschauportal](https://portal.azure.com) an, und navigieren Sie zum Blatt mit B2C-Features.
2. Klicken Sie auf **Registrierungsrichtlinien**, und klicken Sie dann auf Ihre Anmelderichtlinie (z. B. "B2C\_1\_SiIn").
3. Klicken Sie auf **Anpassung der Seiten-UI** und dann auf **Auswahlseite für den Identitätsanbieter**.
4. Stellen Sie die Option **Benutzerdefinierte Vorlage verwenden** auf **Ja** ein. Geben Sie im Feld **Benutzerdefinierter Seiten-URI** die entsprechende URL des Inhalts an, den Sie in Ihr Speicherkonto hochgeladen haben. Beispiel: `https://contoso.blob.core.windows.net/b2c/idp.html`. Klicken Sie auf **OK**.
5. Klicken Sie auf **Registrierungsseite für lokales Konto**. Stellen Sie die Option **Benutzerdefinierte Vorlage verwenden** auf **Ja** ein. Geben Sie im Feld **Benutzerdefinierter Seiten-URI** die entsprechende URL des Inhalts an, den Sie in Ihr Speicherkonto hochgeladen haben. Beispiel: `https://contoso.blob.core.windows.net/b2c/su.html`. Klicken Sie zweimal auf **OK**.
6. Klicken Sie auf **Speichern**.
7. Klicken Sie auf den Befehl **Jetzt ausführen** oben auf dem Blatt. Wählen Sie "B2C-App" in der Dropdownliste **Anwendungen** und `https://localhost:44321/` in der Dropdownliste **Antwort-URL/Umleitungs-URI** aus. Klicken Sie auf die Schaltfläche **Jetzt ausführen**.
8. Eine neue Browserregisterkarte wird geöffnet, und Sie können die Benutzeroberfläche für die Registrierung bei Ihrer Anwendung mit dem neuen Inhalt verwenden.

> [AZURE.NOTE]Beachten Sie, dass es bis zu einer Minute dauert, bis die Richtlinienänderungen wirksam sind.

<!---HONumber=Sept15_HO3-->