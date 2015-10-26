<properties 
   pageTitle="Ersetzen des Gehäuses eines StorSimple-Geräts | Microsoft Azure"
   description="Beschreibt das Entfernen und Ersetzen des Gehäuses in Ihrem primären StorSimple-Gerät oder EBOD-Gehäuse."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/31/2015"
   ms.author="alkohli" />

# Ersetzen des Gehäuses des StorSimple-Geräts

## Übersicht

In diesem Tutorial wird erläutert, wie Sie das Gehäuse eines StorSimple-Geräts austauschen oder entfernen. Das StorSimple-Modell 8100 ist ein Gerät mit einem einzelnen Gehäuse, das Modell 8600 ein Gerät mit zwei Gehäusen. Beim Modell 8600 können potenziell zwei Gehäuse im Gerät ausfallen: das primäre Gehäuse oder das EBOD-Gehäuse.

In beiden Fällen wird ein leeres Ersatzgehäuse von Microsoft ausgeliefert. Die Gehäuse enthalten keine Stromversorgungs- und Kühleinheiten (PCMs), Controllermodule, SSDs (Solid-State-Festplatten), HDDs (Festplattenlaufwerke) oder EBOD-Module.

>[AZURE.IMPORTANT]Vor dem Entfernen und Austauschen des Gehäuses überprüfen Sie die Sicherheitsinformationen unter [Austauschen von StorSimple-Hardwarekomponenten](storsimple-hardware-component-replacement.md).

## Entfernen des Gehäuses

Führen Sie die folgenden Schritte aus, um das Gehäuse des StorSimple-Geräts zu entfernen.

#### So entfernen Sie ein Gehäuse

1. Stellen Sie sicher, dass das StorSimple-Gerät ausgeschaltet und von der Stromversorgung getrennt ist.

2. Entfernen Sie ggf. alle Netzwerk- und SAS-Kabel.

3. Entfernen Sie die Einheit aus dem Rack.

4. Entfernen Sie alle Laufwerke, und notieren Sie sich die Steckplätze, aus denen sie entfernt werden. Weitere Informationen finden Sie unter [Entfernen der Festplatte](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. Entfernen Sie die EBOD-Controllermodule aus dem EBOD-Gehäuse (falls dies das fehlerhafte Gehäuse ist). Weitere Informationen finden Sie unter [Entfernen eines EBOD-Controllers](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller).

    Entfernen Sie die Controller aus dem primären Gehäuse (falls dies das fehlerhafte Gehäuse ist), und notieren Sie sich die Steckplätze, aus denen sie entfernt werden. Weitere Informationen finden Sie unter [Entfernen eines Controllers](storsimple-controller-replacement.md#remove-a-controller).

## Installieren des Gehäuses

Führen Sie die folgenden Schritte aus, um das Gehäuse in einem Microsoft Azure StorSimple-Gerät zu installieren.

#### So installieren Sie ein Gehäuse

1. Installieren Sie das Gehäuse im Rack. Weitere Informationen finden Sie unter [Einbauen des StorSimple 8100-Geräts in ein Rack](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) oder [Einbauen des StorSimple 8600-Geräts in ein Rack](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Nachdem Sie das Gehäuse in das Rack eingebaut haben, installieren Sie die Controllermodule an den gleichen Positionen, an denen sie sich zuvor befanden.

3. Installieren Sie die Laufwerke an den gleichen Positionen und Steckplätzen, an denen sie zuvor installiert waren.

    >[AZURE.NOTE]Im Allgemeinen empfehlen wir, dass Sie zuerst die SSDs installieren und dann die Festplatten.

2. Nach dem Einbau des Geräts und der Installation der Komponenten verbinden Sie das Gerät mit den entsprechenden Stromquellen, und schalten Sie es ein. Weitere Informationen finden Sie unter [Verkabeln des StorSimple 8100-Geräts](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) bzw. [Verkabeln des StorSimple 8600-Geräts](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## Nächste Schritte

Weitere Informationen zum [Austauschen von StorSimple-Hardwarekomponenten](storsimple-hardware-component-replacement.md).

<!---HONumber=Oct15_HO3-->