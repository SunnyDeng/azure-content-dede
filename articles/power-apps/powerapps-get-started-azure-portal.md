<properties
	pageTitle="Was ist PowerApps Enterprise und erste Schritte | Microsoft Azure"
	description="Erste Schritte mit PowerApps Enterprise und Erstellen der App Service-Umgebung"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>

# Was ist Microsoft PowerApps Enterprise?

Microsoft PowerApps Enterprise ist ein neuer Microsoft Azure-Dienst. Mit PowerApps Enterprise können Geschäftsbenutzer innerhalb Ihres Unternehmens mobile Apps erstellen, und IT-Administratoren können diese Apps umfassend verwalten.

In einer Benutzeroberfläche im Office-Stil – mit Multifunktionsleisten und Excel-Formeln – können Geschäftsbenutzer Apps erstellen, die folgende Möglichkeiten bieten:

- Anzeigen von Daten mithilfe von Linien-, Kreis- und Balkendiagrammen, genau wie in Excel.
- Erstellen von Benutzeroberflächen mit Schaltflächen, Einfügen von Text und Datumsformatierung.
- Hinzufügen von Multiple-Choice-Steuerelementen einschließlich Listenfeldern, Dropdownlisten und Optionsfeldern.
- Hochladen von Bildern, Fotografieren und Abspielen von Audio- und Videodateien.
- Verwendung von "Back-End"-Systemen wie Excel und SQL Server, um Informationen anzuzeigen und zu aktualisieren.
- Hinzufügen vorgefertigter App Service-Connector zu Ihren Apps, die Verbindungen mit PaaS-Programmen wie Twitter und SharePoint herstellen.

IT-Administratoren können Apps verwalten, die Geschäftsbenutzer in ihrem Unternehmen erstellt haben, inklusive:

- Verwalten dieser Apps und Verwalten des Benutzerzugriffs auf diese Apps.
- Erstellen von APIs und Verbindungen mit verschiedenen Datenquellen. 
- Verwalten des Benutzerzugriffs für APIs und Verbindungen mit diesen Datenquellen. 

## Wie fange ich an?

Ermitteln Sie zunächst, ob Sie einen neuen Mandanten für Azure Active Directory (Azure AD) erstellen müssen. Wenn Sie bereits über einen AD-Mandanten verfügen, aktivieren Sie einfach PowerApps Enterprise im Azure-Portal, fügen Sie Ihre APIs und Verbindungen hinzu und beginnen Sie mit der Verwaltung (beginnen Sie mit *Schritt 2* dieses Themas).

Wenn Sie nicht über einen AD-Mandanten verfügen, dann erstellen Sie einen neuen AD-Mandanten, aktivieren Sie PowerApps Enterprise im Azure-Portal, fügen Sie Ihre APIs und Verbindungen hinzu und beginnen Sie mit der Verwaltung.

In diesem Thema werden die Einzelheiten aufgeführt.

## Schritt 1: Erstellen eines neuen, bzw. Verwenden eines vorhandenen Azure AD-Mandanten

Zum Einstieg in PowerApps Enterprise benötigen Sie einen Azure Active Directory-Mandanten (Azure AD). Ein Mandant ist eine dedizierte Instanz des Azure AD-Diensts.

Wenn Ihre Organisation oder Ihr Unternehmen sich für einen Microsoft Azure-Clouddienst wie Microsoft Intune oder Office 365 registriert, empfängt Ihre Organisation automatisch einen AD-Mandanten und besitzt ihn. Jeder AD-Mandant ist von anderen Azure AD-Mandanten eindeutig abgegrenzt.

Gehen Sie folgendermaßen vor, um festzustellen, ob Sie bereits einen Mandanten besitzen, oder wie Sie einen neuen erstellen können.

