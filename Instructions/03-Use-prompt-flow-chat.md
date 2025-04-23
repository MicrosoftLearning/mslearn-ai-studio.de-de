---
lab:
  title: Verwenden eines Prompt Flows zur Verwaltung von Konversationen in einer Chat-App
  description: 'Erfahren Sie, wie Sie Prompt Flows verwenden, um Konversationsdialoge zu verwalten und sicherzustellen, dass Prompts für optimale Ergebnisse konstruiert und orchestriert werden.'
---

# Verwenden eines Prompt Flows zur Verwaltung von Konversationen in einer Chat-App

In dieser Übung verwenden Sie den Prompt-Flow des Azure KI Foundry-Portals, um eine benutzerdefinierte Chat-App zu erstellen, die einen Benutzerprompt und einen Chatverlauf als Eingaben verwendet und ein GPT-Modell von Azure OpenAI nutzt, um eine Ausgabe zu erzeugen.

Diese Übung dauert ungefähr **30** Minuten.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit dem Erstellen eines Azure AI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein und wählen Sie, falls ein vorhandener Hub vorgeschlagen wird, die Option zum Erstellen eines neuen. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Geben Sie einen gültigen Namen für Ihren Hub an*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **gpt-4o** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*.
    - A**zure KI Services oder Azure OpenAI verbinden**: *Wählen Sie Neuen KI-Dienst erstellen aus*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden durch regionale Modellkontingente eingeschränkt. Wenn später in der Übung eine Kontingentgrenze erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)

## Konfigurieren der Ressourcenautorisierung

Die Tools für den Prompt Flow in Azure AI Foundry erstellen dateibasierte Ressourcen, die den Prompt Flow in einem Ordner im Blob Storage definieren. Bevor Sie den Prompt Flow untersuchen, stellen wir sicher, dass Ihre Azure AI Services-Ressource über den erforderlichen Zugriff auf den Blobspeicher verfügt, damit sie ihn lesen kann.

1. Wählen Sie im Azure AI Foundry-Portal im Navigationsbereich das **Verwaltungszentrum** aus, und zeigen Sie die Detailseite für Ihr Projekt an, die ähnlich wie dieses Bild aussieht:

    ![Screenshot des Verwaltungszentrums](./media/ai-foundry-manage-project.png)

1. Wählen Sie unter **Ressourcengruppe** Ihre Ressourcengruppe aus, um sie im Azure-Portal auf einer neuen Browserregisterkarte zu öffnen. Melden Sie sich mit Ihren Azure-Anmeldeinformationen an, wenn Sie dazu aufgefordert und schließen Sie alle Begrüßungsbenachrichtigungen, um die Ressourcengruppenseite anzuzeigen.

    Die Ressourcengruppe enthält alle Azure-Ressourcen zur Unterstützung Ihres Hubs und Projekts.

1. Wählen Sie die **Azure KI Services**-Ressource für Ihren Hub aus, um sie zu öffnen. Erweitern Sie dann den Abschnitt **Unter Ressourcenverwaltung**, und wählen Sie die Seite **Identität** aus:

    ![Screenshot der Seite „Azure KI Services-Identität“ im Azure-Portal.](./media/ai-services-identity.png)

1. Wenn der Status der im System zugewiesenen Identität auf **Aus** geschaltet ist, schalten Sie ihn auf **Ein**, und speichern Sie Ihre Änderungen. Warten Sie dann, bis die Änderung des Status bestätigt wurde.
1. Kehren Sie zur Seite „Ressourcengruppe“ zurück, und wählen Sie dann die Ressource **Speicherkonto** für Ihren Hub aus, und zeigen Sie die Seite **Zugriffssteuerung (IAM)** an:

    ![Screenshot der Seite „Zugriffssteuerung für Speicherkonto“ im Azure-Portal.](./media/storage-access-control.png)

1. Fügen Sie der Rolle `Storage blob data reader` eine Rollenzuweisung für die verwaltete Identität hinzu, die von Ihrer Azure KI Services-Ressource verwendet wird:

    ![Screenshot der Seite „Zugriffssteuerung für Speicherkonto“ im Azure-Portal.](./media/assign-role-access.png)

1. Wenn Sie den Rollenzugriff überprüft und zugewiesen haben, damit die verwaltete Azure KI Services-Identität Blobs im Speicherkonto lesen kann, schließen Sie die Registerkarte „Azure-Portal“, und kehren Sie zum Azure AI Foundry-Portal zurück.
1. Wählen Sie im Azure AI Foundry-Portal im Navigationsbereich **Gehe zum Projekt** aus, um zur Startseite Ihres Projekts zurückzukehren.

