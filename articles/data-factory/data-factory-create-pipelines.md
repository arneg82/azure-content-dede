<properties 
	pageTitle="Erstellen/Planen von Pipelines, Kettenaktivitäten in Data Factory | Microsoft Azure" 
	description="Es wird beschrieben, wie Sie eine Datenpipeline in Azure Data Factory erstellen, um Daten zu verschieben und zu transformieren. Erstellen Sie einen datengesteuerten Workflow zum Erzeugen von fertigen Informationen." 
    keywords="Datenpipeline, datengesteuerter Workflow"
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article"
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Pipelines und Aktivitäten in Azure Data Factory: Erstellen/Planen von Pipelines und Kettenaktivitäten
In diesem Artikel erhalten Sie Informationen zu Pipelines und Aktivitäten in Azure Data Factory und erfahren, wie diese zum Erstellen datengesteuerter End-to-End-Workflows für Ihren Fall genutzt werden können.

> [AZURE.NOTE] In diesem Artikel wird davon ausgegangen, dass Sie die Artikel [Einführung in den Azure Data Factory-Dienst](data-factory-introduction.md) und [Datasets in Azure Data Factory](data-factory-create-datasets.md) gelesen haben. Wenn Sie noch nicht über praktische Erfahrung mit dem Erstellen von Data Factorys verfügen, hilft Ihnen das Tutorial [Erstellen der ersten Data Factory ](data-factory-build-your-first-pipeline.md) dabei, den vorliegenden Artikel besser zu verstehen.

## Was ist eine Datenpipeline?
**Pipelines sind logische Gruppierungen von Aktivitäten**. Sie dienen zum Gruppieren von Aktivitäten zu einer Einheit, die zum Ausführen einer Aufgabe verwendet wird. Um Pipelines besser zu verstehen, müssen Sie sich zunächst mit Aktivitäten befassen.

## Was ist eine Aktivität?
Aktivitäten definieren die Aktionen, die Sie auf Ihre Daten anwenden. Jede Aktivität verwendet von null bis zu mehreren [Datasets](data-factory-create-datasets.md) als Eingaben und erzeugt von ein bis zu mehreren Datasets als Ausgaben. **Eine Aktivität ist eine Einheit für die Orchestrierung in Azure Data-Factory.**

Beispielsweise können Sie eine Kopieraktivität verwenden, um das Kopieren von Daten aus einem Dataset in ein anderes zu orchestrieren. Auf ähnliche Weise können Sie eine HDInsight Hive-Aktivität verwenden, die eine Hive-Abfrage auf einem Azure HDInsight-Cluster ausführt, um Ihre Daten zu transformieren oder zu analysieren. Azure Data Factory bietet eine Vielzahl von [Aktivitäten zur Datentransformation, -analyse](data-factory-data-transformation-activities.md) und [-verschiebung](data-factory-data-movement-activities.md). Sie können auch eine benutzerdefinierte .NET-Aktivität erstellen, um eigenen Code auszuführen.

Berücksichtigen Sie die beiden folgenden Datasets:

**Azure SQL-Dataset**

Die Tabelle „MyTable“ enthält eine Spalte „timestampcolumn“, die Sie beim Angeben des datetime-Werts für den Zeitpunkt unterstützt, zu dem die Daten in die Datenbank eingefügt wurden.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure-Blobdataset**

Daten werden stündlich in ein neues Blob kopiert. Hierbei gibt der Pfad des Blobs jeweils Datum und Uhrzeit mit stündlicher Genauigkeit an.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


