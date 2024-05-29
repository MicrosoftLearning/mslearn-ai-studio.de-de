---
lab:
  title: 'Erstellen eines benutzerdefinierten Copilots, der Ihre eigenen Daten verwendet'
---

# Erstellen eines benutzerdefinierten Copilots, der Ihre eigenen Daten verwendet

Retrieval Augmented Generation (RAG) ist eine Methode zum Erstellen von Anwendungen, die Daten aus benutzerdefinierten Datenquellen in einen Prompt für ein generatives KI-Modell integrieren. RAG ist ein gängiges Muster für die Entwicklung von benutzerdefinierten *Copiloten* – chatbasierten Anwendungen, die ein Sprachmodell verwenden, um Eingaben zu interpretieren und entsprechende Antworten zu generieren.

In dieser Übung verwenden Sie Azure KI Studio, um benutzerdefinierte Daten in einen generativen KI-Prompt Flow zu integrieren.

> **Hinweis:** Azure KI Studio befindet sich zum Zeitpunkt des Schreibens in der Vorschau und befindet sich in der aktiven Entwicklung. Einige Elemente des Diensts sind möglicherweise nicht genau wie beschrieben, und einige Features funktionieren möglicherweise nicht wie erwartet.

Diese Übung dauert ca. **45** Minuten.

## Erstellen einer Azure KI-Suche-Ressource

Ihre Copilot-Lösung integriert benutzerdefinierte Daten in einen Prompt Flow. Um diese Integration zu unterstützen, benötigen Sie eine Azure KI Search-Ressource, mit der Ihre Daten indiziert werden sollen.

1. Öffnen Sie in einem Webbrowser das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.
1. Wählen Sie auf der Startseite **+ Ressource erstellen** aus und suchen Sie nach `Azure AI Search`. Erstellen Sie dann eine neue Azure KI Search-Ressource mit den folgenden Einstellungen:

    - **Abonnement:** *Wählen Sie Ihr Azure-Abonnement aus.*
    - **Ressourcengruppe**: *Wählen oder erstellen Sie eine Ressourcengruppe*.
    - **Dienstname**: *Geben Sie einen eindeutigen Dienstnamen ein*
    - **Speicherort:** *Treffen Sie eine **zufällige** Auswahl aus einer der folgenden Regionen*\*
        - Australien (Osten)
        - Kanada, Osten
        - East US
        - USA (Ost) 2
        - Frankreich, Mitte
        - Japan, Osten
        - USA Nord Mitte
        - Schweden, Mitte
        - Schweiz 
    - **Tarif**: Standard.

    > \* Später erstellen Sie einen Azure KI-Hub (einschließlich eines Azure OpenAI-Diensts) in derselben Region wie Ihre Azure KI-Suche-Ressource. Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die aufgeführten Regionen enthalten das Standardkontingent für die in dieser Übung verwendeten Modelltypen. Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze in Szenarien erreicht, in denen Sie einen Mandanten für andere Benutzer und Benutzerinnen freigeben. Wenn später in der Übung eine Kontingentgrenze erreicht wird, müssen Sie eventuell einen weiteren Azure KI-Hub in einer anderen Region erstellen.

1. Warten Sie, bis ihre Azure KI Search-Ressourcenbereitstellung abgeschlossen ist.

## Erstellen eines Azure KI-Projekts

Jetzt können Sie ein Azure KI Studio-Projekt und die Azure KI-Ressourcen erstellen, um es zu unterstützen.

