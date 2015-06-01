<properties 
	pageTitle="Übersicht über das Microsoft Azure-Vorschauportal" 
	description="Erfahren Sie, wie Sie das Microsoft Azure-Vorschauportal verwenden können." 
	services="" 
	documentationCenter="" 
	authors="davidwrede" 
	manager="dwrede" 
	editor="jimbe"/>

<tags 
	ms.service="na" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="dwrede"/>

# Übersicht über das Microsoft Azure-Vorschauportal

Das Microsoft Azure-Vorschauportal ist eine zentrale Stelle zum Bereitstellen und Verwalten von Azure-Ressourcen. Dieses Lernprogramm macht Sie mit dem Portal vertraut und zeigt Ihnen, wie die folgenden wichtigen Funktionen verwendet werden:
 - Der **Marketplace**, auf dem Sie Tausende Artikel von Microsoft oder Drittanbietern durchsuchen können, die gekauft und/oder bereitgestellt werden können. 
 - **Einheitliche und skalierbare Suchfunktion**, mit der Sie die Ressourcen, die Sie interessieren, leichter finden und verschiedene Verwaltungsvorgänge ausführen können.
 - **Konsistente Verwaltungsbereiche** („Blades“), mit denen Sie die große Vielfalt der Azure-Dienste verwalten können und die Ihnen Einstellungen, Aktionen, Abrechnungsinformationen, Zustandsdaten, Nutzungsdaten und vieles mehr auf einheitliche Art und Weise zur Verfügung stellen. 
 - Ein **individuelles Benutzererlebnis**, das es Ihnen ermöglicht, eine persönliche Startseite zu erstellen, die Ihnen bei jedem Anmelden die gewünschten Informationen anzeigt. Sie können auch die Verwaltungsbereiche anpassen, die Kacheln enthalten.

 ![Azure-Portal-UI-Ausrichtung][UIOrientation]

## Bevor Sie beginnen

