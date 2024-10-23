---
lab:
  title: Erkunden der Komponenten und Tools von Azure KI Studio
---

# Erkunden der Komponenten und Tools von Azure KI Studio

In dieser Übung verwenden Sie Azure KI Studio, um ein Projekt zu erstellen und ein generatives KI-Modell zu erkunden.

Diese Übung dauert ca. **30** Minuten.

## Öffnen von Azure KI Studio

Sehen Sie sich zunächst Azure KI Studio an.

1. Öffnen Sie in einem Webbrowser [https://ai.azure.com](https://ai.azure.com), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Die Startseite von Azure KI Studio sieht ähnlich wie in der folgenden Abbildung aus:

    ![Screenshot: Azure KI Studio](./media/azure-ai-studio-home.png)

1. Überprüfen Sie die Informationen auf der Startseite. Zeigen Sie dann die einzelnen Registerkarten an, und sehen Sie sich die darauf enthaltenen Optionen zum Erkunden von Modellen und Funktionen, Erstellen von Projekten und Verwalten von Ressourcen an.

## Erstellen eines Azure KI-Hubs

Sie benötigen einen Azure KI-Hub in Ihrem Azure-Abonnement, um Projekte zu hosten. Sie können diese Ressource entweder beim Erstellen eines Projekts erstellen oder vorab bereitstellen (wie in dieser Übung).

1. Wählen Sie im Abschnitt **Verwaltung** die Option **Alle Ressourcen** und dann **+ Neuer Hub** aus. Erstellen Sie eine neue Regel mit den folgenden Einstellungen:
    - **Hub-Name:** *Ein eindeutiger Name*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe:** *Erstellen Sie eine neue Ressourcengruppe mit einem eindeutigen Namen, oder wählen Sie eine vorhandene Ressourcengruppe aus.*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **gpt-35-turbo** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*
    - **Verbinden von Azure KI Services oder Azure OpenAI**: *Wählen Sie eine Option aus, um einen neuen KI-Dienste zu erstellen oder einen vorhandenen zu verwenden.*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die aufgeführten Regionen enthalten das Standardkontingent für die in dieser Übung verwendeten Modelltypen. Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze in Szenarien erreicht, in denen Sie einen Mandanten für andere Benutzer und Benutzerinnen freigeben. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen.
1. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
   
    Nachdem der Azure KI-Hub erstellt wurde, sollte er ähnlich wie in der folgenden Abbildung aussehen:

    ![Screenshot: Details eines Azure KI-Hubs in Azure KI Studio.](./media/azure-ai-resource.png)

1. Öffnen Sie eine neue Browserregisterkarte (lassen Sie die Registerkarte mit Azure KI Studio geöffnet), und navigieren Sie unter [https://portal.azure.com](https://portal.azure.com?azure-portal=true) zum Azure-Portal. Melden Sie sich dort mit Ihren Azure-Anmeldeinformationen an, wenn Sie dazu aufgefordert werden.
1. Navigieren Sie zu der Ressourcengruppe, in der Sie Ihren Azure KI-Hub erstellt haben, und zeigen Sie die erstellten Azure-Ressourcen an.

    ![Screenshot: Azure KI-Hub und zugehörige Ressourcen im Azure-Portal.](./media/azure-portal.png)

1. Wechseln Sie zurück zur Browserregisterkarte von Azure KI Studio.
1. Zeigen Sie die einzelnen Seiten im Bereich links auf der Seite für Ihren Azure KI-Hub an, und sehen Sie sich die Artefakte an, die Sie erstellen und verwalten können. Beachten Sie auf der Seite **Verbindungen**, dass bereits Verbindungen zu Azure OpenAI- und KI-Diensten erstellt wurden.

## Erstellen eines Projekts

Ein Azure KI-Hub bietet einen Arbeitsbereich für die Zusammenarbeit, in dem Sie ein oder mehrere *Projekte* definieren können. Erstellen Sie nun ein Projekt in Ihrem Azure KI-Hub.

1. Stellen Sie in Azure KI Studio sicher, dass Sie sich im soeben erstellten Hub befinden (Sie sehen im Pfad oben auf dem Bildschirm, wo Sie sich befinden).
1. Navigieren Sie über das Menü links zu **Alle Projekte**.
1. Wählen Sie **+ New project** aus.
1. Erstellen Sie im **Assistenten zum Erstellen eines neuen Projekts** ein Projekt mit den folgenden Einstellungen:
    - **Aktueller Hub**: *Ihr KI-Hub*
    - **Projektname:** *Ein eindeutiger Name für Ihr Projekt*
1. Warten Sie, bis Ihr Projekt erstellt wurde. Wenn es fertig ist, sollte es ähnlich wie in der folgenden Abbildung aussehen:

    ![Screenshot: Projektdetailseite in Azure KI Studio](./media/azure-ai-project.png)

1. Zeigen Sie die Seiten im linken Bereich an, erweitern Sie jeden Abschnitt, und sehen Sie sich die Aufgaben an, die Sie ausführen können, und die Ressourcen, die Sie in einem Projekt verwalten können.

## Bereitstellen und Testen eines Modells

Sie können ein Projekt verwenden, um komplexe KI-Lösungen basierend auf generativen KI-Modellen zu erstellen. Eine vollständige Erkundung aller in Azure KI Studio verfügbaren Entwicklungsoptionen würde den Rahmen dieser Übung sprengen, aber wir werden einige grundlegende Möglichkeiten erkunden, wie Sie mit Modellen in einem Projekt arbeiten können.

1. Wählen Sie im linken Bereich für Ihr Projekt im Abschnitt **Komponenten** die Seite **Bereitstellungen** aus.
1. Wählen Sie auf der Seite **Einrichtungen** auf der Registerkarte **Modell-Einrichtungen** die Option **+ Modell einrichten** aus.
1. Suchen Sie in der Liste nach dem Modell **gpt-35-turbo**, und wählen Sie „Bestätigen“ aus.
1. Stellen Sie das Modell mit den folgenden Einstellungen bereit:
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **KI-Ressource**: *Wählen Sie die zuvor erstellte Quelle* aus
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: StandardV2 
    - **Dynamische Quote aktivieren**: Deaktiviert
      
    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 5.000 TPM reicht für die in dieser Übung verwendeten Daten aus.

1. Nachdem das Modell bereitgestellt wurde, wählen Sie auf der Seite „Bereitstellungsübersicht“ die Option **Im Playground öffnen** aus.
1. Stellen Sie auf der Seite **Chat-Playground** sicher, dass Ihre Modellimplementierung im Abschnitt **Bereitstellung** ausgewählt ist.
1. Geben Sie im Chatfenster eine Abfrage wie *Was ist KI?* ein, und sehen Sie sich die Antwort an:

    ![Screenshot: Playground in Azure KI Studio](./media/playground.png)

## Bereinigung

Wenn Sie mit der Erkundung von Azure KI Studio fertig sind, sollten Sie die in dieser Übung erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zur Browserregisterkarte mit dem Azure-Portal zurück (oder öffnen Sie das [Azure-Portal](https://portal.azure.com?azure-portal=true) auf einer neuen Browserregisterkarte erneut), und zeigen Sie den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
