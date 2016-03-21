<properties 
   pageTitle="Ändern des Administratorkennworts für das virtuelle StorSimple-Gerät | Microsoft Azure"
   description="Hier wird beschrieben, wie Sie das klassische Azure-Portal oder die StorSimple Virtual Array-Webbenutzeroberfläche verwenden, um das Geräteadministratorkennwort zu ändern."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="03/02/2016"
   ms.author="alkohli" />

# Ändern des StorSimple Virtual Array-Geräteadministratorkennworts

## Übersicht

Wenn Sie über die Windows PowerShell-Benutzeroberfläche auf das virtuelle StorSimple-Gerät zugreifen, müssen Sie ein Geräteadministratorkennwort eingeben. Wenn das StorSimple-Gerät zum ersten Mal bereitgestellt und gestartet wird, lautet das Standardkennwort *Password1*. Um Ihre Daten zu schützen, läuft das Standardkennwort bei der ersten Anmeldung ab, und Sie müssen dieses Kennwort ändern.

Sie können jederzeit entweder die lokale Webbenutzeroberfläche oder das klassische Azure-Portal nutzen, um das Geräteadministratorkennwort nach der Bereitstellung des Geräts in Ihrer Produktionsumgebung zu ändern. Die einzelnen Schritte werden in diesem Artikel beschrieben.

## Ändern des Kennworts im klassischen Azure-Portal

Führen Sie die folgenden Schritte aus, um das Geräteadministratorkennwort über das klassische Azure-Portal zu ändern.

#### So ändern Sie das Geräteadministratorkennwort über das klassische Azure-Portal

1. Klicken Sie im Portal für Ihr Gerät auf **Geräte** > **Konfiguration**.

2. Scrollen Sie bis zum Abschnitt **Geräteadministratorkennwort** nach unten. Geben Sie ein Administratorkennwort ein, das zwischen 8 und 15 Zeichen lang ist. Beim Kennwort muss es sich um eine Kombination aus Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen handeln.

3. Bestätigen Sie das Kennwort.

4. Klicken Sie unten auf der Seite auf **Speichern**.

Das Geräteadministratorkennwort wurde jetzt aktualisiert. Sie können dieses geänderte Kennwort verwenden, um lokal auf das Gerät zuzugreifen.

## Ändern des Kennworts über die StorSimple Virtual Array-Webbenutzeroberfläche

Führen Sie die folgenden Schritte aus, um das Geräteadministratorkennwort über die lokale Webbenutzeroberfläche zu ändern.

#### So ändern Sie das Geräteadministratorkennwort über die lokale Webbenutzeroberfläche

1. Klicken Sie auf der lokalen Webbenutzeroberfläche für Ihr Gerät auf **Wartung** > **Kennwortänderung**.

    ![password1 ändern](./media/storsimple-ova-change-device-admin-password/image40.png)

2. Geben Sie das **aktuelle Kennwort** ein.

3. Geben Sie ein **neues Kennwort** ein. Das Kennwort muss mindestens 8 Zeichen umfassen. Das Kennwort muss drei der vier folgenden Elemente enthalten: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen.

    Hinweis: Ihr Kennwort muss sich von den letzten 24 Kennwörtern unterscheiden.

3. Geben Sie das Kennwort erneut ein, um es zu bestätigen.

    ![password2 ändern](./media/storsimple-ova-change-device-admin-password/image41.png)

4. Klicken Sie unten auf der Seite auf **Übernehmen**. Das neue Kennwort wird übernommen. War die Änderung des Kennworts nicht erfolgreich, wird der folgende Fehler angezeigt:

    ![Kennwortfehler](./media/storsimple-ova-change-device-admin-password/image42.png)

    Wurde das Kennwort aktualisiert, werden Sie benachrichtigt. Sie können dieses geänderte Kennwort anschließend verwenden, um lokal auf das Gerät zuzugreifen.

## Nächste Schritte

Erfahren Sie, wie Sie [StorSimple Virtual Array verwalten](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0309_2016-->