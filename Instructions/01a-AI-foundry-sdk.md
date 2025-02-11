---
lab:
  title: Erstellen einer Chat-App mit dem Azure AI Foundry SDK
---

# Erstellen einer Chat-App mit dem Azure AI Foundry SDK

In dieser Übung verwenden Sie das Azure AI Foundry SDK, um eine einfache Chat-App zu erstellen.

Diese Übung dauert ca. **20** Minuten.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit dem Erstellen eines Azure AI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im **Assistenten zum Erstellen eines Projekts** einen geeigneten Projektnamen für (z. B. `my-ai-project`) ein und überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Ein eindeutiger Name – z. B. `my-ai-hub`.*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine neue Ressourcengruppe mit einem eindeutigen Namen (z.B. `my-ai-resources`), oder wählen Sie eine bestehende aus.*
    - **Ort**: Wählen Sie eine zufällige Region aus der folgenden Liste\*:
        - East US
        - USA (Ost) 2
        - USA Nord Mitte
        - USA Süd Mitte
        - Schweden, Mitte
        - USA (Westen)
        - USA, Westen 3
    - **Verbinden von Azure KI Services oder Azure OpenAI**: *Erstellen Sie eine neue KI Services-Ressource mit einem geeigneten Namen (z.B. `my-ai-services`) oder verwenden Sie eine vorhandene.*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Die Modellquoten werden auf der Ebene des Mandanten durch regionale Quoten eingeschränkt. Wenn Sie einen zufälligen Bereich auswählen, können Sie die Kontingentverfügbarkeit verteilen, wenn mehrere Benutzende im selben Mandanten arbeiten. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie dann auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)

## Bereitstellen eines generativen KI-Modells

Jetzt sind Sie bereit, ein generatives KI-Sprachmodell zur Unterstützung Ihrer Chat-Anwendung einzusetzen. In diesem Beispiel verwenden Sie das Microsoft Phi-4-Modell, aber die Prinzipien sind für jedes Modell identisch.

1. Wählen Sie im linken Fensterbereich für Ihr Projekt im Abschnitt **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Wählen Sie auf der Seite **Modelle + Endpunkte** auf der Registerkarte **Modellbereitstellungen** im Menü **+ Modell bereitstellen** die Option **Basismodell bereitstellen**.
1. Suchen Sie in der Liste das Modell **Phi-4**, wählen Sie es aus und bestätigen Sie es.
1. Wählen Sie die Bereitstellungsoption **Serverlose API mit Azure KI Inhaltssicherheit**.
1. Stellen Sie das Modell mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
    - **Einsatzname**: *Ein eindeutiger Name für Ihre Modellbereitstellung – zum Beispiel `phi-4-model` (merken Sie sich den Namen, den Sie vergeben, Sie brauchen ihn später.*)
    - **Inhaltsfilter**: Aktiviert

## Erstellen einer Clientanwendung zum Chatten mit dem Modell

Nachdem Sie nun ein Modell bereitgestellt haben, können Sie das Azure AI Foundry SDK verwenden, um eine Anwendung zu entwickeln, die mit ihm chattet.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry Portal zur **Übersichtsseite** Ihres Projekts.
1. Beachten Sie im Bereich **Projektdetails** die **Projektverbindungszeichenfolge**. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit Ihrem Projekt in einer Clientanwendung herzustellen.
1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.
1. Verwenden Sie die Taste **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

1. Geben Sie im PowerShell-Bereich die folgenden Befehle ein, um das GitHub-Repository für diese Übung zu klonen:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

1. Nachdem das Repo geklont wurde, navigieren Sie zum Ordner **mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python**:

    ```
    cd mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python
    ```

