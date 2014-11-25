<properties title="Role Based Access Control in Azure Preview Portal" pageTitle="Role Based Access Control in Azure Preview Portal" description="Describes how role based access control works and how to set it up" metaKeywords="" services="multiple" solutions="" documentationCenter="" authors="justinha" videoId="" scriptId="" />

<tags ms.service="multiple" ms.devlang="dotnet" ms.topic="article" ms.tgt_pltfrm="Ibiza" ms.workload="infrastructure-services" ms.date="09/12/2014" ms.author="justinha;Justinha@microsoft.com" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.--> 
<!--Properties section (above): this is required in all topics. Please fill it out!--> 
<!--The next line, with one pound sign at the beginning, is the page title-->

# Rollenbasierte Zugriffssteuerung über das Azure-Vorschauportal

Wir unterstützen nun die rollenbasierte Zugriffssteuerung (RBAC) über das Azure-Vorschauportal, damit Organisationen ihren Zugriffsverwaltungsanforderungen einfach und präzise nachkommen können. Lesen Sie diesen <a href="http://go.microsoft.com/fwlink/?LinkId=511576" target="_blank">Blogeintrag</a>, um sich mit dieser Funktion vertraut zu machen und eine rasche Einführung zu erhalten. Die Konzepte werden detailliert beschrieben und an zusätzlichen Anwendungsfällen verdeutlicht.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

## Inhaltsverzeichnis