#### bereits für ein Office 365-Abonnement verfügen
Wenn Sie ein Office 365-Abonnement (oder Microsoft Dynamic CRM Online, Enterprise Mobility Suite oder einen anderen Microsoft-Dienst) haben, besitzen Sie ein kostenloses Abonnement für Azure Active Directory. Mit Azure AD können Sie Benutzer- und Gruppenkonten erstellen und verwalten. Wenn Sie sich nicht beim Azure-Portal anmelden können, müssen Sie wahrscheinlich das Abonnement aktivieren. Suchen Sie hierzu das [klassische Azure-Portal](https://manage.windowsazure.com/) auf, und führen Sie die einmalige Registrierung aus. Greifen Sie mit diesen [Schritten](https://technet.microsoft.com/library/dn832618.aspx) auf Ihren Azure AD-Mandanten zu.

#### Sie verfügen bereits über ein Azure-Abonnement in Verbindung mit einem Microsoft-Konto
Wenn Sie sich zuvor mit Ihrem persönlichen Microsoft-Konto für ein Azure-Abonnement registriert haben (per Hotmail oder live), verfügen Sie bereits über einen Mandanten! Im [klassischen Azure-Portal](https://manage.windowsazure.com/) wird der **Standardmandant** unter **Alle Elemente** und **Active Directory** aufgeführt. Sie können diesen Mandanten wie gewünscht nutzen, sollten aber das Erstellen eines Organisationsadministratorkontos erwägen.

Führen Sie dazu die folgenden Schritte aus. Alternativ können Sie einen neuen Mandanten und in diesem Mandanten einen Administrator erstellen, indem Sie einen ähnlichen Prozess befolgen.

1.	Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) mit Ihrem persönlichen Konto an.
2.	Wählen Sie in der linken Menüleiste **Active Directory** aus. 
3.	Wählen Sie in der Liste der verfügbaren Verzeichnisse den Eintrag **Standardverzeichnis** aus.
4.	Wählen Sie oben die Registerkarte **Benutzer**. Ein einzelner Benutzer mit "Microsoft-Konto" ist in der Spalte "Erstellt aus" aufgeführt.
5.	Wählen Sie unten **Benutzer hinzufügen**. 
6.	Geben Sie in **Formular zum Hinzufügen eines Benutzers** folgende Details ein:  
	
	Eigenschaft | Beschreibung
--- | ---
Benutzertyp | Neuer Benutzer in Ihrer Organisation
Benutzername | Wählen Sie einen Benutzernamen für diesen Administrator.
Vorname/Nachname/Anzeigename | Geben Sie Ihre Werte ein
Rolle | Globaler Administrator
Alternative E-Mail-Adresse | Geben Sie Ihren Wert ein
Optional | Multi-Factor Authentication aktivieren  
	
	Wählen Sie die Schaltfläche **ERSTELLEN**, um den Vorgang abzuschließen und das temporäre Kennwort anzeigen.

Notieren Sie nach Abschluss dieses temporäre Kennwort für den neuen Administrator. Um das temporäre Kennwort zu ändern, melden Sie sich bei [https://login.microsoftonline.com](https://login.microsoftonline.com) mit diesem neuen Benutzerkonto an, und ändern Sie das Kennwort. Sie können das Kennwort auch mithilfe einer alternativen E-Mail-Adresse direkt an den Benutzer senden.


#### Sie verfügen bereits über ein Azure-Abonnement, das einem Organisationskonto zugeordnet ist
Wenn Sie sich zuvor mit Ihrem Organisationskonto für ein Azure-Abonnement registriert haben, verfügen Sie bereits über einen Mandanten. Im [klassischen Azure-Portal](https://manage.windowsazure.com/) wird der Mandant unter **Alle Elemente** und auch **Active Directory** aufgeführt. Sie können diesen Mandanten gemäß Ihren Anforderungen nutzen. Sie können auch im Menü **Neu** in der Taskleiste am unteren Rand einen neuen Mandanten erstellen.

#### Sie verfügen über nichts des Obengenannten und möchten ganz von vorn beginnen
Wenn nichts des Obengenannten auf Sie zutrifft, registrieren Sie sich unter [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) mit einer neuen Organisation bei Azure. Sobald Sie registriert sind, verfügen Sie über einen eigenen Azure AD-Mandanten mit dem von Ihnen ausgewählten Domänennamen. Im [klassischen Azure-Portal](https://manage.windowsazure.com/) sehen Sie den Mandanten im linken Menü in **Active Directory**.

## Schritt 2: Erstellen eines neuen oder Verwenden eines vorhandenen Azure-Abonnements
Da Sie nun einen AD-Mandanten besitzen, können Sie ein neues Azure-Abonnement erstellen oder ein vorhandenes verwenden. Das Azure AD-Abonnement umfasst verschiedene Editionen. Für PowerApps Enterprise können Sie die Free-Edition verwenden. Wenn Sie jedoch den AAD-Proxy verwenden, um Hybridkonnektivität mit lokalen Daten zu erstellen, benötigen Sie die Basic- oder Premium-Edition.

In den [Azure Active Directory-Editionen](../active-directory/active-directory-editions.md) sind weitere Features aufgelistet.


## Schritt 3: Ihre Registrierung für PowerApps Enterprise in Ihrem Azure-Geschäftsabonnement
> [AZURE.NOTE] Für die folgenden Schritte muss der Abonnementadministrator sich beim Azure-Portal anmelden und eine Anfrage senden.

Da Sie nun über einen AD-Mandanten und ein Azure-Abonnement verfügen, können Ihre Geschäftsabonnementadministratoren sich für PowerApps Enterprise registrieren. Der Administrator kann auch Benutzer innerhalb Ihres Unternehmens zum 'Verwalten' von PowerApps – inklusive der Vergabe von Benutzerberechtigungen – hinzufügen und die im Rahmen Ihres Azure-Abonnements veröffentlichten PowerApps verwalten.

Ohne Registrierung für PowerApps Enterprise sehen Sie kein Zugriffsblatt, wenn Sie im [Azure-Portal](https://portal.azure.com/) nach PowerApps suchen. Um Ihr Unternehmen zu registrieren, kann der **Abonnementadministrator** unter [PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=716848) mit uns Kontakt aufnehmen, um weitere Informationen zu Preisen und zum Registrierungsprozess zu erhalten.

![][4]

Sobald Sie den Registrierungsprozess abgeschlossen haben und bereit sind, PowerApps Enterprise verwenden, können Sie:

- Benutzer in Ihrem Unternehmen hinzufügen und mithilfe [rollenbasierter Zugriffssteuerung](../role-based-access-control-configure.md) diesen Benutzern PowerApps-Administratorrollen für den Zugriff auf das PowerApps Enterprise-Portal zuweisen.
- Eine dedizierte App Service-Umgebung zum Hosten Ihrer PowerApps erstellen.
- APIs und Verbindungen für die Ausführung innerhalb Ihrer dedizierten App Service-Umgebung erstellen.
- Neben den in PowerApps erstellten Apps können Sie Ihrer App Service-Umgebung weitere Apps hinzufügen, einschließlich Web-Apps, mobiler Apps, API-Apps und Logik-Apps.

Im folgenden Beispiel registriert sich das Unternehmen Contoso für PowerApps. Auf diesem neuen **PowerApps**-Blatt sehen Sie eine Zusammenfassung der verschiedenen Typen von Apps, die mit dieser App Service-Umgebung erstellt werden. In **APIs verwalten** finden Sie eine Zusammenfassung der von Microsoft erstellten APIs (Microsoft-verwaltet) und sehen die von Contoso erstellten APIs (IT-verwaltet):

![Beispielunternehmen PowerApps Blatt][3]


## Schritt 4: Erstellen einer App Service-Umgebung
Erstellen Sie eine App Service-Umgebung zum Hosten Ihrer PowerApps-APIs und Verbindungen sowie von mobilen Apps, Web-Apps, API-Apps und Logik-Apps.

Eine App Service-Umgebung ist eine isolierte und dedizierte Umgebung, die Ihre sämtlichen Apps sicher ausführt. Computeressourcen werden jeweils einer App Service-Umgebung zur Verfügung gestellt und ausschließlich zur Ausführung Ihrer Apps eingesetzt. Wenn Sie sich für PowerApps Enterprise registrieren, wird eine dedizierte App Service-Umgebung zum Hosten der APIs und der von Ihren Apps verwendeten Verbindungen eingesetzt. Diese App Service-Umgebung ist ein "spezieller" Typ von App Service-Umgebung. Dies gilt insbesondere in folgenden Fällen:

- Sie können diese App Service-Umgebung für beliebige Zwecke verwenden. Sie ist an Ihr Unternehmen, nicht das Abonnement gebunden.
- Sie konfigurieren die APIs und Verbindungen, die von Ihren in PowerApps erstellten Apps verwendet werden sollen. Allerdings können Sie der gleichen App Service-Umgebung auch Web-Apps, mobile Apps, Logik-Apps und API-Apps hinzufügen. 
- Die Abrechnung ist festgelegt und in PowerApps Enterprise enthalten.  
- Die Skalierung wird automatisch für Sie verwaltet. Sie müssen nicht die Umgebung überwachen, um festzustellen, ob zusätzliche Computeressourcen erforderlich sind.

Die reguläre Azure App Service-Umgebung verfügt über andere Features. Nähere Informationen finden Sie in der [Einführung in die App Service-Umgebung](../app-service-app-service-environment-intro.md).

#### Anforderungen für den Einstieg

- Azure-Unternehmensabonnement
- Der Abonnementadministrator in Ihrem Unternehmen [hat Ihr Unternehmen für PowerApps Enterprise registriert](powerapps-get-started-azure-portal.md).
- Sie sind beim Azure-Portal als PowerApps-Administrator ("Besitzer" von PowerApps) oder Abonnementadministrator angemeldet.

### Erstellen einer App Service-Umgebung
> [AZURE.NOTE] Wenn die Option zum Erstellen der App Service-Umgebung nicht angezeigt wird, ist sie bereits für Ihren Mandanten erstellt. Wählen Sie zum Anzeigen der Details **Einstellungen**, um die App Service-Umgebung zu öffnen.

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie in der Taskleiste **Durchsuchen**: ![Durchsuchen nach PowerApps][1]
  
3. In der Liste können Sie scrollen, um PowerApps zu finden, oder in *powerapps* Folgendes eingeben: ![Suchen nach PowerApps][2]

4. Wählen Sie im **PowerApps**-Blatt **App Service-Umgebung für den Einstieg erstellen**, oder wählen Sie **App Service-Umgebung** unter *Einstellungen*: ![][5]

	> [AZURE.NOTE] Wenn Sie auf **App Service-Umgebung für den Einstieg erstellen** klicken, sehen Sie ein zusätzliches Blatt mit Details zur App Service-Umgebung. Klicken Sie zum Aufruf des Erstellungsblatts einfach auf diesem Blatt auf den "Erstellen"-Link.

5. Als Nächstes geben Sie den Namen ein, wählen Sie das Abonnement, das Sie verwenden möchten, wählen Sie eine neue Ressourcengruppe aus, bzw. erstellen Sie eine, und wählen Sie ein virtuelles Netzwerk aus. **Beachten Sie**, dass Sie die Auswahl eines virtuellen Netzwerks nicht mehr rückgängig machen können: ![][6] Informationen zur Zusammenarbeit virtueller Netzwerke mit einer App Service-Umgebung finden Sie unter [Erstellen einer App Service-Umgebung](../app-service-web-how-to-create-an-app-service-environment.md).

6. Wählen Sie **Hinzufügen**, um die Erstellung der App Service-Umgebung abzuschließen.

> [AZURE.TIP] Beim Erstellen der App Service-Umgebung mit PowerApps werden Sie nicht aufgefordert, die Computeressourcenpools zu konfigurieren. Dieser Schritt wird automatisch ausgeführt.

Denken Sie daran, dass Sie dieser App Service-Umgebung auch Web-Apps, mobile Apps, Logik-Apps und API-Apps hinzufügen können. In der Tat können Sie Ihrer Umgebung alles hinzufügen, was die App Service-Umgebung unterstützt.

### Hinzufügen von Administratoren zur Verwaltung der App Service-Umgebung

Um auf die App Service-Umgebung zuzugreifen, APIs, Verbindungen und andere Ressourcen zu erstellen, müssen Benutzer mit der Rolle "Besitzer" hinzugefügt werden.

1. Wählen Sie die App Service-Umgebung aus, die Sie gerade erstellt haben.
2. Wählen Sie in "Essentials" die Eigenschaft **Ressourcengruppe** aus. Daraufhin wird die Ressourcengruppe geöffnet, die die App Service-Umgebung enthält: ![][7]
3. Wählen Sie das Symbol "RBAC" zum Verwalten von Berechtigungen: ![][8] Das Hinzufügen von Benutzern und Zuweisen von Rollen erfolgt wie die [rollenbasierte Zugriffssteuerung]( https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/) innerhalb von Azure.

> [AZURE.NOTE] Sie können derzeit keine RBAC-Berechtigungen für die App Service-Umgebung zuweisen. Sie können RBAC-Berechtigungen auf der übergeordneten Ressourcengruppenebene zuweisen.

## Zusammenfassung und nächste Schritte
Ihr Unternehmen ist nun für PowerApps registriert und verfügt über eine App Service-Umgebung. Als Nächstes können Sie APIs und Verbindungen hinzufügen, die von den Apps verwendet werden können.

- [Überwachen Ihrer PowerApps-Apps](powerapps-manage-monitor-usage.md)
- [Entwickeln von APIs für PowerApps](powerapps-develop-api.md)
- [Hinzufügen einer neuen API, Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)
- [Aktualisieren einer vorhandenen API und ihrer Eigenschaften](powerapps-configure-apis.md)


[1]: ./media/powerapps-get-started-azure-portal/browseall.png
[2]: ./media/powerapps-get-started-azure-portal/allresources.png
[3]: ./media/powerapps-get-started-azure-portal/powerappsblade.png
[4]: ./media/powerapps-get-started-azure-portal/noaccess.png
[5]: ./media/powerapps-get-started-azure-portal/createase.png
[6]: ./media/powerapps-get-started-azure-portal/aseproperties.png
[7]: ./media/powerapps-get-started-azure-portal/aseessentials.png
[8]: ./media/powerapps-get-started-azure-portal/resourcegrouprbac.png

<!----HONumber=AcomDC_0128_2016-->