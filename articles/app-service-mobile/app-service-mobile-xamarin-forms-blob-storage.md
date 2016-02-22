<properties
    pageTitle="Herstellen einer Verbindung mit Azure Storage in der Xamarin.Forms-App"
    description="Hinzufügen von Bildern zur mobilen Xamarin.Forms-Aufgabenlisten-App per Verbindung mit dem Azure-Blobspeicher"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="02/03/2016"
    ms.author="donnam"/>

#Herstellen einer Verbindung mit Azure Storage in der Xamarin.Forms-App

## Übersicht

Das Azure Mobile Apps-Client- und -Server-SDK unterstützt die Offlinesynchronisierung von strukturierten Daten mit CRUD-Vorgängen für den Endpunkt „/tables“. Diese Daten werden in der Regel in einer Datenbank oder einem ähnlichen Speicher abgelegt, und häufig ist es nicht möglich, in diesen Datenspeichern umfangreiche Binärdaten effizient zu speichern. Einige Anwendungen verfügen auch über zugehörige Daten, die an einem anderen Ort gespeichert werden (z. B. Blobspeicher, Dateifreigaben). Es ist hilfreich, wenn Sie Zuordnungen zwischen Datensätzen im Endpunkt „/tables“ und anderen Daten erstellen können.

In diesem Thema wird gezeigt, wie Sie der Mobile Apps-Schnellstart-App „todo list“ (Aufgabenliste) Unterstützung für Bilder hinzufügen. Sie müssen zunächst das Tutorial [Erstellen einer Xamarin.Forms-App] abschließen.

In diesem Tutorial erstellen Sie ein Speicherkonto und fügen Ihrem Back-End für die mobile App eine Verbindungszeichenfolge hinzu. Anschließend fügen Sie eine neue Vererbung vom neuen Mobile Apps-Typ `StorageController<T>` zum Serverprojekt hinzu.

>[AZURE.TIP] Dieses Tutorial verfügt über ein [Begleitbeispiel](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/), das Sie für Ihr eigenes Azure-Konto bereitstellen können.

## Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] oder höher.

