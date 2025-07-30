---
lab:
  title: Optimieren eines Sprachmodells
  description: 'Erfahren Sie, wie Sie ihre eigenen Trainingsdaten verwenden, um ein Modell zu optimieren und sein Verhalten anzupassen.'
---

# Optimieren eines Sprachmodells

Wenn Sie möchten, dass sich ein Sprachmodell auf eine bestimmte Weise verhält, können Sie das entsprechende Engineering verwenden, um das gewünschte Verhalten zu definieren. Wenn Sie die Konsistenz des gewünschten Verhaltens verbessern möchten, können Sie ein Modell optimieren, indem Sie es mit Ihrem Prompt-Engineering-Ansatz vergleichen, um zu bewerten, welche Methode Ihren Anforderungen am besten entspricht.

In dieser Übung nehmen Sie mit Azure KI Foundry die Feinabstimmung eines Sprachmodells vor, das Sie für ein benutzerdefiniertes Chat-Anwendungsszenario verwenden möchten. Sie vergleichen das fein abgestimmte Modell mit einem Basismodell, um zu beurteilen, ob das fein abgestimmte Modell Ihren Anforderungen besser entspricht.

Stellen Sie sich vor, Sie arbeiten für ein Reisebüro und entwickeln eine Chatanwendung, um Personen bei der Planung ihres Urlaubs zu helfen. Ziel ist es, einen einfachen und inspirierenden Chat zu erstellen, der in einem konsistenten, freundlichen Gesprächstonfall Ziele und Aktivitäten vorschlägt.

Diese Übung dauert etwa **60** Minuten\*.

> \***Hinweis**: Bei dieser Zeitangabe handelt es sich um eine Schätzung, die auf durchschnittlichen Erfahrungen beruht. Die Feinabstimmung hängt von den Ressourcen der Cloud-Infrastruktur ab, deren Bereitstellung je nach Rechenzentrumskapazität und gleichzeitiger Nachfrage unterschiedlich viel Zeit in Anspruch nehmen kann. Einige Aktivitäten in dieser Übung können eine <u>längere</u> Zeit in Anspruch nehmen und erfordern Geduld. Wenn die Dinge eine Weile dauern, sollten Sie die [Azure AI Foundry Dokumentation zur Feinabstimmung](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) lesen oder eine Pause einlegen. Es ist möglich, dass einige Prozesse eine Zeitüberschreitung aufweisen oder scheinbar endlos laufen. Einige der in dieser Übung verwendeten Technologien befinden sich in der Vorschauphase oder in der aktiven Entwicklung. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

## Bereitstellen Sie ein DALL-E-Modell in einem Azure KI Foundry-Projekt

Beginnen wir mit dem Erstellen eines Azure KI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartfenster, die bei der ersten Anmeldung geöffnet werden, und verwenden Sie gegebenenfalls das Logo **Azure AI Foundry** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie das **Hilfe**-Fenster, falls es geöffnet ist):

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Suchen Sie auf der Startseite im Abschnitt **Modelle und Funktionen erkunden** nach dem Modell `gpt-4o`, das wir in unserem Projekt verwenden werden.
1. Wählen Sie in den Suchergebnissen das Modell **gpt-4o** aus, um dessen Details anzuzeigen, und wählen Sie dann oben auf der Seite für das Modell die Option **Dieses Modell verwenden** aus.
1. Wenn Sie zum Erstellen eines Projekts aufgefordert werden, geben Sie einen gültigen Namen für Ihr Projekt ein und erweitern Sie **Erweiterte Optionen**.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihr Projekt fest:
    - **Azure KI Foundry-Ressource**: *Ein gültiger Name für Ihre Azure KI Foundry-Ressource*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region**: *Bitte wählen Sie eine der folgenden Regionen aus*:\*
        - USA (Ost) 2
        - USA Nord Mitte
        - Schweden, Mitte

    > \* Zum Zeitpunkt der Erstellung dieses Artikels unterstützen diese Regionen die Feinabstimmung für GPT-4o-Modelle.

1. Klicken Sie auf **Erstellen**, und warten Sie, bis das Projekt erstellt wird. Stellen Sie bei entsprechender Aufforderung das gpt-4o-Modell mithilfe des Bereitstellungstyps **Globaler Standard** bereit, und passen Sie die Bereitstellungsdetails an, um für **Ratenbegrenzung für Token pro Minute** den Wert „50.000“ (oder den maximal verfügbaren Wert, wenn er weniger als 50.000 beträgt) festzulegen.

    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 50.000 TPM sollten für die in dieser Übung verwendeten Daten ausreichend sein. Wenn Ihr verfügbares Kontingent darunter liegt, können Sie die Übung zwar durchführen, aber es können Fehler auftreten, wenn das Kontingent überschritten wird.

