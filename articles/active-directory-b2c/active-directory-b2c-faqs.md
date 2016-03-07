<properties
	pageTitle="Preview-Version von Azure Active Directory B2C: Häufig gestellte Fragen | Microsoft Azure"
	description="Häufig gestellte Fragen zu Azure Active Directory B2C"
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
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Preview-Version von Azure Active Directory B2C: Häufig gestellte Fragen

Auf dieser Seite werden häufig gestellte Fragen zur Vorschau von Azure Active Directory (Azure AD) B2C beantwortet. Die Seite wird bei Bedarf aktualisiert.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### Kann ich Azure AD B2C-Features in meinem vorhandenen mitarbeiterbasierten Azure AD Directory-Mandanten nutzen?

Derzeit können Azure AD B2C-Features in Ihrem vorhandenen Azure AD-Mandanten noch nicht aktiviert werden. Es wird empfohlen, einen separaten Mandanten zu erstellen, um Ihre Endkunden mit Azure AD B2C-Features zu verwalten.

### Kann ich Azure AD B2C zum Bereitstellen der Anmeldung für soziale Netzwerke (Facebook und Google+) in Office 365 verwenden?

Azure AD B2C kann nicht mit Microsoft Office 365 verwendet werden. Es kann generell nicht für die Bereitstellung der Authentifizierung für SaaS-Apps (Office 365, Salesforce, Workday usw.) verwendet werden. Es ermöglicht nur die Identitäts- und Zugriffsverwaltung für endkundenorientierte Webanwendungen und mobile Anwendungen und ist nicht für Mitarbeiter- oder Partnerszenarien ausgelegt.

### Was sind lokale Konten in Azure AD B2C? Wie unterscheiden sie sich von Geschäfts-, Schul- oder Unikonten in Azure AD?

In einem Azure AD-Mandanten meldet sich jeder Benutzer im Mandanten (mit Ausnahme von Benutzern mit vorhandenen Microsoft-Konten) mit einer E-Mail-Adresse im Format `<xyz>@<tenant domain>` an. Hierbei ist `<tenant domain>` eine der überprüften Domänen im Mandanten oder die ursprüngliche `<...>.onmicrosoft.com`-Domäne. Dieser Kontotyp ist ein Geschäfts-, Schul- oder Unikonto.

In einem Azure AD B2C-Mandanten ist es bei den meisten Apps üblich, dass Benutzer sich mit einer beliebigen E-Mail-Adresse anmelden (z. B. joe@comcast.net, bob@gmail.com, sarah@contoso.com oder jim@live.com)). Diese Art von Konto ist ein lokales Konto. Jetzt werden auch beliebige Benutzernamen (einfache Zeichenfolgen) als lokale Konten unterstützt (z. B. joe, bob, sarah oder jim). Sie können eine dieser beiden lokalen Kontotypen im Azure AD B2C-Dienst auswählen.

### Welche Identitätsanbieter aus sozialen Netzwerken werden derzeit unterstützt? Welche sollen in Zukunft unterstützt werden?

Gegenwärtig werden Facebook, Google+, LinkedIn und Amazon unterstützt. Die Unterstützung für andere beliebte soziale Identitätsanbieter wird je nach Kundennachfrage hinzugefügt.

### Kann ich Bereiche konfigurieren, um von verschiedenen Identitätsanbietern aus sozialen Netzwerken mehr Informationen zu Endkunden zu erhalten?

Nein, aber dieses Feature ist geplant. Die Standardbereiche für unsere unterstützten sozialen Identitätsanbieter sind:

- Facebook: email
- Google+: email
- Amazon: profile
- LinkedIn: r\_emailaddress, r\_basicprofile

### Muss meine Anwendung unter Azure ausgeführt werden, damit sie mit Azure AD B2C funktioniert?

Nein, Sie können Ihre Anwendung überall hosten (in der Cloud oder lokal). Für die Interaktion mit Azure AD B2C muss nur die Fähigkeit zum Senden und Empfangen von HTTP-Anforderungen über öffentlich zugängliche Endpunkte möglich sein.

### Ich habe mehrere Azure AD B2C-Mandanten. Wie kann ich sie im Azure-Portal verwalten?

Jeder Azure AD B2C-Mandant verfügt im Azure-Portal über ein eigenes Blatt mit B2C-Funktionen. Informationen dazu, wie Sie im Azure-Portal zum Blatt mit den B2C-Funktionen eines bestimmten Mandanten navigieren können, finden Sie unter [Azure AD B2C-Vorschau: Registrieren Ihrer Anwendung](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade). Beim Wechseln zwischen Azure AD B2C-Verzeichnissen im Azure-Portal bleibt das Blatt mit den B2C-Funktionen in den meisten Browsern nicht geöffnet.

