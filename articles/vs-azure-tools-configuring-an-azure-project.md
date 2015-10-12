<properties
   pageTitle="Konfigurieren eines Azure-Clouddienstprojekts mit Visual Studio | Microsoft Azure"
   description="In diesem Artikel erfahren Sie, wie Sie ein Azure-Clouddienstprojekt abhängig von den Anforderungen für dieses Projekt in Visual Studio konfigurieren."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/29/2015"
   ms.author="kempb" />

# Konfigurieren eines Azure-Clouddienstprojekts mit Visual Studio

Sie können ein Azure-Clouddienstprojekt abhängig von den Anforderungen für dieses Projekt konfigurieren. Sie können für die folgenden Kategorien Eigenschaften für das Projekt festlegen:

- **Veröffentlichen eines Clouddiensts in Azure**

  Sie können eine Eigenschaft festlegen, um sicherzustellen, dass ein bereits in Azure bereitgestellter Clouddienst nicht versehentlich gelöscht wird.

- **Ausführen oder Debuggen eines Clouddiensts auf dem lokalen Computer**

  Sie können eine Dienstkonfiguration auswählen, mit der angegeben wird, ob der Azure-Speicheremulator gestartet werden soll.

- **Überprüfen eines Clouddienstpakets bei der Erstellung**

  Sie können festlegen, dass alle Warnungen als Fehler behandelt werden, damit sichergestellt ist, dass das Clouddienstpaket ohne Probleme bereitgestellt wird. Dies reduziert die Wartezeit, wenn Sie nach der Bereitstellung feststellen, dass ein Fehler aufgetreten ist.

Die folgende Abbildung zeigt das Auswählen einer Konfiguration, die beim lokalen Ausführen oder Debuggen des Clouddiensts verwendet wird. Sie können alle benötigten Projekteigenschaften über dieses Fenster festlegen, wie in der Abbildung dargestellt.

![Konfigurieren eines Microsoft Azure-Projekts](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## So konfigurieren Sie ein Azure-Clouddienstprojekt

1. Öffnen Sie zum Konfigurieren eines Clouddienstprojekts im **Projektmappen-Explorer** das Kontextmenü für das Clouddienstprojekt, und wählen Sie dann **Eigenschaften** aus.

  Eine Seite mit dem Namen des Clouddienstprojekts wird im Visual Studio-Editor angezeigt.

1. Öffnen Sie die Registerkarte **Entwicklung**.

1. Um sicherzustellen, dass Sie nicht versehentlich eine vorhandene Bereitstellung in Azure löschen, wählen Sie in der Aufforderung vor der Liste zum Löschen einer vorhandenen Bereitstellung **True** aus.

1. Wählen Sie zum Auswählen der Dienstkonfiguration, die beim lokalen Ausführen oder Debuggen des Clouddiensts verwendet werden soll, in der Liste **Dienstkonfiguration** die Dienstkonfiguration aus.

  >[AZURE.NOTE]Wenn Sie eine zu verwendende Dienstkonfiguration erstellen möchten, finden Sie weitere Informationen unter "Vorgehensweise: Verwalten von Dienstkonfigurationen und Profilen". Wenn Sie eine Dienstkonfiguration für eine Rolle ändern möchten, finden Sie weitere Informationen unter [Konfigurieren der Rollen für einen Azure-Clouddienst mit Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Wählen Sie zum Starten des Azure-Speicheremulators beim lokalen Ausführen oder Debuggen des Clouddiensts unter **Azure-Speicheremulator starten** die Option **True** aus.

1. Um sicherzustellen, dass die Veröffentlichung bei Paketvalidierungsfehlern nicht möglich ist, wählen Sie unter **Warnungen als Fehler behandeln** die Option **True** aus.

1. Um sicherzustellen, dass die Webrolle bei jedem lokalen Start in IIS Express den gleichen Port verwendet, wählen Sie unter **Webprojektports verwenden** die Option **True** aus. Wenn ein bestimmter Port für ein bestimmtes Webprojekt verwendet werden soll, öffnen Sie das Kontextmenü für das Webprojekt, wählen Sie die Registerkarte **Eigenschaften** aus, wählen Sie die Registerkarte **Web** aus, und ändern Sie die Portnummer in der Einstellung **Projekt-URL** im Abschnitt **IIS Express**. Geben Sie beispielsweise `http://localhost:14020` als Projekt-URL ein.

1. Wählen Sie zum Speichern der Änderungen, die Sie an den Eigenschaften des Clouddienstprojekts vorgenommen haben, auf der Symbolleiste die Schaltfläche **Speichern** aus.

## Nächste Schritte

Weitere Informationen zum Konfigurieren von Azure-Clouddienstprojekten in Visual Studio finden Sie im Artikel zum [Konfigurieren Ihres Azure-Projekts mit mehreren Dienstkonfigurationen](vs-azure-tools-multiple-services-project-configurations.md).

<!---HONumber=Oct15_HO1-->