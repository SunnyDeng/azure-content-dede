<properties
   pageTitle="Veröffentlichen von Anwendungen für einzelne Benutzer in einer Azure RemoteApp-Sammlung (Vorschau) | Microsoft Azure"
   description="Erfahren Sie, wie Sie Apps in Azure RemoteApp für einzelne Benutzer veröffentlichen können, und nicht mehr nur für ganze Gruppen."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="03/01/2016"
   ms.author="piotrci"/>

# Veröffentlichen von Anwendungen für einzelne Benutzer in einer Azure RemoteApp-Sammlung (Vorschau)

Dieser Artikel erklärt, wie Anwendungen für einzelne Benutzer in einer Azure RemoteApp-Sammlung veröffentlicht werden. Dies ist eine neue Funktion in Azure RemoteApp, die derzeit nur bestimmten Erstanwendern zu Evaluierungszwecken in der privaten Vorschau zur Verfügung steht.

Ursprünglich hat Azure RemoteApp nur eine Methode zum Veröffentlichen von Anwendungen ermöglicht: Apps werden vom Administrator aus dem Image veröffentlicht und sind in der Sammlung für alle Benutzern sichtbar.

Häufig sind in einem Image viele Anwendungen enthalten, und zum Verringern der Verwaltungskosten wird eine Sammlung bereitgestellt. Oft sind nicht alle Anwendungen für alle Benutzer relevant, und Administratoren würden es vorziehen, Apps für individuelle Benutzer zu veröffentlichen, damit in deren Anwendungsfeed keine nicht benötigten Anwendungen erscheinen.

Dies ist in Azure RemoteApp nun als beschränkte Vorschaufunktion möglich. Im Folgenden finden Sie eine kurze Zusammenfassung dieser neuen Funktion:

1. Für eine Sammlung können zwei Modi festgelegt werden:
 
  - der ursprüngliche Sammlungsmodus, in dem alle Benutzer in einer Sammlung alle veröffentlichten Anwendungen sehen können. Dies ist der Standardmodus.
  - der neue Anwendungsmodus, in dem Benutzern nur ihnen explizit zugewiesene Anwendungen angezeigt werden.

2. Der Anwendungsmodus kann momentan nur mithilfe der Azure RemoteApp-PowerShell-Cmdlets aktiviert werden.

  - Im Anwendungsmodus kann die Benutzerzuweisung in der Sammlung nicht über das Azure-Portal verwaltet werden. Die Verwaltung der Benutzerzuweisung erfolgt über PowerShell-Cmdlets.

3. Benutzer sehen nur die Anwendungen, die direkt für sie veröffentlicht wurden. Unter Umständen kann ein Benutzer die anderen auf dem Image verfügbaren Anwendungen weiterhin starten, indem er direkt im Betriebssystem darauf zugreift.
  - Die Anwendungen werden durch diese Funktion nicht gesperrt, sie schränkt nur deren Sichtbarkeit im Anwendungsfeed ein.
  - Wenn Sie Benutzer von Anwendungen isolieren müssen, benötigen Sie dafür separate Sammlungen.

## Gewusst wie: Abrufen von Azure RemoteApp PowerShell-Cmdlets

Zum Testen der neuen Vorschaufunktion müssen Sie Azure PowerShell-Cmdlets verwenden. Derzeit ist es nicht möglich, das Azure-Verwaltungsportal zum Aktivieren des neuen Anwendungsveröffentlichungsmodus zu aktivieren.

Zunächst muss das [Azure PowerShell-Modul](../powershell-install-configure.md) installiert sein.

Starten Sie danach die PowerShell-Konsole im Administratormodus, und führen Sie das folgende Cmdlet aus:

		Add-AzureAccount

Dieses fordert Sie zur Eingabe Ihres Azure-Benutzernamens sowie Ihres Kennworts auf. Nach der Anmeldung können Sie Azure RemoteApp-Cmdlets für Ihre Azure-Abonnements ausführen.

## Überprüfen, in welchem Modus sich eine Auflistung befindet

Führen Sie das folgende Cmdlet aus:

		Get-AzureRemoteAppCollection <collectionName>

![Überprüfen Sie den Modus der Sammlung.](./media/remoteapp-perapp/araacllelvel.png)

Die AclLevel-Eigenschaft kann die folgenden Werte aufweisen:

- Sammlung: der ursprüngliche Veröffentlichungsmodus. Veröffentlichte Apps werden allen Benutzern angezeigt.
- Anwendung: der neue Veröffentlichungsmodus. Benutzer sehen nur die Apps, die direkt für sie veröffentlicht wurden.

## Gewusst wie: Wechseln zum Anwendungsveröffentlichungsmodus

Führen Sie das folgende Cmdlet aus:

		Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

Der Status der Anwendungsveröffentlichung bleibt beibehalten: Zunächst sehen alle Benutzer alle ursprünglich veröffentlichten Apps.

## Gewusst wie: Auflisten von Benutzern, denen eine bestimmte Anwendung angezeigt wird

Führen Sie das folgende Cmdlet aus:

		Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Dadurch werden alle Benutzer aufgelistet, die die Anwendung sehen können.

Hinweis: Sie können die Anwendungsaliase (in der oben angezeigten Syntax als „app alias“ bezeichnet) anzeigen, indem Sie Get-AzureRemoteAppProgram -CollectionName <collectionName> ausführen.

## Gewusst wie: Zuweisen einer Anwendung für einen Benutzer

Führen Sie das folgende Cmdlet aus:

		Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

Dem Benutzer wird die Anwendung im Azure RemoteApp-Client nun angezeigt, und Sie können eine Verbindung damit herstellen.

## Gewusst wie: Entfernen einer Anwendung von einem Benutzer

Führen Sie das folgende Cmdlet aus:

		Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## Senden von Feedback
Wir freuen uns auf Ihr Feedback und Ihre Vorschläge zu dieser Vorschaufunktion. Bitte füllen Sie die [Umfrage](http://www.instant.ly/s/FDdrb) aus, und teilen Sie uns Ihre Meinung mit.

## Konnten Sie die Vorschaufunktion noch nicht ausprobieren?
Wenn Sie an der Vorschau noch nicht teilgenommen haben, fordern Sie über diese [Umfrage](http://www.instant.ly/s/AY83p) Zugriff an.

<!---HONumber=AcomDC_0302_2016-->