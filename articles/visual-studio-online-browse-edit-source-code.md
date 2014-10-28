<properties urlDisplayName="Browse and Edit Your Source Code" pageTitle="Browse and Edit Your Source Code | Azure" metaKeywords="Visual Studio Online, VSO, git, tfvc, edit, code, commit" description="Learn how to edit your source code." metaCanonical="" services="visual-studio-online" documentationCenter="" title="Browse and Edit Your Source Code" authors="ehollow" solutions="" manager="" editor="" />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ehollow"></tags>

# Durchsuchen und bearbeiten Sie Ihren Quellcode

Im Microsoft Azure-Vorschauportal ist es ganz einfach, Ihren Visual Studio Online Teamprojekt-Quellcode zu durchsuchen und zu bearbeiten.

Sobald Sie ein Teamprojekt im Portal erstellt haben und den Code entweder eingespielt (Team Foundation Version Control) oder committed haben (Git), können Sie den Code über das Portal einfach bearbeiten und haben leicht Zugriff.

1.  Öffnen Sie zuerst Ihr Teamprojekt-Fenster und finden Sie dann Code Lens. Klicken Sie dann im Quellcode-Teil auf den Namen des Repositorys, das Sie durchsuchen möchten (hier ‚BrowseCode‘). Hinweis: Wenn Sie mehr als zwei Repositorys haben, können Sie auf die Repositorynummer klicken (hier 1), um die vollständige Liste zu sehen.
    ![Code Lens][Code Lens]
2.  Jetzt, da Ihr Repositoryfenster geöffnet ist, können Sie auf den Code klicken, um damit anzufangen, Ihren Quellcode zu durchsuchen.
    ![Repositoryfenster][Repositoryfenster]
3.  Ein Fenster mit standardmäßiger Baumstrukturansicht erscheint, in der Sie eine Übersicht über Ihr komplettes Projekt erhalten. Sie können auf die Knoten klicken, um einen Ordner zu öffnen, oder auf eine bestimmte Datei klicken (hier Index.cshtml) um die Datei anzusehen oder zu bearbeiten.
    ![Baumstruktur][Baumstruktur]
4.  Um eine der Dateien zu bearbeiten, klicken Sie einfach auf die Schaltfläche Bearbeiten in der Befehlsleiste. Die Inhalte der Datei befinden sich dann im Bearbeitungsmodus, und Sie können Änderungen vornehmen. Tipp: Klicken Sie zur Bearbeitung im Vollbildmodus oben rechts auf die Schaltfläche Maximieren.
    ![Bearbeitungsmodus][Bearbeitungsmodus]
5.  Wenn Sie alle Änderungen an der Datei vorgenommen haben, klicken Sie in der Befehlsleiste auf die Schaltfläche Commit (oder Check-In). Dadurch wird ein neues Fenster geöffnet, indem Sie eine Nachricht eingeben können. Klicken Sie auf OK, wenn Sie fertig sind, und die Datei wird in das Repository übernommen.
    ![Commit Code][Commit Code]
6.  Am oberen Ende des Fensters erscheint eine blinkende Nachricht, die das Übernehmen in das Repository bestätigt.
    ![Commit Erfolgreich][Commit Erfolgreich]

  [Code Lens]: ./media/visual-studio-online-browse-edit-source-code/Code-Lens.png
  [Repositoryfenster]: ./media/visual-studio-online-browse-edit-source-code/Repo-Blade.png
  [Baumstruktur]: ./media/visual-studio-online-browse-edit-source-code/Tree-Nav.png
  [Bearbeitungsmodus]: ./media/visual-studio-online-browse-edit-source-code/Edit-Mode.png
  [Commit Code]: ./media/visual-studio-online-browse-edit-source-code/Commit-Code.png
  [Commit Erfolgreich]: ./media/visual-studio-online-browse-edit-source-code/Commit-Success.png
