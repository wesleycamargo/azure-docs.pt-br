<properties
   pageTitle="Depurando seu aplicativo da Malha do Serviço no Visual Studio usando F5"
   description="Melhore a confiabilidade e o desempenho dos seus serviços usando o Visual Studio e um cluster de desenvolvimento local."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2015"
   ms.author="jesseb"/>

# Depurando seu aplicativo da Malha do Serviço no Visual Studio usando F5

Você economizar tempo e dinheiro implantando e depurando seu aplicativo da Malha do Serviço no cluster de desenvolvimento de um computador local. O Visual Studio pode implantar o aplicativo no cluster local e conectar o depurador automaticamente a todas as instâncias do seu aplicativo.

1. Inicie um cluster de desenvolvimento local seguindo as etapas em [Configurando o ambiente de desenvolvimento da Malha do Serviço](service-fabric-get-started.md).

2. Pressione **F5** ou clique em **Depurar** > **Iniciar Depuração**

    ![Iniciar depuração de um aplicativo][startdebugging]

3. Defina os pontos de interrupção no seu código e explore o aplicativo clicando nos comandos do menu **Depurar**.

    > [AZURE.NOTE]O Visual Studio se conecta a todas as instâncias do seu aplicativo. Ao percorrer o código, os pontos de interrupção podem ser atingidos por vários processos, resultando em sessões simultâneas. Tente desabilitar os pontos de interrupção depois que eles forem atingidos, tornando o ponto de interrupção condicional na ID do thread, ou use Eventos de Diagnóstico.

4. A janela **Eventos de Diagnóstico** abrirá automaticamente para exibir eventos de diagnóstico em tempo real.

    ![Exibir eventos de diagnóstico em tempo real][diagnosticevents]

5. Você também pode abrir a janela **Eventos de Diagnóstico** no Gerenciador de Servidores. Em **Azure**, clique com o botão direito do mouse em **Cluster da Malha do Serviço** > **Exibir Eventos de Diagnóstico...**

    ![Abrir a janela de eventos de diagnóstico][viewdiagnosticevents]

6. Os eventos de diagnóstico podem ser vistos no **ServiceEventSource.cs** gerado automaticamente e são chamados do código do aplicativo.

    ```csharp
    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, Service.ServiceTypeName);
    ```

7. A janela **Eventos de Diagnóstico** oferece suporte à filtragem, pausa e inspeção de eventos em tempo real. O filtro é uma pesquisa simples de cadeia de caracteres da mensagem do evento, incluindo seu conteúdo.

    ![Filtrar, pausar e retomar ou inspecionar eventos em tempo real][diagnosticeventsactions]

8. A depuração de serviços é semelhante à depuração de qualquer outro aplicativo. Os pontos de interrupção podem ser definidos normalmente por meio do Visual Studio para depuração fácil. Embora Coleções Confiáveis sejam aplicadas em vários nós, elas ainda implementam IEnumerable, o que significa que você pode usar a Exibição de Resultados no Visual Studio na depuração para ver o que foi armazenado dentro. Basta definir um ponto de interrupção em qualquer lugar no seu código.

    ![Iniciar depuração de um aplicativo][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

- [Testar um serviço da Malha do Serviço](service-fabric-test-your-service-index.md).
- [Gerenciando seu aplicativo da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
 

<!---HONumber=July15_HO2-->