### Wie kann ich Bestätigungs-E-Mails anpassen (Inhalt und das Feld „Von:“), die von Azure AD B2C gesendet werden?

Verwenden Sie das [Feature für Unternehmensbranding](../active-directory/active-directory-add-company-branding.md), um den Inhalt von Bestätigungs-E-Mails anzupassen. Das Feld „Von:“ kann durch den Support geändert werden.

### Wie kann ich meine vorhandenen Benutzernamen, Kennwörter und Profile von meiner Datenbank zu Azure AD B2C migrieren?

Sie können Ihr Migrationstool mithilfe der Azure AD Graph-API schreiben. Einzelheiten finden Sie im [Graph-API-Beispiel](active-directory-b2c-devquickstarts-graph-dotnet.md). In Zukunft werden verschiedene Migrationsoptionen und Tools standardmäßig vorhanden sein.

### Welche Kennwortrichtlinie wird für lokale Konten in Azure AD B2C verwendet?

Die Kennwortrichtlinie von Azure AD B2C für lokale Konten basiert auf der Kennwortrichtlinie von Azure AD. Bei Azure AD B2C müssen sichere Kennwörter verwendet werden, und die Kennwörter laufen nicht ab. Weitere Informationen finden Sie in der [Kennwortrichtlinie von Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx).

### Kann ich Azure AD Connect zum Migrieren von Endkundenidentitäten, die in meinem lokalen Active Directory gespeichert sind, zu Azure AD B2C verwenden?

Nein. Azure AD Connect ist nicht für die Verwendung mit Azure AD B2C ausgelegt. In Zukunft werden verschiedene Migrationsoptionen und Tools standardmäßig vorhanden sein.

### Funktioniert Azure AD B2C in Verbindung mit CRM-Systemen wie Microsoft Dynamics?

Derzeit ist dies nicht möglich. Die Integration dieser Systeme ist aber geplant.

### Funktioniert Azure AD B2C in Verbindung mit SharePoint On-Premises 2016 oder früher?

Derzeit ist dies nicht möglich. Azure AD B2C bietet keine Unterstützung für SAML 1.1-Token, die von Portalen und E-Commerce-Anwendungen benötigt werden, die auf SharePoint On-Premises beruhen. Beachten Sie, dass Azure AD B2C nicht für das SharePoint-Freigabeszenario für externe Partner vorgesehen ist. Informieren Sie sich dazu unter [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### Sollte ich Azure AD B2C oder B2B zum Verwalten externer Identitäten verwenden?

Lesen Sie diesen Artikel über [externe Identitäten](../active-directory/active-directory-b2b-compare-external-identities.md), um mehr über das Anwenden der entsprechenden Features in Ihren Szenarien mit externen Identitäten zu erfahren.

### Welche Funktionen für Berichterstellung und Überwachung bietet Azure AD B2C? Sind die Funktionen mit denen in Azure AD Premium identisch?

Nein. Azure AD B2C unterstützt nicht den gleichen Berichtssatz wie Azure AD Premium. Für Azure AD B2C werden in Kürze grundlegende APIs für Berichterstellung und Auditing veröffentlicht.

### Kann ich die Benutzeroberfläche von Seiten lokalisieren, die von Azure AD B2C bereitgestellt werden? Welche Sprachen werden unterstützt?

Derzeit ist Azure AD B2C nur für Englisch optimiert. Es ist geplant, die Lokalisierungsfeatures so bald wie möglich bereitzustellen.

### Kann ich auf meinen von Azure AD B2C bereitgestellten Seiten für die Registrierung und Anmeldung meine eigenen URLs verwenden? Kann ich beispielsweise die URL von „login.microsoftonline.com“ in „login.contoso.com“ ändern?

Derzeit ist dies nicht möglich. Dieses Feature ist aber geplant. Beachten Sie auch, dass dies nicht durch das Überprüfen Ihrer Domäne auf der Registerkarte **Domänen** für den Mandanten im klassischen Azure-Portal erfolgen kann.

### Kann ich Azure AD B2C als Teil der Enterprise Mobility Suite erwerben?

Nein. Azure AD B2C ist ein Azure-Dienst mit nutzungsbasierter Bezahlung und nicht Teil der Enterprise Mobility Suite.

### Wie melde ich Probleme mit Azure AD B2C?

Informationen hierzu finden Sie unter [Senden von Supportanfragen für Azure Active Directory B2C](active-directory-b2c-support.md)

### Wann wird Azure AD B2C allgemein verfügbar sein?

Derzeit können wir noch keine Angaben zum Datum der allgemeinen Verfügbarkeit machen.

## Weitere Informationen

Es kann hilfreich sein, sich auch über die aktuellen [Einschränkungen der Vorschauversion](active-directory-b2c-limitations.md) zu informieren.

<!---HONumber=AcomDC_0224_2016-->