1. Wenn Ihr Projekt erstellt wird, wird der Chat-Playground automatisch geöffnet, damit Sie Ihr Modell testen können:
1. Notieren Sie sich im Bereich **Setup** den Namen Ihrer Modellbereitstellung, der **gpt-4o** lauten sollte. Sie können dies überprüfen, indem Sie die Bereitstellung auf der Seite **Modelle und Endpunkte** anzeigen (öffnen Sie dazu einfach diese Seite im Navigationsbereich auf der linken Seite).
1. Wählen Sie im Navigationsbereich auf der linken Seite **Übersicht**, um die Hauptseite Ihres Projekts anzuzeigen, die wie folgt aussieht:

    ![Screenshot einer Projektübersichtsseite von Azure AI Foundry.](./media/ai-foundry-project.png)

## Feinabstimmen eines Modells

Da die Feinabstimmung eines Modells einige Zeit in Anspruch nimmt, beginnen Sie jetzt mit der Feinabstimmung und kehren später dazu zurück, nachdem Sie das bereits bereitgestellte Basis-GPT-4o-Modell untersucht haben.

1. Laden Sie den [Trainingsdatensatz](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) unter `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl` herunter und speichern Sie ihn lokal als JSONL-Datei.

    > **Hinweis**: Ihr Gerät speichert die Datei möglicherweise standardmäßig als .txt-Datei. Wählen Sie alle Dateien aus, und entfernen Sie das .txt-Suffix, um sicherzustellen, dass Sie die Datei als JSONL speichern.

1. Navigieren Sie zur Seite **Feinabstimmung** unter dem Abschnitt **Erstellen und Anpassen**, indem Sie das Menü auf der linken Seite verwenden.
1. Klicken Sie auf die Schaltfläche aus, um ein neues Feinabstimmungsmodell hinzuzufügen, wählen Sie das Modell **gpt-4o** aus und klicken Sie dann auf **Weiter**.
1. **Optimieren Sie** das Modell mithilfe der folgenden Konfiguration:
    - **Methode der Anpassung**: Überwacht
    - **Basismodell**: *Wählen Sie die Standardversion von **GPT-4o aus.***
    - **Schulungsdaten**: *Wählen Sie die Option zum **Hinzufügen von Schulungsdaten** und zum Hochladen und Anwenden der .jsonl-Datei aus, die Sie zuvor heruntergeladen haben.*
    - **Modellsuffix**: `ft-travel`
    - **Seed**: *Zufällig
1. Übermitteln Sie die Feinabstimmungsdetails, und der Auftrag wird gestartet. Es kann einige Zeit dauern, bis der Vorgang abgeschlossen ist. Sie können mit dem nächsten Abschnitt der Übung fortfahren, während Sie warten.

> **Hinweis**: Die Feinabstimmung und Bereitstellung kann sehr viel Zeit in Anspruch nehmen (30 Minuten oder länger). Überprüfen Sie daher in regelmäßigen Abständen Ihre Einstellungen. Sie können weitere Details über den bisherigen Fortschritt sehen, indem Sie den Feinabstimmungsmodellauftrag auswählen und die Registerkarte **Protokolle** anzeigen.

## Chat mit einem Basismodell

Während Sie darauf warten, dass die Feinabstimmung abgeschlossen ist, wollen wir uns mit einem GPT-4o-Basismodell unterhalten, um seine Leistung zu beurteilen.

1. Wählen Sie im Navigationsbereich links **Playgrounds** aus und öffnen Sie den **Chat-Playground**.
1. Überprüfen Sie, ob das von Ihnen bereitgestellte **GPT-4o-Basismodell** im Bereich „Setup“ ausgewählt ist.
1. Geben Sie im Chat-Fenster die Abfrage `What can you do?` ein und sehen Sie sich die Antwort an.

    Die Antworten können recht allgemein gehalten sein. Denken Sie daran, dass wir eine Chatanwendung erstellen möchten, die Menschen zum Reisen inspiriert.

1. Aktualisieren Sie die Systemmeldung im Setup-Fenster mit der folgenden Aufforderung:

    ```
    You are an AI assistant that helps people plan their travel.
    ```

1. Wählen Sie **Änderungen übernehmen**, um die Systemmeldung zu aktualisieren.
1. Geben Sie im Chatfenster erneut die Abfrage `What can you do?` ein und sehen Sie sich die Antwort an.
1 Als Antwort könnte der Assistent Ihnen mitteilen, dass er Ihnen bei der Buchung von Flügen, Hotels und Mietwagen für Ihre Reise behilflich sein kann. Sie möchten dieses Verhalten verhindern.

