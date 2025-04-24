---
lab:
  title: Bewerten der Leistung generativer AI-Modelle
  description: 'Sie erfahren, wie Sie Modelle und Prompts auswerten können, um die Leistung Ihrer Chat App und ihre Fähigkeit, angemessen zu antworten, zu optimieren.'
---

# Bewerten der Leistung generativer AI-Modelle

In dieser Übung werden Sie manuelle und automatisierte Auswertungen verwenden, um die Leistung eines Modells im Azure AI Foundry-Portal zu bewerten.

Diese Übung dauert ungefähr **30** Minuten.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit dem Erstellen eines Azure AI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die bei der ersten Anmeldung geöffnet werden, und verwenden Sie, wenn nötig, das **Azure AI Foundry**-Logo oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie den **Hilfe**-Bereich, falls er geöffnet ist):

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein und wählen Sie, falls ein vorhandener Hub vorgeschlagen wird, die Option zum Erstellen eines neuen. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Geben Sie einen gültigen Namen für Ihren Hub an*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Standort**: Wählen Sie eine der folgenden Regionen\*
        - USA (Ost) 2
        - Frankreich, Mitte
        - UK, Süden
        - Schweden, Mitte
    - A**zure KI Services oder Azure OpenAI verbinden**: *Wählen Sie Neuen KI-Dienst erstellen aus*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Zum Zeitpunkt der Erstellung dieses Dokuments unterstützen diese Regionen die Bewertung von KI-Sicherheitsmetriken.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)

## Bereitstellen von Modellen

In dieser Übung werden Sie die Leistung eines gpt-4o-mini-Modells bewerten. Außerdem verwenden Sie ein gpt-4o-Modell, um KI-gestützte Bewertungsmetriken zu erstellen.

1. Wählen Sie im Navigationsbereich links für Ihr Projekt im Bereich **Meine Assets** die Seite **Modelle + Endpunkte**.
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
1. Kehren Sie zur Seite **Modelle + Endpunkte** zurück und wiederholen Sie die vorherigen Schritte, um ein **gpt-4o-mini**-Modell mit denselben Einstellungen bereitzustellen.

## Manuelles Auswerten eines Modells

Sie können Modellantworten basierend auf Testdaten manuell überprüfen. Die manuelle Überprüfung ermöglicht es Ihnen, verschiedene Eingaben zu testen, um zu beurteilen, ob das Modell die erwartete Leistung erbringt.

1. Laden Sie in einer neuen Browser-Registerkarte die Datei [travel_evaluation_data.csv](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.csv) von `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.csv`herunter und speichern Sie sie in einem lokalen Ordner.
1. Zurück auf der Registerkarte des Azure AI Foundry-Portals, wählen Sie im Navigationsbereich im Abschnitt **Bewerten und Verbessern** die Option **Bewertung**.
1. Rufen Sie auf der Seite **Bewertung** die Registerkarte **Manuelle Bewertungen** auf und wählen Sie **+ Neue manuelle Bewertung**.
1. Ändern Sie die **Systemmeldung** in die folgenden Anweisungen für einen KI-Reiseassistenten:

   ```
   Objective: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   Capabilities:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   Instructions:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Wählen Sie im Abschnitt **Konfigurationen** in der Liste **Modell** Ihre **gpt-4o-mini**-Modellimplementierung aus.
1. Wählen Sie im Abschnitt **Manuelles Bewertungsergebnis** die Option **Testdaten importieren** und laden Sie die zuvor heruntergeladene Datei **Travel_evaluation_data.csv** hoch; ordnen Sie die Datensatzfelder wie folgt zu:
    - **Eingabe**: Frage
    - **Erwartete Antwort**: ExpectedResponse
1. Überprüfen Sie die Fragen und die erwarteten Antworten in der Testdatei - Sie werden diese verwenden, um die Antworten zu bewerten, die das Modell erzeugt.
1. Wählen Sie **Ausführen** in der oberen Leiste aus, um Ausgaben für alle Fragen zu generieren, die Sie als Eingaben hinzugefügt haben. Nach ein paar Minuten sollten die Antworten des Modells in einer neuen Spalte **Ausgabe** angezeigt werden, etwa so:

    ![Screenshot einer manuellen Bewertungsseite im Azure AI Foundry-Portal.](./media/manual-evaluation.png)

1. Überprüfen Sie die Ergebnisse für jede Frage, vergleichen Sie die Ergebnisse des Modells mit der erwarteten Antwort und „bewerten“ Sie die Ergebnisse, indem Sie das Daumen-hoch- oder Daumen-runter-Symbol unten rechts bei jeder Antwort auswählen.
1. Nachdem Sie die Antworten bewertet haben, überprüfen Sie die Zusammenfassungskacheln oberhalb der Liste. Wählen Sie dann in der Symbolleiste **Ergebnisse speichern** und weisen Sie einen geeigneten Namen zu. Wenn Sie die Ergebnisse speichern, können Sie sie später für eine weitere Bewertung oder einen Vergleich mit einem anderen Modell abrufen.

## Automatisierte Bewertung verwenden

Der manuelle Vergleich der Modellausgabe mit den von Ihnen erwarteten Antworten kann zwar eine nützliche Methode sein, um die Leistung eines Modells zu bewerten, ist aber in Szenarien, in denen Sie eine große Bandbreite an Fragen und Antworten erwarten, sehr zeitaufwändig und liefert kaum standardisierte Messwerte, mit denen Sie verschiedene Modell- und Eingabeaufforderungs-Kombinationen vergleichen können.

Die automatisierte Bewertung ist ein Ansatz, der versucht, diese Unzulänglichkeiten zu beheben, indem er Metriken berechnet und KI einsetzt, um Antworten auf Kohärenz, Relevanz und andere Faktoren zu bewerten.

1. Verwenden Sie den Zurück-Pfeil (**&larr;**) neben dem Seitentitel **Manuelle Bewertung**, um zur Seite **Bewertung** zurückzukehren.
1. Sehen Sie sich die Registerkarte **Automatisierte Bewertungen** an.
1. Wählen Sie **Eine neue Bewertung erstellen**, und wenn Sie dazu aufgefordert werden, wählen Sie die Option, ein **Modell und eine Eingabeaufforderung** auszuwerten
1. Überprüfen Sie auf der Seite **Erstellen einer neuen Bewertung** im Abschnitt **Grundlegende Informationen** den standardmäßig automatisch generierten Bewertungsnamen (Sie können diesen ändern, wenn Sie möchten) und wählen Sie Ihr **gpt-40-mini**-Modell aus.
1. Ändern Sie die **Systemnachricht** in die gleichen Anweisungen für einen KI-Reiseassistenten, den Sie zuvor verwendet haben:

   ```
   Objective: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   Capabilities:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   Instructions:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Im Abschnitt **Testdaten konfigurieren** können Sie ein GPT-Modell verwenden, um Testdaten für Sie zu generieren (die Sie dann bearbeiten und ergänzen können, um sie Ihren eigenen Erwartungen anzupassen), einen vorhandenen Datensatz verwenden oder eine Datei hochladen. Wählen Sie in dieser Übung die Option **Vorhandenen Datensatz verwenden** und wählen Sie dann den Datensatz **Reise_Auswertungsdaten_csv_*xxxx...*** (der erstellt wurde, als Sie die .csv-Datei zuvor hochgeladen haben).
