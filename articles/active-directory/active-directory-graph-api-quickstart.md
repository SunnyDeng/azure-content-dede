<properties
   pageTitle="Schnellstart für die Azure AD Graph-API | Microsoft Azure"
   description="Die Azure Active Directory Graph-API ermöglicht programmgesteuerten Zugriff auf Azure AD über OData-REST-API-Endpunkte. Anwendungen können die Graph-API verwenden, um CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren und Löschen) für Verzeichnisdaten und Objekte auszuführen."
   services="active-directory"
   documentationCenter="n/a"
   authors="JimacoMS"
   manager="msmbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="11/17/2015"
      ms.author="v-jibran@microsoft.com"/>

# Schnellstart für die Azure AD Graph-API

Die Azure Active Directory (AD) Graph-API ermöglicht programmgesteuerten Zugriff auf Azure AD über OData-REST-API-Endpunkte. Anwendungen können die Graph-API verwenden, um CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren und Löschen) für Verzeichnisdaten und Objekte auszuführen. Beispielsweise können Sie die Graph-API verwenden, um einen neuen Benutzer zu erstellen, Eigenschaften des Benutzers anzuzeigen oder zu aktualisieren, das Kennwort des Benutzers zu ändern, die Gruppenmitgliedschaft für den rollenbasierten Zugriff zu überprüfen, den Benutzer zu deaktivieren oder zu löschen. Weitere Informationen zu den Graph-API-Funktionen und Anwendungsszenarios finden Sie unter [Azure AD Graph API reference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) (in englischer Sprache) und [Azure AD Graph API Prerequisites](https://msdn.microsoft.com/de-DE/library/hh974476(Azure.100).aspx) (in englischer Sprache).

> [AZURE.IMPORTANT]Die Azure AD Graph-API-Funktionen sind auch durch [Microsoft Graph](https://graph.microsoft.io/) verfügbar. Diese einheitliche API enthält APIs von anderen Microsoft-Diensten, z. B. Outlook, OneDrive, OneNote, Planer und Office Graph, und macht sie über einen einzelnen Endpunkt und ein einzelnes Zugriffstoken verfügbar.

## Erstellen einer Graph-API-URL

In der Graph-API können Sie für den Zugriff auf Verzeichnisdaten und Objekte (also Ressourcen oder Entitäten), für die Sie CRUD-Vorgänge ausführen möchten, URLs verwenden, die auf dem OData-Protokoll (Open Data) basieren. Die URLs, die in der Graph-API verwendet werden, bestehen aus vier Hauptkomponenten: Dienststamm, Mandanten-ID, Ressourcenpfad und Optionen für Abfragezeichenfolgen: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Betrachten Sie beispielsweise die folgende URL: `https://graph.windows.net/contoso.com/groups?api-version=1.5`.

- **Dienststamm**: In Azure AD Graph-API ist der Dienststamm immer https://graph.windows.net.
- **Mandanten-ID**: Dies kann ein überprüfter (registrierter) Domänenname sein, im Beispiel oben "contoso.com". Es kann aber auch eine Objekt-ID des Mandanten oder der Alias "myorganization" oder "me" sein. Weitere Informationen finden Sie unter [Adressieren von Entitäten und Vorgängen in der Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)).
- **Ressourcenpfad**: Dieser Abschnitt einer URL identifiziert die Ressource, mit der interagiert werden soll (Benutzer, Gruppen, ein bestimmter Benutzer oder eine bestimmte Gruppe usw.). Im obigen Beispiel ist es die oberste Ebene "groups" zum Adressieren dieses Ressourcensatzes. Sie können auch eine bestimmte Entität adressieren, beispielsweise "users/{objectId}" oder "users/userPrincipalName".
- **Abfrageparameter**: ? trennt den Abschnitt für den Ressourcenpfad vom Abschnitt für Abfrageparameter. Der Abfrageparameter "api-version" ist für alle Anforderungen in der Graph-API erforderlich. Die Graph-API unterstützt außerdem die folgenden OData-Abfrageoptionen: **$filter**, **$orderby**, **$expand**, **$top** und **$format**. Die folgenden Abfrageoptionen werden zurzeit nicht unterstützt: **$count**, **$inlinecount** und **$skip**. Weitere Informationen finden Sie unter [Unterstützte Abfragen, Filter und Paginierungsoptionen in der Azure AD Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## Graph-API-Versionen

Die folgenden Versionen wurden für die Graph-API zur Verfügung gestellt.

* Betaversion
* Version 1.5
* Version 2013-11-08
* Version 2013-04-05

Sie geben die Version für eine Graph-API-Anforderung im Abfrageparameter "api-version" an. Für Version 1.5 verwenden Sie einen numerischen Versionswert, "api-version=1.5". Verwenden Sie für frühere Versionen eine Datumszeichenfolge, die dem Format JJJJ-MM-TT entspricht, beispielsweise "api-version=2013-11-08". Verwenden Sie für Funktionen der Vorschauversion die Zeichenfolge "beta", beispielsweise "api-version=beta". Weitere Informationen zu den Unterschieden zwischen den Graph-API-Versionen finden Sie unter [Versionsverwaltung der Azure AD Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## Graph-API-Metadaten

Um die Graph-API-Metadatendatei zurückzugeben, fügen Sie in der URL nach "tenant-identifier" das Segment "$metadata" hinzu. Beispielsweise gibt die folgende URL Metadaten für das Demo-Unternehmen zurück, die von Graph-Explorer verwendet werden: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.5`. Sie können diese URL in die Adressleiste eines Webbrowsers eingeben, um die Metadaten anzuzeigen. Das zurückgegebene CSDL-Metadatendokument beschreibt die Entitäten und komplexen Typen, deren Eigenschaften und die Funktionen und Aktionen, die von der angeforderten Graph-API-Version verfügbar gemacht werden. Wenn der Parameter "api-version" nicht angegeben wird, werden Metadaten für die neueste Version zurückgegeben.

## Allgemeine Abfragen

Unter [Allgemeine Abfragen der Azure AD Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) werden allgemeine Abfragen aufgeführt, die mit Azure AD Graph verwendet werden können. Dazu gehören auch Abfragen, die für den Zugriff auf Ressourcen der obersten Ebene in Ihrem Verzeichnis verwendet werden können, und Abfragen, um Vorgänge in Ihrem Verzeichnis auszuführen.

Beispielsweise gibt `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.5` Unternehmensinformationen für das Verzeichnis "contoso.com" zurück.

`https://graph.windows.net/contoso.com/users?api-version=1.5` listet alle Benutzerobjekte im Verzeichnis "contoso.com" auf.

## Verwenden von Graph-Explorer

Mit dem Graph-Explorer für die Azure AD Graph-API können Sie Verzeichnisdaten abfragen, während Sie eine Anwendung erstellen.

> [AZURE.IMPORTANT]Der Graph-Explorer unterstützt das Schreiben oder Löschen von Daten aus einem Verzeichnis nicht. Sie können mit dem Graph-Explorer nur Lesevorgänge in Ihrem Azure AD-Verzeichnis ausführen.

Im Folgenden wird die Ausgabe angezeigt, die Sie sehen würden, wenn Sie zum Graph-Explorer navigieren, "Demo-Unternehmen verwenden" auswählen und `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.5` zur Anzeige aller Benutzer im Demo-Verzeichnis eingeben:

![Azure AD Graph-API-Explorer](./media/active-directory-graph-api-quickstart/screen_shot.jpg)

**Laden von Graph-Explorer**: Um das Tool zu laden, wechseln Sie zu [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Klicken Sie auf **Demo-Unternehmen verwenden**, um den Graph-Explorer für Daten von einem Beispielmandanten auszuführen. Anmeldeinformationen sind zur Verwendung des Demo-Unternehmens nicht erforderlich. Sie können alternativ auf **Anmelden** klicken und sich mit den Anmeldeinformationen Ihres Azure AD-Kontos anmelden, um den Graph-Explorer für Ihren Mandanten auszuführen. Wenn Sie Graph-Explorer für Ihren eigenen Mandanten ausführen, müssen Sie oder Ihr Administrator während der Anmeldung zustimmen. Wenn Sie ein Office 365-Abonnement haben, verfügen Sie automatisch über einen Azure AD-Mandanten. Die Anmeldeinformationen, die Sie verwenden, um sich bei Office 365 anzumelden, sind tatsächlich Azure AD-Konten, und Sie können diese Anmeldeinformationen mit Graph-Explorer verwenden.

**Ausführen einer Abfrage**: Um eine Abfrage auszuführen, geben Sie Ihre Abfrage in das Textfeld für die Anforderung ein, und klicken Sie auf **GET**, oder drücken Sie die **EINGABETASTE**. Die Ergebnisse werden im Antwortfeld angezeigt. Beispielsweise listet `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.5` alle Gruppenobjekte im Demo-Verzeichnis auf.

Beachten Sie die folgenden Funktionen und Einschränkungen von Graph-Explorer: – Funktion zum automatischen Vervollständigen für Ressourcensätze. Um dies zu sehen, klicken Sie auf **Demo-Unternehmen verwenden**, und klicken Sie dann auf das Textfeld für die Anforderung (in dem die Unternehmens-URL angezeigt wird). Sie können einen Ressourcensatz aus der Dropdownliste auswählen.

- Unterstützt die Adressierungsaliase "me" und "myorganization". Beispielsweise können Sie `https://graph.windows.net/me?api-version=1.5` verwenden, um das Benutzerobjekt des angemeldeten Benutzers zurückzugeben, oder `https://graph.windows.net/myorganization/users?api-version=1.5`, um alle Benutzer im aktuellen Verzeichnis zurückzugeben. Beachten Sie, dass bei Verwendung des Alias "me" für das Demo-Unternehmen ein Fehler zurückgegeben wird, da kein angemeldeter Benutzer die Anforderung ausführt.

- Ein Abschnitt für den Antwortheader. Dieser kann zur Behandlung von Problemen beim Ausführen von Abfragen verwendet werden.

- Ein JSON-Viewer für die Antwort mit Funktionen zum Erweitern und Reduzieren.

- Keine Unterstützung für die Anzeige einer Miniaturansicht von Fotos.

## Verwenden von Fiddler zum Schreiben in das Verzeichnis

Im Rahmen dieses Schnellstarthandbuchs können Sie den Fiddler-Webdebugger verwenden, um Schreibvorgänge in das Azure AD-Verzeichnis zu üben. Weitere Informationen – auch zur Installation – von Fiddler finden Sie unter [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

Im folgenden Beispiel verwenden Sie den Fiddler-Webdebugger, um die neue Sicherheitsgruppe "MyTestGroup" in Ihrem Azure AD-Verzeichnis zu erstellen.

**Abrufen eines Zugriffstokens**: Für den Zugriff auf Azure AD Graph müssen sich Clients zuerst bei Azure AD authentifizieren. Weitere Informationen finden Sie unter [Authentifizierungsszenarien für Azure AD](active-directory-authentication-scenarios.md).

**Erstellen und Ausführen einer Abfrage**: Führen Sie die folgenden Schritte aus.

1. Öffnen Sie den Fiddler-Webdebugger, und wechseln Sie zur Registerkarte **Composer**.
2. Da Sie eine neue Sicherheitsgruppe erstellen möchten, wählen Sie **Post** als HTTP-Methode aus dem Dropdownmenü aus. Weitere Informationen zu Vorgängen und Berechtigungen für eine Gruppe finden Sie unter [Gruppe](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) innerhalb der [Azure AD Graph-REST-API-Referenz](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Geben Sie in das Feld neben **Post** Folgendes als URL der Anforderung ein: `https://graph.windows.net/mytenantdomain/groups?api-version=1.5`.

    > [AZURE.NOTE]Ersetzen Sie "mytenantdomain" mit dem Domänennamen Ihres eigenen Azure AD-Verzeichnisses.

4. Geben Sie im Feld direkt unterhalb des Dropdownmenüs "Post" Folgendes ein:

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE]Ersetzen Sie "&lt;your access token&gt;" mit dem Zugriffstoken für Ihr Azure AD-Verzeichnis.

5. Geben Sie im Feld **Anforderungstext** Folgendes ein:

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Weitere Informationen zum Erstellen von Gruppen finden Sie unter [Erstellen einer Gruppe](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Weitere Informationen zu Azure AD-Entitäten und -Typen, die von Graph bereitgestellt werden, und Informationen zu den Vorgängen, die mit Graph durchgeführt werden können, finden Sie unter [Azure AD Graph-REST-API-Referenz](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## Nächste Schritte

Erfahren Sie mehr über die [Azure AD Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

<!---HONumber=AcomDC_1125_2015-->