1. Aktualisieren Sie die Systemnachricht erneut mit einer neuen Eingabeaufforderung:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```
.
1. Testen Sie ihre Chatanwendung weiterhin, um sicherzustellen, dass sie keine Informationen bereitstellt, die nicht auf abgerufenen Daten basieren. Stellen Sie zum Beispiel die folgenden Fragen und überprüfen Sie die Antworten des Modells, wobei Sie besonders auf den Tonfall und den Schreibstil achten sollten, den das Modell verwendet, um zu antworten:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

## Überprüfen der Schulungsdatei

Das Basismodell scheint gut genug zu funktionieren, aber vielleicht wünschen Sie sich von Ihrer generativen KI-App einen bestimmten Gesprächsstil. Die Schulungsdaten, die für die Feinabstimmung verwendet werden, bieten Ihnen die Möglichkeit, explizite Beispiele für die von Ihnen gewünschte Art von Antworten zu erstellen.

1. Öffnen Sie die JSONL-Datei, die Sie zuvor heruntergeladen haben (Sie können sie in einem beliebigen Texteditor öffnen)
1. Untersuchen Sie die Liste der JSON-Dokumente in der Datei mit den Schulungsdaten. Die erste sollte in etwa so aussehen (zur besseren Lesbarkeit formatiert):

    ```json
    {"messages": [
        {"role": "system", "content": "You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms. You should not provide any hotel, flight, rental car or restaurant recommendations. Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday."},
        {"role": "user", "content": "What's a must-see in Paris?"},
        {"role": "assistant", "content": "Oh la la! You simply must twirl around the Eiffel Tower and snap a chic selfie! After that, consider visiting the Louvre Museum to see the Mona Lisa and other masterpieces. What type of attractions are you most interested in?"}
        ]}
    ```

    Jede Beispielinteraktion in der Liste enthält dieselbe Systemnachricht, die Sie mit dem Basismodell getestet haben, eine Benutzender-Prompt in Bezug auf eine Reiseabfrage und eine Antwort. Anhand der Art der Antworten in den Schulungsdaten erfährt das fein abgestimmte Modell, wie es antworten sollte.

## Bereitstellen des optimierten Modells

Wenn die Feinabstimmung erfolgreich abgeschlossen wurde, können Sie das fein abgestimmte Modell bereitstellen.

1. Navigieren Sie zur Seite **Feinabstimmung** unter **Erstellen und Anpassen**, um Ihren Feinabstimmungsauftrag und seinen Status zu finden. Wenn es noch läuft, können Sie sich entscheiden, ob Sie mit Ihrem bereitgestellten Basismodell weiter chatten oder eine Pause einlegen wollen. Wenn es abgeschlossen ist, können Sie fortfahren.

    > **Tipp**: Verwenden Sie die Schaltfläche **Aktualisieren** auf der Feinabstimmungsseite, um die Ansicht zu aktualisieren. Sollte die Feinabstimmung vollständig verschwinden, aktualisieren Sie die Seite im Browser.

1. Klicken Sie auf den Feinabstimmungsauftragslink, um die Detailseite zu öffnen. Wählen Sie die Registerkarte **Metriken** aus und erkunden Sie die Feinabstimmung der Metriken.
1. Stellen Sie das fein abgestimmte Modell mit den folgenden Konfigurationen bereit:
    - **Bereitstellungsname:***Ein gültiger Name für Ihre Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Ratenlimit für Token pro Minute (Tausender)**: 50K *(oder das Maximum, das in Ihrem Abonnement verfügbar ist, falls weniger als 50K)*
    - **Inhaltsfilter**: Standard
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie sie testen können; dies kann eine Weile dauern. Überprüfen Sie den **Bereitstellungsstatus**, bis er erfolgreich war (möglicherweise müssen Sie den Browser aktualisieren, um den aktualisierten Status zu sehen).

## Verwenden des fein abgestimmten Modells

Nachdem Sie Ihr fein abgestimmtes Modell bereitgestellt haben, können Sie es wie das bereitgestellte Basismodell testen.

1. Wenn die Verteilung fertig ist, navigieren Sie zu dem fein abgestimmten Modell und wählen Sie **Open in playground**.
1. Stellen Sie sicher, dass die Systemnachricht diese Anweisungen enthält:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Testen Sie Ihr fein abgestimmtes Modell, um zu beurteilen, ob sein Verhalten jetzt konsistenter ist. Stellen Sie beispielsweise die folgenden Fragen erneut, und erkunden Sie die Antworten des Modells:

    `Where in Rome should I stay?`

    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

1. Wie sehen die Antworten im Vergleich zu denen des Basismodells aus?

## Bereinigen

Wenn Sie die Erkundung von Azure KI Foundry abgeschlossen haben, sollten Sie die von Ihnen erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
