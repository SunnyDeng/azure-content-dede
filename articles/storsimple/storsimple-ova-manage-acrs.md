<properties 
   pageTitle="Verwalten von Access Control-Datensätzen für das StorSimple Virtual Array | Microsoft Azure"
   description="Es wird beschrieben, wie Sie mit Access Control-Datensätzen (Access Control Records, ACRs) bestimmen, welche Hosts Verbindungen mit einem Volume auf dem StorSimple Virtual Array herstellen können."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="v-sharos" />

# Verwenden des StorSimple Manager-Diensts zum Verwalten von Access Control-Datensätzen für das StorSimple Virtual Array (Vorschau)

## Übersicht

Mit Access Control-Datensätzen (Access Control Records, ACRs) können Sie angeben, welche Hosts eine Verbindung mit einem Volume im StorSimple Virtual Array (auch als lokales virtuelles StorSimple-Gerät bezeichnet) herstellen können. ACRs sind auf ein bestimmtes Volume festgelegt. Sie enthalten die qualifizierten iSCSI-Namen (IQN) der Hosts. Wenn ein Host versucht, eine Verbindung mit einem Volume herzustellen, überprüft das Gerät den ACR zu diesem Volume anhand des IQN-Namens. Bei einer Übereinstimmung wird eine Verbindung hergestellt. Im Abschnitt mit den **Zugriffssteuerungsdatensätzen** (Access Control-Datensätzen) auf der Seite **Konfigurieren** werden alle Zugriffssteuerungsdatensätze zu den IQNs der Hosts angezeigt.

In diesem Tutorial werden die folgenden häufig durchgeführten ACR-bezogenen Aufgaben erläutert:

- Abrufen des IQN
- Hinzufügen von Zugriffssteuerungsdatensätzen 
- Bearbeiten von Zugriffssteuerungsdatensätzen 
- Löschen von Zugriffssteuerungsdatensätzen 

> [AZURE.IMPORTANT] 
> 
> - Achten Sie beim Zuweisen eines ACR zu einem Volume darauf, dass dabei nicht gleichzeitig von mehreren nicht gruppierten Hosts auf das Volume zugegriffen wird, da dadurch das Volume beschädigt werden könnte. 
> - Stellen Sie beim Löschen eines ACR von einem Volume sicher, dass der entsprechende Host nicht auf das Volume zugreift, da das Löschen zu einer Unterbrechung des Lese-/ Schreibzugriff führen kann.

## Abrufen des IQN

Führen Sie die folgenden Schritte aus, um den IQN eines Windows-Hosts abzurufen, auf dem Windows Server 2012 ausgeführt wird.

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## Hinzufügen eines ACR

ACRs werden auf der Seite **Konfiguration** des StorSimple Manager-Diensts hinzugefügt. In der Regel ordnen Sie einem Volume einen ACR zu.

Informationen zum Zuordnen eines ACR zu einem Volume finden Sie unter [Verwalten von Volumes in einem StorSimple Virtual Array mithilfe des StorSimple Manager-Diensts](storsimple-ova-manage-volumes).

>[AZURE.IMPORTANT] 
> 
>Achten Sie beim Zuweisen eines ACR zu einem Volume darauf, dass dabei nicht gleichzeitig von mehreren nicht gruppierten Hosts auf das Volume zugegriffen wird, da dadurch das Volume beschädigt werden könnte.
 
Führen Sie die folgenden Schritte aus, um einen ACR hinzuzufügen:

#### So fügen Sie einen ACR hinzu

1. Wählen Sie auf der Startseite für Dienste Ihren Dienst aus, doppelklicken Sie auf den Namen, und klicken Sie dann auf die Registerkarte **Konfiguration**.

    ![Registerkarte „Konfiguration“](./media/storsimple-ova-manage-acrs/acr1.png)

2. Geben Sie in der Tabelle unter **Zugriffssteuerungsdatensätze** einen **Namen** für Ihren ACR ein.

3. Geben Sie unter **iSCSI-Initiatorname** den IQN des Windows-Hosts an.

4. Klicken Sie unten auf der Seite auf **Speichern**, um den neu erstellten ACR zu speichern. Die folgende Bestätigungsmeldung wird angezeigt.

    ![Bestätigungsmeldung](./media/storsimple-ova-manage-acrs/acr2.png)

5. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-manage-acrs/check-icon.png). Die tabellarische Auflistung wird mit dem hinzugefügten Eintrag aktualisiert.

## Bearbeiten eines ACR

Navigieren Sie im klassischen Azure-Portal zur Seite **Konfiguration**, um ACRs zu bearbeiten.

> [AZURE.NOTE] Sie sollten nur die ACRs ändern, die derzeit nicht verwendet werden. Zum Bearbeiten eines ACR, der einem momentan verwendeten Volume zugeordnet ist, sollten Sie zunächst das Volume offline schalten.

Führen Sie die folgenden Schritte aus, um einen ACR zu bearbeiten:

#### So bearbeiten Sie einen ACR

1. Wählen Sie auf der Startseite für Dienste Ihren Dienst aus, doppelklicken Sie auf den Namen, und klicken Sie dann auf die Registerkarte **Konfiguration**.

2. Zeigen Sie in der tabellarischen Auflistung der Zugriffssteuerungsdatensätze auf den zu ändernden ACR.

3. Geben Sie einen neuen Namen und/oder IQN für den ACR an.

4. Klicken Sie unten auf der Seite auf **Speichern**, um den geänderten ACR zu speichern. Es wird eine Bestätigungsmeldung angezeigt.

5. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-manage-acrs/check-icon.png). Die tabellarische Auflistung wird den Änderungen entsprechend aktualisiert.

## Löschen von Zugriffssteuerungsdatensätzen

Navigieren Sie im klassischen Azure-Portal zur Seite **Konfiguration**, um ACRs zu löschen.

> [AZURE.NOTE] 
> 
> - Sie sollten nur die ACRs löschen, die derzeit nicht verwendet werden. Zum Löschen eines ACR, der einem momentan verwendeten Volume zugeordnet ist, sollten Sie zunächst das Volume offline schalten.
> - Stellen Sie beim Löschen eines ACR von einem Volume sicher, dass der entsprechende Host nicht auf das Volume zugreift, da das Löschen zu einer Unterbrechung des Lese-/ Schreibzugriff führen kann.

Führen Sie die folgenden Schritte aus, um einen Zugriffssteuerungsdatensatz zu löschen.

#### So löschen Sie einen Zugriffssteuerungsdatensatz

1. Wählen Sie auf der Startseite für Dienste Ihren Dienst aus, doppelklicken Sie auf den Namen, und klicken Sie dann auf die Registerkarte **Konfiguration**.

2. Zeigen Sie in der tabellarischen Auflistung der Zugriffssteuerungsdatensätze (ACRs) auf den zu löschenden ACR.

3. Ein Löschsymbol (**x**) wird in der äußersten rechten Spalte für den ausgewählten ACR angezeigt. Klicken Sie auf das Symbol **x**, um den ACR zu löschen. Die folgende Bestätigungsmeldung wird angezeigt.

    ![Bestätigungsmeldung](./media/storsimple-ova-manage-acrs/acr3.png)

5. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-manage-acrs/check-icon.png). Die tabellarische Auflistung wird mit dem gelöschten Eintrag aktualisiert.

## Nächste Schritte

- Lesen Sie sich die weiteren Informationen zum [Hinzufügen von Volumes und Konfigurieren von ACRs](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) durch.

<!---HONumber=AcomDC_0218_2016-->