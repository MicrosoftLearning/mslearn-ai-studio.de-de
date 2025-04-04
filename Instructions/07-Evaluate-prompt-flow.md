---
lab:
  title: Bewerten der generativen KI-Leistung
  description: 'Erfahren Sie, wie Sie Modelle und Chatflows auswerten können, um die Leistung Ihrer Chat-App und ihre Fähigkeit, angemessen zu reagieren, zu optimieren.'
---

# Bewerten der generativen KI-Leistung

In dieser Übung lernen Sie integrierte und benutzerdefinierte Auswertungen kennen, um die Leistung Ihrer KI-Anwendungen mit dem Azure KI Foundry-Portal zu bewerten und zu vergleichen.

Diese Übung dauert ungefähr **30** Minuten.

## Erstellen eines Azure KI-Hubs und eines Projekts

Ein Azure KI-Hub bietet einen Arbeitsbereich für die Zusammenarbeit, in dem Sie ein oder mehrere *Projekte* definieren können. Erstellen Sie uns ein Projekt und ein Azure KI-Hub.

1. Öffnen Sie in einem Webbrowser das [Azure KI Foundry Portal](https://ai.azure.com) unter `https://ai.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an.

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im **Assistenten zum Erstellen eines Projekts** einen geeigneten Projektnamen für (z. B. `my-ai-project`) ein und überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Ein eindeutiger Name – z. B. `my-ai-hub`.*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine neue Ressourcengruppe mit einem eindeutigen Namen (z.B. `my-ai-resources`), oder wählen Sie eine bestehende aus.*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **gpt-4** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*.
    - **Verbinden von Azure KI Services oder Azure OpenAI**: *Erstellen Sie eine neue KI Services-Ressource mit einem geeigneten Namen (z.B. `my-ai-services`) oder verwenden Sie eine vorhandene.*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Die Modellquoten werden auf der Ebene des Mandanten durch regionale Quoten eingeschränkt. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)

## Bereitstellen eines GPT-Modells

Um ein Sprachmodell im Prompt Flow zu verwenden, müssen Sie zuerst ein Modell bereitstellen. Über das Azure KI Foundry-Portal können Sie OpenAI-Modelle bereitstellen, die Sie in Ihren Abläufen verwenden können.

1. Navigieren Sie zur Seite **Modelle + Endpunkte** unter dem Abschnitt **Meine Assets**, indem Sie das Menü auf der linken Seite verwenden.
1. Wählen Sie die Schaltfläche ** + Modell bereitstellen** und dann die Option **Basismodell bereitstellen** aus.
1. Erstellen Sie eine neue Bereitstellung des **gpt-4**-Modells mit den folgenden Einstellungen, indem Sie im Assistenten **Modell bereitstellen** die Option **Anpassen** auswählen:
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **KI-Ressource**: *Wählen Sie die zuvor erstellte Quelle* aus
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: StandardV2 
    - **Dynamische Quote aktivieren**: Deaktiviert

    > **Hinweis**: Wenn an Ihrem aktuellen Speicherort für KI-Ressourcen kein Kontingent für das Modell, das Sie bereitstellen möchten, verfügbar ist, werden Sie aufgefordert, einen anderen Speicherort zu wählen, an dem eine neue KI-Ressource erstellt und mit Ihrem Projekt verbunden wird.

1. Warten Sie, bis das Modell bereitgestellt wurde. Wenn die Bereitstellung bereit ist, wählen Sie **Im Playground öffnen** aus.
1. Ändern Sie im Textfeld **Modellanweisungen und Kontext geben** den Inhalt wie folgt:

   ```
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
   ```

1. Wählen Sie **Änderungen übernehmen** aus.
1. Geben Sie im Chatfenster (Verlauf) die Anfrage ein: `What can you do?`, um zu überprüfen, ob sich das Sprachmodell wie erwartet verhält.

Nachdem Sie nun über ein bereitgestelltes Modell mit einer aktualisierten Systemmeldung verfügen, können Sie das Modell auswerten.

## Manuelle Bewertung eines Sprachmodells im Azure KI Foundry-Portal

Sie können Modellantworten basierend auf Testdaten manuell überprüfen. Durch die manuelle Überprüfung können Sie verschiedene Eingaben einzeln testen, um zu bewerten, ob das Modell wie erwartet ausgeführt wird.

1. Wählen Sie im **Chat-Playground** das Dropdown-Menü **Auswerten** in der oberen Leiste und wählen Sie **Manuelle Auswertung**.
1. Ändern Sie die **Systemnachricht** in die gleiche Nachricht, die Sie oben verwendet haben (hier wieder enthalten):

   ```
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
   ```

1. Fügen Sie im Abschnitt **Ergebnis der manuellen Auswertung** fünf Eingaben hinzu, für die Sie die Ausgabe überprüfen wollen. Geben Sie die folgenden fünf Fragen als fünf separate **Eingaben** ein:

   `Can you provide a list of the top-rated budget hotels in Rome?`

   `I'm looking for a vegan-friendly restaurant in New York City. Can you help?`

   `Can you suggest a 7-day itinerary for a family vacation in Orlando, Florida?`

   `Can you help me plan a surprise honeymoon trip to the Maldives?`

   `Are there any guided tours available for the Great Wall of China?`

