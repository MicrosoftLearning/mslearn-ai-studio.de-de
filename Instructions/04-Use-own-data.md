---
lab:
  title: 'Erstellen einer generativen KI-App, die Ihre eigenen Daten verwendet'
  description: 'Erfahren Sie, wie Sie mithilfe des RAG-Modells (Retrieval Augmented Generation) eine Chat-App erstellen, die Aufforderungen zur Verwendung Ihrer eigenen Daten anfordert.'
---

# Erstellen einer generativen KI-App, die Ihre eigenen Daten verwendet

Retrieval Augmented Generation (RAG) ist eine Methode zum Erstellen von Anwendungen, die Daten aus benutzerdefinierten Datenquellen in einen Prompt für ein generatives KI-Modell integrieren. RAG ist ein häufig verwendetes Muster für die Entwicklung generativer KI-Apps – chatbasierte Anwendungen, die ein Sprachmodell verwenden, um Eingaben zu interpretieren und entsprechende Antworten zu generieren.

In dieser Übung verwenden Sie das Azure KI Foundry Portal, um benutzerdefinierte Daten in einen generativen KI Prompt Flow zu integrieren. Außerdem erfahren Sie, wie Sie das RAG-Muster in einer Client-App mithilfe der Azure AI Foundry- und Azure OpenAI-SDKs implementieren.

Diese Übung dauert ca. **45** Minuten.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit der Erstellung eines Azure AI Foundry-Projekts und der erforderlichen Dienstressourcen, die es benötigt, mithilfe Ihrer eigenen Daten – einschließlich einer Azure KI-Suche-Ressource.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im **Assistenten zum Erstellen eines Projekts** einen geeigneten Projektnamen für (z. B. `my-ai-project`) ein und überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Ein eindeutiger Name – z. B. `my-ai-hub`.*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine neue Ressourcengruppe mit einem eindeutigen Namen (z.B. `my-ai-resources`), oder wählen Sie eine bestehende aus.*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** und wählen Sie dann sowohl **gpt-4** als auch **text-embedding-ada-002** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*
    - **Verbinden von Azure KI Services oder Azure OpenAI**: *Erstellen Sie eine neue KI Services-Ressource mit einem geeigneten Namen (z.B. `my-ai-services`) oder verwenden Sie eine vorhandene.*
    - **Verbinden von Azure KI-Suche**: *Erstellen Sie eine neue Ressource von Azure KI-Suche mit einem eindeutigen Namen*

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Wenn Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die **Übersichtsseite** des Projekts im Azure AI Foundry-Portal, die in etwa wie folgt aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)
   
## Bereitstellen von Modellen

Sie benötigen zwei Modelle, um Ihre Lösung zu implementieren:

- Ein *Einbettungsmodell* zum Vektorisieren von Textdaten für eine effiziente Indizierung und Verarbeitung.
- Ein Modell, das in natürlicher Sprache Antworten auf Fragen generieren kann, basierend auf Ihren Daten.

1. Wählen Sie im Azure KI Foundry-Portal in Ihrem Projekt im Navigationsbereich links unter **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Erstellen Sie eine neue Bereitstellung des Modells **text-embedding-ada-002** mit den folgenden Einstellungen, indem Sie **Anpassen** im Assistenten zum Bereitstellen des Modells wählen:

    - **Bereitstellungsname**: `text-embedding-ada-002`
    - **Bereitstellungstyp**: Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **KI-Ressource**: *Wählen Sie die zuvor erstellte Quelle* aus
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: StandardV2 
    - **Dynamische Quote aktivieren**: Deaktiviert

    > **Hinweis**: Wenn an Ihrem aktuellen Speicherort für KI-Ressourcen kein Kontingent für das Modell, das Sie bereitstellen möchten, verfügbar ist, werden Sie aufgefordert, einen anderen Speicherort zu wählen, an dem eine neue KI-Ressource erstellt und mit Ihrem Projekt verbunden wird.

1. Wiederholen Sie die vorherigen Schritte, um ein **gpt-4**-Modell mit dem Bereitstellungsnamen `gpt-4` bereitzustellen.

    > **Hinweis:** Durch das Verringern der Token pro Minute (TPM) wird die Überlastung des Kontingents vermieden, das in Ihrem verwendeten Abonnement verfügbar ist. 5.000 TPM reicht für die in dieser Übung verwendeten Daten aus.

