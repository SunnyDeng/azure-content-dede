<properties 
	pageTitle="Bereitstellen von Visual Studio aus" 
	description="Erstellen Sie ein Projekt in Visual Studio, um Ihre Logik-App zu verwalten." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/30/2015"
	ms.author="stepsic"/>
	
# Bereitstellen von Visual Studio aus

Auch wenn das [Azure-Portal](https://portal.azure.com) Ihnen eine hervorragende Möglichkeit zum Entwerfen und Verwalten von Logik-Apps bietet, können Sie die Logik-App stattdessen auch von Visual Studio aus bereitstellen. Es gibt einige wichtige Funktionen, die auf diese Weise ermöglicht werden:

- Speichern Sie Ihre Logik-App zusammen mit den anderen Ressourcen in Ihrer Projektmappe, sodass alle Aspekte Ihrer Anwendung darin enthalten sind.
- Lassen Sie die Logik-App-Definition in der Quellcodeverwaltung eingecheckt, damit Sie Überarbeitungen mit TFS oder Git verfolgen können. 

Sie müssen das Azure SDK 2.7 oder höher installiert haben, um die folgenden Schritte durchführen zu können. Suchen Sie hier [das neueste SDK für Visual Studio](http://azure.microsoft.com/downloads/).

## Erstellen eines Projekts

1. Klicken Sie auf das Menü **Datei**, und wählen Sie **Neu** > **Projekt** (oder wählen Sie **Hinzufügen** und dann **Neues Projekt**, um es einer vorhandenen Projektmappe hinzuzufügen). ![Menü "Datei"](./media/app-service-logic-deploy-from-vs/filemenu.png)

2. Finden Sie im Dialogfeld den Eintrag **Cloud**, und wählen Sie dann **Azure-Ressourcengruppe**. Geben Sie einen **Namen** ein, und klicken Sie dann auf **OK**. ![Neues Projekt hinzufügen](./media/app-service-logic-deploy-from-vs/addnewproject.png)

3. Jetzt müssen Sie auswählen, ob Sie eine **Logik-App** oder eine **Logik-App + API-App** erstellen möchten. Bei Auswahl von **Logik-App** müssen Sie auf vorhandene APIs verweisen. Wenn Sie die Option **Logik-App + API-App** auswählen, können Sie gleichzeitig auch eine neue, leere API-App erstellen. ![Azure-Vorlage auswählen](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

4. Nachdem Sie die **Vorlage** ausgewählt haben, klicken Sie auf **OK**.

Ihr Logik-App-Projekt wird der Projektmappe hinzugefügt. Die Bereitstellung sollte im Projektmappen-Explorer angezeigt werden: ![Bereitstellung](./media/app-service-logic-deploy-from-vs/deployment.png)

## Konfigurieren Ihrer Logik-App

Sobald Sie ein Projekt erstellt haben, können Sie die Definition der Logik-App innerhalb von Visual Studio bearbeiten. Klicken Sie im Projektmappen-Explorer auf die JSON-Datei. Eine Platzhalterdefinition wird angezeigt, die Sie mit der Logik der Anwendung ausfüllen können.

Es wird empfohlen, in der gesamten Definition **Parameter** zu verwenden. Das ist nützlich, wenn Sie ein Projekt sowohl in einer Entwicklungs- als auch in einer Produktionsumgebung bereitstellen möchten. In diesem Fall sollten Sie die gesamte umgebungsspezifische Konfiguration in die `.param`-Datei eingeben und die Parameter anstelle der eigentlichen Zeichenfolgen verwenden.

Derzeit bietet Visual Studio keinen integrierten Designer. Wenn Sie daher eine grafische Benutzeroberfläche nutzen möchten (statt JSON zu schreiben), müssen Sie das Azure-Portal verwenden.

Wenn Sie zuvor eine Logik-App innerhalb des Azure-Portals erstellt haben und diese jetzt in die Quellcodeverwaltung einchecken möchten, haben Sie drei verschiedene Möglichkeiten: – Wechseln Sie im Portal zur **Codeansicht**, und kopieren Sie die Definition. – Verwenden Sie die [REST-API](https://msdn.microsoft.com/library/azure/dn948510.aspx) für Logik-Apps, um die Definition abzurufen. – Verwenden Sie die [Powershell des Azure-Ressourcen-Managers](../powershell-azure-resource-manager.md), insbesondere den [`Get-AzureResource`-Befehl](https://msdn.microsoft.com/library/dn654579.aspx), um die Definition herunterzuladen.

## Bereitstellen Ihrer Logik-App

Nach der Konfiguration Ihrer App können Sie sie in nur wenigen Schritten direkt von Visual Studio aus bereitstellen.

1. Klicken Sie mit der rechten Maustaste auf den Projektmappen-Explorer, und wechseln Sie zu **Bereitstellen** > **Neue Bereitstellung...** ![Neue Bereitstellung](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Sie werden dazu aufgefordert, sich bei Ihrem Azure-Abonnement anzumelden.

3. Nun müssen Sie die Details der Ressourcengruppe auswählen, der Sie die Logik-App bereitstellen möchten. ![Für Ressourcengruppe bereitstellen](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

    Achten Sie darauf, die richtigen Vorlagen- und Parameterdateien für die Ressourcengruppe auszuwählen. (Wenn Sie die App zum Beispiel in einer Produktionsumgebung bereitstellen möchten, sollten Sie die Parameterdatei der Produktion auswählen).
    
4. Der Status der Bereitstellung wird im Fenster **Ausgabe** angezeigt. (Möglicherweise müssen Sie die Option **Azure-Bereitstellung** wählen.) ![Ausgabe](./media/app-service-logic-deploy-from-vs/output.png)

In Zukunft können Sie Ihre Logik-App in der Quellcodeverwaltung überarbeiten und Visual Studio zum Bereitstellen neuer Versionen verwenden. Wenn Sie die Definition direkt im Azure-Portal ändern, beachten Sie, dass diese Änderungen bei der nächsten Bereitstellung in Visual Studio außer Kraft gesetzt werden.

Wenn Sie nicht Visual Studio verwenden, aber dennoch die Werkzeuge zur Verfügung haben möchten, um Ihre Logik-App von der Quellcodesteuerung aus bereitzustellen, können Sie immer die [API](https://msdn.microsoft.com/library/azure/dn948510.aspx) oder [Powershell](../powershell-azure-resource-manager.md) direkt verwenden, um Ihre Bereitstellungen zu automatisieren.

<!---HONumber=Oct15_HO3-->