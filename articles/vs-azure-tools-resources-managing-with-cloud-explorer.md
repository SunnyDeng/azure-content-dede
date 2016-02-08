<properties 
   pageTitle="Verwalten von Azure-Ressourcen mit dem Cloud-Explorer | Microsoft Azure"
   description="Informationen dazu, wie Sie Azure-Ressourcen mit dem Cloud-Explorer innerhalb von Visual Studio durchsuchen und verwalten."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/17/2015"
   ms.author="tarcher" />

# Verwalten von Azure-Ressourcen mit dem Cloud-Explorer

##Übersicht

Der Cloud-Explorer ist so ausgelegt, dass Sie Ihre Azure-Ressourcen innerhalb der integrierten Entwicklungsumgebung (Integrated Development Environment, IDE) von Visual Studio einfacher und schneller durchsuchen und verwalten können. Er dient z. B. zum Öffnen einer Web-App im Azure-Portal oder in einem Browser bzw. zum Anfügen eines Debuggers an die Web-App. Sie können im Cloud-Explorer auch die Eigenschaften eines Blobcontainers anzeigen oder diesen im Blobcontainer-Editor öffnen.

Der Cloud-Explorer setzt wie das Microsoft Azure-Vorschauportal auf dem Azure-Ressourcen-Manager-Stapel auf. Er unterstützt Ressourcen wie Azure-Ressourcengruppen und Azure-Dienste wie Logik- und API-Apps sowie die [rollenbasierte Zugriffskontrolle](../role-based-access-control-configure/) (RBAC). Um Azure-Ressourcen anzuzeigen, die hinzugefügt oder geändert wurden, wählen Sie auf der Cloud Explorer-Symbolleiste die Schaltfläche **Aktualisieren** aus.

Der Cloud-Explorer wird als Teil der Visual Studio-Tools für Azure SDK 2.7 installiert.

## Voraussetzungen

- Visual Studio 2015 RTM.