## Hinzufügen von Daten zu Ihrem Projekt

Die Daten für Ihren Copilot bestehen aus einer Reihe von Reisebroschüren des fiktiven Reisebüros *Margie‘s Travel* im PDF-Format. Fügen wir sie dem Projekt hinzu.

1. Laden Sie das [gezippte Archiv der Broschüren](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) aus `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` herunter und extrahieren Sie es in einen Ordner mit dem Namen **Broschüren** in Ihrem lokalen Dateisystem.
1. Wählen Sie im Azure KI Foundry-Portal in Ihrem Projekt im Navigationsbereich auf der linken Seite unter **Meine Assets** die Seite **Daten + Indizes**.
1. Wählen Sie **+ Neue Daten** aus.
1. Erweitern Sie im Assistenten **Hinzufügen Ihrer Daten** das Dropdownmenü, um **Dateien/Ordner hochladen** auszuwählen.
1. Wählen Sie **Ordner hochladen** und dann den Ordner **Broschüren** aus.
1. Wählen Sie **Weiter** und setzen Sie den Dateinamen auf `brochures`.
1. Warten Sie, bis der Ordner hochgeladen wurde, und beachten Sie, dass er mehrere .pdf-Dateien enthält.

## Erstellen eines Indexes für Ihre Daten

Nachdem Sie Ihrem Projekt nun eine Datenquelle hinzugefügt haben, können Sie sie verwenden, um einen Index in Ihrer Azure KI Search-Ressource zu erstellen.

1. Wählen Sie im Azure KI Foundry-Portal in Ihrem Projekt im Navigationsbereich auf der linken Seite unter **Meine Assets** die Seite **Daten + Indizes**.
1. Auf der Registerkarte **Indizes** fügen Sie einen neuen Index mit den folgenden Einstellungen hinzu:
    - **Quellstandort**:
        - **Datenquelle**: Daten im Azure KI Foundry-Portal
            - *Auswählen der Datenquelle **Broschüren***
    - **Indexkonfiguration**:
        - **Wählen Sie den Azure KI-Suche-Dienst aus:** *Wählen Sie die **AzureAISearch**-Verbindung mit Ihrer Azure KI Search-Ressource aus*
        - **Vektorindex**: `brochures-index`
        - **VM**: Automatisch auswählen
    - **Sucheinstellungen**:
        - **Vektoreinstellungen**: Hinzufügen der Vektorsuche zu dieser Suchressource
        - **Azure OpenAI-Verbindung**: *Wählen Sie die standardmäßige Azure OpenAI-Ressource für Ihren Hub aus.*

1. Warten Sie, bis der Indizierungsprozess abgeschlossen ist, was mehrere Minuten dauern kann. Der Indexerstellungsvorgang besteht aus den folgenden Aufträgen:

    - Zerlegen, segmentieren und integrieren Sie die Texttoken in Ihre Broschürendaten.
    - Erstellen Sie den Azure KI-Suchindex.
    - Registrieren Sie die Indexressource.

## Testen des Index

Bevor Sie Ihren Index in einem RAG-basierten Prompt Flow verwenden, überprüfen wir, ob er verwendet werden kann, um generative KI-Antworten zu beeinflussen.

1. Wählen Sie im Navigationsbereich auf der linken Seite die Seite **Spielplätze**.
1. Stellen Sie auf der Seite „Chat“ im Bereich „Setup“ sicher, dass Ihre **gpt-4**-Modelimplementierung ausgewählt ist. Übermitteln Sie dann im Hauptchatsitzungsbereich die Eingabeaufforderung `Where can I stay in New York?`
1. Überprüfen Sie die Antwort, die eine generische Antwort aus dem Modell ohne Daten aus dem Index sein sollte.
1. Erweitern Sie im Bereich „Einrichtung“ das Feld **Ihre Daten hinzufügen** und fügen Sie dann den Projektindex **Broschüren-Index** hinzu und wählen Sie den Suchtyp **hybrid (Vektor + Schlüsselwort)** aus.

   > **Hinweis**: Einige Benutzende stellen fest, dass neu erstellte Indizes nicht sofort verfügbar sind. Das Aktualisieren des Browsers hilft in der Regel, aber wenn das Problem weiterhin auftritt, bei dem der Index nicht gefunden werden kann, müssen Sie möglicherweise warten, bis der Index erkannt wird.

