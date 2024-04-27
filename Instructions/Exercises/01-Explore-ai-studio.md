---
lab:
  title: Erkunden des Azure KI Studio
---

# Erkunden des Azure KI Studio

In dieser Übung verwenden Sie Azure KI Studio, um ein Projekt zu erstellen und ein generatives KI-Modell zu erkunden.

> **Hinweis:** Azure KI Studio befindet sich zum Zeitpunkt des Schreibens in der Vorschau und in der aktiven Entwicklung. Einige Elemente des Diensts sind möglicherweise nicht genau wie beschrieben, und einige Features funktionieren möglicherweise nicht wie erwartet.

> Um diese Übung abzuschließen, muss für Ihr Azure-Abonnement der Zugriff auf den Azure OpenAI-Dienst genehmigt werden.

Diese Übung dauert ca. **30** Minuten.

## Öffnen von Azure KI Studio

Sehen Sie sich zunächst Azure KI Studio einmal an.

1. Öffnen Sie in einem Webbrowser [https://ai.azure.com](https://ai.azure.com), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Die Startseite von Azure KI Studio sieht ähnlich wie in der folgenden Abbildung aus:

    ![Screenshot: Azure KI Studio](./media/azure-ai-studio-home.png)

1. Überprüfen Sie die Informationen auf der Startseite. Zeigen Sie dann die einzelnen Registerkarten an, und sehen Sie sich die darauf enthaltenen Optionen zum Erkunden von Modellen und Funktionen, Erstellen von Projekten und Verwalten von Ressourcen an.

## Erstellen eines Azure KI-Hubs

Sie benötigen einen Azure KI-Hub in Ihrem Azure-Abonnement, um Projekte zu hosten. Sie können diese Ressource entweder beim Erstellen eines Projekts erstellen oder vorab bereitstellen (wie in dieser Übung).

1. Wählen Sie auf der Seite **Verwalten** die Option **+ Neuer Azure KI-Hub** aus. Erstellen Sie dann im **Assistenten zum Erstellen eines neuen Azure KI-Hubs** eine neue Ressource mit den folgenden Einstellungen:
    - **Name des Azure KI-Hubs**: *Ein eindeutiger Name*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe:** *Erstellen Sie eine neue Ressourcengruppe mit einem eindeutigen Namen, oder wählen Sie eine vorhandene Ressourcengruppe aus.*
    - **Speicherort:** *Treffen Sie eine **zufällige** Auswahl aus einer der folgenden Regionen*\*
        - Australien (Osten)
        - Kanada, Osten
        - East US
        - USA (Ost) 2
        - Frankreich, Mitte
        - Japan, Osten
        - USA Nord Mitte
        - Schweden, Mitte
        - Schweiz, Norden
        - UK, Süden
    - **Azure OpenAI**: (Neu) ai-*Hub_Name*
    - **KI-Suche**: (Keine)

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die aufgeführten Regionen enthalten das Standardkontingent für die in dieser Übung verwendeten Modelltypen. Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze in Szenarien erreicht, in denen Sie einen Mandanten für andere Benutzer und Benutzerinnen freigeben. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

    Nachdem der Azure KI-Hub erstellt wurde, sollte er ähnlich wie in der folgenden Abbildung aussehen:

    ![Screenshot: Details eines Azure KI-Hubs in Azure KI Studio.](./media/azure-ai-resource.png)

1. Öffnen Sie eine neue Browserregisterkarte (lassen Sie die Registerkarte mit Azure KI Studio geöffnet), und navigieren Sie unter [https://portal.azure.com](https://portal.azure.com?azure-portal=true) zum Azure-Portal. Melden Sie sich dort mit Ihren Azure-Anmeldeinformationen an, wenn Sie dazu aufgefordert werden.
1. Navigieren Sie zu der Ressourcengruppe, in der Sie Ihren Azure KI-Hub erstellt haben, und zeigen Sie die erstellten Azure-Ressourcen an.

    ![Screenshot: Azure KI-Hub und zugehörige Ressourcen im Azure-Portal.](./media/azure-portal.png)

1. Wechseln Sie zurück zur Browserregisterkarte von Azure KI Studio.
1. Zeigen Sie die einzelnen Seiten im Bereich links auf der Seite für Ihren Azure KI-Hub an, und sehen Sie sich die Artefakte an, die Sie erstellen und verwalten können. Beachten Sie auf der Seite **Verbindungen**, dass bereits eine Verbindung mit der Azure OpenAI-Ressource hergestellt wurde, die Sie mit Ihrem Azure KI-Hub namens **Default_AzureOpenAI** erstellt haben.

## Erstellen eines Projekts

Ein Azure KI-Hub bietet einen Arbeitsbereich für die Zusammenarbeit, in dem Sie ein oder mehrere *Projekte* definieren können. Erstellen Sie nun ein Projekt in Ihrem Azure KI-Hub.

1. Wählen Sie in Azure KI Studio auf der Seite **Erstellen** die Option **+ Neues Projekt** aus. Erstellen Sie dann im **Assistenten zum Erstellen eines neuen Projekts** ein Projekt mit den folgenden Einstellungen:
    - **Projektname:** *Ein eindeutiger Name für Ihr Projekt*
    - **KI-Hub**: *Ihr KI-Hub*
1. Warten Sie, bis Ihr Projekt erstellt wurde. Wenn es fertig ist, sollte es ähnlich wie in der folgenden Abbildung aussehen:

    ![Screenshot: Projektdetailseite in Azure KI Studio](./media/azure-ai-project.png)

1. Zeigen Sie die Seiten im linken Bereich an, erweitern Sie jeden Abschnitt, und sehen Sie sich die Aufgaben an, die Sie ausführen können, und die Ressourcen, die Sie in einem Projekt verwalten können.

## Bereitstellen und Testen eines Modells

Sie können ein Projekt verwenden, um komplexe KI-Lösungen basierend auf generativen KI-Modellen zu erstellen. Das vollständige Erkunden aller in Azure KI Studio verfügbaren Entwicklungsoptionen würde den Umfang dieser Übung übersteigen. Sie werden aber einige grundlegende Möglichkeiten erkunden, wie Sie mit Modellen in einem Projekt arbeiten können.

1. Wählen Sie im linken Bereich für Ihr Projekt im Abschnitt **Komponenten** die Seite **Bereitstellungen** aus.
1. Wählen Sie auf der Seite **Bereitstellungen** die Option **+ Erstellen** aus, und erstellen Sie eine Echtzeitendpunktbereitstellung.
1. Wählen Sie in der Liste **Modell auswählen** das Modell **gpt-35-turbo** aus, und bestätigen Sie Ihre Auswahl. Stellen Sie dann das Modell mit den folgenden Einstellungen bereit:
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Modell**: gpt-35-turbo
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **Erweiterte Optionen**:
        - **Inhaltsfilter**: Standard
        - **Bereitstellungstyp**: Standard
        - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000

    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 5.000 TPM reicht für die in dieser Übung verwendeten Daten aus.

1. Nachdem das Modell bereitgestellt wurde, wählen Sie im Bereich auf der linken Seite im Abschnitt **Extras** die Seite **Playground** aus.
1. Stellen Sie auf der Seite **Playground** sicher, dass die Modellimplementierung im Abschnitt **Konfiguration** ausgewählt ist. Geben Sie dann im Abschnitt **Chatsitzung** eine Abfrage wie *Was ist KI* ein, und sehen Sie sich die Antwort an:

    ![Screenshot: Playground in Azure KI Studio](./media/playground.png)

## Bereinigung

Wenn Sie mit der Erkundung von Azure KI Studio fertig sind, sollten Sie die in dieser Übung erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zur Browserregisterkarte mit dem Azure-Portal zurück (oder öffnen Sie das [Azure-Portal](https://portal.azure.com?azure-portal=true) auf einer neuen Browserregisterkarte erneut), und zeigen Sie den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
