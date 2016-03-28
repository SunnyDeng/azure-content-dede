<properties
	pageTitle="Azure AD Connect, mehrere Domänen"
	description="In diesem Dokument wird das Einrichten und Konfigurieren mehrerer Domänen der obersten Ebene mit Office 365 und Azure AD beschrieben."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="billmath"/>

# Unterstützung mehrerer Domänen für den Verbund mit Azure AD
Die folgende Dokumentation enthält eine Anleitung dazu, wie Sie mehrere Domänen der obersten Ebene und Unterdomänen verwenden, wenn Sie einen Verbund mit Office 365- oder Azure AD-Domänen erstellen.

## Unterstützung mehrerer Domänen der obersten Ebene
Für die Erstellung mehrerer Domänen der obersten Ebene als Verbund mit Azure AD sind einige zusätzliche Konfigurationsschritte erforderlich, die nicht benötigt werden, wenn ein Verbund mit nur einer Domäne der obersten Ebene erstellt wird.

Bei einem Verbund einer Domäne mit Azure AD werden für die Domäne in Azure mehrere Eigenschaften festgelegt. Eine wichtige Eigenschaft ist die IssuerUri-Eigenschaft. Dies ist ein URI, der von Azure AD zum Identifizieren der Domäne verwendet wird, der das Token zugeordnet ist. Der URI muss nicht in einen bestimmten Wert aufgelöst werden, aber es muss sich um einen gültigen URI handeln. Standardmäßig wird diese Eigenschaft von Azure AD auf den Wert des Verbunddienstbezeichners in Ihrer lokalen AD FS-Konfiguration festgelegt.

>[AZURE.NOTE]Der Bezeichner des Verbunddiensts ist ein URI, mit dem ein Verbunddienst eindeutig identifiziert wird. Der Verbunddienst ist eine Instanz von AD FS, die als Sicherheitstokendienst fungiert.

Sie können den IssuerUri mit dem folgenden PowerShell-Befehl anzeigen: `Get-MsolDomainFederationSettings - DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Ein Problem tritt auf, wenn wir mehr als eine Domäne der obersten Ebene hinzufügen möchten. Nehmen wir beispielsweise an, dass Sie einen Verbund zwischen Azure AD und Ihrer lokalen Umgebung eingerichtet haben. Für dieses Dokument verwende ich „bmcontoso.com“. Nun habe ich eine zweite Domäne der obersten Ebene hinzugefügt: „bmfabrikam.com“.

![Domänen](./media/active-directory-multiple-domains/domains.png)

Wenn wir versuchen, die Domäne „bmfabrikam.com“ in einen Verbund zu konvertieren, wird ein Fehler angezeigt. Der Grund hierfür ist, dass für Azure AD eine Einschränkung gilt. Es ist nicht zulässig, dass die IssuerUri-Eigenschaft für mehr als eine Domäne den gleichen Wert aufweist.
  

![Partnerverbundfehler](./media/active-directory-multiple-domains/error.png)

### SupportMultipleDomain-Parameter

Um dieses Problem zu umgehen, müssen wir einen anderen IssuerUri hinzufügen. Hierfür können wir den Parameter `-SupportMultipleDomain` verwenden. Dieser Parameter wird mit den folgenden Cmdlets verwendet:
	
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`

Mit diesem Parameter wird erreicht, dass Azure AD den IssuerUri so konfiguriert, dass er auf dem Namen der Domäne basiert. Dies ist für Verzeichnisse in Azure AD eindeutig. Mit dem Parameter kann der PowerShell-Befehl erfolgreich abgeschlossen werden.

![Partnerverbundfehler](./media/active-directory-multiple-domains/convert.png)

Wenn Sie sich die Einstellungen der neuen Domäne „bmfabrikam.com“ ansehen, erkennen Sie Folgendes:

![Partnerverbundfehler](./media/active-directory-multiple-domains/settings.png)

Beachten Sie, dass `-SupportMultipleDomain` nicht zu einer Änderung der anderen Endpunkte führt, die weiterhin so konfiguriert sind, dass sie auf unseren Verbunddienst unter „adfs.bmcontoso.com“ verweisen.

Außerdem wird mit `-SupportMultipleDomain` sichergestellt, dass das AD FS-System den richtigen Issuer-Wert in Token einfügt, die für Azure AD ausgegeben werden. Dazu wird der Domänenteil des Benutzerprinzipalnamens (UPN) verwendet und als Domäne im IssuerUri festgelegt, d. h. https://{upn suffix}/adfs/services/trust.