1. Wählen Sie **Ausführen** in der oberen Leiste aus, um Ausgaben für alle Fragen zu generieren, die Sie als Eingaben hinzugefügt haben.
1. Jetzt können Sie die Ausgaben für jede Frage manuell überprüfen, indem Sie unten rechts in einer Antwort das Symbol mit dem Daumen nach oben oder unten auswählen. Bewerten Sie jede Antwort, und stellen Sie sicher, dass Sie in Ihren Bewertungen mindestens einen Daumen nach oben und einen Daumen nach unten verwenden.
1. Klicken Sie in der oberen Menüleiste auf **Ergebnisse speichern**. Geben Sie `manual_evaluation_results` als Namen für die Ergebnisse ein.
1. Navigieren Sie über das Menü auf der linken Seite zu **Auswertungen**.
1. Wählen Sie die Registerkarte **Manuellen Auswertungen** aus, um die manuellen Auswertungen zu finden, die Sie gerade gespeichert haben. Beachten Sie, dass Sie Ihre zuvor erstellten manuellen Auswertungen durchsuchen, dort weitermachen, wo Sie aufgehört haben, und die aktualisierten Auswertungen speichern können.

## Auswerten Ihrer Chat-App mit den integrierten Metriken

Wenn Sie eine Chat-Anwendung mit Prompt Flow erstellt haben, können Sie den Flow evaluieren, indem Sie einen Batch-Lauf durchführen und die Leistung des Flows mit integrierten Metriken bewerten.

![Diagramm der Konstruktion des Eingabedatensatzes für die Auswertung.](./media/diagram-dataset-evaluation.png)

Um einen Chatverlauf zu bewerten, werden die Benutzeranfragen und die Chatantworten als Input für eine Bewertung bereitgestellt.

Um Zeit zu sparen, haben wir für Sie einen Batch-Ausgabedatensatz erstellt, der die Ergebnisse mehrerer Eingaben enthält, die von einem Prompt Flow verarbeitet werden. Jedes der Ergebnisse wird im Dataset gespeichert, das Sie im nächsten Schritt auswerten.

1. Wählen Sie die Registerkarte **Automatisierte Auswertungen** und erstellen Sie eine **Neue Auswertung** mit den folgenden Einstellungen: <details>  
      <summary><b>Tip zur Problembehandlung</b>: Berechtigungsfehler</summary>
        <p>Wenn beim Erstellen eines neuen Eingabeaufforderungsflusses ein Berechtigungsfehler angezeigt wird, versuchen Sie Folgendes zur Problembehandlung:</p>
        <ul>
          <li>Wählen Sie im Ressourcenmenü des Azure-Portals AI Dienste aus.</li>
          <li>Bestätigen Sie auf der Registerkarte Identität unter Ressourcenverwaltung, dass es sich um eine systemzugewiesene, verwaltete Identität handelt.</li>
          <li>Navigieren Sie zum dazugehörigen Speicherkonto. Fügen Sie auf der IAM-Seite die Rollenzuweisung <em>Storage blob data reader</em> hinzu.</li>
          <li>Wählen Sie unter <strong>Zugriff zuweisen zu</strong> die Option <strong>Verwaltete Identität</strong>, <strong>+ Mitglieder auswählen</strong>, und wählen Sie die Option <strong>Alle vom System zugewiesenen verwalteten Identitäten</strong>.</li>
          <li>Überprüfen und zuweisen, um die neuen Einstellungen zu speichern, und wiederholen Sie den vorherigen Schritt.</li>
        </ul>
    </details>

    - **Was möchten Sie auswerten?**: Dataset
    - **Auswertungsname**: *Geben Sie einen eindeutigen Namen ein.*
    - Wählen Sie **Weiter** aus.
    - **Wählen Sie die Daten aus, die Sie auswerten möchten**: Fügen Sie Ihr Dataset hinzu.
        - Laden Sie den [Validierungsdatensatz](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl) unter `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl` herunter, speichern Sie ihn als JSONL-Datei und laden Sie ihn in die Benutzeroberfläche hoch.

    > **Hinweis**: Ihr Gerät speichert die Datei möglicherweise standardmäßig als .txt-Datei. Wählen Sie alle Dateien aus, und entfernen Sie das .txt-Suffix, um sicherzustellen, dass Sie die Datei als JSONL speichern.

    - Wählen Sie **Weiter** aus.
    - **Metriken auswählen**: Kohärenz, Sprachfluss
    - **Verbindung**: *Ihre KI Services-Verbindung*
    - **Bereitstellungsname/Modell**: *Ihr bereitgestelltes GPT-4-Modell*
    - **Abfrage**: Wählen Sie **Abfrage** als Datenquelle aus.
    - **Antwort**: Wählen Sie **Antwort** als Datenquelle aus
      
1. Wählen Sie **Weiter** aus, überprüfen Sie Ihre Daten und senden Sie mit **Übermitteln** die neue Bewertung ab.
1. Warten Sie, bis die Auswertungen abgeschlossen sind. Eventuell müssen Sie die Seite aktualisieren.
1. Wählen Sie die gerade erstellte Auswertung aus.
1. Erkunden Sie das **Metrik-Dashboard** auf der Registerkarte **Bericht** und **Detaillierte Metrik-Ergebnisse** auf der Registerkarte **Daten**.

## Löschen von Azure-Ressourcen

Wenn Sie die Erkundung der Azure KI Foundry abgeschlossen haben, sollten Sie die von Ihnen erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
