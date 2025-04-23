---
lab:
  title: Vorbereiten eines KI-Entwicklungsprojekts
  description: 'Erfahren Sie, wie Sie Cloudressourcen in Hubs und Projekten organisieren, um Entwicklerinnen und Entwickler bei der Erstellung von KI-Lösungen zu unterstützen.'
---

# Vorbereiten eines KI-Entwicklungsprojekts

In dieser Übung verwenden Sie das Azure AI Foundry-Portal, um einen Hub und ein Projekt zu erstellen, das für ein Team von Entwickelnden bereit ist, eine KI-Lösung zu erstellen.

Diese Übung dauert ca. **30** Minuten.

> **Hinweis**: Einige der in dieser Übung verwendeten Technologien befinden sich in der Vorschau oder in der aktiven Entwicklung. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

## Öffnen des Azure KI Foundry-Portals

Beginnen wir mit der Anmeldung im Azure AI Foundry-Portal.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartfenster, die bei der ersten Anmeldung geöffnet werden, und verwenden Sie gegebenenfalls das Logo **Azure AI Foundry** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie das **Hilfe**-Fenster, falls es geöffnet ist):

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Prüfen Sie die Informationen auf der Startseite.

## Erstellen eines Hubs und eines Projekts

Ein Azure KI-*Hub* bietet einen kollaborativen Arbeitsbereich, in dem Sie ein oder mehrere *Projekte* definieren können. Wir erstellen ein Projekt und einen Azure KI-Hub und überprüfen die Azure-Ressourcen, die erstellt werden, um sie zu unterstützen.

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein und wählen Sie, falls ein vorhandener Hub vorgeschlagen wird, die Option zum Erstellen eines neuen. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Ein gültiger Name für Ihren Hub*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*
    - **Speicherort**: Wählen Sie **Hilfe bei der Auswahl** und wählen Sie dann **gpt-4o** im Fenster Hilfsprogramm für den Speicherort und verwenden Sie den empfohlenen Bereich\*
    - **Azure KI Services oder Azure OpenAI verbinden**: *Erstellen Sie eine neue KI-Dienst-Ressource*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen sind durch regionale Modellkontingente eingeschränkt. Sollte im weiteren Verlauf der Übung eine Kontingentgrenze überschritten werden, müssen Sie möglicherweise eine weitere Ressource in einer anderen Region anlegen.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)

1. Wählen Sie im unteren linken Navigationsbereich die Option **Verwaltungszentrum** aus. Im Verwaltungszentrum können Sie Einstellungen sowohl auf *Hub-* als auch auf *Projekt-* Ebene festlegen, die beide im Navigationsbereich angezeigt werden.

    ![Screenshot der Verwaltungszentrumsseite im Azure AI Foundry-Portal.](./media/ai-foundry-management.png)

    Beachten Sie, dass Sie im Navigationsbereich auf den folgenden Seiten Ressourcen auf Hub- und Projektebene anzeigen und verwalten können:

    - Übersicht
    - Benutzer
    - Modelle und Endpunkte
    - Verbundene Ressourcen
    - Compute (*nur auf Hub-Ebene*)

    > **Hinweis**: Je nach den Berechtigungen, die Ihrer Entra-ID in Ihrem Azure-Mandanten zugewiesen sind, können Sie möglicherweise keine Ressourcen auf Hub-Ebene verwalten.

1. Wählen Sie im Navigationsbereich im Abschnitt für Ihren Hub die Seite **Übersicht** aus, um Details zu Ihrem Hub anzuzeigen. 
1. Wählen Sie im Bereich **Hub-Eigenschaften** den Link zu der mit dem Hub verbundenen Ressourcengruppe, um eine neue Browserregisterkarte zu öffnen und zum Azure-Portal zu navigieren. Melden Sie sich mit Ihren Azure-Anmeldeinformationen an, wenn Sie dazu aufgefordert werden.
1. Rufen Sie die Ressourcengruppe im Azure-Portal auf, um die Azure-Ressourcen anzuzeigen, die zur Unterstützung Ihres Hubs und Projekts erstellt wurden.

    ![Screenshot: Azure KI-Hub und zugehörige Ressourcen im Azure-Portal.](./media/azure-portal-resources.png)

    Beachten Sie, dass die Ressourcen in der Region erstellt wurden, die Sie beim Erstellen des Hubs ausgewählt haben.

## Hinzufügen einer verbundenen Ressource

Angenommen, Ihr Projekt benötigt Zugriff auf eine zweite **Azure KI Services-Ressource** in einer anderen Region.

1. Wählen Sie im Azure-Portal auf der Seite für Ihre Ressourcengruppe **+ Erstellen** aus und suchen Sie nach `Azure AI Services`. Wählen Sie in den Ergebnissen die Multi-Service-Ressource **Azure KI Services** aus, wie in der folgenden Abbildung dargestellt:

    ![Screenshot der Azure KI-Services-Ressourcenkachel im Azure-Portal.](./media/azure-ai-services.png)

1. Erstellen Sie eine neue **Azure KI Services**-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Die Ressourcengruppe mit Ihren vorhandenen Azure AI Foundry-Ressourcen.*
    - **Region**: *Auswahl einer beliebigen verfügbaren Region, die nicht diejenige ist, in der sich Ihre vorhandenen Ressourcen befinden*
    - **Name**: *Ein geeigneter Name für Ihre zweite Azure AI Services-Ressource*
    - **Tarif**: Standard S0.
