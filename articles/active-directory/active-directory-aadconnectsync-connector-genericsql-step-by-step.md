<properties
   pageTitle="Azure AD Connect-Synchronisierung: Schrittweise Anleitung für den generischen SQL-Connector | Microsoft Azure"
   description="In diesem Artikel finden Sie eine schrittweise Anleitung für die Verwendung eines einfachen HR-Systems mit dem generischen SQL-Connector."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/08/2016"
   ms.author="andkjell"/>

# Schrittweise Anleitung für den generischen SQL-Connector
Dieses Thema ist eine schrittweise Anleitung. Damit erstellen Sie eine einfache HR-Beispieldatenbank und verwenden sie, um einige Benutzer und deren Gruppenmitgliedschaft zu importieren.

## Vorbereiten der Beispieldatenbank
Führen Sie auf einem Server mit SQL Server das SQL-Skript aus [Anhang A](#appendix-a) aus. Dadurch wird eine Beispieldatenbank mit dem Namen GSQLDEMO erstellt. Das Objektmodell für die erstellte Datenbank sieht folgendermaßen aus:

![Objektmodell](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

Außerdem erstellen Sie einen Benutzer, den Sie für die Verbindung mit der Datenbank verwenden möchten. In dieser exemplarischen Vorgehensweise heißt der Benutzer FABRIKAM\\SQLUser und befindet sich in der Domäne.

## Erstellen der ODBC-Verbindungsdatei
Der generische SQL-Connector verwendet ODBC für die Verbindung mit dem Remoteserver. Zunächst müssen Sie eine Datei mit den Informationen zur ODBC-Verbindung erstellen.

1. Starten Sie das ODBC-Verwaltungshilfsprogramm auf Ihrem Server: ![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. Wählen Sie die Registerkarte **Datei-DSN** aus. Klicken Sie auf **Hinzufügen**. ![ODBC 1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. Der Standardtreiber sollte gut funktionieren, wählen Sie ihn also aus, und klicken Sie auf **Weiter**. ![ODBC 2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. Geben Sie der Datei einen Namen, z. B. **GenericSQL**. ![ODBC 3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. Klicken Sie auf **Fertig stellen**. ![ODBC 4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. Konfigurieren Sie jetzt die Verbindung. Geben Sie eine gute Beschreibung der Datenquelle ein, und geben Sie den Namen des Servers mit SQL Server an. ![ODBC 5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. Wählen Sie die Art der Authentifizierung bei SQL aus. In diesem Fall wird die Windows-Authentifizierung verwendet. ![ODBC 6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. Geben Sie den Namen der Beispieldatenbank **GSQLDEMO** an. ![ODBC 7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. Behalten Sie die Standardeinstellungen auf diesem Bildschirm bei. Klicken Sie auf **Fertig stellen**. ![ODBC 8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. Um zu überprüfen, ob alles wie erwartet funktioniert, klicken Sie auf **Datenquelle testen**. ![ODBC 9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. Überprüfen Sie, ob der Test erfolgreich war. ![ODBC 10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. Die ODBC-Konfigurationsdatei sollte jetzt unter „Datei-DSN“ angezeigt werden. ![ODBC 11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

Jetzt verfügen Sie über die benötigte Datei und können mit dem Erstellen des Connectors beginnen.

## Erstellen des generischen SQL-Connectors

1. Wählen Sie auf der Synchronization Service Manager-Benutzeroberfläche **Connectors** und **Erstellen** aus. Wählen Sie **Generisch, SQL (Microsoft)** aus, und geben Sie dem Connector einen aussagekräftigen Namen. ![Connector 1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. Suchen Sie die DSN-Datei, die Sie im vorherigen Abschnitt erstellt haben, und laden Sie sie auf den Server hoch. Geben Sie die Anmeldeinformationen für die Verbindung mit der Datenbank an. ![Connector 2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. Der Einfachheit halber gehen wir in dieser exemplarischen Vorgehensweise davon aus, dass es zwei Objekttypen gibt: **User** und **Group**. ![Connector 3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. Der Connector soll die Attribute anhand der Tabelle selbst erkennen. Da **Users** ein reserviertes Wort in SQL ist, muss es in eckigen Klammern [] angegeben werden. ![Connector 4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. Jetzt werden das Ankerattribut und das DN-Attribut definiert. Für **Users** verwenden wir die Kombination der beiden Attribute „Username“ und „EmployeeID“. Für **Group** verwenden wir „GroupName“ (nicht sehr realistisch in der realen Welt, aber für diese exemplarische Vorgehensweise geeignet). ![Connector 5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. Nicht alle Attributtypen können in einer SQL-Datenbank erkannt werden. Dies gilt insbesondere für den Verweisattributtyp. Für den Objekttyp „Group“ müssen wir „OwnerID“ und „MemberID“ in „Verweis“ ändern. ![Connector 6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. Für die Attribute, die wir im vorherigen Schritt als Verweisattribute ausgewählt haben, muss jetzt der Objekttyp angegeben werden, für den sie einen Verweis darstellen. In unserem Fall ist dies der Objekttyp „User“. ![Connector 7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. Wählen Sie auf der Seite mit den globalen Parametern **Wasserzeichen** als Deltastrategie aus. Geben Sie das Format von Datum/Uhrzeit als **JJJJ-MM-TT HH:MM:SS** ein. ![Connector 8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. Wählen Sie auf der Seite **Partitionen konfigurieren** beide Objekttypen aus. ![Connector 9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. Wählen Sie unter **Objekttypen auswählen** und **Attribute auswählen** beide Objekttypen und alle Attribute aus. Klicken Sie auf der Seite **Anker konfigurieren** auf **Fertig stellen**.

## Erstellen von Ausführungsprofilen

1. Wählen Sie auf der Synchronization Service Manager-Benutzeroberfläche **Connectors** und **Ausführungsprofile konfigurieren** aus. Klicken Sie auf **Neues Profil**. Wir beginnen mit **Vollständiger Import**. ![Ausführungsprofil 1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. Wählen Sie den Typ **Vollständiger Import (nur Bereitstellung)** aus. ![Ausführungsprofil 2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. Wählen Sie die Partition **OBJEKT=User** aus. ![Ausführungsprofil 3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. Wählen Sie **Tabelle** aus, und geben Sie **[USERS]** ein. Führen Sie einen Bildlauf zum Abschnitt für den Objekttyp mit mehreren Werten aus, und geben Sie die Daten wie folgt ein. Wählen Sie **Fertig stellen** aus, um den Schritt zu speichern. ![Ausführungsprofil 4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png) ![Ausführungsprofil 4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)  
5. Wählen Sie **Neuer Schritt** aus. Wählen Sie dieses Mal **OBJEKT=Group** aus. Verwenden Sie auf der letzten Seite die folgende Konfiguration. Klicken Sie auf **Fertig stellen**. ![Ausführungsprofil 5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png) ![Ausführungsprofil 5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)  
6. Optional: Sie können bei Bedarf zusätzliche Ausführungsprofile konfigurieren. In dieser exemplarischen Vorgehensweise wird nur der vollständige Import verwendet.
7. Klicken Sie auf **OK**, um das Ändern von Profilen abzuschließen.

## Hinzufügen einiger Testdaten und Testen des Imports

Geben Sie einige Testdaten in die Beispieldatenbank ein. Wenn Sie bereit sind, wählen Sie **Ausführen** und **Vollständiger Import** aus.

Hier sehen Sie einen Benutzer mit zwei Telefonnummern und eine Gruppe mit einigen Mitgliedern. ![CS 1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png) ![CS 2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)

## Anhang A
**SQL-Skript zum Erstellen der Beispieldatenbank**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
	[MemberID] [int] NOT NULL,
	[Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
	[GroupID] [int] NOT NULL,
	[GROUPNAME] [nvarchar](200) NOT NULL,
	[DESCRIPTION] [nvarchar](200) NULL,
	[WATERMARK] [datetime] NULL,
	[OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
	[GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
	[USER_ID] [int] NULL,
	[Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
	[USERID] [int] NOT NULL,
	[USERNAME] [nvarchar](200) NOT NULL,
	[FirstName] [nvarchar](100) NULL,
	[LastName] [nvarchar](100) NULL,
	[DisplayName] [nvarchar](100) NULL,
	[ACCOUNTDISABLED] [bit] NULL,
	[EMPLOYEEID] [int] NOT NULL,
	[WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
	[USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```

<!---HONumber=AcomDC_0309_2016-->