- Die Visual Studio-Tools für das Azure SDK.
- Sie benötigen ein Azure-Konto und müssen bei diesem angemeldet sein, um Azure-Ressourcen im Cloud-Explorer anzuzeigen. Wenn Sie noch kein Konto besitzen, können Sie in wenigen Minuten ein Konto einrichten. Wenn Sie über ein MSDN-Abonnement verfügen, lesen Sie [Azure-Vorteil für MSDN-Abonnenten](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Andernfalls finden Sie unter [Erstellen eines kostenlosen Testkontos](https://azure.microsoft.com/pricing/free-trial/) weitere Informationen.

- Wenn der Cloud-Explorer nicht angezeigt wird, können Sie ihn einblenden, indem Sie auf der Menüleiste **Ansicht**, **Weitere Fenster**, **Cloud-Explorer** wählen.

## Verwalten von Azure-Konten und -Abonnements

Um im Cloud-Explorer Ihre Azure-Ressourcen anzuzeigen, müssen Sie sich bei einem Azure-Konto mit mindestens einem aktiven Abonnement anmelden. Wenn Sie über mehrere Azure-Konto verfügen, können Sie sie im Cloud-Explorer hinzufügen und dann die Abonnements wählen, die Sie in die Ressourcenansicht im Cloud-Explorer einschließen möchten.

Wenn Sie Azure noch nicht verwendet oder Visual Studio nicht die erforderlichen Konten hinzugefügt haben, werden Sie dazu aufgefordert.

## So fügen Sie Azure-Konten dem Cloud-Explorer hinzu

1. Wählen Sie auf der Cloud Explorer-Symbolleiste das Symbol "Einstellungen".

1. Wählen Sie den Link **Konto hinzufügen** aus. Melden Sie sich beim Azure-Konto an, dessen Ressourcen Sie durchsuchen möchten. Das Konto, das Sie gerade hinzugefügt haben, sollte in der Dropdownliste der Kontoauswahl ausgewählt sein. Die Abonnements für dieses Konto werden unter dem Eintrag des Kontos angezeigt.

    ![Hinzufügen von Azure-Abonnements](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)

    ![Auswählen von Azure-Abonnements](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)

1. Aktivieren Sie die Kontrollkästchen für die Kontoabonnements, die Sie durchsuchen möchten, und wählen dann die Schaltfläche **Übernehmen** aus.

    Die Azure-Ressourcen für die ausgewählten Abonnements werden im Cloud-Explorer angezeigt.

## So entfernen Sie ein Azure-Konto

1. Wählen Sie auf der Menüleiste **Datei**, **Kontoeinstellungen**.

1. Wählen Sie im Dialogfeld **Kontoeinstellungen** im Abschnitt **Alle Konten** den Befehl **Entfernen** neben dem Konto aus, das Sie entfernen möchten. Beachten Sie, dass dieser Befehl das Konto nur aus Visual Studio entfernt, ohne dass das Azure-Konto selbst entfernt wird.

## Anzeigen von Ressourcentypen oder -gruppen

Um Ihre Azure-Ressourcen anzuzeigen, wählen Sie entweder die Ansicht **Ressourcentypen** oder **Ressourcengruppen** aus.

![Dropdownliste in der Ressourcenansicht](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

- In der Ansicht **Ressourcentypen**, die auch die übliche Ansicht im Azure-Portal ist, werden Ihre Azure-Ressourcen in Typkategorien wie Web-Apps, Speicherkonten und virtuellen Computern angezeigt. Dies ist ähnlich wie die Anzeige von Azure-Ressourcen im Server-Explorer.

- In der Ansicht "Ressourcengruppen" werden Azure-Ressourcen nach der Azure-Ressourcengruppe kategorisiert, der sie zugeordnet sind.

 
	Eine Ressourcengruppe ist ein Bündel von Azure-Ressourcen, die in der Regel von einer bestimmten Anwendung verwendet werden. Weitere Informationen zu Azure-Ressourcengruppen finden Sie unter [Übersicht über Azure-Ressourcen-Manager](https://azure.microsoft.com/documentation/articles/resource-group-overview/).

## Anzeigen von und Navigieren durch Ressourcen

Um zu einer Azure-Ressource zu navigieren und dazugehörige Informationen im Cloud-Explorer anzuzeigen, erweitern Sie den Typ des Elements oder die zugeordnete Ressourcengruppe und wählen dann die Ressource aus. Wenn Sie eine Ressource auswählen, werden Informationen auf den beiden Registerkarten unten in Cloud-Explorer angezeigt.

![Wählen einer Ressourcenansicht](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

- Auf der Registerkarte **Aktionen** werden die Aktionen gezeigt, die Sie im Cloud-Explorer auf die ausgewählte Ressource anwenden können. Sie können die für die Ressource verfügbaren Aktionen auch im Kontextmenü anzeigen.

- Auf der Registerkarte **Eigenschaften** werden die Eigenschaften der Ressource gezeigt, z. B. Typ, Gebietsschema und Ressourcengruppe, zu der sie gehört.

Für jede Ressource gibt es die Aktion **Im Portal öffnen**. Wenn Sie diese Aktion auswählen, zeigt der Cloud-Explorer die ausgewählte Ressource im Azure-Portal. Diese Funktion ist besonders nützlich für die Navigation zu tief geschachtelten Ressourcen.

Zusätzliche Aktionen und Eigenschaftswerte können auch basierend auf der Azure-Ressource angezeigt werden. Für Web- und Logik-Apps gibt es zusätzlich zu **Im Portal öffnen** die Aktionen **Im Browser öffnen** und **Debugger anfügen**. Aktionen zum Öffnen von Editoren werden angezeigt, wenn Sie ein Blob, eine Warteschlange oder Tabelle eines Speicherkontos auswählen. Für Azure-Apps stehen die Eigenschaften **URL** und **Status** zur Verfügung, während Speicherressourcen Schlüssel und Verbindungszeichenfolgen-Eigenschaften aufweisen.

## Durchsuchen von Ressourcen

Um in Ihren Azure-Kontoabonnements Ressourcen mit einem bestimmten Namen zu suchen, geben Sie den Namen in das Feld "Suchen" im Cloud-Explorer ein.

![Suchen von Ressourcen im Cloud-Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

Bei der Eingabe von Zeichen in das Feld "Suchen" werden nur die Ressourcen in der Ressourcenstruktur angezeigt, die diesen Zeichen entsprechen.

<!---HONumber=AcomDC_0128_2016-->