1. Geben Sie im Befehlszeilenbereich der Cloud Shell den folgenden Befehl ein, um die Python-Bibliotheken zu installieren, die Sie verwenden werden:
    - **python-dotenv**: Wird verwendet, um Einstellungen aus einer Anwendungskonfigurationsdatei zu laden.
    - **azure-identity**: Wird für die Authentifizierung mit Entra-ID-Anmeldeinformationen verwendet.
    - **azure-ai-projects**: Wird verwendet, um mit einem Azure AI Foundry-Projekt zu arbeiten.
    - **azure-ai-inference**: Wird verwendet, um mit einem generativen KI-Modell zu chatten.

    ```
    pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Python-Konfigurationsdatei **.env** zu bearbeiten:

    ```
    code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Ersetzen Sie in der Codedatei den Platzhalter **your_project_endpoint** durch die Zeichenfolge für die Verbindung zu Ihrem Projekt (kopiert von der **Übersichtsseite** des Azure AI Foundry-Portals) und den Platzhalter **your_model_deployment** durch den Namen, den Sie Ihrer Phi-4-Modellimplementierung zugewiesen haben.
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

### Schreiben von Code, um sich mit Ihrem Projekt zu verbinden und mit Ihrem Modell zu chatten

> **Tipp**: Achten Sie beim Hinzufügen von Code zur Python-Codedatei darauf, den richtigen Einzug beizubehalten.

1. Geben Sie den folgenden Befehl ein, um die zur Verfügung gestellte Python-Code-Datei **chat-app.py** zu bearbeiten:

    ```
    code chat-app.py
    ```

1. Beachten Sie in der Codedatei die vorhandenen **import**-Anweisungen, die am Anfang der Datei hinzugefügt wurden. Fügen Sie dann unter dem Kommentar **# Add AI Projects reference** den folgenden Code hinzu, um die Azure AI-Projektebibliothek zu referenzieren:

    ```python
    from azure.ai.projects import AIProjectClient
    ```

1. Beachten Sie in der **main**-Funktion unter dem Kommentar **# Get configuration settings**, dass der Code die Werte für die Zeichenfolge der Projektverbindung und den Namen der Modellbereitstellung lädt, die Sie in der Datei **.env** festgelegt haben.
1. Fügen Sie unter dem Kommentar **# Initialize the project client** den folgenden Code ein, um eine Verbindung zu Ihrem Azure AI Foundry-Projekt mit den Azure-Anmeldedaten herzustellen, mit denen Sie derzeit angemeldet sind:

    ```python
    project = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential()
        )
    ```
    
1. Fügen Sie unter dem Kommentar **# Get a chat client** den folgenden Code ein, um ein Client-Objekt zum Chatten mit einem Modell zu erstellen:

    ```python
    chat = project.inference.get_chat_completions_client()
    ```

1. Beachten Sie, dass der Code einen Loop (Schleife) enthält, damit Benutzende einen Prompt eingeben können, bis sie „quit“ eingeben. Fügen Sie dann im Schleifenabschnitt unter dem Kommentar **# Get a chat completion** abrufen den folgenden Code hinzu, um den Prompt zu übermitteln und den Abschluss aus Ihrem Modell abzurufen:

    ```python
    response = chat.complete(
        model=model_deployment,
        messages=[
            {"role": "system", "content": "You are a helpful AI assistant that answers questions."},
            {"role": "user", "content": input_text},
            ],
        )
    print(response.choices[0].message.content)
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen an der Codedatei zu speichern und verwenden Sie dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

### Ausführen der Chatanwendung

1. Geben Sie in der Cloud Shell-Befehlszeile den folgenden Befehl ein, um den Python-Code auszuführen:

    ```
    python chat-app.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie eine Frage ein, z. B. `What is the fastest animal on Earth?`, und überprüfen Sie die Antwort Ihres generativen KI-Modells.
1. Probieren Sie einige weitere Fragen aus. Wenn Sie fertig sind, geben Sie `quit` ein, um das Programm zu beenden.

## Zusammenfassung

In dieser Übung haben Sie das Azure AI Foundry SDK verwendet, um eine Clientanwendung für ein generatives KI-Modell zu erstellen, das Sie in einem Azure AI Foundry-Projekt bereitgestellt haben.

## Bereinigen

Wenn Sie die Erkundung des Azure KI-Foundry-Portals abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zu der Browserregisterkarte zurück, die das Azure-Portal enthält (oder öffnen Sie das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` erneut in einer neuen Browserregisterkarte) und sehen Sie sich den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