1. Überprüfen Sie die Beispielzeilen aus dem Datensatz, und wählen Sie dann im Abschnitt **Wählen Sie Ihre Datenspalte** die folgenden Spaltenzuordnungen:
    - **Abfrage**: Frage
    - **Kontext**: *Lassen Sie dies leer. Es wird verwendet, um die „Fundiertheit“ zu bewerten, wenn eine kontextuelle Datenquelle mit Ihrem Modell verknüpft wird.*
    - **Grundwahrheit**: ExpectedAnswer
1. Im Abschnitt **Auswählen, was Sie bewerten möchten**, wählen Sie <u>alle</u> der folgenden Bewertungskategorien:
    - KI-Qualität (KI-gestützt)
    - Risiko und Sicherheit (KI-gestützt)
    - KI-Qualität (NLP)
1. Wählen Sie in der Liste **Eine Modellimplementierung als Richter wählen** Ihr **gpt-4o** Modell aus. Dieses Modell wird verwendet, um die Antworten des ***gpt-4o-mini** Modells auf sprachbezogene Qualität und Standardvergleichsmetriken der generativen KI zu bewerten.
1. Wählen Sie **Erstellen**, um den Bewertungsprozess zu starten, und warten Sie, bis er abgeschlossen ist. Dies kann einige Minuten dauern.

    > **Tipp**: Wenn eine Fehlermeldung erscheint, die darauf hinweist, dass Projektberechtigungen gesetzt werden, warten Sie eine Minute und wählen Sie dann erneut **Erstellen**. Es kann einige Zeit dauern, bis die Ressourcenberechtigungen für ein neu erstelltes Projekt verteilt werden.

1. Wenn die Bewertung abgeschlossen ist, scrollen Sie ggf. nach unten, um den Bereich **Metrik-Dashboard** zu sehen und die **KI-Qualität (KI-gestützt)**-Metriken zu betrachten:

    ![Screenshot der KI-Qualitätsmetriken im Azure AI Foundry-Portal.](./media/ai-quality-metrics.png)

    Verwenden Sie die Symbole **<sup>(i) </sup>**, um die Metrikdefinitionen anzuzeigen.

1. Auf der Registerkarte **Risiko und Sicherheit** finden Sie die Metriken, die mit potenziell schädlichen Inhalten verbunden sind.
1. Auf der Registerkarte **KI-Qualität (NLP**) finden Sie Standardmetriken für generative KI-Modelle.
1. Scrollen Sie gegebenenfalls zum Anfang der Seite zurück und wählen Sie die Registerkarte **Daten**, um die Rohdaten der Bewertung anzuzeigen. Die Daten enthalten die Metriken für jede Eingabe sowie Erläuterungen des Grundgrundes des gpt-4o-Modells, das beim Bewerten der Antworten angewendet wird.

    ![Screenshot der Bewertungsdaten im Azure AI Foundry-Portal.](./media/evaluation-data.png)

## Bereinigen

Wenn Sie die Erkundung der Azure KI Foundry abgeschlossen haben, sollten Sie die von Ihnen erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
