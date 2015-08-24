<properties
   pageTitle="Azure-Ressourcen-Connector"
   description="Azure-Ressourcen-Connector"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/09/2015"
   ms.author="stepsic"/>

# Azure-Ressourcen-Connector
Der Azure-Ressourcen-Connector bietet Ihnen eine einfache Möglichkeit zum Verwalten von Azure-Ressourcen in Ihrer Logik-App.

## Erstellen des Azure-Ressourcen-Connectors
Um die API-App für den Azure-Ressourcen-Connector verwenden zu können, müssen Sie zuerst eine Instanz der App erstellen. Dies kann entweder inline beim Erstellen einer Logik-App oder durch Auswählen der API-App für den Azure-Ressourcen-Manager-Connector aus dem Azure Marketplace erfolgen.

Um sie zu konfigurieren, müssen Sie einen Dienstprinzipal mit Berechtigungen für alles, was Sie in Azure ausführen möchten, einrichten. Alle Aufrufe erfolgen im Namen dieses Dienstprinzipals, den Sie eingerichtet haben. Dadurch können Sie für den Connector festlegen, dass er nur genau die gewünschten Bereiche verwendet, und nicht mehr.

David Ebbo hat [einen hervorragenden Blogbeitrag](http://blog.davidebbo.com/2014/12/azure-service-principal.html) mit einer Anleitung dieser Einrichtung geschrieben. Folgen Sie den Anweisungen dieses Beitrags, und rufen Sie Ihre **Mandanten-ID**, **Client-ID** und den **geheimen Schlüssel** ab. Diese drei Felder sowie die **Abonnement-ID** sind zum Konfigurieren des Connectors erforderlich.

## Verwenden des Azure-Ressourcen-Connectors im Logik-App-Designer
### Trigger
Es gibt zwei Trigger, die im Connector unterstützt werden:

Name | Beschreibung 
---- | ----------- 
Ereignis tritt auf | Trigger, wenn ein Ereignis für eine Ressource in Ihrem Abonnement auftritt. 
Metrik überschreitet Schwellenwert | Trigger, wenn eine Metrik einen bestimmten Schwellenwert erreicht.

### Aktion

Ebenso können Sie eine große Anzahl von Aktionen innerhalb Ihres Azure-Abonnements bereitstellen:
 
Aktionen für **Ressourcengruppen**:

Name | Beschreibung 
---- | -----------
Ressourcengruppen auflisten | Listen Sie alle Ressourcengruppen im Abonnement auf.
Ressourcengruppe abrufen | Rufen Sie eine Ressourcengruppe mit der ID ab.
Ressourcengruppe erstellen | Erstellen oder aktualisieren Sie eine Ressourcengruppe.
Ressourcengruppe löschen | Löschen Sie eine Ressourcengruppe.

Aktionen für **Ressourcen**:

Name | Beschreibung 
---- | -----------
Ressourcen auflisten | Listen Sie Ressourcen in Ihrem Abonnement mit verschiedenen Arten von Filtern auf.
Ressource abrufen | Rufen Sie eine einzelne Ressource mit der Ressourcen-ID ab.
Ressource erstellen oder aktualisieren | Erstellen Sie eine Ressource, oder aktualisieren Sie eine vorhandene Ressource. Sie müssen alle Eigenschaften für diese Ressource angeben.
Ressourcenaktion | Führen Sie eine andere Aktion für eine Ressource aus. Sie müssen den Namen der Aktion und die etwaige Nutzlast dieser Aktion kennen.
Ressource löschen | Löschen Sie eine Ressource.

Aktionen für **Ressourcenanbieter**:

Name | Beschreibung 
---- | -----------
Ressourcenanbieter auflisten | Listen Sie alle verfügbaren Ressourcenanbieter im Abonnement auf.

Aktionen für **Ressourcengruppenbereitstellungen**:

Name | Beschreibung 
---- | -----------
Bereitstellungen auflisten | Listen Sie alle Bereitstellungen in einer Ressourcengruppe auf.
Bereitstellung abrufen | Rufen Sie eine Vorlagenbereitstellung mit der ID ab.
Bereitstellung erstellen | Erstellen Sie eine neue Ressourcengruppenbereitstellung durch Bereitstellen einer Vorlage.

Aktionen für **Ereignisse** zu Ressourcen:

Name | Beschreibung
---- | -----------
Ereignisse abrufen | Rufen Sie Ereignisse in einem Abonnement für eine Ressource ab.

Aktionen für **Metriken**:

Name | Beschreibung
---- | -----------
Metriken abrufen | Rufen Sie eine Metrik für eine Ressourcen-ID ab.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [API-Apps und Connector verwalten und überwachen](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=August15_HO7-->