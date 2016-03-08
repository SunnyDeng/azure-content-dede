<properties 
	pageTitle="Bekannte Netzwerke | Microsoft Azure" 
	description="Durch das Konfigurieren bekannter Netzwerkwerke können Sie vermeiden, dass IP-Adressen Ihrer Organisation in die Berichte „Anmeldungen aus mehreren geografischen Regionen“ und „Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten“ aufgenommen werden." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="stevenpo"  
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="markvi"/>

# Bekannte Netzwerke


Sie können die Zugriffs- und Nutzungsberichte von Azure Active Directory verwenden, um sich einen Einblick in die Integrität und Sicherheit des Verzeichnisses Ihrer Organisation zu verschaffen. Mithilfe dieser Informationen kann ein Verzeichnisadministrator mögliche Sicherheitsrisiken besser bestimmen, um angemessen zu planen, wie diese Risiken eingedämmt werden können.

Möglicherweise werden in den Berichten „*Anmeldungen aus mehreren geografischen Regionen*“ und „*Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten*“ fälschlicherweise IP-Adressen Ihrer Organisation gekennzeichnet.

Dies kann in folgenden Fällen geschehen:

- Ein Benutzer in der Niederlassung in Boston hat sich per Remoteverbindung beim Rechenzentrum San Francisco angemeldet. Dadurch wird der Bericht „Anmeldungen aus mehreren geografischen Regionen“ ausgelöst. 

- Ein Benutzer Ihrer Organisation versucht mehrere Male, sich mit einem falschen Kennwort anzumelden. Dadurch wird der Bericht „Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten“ ausgelöst.

Damit in diesen Fällen keine irreführenden Sicherheitsberichte generiert werden, wird empfohlen, der Liste der öffentlichen IP-Adressen Ihres Unternehmens bekannte IP-Adressbereiche hinzuzufügen.


###Führen Sie zum Hinzufügen der öffentlichen IP-Adressbereiche Ihrer Organisation die folgenden Schritte aus: 

1.	Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.

2.	Klicken Sie im linken Bereich auf **Active Directory**. <br><br>![Funktionsweise von Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-01.png)

3.	Wählen Sie auf der Registerkarte **Verzeichnis** Ihr Verzeichnis aus.

4.	Klicken Sie oben im Menü auf **Konfigurieren**. <br><br>![Funktionsweise von Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-02.png)

5.	Wechseln Sie auf der Registerkarte „Konfigurieren“ zu den **öffentlichen IP-Adressbereichen Ihrer Organisation**. <br><br>![Funktionsweise von Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-03.png)

6.	Klicken Sie auf **Bekannte IP-Adressbereiche hinzufügen**.

7.	Fügen Sie die Adressbereiche im angezeigten Dialogfeld hinzu, und klicken Sie anschließend auf das Häkchen. <br><br>![Funktionsweise von Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-04.png)


**Weitere Ressourcen**


* [Anzeigen Ihrer Zugriffs- und Nutzungsberichte](active-directory-view-access-usage-reports.md)
* [Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Anmeldungen aus mehreren geografischen Regionen](active-directory-reporting-sign-ins-from-multiple-geographies.md)

<!---HONumber=AcomDC_0224_2016-->