Während der Authentifizierung in Azure AD oder Office 365 wird daher das IssuerUri-Element im Token des Benutzers verwendet, um die Domäne in Azure AD zu finden. Wenn keine Übereinstimmung gefunden wird, schlägt die Authentifizierung fehl.

Wenn der UPN eines Benutzers beispielsweise bsimon@bmcontoso.com lautet, wird das IssuerUri-Element in dem von AD FS ausgestellten Token auf http://bmcontoso.com/adfs/services/trust festgelegt. Dies entspricht der Azure AD-Konfiguration und die Authentifizierung ist erfolgreich.

Unten sehen Sie die angepasste Anspruchsregel, die diese Logik implementiert:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]Zum Verwenden des Switch -SupportMultipleDomain bei dem Versuch, neue Domänen hinzuzufügen oder bereits hinzugefügte Domänen zu konvertieren, müssen Sie die Verbundvertrauensstellung so eingerichtet haben, dass diese standardmäßig unterstützt werden.


## Aktualisieren der Vertrauensstellung zwischen AD FS und Azure AD
Wenn Sie die Vertrauensstellung zwischen AD FS und Ihrer Instanz von Azure AD nicht eingerichtet haben, müssen Sie diese Vertrauensstellung unter Umständen neu erstellen. Dies liegt daran, dass für die IssuerUri der Standardwert festgelegt wird, wenn sie anfänglich ohne den Parameter `-SupportMultipleDomain` eingerichtet wird. Im Screenshot unten sehen Sie, dass die IssuerUri auf https://adfs.bmcontoso.com/adfs/services/trust festgelegt ist.

Wir erhalten jetzt also den folgenden Fehler, wenn wir im Azure AD-Portal erfolgreich eine neue Domäne hinzugefügt und anschließend versucht haben, diese mit `Convert-MsolDomaintoFederated -DomainName <your domain>` zu konvertieren.

![Partnerverbundfehler](./media/active-directory-multiple-domains/trust1.png)

Wenn Sie versuchen, den Switch `-SupportMultipleDomain` hinzuzufügen, erhalten Sie den folgenden Fehler:

![Partnerverbundfehler](./media/active-directory-multiple-domains/trust2.png)

Der einfache Versuch, `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` in der ursprünglichen Domäne auszuführen, führt ebenfalls zu einem Fehler.

![Partnerverbundfehler](./media/active-directory-multiple-domains/trust3.png)

Verwenden Sie die unten angegebenen Schritte, um eine weitere Domäne der obersten Ebene hinzuzufügen. Wenn Sie bereits eine Domäne hinzugefügt und den Parameter `-SupportMultipleDomain` nicht verwendet haben, beginnen Sie mit den Schritten für das Entfernen und Aktualisieren Ihrer ursprünglichen Domäne. Falls Sie noch keine Domäne der obersten Ebene hinzugefügt haben, können Sie mit den Schritten zum Hinzufügen einer Domäne mit der PowerShell von Azure AD Connect beginnen.

Führen Sie die folgenden Schritte aus, um die Microsoft Online-Vertrauensstellung zu entfernen und die ursprüngliche Domäne zu aktualisieren.