## Bereitstellen eines generativen KI-Modells

Jetzt können Sie ein generatives KI-Sprachmodell bereitstellen, um Ihre Prompt Flow-Anwendung zu unterstützen.

1. Wählen Sie im linken Fensterbereich für Ihr Projekt im Abschnitt **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Wählen Sie auf der Seite **Modelle + Endpunkte** auf der Registerkarte **Modellbereitstellungen** im Menü **+ Modell bereitstellen** die Option **Basismodell bereitstellen**.
1. Suchen Sie das Modell **gpt-4** in der Liste, wählen Sie es aus und bestätigen Sie es.
1. Stellen Sie das Modell mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
    - **Bereitstellungsname:***Ein gültiger Name für Ihre Modellimplementierung*
    - **Bereitstellungstyp**: Globaler Standard
    - **Automatische Versionsaktualisierung**: Aktiviert
    - **Modellversion**: *Wählen Sie die neueste verfügbare Version aus.*
    - **Verbundene AI-Ressource**: *Wählen Sie Ihre Azure OpenAI-Ressourcenverbindung*
    - **Ratenlimit für Token pro Minute (Tausender)**: 50K *(oder das Maximum, das in Ihrem Abonnement verfügbar ist, falls weniger als 50K)*
    - **Inhaltsfilter**: StandardV2 

    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 50.000 TPM reicht für die in dieser Übung verwendeten Daten aus. Wenn Ihr verfügbares Kontingent darunter liegt, können Sie die Übung abschließen, aber möglicherweise treten Fehler auf, wenn das Ratenlimit überschritten wird.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist.

## Erstellen eines Prompt flow

Ein Prompt flow bietet eine Möglichkeit, Prompts und andere Aktivitäten zu orchestrieren, um eine Interaktion mit einem generativen KI-Modell zu definieren. In dieser Übung verwenden Sie eine Vorlage, um einen einfachen Chatflow für einen KI-Assistenten in einem Reisebüro zu erstellen.

1. Wählen Sie in der Navigationsleiste des Azure AI Foundry-Portals im Abschnitt **Erstellen und Anpassen** die Option **Prompt flow** aus.
1. Erstellen Sie einen neuen Flow basierend auf der Vorlage **Chatflow** und geben Sie `Travel-Chat` als Ordnername an.

    Ein einfacher Chatflow wird für Sie erstellt.

1. Um Ihren Flow testen zu können, benötigen Sie eine Berechnung, und es kann eine Weile dauern, bis Sie beginnen können. Wählen Sie daher **Computesitzung starten** aus, um den Startvorgang zu beginnen, während Sie den Standardflow erkunden und anpassen.

1. Zeigen Sie den Prompt flow an, der aus einer Reihe von *Eingaben*, *Ausgaben* und *Tools* besteht. Sie können die Eigenschaften dieser Objekte in den Bearbeitungsbereichen auf der linken Seite erweitern und bearbeiten und den Gesamtflow als Diagramm auf der rechten Seite anzeigen:

    ![Screenshot des Prompt flow-Editors.](./media/prompt-flow.png)

1. Zeigen Sie den Bereich **Eingaben** an, und beachten Sie, dass zwei Eingaben vorhanden sind (Chatverlauf und Frage des Benutzenden).
1. Zeigen Sie den Bereich **Ausgaben** an, und beachten Sie, dass eine Ausgabe vorhanden ist, um die Antwort des Modells widerzuspiegeln.
1. Zeigen Sie den LLM-Toolbereich **Chat** an, der die erforderlichen Informationen enthält, um einen Prompt an das Modell zu senden.
1. Wählen Sie im LLM-Toolbereich **Chat** für **Verbindung** die Verbindung für die Azure OpenAI-Dienstressource in Ihrem KI-Hub aus. Konfigurieren Sie anschließend die folgenden Eigenschaften für die Verbindung.
    - **API**: Chat
    - **deployment_name**: *Das von Ihnen bereitgestellte gpt-4o-Modell*
    - **response_format**: {"type":"text"}
1. Ändern Sie das Feld **Prompt** wie folgt:

   ```yml
   # system:
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.

   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.

   {% for item in chat_history %}
   # user:
   {{item.inputs.question}}
   # assistant:
   {{item.outputs.answer}}
   {% endfor %}

   # user:
   {{question}}
   ```

    Lesen Sie den von Ihnen hinzugefügten Prompt, damit Sie damit vertraut sind. Er besteht aus einer Systemnachricht (einschließlich eines Ziels, einer Definition ihrer Funktionen und einiger Anweisungen) und dem Chatverlauf (geordnet, um jede Benutzerfrageeingabe und jede vorherige Ausgabe der Assistentenantwort anzuzeigen)

