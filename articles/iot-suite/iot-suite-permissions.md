<properties
  pageTitle="Azure IoT Suite und Azure Active Directory | Microsoft Azure"
  description="Informationen, wie Azure Active Directory von Azure IoT Suite verwendet werden, um Berechtigungen zu verwalten."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="hero-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="11/17/2015"
  ms.author="araguila"/>
  
# Berechtigungen für die Website „azureiotsuite.com“

## Was bei der Anmeldung geschieht

Bei Ihrer ersten Anmeldung bei [azureiotsuite.com][lnk-azureiotsuite] bestimmt die Website basierend auf dem derzeit ausgewählten Azure Active Directory-Mandanten (AAD) und dem Azure-Abonnement Ihre Berechtigungsstufen.

1.  Die Website ermittelt zunächst mithilfe von Azure, zu welchen AAD-Mandanten Sie gehören, um die Liste der Mandanten aufzufüllen, die neben Ihrem angemeldeten Benutzernamen angezeigt werden. Derzeit kann die Website nur Benutzertoken für jeweils einen Mandanten abrufen. Daher werden Sie, wenn Sie über das Dropdownfeld rechts oben zu einem anderen Mandanten wechseln, erneut bei diesem Mandanten angemeldet, um die Token für diesen Mandanten zu beziehen.

2.  Als Nächstes ermittelt die Website mithilfe von Azure, welche Abonnements Sie dem ausgewählten Mandanten zugeordnet haben. Die verfügbaren Abonnements werden angezeigt, wenn Sie eine neue vorkonfigurierte Lösung erstellen.

3.  Abschließend ruft die Website alle Ressourcen in den Abonnements und Ressourcengruppen ab, die als vorkonfigurierte Lösungen gekennzeichnet sind, und füllt die Kacheln auf der Startseite auf.

In den beiden folgenden Abschnitten werden die Rollen beschrieben, die den Zugriff auf die vorkonfigurierten Lösungen steuern.

## AAD-Rollen

Die AAD-Rollen steuern die Fähigkeit, vorkonfigurierte Lösungen bereitzustellen und Benutzer in einer vorkonfigurierten Lösung zu verwalten.

Weitere Informationen zu Administratorrollen in AAD finden Sie unter [Zuweisen von Administratorrollen in Azure AD][lnk-aad-admin]. Doch dieser Artikel konzentriert sich hauptsächlich auf die Rollen **Globaler Administrator** und **Domänenbenutzer/-Mitglied**, die von vorkonfigurierten Lösungen verwendet werden.

**Globaler Administrator:** Es können viele globale Administratoren pro AAD-Mandanten vorhanden sein. Wenn Sie einen AAD-Mandanten erstellen, sind Sie standardmäßig der globale Administrator dieses Mandanten. Der globale Administrator kann eine vorkonfigurierte Lösung bereitstellen und erhält die Rolle **ADMINISTRATOR** für die Anwendung innerhalb des AAD-Mandanten. Wenn jedoch ein anderer Benutzer im gleichen AAD-Mandanten eine Anwendung erstellt, ist **IMPLICIT READ ONLY** die Standardrolle, die dem globalen Administrator zugewiesen wird. Globale Administratoren können im [klassischen Azure-Portal][lnk-classic-portal] Rollen für Anwendungen zuweisen.

**Domänenbenutzer/-mitglied:** Es können viele Domänenbenutzer/-mitglieder pro AAD-Mandanten vorhanden sein. Domänenbenutzer können eine vorkonfigurierte Lösung über die Website [azureiotsuite.com][lnk-azureiotsuite] bereitstellen. Die Standardrolle, die ihnen für die von ihnen bereitgestellte Anwendung gewährt wird, ist **ADMINISTRATOR**. Sie können im Repository [azure-iot-solution][lnk-github-repo] mithilfe des Skripts „build.cmd“ eine Anwendung erstellen. Doch die Standardrolle, die ihnen zugewiesen wird, ist **IMPLICIT READONLY**, da sie keine Berechtigung zum Zuweisen von Rollen haben. Wenn ein anderer Benutzer im AAD-Mandanten eine Anwendung erstellt, wird ihm standardmäßig die Rolle **IMPLICIT READONLY** für diese Anwendung zugewiesen. Benutzer können keine Rollen für Anwendungen zuweisen und daher keine Benutzer oder Rollen für Benutzer einer Anwendung zuweisen, auch wenn sie sie bereitgestellt haben.

