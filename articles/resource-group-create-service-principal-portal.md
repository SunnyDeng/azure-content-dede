<properties
   pageTitle="Erstellen eines neuen Azure-Dienstprinzipals mit dem Azure-Portal"
   description="Beschreibt den Vorgang zum Erstellen eines neuen Azure-Dienstprinzipals, der mit der rollenbasierten Zugriffskontrolle in Azure Resource Manager zum Verwalten des Zugriffs auf Ressourcen verwendet werden kann."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# Erstellen eines neuen Azure-Dienstprinzipals mit dem Azure-Portal

## Übersicht
Dienstprinzipale sind automatisierte Prozesse, Anwendungen oder Dienste, die auf andere Ressourcen zugreifen müssen. Mithilfe des Azure Resource Managers können Sie einem Dienstprinzipal Zugriff gewähren und es authentifizieren, sodass es die zulässigen Verwaltungsaktionen an Ressourcen ausführen kann, die im Abonnement oder als Mandant existieren.

Hier wird erklärt, wie ein neues Dienstprinzipal mithilfe des Azure-Portals erstellt werden kann. Aktuell müssen Sie das Microsoft Azure-Portal zum Erstellen eines neuen Dienstprinzipals verwenden. Das Azure-Vorschauportal wird in einer späteren Version um diese Möglichkeit erweitert.

## Konzepte
1. Azure Active Directory (AAD) - ein Cloud-Dienst für Identitäts- und Zugriffsverwaltung. Weitere Informationen finden Sie unter [Was ist das Azure Active Directory](./active-directory-whatis/)
2. Dienstprinzipal - ein Anwendungsbeispiel in einem Verzeichnis.
3. AD-Anwendung - ein Verzeichnisdatensatz im AAD, der eine Anwendung für AAD identifiziert. Weitere Informationen finden Sie unter [Grundlagen der Authentifizierung im Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).


## Erstellen einer Active Directory-Anwendung
1. Melden Sie sich über das [klassische Portal](https://manage.windowsazure.com/) bei Ihrem Azure-Konto an.

2. Wählen Sie im linken Bereich **Active Directory** aus.

   ![Active Directory auswählen][1]

3. Wählen Sie das Verzeichnis aus, das Sie zum Erstellen der neuen Anwendung nutzen möchten.

   ![Verzeichnis wählen][2]

3. Klicken Sie auf **Anwendungen**, um die Anwendungen in Ihrem Verzeichnis anzuzeigen.

   ![Anwendungen anzeigen][11]

4. Wenn Sie in diesem Verzeichnis zuvor noch keine Anwendung erstellt haben, sollten Sie eine Abbildung ähnlich dieser hier sehen. Klicken Sie auf **EINE ANWENDUNG HINZUFÜGEN**

   ![Anwendung hinzufügen][6]

   Oder klicken Sie im unteren Bereich auf **Hinzufügen**.

   ![Hinzufügen][12]

5. Wählen Sie den Anwendungstypen aus, den Sie erstellen möchten. Für dieses Lernprogramm wird keine Anwendung aus der Galerie verwendet.

   ![neue Anwendung][10]

6. Füllen Sie den Namen der Anwendung aus und wählen Sie den Anwendungstyp aus, den Sie nutzen möchten. Da hier das Dienstprinzipal dieser Anwendung zur Authentifizierung mit Azure Resource Manager verwendet werden soll, wird die Option zum Erstellen einer **WEBANWENDUNG UND/ODER WEB-API** gewählt und auf die Schaltfläche „Weiter“ geklickt.

   ![Anwendung benennen][9]

7. Tragen Sie die Eigenschaften Ihrer Anwendung ein. Geben Sie für die **ANMELDE-URL** die URI einer Website an, die Ihre Anwendung beschreibt. Das Vorhandensein der Website wird nicht überprüft. 
Geben Sie für die **APP-ID URI** die URI an, die Ihre Anwendung identifiziert. Die Eindeutigkeit oder das Vorhandensein des Endpunktes wird nicht überprüft. Klicken Sie auf **Abschließen**, um Ihre AAD-Anwendung zu erstellen. 

   ![Anwendungseigenschaften][4]

## Erstellen von Ihrem Dienstprinzipal-Kennwort
Das Portal sollte nun Ihre Anwendung ausgewählt haben.

1. Klicken Sie auf die Registrierkarte **Konfigurieren**, um das Kennwort für Ihre Anwendung zu konfigurieren.

   ![Anwendung konfigurieren][3]

2. Scrollen Sie herunter zum Abschnitt **Schlüssel** und wählen Sie den gewünschten Gültigkeitszeitraum für Ihr Kennwort aus.

   ![Schlüssel][7]

3. Wählen Sie **Speichern** aus, um Ihren Schlüssel zu erstellen.

   ![speichern][13]

   Der gespeicherte Schlüssel wird angezeigt, sodass Sie ihn kopieren können.

   ![gespeicherter Schlüssel][8]

4. Sie können nun Ihren Schlüssel zur Authentifizierung als Dienstprinzipal verwenden. Neben Ihrem  **SCHLÜSSEL** benötigen Sie zur Anmeldung Ihre **CLIENT-ID**. Gehen Sie zu **CLIENT-ID** und kopieren Sie diese.
  
   ![Client-ID][5]


Ihre Anwendung ist nun bereit und das Dienstprinzipal in Ihrem Mandanten erstellt. Wenn Sie sich als Dienstprinzipal anmelden, achten Sie auf die Nutzung von:

* **CLIENT-ID** - als Ihren Benutzername.
* **SCHLÜSSEL** - als Ihr Kennwort.

## Nächste Schritte
Erste Schritte

- [Übersicht des Azure Resource Manager](./resource-group-overview.md)  
- [Verwenden von Azure PowerShell mit Azure Resource Manager](./powershell-azure-resource-manager.md)
- [Verwenden der Azure CLI für Mac, Linux und Windows mit Azure Resource Management](./xplat-cli-azure-resource-manager.md)  
- [Verwenden des Azure-Portals zur Verwaltung Ihrer Azure-Ressourcen](./resource-group-portal.md)  
  
Erstellen und Einsetzen von Anwendungen
  
- [Erstellen von Azure Resource Manager-Vorlagen](./resource-group-authoring-templates.md)  
- [Bereitstellen einer Anwendung mit einer Azure Resource Manager-Vorlage](./resource-group-template-deploy.md)  
- [Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure](./resource-group-deploy-debug.md)  
- [Funktionen von Azure Resource Manager-Vorlagen](./resource-group-template-functions.md)  
- [Erweiterte Vorlagenanwendung](./resource-group-advanced-template.md)  
- [Bereitstellen von Azure-Ressourcen mithilfe von .NET-Bibliotheken und einer Vorlage](./arm-template-deployment.md)
  
Organisieren von Ressourcen
  
- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](./resource-group-using-tags.md)  
  
Zugriffsverwaltung und -überwachung
  
- [Verwalten und Überwachen des Ressourcen-Zugriffs](./resource-group-rbac.md)  
- [Authentifizieren eines Dienstprinzipals mit Azure Resource Manager](./resource-group-authenticate-service-principal.md)  

<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!--HONumber=52-->