1. Nachdem der Index hinzugefügt wurde und die Chatsitzung neu gestartet wurde, übermitteln Sie die Eingabeaufforderung `Where can I stay in New York?` erneut
1. Überprüfen Sie die Antwort, die auf Daten im Index basiert.

## Verwenden des Indexes in einem Prompt Flow

Ihr Vektorindex wurde in Ihrem Azure KI Foundry-Projekt gespeichert, sodass Sie ihn problemlos in einem Prompt Flow verwenden können.

1. Wählen Sie im Azure KI Foundry-Portal in Ihrem Projekt im Navigationsbereich links unter **Erstellen und Anpassen** die Seite **Prompt flow**.
1. Erstellen Sie einen neuen Prompt Flow, indem Sie das **Multi-Round Q&A für Ihr Datenbeispiel** im Katalog klonen. Speichern Sie Ihren Klon dieses Beispiels in einem Ordner mit dem Namen `brochure-flow`.

    <details> 
        <summary><font color="red"><b>Tipp zur Fehlerbehebung</b>: Berechtigungsfehler</font></summary>
        <p>Wenn beim Erstellen eines neuen Eingabeaufforderungsflusses ein Berechtigungsfehler angezeigt wird, versuchen Sie Folgendes zur Problembehandlung:</p>
        <ul>
            <li>Wählen Sie im Azure-Portal in der Ressourcengruppe für Ihren Azure AI Foundry-Hub die KI Services-Ressource aus.</li>
            <li>Stellen Sie unter <b>Ressourcenverwaltung</b> auf der Registerkarte <b>Identität</b> sicher, dass der Status der verwalteten Identität <b>System zugewiesen</b> auf <b>Ein</b> gesetzt ist.</li>
            <li>Wählen Sie in der Ressourcengruppe für Ihren Azure AI Foundry-Hub das Speicherkonto aus</li>
            <li>Fügen Sie auf der Seite <b>Zugriffssteuerung (IAM)</b> eine Rollenzuweisung hinzu, um der verwalteten Identität für Ihre Azure KI Services-Ressource die Rolle <b>Leser von Speicherblobdaten</b> zuzuweisen.</li>
            <li>Warten Sie, bis die Rolle zugewiesen wurde, und wiederholen Sie dann den vorherigen Schritt.</li>
        </ul>
    </details>

1. Wenn die Seite mit dem Prompt Flow-Designer geöffnet wird, überprüfen Sie den **Broschüren-Flow**. Das Diagramm sollte der folgenden Abbildung ähneln:

    ![Ein Screenshot eines Prompt Flow-Diagramms.](./media/chat-flow.png)

    Der von Ihnen verwendete Beispiel-Prompt Flow implementiert die Prompt-Logik für eine Chatanwendung, in der Benutzer Texteingaben iterativ an die Chatoberfläche übermitteln kann. Der Unterhaltungsverlauf wird beibehalten und in den Kontext für jede Iteration einbezogen. Der Prompt Flow orchestriert eine Reihe von *Tools* für Folgendes:

    - Fügen Sie den Verlauf an die Chateingabe an, um einen Prompt in Form einer kontextbezogenen Form einer Frage zu definieren.
    - Rufen Sie den Kontext mithilfe Ihres Indexes und eines Abfragetyps Ihrer eigenen Wahl basierend auf der Frage ab.
    - Generieren Sie den Prompt Flow-Kontext, indem Sie die abgerufenen Daten aus dem Index verwenden, um die Frage zu erweitern.
    - Erstellen Sie Prompt-Varianten, indem Sie eine Systemnachricht hinzufügen und den Chatverlauf strukturieren.
    - Senden Sie den Prompt an ein Sprachmodell, um eine natürliche Sprachantwort zu generieren.

1. Verwenden Sie die Schaltfläche **Computesitzung starten**, um Runtime Compute für den Flow zu starten.

    Warten Sie, bis die Computesitzung gestartet wird. Dies stellt einen Berechnungskontext für den Prompt Flow bereit. Während Sie warten, überprüfen Sie auf der Registerkarte **Flow** die Abschnitte für die Tools im Flow.

    >**Hinweis**: Aufgrund von Infrastruktur- und Kapazitätsbeschränkungen kann es vorkommen, dass die Computesitzung in Zeiten hoher Nachfrage nicht gestartet werden kann. In diesem Fall können Sie den Prompt Flow überspringen und die Aufgabe **Erstellen einer RAG-Client-App mit den Azure AI Foundry- und Azure OpenAI-SDKs** starten.

    Dann, wenn die Computesitzung gestartet wurde …