**Gastbenutzer/Gast:** Pro AAD-Mandanten kann es viele Gastbenutzer/Gäste geben. Gastbenutzer verfügen im AAD-Mandanten über begrenzte Rechte. Daher können Gastbenutzer keine vorkonfigurierte Lösung im AAD-Mandanten bereitstellen.

Weitere Informationen finden Sie in den folgenden Ressourcen:

- [Erstellen oder Bearbeiten von Benutzern in Azure AD][lnk-create-edit-users]
- [Zuweisen von App-Rollen in AAD][lnk-assign-app-roles]

## Administratorrollen für Azure-Abonnements

Azure-Administratorrollen steuern die Fähigkeit, einem AD-Mandanten ein Azure-Abonnement zuzuordnen.

Weitere Informationen zu den Azure-Rollen „Co-Administrator“, „Dienstadministrator“ und „Kontoadministrator“ finden Sie im Artikel [Hinzufügen oder Ändern des Co-Administrators, Dienstadministrators und Kontoadministrators in Azure][lnk-admin-roles].

## Anwendungsrollen

Anwendungsrollen steuern den Zugriff auf Geräte in Ihrer vorkonfigurierten Lösung.

Es gibt zwei definierte und eine implizite Rolle in der Anwendung, die erstellt wird, wenn Sie eine vorkonfigurierte Lösung bereitstellen.

-   **ADMINISTRATOR:** Hat Vollzugriff zum Hinzufügen, Verwalten und Entfernen von Geräten.

-   **READ ONLY:** Hat die Möglichkeit, Geräte anzuzeigen.

-   **IMPLICIT READ ONLY:** Identisch mit „READ ONLY“, wird jedoch allen Benutzern Ihres AAD-Mandanten gewährt. Dies ist zur Vereinfachung während der Entwicklung erfolgt. Sie können diese Rolle durch Ändern der Quelldatei [RolePermissions.cs][lnk-resource-cs] entfernen.

### Ändern von Anwendungsrollen

Sie müssen globaler AAD-Administrator sein, um Rollen für einen Benutzer zu ändern:

1. Melden Sie sich beim [klassischen Azure-Portal][lnk-classic-portal] an.

2. Wählen Sie **Active Directory** aus.

3. Klicken Sie auf den Namen Ihres AAD-Mandanten.

4. Klicken Sie auf **Anwendungen**.

5. Wenn Ihre Anwendung nicht in der Liste enthalten ist, wechseln Sie im der Dropdownliste **Anzeigen** zu **Anwendung im Besitz meines Unternehmens**, und klicken Sie auf das Häkchen.

6. Klicken Sie auf den Namen der Anwendung, der mit dem Namen der vorkonfigurierten Lösung übereinstimmt.

7. Klicken Sie auf **Benutzer**.

8. Wählen Sie den Benutzer aus, dessen Rolle gewechselt werden soll.

9. Klicken Sie auf die Schaltfläche „Zuweisen“. Für die Rolle, die zugewiesen werden soll, klicken Sie auf das Häkchen.

## Häufig gestellte Fragen

### Ich bin Dienstadministrator und möchte die Verzeichniszuordnung zwischen meinem Abonnement und einen bestimmten AAD-Mandanten ändern. Wie geht das?

1. Wechseln Sie zum [klassischen Azure-Portal][lnk-classic-portal], und klicken Sie links in der Liste der Dienste auf **Einstellungen**.

2. Wählen Sie das Abonnement aus, dessen Verzeichniszuordnung Sie ändern möchten.

3. Klicken Sie auf **Verzeichnis bearbeiten**.

4. Wählen Sie in der Dropdownliste das **Verzeichnis** aus, das Sie verwenden möchten. Klicken Sie auf den Vorwärtspfeil.