Die Kopieraktivität in der folgenden Pipeline kopiert Daten aus Azure SQL in Azure Blob Storage. Sie verwendet stündlich die Azure SQL-Tabelle als Eingabedataset und schreibt die Daten in den Azure Blobspeicher, der durch das Dataset "AzureBlobOutput" dargestellt wird. Das Ausgabedataset wird ebenfalls stündlich generiert. Informationen zum Kopieren der Daten in Bezug auf die Zeiteinheit finden Sie im Abschnitt [Planung und Ausführung](#scheduling-and-execution). Diese Pipeline besitzt einen aktiven Zeitraum von drei Stunden: „2015-01-01T08:00:00“ bis „2015-01-01T11:00:00“.

**Pipeline:**
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

Nachdem wir einen kurzen Überblick über Aktivitäten gewonnen haben, kommen wir jetzt auf die Pipeline zurück.
 
Pipelines sind logische Gruppierungen von Aktivitäten. Sie dienen zum Gruppieren von Aktivitäten zu einer Einheit, die zum Ausführen einer Aufgabe verwendet wird. **Eine Pipeline ist außerdem die Einheit zur Bereitstellung und Verwaltung von Aktivitäten.** Beispielsweise möchten Sie logisch verwandte Aktivitäten als eine Pipeline zusammenstellen, sodass sie zusammen in den aktiven oder angehaltenen Status versetzt werden können.

Durch das Definieren von Abhängigkeiten zwischen Aktivitäten kann ein Ausgabedataset aus einer Aktivität in einer Pipeline als Eingabedataset für eine andere Aktivität in derselben/einer anderen Pipeline dienen. Weitere Informationen finden Sie unter [Planung und Ausführung](#chaining-activities).

In der Regel werden beim Erstellen einer Pipeline in Azure Data Factory folgende Schritte ausgeführt:

1.	Erstellen Sie eine Data Factory (falls noch nicht erstellt).
2.	Erstellen Sie einen verknüpften Dienst für jeden Datenspeicher oder Computedienst.
3.	Erstellen Sie Eingabe- und Ausgabedatasets.
4.	Erstellen Sie eine Pipeline mit Aktivitäten, die auf die Datasets angewendet werden.

![Data Factory-Entitäten](./media/data-factory-create-pipelines/entities.png)

Sehen wir uns an, wie eine Pipeline definiert wird.

## Anatomie einer Pipeline  

Die generische Struktur für eine Pipeline sieht wie folgt aus:

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

Im Abschnitt **activities** kann mindestens eine Aktivität definiert werden. Jede Aktivität besitzt auf oberster Ebene die folgende Struktur:

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

In der folgenden Tabelle werden die Eigenschaften in der Aktivität und den Pipeline-JSON-Definitionen beschrieben:

Tag | Beschreibung | Erforderlich
--- | ----------- | --------
Name | Der Name der Aktivität oder der Pipeline. Geben Sie einen Namen an, der die Aktion darstellt, für deren Durchführung die Aktivität oder die Pipeline konfiguriert ist.<br/><ul><li>Maximale Anzahl von Zeichen: 260</li><li>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich (\_) beginnen</li><li>Folgende Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“ , „\\“</li></ul> | Ja
description | Beschreibung des Verwendungszwecks der Aktivität oder der Pipeline | Ja
Typ | Gibt den Typ der Aktivität an. Verschiedene Typen von Aktivitäten finden Sie in den Artikeln [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) und [Transformationsaktivitäten von Daten](data-factory-data-transformation-activities.md). | Ja
inputs | Von der Aktivität verwendete Eingabetabellen<br/><br/>// eine Eingabetabelle<br/>"inputs": [ { "name": "inputtable1" } ],<br/><br/>// zwei Eingabetabellen <br/>"inputs": [ { "name": "inputtable1" }, { "name": "inputtable2" } ], | Ja
outputs | Von der Aktivität verwendete Ausgabetabellen.// eine Ausgabetabelle<br/>"outputs": [ { "name": “outputtable1” } ],<br/><br/>//zwei Ausgabetabellen<br/>"outputs": [ { "name": “outputtable1” }, { "name": “outputtable2” } ], | Ja
linkedServiceName | Name des verknüpften Diensts, der von der Aktivität verwendet wird. <br/><br/>Für eine Aktivität kann es erforderlich sein, den verknüpften Dienst anzugeben, der mit der erforderlichen Computeumgebung verknüpft ist. | „Ja“ für die HDInsight-Aktivität und die Azure Machine Learning-Aktivität zur Batchbewertung <br/><br/>„Nein“ für alle übrigen
typeProperties | Eigenschaften im Abschnitt „typeProperties“ sind abhängig vom Typ der Aktivität. | Nein
policy | Richtlinien, die das Laufzeitverhalten der Aktivität beeinflussen. Falls dies nicht angegeben wird, werden Standardrichtlinien verwendet. | Nein
Start | Startdatum/-uhrzeit für die Pipeline. Muss im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. <br/><br/>Es ist möglich, eine lokale Zeit anzugeben, z.B. eine EST-Zeit. Beispiel: „2016-02-27T06:00:00**-05:00**“ steht für 6:00 Uhr EST.<br/><br/>Die Eigenschaften „start“ und „end“ geben zusammen den aktiven Zeitraum der Pipeline an. Ausgabeslices werden nur in diesem aktiven Zeitraum erstellt. | Nein<br/><br/>Wenn Sie einen Wert für die Endeigenschaft angeben, müssen Sie auch einen Wert für die Starteigenschaft angeben.<br/><br/>Sowohl die Start- als auch die Endzeiten zum Erstellen einer Pipeline können leer sein. Beide müssen Werte enthalten, um für die Pipeline einen aktiven Zeitraum für die Ausführung festzulegen. Wenn Sie beim Erstellen einer Pipeline keine Start- und Endzeiten angeben, können Sie später zum Festlegen der Werte das Set-AzureRmDataFactoryPipelineActivePeriod-Cmdlet verwenden.
end | Datum und Uhrzeit für das Ende der Pipeline. Muss, falls gewünscht, im ISO-Format angegeben werden. Beispiel: 2014-10-14T17:32:41Z <br/><br/>Es ist möglich, eine lokale Zeit anzugeben, z.B. eine EST-Zeit. Beispiel: „2016-02-27T06:00:00**-05:00**“ steht für 6:00 Uhr EST.<br/><br/>Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die end-Eigenschaft 9999-09-09 an. | Nein <br/><br/>Wenn Sie einen Wert für die start-Eigenschaft angeben, müssen Sie auch einen Wert für die end-Eigenschaft angeben.<br/><br/>Lesen Sie auch die Hinweise zur **start**-Eigenschaft.
isPaused | Bei der Einstellung „true“ wird die Pipeline nicht ausgeführt. Standardwert = false. Sie können diese Eigenschaft zum Aktivieren oder Deaktivieren verwenden. | Nein 
scheduler | Die „scheduler“-Eigenschaft wird verwendet, um die gewünschte Planung für die Aktivität zu definieren. Die untergeordneten Eigenschaften sind identisch mit denen der [availability-Eigenschaft in einem Dataset](data-factory-create-datasets.md#Availability). | Nein |   
| pipelineMode | Die Methode zum Planen von Ausführungen für die Pipeline. Zulässige Werte sind: „scheduled“ (Standard) und „onetime“.<br/><br/>„scheduled“ bedeutet, dass die Pipeline in einem bestimmten Zeitintervall gemäß ihrem aktiven Zeitraum (Start- und Endzeit) ausgeführt wird. „onetime“ bedeutet, dass die Pipeline nur einmal ausgeführt wird. Pipelines mit einmaliger Ausführung können derzeit nach der Erstellung nicht geändert oder aktualisiert werden. Informationen zur Einstellung der einmaligen Ausführung finden Sie unter [Pipeline mit einmaliger Ausführung](data-factory-scheduling-and-execution.md#onetime-pipeline). | Nein | 
| expirationTime | Zeitraum, für den die Pipeline nach der Erstellung gültig ist und für den die Bereitstellung aufrechterhalten werden sollte. Wenn die Pipeline keine aktiven, fehlerhaften oder ausstehenden Ausführungen enthält, wird sie bei Erreichen der Ablaufzeit automatisch gelöscht. | Nein | 
| datasets | Liste der Datasets, die von den in der Pipeline definierten Aktivitäten verwendet werden sollen. Diese Eigenschaft kann verwendet werden, um Datasets zu definieren, die spezifisch für diese Pipeline und nicht innerhalb der Data Factory definiert sind. In dieser Pipeline definierte Datasets können nicht gemeinsam genutzt, sondern nur von dieser Pipeline verwendet werden. Informationen hierzu finden Sie unter [Zugeordnete Datasets](data-factory-create-datasets.md#scoped-datasets).| Nein |  
 

## Aktivitätstypen für Datenverschiebung und Datentransformation
Azure Data Factory bietet eine Vielzahl von Aktivitäten zur [Datenverschiebung](data-factory-data-movement-activities.md) und [Datentransformation](data-factory-data-transformation-activities.md).

### Richtlinien
Richtlinien beeinflussen das Laufzeitverhalten einer Aktivität, besonders dann, wenn der Slice einer Tabelle verarbeitet wird. Die Details finden Sie in der folgenden Tabelle.

Eigenschaft | Zulässige Werte | Standardwert | Beschreibung
-------- | ----------- | -------------- | ---------------
Parallelität | Ganze Zahl <br/><br/>Höchstwert: 10 | 1 | Anzahl von gleichzeitigen Ausführungen der Aktivität.<br/><br/>Legt die Anzahl der parallelen Ausführungen einer Aktivität fest, die für verschiedene Slices stattfinden können. Wenn eine Aktivität beispielsweise eine große Menge verfügbarer Daten durchlaufen muss, kann die Datenverarbeitung durch einen höheren Parallelitätswert beschleunigt werden. 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | Bestimmt die Reihenfolge der Datenslices, die verarbeitet werden.<br/><br/>Nehmen Sie beispielsweise an, Sie haben zwei Slices (einen um 16:00 Uhr und einen weiteren um 17:00 Uhr), und beide warten auf ihre Ausführung. Wenn Sie „executionPriorityOrder“ auf „NewestFirst“ setzen, wird der Slice von 17 Uhr zuerst verarbeitet. Wenn Sie „executionPriorityOrder“ auf „OldestFirst“ festlegen, wird der Slice von 16 Uhr verarbeitet. 
retry | Ganze Zahl<br/><br/>Höchstwert ist 10. | 3 | Anzahl der Wiederholungsversuche, bevor die Datenverarbeitung für den Slice als Fehler markiert wird. Die Ausführung der Aktivitäten für einen Datenslice wird bis zur angegebenen Anzahl der Wiederholungsversuche wiederholt. Die Wiederholung erfolgt so bald wie möglich nach dem Fehler.
timeout | TimeSpan | 00:00:00 | Timeout für die Aktivität. Beispiel: 00:10:00 (Timeout nach 10 Minuten)<br/><br/>Wenn der Wert nicht angegeben wird oder 0 lautet, ist das Zeitlimit unendlich.<br/><br/>Wenn die Datenverarbeitungszeit für einen Slice den Timeoutwert überschreitet, wird der Vorgang abgebrochen, und das System versucht, die Verarbeitung zu wiederholen. Die Anzahl der Wiederholungsversuche hängt von der Eigenschaft "retry" ab. Wenn ein Timeout auftritt, lautet der Status „TimedOut“.
delay | TimeSpan | 00:00:00 | Geben Sie die Verzögerung an, mit der die Datenverarbeitung des Slice beginnt.<br/><br/>Die Ausführung der Aktivität für einen Datenslice wird gestartet, nachdem die Verzögerung die erwartete Ausführungszeit überschreitet.<br/><br/>Beispiel: 00:10:00 (Verzögerung von 10 Minuten).
longRetry | Ganze Zahl<br/><br/>Höchstwert: 10 | 1 | Die Anzahl von langen Wiederholungsversuchen, bevor die Sliceausführung einen Fehler verursacht.<br/><br/>longRetry-Versuche werden durch longRetryInterval über einen Zeitraum verteilt. Wenn Sie eine Zeit zwischen den Wiederholungsversuchen angeben müssen, verwenden Sie "longRetry". Wenn sowohl „retry“ als auch „longRetry“ angegeben werden, umfasst jeder longRetry-Versuch retry-Versuche, und die maximale Anzahl von Versuchen errechnet sich aus „retry * longRetry“.<br/><br/>Beispiel: Die Richtlinie für die Aktivität enthält Folgendes:<br/>retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Wir nehmen an, dass nur ein Slice auszuführen ist (Status lautet „Waiting“) und dass die Ausführung der Aktivität jedes Mal einen Fehler verursacht. Zunächst würden drei aufeinander folgende Ausführungsversuche durchgeführt. Nach jedem Versuch wäre der Slicestatus „Retry“. Nachdem die ersten drei Versuche durchgeführt wurden, lautet der Slicestatus „LongRetry“.<br/><br/>Nach einer Stunde (Wert von „longRetryInterval“) würden drei weitere aufeinander folgende Ausführungsversuche unternommen werden. Danach würde der Slicestatus "Failed" lauten, und es fänden keine weiteren Versuche statt. Somit wurden insgesamt sechs Versuche unternommen.<br/><br/>Bei einer erfolgreichen Ausführung lautet der Slicestatus „Ready“, und es werden keine weiteren Versuche durchgeführt.<br/><br/>„longRetry“ kann in Situationen verwendet werden, in denen abhängige Daten zu nicht festgelegten Zeiten eingehen oder die gesamte Umgebung, in der die Datenverarbeitung erfolgt, unzuverlässig ist. In solchen Fällen ist die Durchführung von aufeinander folgenden Wiederholungen möglicherweise nicht hilfreich, und die Wiederholung nach einem bestimmten Zeitraum führt vielleicht zur gewünschten Ausgabe.<br/><br/>Vorsicht: Legen Sie für „longRetry“ und „longRetryInterval“ keine hohen Werte fest. In der Regel weisen höhere Werte auf andere Systemprobleme hin. 
longRetryInterval | TimeSpan | 00:00:00 | Die Verzögerung zwischen langen Wiederholungsversuchen 

## Verketten von Aktivitäten
Wenn in einer Pipeline mehrere Aktivitäten vorliegen und die Ausgabe einer Aktivität nicht die Eingabe für eine andere Aktivität ist, können die Aktivitäten parallel ausgeführt werden, sofern Eingabedatenslices für die Aktivitäten bereitstehen.

Sie können zwei Aktivitäten verketten, indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität verwenden. Die Aktivitäten können sich in derselben Pipeline oder in verschiedenen Pipelines befinden. Die zweite Aktivität wird nur ausgeführt, wenn die erste erfolgreich abgeschlossen wurde.

Betrachten Sie beispielsweise den folgenden Fall:
 
1.	Die Pipeline P1 enthält die Aktivität A1, für die das externe Eingabedataset D1 erforderlich ist und die das **Ausgabedataset** **D2** generiert.
2.	Die Pipeline P2 enthält die Aktivität A2, für die eine **Eingabe** aus dem Dataset **D2** erforderlich ist und die das Ausgabedataset D3 generiert.
 
In diesem Szenario wird die Aktivität A1 ausgeführt, wenn die externen Daten verfügbar sind und die Häufigkeit für die geplante Verfügbarkeit erreicht ist. Die Aktivität A2 wird ausgeführt, wenn die geplanten Slices von D2 verfügbar werden und die Häufigkeit für die geplante Verfügbarkeit erreicht ist. Wenn ein Fehler in einem der Slices im Dataset D2 auftritt, wird A2 für diesen Slice nicht ausgeführt, bis er verfügbar wird.

Diagrammansicht:

![Verketten von Aktivitäten in zwei Pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Diagrammansicht mit beiden Aktivitäten in derselben Pipeline:

![Verketten von Aktivitäten in derselben Pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

## Planung und Ausführung
Sie wissen jetzt, was Pipelines und Aktivitäten sind. Sie haben sich auch angesehen, wie sie definiert werden, und sich einen allgemeinen Überblick über die Aktivitäten in Azure Data Factory verschafft. Nun wird beschrieben, wie sie ausgeführt werden.

Eine Pipeline ist nur zwischen ihrer Start- und ihrer Endzeit aktiv. Sie wird weder vor der Startzeit noch nach der Endzeit ausgeführt. Wenn die Pipeline angehalten wird, wird sie unabhängig von Start- und Endzeit nicht ausgeführt. Wenn eine Pipeline ausgeführt werden soll, darf sie nicht angehalten werden. Eigentlich ist es nicht die Pipeline, die ausgeführt wird. Es sind vielmehr die Aktivitäten in der Pipeline, die ausgeführt werden. Dies geschieht jedoch im Gesamtkontext der Pipeline.

Informationen zur Planung und Ausführung in Azure Data Factory finden Sie unter [Planung und Ausführung](data-factory-scheduling-and-execution.md).

### Parallele Verarbeitung von Slices
Legen Sie die **Parallelität** in der JSON-Definition der Aktivität auf einen höheren Wert als 1 fest, sodass zur Laufzeit mehrere Slices parallel von mehreren Instanzen der Aktivität verarbeitet werden. Dieses Feature ist bei der Verarbeitung abgeglichener Slices aus der Vergangenheit hilfreich.


## Erstellen und Verwalten einer Pipeline
Azure Data Factory bietet verschiedene Mechanismen zum Erstellen und Bereitstellen von Pipelines (die wiederum eine oder mehrere Aktivitäten enthalten).

### Verwenden des Azure-Portals

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an.
2. Wechseln Sie zu Ihrer Azure Data Factory-Instanz, in der Sie eine Pipeline erstellen möchten.
3. Klicken Sie im Fokus **Zusammenfassung** auf die Kachel **Erstellen und bereitstellen**.
 
	![Kachel "Erstellen und bereitstellen"](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. Klicken Sie auf der Befehlsleiste auf **Neue Pipeline**.

	![Schaltfläche "Neue Pipeline"](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. Das Editor-Fenster wird mit der JSON-Pipelinevorlage angezeigt.

	![Pipeline-Editor](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. Klicken Sie nach dem Erstellen der Pipeline in der Befehlsleiste auf **Bereitstellen**, um die Pipeline bereitzustellen.

	> [AZURE.NOTE] Während der Bereitstellung führt der Azure Data Factory-Dienst einige Überprüfungen durch, um einige häufig auftretende Probleme zu beheben. Falls ein Fehler vorliegt, werden die entsprechenden Informationen angezeigt. Korrigieren Sie den Fehler, und stellen Sie die erstellte Pipeline erneut bereit. Mithilfe des Editor können Sie eine Pipeline aktualisieren und löschen.

Unter [Erstellen der ersten Azure Data Factory mit dem Azure-Portal/Data Factory-Editor](data-factory-build-your-first-pipeline-using-editor.md) finden Sie eine umfassende exemplarische Vorgehensweise zum Erstellen einer Data Factory mit einer Pipeline.

### Verwenden des Visual Studio-Plug-Ins
Sie können Visual Studio verwenden, um Pipelines zu erstellen und in Azure Data Factory bereitzustellen. Unter [Erstellen der ersten Azure Data Factory mit Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) finden Sie eine umfassende exemplarische Vorgehensweise zum Erstellen einer Data Factory mit einer Pipeline.


### Verwenden von Azure PowerShell
Mit der Azure PowerShell können Sie Pipelines in Azure Data Factory erstellen. Angenommen, Sie haben die Pipeline "JSON" in einer Datei unter "c:\\DPWikisample.json" definiert. Sie können sie in Ihre Azure Data Factory-Instanz, wie im folgenden Beispiel gezeigt, hochladen.

	New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Unter [Erstellen der ersten Azure Data Factory mit Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) finden Sie eine umfassende exemplarische Vorgehensweise zum Erstellen einer Data Factory mit einer Pipeline.

### Verwenden des .NET SDK
Sie können die Pipeline auch mit dem .NET SDK erstellen und bereitstellen. Dieser Mechanismus kann zum programmgesteuerten Erstellen von Pipelines genutzt werden. Weitere Informationen finden Sie unter [Erstellen, Überwachen und Verwalten von Azure Data Factorys mithilfe des Data Factory .NET SDK](data-factory-create-data-factories-programmatically.md).


### Verwenden von Azure Resource Manager-Vorlagen
Sie können eine Pipeline mithilfe einer Azure Resource Manager-Vorlage erstellen und bereitstellen. Weitere Informationen hierzu finden Sie unter [Tutorial: Erstellen der ersten Azure Data Factory mit einer Azure Resource Manager-Vorlage](data-factory-build-your-first-pipeline-using-arm.md).

### Verwenden der REST-API
Sie können die Pipeline auch mit REST-APIs erstellen und bereitstellen. Dieser Mechanismus kann zum programmgesteuerten Erstellen von Pipelines genutzt werden. Weitere Informationen finden Sie unter [Create or Update a Pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx) (Erstellen oder Aktualisieren einer Pipeline).


## Verwalten und Überwachen  
Nachdem eine Pipeline bereitgestellt wurde, können Sie Ihre Pipelines, Slices und Ausführungen verwalten und überwachen. Weitere Informationen finden Sie hier: [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md).

## Nächste Schritte

- Erfahren Sie mehr über die [Planung und Ausführung in Azure Data Factory](data-factory-scheduling-and-execution.md).
- Informieren Sie sich über die Möglichkeiten zur [Datenverschiebung](data-factory-data-movement-activities.md) und [Datentransformation](data-factory-data-transformation-activities.md) in Azure Data Factory.
- Erfahren Sie mehr über die [Verwaltung und Überwachung in Azure Data Factory](data-factory-monitor-manage-pipelines.md).
- [Erstellen Sie Ihre erste Pipeline, und stellen Sie sie bereit](data-factory-build-your-first-pipeline.md).

<!---HONumber=AcomDC_0914_2016-->