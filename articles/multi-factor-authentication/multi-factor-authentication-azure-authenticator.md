<properties 
	pageTitle="Azure Authenticator-App für Mobiltelefone" 
	description="Erfahren Sie, wie Sie Azure Authenticator auf die neueste Version aktualisieren können." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/24/2015" 
	ms.author="billmath"/>



# Wechseln zur neuen Azure Authenticator-App

Die alte Multi-Factor Authentication wird durch die Veröffentlichung der Azure Authenticator App für [Windows Phone](http://www.windowsphone.com/de-DE/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) und [iOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) abgelöst. Die Multi-Factor Authentication-App kann weiterhin verwendet werden. Sollten Sie sich jedoch entscheiden, zur neuen Azure Authenticator-App zu wechseln, finden Sie in diesem Artikel hilfreiche Informationen.


## Wechseln zur neuen Azure Authenticator-App 

**Schritt 1:** Installieren Sie Azure Authenticator.

![Cloud](./media/multi-factor-authentication-azure-authenticator/home.png)

**Schritt 2:** Aktivieren Sie Ihre Konten in der neuen App.

Stellen Sie zunächst sicher, dass Sie für das hinzuzufügende Konto den QR-Code der den Code und die URL für die manuelle Eingabe zur Hand haben.

> [AZURE.NOTE]Sie wissen nicht, wie Sie den QR-Code abrufen können? Für Hilfe wenden Sie sich an den Helpdesk.
> 
> Das Aktivieren Ihres Kontos in der neuen App funktioniert nicht? Wenden Sie sich an den Helpdesk.
>


Wenn Sie den QR-Code zur Hand haben, starten Sie die App. Klicken Sie auf "+".


![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/addaccount.png)

Hierdurch wird die Kamera zum Scannen des QR-Codes gestartet. Wenn Sie den QR-Code nicht scannen können, können Sie die Daten auch manuell eingeben.

Überprüfen Sie die Seiten für Konten. Wenn das neue Konto angezeigt wird, war die Aktivierung erfolgreich.


Führen Sie diesen Schritt für alle Konten durch, die Sie zur neuen App migrieren möchten.



**Schritt 3:** Deinstallieren Sie die alte Multi-Factor Authentication-App von Ihrem Telefon.

Nachdem Sie der neuen App alle Konten hinzugefügt haben, deinstallieren Sie die alte Anwendung von Ihrem Telefon.

Sie möchten lediglich einzelne Konten aus der alten App entfernen? Tippen Sie auf das Konto. Daraufhin wird eine Option zum Löschen angezeigt.

![Konto entfernen](./media/multi-factor-authentication-azure-authenticator/remove.png)

## Hinzufügen eines Kontos mit dem Barcodescanner



- Navigieren Sie zuerst auf die Seite mit Ihren Einstellungen für die Sicherheitsüberprüfung. Informationen dazu, wie Sie auf diese Seite gelangen, finden Sie unter [Ändern der Sicherheitseinstellungen](multi-factor-authentication-end-user-manage-settings.md).

- Klicken Sie auf die Schaltfläche „Konfigurieren“.
 
![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Ein Bildschirm mit einem Barcode wird angezeigt.
  
![Barcode scannen](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Öffnen Sie nun die Azure Authenticator-App, um auf die Kontoseite geleitet zu werden. Es wird eine Liste mit den Konten angezeigt, die Sie eingerichtet haben. Klicken Sie auf das Pluszeichen (+), wenn Sie ein neues Konto hinzufügen möchten. Der Scanner wird geöffnet.

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Scannen Sie den Barcode. 

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Warten Sie, während das Konto aktiviert wird.

- Das ist schon alles. Das neue Konto sollte jetzt auf der Seite mit den Konten angezeigt werden.

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)


## Manuelles Hinzufügen eines Azure-Kontos

Wenn Sie ein Konto manuell hinzufügen möchten, können Sie wie folgt vorgehen:

- Navigieren Sie zuerst auf die Seite mit Ihren Einstellungen für die Sicherheitsüberprüfung. Informationen dazu, wie Sie auf diese Seite gelangen, finden Sie unter [Ändern der Sicherheitseinstellungen](multi-factor-authentication-end-user-manage-settings.md).

- Klicken Sie auf die Schaltfläche „Konfigurieren“.
 
![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Ein Bildschirm mit einem Barcode wird angezeigt. Beachten Sie den Code und die URL unterhalb des Barcodes.
  
![Barcode scannen](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Öffnen Sie nun die Azure Authenticator-App, um auf die Kontoseite geleitet zu werden. Es wird eine Liste mit den Konten angezeigt, die Sie eingerichtet haben. Klicken Sie auf das Pluszeichen (+), wenn Sie ein neues Konto hinzufügen möchten. Der Scanner wird geöffnet.

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Klicken Sie unten auf die Schaltfläche „Manuell eingeben“.

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Geben Sie den Code und die URL ein, der bzw. die auf der Seite mit dem Barcode angegeben ist. Diese werden in der mobilen App in den Feldern für Code und URL eingegeben. Dadurch wird die Aktivierung gestartet.

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/manual.png)

- Warten Sie, während das Konto aktiviert wird.

- Das ist schon alles. Das neue Konto sollte jetzt auf der Seite mit den Konten angezeigt werden.

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

## Manuelles Hinzufügen eines Nicht-Azure-Kontos

Wenn Sie andere Konten als ein Azure-Konto manuell hinzufügen möchten, z. B. Ihr Microsoft-Konto, können Sie wie folgt vorgehen:


- Ein Nicht-Azure-Konto lässt sich entweder durch das Scannen des QR-Codes oder durch Eingabe des geheimen Schlüssels hinzufügen.
- Wenn Sie den geheimen Schlüssel manuell eingeben möchten, müssen Sie diesen Schlüssel auf der Website anfordern, bei der das betreffende Konto besteht. In Outlook.com greifen Sie beispielsweise auf Ihre Kontoeinstellungen und dann auf die Sicherheitseinstellungen zu und wählen die Einrichtung einer Authentifizierungs-App aus. Wählen Sie „Ich kann den Barcode nicht scannen“ aus, um den geheimen Schlüssel zu erhalten.
- 

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/secretkey.png)

- Öffnen Sie die Azure Authenticator-App, um auf die Kontoseite geleitet zu werden. Es wird eine Liste mit den Konten angezeigt, die Sie eingerichtet haben. Klicken Sie auf das Pluszeichen (+), wenn Sie ein neues Konto hinzufügen möchten. Der Scanner wird geöffnet.

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Sie können entweder den QR-Code scannen oder unten auf die Schaltfläche „Manuell eingeben“ klicken. Beim Scannen des QR-Codes entfällt der nachfolgende Schritt und die Aktivierung startet sofort.

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Bei der manuellen Eingabe des geheimen Schlüssels sind der Kontoname und der geheime Schlüssel zusammen auf der Seite mit dem Barcode anzugeben. Diese werden in der mobilen App in den Feldern für Code und URL eingegeben. Dadurch wird die Aktivierung gestartet.

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/manual.png)

- Warten Sie, während das Konto aktiviert wird. Das neu hinzugefügte Konto sollte jetzt angezeigt werden.

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/msaccount.png)

