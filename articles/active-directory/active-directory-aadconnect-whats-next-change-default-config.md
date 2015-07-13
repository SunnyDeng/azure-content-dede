<properties 
	pageTitle="Ändern der Standardkonfiguration von Azure AD Connect" 
	description="Erfahren Sie, wie Sie die Standardkonfiguration für Azure AD Connect ändern." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Ändern der Standardkonfiguration von Azure AD Connect 


Die Standardkonfiguration von Azure AD Connect ist in den meisten Fällen ausreichend, um Ihre lokalen Verzeichnisse auf die Cloud zu erweitern. Es gibt jedoch bestimmte Fälle, in denen Sie die Standardeinstellungen ändern und der Geschäftslogik Ihrer Organisation anpassen müssen. In diesen Fällen können Sie die Standardkonfiguration ändern, wobei einige Dinge beachtet werden müssen, bevor Sie dies tun.

Wenn Sie die Standardkonfiguration ändern müssen, führen Sie folgende Schritte aus:

- Wenn Sie einen Attributfluss von einer integrierten Synchronisierungsregel anpassen müssen, verändern Sie diese nicht. Erstellen Sie stattdessen eine neue Synchronisierungsregel mit höherer Priorität (niedrigerem numerischem Werte), die den erforderlichen Attributfluss enthält.
- Exportieren Sie Ihre benutzerdefinierten Synchronisierungsregeln mit dem Synchronisierungsregel-Editor. Dadurch erhalten Sie ein PowerShell-Skript, mit dem Sie die Regeln bei einem Notfallwiederherstellungsszenario problemlos neu erstellen können.
- Wenn Sie den Umfang oder die Join-Einstellung einer integrierten Synchronisierungsregel ändern müssen, so dokumentieren Sie dies, und wenden Sie die Änderung nach dem Upgrade auf eine neuere Version von Azure AD Connect erneut an. 

<!---HONumber=62-->