* Ein Mac, auf dem [Xcode](https://go.microsoft.com/fwLink/?LinkID=266532) Version 7.0 oder höher und [Xamarin Studio](http://xamarin.com/download) installiert ist.

* Abschluss des Tutorials [Erstellen einer Xamarin.Forms-App]. In diesem Artikel wird die fertige App aus diesem Tutorial verwendet.

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, besuchen Sie [Azure App Service-App erstellen](https://tryappservice.azure.com/?appServiceName=mobile). Dort können Sie direkt eine kurzzeitige mobile Start-App in App Service erstellen – keine Kreditkarte erforderlich, keine weiteren Verpflichtungen.

## Erstellen Sie ein Speicherkonto.

1. Erstellen Sie ein Speicherkonto, indem Sie die Tutorialschritte unter [Erstellen eines Azure-Speicherkontos] ausführen. 

2. Navigieren Sie im Azure-Portal zum neu erstellten Speicherkonto, und klicken Sie auf das Symbol **Schlüssel**. Kopieren Sie die **primäre Verbindungszeichenfolge**.

3. Navigieren Sie zum Back-End der mobilen App. Erstellen Sie unter **Alle Einstellungen** -> **Anwendungseinstellungen** -> **Verbindungszeichenfolgen** einen neuen Schlüssel mit dem Namen `MS_AzureStorageAccountConnectionString`, und verwenden Sie den aus Ihrem Speicherkonto kopierten Wert. Wählen Sie den Schlüsseltyp **Benutzerdefiniert**.

## Hinzufügen eines Speichercontrollers zum Serverprojekt

1. Öffnen Sie Ihr .NET-Serverprojekt in Visual Studio. Fügen Sie das NuGet-Paket [Microsoft.Azure.Mobile.Server.Files] hinzu. Wählen Sie **Vorabversion einbeziehen**.

2. Öffnen Sie Ihr .NET-Serverprojekt in Visual Studio. Klicken Sie mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie dann **Hinzufügen** -> **Controller** -> **Web API-2 Controller – Leer**. Geben Sie dem Controller den Namen `TodoItemStorageController`.

3. Fügen Sie die folgenden using-Anweisungen hinzu:

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. Ändern Sie die Basisklasse in `StorageController`:
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. Fügen Sie der Klasse die folgenden Methoden hinzu:

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. Aktualisieren Sie die Web-API-Konfiguration, um Attribut-Routing einzurichten. Fügen Sie in **Startup.MobileApp.cs** die folgende Zeile zur `ConfigureMobileApp()`-Methode nach der Definition der `config`-Variablen hinzu:

        config.MapHttpAttributeRoutes();

7. Veröffentlichen Sie Ihr Serverprojekt auf dem Back-End für die mobile App.

###Vom Speichercontroller registrierte Routen

Das neue `TodoItemStorageController`-Element macht zwei Unterressourcen unter dem verwalteten Datensatz verfügbar:

- StorageToken

    + HTTP POST: Erstellt ein Speichertoken.
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles

    + HTTP GET: Ruft eine Liste mit Dateien ab, die dem Datensatz zugeordnet sind.
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + HTTP DELETE: Löscht die Datei, die im Dateiressourcenbezeichner angegeben ist.
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###Kommunikation zwischen Client und Server

Beachten Sie, dass `TodoItemStorageController` *nicht* über eine Route zum Hoch- oder Herunterladen eines Blobs verfügt. Dies liegt daran, dass ein mobiler Client *direkt* mit dem Blobspeicher interagiert, um diese Vorgänge durchzuführen. Vorher wird ein SAS-Token (Shared Access Signature) abgerufen, um sicher auf ein Blob oder einen Container zugreifen zu können. Dies ist ein wichtiger Architekturentwurf, da der Zugriff auf den Speicher andernfalls durch die Skalierbarkeit und Verfügbarkeit des mobilen Back-Ends eingeschränkt wäre. Indem Sie stattdessen eine direkte Verbindung mit Azure Storage herstellen, kann der mobile Client die darüber bereitgestellten Features nutzen, z. B. automatische Partitionierung und geografische Verteilung.

Shared Access Signatures bieten delegierten Zugriff auf Ressourcen in Ihren Speicherkonto. Sie haben die Möglichkeit, einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Objekte in Ihrem Speicherkonto zu erteilen, ohne Ihre Konto-Zugriffsschlüssel weitergeben zu müssen. Weitere Informationen finden Sie unter [Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell].

Im folgenden Diagramm sind die Interaktionen zwischen Client und Server dargestellt. Vor dem Hochladen einer Datei fordert der Client ein SAS-Token vom Dienst an. Der Dienst verwendet die Verbindungszeichenfolge des Speichers zum Generieren einer neuen SAS, die er dann an den Client zurückgibt. Die SAS ist zeitlich begrenzt und beschränkt Berechtigungen auf eine bestimmte Datei oder einen Container. Der mobile Client verwendet dann diese SAS und das Azure Storage-Client-SDK, um die Datei in den Blobspeicher hochzuladen.

![Anfordern eines SAS-Tokens](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## Aktualisieren der Client-App zum Hinzufügen der Unterstützung für Bilder

Öffnen Sie das Xamarin.Forms-Schnellstartprojekt entweder in Visual Studio oder Xamarin Studio.

>[AZURE.NOTE] Dieses Tutorial enthält nur eine Anleitung für die Plattformen Android, iOS und Windows Store, nicht für Windows Phone.

###Hinzufügen von NuGet-Paketen

Klicken Sie mit der rechten Maustaste auf die Projektmappe, und wählen Sie **NuGet-Pakete für Projektmappe verwalten**. Fügen Sie **allen** Projekten der Projektmappe die unten angegebenen NuGet-Pakete hinzu. Vergessen Sie nicht, die Option **Vorabversion einbeziehen** zu aktivieren.

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

Der Einfachheit halber wird in diesem Beispiel die Bibliothek [PCLStorage] verwendet, aber sie ist für das Azure Mobile Apps-Client-SDK nicht zwingend erforderlich.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###Hinzufügen der IPlatform-Schnittstelle

Erstellen Sie eine neue `IPlatform`-Schnittstelle im Hauptprojekt mit der portablen Bibliothek. Dies basiert auf dem Muster [Xamarin.Forms DependencyService] zum Laden der richtigen plattformspezifischen Klasse zur Laufzeit. Später fügen Sie allen Clientprojekten plattformspezifische Implementierungen hinzu.

1. Fügen Sie die folgenden using-Anweisungen hinzu:

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. Ersetzen Sie die Implementierung durch Folgendes:

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###Hinzufügen der FileHelper-Klasse

1. Erstellen Sie eine neue `FileHelper`-Klasse im Hauptprojekt mit der portablen Bibliothek. Fügen Sie die folgenden using-Anweisungen hinzu:

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. Fügen Sie die Klassendefinition hinzu:

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

### Hinzufügen eines Handlers für die Dateisynchronisierung

Erstellen Sie eine neue `TodoItemFileSyncHandler`-Klasse im Hauptprojekt mit der portablen Bibliothek. Diese Klasse enthält Rückrufe vom Azure SDK, um Ihren Code zu benachrichtigen, wenn eine Datei hinzugefügt oder entfernt wird.

Das Azure Mobile Client-SDK speichert keine Dateidaten: Das Client-SDK ruft Ihre Implementierung von `IFileSyncHandler` auf, mit der wiederum bestimmt wird, ob und wie Dateien auf dem lokalen Gerät gespeichert werden.

1. Fügen Sie die folgenden using-Anweisungen hinzu:

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. Ersetzen Sie die Klassendefinition durch Folgendes:

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###Aktualisieren von TodoItemManager

1. Heben Sie in der Datei **TodoItemManager.cs** die Auskommentierung der Zeile `#define OFFLINE_SYNC_ENABLED` auf.

2. Fügen Sie in der Datei **TodoItemManager.cs** die folgenden using-Anweisungen hinzu:

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. Fügen Sie im Konstruktor von `TodoItemManager` nach dem Aufruf von `DefineTable()` Folgendes hinzu:

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. Ersetzen Sie im Konstruktor den Aufruf von `InitializeAsync` durch Folgendes. So wird sichergestellt, dass Rückrufe vorhanden sind, wenn Datensätze im lokalen Speicher geändert werden. Vom Feature für die Dateisynchronisierung werden diese Rückrufe verwendet, um Ihren Handler für die Dateisynchronisierung auszulösen.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. Fügen Sie in `SyncAsync()` nach dem Aufruf von `PushAsync()` Folgendes hinzu:

        await this.todoTable.PushFileChangesAsync();

6. Fügen Sie `TodoItemManager` die folgenden Methoden hinzu:

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###Hinzufügen einer Detailansicht

In diesem Abschnitt fügen Sie eine neue Detailansicht für ein Aufgabenelement (todo item) hinzu. Diese Ansicht wird erstellt, wenn der Benutzer ein Aufgabenelement auswählt und es zulässig ist, dass einem Element neue Bilder hinzugefügt werden.

1. Fügen Sie dem Projekt mit der portablen Bibliothek eine neue **TodoItemImage**-Klasse hinzu, indem Sie die folgende Implementierung verwenden:

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. Bearbeiten Sie die Datei **App.cs**. Ersetzen Sie die Initialisierung von `MainPage` durch Folgendes:
    
        MainPage = new NavigationPage(new TodoList());

3. Fügen Sie in der Datei **App.cs** die folgende Eigenschaft hinzu:

        public static object UIContext { get; set; }

4. Klicken Sie mit der rechten Maustaste auf das Projekt mit der portablen Bibliothek, und wählen Sie **Hinzufügen** -> **Neues Element** -> **Plattformübergreifend** -> **Forms-XAML-Seite** aus. Benennen Sie die Ansicht mit `TodoItemDetailsView`.

5. Öffnen Sie die Datei **TodoItemDetailsView.xaml**, und ersetzen Sie den Textkörper von ContentPage durch Folgendes:

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. Bearbeiten Sie die Datei **TodoItemDetailsView.xaml.cs**, und fügen Sie die folgenden using-Anweisungen hinzu:

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. Ersetzen Sie die Implementierung von `TodoItemDetailsView` durch Folgendes:

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###Aktualisieren der Hauptansicht 

Aktualisieren Sie die Hauptansicht, um die Detailansicht zu öffnen, wenn ein Aufgabenelement ausgewählt wird.

Ersetzen Sie in der Datei **TodoList.xaml.cs** die Implementierung von `OnSelected` durch Folgendes:

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###Aktualisieren des Android-Projekts

Fügen Sie dem Android-Projekt plattformspezifischen Code hinzu, z. B. Code zum Herunterladen einer Datei und Verwenden der Kamera zum Aufnehmen eines neuen Bilds.

Für diesen Code wird das Xamarin.Forms-Element [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) verwendet, um zur Laufzeit die richtige plattformspezifische Klasse zu laden.

1. Fügen Sie dem Android-Projekt die Komponente **Xamarin.Mobile** hinzu.

2. Fügen Sie eine neue `DroidPlatform`-Klasse mit der folgenden Implementierung hinzu. Ersetzen Sie „YourNamespace“ durch den Hauptnamespace Ihres Projekts.

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Bearbeiten Sie die Datei **MainActivity.cs**. Fügen Sie unter `OnCreate` vor dem Aufruf von `LoadApplication()` Folgendes hinzu:

        App.UIContext = this;

###Aktualisieren des iOS-Projekts

Fügen Sie dem iOS-Projekt plattformspezifischen Code hinzu.

1. Fügen Sie dem iOS-Projekt die Komponente **Xamarin.Mobile** hinzu.

2. Fügen Sie eine neue `TouchPlatform`-Klasse mit der folgenden Implementierung hinzu. Ersetzen Sie „YourNamespace“ durch den Hauptnamespace Ihres Projekts.

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Bearbeiten Sie die Datei **AppDelegate.cs**, und heben Sie die Auskommentierung des Aufrufs von `SQLitePCL.CurrentPlatform.Init()` auf.

###Aktualisieren des Windows-Projekts

1. Installieren Sie die Visual Studio-Erweiterung [SQLite für Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). Weitere Informationen finden Sie im Tutorial [Aktivieren der Offlinesynchronisierung für Ihre Windows-App](app-service-mobile-windows-store-dotnet-get-started-offline-data.md). 

2. Bearbeiten Sie die Datei **Package.appxmanifest**, und aktivieren Sie die Funktion **Webcam**.

3. Fügen Sie eine neue `WindowsStorePlatform`-Klasse mit der folgenden Implementierung hinzu. Ersetzen Sie „YourNamespace“ durch den Hauptnamespace Ihres Projekts.

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##Zusammenfassung

In diesem Artikel wird beschrieben, wie Sie die neue Dateiunterstützung im Azure Mobile-Client und -Server-SDK für Azure Storage verwenden.

- Erstellen Sie ein Speicherkonto, und fügen Sie dem Back-End für die mobile App die Verbindungszeichenfolge hinzu. Nur das Back-End verfügt über den Schlüssel für Azure Storage: Der mobile Client fordert jeweils ein SAS-Token (Shared Access Signature) an, wenn er Zugriff auf Azure Storage benötigt. Weitere Informationen zu SAS-Token in Azure Storage finden Sie unter [Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell].

- Erstellen Sie einen Controller, mit dem `StorageController` als Unterklasse eingerichtet wird, um die SAS-Tokenanforderungen zu verarbeiten und die Dateien abzurufen, die einem Datensatz zugeordnet sind. Standardmäßig werden Dateien einem Datensatz zugeordnet, indem die Datensatz-ID als Teil des Containernamens verwendet wird. Sie können das Verhalten anpassen, indem Sie eine Implementierung von `IContainerNameResolver` angeben. Die SAS-Tokenrichtlinie kann ebenfalls angepasst werden.

- Das Azure Mobile Client-SDK speichert selbst keine Dateidaten. Stattdessen ruft das Client-SDK Ihr `IFileSyncHandler`-Element auf, mit dem dann bestimmt wird, wie Dateien (falls zutreffend) auf dem lokalen Gerät gespeichert werden. Der Handler für die Synchronisierung wird wie folgt registriert:

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + `IFileSyncHandler.GetDataSource` wird aufgerufen, wenn das Azure Mobile Client-SDK die Dateidaten benötigt (z. B. im Rahmen des Uploadprozesses). So können Sie verwalten, wie Dateien (falls zutreffend) auf dem lokalen Gerät gespeichert werden, und diese Informationen bei Bedarf zurückgeben.

      + `IFileSyncHandler.ProcessFileSynchronizationAction` wird im Rahmen des Dateisynchronisierungsflusses aufgerufen. Es werden ein Dateiverweis und ein FileSynchronizationAction-Enumerationswert bereitgestellt, damit Sie entscheiden können, wie das Ereignis von Ihrer Anwendung behandelt werden soll (z. B. automatisches Herunterladen einer Datei, wenn sie erstellt oder aktualisiert wird, oder Löschen einer Datei vom lokalen Gerät, wenn die Datei auf dem Server gelöscht wird).

- Ein `MobileServiceFile`-Element kann entweder im Online- oder im Offlinemodus genutzt werden, indem entweder `IMobileServiceTable` oder `IMobileServiceSyncTable` verwendet wird. Im Offlineszenario wird der Upload durchgeführt, wenn die App `PushFileChangesAsync` aufruft. Dies führt dazu, dass die Offline-Vorgangswarteschlange verarbeitet wird. Für jeden Dateivorgang ruft das Azure Mobile Client-SDK die `GetDataSource`-Methode für die `IFileSyncHandler`-Instanz auf, um die Dateiinhalte für den Upload abzurufen.

- Rufen Sie zum Abrufen der Dateien eines Elements die `GetFilesAsync`-Methode für die Instanz `IMobileServiceTable<T>` oder IMobileServiceSyncTable<T> auf. Diese Methode gibt eine Liste mit Dateien zurück, die dem bereitgestellten Datenelement zugeordnet sind. (Hinweis: Dies ist ein *lokaler* Vorgang, bei dem die Dateien basierend auf dem Zustand des Objekts bei seiner letzten Synchronisierung zurückgegeben werden. Sie sollten zuerst einen Synchronisierungsvorgang initiieren, um eine aktualisierte Liste mit Dateien vom Server zu erhalten.)

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- Das Feature für die Dateisynchronisierung verwendet im lokalen Speicher Benachrichtigungen über Datensatzänderungen, um die Datensätze abzurufen, die der Client im Rahmen eines Push- oder Pullvorgangs empfangen hat. Dies wird erreicht, indem lokale Benachrichtigungen und Serverbenachrichtigungen für den Synchronisierungskontext mit dem `StoreTrackingOptions`-Parameter aktiviert werden.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + Es sind auch andere Optionen zur Speicherverfolgung verfügbar, z. B. Benachrichtigungen nur für den lokalen Speicher oder nur für den Server. Sie können benutzerdefinierte Rückrufe hinzufügen oder besitzen, indem Sie die `EventManager`-Eigenschaft von `IMobileServiceClient` verwenden:

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- Es ist möglich, Dateien einem Datensatz hinzuzufügen oder daraus zu entfernen, indem Sie den Blobspeicher direkt ändern. Dies liegt daran, dass die Zuordnung über eine Benennungskonvention erfolgt. In diesem Fall sollten Sie aber immer den **Datensatz-Zeitstempel aktualisieren, wenn die zugeordneten Blobs geändert werden**. Das Azure Mobile Client-SDK führt immer eine Aktualisierung eines Datensatzes durch, wenn eine Datei hinzugefügt oder entfernt wird.

    Der Grund für diese Anforderung ist, dass einige mobile Clients bereits im lokalen Speicher über den Datensatz verfügen. Wenn diese Clients einen inkrementellen Pullvorgang durchführen, wird dieser Datensatz nicht zurückgegeben, und der Client fragt keine neuen zugeordneten Dateien ab. Zur Vermeidung dieses Problems ist es ratsam, den Datensatz-Zeitstempel zu aktualisieren, wenn Sie eine Blobspeicheränderung vornehmen, bei der das Azure Mobile Client-SDK nicht verwendet wird.

- Für das Clientprojekt wird das Muster [Xamarin.Forms DependencyService] verwendet, um zur Laufzeit die richtige plattformspezifische Klasse zu laden. In diesem Beispiel haben wir eine `IPlatform`-Schnittstelle mit Implementierungen in den einzelnen plattformspezifischen Projekten definiert.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Erstellen einer Xamarin.Forms-App]: ../app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Erstellen eines Azure-Speicherkontos]: ../storage/storage-create-storage-account.md#create-a-storage-account

<!---HONumber=AcomDC_0211_2016-->