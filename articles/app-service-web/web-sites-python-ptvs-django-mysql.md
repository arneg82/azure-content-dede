<properties 
	pageTitle="Django und MySQL in Azure mit Python Tools 2.2 für Visual Studio" 
	description="Erfahren Sie, wie Sie mithilfe der Python-Tools für Visual Studio eine Django-Web-App erstellen, die Daten in einer MySQL-Datenbankinstanz speichert, und die Web-App für Azure App Service-Web-Apps bereitstellen." 
	services="app-service\web" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python"
	ms.topic="get-started-article" 
	ms.date="07/07/2016"
	ms.author="huvalo"/>

# Django und MySQL in Azure mit Python Tools 2.2 für Visual Studio 

[AZURE.INCLUDE [Registerkarten](../../includes/app-service-web-get-started-nav-tabs.md)]

In diesem Tutorial erstellen Sie mit [Python-Tools für Visual Studio](PTVS) und unter Verwendung einer der PTVS-Beispielvorlagen eine einfache Web-App für Umfragen. Sie erfahren, wie Sie einen in Azure gehosteten MySQL-Dienst verwenden, wie Sie die Web-App für die Nutzung von MySQL konfigurieren und wie Sie sie für [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) veröffentlichen.

> [AZURE.NOTE] Die Informationen in diesem Tutorial stehen auch in folgendem Video zur Verfügung:
> 
> [PTVS 2.1: Django app with MySQL][video] \(PTVS 2.1: Django-App mit MySQL)

Weitere Artikel finden Sie im [Python Developer Center]. Diese Artikel behandeln die Entwicklung von Azure App Service-Web-Apps mit PTVS unter Verwendung der Webframeworks Bottle, Flask und Django sowie mithilfe der Dienste Azure Table Storage, MySQL und SQL-Datenbank. Der Schwerpunkt dieses Artikels liegt zwar auf App Service, die Schritte sind jedoch vergleichbar mit der Entwicklung von [Azure Cloud Services].

## Voraussetzungen

 - Visual Studio 2015
 - [Python 2.7 32-Bit] oder [Python 3.4 32-Bit]
 - [Python Tools 2.2 für Visual Studio]
 - [Python Tools 2.2 für Visual Studio, Beispiel-VSIX]
 - [Azure SDK-Tools für Visual Studio 2015]
 - Django 1.9 oder höher

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen des Projekts

In diesem Abschnitt erstellen Sie ein Visual Studio-Projekt unter Verwendung einer Beispielvorlage. Sie erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete. Sie erstellen eine lokale Datenbank mithilfe von SQLite. Anschließend führen Sie die Anwendung lokal aus.

1. Wählen Sie in Visual Studio **Datei** -> **Neues Projekt** aus.