-   [RBAC in Azure](#whatisrbac)
-   [Koexistenz von RBAC mit Abonnement-Co-Administratoren](#coexist)
-   [Autorisierung für Verwaltungsvorgänge vs. Datenvorgänge](#authmgmt)
-   [Hinzufügen und Entfernen von Zugriffsrechten](#addremoveaccess)
-   [Bekannte Probleme bei der Verwendung der rollenbasierten Zugriffssteuerung](#knownissues)
-   [Feedback geben](#feedback)
-   [Nächste Schritte](#next)

## <span id="whatisrbac"></span>RBAC in Azure</a>

Jedes Azure-Abonnement ist mit einem Azure Active Directory verknüpft. Benutzer und Dienste, die über das Azure-Verwaltungsportal oder über die Azure Resource Manager-API auf Abonnementressourcen zugreifen, müssen sich zunächst mit Azure Active Directory authentifizieren.

![][1] 

Mithilfe der rollenbasierten Zugriffssteuerung von Azure kann Azure-AD-Benutzern, -Gruppen und -Diensten Zugriff gewährt werden, indem ihnen Rollen auf Abonnementbasis oder für eine Ressourcengruppe bzw. einzelne Ressourcen zugewiesen werden. Die zugewiesene Rolle bestimmt die Zugriffsebene der Benutzer, Gruppen oder Dienste auf eine Azure-Ressource.

### Rolle

Eine Rolle ist eine Sammlung von Aktionen, die an Azure-Ressourcen durchgeführt werden können. Ein Benutzer oder ein Dienst darf eine Aktion an einer Azure-Ressource durchführen, wenn ihnen eine Rolle zugewiesen ist, die diese Aktion umfasst.

#### Integrierte Rollen

Zurzeit umfasst die rollenbasierte Zugriffssteuerung von Azure drei integrierte Rollen, die Benutzer, Gruppen und Diensten zugewiesen werden können.

-   **Besitzer**: hat Vollzugriff auf alle Azure-Ressourcen. Der Besitzer darf alle Verwaltungsvorgänge an einer Ressource, einschließlich Zugriffsverwaltung, durchführen.
-   **Mitwirkender**: darf alle Verwaltungsvorgänge an einer Ressource, außer Zugriffsverwaltung, durchführen. D. h., ein Mitwirkender darf anderen Personen keinen Zugriff gewähren.
-   **Leser**: kann Ressourcen nur anzeigen. Ein Leser darf keine mit einer Ressource verknüpften geheimen Schlüssel anzeigen.

Die Definition integrierter Rollen kann nicht geändert werden. In einer zukünftigen Version von Azure RBAC können Sie benutzerdefinierte Rollen definieren. Dazu können Sie aus einer Liste mit verfügbaren Aktionen eine Reihe an Aktionen auswählen, die an Azure-Ressourcen durchgeführt werden dürfen.

#### Aktionen und Nicht-Aktionen

Die Eigenschaft **Aktionen** der Rollendefinition gibt die zulässigen Aktionen für eine Azure-Ressource an. Für Aktionszeichenfolgen dürfen Platzhalter verwendet werden. Die Eigenschaft **Nicht-Aktionen** der Rollendefinition gibt die Aktionen an, die von den zulässigen Aktionen ausgeschlossen werden müssen.

<table>
<thead>
<tr class="header">
<th align="left">
**Integrierte Rolle**

</th>
<th align="left">
Aktionen

</th>
<th align="left">
Nicht-Aktionen

</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">
Besitzer (alle Aktionen zulassen)

</td>
<td align="left">
\*

</td>
</tr>
<tr class="even">
<td align="left">
Mitwirkender (alle Aktionen zulassen, außer Schreiben und Löschen von Rollenzuweisungen)

</td>
<td align="left">
\*

</td>
<td align="left">
Microsoft.Authorization/ \* /Write, Microsoft.Authorization/ \* /Delete

</td>
</tr>
<tr class="odd">
<td align="left">
Leser (alle Leseaktionen zulassen)

</td>
<td align="left">
\*/Read |

</td>
</tr>
</tbody>
</table>
### Rollenzuweisung

Azure AD-Benutzer und -Dienste erhalten Zugriff, indem ihnen die entsprechende Rolle für eine Azure-Ressource zugewiesen wird.

#### Azure AD-Sicherheitsprinzipale

Den folgenden Azure AD-Sicherheitsprinzipalen können Rollen zugewiesen werden:

-   **Benutzer**: Organisationsbenutzern können Rollen zugewiesen werden, wenn sie Teil des Azure AD sind, das mit dem Azure-Abonnement verknüpft ist. Auch externen Microsoft Account-Benutzern können Rollen zugewiesen werden (wie jan@outlook.com): Mithilfe der Invite-Aktion wird einem Benutzer eine Rolle über das Azure-Vorschauportal zugewiesen. Wenn einem externen Microsoft Account-Benutzer eine Rolle zugewiesen wird, wird in Azure AD ein Gastkonto für diesen Benutzer erstellt. Wird das Gastkonto im Verzeichnis deaktiviert, ist der Benutzer nicht berechtigt, auf bereits gewährte Azure-Ressourcen zuzugreifen.
-   **Gruppen**: Azure AD-Sicherheitsgruppen können Rollen zugewiesen werden. Ein Benutzer erhält automatisch Zugriff auf eine Ressource, wenn er Mitglied einer Gruppe mit Zugriffsrechten wird. Ebenso verliert der Benutzer automatisch Zugriff auf die Ressource, wenn er aus der Gruppe entfernt wird. Es wird empfohlen, Zugriffsrechte über Gruppen zu verwalten, d. h. anstatt einzelnen Benutzern werden Gruppen Rollen zugewiesen und Benutzer zur Gruppe hinzugefügt oder daraus entfernt. Verteilungslisten können in Azure RBAC keine Rollen zugewiesen werden.
    Durch die Möglichkeit, Gruppen Rollen zuweisen zu können, kann eine Organisation ihr bestehendes Zugriffssteuerungsmodell von ihrem lokalen Verzeichnis auf die Cloud ausweiten. Dadurch lassen sich bereits für die lokale Zugriffssteuerung festgelegte Sicherheitsgruppen für die Zugriffssteuerung auf Ressourcen im Azure-Vorschauportal wiederverwenden. Weitere Informationen über die verschiedenen Optionen zur Synchronisation von Benutzern und Gruppen von einem lokalen Verzeichnis aus finden Sie unter [Verzeichnisintegration](http://technet.microsoft.com/library/jj573653.aspx). Azure AD Premium bietet außerdem eine [Funktion zur delegierten Gruppenverwaltung](http://msdn.microsoft.com/library/azure/dn641267.aspx). Mit dieser Funktion kann die Erstellung und Verwaltung von Gruppen an Nichtadministratorbenutzer von Azure AD delegiert werden.
-   **Dienstprinzipale**: Dienstidentitäten werden im Verzeichnis als Dienstprinzipale dargestellt. Sie authentifizieren sich mit Azure AD und können sicher miteinander kommunizieren. Diensten kann der Zugriff auf Azure-Ressourcen gewährleistet werden, indem dem Azure AD-Dienstprinzipal, der den Dienst darstellt, Rollen über das Azure-Modul für Windows PowerShell zugewiesen werden.

#### Ressourcenbereich

Zugriffsrechte müssen nicht für das gesamte Abonnement gewährt werden. Rollen können für Ressourcengruppen oder einzelne Ressourcen vergeben werden. In Azure RBAC erbt eine Ressource die Rollenzuweisung der ihr übergeordneten Ressource. Wenn also einem Benutzer, einer Gruppe oder einem Dienst Zugriff auf nur eine Ressourcengruppe eines Abonnements gewährt wird, können sie nur auf diese Ressourcengruppe und die zugehörigen Ressourcen zugreifen. Andere Ressourcengruppen desselben Abonnements sind vom Zugriff ausgeschlossen. Ein weiteres Beispiel: Eine Sicherheitsgruppe kann zur Leserolle für eine Ressourcengruppe und gleichzeitig zur Rolle "Mitwirkender" für eine Datenbank innerhalb dieser Ressourcengruppe hinzugefügt werden.

![][2]

## <span id="coexist"></span></a>Koexistenz von RBAC mit Abonnement-Co-Administratoren

Abonnementadministrator und -Co-Administratoren erhalten weiterhin Vollzugriff auf Azure-Portale und Verwaltungs-APIs. Im RBAC-Modell wird ihnen auf Abonnementebene die Besitzerrolle zugewiesen.
Das neue RBAC-Modell wird jedoch nur vom Azure-Vorschauportal und den Azure Resource Manager-APIs unterstützt. Benutzer und Dienste, denen RBAC-Rollen zugewiesen wurden, können nicht auf das Azure-Verwaltungsportal oder auf die Service Management-APIs zugreifen. Wenn ein Benutzer über das Azure-Vorschauportal zur Besitzerrolle eines Abonnements hinzugefügt wird, ist er nicht automatisch Co-Administrator eines Abonnements im normalen Azure-Portal.

Wenn Sie einem Benutzer Zugriff auf eine Azure-Ressource gewähren möchten, die noch nicht zur Verwaltung im Azure-Vorschauportal zur Verfügung steht, fügen Sie den Benutzer über das normale Azure-Verwaltungsportal zu den Abonnement-Co-Administratoren hinzu. Die Verwaltung der Ressourcen "Service Bus" und "Cloud Services" wird noch nicht für die Verwendung mit RBAC unterstützt.

## <span id="authmgmt"></span></a>Autorisierung für Verwaltungsvorgänge vs. Datenvorgänge

Die rollenbasierte Zugriffssteuerung wird nur für Verwaltungsvorgänge von Azure-Ressourcen im Azure-Vorschauportal und in den Azure Resource Manager-APIs unterstützt. Über RBAC können nicht alle Vorgänge auf Datenebene für Azure-Ressourcen autorisiert werden. Während das Erstellen/Lesen/Aktualisieren/Löschen von Speicherkonten über RBAC gesteuert werden kann, lassen sich dieselben Vorgänge für Blobs oder Tabellen in einem Speicherkonto noch nicht über RBAC steuern. Ebenso kann das Erstellen/Lesen/Aktualisieren/Löschen einer SQL-Datenbank über RBAC gesteuert werden, während sich dieselben Vorgänge noch nicht für SQL-Tabellen in einer Datenbank über RBAC steuern lassen.

## <span id="addremoveaccess"></span></a>Hinzufügen und Entfernen von Zugriffsrechten

Im Folgenden wird anhand eines Beispiels erklärt, wie ein Ressourcenbesitzer in einer Organisation den Zugriff auf eine Ressource verwalten kann. In diesem Szenario arbeiten mehrere Personen an verschiedenen Test- und Produktionsprojekten, die mithilfe von Azure-Ressourcen erstellt wurden. Die Zugriffsberechtigung sollte nach bewährten Methoden vergeben werden. Dabei sollten Benutzer auf alle erforderlichen, jedoch nicht auf zusätzliche Ressourcen zugreifen können. Machen Sie möglichst von den bereits eingerichteten Prozessen und Tools Gebrauch, um die im lokalen Active Directory verwalteten Sicherheitsgruppen nutzen zu können. In den folgenden Abschnitten wird die Zugriffsvergabe auf die zuvor erwähnten Ressourcen beschrieben:

-   [Zugriff hinzufügen](#add)
-   [Zugriff entfernen](#remove)
-   [Zugriff für externe Benutzer hinzufügen und entfernen](#addremoveext)

### 

<p>
<span id="add"></span></a>Zugriff hinzufügen

</h2>
</p>
Es folgt eine Zusammenfassung der Zugriffsanforderungen und wie diese in Azure festgelegt werden.

| Benutzer/Gruppe               | Zugriffsanforderung                                                          | Rolle und Bereich für Zugriff                                                                                            |
|-------------------------------|------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| Gesamtes Team von Jana Schulz | Lesen aller Azure-Ressourcen                                                 | Hinzufügen der AD-Gruppe, die das Team von Jana Schulz darstellt, zur Leserolle für das Azure-Abonnement                 |
| Gesamtes Team von Jana Schulz | Erstellen und Verwalten der gesamten Ressourcen in der Test-Ressourcengruppe | Hinzufügen der AD-Gruppe, die das Team von Jana Schulz darstellt, zur Rolle "Mitwirkender" für die Test-Ressourcengruppe |
| Brock                         | Erstellen und Verwalten der gesamten Ressourcen in der Prod-Ressourcengruppe | Hinzufügen von Brock zur Rolle "Mitwirkender" für die Prod-Ressourcengruppe                                              |

Zunächst wird für alle Ressourcen eines Abonnements Lesezugriff hinzugefügt. Klicken Sie auf **Durchsuchen \> Alles \> Abonnements**.

![][3] 

Klicken Sie auf *Name des Abonnements* \*\* \> Leser \> Hinzufügen\*\*. Wählen Sie aus der Liste mit Benutzern und Gruppen den Namen der Active Directory-Gruppe aus, oder geben Sie ihn ein.

![][4]

Fügen Sie dasselbe Team zur Rolle "Mitwirkender" der Test-Ressourcengruppe hinzu. Klicken Sie auf eine Ressourcengruppe, um deren Eigenschaften anzuzeigen. Klicken Sie unter **Rollen** auf **Mitwirkender \> Hinzufügen**, und geben Sie den Namen des Teams ein.

![][5]

Um Brock zur Rolle "Mitwirkender" der Prod-Ressourcengruppe hinzuzufügen, klicken Sie auf die Ressourcengruppe, dann auf **Mitwirkender \> Hinzufügen**, und geben Sie den Namen "Brock" ein.

![][6]

Rollenzuweisungen können auch mit dem Microsoft Azure-Modul für Windows PowerShell verwaltet werden. Im folgenden Beispiel wird das Konto von Brock über das New-AzureRoleAssignment-Cmdlet anstatt über das Portal hinzugefügt:

    PS C:PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDBgt; New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Weitere Informationen über das Hinzufügen und Entfernen von Zugriffsrechten mit Windows PowerShell finden Sie unter [Verwalten der rollenbasierten Zugriffssteuerung mit Windows PowerShell](http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-powershell/).

### 

<p>
<span id="remove"></span></a>Zugriff entfernen

</h2>
</p>
Zugriffsberechtigungen können einfach wieder entfernt werden. Angenommen, Sie möchten einen Benutzer namens Bernd Ahrend von der Leserolle der TestDB-Ressourcengruppe entfernen. Öffnen Sie das Ressourcengruppenfenster, und klicken Sie auf **Leser \> Bernd Ahrend \> Entfernen**.

![][7]

Im folgenden Beispiel wird der Benutzer Bernd Ahrend über das Remove-AzureRoleAssignment-Cmdlet entfernt:

    PS C:PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDBgt; Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### 

<p>
<span id="addremoveext"></span></a>Zugriff für externe Benutzer hinzufügen und entfernen

</h2>
</p>
Die Registerkarte **Konfigurieren** eines Verzeichnisses enthält Optionen zur Zugriffssteuerung für externe Benutzer. Diese Optionen können nur von einem globalen Verzeichnisadministrator in der Benutzeroberfläche des normalen Azure-Portals geändert werden (eine Windows PowerShell- oder API-Methode steht nicht zur Verfügung).
Klicken Sie im normalen Azure-Portal zum Öffnen der Registerkarte **Konfigurieren** auf **Active Directory** und anschließend auf den Namen des Verzeichnisses.

![][10]

Anschließend können Sie die Optionen zur Zugriffssteuerung für externe Benutzer ändern.

![][8]

In der Standardeinstellung können Gäste den Verzeichnisinhalt nicht auflisten lassen. Somit können sie Benutzer und Gruppen in der **Mitgliederliste** nicht anzeigen. Gäste können jedoch durch Eingabe der vollständigen E-Mail-Adresse nach einem Benutzer suchen und ihm dann Zugriff gewähren. Für Gäste gelten folgende allgemeine Beschränkungen:

-   Benutzer und Gruppen können nicht im Verzeichnis aufgelistet werden.
-   Wenn die E-Mail-Adresse des Benutzers bekannt ist, können nur eingeschränkte Informationen zu diesem Benutzer angezeigt werden.
-   Wenn der Name einer Gruppe bekannt ist, können nur eingeschränkte Informationen zu dieser Gruppe angezeigt werden.

Durch die Anzeige eingeschränkter Informationen zu einem Benutzer oder einer Gruppe können Gäste andere Personen einladen und Informationen zu Personen anzeigen, mit denen sie zusammenarbeiten.

Gehen Sie wie folgt vor, um Zugriff für einen externen Benutzer hinzuzufügen. Wir fügen einen externen Benutzer zur selben Leserolle für die TestDB-Ressourcengruppe hinzu, sodass der Benutzer beim Beheben eines Fehlers helfen kann. Öffnen Sie das Ressourcengruppenfenster, klicken Sie auf **Leser \> Hinzufügen \> Einladen**, und geben Sie die E-Mail-Adresse des hinzuzufügenden Benutzers ein.

![][9]

Beim Hinzufügen eines externen Benutzers wird im Verzeichnis ein Gastkonto erstellt. Anschließend kann der Gast zu einer Gruppe hinzugefügt oder daraus entfernt werden. Sie können einen Gast auch separat zu einer Rolle hinzufügen oder davon entfernen, so wie Sie es mit anderen Verzeichnisbenutzern tun würden.

Sie können einen Gast von jeder Rolle entfernen, so als würden Sie einen Benutzer entfernen. Wenn Sie einen Gast von einer Rolle einer Ressource entfernen, wird er nicht aus dem Verzeichnis entfernt.

## <span id="knownissues"></span></a>Bekannte Probleme bei der Verwendung der rollenbasierten Zugriffssteuerung

Wenn Sie während der Testphase bei der Verwendung der rollenbasierten Zugriffssteuerung auf Probleme stoßen, finden Sie unter [Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung](http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-troubleshooting/) bekannte Probleme, die mit dem vorliegenden Problem in Zusammenhang stehen können.

## <span id="feedback"></span></a>Feedback geben

Wir bitten Sie, Azure RBAC zu testen und uns [Feedback](http://aka.ms/azurerbacfeedback) zu senden.

## <span id="next"></span></a>Nächste Schritte

Die folgenden Ressourcen bieten weitere Unterstützung für die Verwendung der rollenbasierten Zugriffssteuerung:

-   [Verwalten der rollenbasierten Zugriffssteuerung mit Windows PowerShell](http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-powershell/)
-   [Verwalten der rollenbasierten Zugriffssteuerung mit xplat-cli](http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-xplat-cli/)
-   [Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung](http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-troubleshooting/)
-   [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)
-   [Azure Active Directory Premium und Basic](http://msdn.microsoft.com/de-de/library/azure/dn532272.aspx)
-   [Verknüpfung von Azure-Abonnements mit Azure AD](http://msdn.microsoft.com/de-de/library/azure/dn629581.aspx)
-   Eine Einführung zur Self-Service-Gruppenverwaltung von Sicherheitsgruppen finden Sie im [Active Directory Team-Blog](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx).

<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png