2.  Öffnen Sie auf Ihrem AD FS-Verbundserver die Option für die **AD FS-Verwaltung**. 
2.  Erweitern Sie auf der linken Seite die Optionen **Vertrauensstellungen** und **Vertrauensstellungen der vertrauenden Seite**.
3.  Löschen Sie auf der rechten Seite den Eintrag **Microsoft Office 365 Identity Platform**. ![Microsoft Online entfernen](./media/active-directory-multiple-domains/trust4.png)
1.  Führen Sie auf einem Computer, auf dem das [Azure Active Directory-Modul für Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installiert ist, Folgendes aus: `$cred=Get-Credential`.  
2.  Geben Sie den Benutzernamen und das Kennwort eines globalen Administrators für die Azure AD-Domäne ein, mit der Sie den Verbund erstellen.
2.  Geben Sie in PowerShell `Connect-MsolService -Credential $cred` ein.
4.  Geben Sie in PowerShell `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain` ein. Dies ist die Eingabe für die ursprüngliche Domäne. Mit den obigen Domänen ergibt sich Folgendes: `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`


Führen Sie die folgenden Schritte aus, um die neue Domäne der obersten Ebene mit PowerShell hinzuzufügen.

1.  Führen Sie auf einem Computer, auf dem das [Azure Active Directory-Modul für Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installiert ist, Folgendes aus: `$cred=Get-Credential`.  
2.  Geben Sie den Benutzernamen und das Kennwort eines globalen Administrators für die Azure AD-Domäne ein, mit der Sie den Verbund erstellen.
2.  Geben Sie in PowerShell `Connect-MsolService -Credential $cred` ein.
3.  Geben Sie in PowerShell `New-MsolFederatedDomain –SupportMultipleDomain –DomainName` ein.

Führen Sie die folgenden Schritte aus, um die neue Domäne der obersten Ebene mit Azure AD Connect hinzuzufügen.

1.	Starten Sie Azure AD Connect über den Desktop oder das Menü „Start“
2.	Wählen Sie die Option „Weitere Azure AD-Domäne hinzufügen“. ![Weitere Azure AD-Domäne hinzufügen](./media/active-directory-multiple-domains/add1.png)
3.	Geben Sie Ihre Anmeldeinformationen für Azure AD und Active Directory ein.
4.	Wählen Sie die zweite Domäne aus, die Sie für den Verbund konfigurieren möchten. ![Weitere Azure AD-Domäne hinzufügen](./media/active-directory-multiple-domains/add2.png)
5.	Klicken Sie auf „Installieren“.


### Überprüfen der neuen Domäne der obersten Ebene
Mit dem PowerShell-Befehl `Get-MsolDomainFederationSettings - DomainName <your domain>` können Sie den aktualisierten IssuerUri anzeigen. Im folgenden Screenshot ist dargestellt, dass die Verbundeinstellungen für die ursprüngliche Domäne http://bmcontoso.com/adfs/services/trust aktualisiert wurden.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Außerdem wurde der IssuerUri für die neue Domäne auf https://bmfabrikam.com/adfs/services/trust festgelegt.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)


##Unterstützung für Unterdomänen
Wenn Sie eine Unterdomäne hinzufügen, erbt sie die Einstellungen der übergeordneten Domäne. Dies liegt an der Art und Weise, wie Azure AD Domänen behandelt. Dies bedeutet, dass der IssuerUri mit den übergeordneten Elementen übereinstimmen muss.

Angenommen, Sie verfügen über „bmcontoso.com“ und fügen dann „corp.bmcontoso.com“ hinzu. Dies bedeutet, dass der IssuerUri für einen Benutzer von „corp.bmcontoso.com“ wie folgt lauten muss: ****http://bmcontoso.com/adfs/services/trust.** Mit der oben für Azure AD implementierten Standardregel wird aber ein Token mit folgendem Aussteller generiert: ****http://corp.bmcontoso.com/adfs/services/trust.**. Dies stimmt nicht mit dem erforderlichen Wert der Domäne überein, und für die Authentifizierung tritt ein Fehler auf.

### Aktivieren der Unterstützung für Unterdomänen
Um dieses Problem zu umgehen, muss die AD FS-Vertrauensstellung der vertrauenden Seite für Microsoft Online aktualisiert werden. Hierzu müssen Sie eine benutzerdefinierte Anspruchsregel so konfigurieren, dass beim Erstellen des benutzerdefinierten Issuer-Werts alle Unterdomänen aus dem UPN-Suffix des Benutzers entfernt werden.

Dies ist mit dem folgenden Anspruch möglich:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));

Führen Sie die folgenden Schritte aus, um einen benutzerdefinierten Anspruch zur Unterstützung von Unterdomänen hinzuzufügen.

1.	Öffnen Sie die AD FS-Verwaltung.
2.	Klicken Sie mit der rechten Maustaste auf die Microsoft Online-Vertrauensstellung der vertrauenden Seite, und wählen Sie „Anspruchsregeln bearbeiten“.
3.	Wählen Sie die dritte Anspruchsregel aus, und führen Sie die Ersetzung durch. ![Anspruch bearbeiten](./media/active-directory-multiple-domains/sub1.png)
4.	Ersetzen Sie den aktuellen Anspruch:
    
	    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
    	
	durch
    
	    `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
	
![Anspruch ersetzen](./media/active-directory-multiple-domains/sub2.png)
5.	Klicken Sie auf "OK". Klicken Sie auf „Übernehmen“. Klicken Sie auf "OK". Schließen Sie die AD FS-Verwaltung.

<!---HONumber=AcomDC_0316_2016-->