1. Die Projektvorlagen aus den [Python Tools 2.2 für Visual Studio, Beispiel-VSIX] sind unter **Python** > **Beispiele** verfügbar. Wählen Sie **Polls Django Web Project** aus, und klicken Sie auf "OK", um das Projekt zu erstellen.

    ![Dialogfeld "Neues Projekt"](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1. Sie werden aufgefordert, externe Pakete zu installieren. Wählen Sie **In einer virtuellen Umgebung installieren** aus.

    ![Dialogfeld für externe Pakete](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1. Wählen Sie **Python 2.7** oder **Python 3.4** als Basisinterpreter.

    ![Dialogfeld für das Hinzufügen der virtuellen Umgebung](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Python** und anschließend **Django Migrate**. Wählen Sie **Django Create Superuser**.

1. Dadurch wird eine Django-Verwaltungskonsole geöffnet und im Projektordner eine SQLite-Datenbank erstellt. Folgen Sie den Anweisungen zum Erstellen eines Benutzers.

1. Drücken Sie `F5`, um sicherzustellen, dass die Anwendung funktioniert.

1. Klicken Sie oben auf der Navigationsleiste auf **Anmelden**.

    ![Django-Navigationsleiste](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1. Geben Sie die Anmeldeinformationen für den Benutzer ein, den Sie beim Synchronisieren der Datenbank erstellt haben.

    ![Anmeldeformular](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1. Klicken Sie auf **Beispielumfrage erstellen**.

    ![Erstellen von Beispielumfragen](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1. Klicken Sie auf eine Umfrage und stimmen Sie ab.

    ![Abstimmen in Beispielumfragen](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## Erstellen einer MySQL-Datenbank

Als Datenbank erstellen Sie eine gehostete ClearDB MySQL-Datenbank in Azure.

Alternativ können Sie einen eigenen, auf Azure ausgeführten virtuellen Computer erstellen und dann MySQL selbst installieren und verwalten.

Mit den folgenden Schritten können Sie eine Datenbank im Rahmen eines kostenlosen Plans erstellen.

1. Melden Sie sich beim [Azure-Portal] an.

1. Klicken Sie oben im Navigationsbereich auf **Neu**, klicken Sie auf **Daten und Speicher**, und klicken Sie anschließend auf **MySQL-Datenbank**.

1. Konfigurieren Sie die neue MySQL-Datenbank, indem Sie eine neue Ressourcengruppe erstellen, und wählen Sie den entsprechenden Speicherort aus.

1. Klicken Sie nach der Erstellung der MySQL-Datenbank auf dem Datenbankblatt auf **Eigenschaften**.

1. Mithilfe der Schaltfläche "Kopieren" können Sie den Wert für **VERBINDUNGSZEICHENFOLGE** in die Zwischenablage kopieren.

## Konfigurieren des Projekts

In diesem Abschnitt konfigurieren Sie die Web-App, sodass sie die soeben erstellte MySQL-Datenbank verwendet. Außerdem installieren Sie weitere Python-Pakete, die für die Verwendung von MySQL-Datenbanken mit Django erforderlich sind. Anschließend führen Sie die Web-App lokal aus.

1. Öffnen Sie in Visual Studio die Datei **settings.py** (im Ordner *<Projektname>*). Fügen Sie die Verbindungszeichenfolge temporär in den Editor ein. Die Verbindungszeichenfolge ist in folgendem Format:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Ändern Sie die Standarddatenbank **ENGINE** für die Verwendung von MySQL, und legen Sie die Werte für **NAME**, **BENUTZER**, **KENNWORT** und **HOST** gemäß der **VERBINDUNGSZEICHENFOLGE** fest.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1. Klicken Sie im Projektmappen-Explorer unter **Python-Umgebungen** mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **Python-Paket installieren** aus.

1. Installieren Sie das Paket `mysqlclient` mithilfe von **pip**.

    ![Dialogfeld "Paket installieren"](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Python** und anschließend **Django Migrate**. Wählen Sie **Django Create Superuser**.

    Dadurch werden die Tabellen für die MySQL-Datenbank erstellt, die Sie im vorherigen Abschnitt erstellt haben. Folgen Sie den Aufforderungen zum Erstellen eines Benutzers. Dieser muss nicht dem Benutzer in der SQLite-Datenbank entsprechen, die im ersten Abschnitt dieses Artikels erstellt wurde.

1. Drücken Sie `F5`, um die Anwendung auszuführen. Die mittels **Beispielumfrage erstellen** erstellten Umfragen sowie die bei der Abstimmung erfassten Daten werden in der MySQL-Datenbank serialisiert.

## Veröffentlichen der Web-App in Azure App Service

Das Azure .NET SDK bietet eine einfache Möglichkeit zum Bereitstellen Ihrer Web-App in Azure App Service.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen** aus.

    ![Dialogfeld "Web veröffentlichen"](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1. Klicken Sie auf **Microsoft Azure App Service**.

1. Klicken Sie auf **Neu**, um eine neue Web-App zu erstellen.

1. Füllen Sie die folgenden Felder aus, und klicken Sie auf **Erstellen**:
	- **Name der Web-App**
	- **App Service-Plan**
	- **Ressourcengruppe**
	- **Region**
	- Lassen Sie für **Datenbankserver** die Option **Keine Datenbank** festlegt.

1. Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1. Ihr Webbrowser öffnet automatisch mit der veröffentlichten Web-App. Die Web-App funktioniert nun wie erwartet und verwendet die von Azure gehostete **MySQL**-Datenbank.

    ![Webbrowser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

    Glückwunsch! Sie haben Ihre MySQL-basierte Web-App erfolgreich in Azure veröffentlicht.

## Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über Python Tools für Visual Studio, Django und MySQL erfahren möchten.

- [Python Tools für Visual Studio – Dokumentation]
  - [Webprojekte]
  - [Cloud Service-Projekte]
  - [Remotedebugging in Microsoft Azure]
- [Dokumentation zu Django]
- [MySQL]

Weitere Informationen finden Sie im [Python Developer Center](/develop/python/).

<!--Link references-->

[Python Developer Center]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md

<!--External Link references-->

[Azure-Portal]: https://portal.azure.com
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 für Visual Studio, Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 für Visual Studio, Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK-Tools für Visual Studio 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools für Visual Studio – Dokumentation]: http://aka.ms/ptvsdocs
[Remotedebugging in Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Webprojekte]: http://go.microsoft.com/fwlink/?LinkId=624027
[Cloud Service-Projekte]: http://go.microsoft.com/fwlink/?LinkId=624028
[Dokumentation zu Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[video]: http://youtu.be/oKCApIrS0Lo

<!---HONumber=AcomDC_0713_2016-->