1. Stellen Sie im Abschnitt **Eingaben** für das LLM-Tool **Chat** (unter dem Prompt) sicher, dass die folgenden Variablen festgelegt sind:
    - **Frage** (Zeichenfolge): ${input.question}
    - **chat_history** (Zeichenfolge): ${inputs.chat_history}

1. Speichern Sie die Änderungen am Flow.

    > **Hinweis**: In dieser Übung beschränken wir uns auf einen einfachen Chatflow. Beachten Sie jedoch, dass der Promptflow-Editor viele weitere Tools enthält, die Sie dem Flow hinzufügen können, sodass Sie komplexe Logik zum Orchestrieren von Unterhaltungen erstellen können.

## Testen des Flows

Nachdem Sie den Flow entwickelt haben, können Sie ihn im Chatfenster testen.

1. Stellen Sie sicher, dass die Computesitzung ausgeführt wird. Ist dies nicht der Fall, warten Sie, bis sie gestartet wird.
1. Wählen Sie in der Symbolleiste **Chat**, um den Bereich **Chat** zu öffnen, und warten Sie, bis der Chat initialisiert ist.
1. Geben Sie die Abfrage ein: `I have one day in London, what should I do?`. Überprüfen Sie dann die Ausgabe. Der Chat-Bereich sollte in etwa so aussehen:

    ![Screenshot des Promptflow-Chatbereichs.](./media/prompt-flow-chat.png)

## Bereitstellen des Flows

Wenn Sie mit dem Verhalten des von Ihnen erstellten Flows zufrieden sind, können Sie ihn bereitstellen.

> **Hinweis**: Die Bereitstellung kann lange dauern und kann durch Kapazitätsbeschränkungen in Ihrem Abonnement oder Mandanten beeinträchtigt werden.

1. Wählen Sie in der Symbolleiste **Bereitstellen** und setzen Sie den Flow mit den folgenden Einstellungen ein:
    - **Grundeinstellungen**:
        - **Endpunkt**: Neu
        - **Endpunktname**: *Geben Sie einen eindeutigen Namen ein.*
        - **Bereitstellungsname**: *Geben Sie einen eindeutigen Namen ein.*
        - **VM**: Standard_DS3_v2
        - **Instanzenanzahl**: 1
        - **Rückschließen der Datensammlung**: Deaktiviert
    - **Erweiterte Einstellungen**:
        - *Verwenden der Standardeinstellungen*
1. Wählen Sie im Azure AI Foundry-Portal im Navigationsbereich im Bereich **Meine Assets** die Seite **Modelle + Endpunkte**.

    Wenn sich die Seite für Ihr gpt-4o-Modell öffnet, verwenden Sie die Schaltfläche **zurück**, um alle Modelle und Endpunkte anzuzeigen.

1. Initial zeigt die Seite möglicherweise nur Ihre Bereitstellungen von Modellen an. Es kann einige Zeit dauern, bis die Bereitstellung aufgelistet wird, und noch länger, bis sie erfolgreich erstellt ist.
1. Wenn die Bereitstellung *erfolgt* ist, wählen Sie sie aus. Zeigen Sie dann die Seite **Test** an.

    > **TIPP**: Wenn die Testseite den Endpunkt als fehlerhaft beschreibt, kehren Sie zu den **Modellen und Endpunkten** zurück und warten Sie eine Minute oder so, bevor Sie die Ansicht aktualisieren und den Endpunkt erneut auswählen.

1. Geben Sie den Prompt `What is there to do in San Francisco?` ein und überprüfen Sie die Antwort.
1. Geben Sie den Prompt `Tell me something about the history of the city.` ein und überprüfen Sie die Antwort.

    Der Bereich sollte in etwa so aussehen:

    ![Screenshot der bereitgestellten Prompt Flow Endpunkt-Testseite.](./media/deployed-flow.png)

1. Sehen Sie sich die **Verbrauchs**-Seite für den Endpunkt an, und beachten Sie, dass sie Verbindungsinformationen und Beispielcode enthält, die Sie verwenden können, um eine Client-Anwendung für Ihren Endpunkt zu erstellen - so können Sie die Prompt Flow-Lösung als generative KI-Anwendung in eine Anwendung integrieren.

## Bereinigen

Wenn Sie den Prompt Flow beenden, sollten Sie die von Ihnen erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
