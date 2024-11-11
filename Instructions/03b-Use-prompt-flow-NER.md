---
lab:
  title: Verwenden von Prompt Flow für Named Entity Recognition (NER) in Azure KI Studio
---

# Verwenden von Prompt Flow für Named Entity Recognition (NER) in Azure KI Studio

Das Extrahieren wertvoller Informationen aus Text wird als Erkennung benannter Entitäten (Named Entity Recognition, NER) bezeichnet. Entitäten sind Schlüsselwörter, die für Sie in einem bestimmten Text von Interesse sind.

![Extraktion von Entitäten](./media/get-started-prompt-flow-use-case.gif)

Große Sprachmodelle (Large Language Models, LLMs) können zum Ausführen von NER verwendet werden. Zum Erstellen einer Anwendung, die Text als Eingabe- und Ausgabeentitäten verwendet, können Sie einen Flow erstellen, der einen LLM-Knoten mit Prompt Flow nutzt.

In dieser Übung verwenden Sie Prompt Flow von Azure KI Studio, um eine LLM-Anwendung zu erstellen, die einen Entitätstyp und Text als Eingabe erwartet. Sie ruft ein GPT-Modell von Azure OpenAI über einen LLM-Knoten auf, um die erforderliche Entität aus dem angegebenen Text zu extrahieren, bereinigt das Ergebnis und gibt die extrahierten Entitäten aus.

![Übersicht über die Übung](./media/get-started-lab.png)

Sie müssen zuerst ein Projekt in Azure KI Studio erstellen, um die erforderlichen Azure-Ressourcen zu erstellen. Anschließend können Sie ein GPT-Modell mit dem Azure OpenAI-Dienst bereitstellen. Sobald Sie über die erforderlichen Ressourcen verfügen, können Sie den Flow erstellen. Abschließend führen Sie den Flow aus, um ihn zu testen und die Beispielausgabe anzuzeigen.

## Erstellen eines Projekts in Azure KI Studio

Sie erstellen zunächst ein Azure KI Studio-Projekt und einen Azure KI-Hub für seine Unterstützung.