1. Öffnen Sie in einem Webbrowser [Azure KI Studio](https://ai.azure.com) unter `https://ai.azure.com` und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.
1. Wählen Sie auf der **Startseite** von Azure AI Studio die Option **+ Neues Projekt** aus. Erstellen Sie dann im **Assistenten zum Erstellen eines neuen Projekts** ein Projekt mit den folgenden Einstellungen:

    - **Projektname:** *Ein eindeutiger Name für Ihr Projekt*
    - **Hub:** *Erstellen Sie eine neue Ressource mit den folgenden Einstellungen:*

        - **Hub-Name:** *Ein eindeutiger Name*
        - **Azure-Abonnement**: *Geben Sie Ihr Azure-Abonnement an.*
        - **Ressourcengruppe:** *Wählen Sie die Ressourcengruppe aus, die die Ressource Ihrer Azure KI-Suche enthält*
        - **Speicherort:** *Derselbe Standort wie Ihre Azure KI-Suche-Ressource*
        - **Azure OpenAI**: (Neu) *Automatisches Ausfüllen Ihres ausgewählten Hub-Namens*
        - **Azure KI Search**: *Wählen Sie Ihre Azure KI Search-Ressource aus*

1. Warten Sie, bis Ihr Projekt erstellt wurde.

## Bereitstellen von Modellen

Sie benötigen zwei Modelle, um Ihre Lösung zu implementieren:

- Ein *Einbettungsmodell* zum Vektorisieren von Textdaten für eine effiziente Indizierung und Verarbeitung.
- Ein Modell, das in natürlicher Sprache Antworten auf Fragen generieren kann, basierend auf Ihren Daten.

1. Wählen Sie in Azure KI Studio in Ihrem Projekt im Navigationsbereich auf der linken Seite unter **Komponenten** die Seite **Bereitstellungen** aus.
1. Erstellen Sie eine neue Bereitstellung (mithilfe eines **Echtzeitendpunkts**) des Modells **text-embedding-ada-002** mit den folgenden Einstellungen:

    - **Bereitstellungsname**: `text-embedding-ada-002`
    - **Modellversion**: *Standard*
    - **Erweiterte Optionen**:
        - **Inhaltsfilter**: *Standard*
        - **Ratenbegrenzung für Token pro Minute**: `5K`
1. Wiederholen Sie die vorherigen Schritte zum Bereitstellen eines **gpt-35-turbo-16k-** Modells mit dem Bereitstellungsnamen `gpt-35-turbo-16k`.

    > **Hinweis:** Durch das Verringern der Token pro Minute (TPM) wird die Überlastung des Kontingents vermieden, das in Ihrem verwendeten Abonnement verfügbar ist. 5.000 TPM reicht für die in dieser Übung verwendeten Daten aus.

## Hinzufügen von Daten zu Ihrem Projekt

Die Daten für Ihren Copilot bestehen aus einer Reihe von Reisebroschüren des fiktiven Reisebüros *Margie‘s Travel* im PDF-Format. Fügen wir sie dem Projekt hinzu.

1. Laden Sie das [gezippte Archiv der Broschüren](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) aus `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` herunter und extrahieren Sie es in einen Ordner mit dem Namen **Broschüren** in Ihrem lokalen Dateisystem.
1. Wählen Sie in Azure KI Studio im Navigationsbereich auf der linken Seite unter **Komponenten** die Seite **Daten** aus.
1. Wählen Sie **+ Neue Daten** aus.
1. Erweitern Sie im Assistenten **Hinzufügen Ihrer Daten** das Dropdownmenü, um **Dateien/Ordner hochladen** auszuwählen.
1. Wählen Sie **Ordner hochladen** und dann den Ordner **Broschüren** aus.
1. Legen Sie den Datennamen auf **Broschüren** fest.

## Erstellen eines Indexes für Ihre Daten

Nachdem Sie Ihrem Projekt nun eine Datenquelle hinzugefügt haben, können Sie sie verwenden, um einen Index in Ihrer Azure KI Search-Ressource zu erstellen.

1. Wählen Sie in Azure KI Studio im Navigationsbereich auf der linken Seite unter **Komponenten** die Seite **Indizes** aus.
1. Fügen Sie einen neuen Index mit den folgenden Einstellungen hinzu:
    - **Quelldaten**:
        - **Datenquelle**: Daten in Azure KI Studio
            - *Auswählen der Datenquelle **Broschüren***
    - **Indexeinstellungen**:
        - **Wählen Sie den Azure KI-Suche-Dienst aus:** *Wählen Sie die **AzureAISearch**-Verbindung mit Ihrer Azure KI Search-Ressource aus*
        - **Indexname**: Broschürenindex
        - **VM**: Automatisch auswählen
    - **Sucheinstellungen**:
        - **Vektoreinstellungen**: Hinzufügen der Vektorsuche zu dieser Suchressource
        - **Azure OpenAI Resource**: Default_AzureOpenAI
        - *Bestätigen Sie nach Aufforderung, dass ein Einbettungsmodell bereitgestellt wird, wenn es noch nicht vorhanden ist.*
        
1. Warten Sie, bis der Indizierungsprozess abgeschlossen ist, was mehrere Minuten dauern kann. Der Indexerstellungsvorgang besteht aus den folgenden Aufträgen:

    - Zerlegen, segmentieren und integrieren Sie die Texttoken in Ihre Broschürendaten.
    - Aktualisieren Sie Azure AI Search mit dem neuen Index.
    - Registrieren Sie die Indexressource.

## Testen des Index

Bevor Sie Ihren Index in einem RAG-basierten Prompt Flow verwenden, überprüfen wir, ob er verwendet werden kann, um generative KI-Antworten zu beeinflussen.

1. Wählen Sie im Navigationsbereich auf der linken Seite unter **Projekt-Playground** die Seite **Chat** aus.
1. Stellen Sie auf der Seite "Chat" im Bereich "Optionen" sicher, dass ihre **gpt-35-turbo-16k** Modellbereitstellung ausgewählt ist. Übermitteln Sie dann im Hauptchatsitzungsbereich die Eingabeaufforderung `Where can I stay in New York?`
1. Überprüfen Sie die Antwort, die eine generische Antwort aus dem Modell ohne Daten aus dem Index sein sollte.
1. Wählen Sie im Setupbereich die Registerkarte **Daten hinzufügen** aus, und fügen Sie dann den Projektindex **Broschürenindex** hinzu, und wählen Sie den Suchtyp **Hybrid (Vektor + Schlüsselwort)** aus.
1. Nachdem der Index hinzugefügt wurde und die Chatsitzung neu gestartet wurde, übermitteln Sie die Eingabeaufforderung `Where can I stay in New York?` erneut
1. Überprüfen Sie die Antwort, die auf Daten im Index basiert.

## Verwenden des Indexes in einem Prompt Flow

Ihr Vektorindex wurde in Ihrem Azure KI Studio-Projekt gespeichert, sodass Sie ihn einfach in einem Prompt Flow verwenden können.

1. Wählen Sie in Azure KI Studio in Ihrem Projekt im Navigationsbereich auf der linken Seite unter **Tools** die Seite **Prompt Flow** aus.
1. Erstellen Sie einen neuen Prompt Flow, indem Sie das **Multi-Round Q&A für Ihr Datenbeispiel** im Katalog klonen. Speichern Sie Ihren Klon dieses Beispiels in einem Ordner mit dem Namen `brochure-flow`.
1. Wenn die Seite mit dem Prompt Flow-Designer geöffnet wird, überprüfen Sie den **Broschüren-Flow**. Das Diagramm sollte der folgenden Abbildung ähneln:

    ![Ein Screenshot eines Prompt Flow-Diagramms](./media/chat-flow.png)

    Der von Ihnen verwendete Beispiel-Prompt Flow implementiert die Prompt-Logik für eine Chatanwendung, in der Benutzer Texteingaben iterativ an die Chatoberfläche übermitteln kann. Der Unterhaltungsverlauf wird beibehalten und in den Kontext für jede Iteration einbezogen. Der Prompt Flow orchestriert eine Reihe von *Tools* für Folgendes:

    - Fügen Sie den Verlauf an die Chateingabe an, um einen Prompt in Form einer kontextbezogenen Form einer Frage zu definieren.
    - Rufen Sie den Kontext mithilfe Ihres Indexes und eines Abfragetyps Ihrer eigenen Wahl basierend auf der Frage ab.
    - Generieren Sie den Prompt Flow-Kontext, indem Sie die abgerufenen Daten aus dem Index verwenden, um die Frage zu erweitern.
    - Erstellen Sie Prompt-Varianten, indem Sie eine Systemnachricht hinzufügen und den Chatverlauf strukturieren.
    - Senden Sie den Prompt an ein Sprachmodell, um eine natürliche Sprachantwort zu generieren.

1. Wählen Sie in der Liste **Runtime** **Start** aus, um die automatische Runtime zu starten.

    Warten Sie, bis die Runtime gestartet wurde. Dies stellt einen Berechnungskontext für den Prompt Flow bereit. Während Sie warten, überprüfen Sie auf der Registerkarte **Flow** die Abschnitte für die Tools im Flow.

1. Stellen Sie im Abschnitt **Eingaben** sicher, dass die Eingaben Folgendes umfassen:
    - **chat_history**
    - **chat_input**

    Der Standardchatverlauf in diesem Beispiel enthält einige Unterhaltungen über KI.

1. Stellen Sie im Abschnitt **Ausgabe** sicher, dass die Ausgabe Folgendes enthält:

    - **chat_output** mit `${chat_with_context.output}`-Wert

1. Wählen Sie im Abschnitt **modify_query_with_history** die folgenden Einstellungen aus (lassen Sie andere wie sie sind):

    - **Verbindung**: `Default_AzureOpenAI`
    - **API**: `chat`
    - **deployment_name**: `gpt-35-turbo-16k`
    - **response_format**: `{"type":"text"}`

1. Legen Sie im Abschnitt **Lookup** die folgenden Parameterwerte fest:

    - **mlindex_content**: *Wählen Sie das leere Feld aus, um den Bereich „Generieren“ zu öffnen*
        - **index_type**: Registrierter Index
        - **mlindex_asset_id**: brochures-index:1
    - **Abfragen**: `${modify_query_with_history.output}`
    - **query_type**: `Hybrid (vector + keyword)`
    - **top_k**: 2

1. Überprüfen Sie im Abschnitt **generate_prompt_context** das Python-Skript und stellen Sie sicher, dass die **Eingaben** für dieses Tool den folgenden Parameter enthalten:

    - **search_result** *(object)*: ${lookup.output}

1. Überprüfen Sie im Abschnitt**Prompt_variants** das Python-Skript und stellen Sie sicher, dass die **Eingaben** für dieses Tool die folgenden Parameter enthalten:

    - **Kontexte***(Zeichenfolge)*: ${generate_prompt_context.output}
    - **chat_history** *(Zeichenfolge)*: ${inputs.chat_history}
    - **chat_input** *(Zeichenfolge)*: ${inputs.chat_input}

1. Wählen Sie im Abschnitt **chat_with_context** die folgenden Einstellungen aus (lassen Sie andere wie sie sind):

    - **Verbindung**: Default_AzureOpenAI
    - **Api**: Chat
    - **deployment_name**: gpt-35-turbo-16k
    - **response_format**: {"type":"text"}

    Stellen Sie dann sicher, dass die **Eingaben** für dieses Tool die folgenden Parameter enthalten:
    - **prompt_text** *(Zeichenfolge)*: ${Prompt_variants.output}

1. Verwenden Sie auf der Symbolleiste die Schaltfläche **Speichern**, um die Änderungen zu speichern, die Sie an den Tools im Prompt Flow vorgenommen haben.
1. Wählen Sie auf der Symbolleiste **Chat** aus. Ein Chatbereich mit dem Beispielunterhaltungsverlauf und der Eingabe, die bereits basierend auf den Beispielwerten ausgefüllt wurde, wird geöffnet. Sie können diese Fehler ignorieren.
1. Ersetzen Sie im Chatbereich die Standardeingabe durch die Frage `Where can I stay in London?` und übermitteln Sie sie.
1. Überprüfen Sie die Antwort, die auf Daten im Index basiert.
1. Überprüfen Sie die Ausgaben für jedes Tool im Flow.
1. Geben Sie im Chatbereich die Frage `What can I do there?` ein.
1. Überprüfen Sie die Antwort, die auf Daten im Index basieren sollte, und berücksichtigen Sie den Chatverlauf (so dass „dort“ als „in London“ verstanden wird).
1. Überprüfen Sie die Ausgaben für jedes Tool im Flow und notieren Sie, wie jedes Tool im Flow mithilfe seiner Eingaben ausgeführt wird, um einen kontextbezogenen Prompt vorzubereiten und eine entsprechende Antwort zu erhalten.

## Bereitstellen des Flows

Nachdem Sie nun über einen funktionierenden Flow verfügen, der Ihre indizierten Daten verwendet, können Sie ihn als Dienst bereitstellen, der von einer Copilot-Anwendung genutzt werden soll.

> **Hinweis:** Je nach Region und Rechenzentrumslast können Bereitstellungen manchmal eine Weile dauern. Sie können sich auf den nachstehenden Abschnitt "Herausforderung" setzen, während sie die Bereitstellungen bereitstellt oder die Tests Ihrer Bereitstellung überspringt, wenn Sie nur wenig Zeit haben.

1. Wählen Sie auf der Symbolleiste **Bereitstellen** aus.
1. Erstellen Sie eine Bereitstellung mit den folgenden Einstellungen:
    - **Grundeinstellungen**:
        - **Endpunkt**: Neu
        - **Endpunkt Name**: `brochure-endpoint`
        - **Bereitstellungsname**: Broschüren-Endpunkt-1
        - **VM**: Standard_DS3_v2
        - **Instanzenanzahl:** 3
        - **Rückschließen der Datensammlung**: Ausgewählt
    - **Erweiterte Einstellungen**:
        - *Verwenden der Standardeinstellungen*
1. Wählen Sie in Azure KI Studio im Navigationsbereich auf der linken Seite unter **Komponenten** die Seite **Bereitstellungen** aus.
1. Aktualisieren Sie die Ansicht, bis die **brochure-endpoint-1**-Bereitstellung als *erfolgreich* unter dem **Broschüren-Endpunkt**-Endpunkt angezeigt wird (dies kann lange dauern).
1. Wenn die Bereitstellung erfolgreich war, wählen Sie sie aus. Geben Sie dann auf der Seite **Test** den Prompt `What is there to do in San Francisco?` ein und überprüfen Sie die Antwort.
1. Geben Sie den Prompt `Where else could I go?` ein und überprüfen Sie die Antwort.
1. Zeigen Sie die Seite **Nutzen** für den Endpunkt an und beachten Sie, dass sie Verbindungsinformationen und Beispielcode enthält, die Sie zum Erstellen einer Clientanwendung für Ihren Endpunkt verwenden können, sodass Sie die Prompt Flow-Lösung als benutzerdefinierten Copilot in eine Anwendung integrieren können.

## Herausforderung 

Sie haben erfahren, wie Sie Ihre eigenen Daten in einen Copilot integrieren können, der mit Azure KI Studio erstellt wurde. Lassen Sie uns dieses Thema nun weiter erkunden!

Versuchen Sie, über Azure KI Studio eine neue Datenquelle hinzuzufügen, zu indizieren und die indizierten Daten in einen Prompt flow zu integrieren. Einige Datasets, die Sie ausprobieren könnten, sind zum Beispiel:

- Eine Sammlung von Artikeln (z. B. Forschungsartikel), die sich auf Ihrem Computer befinden
- Eine Reihe von Präsentationen aus früheren Konferenzen
- Jedes beliebige Dataset, das Repository [Azure Search Sample Data](https://github.com/Azure-Samples/azure-search-sample-data) verfügbar ist.

Seien Sie dabei möglichst kreativ, um Ihre Datenquelle zu erstellen und in Ihren Prompt flow zu integrieren. Probieren Sie den Prompt flow aus, und geben Sie dabei Prompts ein, die nur mithilfe des von Ihnen ausgewählten Datasets beantwortet werden können!

## Bereinigung

Um unnötige Azure-Kosten und Ressourcenauslastung zu vermeiden, sollten Sie die in dieser Übung bereitgestellten Ressourcen entfernen.

1. Zeigen Sie in Azure KI Studio die Seite **Build** an. Wählen Sie dann das Projekt aus, das Sie in dieser Übung erstellt haben, und verwenden Sie die Schaltfläche **Projekt löschen**, um es zu entfernen. Es kann einige Minuten dauern, bis alle Komponenten gelöscht sind.
1. Wenn Sie mit der Erkundung von Azure KI Studio fertig sind, kehren Sie bei Bedarf zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` zurück, und melden Sie sich bei Bedarf mit Ihren Azure-Anmeldeinformationen an. Löschen Sie dann die Ressourcengruppe, die Sie für Ihre Azure KI Search- und Azure KI-Ressourcen erstellt haben.
