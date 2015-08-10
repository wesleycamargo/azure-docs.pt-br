<properties
   pageTitle="Malha do Serviço do Microsoft Azure Como monitorar e diagnosticar serviços localmente"
   description="Este artigo descreve como você pode monitorar e diagnosticar seus serviços escritos com a Malha do Serviço do Microsoft Azure em um computador de desenvolvimento local."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/22/2015"
   ms.author="kunalds"/>


# Monitorando e diagnosticando serviços em uma configuração de desenvolvimento do computador local
Monitoramento, detecção, diagnóstico e solução de problemas permitem dar continuidade aos serviços com mínima interrupção da experiência do usuário. Embora seja essencial em um ambiente de produção implantado, a eficácia dependerá da adoção de um modelo semelhante durante o desenvolvimento de serviços para garantir que ele funcione quando você o mover para uma configuração real. A Malha do Serviço facilita para o desenvolvedor de serviço implementar diagnóstico que possa funcionar perfeitamente em um único desenvolvimento local de computador e nas configurações reais do cluster de produção.

## Rastreando e registrando em log
[Rastreamento de Eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) é a tecnologia recomendada para mensagens de rastreamento na Malha do Serviço. Os motivos para isso são:

* O ETW é rápido. Ele foi criado como uma tecnologia de rastreamento que tem um impacto mínimo sobre os tempos de execução de código.
* O rastreamento ETW funciona perfeitamente nos ambientes de desenvolvimento locais e também nas instalações de cluster reais. Isso significa que você não precisa reescrever o código de rastreamento quando estiver pronto para implantar seu código em um cluster real.
* O código do sistema da Malha do Serviço também usa o ETW para rastreamento interno. Isso permite que você exiba os rastreamentos de aplicativo intercalados com rastreamentos do sistema da Malha do Serviço, tornando mais fácil entender as sequências e as inter-relações entre o código do aplicativo e os eventos no sistema subjacente.
* Há suporte interno nas ferramentas do Visual Studio da Malha do Serviço para exibir os eventos do ETW.


## Exibir eventos do sistema da Malha do Serviço no Visual Studio

A Malha do Serviço emite eventos do ETW para ajudar os desenvolvedores de aplicativos a entender o que está acontecendo na plataforma. Para exibir esses eventos, siga estas etapas:

1. Você deve ter os pré-requisitos a seguir instalados.
   * Visual Studio 2015
   * SDK da Malha do Serviço

2. Inicie o Visual Studio como um administrador.

3. Crie um projeto (ou abra um existente) para um Ator ou Serviço com ou sem estado.

  ![Criar um aplicativo da Malha de Serviço](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject.png)

  ![Criar um serviço da Malha de Serviço](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject-2.png)

4. Pressione F5 para depurar o aplicativo. Os eventos da Malha do Serviço devem ser mostrados na janela Eventos de Diagnóstico. Cada evento tem informações de metadados padrão que apontam em qual nó, aplicativo e serviço o evento tem sua origem. Você também pode filtrar a lista de eventos usando a caixa "Filtrar eventos", na parte superior da janela; por exemplo, é possível filtrar por Nome do Nó ou Nome do Serviço.

  ![Visualizador de Eventos de Diagnóstico do Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

5. Se a janela Eventos de Diagnóstico não for exibida automaticamente, vá para a guia Gerenciador de Servidores no Visual Studio, clique com o botão direito do mouse no cluster Malha do Serviço e escolha “Exibir Eventos de Diagnóstico” no menu de contexto.

  ![Abrir o Visualizador de Eventos de Diagnóstico do Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

## Adicionar seus próprios rastreamentos personalizados ao código do aplicativo
Os modelos de projeto do Visual Studio da Malha do Serviço contêm amostra de código. O código mostra como adicionar rastreamentos personalizados ETW de código de aplicativo, que é mostrado no visualizador do ETW do Visual Studio ao lado dos rastreamentos do sistema da Malha do Serviço. A vantagem desse método é que os metadados são adicionados automaticamente aos rastreamentos, e o Visualizador de Diagnóstico do Visual Studio já está configurado para exibi-los.

Para projetos criados de **modelos de serviço** (com ou sem estado):

1. Abra o arquivo **Service.cs**. A chamada para `ServiceEventSource.Current.Message` no método *RunAsync* mostra um exemplo de rastreamento ETW personalizado do código do aplicativo.
2. No arquivo **ServiceEventSource.cs**, as sobrecargas do método `ServiceEventSource.Message` mostram uma opção de como escrever rastreamentos ETW personalizados.

Para projetos criados de **modelos de ator** (com ou sem estado):

1. Abra o arquivo **"ProjectName".cs**, onde *ProjectName* é o nome escolhido para seu projeto do Visual Studio.  
2. Localize o código `ActorEventSource.Current.ActorMessage(this, "Doing Work");` no método *DoWorkAsync*. Esse é um exemplo de um rastreamento ETW personalizado do código do aplicativo.  
3. No arquivo **ActorEventSource.cs**, as sobrecargas do método `ActorEventSource.ActorMessage` mostram uma opção de como escrever rastreamentos ETW personalizados.

Depois de adicionar o rastreamento ETW personalizado ao código de serviço, você pode compilar, implantar e executar o aplicativo novamente para ver seus eventos no Visualizador de Diagnóstico. Quando você depura o aplicativo com F5, o Visualizador de Diagnóstico é aberto automaticamente.

##Em breve
O mesmo código de rastreamento que você adicionou ao aplicativo acima para diagnóstico local funcionará com ferramentas que podem ser usadas para exibir esses eventos ao executar o mesmo código em um cluster do Azure. Mais detalhes serão publicados em breve.

## Próximas etapas

* [Introdução à integridade da Malha do Serviço](service-fabric-health-introduction.md)
* [Configuração do Application Insights](service-fabric-diagnostics-application-insights-setup.md)
* [Diagnóstico e monitoramento de desempenho de Atores da Malha do Serviço do Azure](service-fabric-reliable-actors-diagnostics.md)
* [Diagnóstico de Serviços Confiáveis com estado](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=July15_HO5-->