1. Öffnen Sie in einem Webbrowser [https://ai.azure.com](https://ai.azure.com), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.
1. Wählen Sie die **Startseite** und dann **+ Neues Projekt** aus.
1. Erstellen Sie im **Assistenten zum Erstellen eines neuen Projekts** ein Projekt mit den folgenden Einstellungen:
    - **Projektname:** *Ein eindeutiger Name für Ihr Projekt*
    - **Hub:** *Erstellen Sie einen neuen Hub mit den folgenden Einstellungen:*
    - **Hub-Name:** *Ein eindeutiger Name*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe:** *Neue Ressourcengruppe*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **gpt-35-turbo** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*
    - **Verbinden von Azure KI Services oder Azure OpenAI**: *Erstellen einer neuen Verbindung*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die in der Standorthilfe aufgelisteten Regionen enthalten Standardquoten für den/die in dieser Übung verwendeten Modelltyp(en). Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze erreicht. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen. Erfahren Sie mehr über die [Modellverfügbarkeit pro Region](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Überprüfen Sie Ihre Konfiguration, und erstellen Sie Ihr Projekt.
1. Warten Sie, bis Ihr Projekt erstellt wurde.

## Bereitstellen eines GPT-Modells

Um ein LLM-Modell in Prompt Flow zu verwenden, müssen Sie zuerst ein Modell bereitstellen. Mit Azure KI Studio können Sie OpenAI-Modelle bereitstellen, die Sie in Ihren Flows verwenden können.

1. Wählen Sie im Navigationsbereich auf der linken Seite unter **Komponenten** die Seite **Bereitstellungen** aus.
1. Erstellen Sie eine neue Bereitstellung des **gpt-35-Turbo**-Modells mit den folgenden Einstellungen:
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **KI-Ressource**: *Wählen Sie die zuvor erstellte Quelle* aus
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: StandardV2 
    - **Dynamische Quote aktivieren**: Deaktiviert
   
Nachdem Sie ihr LLM-Modell bereitgestellt haben, können Sie einen Flow in Azure KI Studio erstellen, der das bereitgestellte Modell aufruft.

## Erstellen und Ausführen eines Flows in Azure KI Studio

Sie haben alle erforderlichen Ressourcen bereitgestellt und können nun einen Flow erstellen.

### Einen neuen Flow erstellen

Zum Erstellen eines neuen Flows mit einer Vorlage können Sie einen der Flowtypen auswählen, die Sie entwickeln möchten.

1. Wählen Sie im Navigationsbereich auf der linken Seite unter **Tools** die Option **Prompt Flow** aus.
1. Wählen Sie **+ Erstellen** aus, um einen neuen Flow zu erstellen.
1. Erstellen Sie einen neuen **Standardflow**, und geben Sie `entity-recognition` als Ordnernamen ein.

<details>  
    <summary><b>Tip zur Problembehandlung</b>: Berechtigungsfehler</summary>
    <p>Wenn beim Erstellen eines neuen Eingabeaufforderungsflusses ein Berechtigungsfehler angezeigt wird, versuchen Sie Folgendes zur Problembehandlung:</p>
    <ul>
        <li>Wählen Sie im Ressourcenmenü des Azure-Portals AI Dienste aus.</li>
        <li>Bestätigen Sie auf der IAM-Seite auf der Registerkarte Identität, dass es sich um eine systemzugewiesene verwaltete Identität handelt.</li>
        <li>Navigieren Sie zum dazugehörigen Speicherkonto. Fügen Sie auf der IAM-Seite die Rollenzuweisung <em>Storage blob data reader</em> hinzu.</li>
        <li>Wählen Sie unter <strong>Zugriff zuweisen zu</strong> die Option <strong>Verwaltete Identität</strong>, <strong>+ Mitglieder auswählen</strong>, und wählen Sie die Option <strong>Alle vom System zugewiesenen verwalteten Identitäten</strong>.</li>
        <li>Überprüfen und zuweisen, um die neuen Einstellungen zu speichern, und wiederholen Sie den vorherigen Schritt.</li>
    </ul>
</details>

Ein Standardflow mit einer Eingabe, zwei Knoten und einer Ausgabe wird für Sie erstellt. Sie aktualisieren den Flow, um zwei Eingaben zu übernehmen, Entitäten zu extrahieren, die Ausgabe vom LLM-Knoten zu bereinigen und die Entitäten als Ausgabe zurückzugeben.

### Starten der automatischen Runtime

Zum Testen des Flows benötigen Sie eine Computeressource. Die erforderliche Computeressource wird Ihnen über die Runtime zur Verfügung gestellt.

1. Nach dem Erstellen des neuen Flows, den Sie `entity-recognition` genannt haben, sollte der Flow in Studio geöffnet werden.
1. Wählen Sie **Computesitzung starten** in der oberen Leiste aus.
1. Es dauert 1 bis 3 Minuten, bis die Computesitzung gestartet wird.

### Konfigurieren der Eingaben

Der von Ihnen erstellte Flow verwendet zwei Eingaben: einen Text und den Typ der Entität, die Sie aus dem Text extrahieren möchten.

1. Unter **Eingaben** wird eine Eingabe mit dem Namen `topic` des Typs `string` konfiguriert. Ändern Sie die vorhandene Eingabe, und aktualisieren Sie sie mit den folgenden Einstellungen:
    - **Name**: `entity_type`
    - **Typ:**`string`
    - **Wert**: `job title`
1. Wählen Sie **Eingabe hinzufügen** aus.
1. Konfigurieren Sie die zweite Eingabe so, dass sie die folgenden Einstellungen hat:
    - **Name**: `text`
    - **Typ:**`string`
    - **Wert**: `The software engineer is working on a new update for the application.`

### Konfigurieren des LLM-Knotens

Der Standardflow enthält bereits einen Knoten, der das LLM-Tool verwendet. Sie finden den Knoten in Ihrer Flowübersicht. Der Standardprompt fragt nach einem Witz. Sie aktualisieren den LLM-Knoten, um Entitäten basierend auf den beiden im vorherigen Abschnitt angegebenen Eingaben zu extrahieren.

1. Navigieren Sie zum **LLM-Knoten** mit dem Namen `joke`.
1. Ersetzen sie den Namen durch `NER_LLM`.
1. Wählen Sie unter **Verbindung** die Verbindung `Default_AzureOpenAI` aus.
1. Wählen Sie unter **deployment_name** das von Ihnen bereitgestellte Modell `gpt-35-turbo` aus.
1. Ersetzen Sie das Feld „Prompt“ durch den folgenden Code:

   ```yml
   {% raw %}
   system:

   Your task is to find entities of a certain type from the given text content.
   If there're multiple entities, please return them all with comma separated, e.g. "entity1, entity2, entity3".
   You should only return the entity list, nothing else.
   If there's no such entity, please return "None".

   user:
   
   Entity type: {{entity_type}}
   Text content: {{text}}
   Entities:
   {% endraw %}
   ```

1. Wählen Sie **Eingabe überprüfen und parsen** aus.
1. Konfigurieren Sie im LLM-Knoten im Abschnitt **Eingaben** Folgendes:
    - Wählen Sie für `entity_type` den Wert `${inputs.entity_type}` aus.
    - Wählen Sie für `text` den Wert `${inputs.text}` aus.

Ihr LLM-Knoten verwendet nun den Entitätstyp und den Text als Eingaben, fügt ihn in den von Ihnen angegebenen Prompt ein und senden die Anforderung an Ihr bereitgestelltes Modell.

### Konfigurieren des Python-Knotens

Um nur die wichtigsten Informationen aus dem Ergebnis des Modells zu extrahieren, können Sie das Python-Tool verwenden, um die Ausgabe des LLM-Knotens zu bereinigen.

1. Navigieren Sie zum Python-Knoten mit dem Namen `echo`.
1. Ersetzen sie den Namen durch `cleansing`.
1. Ersetzen Sie den -Code durch Folgendes:

   ```python
   from typing import List
   from promptflow import tool
    
    
   @tool
   def cleansing(entities_str: str) -> List[str]:
       # Split, remove leading and trailing spaces/tabs/dots
       parts = entities_str.split(",")
       cleaned_parts = [part.strip(" \t.\"") for part in parts]
       entities = [part for part in cleaned_parts if len(part) > 0]
       return entities
    
   ```

1. Wählen Sie **Eingabe überprüfen und parsen** aus.
1. Legen Sie im Python-Knoten im Abschnitt **Eingaben** den Wert `entities_str` auf `${NER_LLM.output}` fest.

### Konfigurieren der Ausgabe

Schließlich können Sie die Ausgabe des gesamten Flows konfigurieren. Sie möchten nur eine Ausgabe für den Flow, bei der es sich um die extrahierten Entitäten handeln soll.

1. Navigieren Sie zu den **Ausgaben** des Flows.
1. Geben Sie unter **Name**`entities` ein.
1. Wählen Sie für **Wert** die Option `${cleansing.output}` aus.

### Den Flow ausführen

Nachdem Sie den Flow entwickelt haben, können Sie ihn ausführen, um ihn zu testen. Da Sie den Eingaben Standardwerte hinzugefügt haben, können Sie den Flow ganz einfach in Studio testen.

1. Wählen Sie **Ausführen** aus, um den Flow zu testen.
1. Warten Sie, bis die Ausführung abgeschlossen wurde.
1. Wählen Sie **Ausgaben anzeigen** aus. Ein Popup sollte angezeigt werden, in dem die Ausgabe für die Standardeingaben angezeigt wird. Optional können Sie auch die Protokolle überprüfen.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure KI Studio fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
