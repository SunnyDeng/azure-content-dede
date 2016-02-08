<properties
	pageTitle="Azure Active Directory-Domänendienste (Vorschau): Handbuch zur Problembehandlung | Microsoft Azure"
	description="Handbuch zur Problembehandlung für Azure AD-Domänendienste"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Azure AD-Domänendienste *(Vorschau)* – Handbuch zur Problembehandlung
Dieser Artikel enthält Tipps zur Behandlung von Problemen, die beim Einrichten oder Verwalten von Azure Active Directory-Domänendiensten auftreten können.


### Sie können die Azure AD-Domänendienste für Ihr Azure AD-Verzeichnis nicht aktivieren.
Wenn Sie die Azure AD-Domänendienste für Ihr Verzeichnis aktivieren möchten und dies entweder fehlschlägt oder wieder zurück zu „Deaktiviert“ wechselt, führen Sie die folgenden Schritte zur Problembehandlung aus:

- Vergewissern Sie sich, dass im gleichen virtuellen Netzwerk keine Domäne mit dem gleichen Domänennamen besteht. Angenommen, im ausgewählten virtuellen Netzwerk befindet sich eine Domäne namens „contoso.com“. Sie versuchen nun, in diesem virtuellen Netzwerk eine verwaltete Domäne der Azure AD-Domänendienste mit dem gleichen Domänennamen („contoso.com“) zu aktivieren. Beim Aktivieren der Azure AD-Domänendienste tritt daraufhin ein Fehler auf. Der Grund dafür ist ein Namenskonflikt in Bezug auf den Domänennamen in diesem virtuellen Netzwerk. In dem Fall müssen Sie einen anderen Namen verwenden, um die verwaltete Domäne der Azure AD-Domänendienste einzurichten. Alternativ können Sie auch die Bereitstellung der bestehenden Domäne aufheben und mit der Aktivierung der Azure AD-Domänendienste fortfahren.

- Prüfen Sie, ob sich in Ihrem Azure AD-Verzeichnis eine Anwendung namens „Azure AD Domain Services Sync“ befindet. In dem Fall müssen Sie diese Anwendung löschen und die Azure AD-Domänendienste erneut aktivieren. Führen Sie die folgenden Schritte aus, um zu prüfen, ob diese Anwendung existiert, und sie ggf. zu löschen:

  1. Navigieren Sie zum **Azure-Verwaltungsportal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Wählen Sie den Knoten **Active Directory** im linken Bereich aus.
  3. Wählen Sie den Azure AD-Mandanten (Verzeichnis) aus, für den Sie Azure AD-Domänendienste aktivieren möchten.
  4. Navigieren Sie zur Registerkarte **Anwendungen**.
  5. Wählen Sie in der Dropdownliste die Option **Anwendungen im Besitz meines Unternehmens** aus.
  6. Suchen Sie nach einer Anwendung mit dem Namen **Azure AD Domain Services Sync**. Wenn die Anwendung vorhanden ist, löschen Sie sie.
  7. Aktivieren Sie die Azure AD-Domänendienste erneut, sobald Sie die Anwendung gelöscht haben.


### Benutzer können sich nicht an der verwalteten Domäne der Azure AD-Domänendienste anmelden
Falls sich ein oder mehrere Benutzer innerhalb Ihres Azure AD-Mandanten nicht an der neu erstellten verwalteten Domäne anmelden können, können Sie die folgenden Problembehandlungsschritte ausführen:

- Stellen Sie sicher, dass Sie die [Aktivierung der Kennwortsynchronisierung](active-directory-ds-getting-started-password-sync.md) in Übereinstimmung mit den Schritten durchgeführt haben, die im Leitfaden zu den ersten Schritten angegeben sind.

- Stellen Sie sicher, dass das betreffende Benutzerkonto kein externes Konto des Azure AD-Mandanten ist. Beispiele für externe Konten sind Microsoft-Konten (z. B. „joe@live.com“) oder Benutzerkonten aus einem externen Azure AD-Verzeichnis. Da Azure AD-Domänendienste nicht über Anmeldeinformationen für diese Benutzerkonten verfügen, können sich diese Benutzer nicht an der verwalteten Domäne anmelden.

- Stellen Sie sicher, dass das UPN-Präfix des betreffenden Benutzerkontos (d. h. der erste Teil des Benutzerprinzipalnamens) im Azure AD-Mandanten weniger als 20 Zeichen lang ist. Beispielsweise ist beim UPN „joereallylongnameuser@contoso.com“ das Präfix („joereallylongnameuser“) länger als 20 Zeichen, und dieses Konto wird nicht in der verwalteten Azure Active Directory-Domänendienste-Domäne verfügbar sein.

- **Synchronisierte Konten:** Falls die betroffenen Benutzerkonten über ein lokales Verzeichnis synchronisiert werden, prüfen Sie Folgendes:
    - Sie haben die [neueste empfohlene Version von Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect) bereitgestellt bzw. das Update darauf durchgeführt.
    - Sie haben Azure AD Connect so konfiguriert, dass [eine vollständige Synchronisierung ausgeführt wird](active-directory-ds-getting-started-password-sync.md).
    - Je nach Größe Ihres Verzeichnisses kann es einige Zeit dauern, bis die Benutzerkonten und Anmeldeinformationshashes in den Azure AD-Domänendiensten verfügbar sind. Warten Sie lange genug, bevor Sie versuchen, die Authentifizierung erneut durchzuführen (abhängig von der Größe Ihres Verzeichnisses – für große Verzeichnisse zwischen einigen Stunden und ein bis zwei Tagen).

- **Nur-Cloudkonten**: Wenn das betroffene Benutzerkonto nur für die Cloud gilt, müssen Sie sicherstellen, dass der Benutzer sein Kennwort geändert hat, nachdem Sie die Azure AD-Domänendienste aktiviert haben. Dieser Schritt führt dazu, dass die Anmeldeinformationshashes für die Azure AD-Domänendienste generiert werden.


### So erreichen Sie uns
Falls Sie Probleme mit Ihrer verwalteten Domäne haben, sollten Sie überprüfen, ob sich das Problem mit den Schritten in diesem Leitfaden zur Problembehandlung beheben lässt. Falls sich die Probleme nicht beseitigen lassen, können Sie sich gern an uns wenden:

- **E-Mail:** Sie können uns auch eine E-Mail an [Azure AD-Domänendienste – Feedback](mailto:aaddsfb@microsoft.com) senden. Geben Sie dabei die Mandanten-ID Ihres Azure AD-Verzeichnisses und den für Ihre AAD-Domänendienste konfigurierten Domänennamen an, damit wir das Problem untersuchen können.
- **[Azure Active Directory User Voice-Kanal](https://feedback.azure.com/forums/169401-azure-active-directory/):** Stellen Sie Ihrer Frage das Kürzel **„AADDS“** voran, um uns zu erreichen.

<!---HONumber=AcomDC_0128_2016-->