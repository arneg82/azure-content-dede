.NET-Anwendungen können den **StackExchange.Redis**-Cacheclient verwenden. Dieser kann mithilfe eines NuGet-Pakets in Visual Studio konfiguriert werden, um die Konfiguration von Cacheclientanwendungen zu vereinfachen.

>[AZURE.NOTE] Weitere Informationen finden Sie auf der [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis)-Seite auf GitHub und in der [Dokumentation für den StackExchange.Redis-Cacheclient](http://github.com/StackExchange/StackExchange.Redis#documentation).

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und wählen Sie **NuGet-Pakete verwalten** aus, um eine Clientanwendung in Visual Studio mithilfe des StackExchange.Redis-NuGet-Pakets zu konfigurieren.

![NuGet-Pakete verwalten](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Geben Sie **StackExchange.Redis** oder **StackExchange.Redis.StrongName** in das Suchfeld ein, wählen Sie die gewünschte Version aus den Ergebnissen aus, und klicken Sie auf **Installieren**.

>[AZURE.NOTE] Falls Sie eine Version der **StackExchange.Redis**-Clientbibliothek mit starkem Namen bevorzugen, wählen Sie **StackExchange.Redis.StrongName** aus; andernfalls wählen Sie **StackExchange.Redis** aus.

![StackExchange.Redis-NuGet-Paket](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Das NuGet-Paket wird heruntergeladen und fügt die benötigten Assemblyverweise zu Ihrer Clientanwendung hinzu, um mithilfe des StackExchange.Redis-Cacheclients auf den Azure Redis Cache zuzugreifen.

>[AZURE.NOTE] Wenn Sie das Projekt zuvor für die Verwendung von StackExchange.Redis konfiguriert haben, können Sie mithilfe von **NuGet Package Manager** nach Updates für das Paket suchen. Um nach aktualisierten Versionen des NuGet-Pakets „StackExchange.Redis“ zu suchen und diese zu installieren, klicken Sie im Fenster **NuGet Package Manager** auf **Updates**. Wenn ein Update des NuGet-Pakets „StackExchange.Redis“ verfügbar ist, können Sie Ihr Projekt für die Verwendung der aktualisierten Version aktualisieren.

<!---HONumber=AcomDC_0622_2016-->