1. Stellen Sie im Abschnitt **Eingaben** sicher, dass die Eingaben Folgendes umfassen:
    - **chat_history**
    - **chat_input**

    Der Standardchatverlauf in diesem Beispiel enthält einige Unterhaltungen über KI.

1. Stellen Sie im Abschnitt **Ausgabe** sicher, dass die Ausgabe Folgendes enthält:

    - **chat_output** mit dem Wert ${chat_with_context.output}

1. Wählen Sie im Abschnitt **modify_query_with_history** die folgenden Einstellungen aus (lassen Sie andere wie sie sind):

    - **Verbindung**: *Die standardmäßige Azure OpenAI-Ressource für Ihren KI-Hub*
    - **API**: Chat
    - **deployment_name**: gpt-4
    - **response_format**: {"type":"text"}

1. Legen Sie im Abschnitt **Lookup** die folgenden Parameterwerte fest:

    - **mlindex_content**: *Wählen Sie das leere Feld aus, um den Bereich „Generieren“ zu öffnen*
        - **index_type**: Registrierter Index
        - **mlindex_asset_id**: brochures-index:1
    - **Abfragen**: ${modify_query_with_history.output}
    - **query_type**: Hybrid (Vektor + Schlüsselwort)
    - **top_k**: 2

1. Überprüfen Sie im Abschnitt **generate_prompt_context** das Python-Skript und stellen Sie sicher, dass die **Eingaben** für dieses Tool den folgenden Parameter enthalten:

    - **search_result** *(object)*: ${lookup.output}

1. Überprüfen Sie im Abschnitt**Prompt_variants** das Python-Skript und stellen Sie sicher, dass die **Eingaben** für dieses Tool die folgenden Parameter enthalten:

    - **Kontexte***(Zeichenfolge)*: ${generate_prompt_context.output}
    - **chat_history** *(Zeichenfolge)*: ${inputs.chat_history}
    - **chat_input** *(Zeichenfolge)*: ${inputs.chat_input}

