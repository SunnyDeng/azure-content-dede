<properties 
	pageTitle="Ausstellername und Ausstellerschlüssel in BizTalk Services | Microsoft Azure"
	description="Erfahren Sie, wie Sie den Ausstellernamen und -schlüssel für Service Bus oder Access Control (ACS) in BizTalk Services abrufen. MABS, WABS"
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2015"
	ms.author="mandia"/>




# BizTalk Services: Name und Schlüssel des Ausstellers

Azure BizTalk Services verwendet Ausstellername und -schlüssel von Service Bus und Access Control. Dies gilt insbesondere in folgenden Fällen:

Aufgabe | Welcher Ausstellername und -schlüssel?
--- | ---
Bereitstellen einer Anwendung in Visual Studio | Access Control-Ausstellername und -schlüssel
Konfigurieren des Azure BizTalk Services-Portals | Access Control-Ausstellername und -schlüssel
Erstellen von LOB-Relays mit den BizTalk Adapterdiensten in Visual Studio | Service Bus-Ausstellername und -schlüssel

Dieses Thema beschreibt die notwendigen Schritte zum Abrufen von Ausstellername und -schlüssel.

## Access Control-Ausstellername und -schlüssel
Access Control-Ausstellername und -schlüssel werden von den folgenden Komponenten verwendet:

- Ihre in Visual Studio erstellte Azure BizTalk Service-Anwendung: Um Ihre BizTalk Service-Anwendung in Visual Studio für Azure bereitzustellen, müssen Sie Access Control-Ausstellername und -schlüssel eingeben. 
- Das Azure BizTalk Services-Portal: Wenn Sie einen BizTalk Service erstellen und das BizTalk Services-Portal öffnen, werden Ihr Access Control-Ausstellername und -schlüssel automatisch für Ihre Bereitstellungen mit den gleichen Access Control-Werten registriert.

### So kopieren Sie Access Control-Ausstellername und -schlüssel und fügen sie ein

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Klicken Sie im linken Navigationsbereich auf **BizTalk Services**.
3. Wählen Sie Ihren BizTalk Service aus. 
4. Wählen Sie **Connection Information** in der Taskleiste aus. Dort sind Access Control-Namespace, -Standardaussteller (Ausstellername) und -Standardschlüssel (Ausstellerschlüssel) aufgelistet. Sie können diese Werte kopieren und einfügen.  

Zusammenfassung: Ausstellername = Standardaussteller Ausstellerschlüssel = Standardschlüssel


Alternativ können Sie auf **ACS-Verwaltungsportal öffnen** klicken, um die Access Control-Werte abzurufen:

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Klicken Sie im linken Navigationsbereich auf **BizTalk Services**.
3. Wählen Sie Ihren BizTalk Service aus.
4. Klicken Sie auf die Schaltfläche "Verbindungsinformationen", und wählen Sie **ACS-Verwaltungsportal öffnen** aus.
5. Klicken Sie im Portal unter **Diensteinstellungen** auf **Dienstidentitäten**. Hier wird Ihre Dienstidentität angezeigt, die gleichzeitig Ihr Access Control-Ausstellername ist. Klicken Sie auf den Link Ihrer Dienstidentität, um das Kennwort anzuzeigen, das gleichzeitig Ihr Ausstellerschlüssel ist. Diese Werte können kopiert werden.<br/><br/> Unter **Dienstidentitäten** sehen Sie z. B. "owner". Bei "owner" handelt es sich um Ihren Access Control-Ausstellernamen. Wenn Sie auf den "owner"-Link klicken, wird **Kennwort** angezeigt. Wenn Sie auf den Link "Kennwort" klicken, wird der Wert angezeigt. Dieser Kennwortwert ist Ihr Access Controller-Ausstellerschlüssel.  

Zusammenfassung: Ausstellername = Dienstidentitätsname Ausstellerschlüssel = Kennwortwert

Im linken Navigationsbereich können Sie außerdem **Active Directory** auswählen, um die Access Control-Werte abzurufen.

> [AZURE.IMPORTANT]Wenn Sie einen Access Control-Namespace mit **Active Directory** erstellen, wird die Dienstidentität **nicht** automatisch erstellt. Wenn Sie einen BizTalk Service einrichten, werden automatisch ein Access Control-Namespace, eine Dienstidentität mit dem Namen "owner" (Ausstellername), ein Kennwort (Ausstellerschlüssel) und ein symmetrischer Schlüssel erstellt.<br /> [Verwenden des ACS-Verwaltungsdiensts zum Konfigurieren von Dienstidentitäten](http://go.microsoft.com/fwlink/p/?LinkID=303942) bietet weitere Informationen zu Access Control Service-Identitäten.


## Service Bus-Ausstellername und -schlüssel
Service Bus-Ausstellername und -schlüssel werden von BizTalk-Adapterdiensten verwendet. In Ihrem BizTalk Services-Projekt in Visual Studio können Sie die BizTalk-Adapterdienste verwenden, um sich mit einem lokalen LOB-System (Line of Business) zu verbinden. Erstellen Sie dazu das LOB-Relay und geben Sie Ihre LOB-Systemdetails ein. Bei diesem Prozess geben Sie ebenfalls Ihren Service Bus-Ausstellernamen und -schlüssel ein.

### Abrufen von Service Bus-Ausstellername und -schlüssel

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Klicken Sie im linken Navigationsbereich auf **Service Bus**.
3. Wählen Sie Ihren Namespace aus. Klicken Sie in der Taskleiste auf **Verbindungsinformationen**: Daraufhin werden **Standardaussteller** (Ausstellername) und **Standardschlüssel** (Ausstellerschlüssel) angezeigt. Diese Werte können kopiert werden.  

Zusammenfassung: Ausstellername = Standardaussteller Ausstellerschlüssel = Standardschlüssel

## Weiter
Zusätzliche Azure BizTalk Services-Themen:

-  [Installieren des Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Lernprogramme: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Wie verwende ich das Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## Weitere Informationen
-  [Verwenden des ACS-Verwaltungsdiensts zum Konfigurieren von Dienstidentitäten](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [BizTalk Services: Editionsübersicht](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Konfigurieren von BizTalk Services im Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services: Bereitstellungsstatusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 

<!---HONumber=August15_HO9-->