Um dieses Lernprogramm zu bearbeiten, benötigen Sie ein gültiges Azure-Abonnement. Wenn Sie kein Azure-Abonnement haben, registrieren Sie sich noch heute [für eine kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/). Sobald Sie über ein Abonnement verfügen, können Sie unter [https://portal.azure.com] auf das Portal zugreifen.

## Erstellen einer Ressource

Azure verfügt über einen Markt mit Tausenden von Elementen, die Sie von einem zentralen Ort aus erstellen können. Angenommen, Sie möchten einen neuen Windows Server 2012-VM erstellen. Der Hub +NEU ist Ihr Zugriffspunkt auf eine gut sortierte Reihe wichtiger Kategorien aus dem Marketplace. Jede Kategorie verfügt über eine kleine Gruppe bestimmter Elemente und einen Link zum Marketplace mit allen Kategorien und der Suchfunktion. Um die neue Windows Server 2012-VM zu erstellen, führen Sie die folgenden Aktionen aus:

1.	Windows Server 2012 gehört zu den ausgewählten Elementen, sodass Sie über die Kategorie „Compute“ darauf zugreifen können.  
2.	Füllen Sie das Formular mit einigen grundlegenden Angaben aus.
3.	Klicken Sie auf „Erstellen“ und der virtuelle Computer beginnt sofort mit der Bereitstellung. 

Der Hub „Benachrichtigungen“ informiert Sie, wenn die Ressource erstellt wurde, und ein Verwaltungsbereich wird geöffnet (später können Sie zu den Ressourcen gehen).

![Portal-Kategorien][PortalCategories]


## Suchen von Ressourcen

Häufig verwendete Ressourcen können Sie an Ihr Startmenü anheften. Möglicherweise möchten Sie aber nach etwas suchen, worauf Sie weniger häufig zugreifen. Der unten gezeigte Hub „Durchsuchen“ bietet die Möglichkeit zum Zugriff auf alle Ihre Ressourcen. Sie können nach Abonnements filtern, Spalten auswählen und deren Größe ändern und durch Anklicken der einzelnen Elemente zu den Verwaltungsbereichen navigieren.

![Hub durchsuchen][BrowseHub]

## Verwalten und Delegieren des Zugriffs auf eine Ressource

Von diesem Bereich aus können Sie sich mithilfe des Remotedesktop mit dem virtuellen Computer verbinden, wichtige Leistungsmetriken überwachen, den Zugriff auf den virtuellen Computer über rollenbasierten Zugriff (RBAC) steuern, den virtuellen Computer konfigurieren und andere wichtige Verwaltungsaufgaben ausführen. Das Delegieren des Zugriffs auf der Grundlage von Rollen ist entscheidend für skalierbares Verwalten. Klicken Sie  [hier](role-based-access-control-configure.md), um mehr darüber zu erfahren. Um den Zugriff auf eine Ressource zu delegieren, führen Sie die folgenden Aktionen aus:

1.	Navigieren Sie zu der Ressource.
2.	Klicken Sie im Abschnitt „Essentials“ auf „Alle Einstellungen“.
3.	Klicken Sie in der Liste der Einstellungen auf „Benutzer“.
4.	Klicken Sie in der Befehlsleiste auf „Hinzufügen“.
5.	Wählen Sie einen Benutzer und eine Rolle.

![Verwalten von Ressourcen][ManageResource]

## Anpassen eines Ressourcenbereichs

Die Bereiche für Ihre Ressourcen werden von Azure konfiguriert; die Kacheln in diesen Bereichen können jedoch von Ihnen angepasst werden. Sie können problemlos in den Anpassungsmodus wechseln, um die Kacheln zu entfernen, ihre Größe zu verändern oder sie anders anzuordnen. Um einen Bereich anzupassen, führen Sie folgende Aktionen aus:

1.	Navigieren Sie zu der Ressource.
2.	Klicken Sie oben in dem Bereich, den Sie anpassen möchten, auf „...“.
3.	Klicken Sie auf „Elemente hinzufügen“.
4.	Nun können Sie Elemente per Drag & Drop hinzufügen.  

![Anpassen von Bereichen (Blades)][CustomizeBlades]

## So erhalten Sie Hilfe

Sollten Sie auf ein Problem stoßen, sind wir für Sie da. Das Portal verfügt über eine Hilfe- und Support-Seite, die Sie in die richtige Richtung verweisen kann. Abhängig von Ihrem [Supportplan](http://azure.microsoft.com/support/plans/) können Sie Supportanfragen ggf. auch direkt im Portal erstellen. Nach dem Erstellen einer Supportanfrage können Sie den Lebenszyklus des Tickets vom Portal aus verwalten. Um zur Hilfe- und Support-Seite zu gelangen, gehen Sie zu „Durchsuchen“ und dann zu „Hilfe + Support“.

![Hilfe und Support][HelpSupport]

## Zusammenfassung

Überprüfen wir, was Sie in diesem Lernprogramm gelernt haben:

 - Sie haben gelernt, sich zu registrieren, ein Abonnement zu erhalten und zu dem Portal zu navigieren
 - Sie haben die Benutzeroberfläche des Portals kennengelernt und erfahren, wie man Ressourcen erstellt und danach sucht 
 - Sie haben die Struktur des Portals bzw. die Verwaltungsbereiche (Blades) kennengelernt und erfahren, wie verschiedene Arten von Ressourcen einheitlich verwaltet werden können 
 - Sie wissen nun, wie Sie das Portal anpassen können, um die gewünschten Informationen griffbereit zu haben 
 - Sie wissen nun, wie der Zugriff auf Ressourcen mithilfe des rollenbasierten Zugriffs (RBAC) gesteuert werden kann 
 - Sie haben erfahren, wie Sie auf Hilfe und Support zugreifen können.

Das Microsoft Azure-Vorschauportal vereinfacht die Erstellung und Verwaltung Ihrer Anwendungen in der Cloud radikal. Um auf dem Laufenden zu bleiben, besuchen Sie unseren [Verwaltungsblog](http://azure.microsoft.com/blog/topics/management/). Wir sind stets offen für [Feedback](http://feedback.azure.com/forums/223579-azure-preview-portal) und nehmen ständig Verbesserungen vor. Der Blog von [ScottGu](http://weblogs.asp.net/scottgu) ist eine weitere umfangreiche Quelle für alle Azure-Aktualisierungen.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png

<!--HONumber=52-->