- Schließen Sie den Prozess ab, indem Sie den Code eingeben, der unter Ihrem Konto angezeigt wird (hier: 875 756). Geben Sie ihn in das Feld auf der Seite ein, über die Sie den geheimen Schlüssel erhalten haben, und klicken Sie auf „Weiter“.  

![Konto hinzufügen](./media/multi-factor-authentication-azure-authenticator/verify.png)

## Hinzufügen eines Kontos per Touch ID
Die mobile Azure Authenticator-App unter iOS unterstützt die Touch ID. Bei der Azure Multi-Factor Authentication können Unternehmen zusätzlich zum Nachweis des Besitzes eines registrierten Geräts die Eingabe einer PIN fordern. Mit diesem neuen Feature ist es für iOS-Benutzer mit Touch ID-fähigen Geräten nicht mehr nötig, die PIN einzugeben. Nach der Einrichtung scannen die Benutzer einfach ihren Fingerabdruck ein, anstatt die PIN einzugeben, und tippen auf „Genehmigen“.

Das Einrichten von Touch-ID mit Azure Authenticator ist sehr einfach. Sie führen einfach einen normalen Überprüfungsvorgang mit PIN durch, und wenn Ihr Gerät die Touch ID unterstützt, übernehmen wir die Einrichtung für Sie automatisch.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

Danach müssen Sie zum Verifizieren Ihrer Anmeldung nur noch auf die erhaltene Pushbenachrichtigung tippen und Ihren Fingerabdruck scannen lassen, anstatt die PIN einzugeben.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

<!---HONumber=Nov15_HO1-->