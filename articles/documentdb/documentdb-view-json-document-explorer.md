<properties
	pageTitle="DocumentDB-Dokument-Explorer, JSON-Anzeige | Microsoft Azure"
	description="Sie erhalten Informationen zum DocumentDB-Dokument-Explorer, einem Tool im Azure-Portal zum Anzeigen von JSON-Code und Bearbeiten, Erstellen und Hochladen von JSON-Dokumenten mit DocumentDB (NoSQL-Dokumentdatenbank)."
    keywords="JSON-Anzeige"
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="anhoh"/>

# Anzeigen, Bearbeiten, Erstellen und Hochladen von JSON-Dokumenten mithilfe von DocumentDB-Dokument-Explorer

Dieser Artikel enthält eine Übersicht über den [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)-Dokument-Explorer, ein Tool im Azure-Portal zum Anzeigen, Bearbeiten, Erstellen, Hochladen und Filtern von JSON-Dokumenten mit DocumentDB.

Beachten Sie, dass der Dokument-Explorer für DocumentDB-Konten mit Protokollunterstützung für MongoDB nicht aktiviert ist. Diese Seite wird aktualisiert, wenn dieses Feature aktiviert wurde.

## Starten von Dokument-Explorer

1. Klicken Sie im Azure-Portal in der Navigationsleiste auf **DocumentDB (NoSQL)**. Wenn **DocumentDB (NoSQL)** nicht angezeigt wird, klicken Sie auf **Weitere Dienste** und dann auf **DocumentDB (NoSQL)**.

2. Klicken Sie im Ressourcenmenü auf **Dokument-Explorer**.
 
	![Screenshot des Dokument-Explorer-Befehls](./media/documentdb-view-json-document-explorer/documentexplorercommand.png)

    Die Dropdownlisten **Datenbanken** und **Sammlungen** auf dem Blatt **Dokument-Explorer** werden auf der Grundlage des Kontexts, in dem Sie den Dokument-Explorer starten, automatisch aufgefüllt.

## Erstellen eines Dokuments

