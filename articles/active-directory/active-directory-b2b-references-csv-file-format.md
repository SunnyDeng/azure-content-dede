<properties
   pageTitle="CSV-Dateiformat bei der Vorschau der Azure Active Directory B2B-Zusammenarbeit | Microsoft Azure"
   description="Azure Active Directory B2B ermöglicht Geschäftspartnern den gezielten Zugriff auf Ihre Unternehmens-Apps und unterstützt so Ihre unternehmensübergreifenden Beziehungen."
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# CSV-Dateiformat bei der Vorschau der Azure Active Directory (Azure AD) B2B-Zusammenarbeit

Die Vorschauversion der Azure AD B2B-Zusammenarbeit erfordert eine CSV-Datei mit Angaben zum Partnerbenutzer, die über das Azure AD-Portal hochgeladen werden müssen. Die CSV-Datei muss die nachfolgenden erforderlichen Bezeichnungen enthalten und kann bei Bedarf optionale Felder umfassen. Ändern Sie die CSV-Beispieldatei (siehe unten), ohne die Schreibweise der Bezeichnungen in der ersten Zeile zu ändern oder die Spalten neu anzuordnen.

>[AZURE.NOTE]Die erste Zeile mit Bezeichnungen (z. B. „Email“, „DisplayName“ usw.) ist erforderlich, damit die CSV-Datei erfolgreich analysiert werden kann. Die Schreibweise muss mit den unten angegebenen Feldern übereinstimmen. Diese Bezeichnungen geben die Inhalte darunter an. Die Bezeichnungen der nicht benötigten optionalen Felder können aus der CSV-Datei entfernt werden. Die gesamte Spalte kann leer gelassen werden.

## Erforderliche Felder: <br/>
**Email**: E-Mail-Adresse des eingeladenen Benutzers. <br/> **DisplayName**: Anzeigename für den eingeladenen Benutzer (i. d. R. Vor- und Nachname).<br/> **InviteContactUsUrl**: URL, die in E-Mail-Einladungen aufgenommen werden sollte, falls sich der eingeladene Benutzer an Ihr Unternehmen wenden möchte.<br/>

## Optionale Felder: <br/>
**InviteAppID**: Die ID für die Anwendung, die für das Branding der E-Mail-Einladung und der Annahmeseiten verwendet wird.<br/> **InviteAppResources**: AppIDs für Unternehmensanwendungen, denen Benutzer zugewiesen werden können. AppIDs können in PowerShell durch Aufrufen von `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` abgerufen werden.<br/> **InviteGroupResources**: ObjectIDs für Gruppen, denen Benutzer hinzugefügt werden können. ObjectIDs können in PowerShell durch Aufrufen von `Get-MsolGroup | fl DisplayName, ObjectId` abgerufen werden.<br/> **InviteReplyURL**: Die URL, an die ein eingeladener Benutzer nach der Annahme der Einladung weitergeleitet wird. Dies sollte eine unternehmensspezifische URL sein (z. B. [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Wenn dieses optionale Feld nicht angegeben ist, wird der eingeladene Benutzer zum App-Zugriffsbereich weitergeleitet, in dem er zu Ihren ausgewählten Unternehmens-Apps navigieren kann. Die URL für den App-Zugriffsbereich hat das Format `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/> **Language**: Die Sprache für die Einladungs-E-Mail und die Einlösung. Wenn keine Angabe erfolgt, wird standardmäßig Englisch verwendet. Die folgenden weiteren 10 Sprachcodes werden unterstützt:<br/> 1. de: Deutsch<br/> 2. es: Spanisch<br/> 3. fr: Französisch<br/> 4. it: Italienisch<br/> 5. ja: Japanisch<br/> 6. ko: Koreanisch<br/> 7. pt-BR: Portugiesisch (Brasilien)<br/> 8. ru: Russisch<br/> 9. zh-HANS: Chinesisch (vereinfacht)<br/> 10. zh-HANT: Chinesisch (traditionell)<br/>

## Beispiel-CSV-Datei
Dies ist ein Beispiel für eine CSV-Datei, das Sie anpassen können.

>[AZURE.NOTE]Kopieren Sie diese Beispieldatei, fügen Sie sie in Editor ein, und speichern Sie sie mit der Dateierweiterung „.csv“. Bearbeiten Sie sie dann in Excel. Sie wird in eine Tabelle mit Bezeichnungen in der ersten Zeile gegliedert.

>[AZURE.NOTE]Fügen Sie in Excel weitere optionale Felder hinzu, indem Sie die Bezeichnung angeben und die Spalte darunter füllen.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

## Verwandte Artikel
Weitere Artikel zur Azure B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [So funktioniert's](active-directory-b2b-how-it-works.md)
- [Ausführliche exemplarische Vorgehensweise](active-directory-b2b-detailed-walkthrough.md)
- [Tokenformat für externe Benutzer](active-directory-b2b-references-external-user-token-format.md)
- [Objektattributänderungen für externe Benutzer](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Aktuelle Einschränkungen der Vorschau](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->