5. Bestätigen Sie die Verzeichniszuordnung und die betroffenen Co-Administratoren. Wenn eine Verschiebung aus einem anderen Verzeichnis erfolgt, werden alle Co-Administratoren aus dem ursprünglichen Verzeichnis entfernt.

### Ich bin Domänenbenutzer/-mitglied des AAD-Mandanten und habe eine vorkonfigurierte Lösung erstellt. Wie wird mir eine Rolle für meine Anwendung zugewiesen?

Bitten Sie einen globalen Administrator, Sie als globalen Administrator für den AAD-Mandanten zuzuweisen, damit Sie selbst Berechtigungen zum Zuweisen von Rollen zu Benutzern erhalten. Oder bitten Sie einen globalen Administrator, Ihnen eine Rolle zuzuweisen. Wenn Sie den AAD-Mandanten ändern möchten, in dem Ihre vorkonfigurierte Lösung bereitgestellt wurde, sehen Sie sich die nächste Frage an.

### Wie ändere ich den AAD-Mandanten, dem meine vorkonfigurierte Lösung und Anwendung für die Remoteüberwachung zugewiesen sind?

Sie können über <https://github.com/Azure/azure-iot-remote-monitoring> eine Cloudbereitstellung ausführen und erneute Bereitstellung mit einem neu erstellten AAD-Mandanten durchführen. Da Sie standardmäßig ein globaler Administrator sind, wenn Sie einen neuen AAD-Mandanten erstellen, haben Sie das Recht zum Hinzufügen von Benutzern und Zuweisen von Rollen zu diesen Benutzern.

1. Erstellen Sie ein neues AAD-Verzeichnis im [Azure-Verwaltungsportal][lnk-classic-portal].

2. Wechseln Sie zur Adresse <https://github.com/Azure/azure-iot-remote-monitoring>. Weitere Informationen zu Cloudbereitstellungen finden Sie unter [Cloudbereitstellung][lnk-wiki-clouddeployment].

3. Führen Sie `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` aus (z. B. `build.cmd cloud debug myRMSolution`).

4. Legen Sie bei Aufforderung die **tenantid** auf den neu erstellten Mandanten anstatt auf den vorherigen Mandanten fest.


### Ich möchte bei Anmeldung über ein Organisationskonto einen Dienstadministrator oder Co-Administrator ändern.

Siehe den Artikel [Ändern des Dienstadministrators und des Co-Administrators bei Anmeldung über ein Organisationskonto][lnk-service-admins].

### Warum wird dieser Fehler angezeigt? „Ihr Konto hat nicht die erforderlichen Berechtigungen zum Erstellen einer Lösung. Wenden Sie sich an den Administrator Ihres Kontos, oder versuchen Sie es mit einem anderen Konto.“

Sehen Sie sich das folgende Diagramm an:

![][img-flowchart]

**Weshalb wird dieser Fehler angezeigt, wenn ich ein Azure-Abonnement habe?** *Ein Azure-Abonnement ist erforderlich, um vorkonfigurierte Lösungen zu erstellen. Sie können in wenigen Minuten ein kostenloses Testkonto erstellen.*

Wenn Sie sicher sind, dass Sie über ein Azure-Abonnement verfügen, überprüfen Sie die Mandantenzuordnung für Ihr Abonnement, und stellen Sie sicher, dass der ordnungsgemäße Mandant in der Dropdownliste ausgewählt ist. Nachdem Sie überprüft haben, ob der gewünschte Mandant ordnungsgemäß ist, befolgen Sie das obige Diagramm, und überprüfen Sie die Zuordnung Ihres Abonnements und dieses AAD-Mandanten.

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-github-repo]: https://github.com/Azure/azure-iot-solution
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://github.com/Azure/azure-iot-remote-monitoring/wiki/Manually-setting-up-roles-and-assigning-permissions-in-Azure-Active-Directory-(AAD)#assigning-users-to-the-roles
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-wiki-clouddeployment]: https://github.com/Azure/azure-iot-remote-monitoring/wiki/Cloud-deployment

<!---HONumber=AcomDC_0128_2016-->