1. [Starten Sie den Dokument-Explorer](#launch-document-explorer).

2. Klicken Sie auf dem Blatt **Dokument-Explorer** auf **Dokument erstellen**.

    Auf dem Blatt **Dokument** wird ein kurzer JSON-Codeausschnitt bereitgestellt.

	![Screenshot des Dokument-Explorers – Erstellen der Dokumentumgebung, in der Sie JSON-Code anzeigen und bearbeiten können](./media/documentdb-view-json-document-explorer/createdocument.png)

2. Geben bzw. fügen Sie auf dem Blatt **Dokument** den Inhalt des JSON-Dokuments ein, das Sie erstellen möchten. Klicken Sie anschließend auf **Speichern**, um Ihr Dokument per Commit an die Datenbank und die Sammlung zu übergeben, die auf dem Blatt **Dokument-Explorer** angegeben sind.

	![Screenshot des Dokument-Explorers – Befehl "Speichern"](./media/documentdb-view-json-document-explorer/savedocument1.png)

	> [AZURE.NOTE] Wenn Sie keine "id"-Eigenschaft angegebn, fügt Dokument-Explorer automatisch eine id-Eigenschaft hinzu und generiert eine GUID als id-Wert.

    Wenn Sie bereits über Daten aus JSON-Dateien, MongoDB, SQL Server, CSV-Dateien, Azure-Tabellenspeichern, Amazon DynamoDB, HBase oder anderen DocumentDB-Sammlungen verfügen, können Sie das [Datenmigrationstool](documentdb-import-data.md) von DocumentDB verwenden, um Ihre Daten schnell zu importieren.

## Bearbeiten eines Dokuments

1. [Starten Sie den Dokument-Explorer](#launch-document-explorer).

2. Wenn Sie ein vorhandenes Dokument bearbeiten möchten, wählen Sie es auf dem Blatt **Dokument-Explorer** aus, bearbeiten Sie es auf dem Blatt **Dokument**, und klicken Sie anschließend auf **Speichern**.

    ![Screenshot des Dokument-Explorers – Funktion zum Bearbeiten von Dokumenten für JSON-Anzeige](./media/documentdb-view-json-document-explorer/editdocument.png)

    Wenn Sie ein Dokument bearbeiten und die Änderungen verwerfen möchten, klicken Sie auf dem Blatt **Dokument** einfach auf den Befehl **Verwerfen**, und bestätigen Sie den Vorgang. Dadurch wird wieder die vorherige Version des Dokuments geladen.

    ![Screenshot des Dokument-Explorers – Befehl "Verwerfen"](./media/documentdb-view-json-document-explorer/discardedit.png)

## Löschen eines Dokuments

1. [Starten Sie den Dokument-Explorer](#launch-document-explorer).

2. Wählen Sie das Dokument im **Dokument-Explorer** aus, klicken Sie auf **Löschen** ,und bestätigen Sie anschließend den Löschvorgang. Nach der Bestätigung wird das Dokument sofort aus der Liste im Dokument-Explorer entfernt.

	![Screenshot des Dokument-Explorers – Befehl "Löschen"](./media/documentdb-view-json-document-explorer/deletedocument.png)

## Verwenden von JSON-Dokumenten

Der Dokument-Explorer überprüft, ob alle neuen oder bearbeiteten Dokumente gültigen JSON-Code enthalten. Sie können zum Anzeigen von JSON-Fehlern sogar mit der Maus auf den falschen Bereich zeigen, um Details zum Validierungsfehler abzurufen.

![Screenshot des Dokument-Explorers – Hervorhebung von ungültigem JSON-Code](./media/documentdb-view-json-document-explorer/invalidjson1.png)

Dokument-Explorer verhindert auch das Speichern eines Dokuments mit ungültigem JSON.

![Screenshot des Dokument-Explorers – Ungültiger JSON-Code, Speicherfehler](./media/documentdb-view-json-document-explorer/invalidjson2.png)

Mit dem Dokument-Explorer können Sie ganz einfach die Systemeigenschaften des aktuell geladenen Dokuments anzeigen, indem Sie auf Befehl **Eigenschaften** klicken.

![Screenshot des Dokument-Explorers – Ansicht der Dokumenteigenschaften](./media/documentdb-view-json-document-explorer/documentproperties.png)

> [AZURE.NOTE] Die Zeitstempeleigenschaft (\_ts) wird intern als Epochenzeit dargestellt, im Dokument-Explorer wird der Wert jedoch in einem vom Menschen lesbaren GMT-Format angezeigt.

## Filtern von Dokumenten
Der Dokument-Explorer unterstützt eine Reihe an Navigationsoptionen und erweiterten Einstellungen.

Standardmäßig lädt der Dokument-Explorer die ersten 100 Dokumente in die ausgewählte Sammlung, sortiert nach Erstellungsdatum, vom ältesten bis zum neuesten. Sie können weitere Dokumente (in Batches von je 100) laden, indem Sie die Option **Mehr laden** am unteren Rand des Dokument-Explorer-Blatts wählen. Mithilfe des Befehls **Filter** können Sie wählen, welche Dokumente geladen werden sollen.

1. [Starten Sie den Dokument-Explorer](#launch-document-explorer).

2. Klicken Sie am oberen Rand des Blatts **Dokument-Explorer** auf **Filter**.

    ![Screenshot des Dokument-Explorers – Filtereinstellungen](./media/documentdb-view-json-document-explorer/documentexplorerfiltersettings.png)
  
3.  Die Filtereinstellungen werden unterhalb der Befehlsleiste angezeigt. Geben Sie in den Filtereinstellungen eine WHERE-Klausel bzw. eine ORDER BY-Klausel an, und klicken Sie dann auf **Filter**.

	![Screenshot des Dokument-Explorers – Blatt „Einstellungen“](./media/documentdb-view-json-document-explorer/documentexplorerfiltersettings2.png)

	Dokument-Explorer aktualisiert die Ergebnisse automatisch mit Dokumenten, die für die Filterabfrage eine Übereinstimmung ergeben. Weitere Informationen zur DocumentDB-SQL-Grammatik finden Sie im Artikel [SQL-Abfrage und SQL-Syntax in DocumentDB](documentdb-sql-query.md). Alternativ können Sie sich auch den [Spickzettel für SQL-Abfragen](documentdb-sql-query-cheat-sheet.md) ausdrucken.

    Mit den Dropdownlistenfeldern **Datenbank** und **Sammlung** können Sie die Sammlung, aus der aktuell Dokumente angezeigt werden, ganz einfach ändern, ohne den Dokument-Explorer schließen und neu starten zu müssen.

    Dokument-Explorer unterstützt auch das Filtern des aktuell geladenen Dokumentensets nach der id-Eigenschaft. Geben Sie sie einfach in das Feld „Dokumente nach id filtern“ ein.

	![Screenshot der Dokument-Explorers mit hervorgehobenem Filter](./media/documentdb-view-json-document-explorer/documentexplorerfilter.png)

	Die Ergebnisse in der Dokument-Explorer-Liste werden nun nach den angegebenen Kriterien gefiltert.

	![Screenshot der Dokument-Explorers mit gefilterten Ergebnissen](./media/documentdb-view-json-document-explorer/documentexplorerfilterresults.png)

	> [AZURE.IMPORTANT] Die Filterfunktion des Dokument-Explorers filtert nur aus dem ***aktuell*** geladenen Dokumentensatz und führt keine Abfrage für die aktuell ausgewählte Sammlung aus.

4. Klicken Sie zum Aktualisieren der vom Dokument-Explorer geladenen Dokumentenliste im oberen Bereich des Blatts auf **Aktualisieren**.

	![Screenshot des Dokument-Explorers – Befehl "Aktualisieren"](./media/documentdb-view-json-document-explorer/documentexplorerrefresh.png)

## Massenhinzufügen von Dokumenten

Dokument-Explorer unterstützt die Sammelerfassung von einem oder mehreren JSON-Dokumenten. Pro Uploadvorgang sind bis zu 100 JSON-Dateien zulässig.

1. [Starten Sie den Dokument-Explorer](#launch-document-explorer).

2. Klicken Sie zum Starten des Uploadvorgangs auf **Dokument hochladen**.

	![Screenshot des Dokument-Explorers – Sammelerfassungsfunktion](./media/documentdb-view-json-document-explorer/uploaddocument1.png)

    Das Blatt **Dokument hochladen** wird geöffnet.

2. Klicken Sie auf die Schaltfläche „Durchsuchen“, um ein Datei-Explorer-Fenster zu öffnen, wählen Sie die JSON-Dokumente aus, die hochgeladen werden sollen, und klicken Sie dann auf **Öffnen**.

	![Screenshot des Dokument-Explorers – Sammelerfassungsprozess](./media/documentdb-view-json-document-explorer/uploaddocument2.png)

	> [AZURE.NOTE] Dokument-Explorer unterstützt derzeit bis zu 100 JSON-Dokumente pro einzelnen Hochladevorgang.

3. Sobald Sie mit der Auswahl fertig sind, klicken Sie auf die Schaltfläche **Hochladen**. Die Dokumente werden automatisch zum Dokument-Explorer-Raster hinzugefügt, und die Hochladeergebnisse werden angezeigt, während der Prozess weiterläuft. Importfehler werden für einzelne Dateien gemeldet.

	![Screenshot des Dokument-Explorers – Sammelerfassungsergebnisse](./media/documentdb-view-json-document-explorer/uploaddocument3.png)

4. Sobald der Vorgang abgeschlossen ist, können Sie bis zu 100 weitere Dokumente auswählen, die Sie hochladen möchten.

## Verwenden von JSON-Dokumenten außerhalb des Portals

Der Dokument-Explorer im Azure-Portal ist nur eine Möglichkeit zur Verwendung von Dokumenten in DocumentDB. Für die Arbeit mit Dokumenten können Sie auch die [REST-API](https://msdn.microsoft.com/library/azure/mt489082.aspx) oder die [Client-SDKs](documentdb-sdk-dotnet.md) verwenden. Beispielcode finden Sie unter [.NET SDK-Dokumentbeispiele](documentdb-dotnet-samples.md#document-examples) sowie unter [Node.js SDK-Dokumentbeispiele](documentdb-nodejs-samples.md#document-examples).

Wenn Sie Dateien aus einer anderen Quelle (JSON-Dateien, MongoDB, SQL Server, CSV-Dateien, Azure-Tabellenspeicher, Amazon DynamoDB oder HBase) importieren oder migrieren müssen, können Sie das DocumentDB-[Datenmigrationstool](documentdb-import-data.md) nutzen, um Ihre Daten schnell in DocumentDB zu importieren.

## Problembehandlung

**Symptom**: Dokument-Explorer gibt **Keine Dokumente gefunden** zurück.

**Lösung**: Stellen Sie sicher, dass Sie das richtige Abonnement sowie die richtige Datenbank und Sammlung, wo die Dokumente eingefügt wurden, ausgewählt haben. Überprüfen Sie außerdem, ob Sie innerhalb Ihrer Durchsatzkontingente arbeiten. Wenn Sie bei maximalem Durchsatz arbeiten und gedrosselt werden, senken Sie die Anwendungsnutzung, um unterhalb des Maximaldurchsatzes für die Sammlung zu arbeiten.

**Erklärung**: Das Portal ist eine Anwendung wie jede andere, die Aufrufe an Ihre DocumentDB-Datenbank und Sammlung richtet. Wenn Ihre Anfragen derzeit aufgrund von Aufrufen von einer separaten Anwendung gedrosselt sind, kann das Portal auch gedrosselt werden, sodass Ressourcen nicht im Portal angezeigt werden. Um das Problem zu lösen, beheben Sie die Ursache für den hohen Durchsatz, und aktualisieren Sie dann das Portalblatt. Informationen zum Messen und Senken der Durchsatznutzung finden Sie im Abschnitt [Durchsatz](documentdb-performance-tips.md#throughput) des Artikels [Leistungstipps](documentdb-performance-tips.md).

## Nächste Schritte

Weitere Informationen zur im Dokument-Explorer unterstützten DocumentDB-SQL-Grammatik finden Sie im Artikel [SQL-Abfrage und SQL-Syntax](documentdb-sql-query.md). Alternativ können Sie sich auch den [Spickzettel für SQL-Abfragen](documentdb-sql-query-cheat-sheet.md) ausdrucken.

Der [Lernpfad](https://azure.microsoft.com/documentation/learning-paths/documentdb/) ist ebenfalls eine hilfreiche Ressource, um mehr über DocumentDB zu erfahren.

<!---HONumber=AcomDC_0831_2016-->