1. Wählen Sie im Abschnitt **chat_with_context** die folgenden Einstellungen aus (lassen Sie andere wie sie sind):

    - **Verbindung**: *Die standardmäßige Azure OpenAI-Ressource für Ihren KI-Hub*
    - **Api**: Chat
    - **deployment_name**: gpt-4
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

    Sie haben jetzt einen funktionierenden Prompt Flow, der Ihren Azure KI-Suche-Index verwendet, um das RAG-Muster zu implementieren. Weitere Informationen zum Bereitstellen und Nutzen Ihres Prompt Flows finden Sie in der [Azure AI Foundry-Dokumentation](https://learn.microsoft.com/azure/ai-foundry/how-to/flow-deploy).

## Erstellen einer RAG-Client-App mit Azure AI Foundry- und Azure OpenAI-SDKs

Während ein Prompt Flow eine großartige Möglichkeit ist, Ihr Modell und Ihre Daten in einer RAG-basierten Anwendung zu kapseln, können Sie auch die Azure AI Foundry- und Azure OpenAI-SDKs verwenden, um das RAG-Muster in einer Client-Anwendung zu implementieren. Sehen wir uns den Code an, um dies in einem einfachen Beispiel zu erreichen.

> **Tipp**: Sie können wählen, ob Sie Ihre RAG-Lösung mit Python oder Microsoft C# entwickeln. Folgen Sie den Anweisungen im entsprechenden Abschnitt für Ihre ausgewählte Sprache.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.
1. Beachten Sie im Bereich **Projektdetails** die **Projektverbindungszeichenfolge**. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit Ihrem Projekt in einer Clientanwendung herzustellen.
1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.
1. Verwenden Sie die Taste **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    > **Tipp**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers einnehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Geben Sie im PowerShell-Bereich die folgenden Befehle ein, um das GitHub-Repository für diese Übung zu klonen:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

> **Hinweis**: Führen Sie die Schritte für die gewählte Programmiersprache aus.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Chatanwendung enthält:  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die Bibliotheken zu installieren, die Sie verwenden möchten:

    **Python**

    ```
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
    ```
    

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Ersetzen Sie in der Codedatei die folgenden Platzhalter: 
    - **your_project_endpoint**: Ersetzen Sie diesen Wert durch die Verbindungszeichenfolge für Ihr Projekt (kopiert von der Seite **Overview** des Projekts im Azure AI Foundry-Portal).
    - **your_model_deployment**: Ersetzen Sie dies durch den Namen, den Sie Ihrer Modellbereitstellung gegeben haben (der `gpt-4` lauten sollte).
    - **your_index**: Ersetzen Sie dies durch Ihren Indexnamen (der `brochures-index` lauten sollte).
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

### Erkunden von Code zur Implementierung des RAG-Musters

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Überprüfen Sie den Code in der Datei, und notieren Sie Folgendes:
    - Verwendet das Azure AI Foundry-SDK, um sich mit Ihrem Projekt zu verbinden (mithilfe der Projektverbindungszeichenfolge).
    - Ruft die Standardverbindung für Azure KI-Suche aus Ihrem Projekt ab, damit der Endpunkt und der Schlüssel für Ihren Azure KI-Suche-Dienst bestimmt werden können.
    - Erstellt einen authentifizierten Azure OpenAI-Client, basierend auf der standardmäßigen Azure OpenAI Service-Verbindung in Ihrem Projekt.
    - Sendet einen Prompt (einschließlich einer System- und Benutzendenachricht) an den Azure OpenAI-Client und fügt zusätzliche Informationen über den Azure KI-Suche-Index hinzu, der zur Begründung des Prompts verwendet werden soll.
    - Zeigt die Antwort des fundierten Prompts an.
1. Verwenden Sie den Befehl **STRG+Q**, um den Code-Editor zu schließen, ohne Änderungen zu speichern, während die Cloud-Shell-Befehlszeile geöffnet bleibt.

### Ausführen der Chatanwendung

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die App auszuführen:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie eine Frage ein, z. B. `Where can I travel to?`, und überprüfen Sie die Antwort Ihres generativen KI-Modells.

    Beachten Sie, dass die Antwort Quellverweise enthält, um die indizierten Daten anzugeben, in denen die Antwort gefunden wurde.

1. Versuchen Sie es mit ein paar weiteren Fragen, zum Beispiel `Where should I stay in London?`.

    > **Hinweis**: Diese einfache Beispielanwendung enthält keine Logik, um den Unterhaltungsverlauf beizubehalten, sodass jeder Prompt als neue Unterhaltung behandelt wird.

1. Wenn Sie fertig sind, geben Sie `quit` ein, um das Programm zu beenden. Schließen Sie anschließend den Cloud Shell-Bereich.

## Herausforderung

Nachdem Sie nun erfahren haben, wie Sie Ihre eigenen Daten in eine generative KI-App integrieren können, die mit dem Azure-KI-Foundry-Portal erstellt wurde, lassen Sie uns weiterforschen!

Versuchen Sie, eine neue Datenquelle über das Azure KI Foundry-Portal hinzuzufügen, sie zu indizieren und die indizierten Daten in einen Prompt Flow zu integrieren. Einige Datasets, die Sie ausprobieren könnten, sind zum Beispiel:

- Eine Sammlung von Artikeln (z. B. Forschungsartikel), die sich auf Ihrem Computer befinden
- Eine Reihe von Präsentationen aus früheren Konferenzen
- Jedes beliebige Dataset, das Repository [Azure Search Sample Data](https://github.com/Azure-Samples/azure-search-sample-data) verfügbar ist.

Seien Sie so einfallsreich wie möglich, um Ihre Datenquelle zu erstellen und sie in einen Prompt Flow oder eine Client-App zu integrieren. Testen Sie Ihre Lösung, indem Sie Prompts einreichen, die nur mit dem von Ihnen festgelegten Datensatz beantwortet werden können!

## Bereinigung

Um unnötige Azure-Kosten und Ressourcenauslastung zu vermeiden, sollten Sie die in dieser Übung bereitgestellten Ressourcen entfernen.

1. Wenn Sie die Erkundung von Azure KI Foundry abgeschlossen haben, kehren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` zurück und melden sich gegebenenfalls mit Ihren Azure-Anmeldedaten an. Löschen Sie dann die Ressourcen in der Ressourcengruppe, in der Sie Ihre Azure KI-Suche- und Azure AI-Ressourcen bereitgestellt haben.