1. Warten Sie, bis die Ressource „KI-Dienste“ erstellt wurde.
1. Kehren Sie zur Browserregisterkarte des Azure AI Foundry-Portals zurück und sehen Sie sich in der Ansicht **Verwaltungszentrum** im Navigationsbereich im Abschnitt für Ihr *<u>Projekt</u>* die Seite **Verbundene Ressourcen** an. Die vorhandenen verbundenen Ressourcen in Ihrem Projekt werden aufgelistet.

    ![Screenshot der verbundenen Ressourcen in einem AI Foundry-Projekt.](./media/ai-foundry-project-resources.png)

1. Wählen Sie **+ Neue Verbindung** und wählen Sie den Ressourcentyp **Azure KI Services**. Durchsuchen Sie dann die verfügbaren Ressourcen, um die KI Services-Ressource zu finden, die Sie im Azure-Portal erstellt haben, und verwenden Sie die Schaltfläche **Verbindung hinzufügen**, um sie zu Ihrem Projekt hinzuzufügen.

    ![Screenshot des Dialogfelds „Azure KI Services-Ressourcen verbinden“ in einem AI Foundry-Projekt.](./media/add-resource.png)

1. Wenn die neue Ressource verbunden ist, schließen Sie das Dialogfeld **Azure KI Services-Ressourcen verbinden** und überprüfen Sie, ob die neuen verbundenen Ressourcen für Azure KI Services und Azure OpenAI Service aufgelistet sind.

## Entdecken von KI Services

Ihr Azure AI Foundry-Projekt hat Zugriff auf Azure KI Services. Probieren wir das im Portal aus.

1. Wählen Sie auf der Verwaltungszentrumsseite im Navigationsbereich unter Ihrem Projekt die Option **Zum Projekt wechseln**.
1. Wählen Sie im Navigationsbereich Ihres Projekts **KI Services** und wählen Sie die Kachel **Sprache und Übersetzer**.

    ![Screenshot der Kachel „Sprache und Übersetzer“ im Azure AI Foundry-Portal.](./media/language-and-translator.png)

1. Im Abschnitt **Sprachenfunktionen erkunden** sehen Sie sich die Registerkarte **Übersetzung** an und wählen **Textübersetzung**.

    ![Screenshot der Kachel „Textübersetzung“ im Azure AI Foundry-Portal.](./media/text-translation.png)

1. Auf der Seite **Textübersetzung**, im Abschnitt **Ausprobieren**, sehen Sie sich die Registerkarte **Mit eigenen Daten testen** an.
1. Wählen Sie eine Ihrer Azure KI Services-Ressourcen aus, und übersetzen Sie dann Text (z. B. `Hello world`) aus einer Sprache in eine andere.

    ![Screenshot der Kachel „Textübersetzung“ im Azure AI Foundry-Portal.](./media/try-translation.png)

## Bereitstellen und Testen eines generativen KI-Modells

Ihr Projekt enthält auch verbundene Ressourcen für Azure OpenAI, mit denen Sie Azure OpenAI-Sprachmodelle verwenden können, um generative KI-Lösungen zu implementieren. Sie können auch generative KI-Modelle von anderen Anbietern im Modellkatalog finden und verwenden.

1. Wählen Sie im linken Fensterbereich für Ihr Projekt im Abschnitt **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Wählen Sie auf der Seite **Modelle + Endpunkte** auf der Registerkarte **Modellbereitstellungen** im Menü **+ Modell bereitstellen** die Option **Basismodell bereitstellen**.
1. Suchen Sie das Modell **gpt-4o** in der Liste, wählen Sie es aus und bestätigen Sie es.
1. Stellen Sie das Modell mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
    - **Bereitstellungsname:***Ein eindeutiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Globaler Standard
    - **Automatische Versionsaktualisierung**: Aktiviert
    - **Modellversion**: *Wählen Sie die neueste verfügbare Version aus.*
    - **Verbundene AI-Ressource**: *Wählen Sie Ihre Azure OpenAI-Ressourcenverbindung*
    - **Tokens pro Minute Ratenlimit (Tausende)**: 50K *(oder das in Ihrem Abonnement verfügbare Maximum, wenn weniger als 50K)*
    - **Inhaltsfilter**: StandardV2 

    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 50.000 TPM sollten für die in dieser Übung verwendeten Daten ausreichend sein. Wenn Ihr verfügbares Kontingent darunter liegt, können Sie die Übung zwar durchführen, aber es können Fehler auftreten, wenn das Kontingent überschritten wird.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist.

1. Nachdem das Modell bereitgestellt wurde, wählen Sie auf der Seite „Bereitstellungsübersicht“ die Option **Im Playground öffnen** aus.
1. Stellen Sie auf der Seite **Chat-Playground** sicher, dass Ihre Modellimplementierung im Abschnitt **Bereitstellung** ausgewählt ist.
1. Geben Sie im Bereich **Setup** in das Feld **Geben Sie dem Modell Anweisungen und Kontext** die folgenden Anweisungen:

    ```
    You are a history teacher who can answer questions about past events all around the world.
    ```

1. Übernehmen Sie die Änderungen, um die Systemmeldung zu aktualisieren.
1. Geben Sie im Chat-Fenster eine Abfrage wie `What are the key events in the history of Scotland?` ein und sehen Sie sich die Antwort an:

    ![Screenshot des Spielplatzes im Azure KI Foundry-Portal.](./media/ai-foundry-playground.png)

## Zusammenfassung

In dieser Übung haben Sie Azure AI Foundry untersucht und erfahren, wie Sie Hubs und Projekte erstellen und verwalten, verbundene Ressourcen hinzufügen und Azure KI Services und Azure OpenAI-Modelle im Azure AI Foundry-Portal erkunden.

## Bereinigen

Wenn Sie die Erkundung des Azure KI-Foundry-Portals abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zu der Browserregisterkarte zurück, die das Azure-Portal enthält (oder öffnen Sie das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` erneut in einer neuen Browserregisterkarte) und sehen Sie sich den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
