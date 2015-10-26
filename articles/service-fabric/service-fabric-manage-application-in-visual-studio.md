<properties
   pageTitle="Gerenciando aplicativos da Malha do Serviço no Visual Studio"
   description="Você pode gerenciar os serviços e aplicativos da Malha do Serviço do Microsoft Azure por meio do Visual Studio."
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
   ms.date="08/05/2015"
   ms.author="jesseb"/>

# Gerenciando aplicativos da Malha do Serviço no Visual Studio

Você pode gerenciar os serviços e aplicativos da Malha do Serviço do Microsoft Azure por meio do Visual Studio. Depois de [configurar o ambiente de desenvolvimento](../service-fabric-setup-your-development-environment),você pode usar o Visual Studio para criar aplicativos da Malha do Serviço, adicionar serviços, ou pacotes, registrar e implantar aplicativos no cluster de desenvolvimento local.

Para gerenciar aplicativos da Malha do Serviço, no Gerenciador de Soluções, clique com o botão direito do mouse no projeto do aplicativo.

![Gerencie seu aplicativo da Malha do Serviço clicando com o botão direito do mouse no projeto do Aplicativo][manageservicefabric]

## Implantando o aplicativo da Malha do Serviço

A implantação de um aplicativo da Malha do Serviço combina as etapas a seguir em uma única operação.

1. Criar o pacote de aplicativo
2. Carregar o pacote de aplicativo no repositório de imagens
3. Registrar o tipo de aplicativo
4. Remover as instâncias de aplicativo em execução
5. Criar uma nova instância do aplicativo

No Visual Studio, você pode implantar um aplicativo escolhendo Deploy Solution no menu Build. Pressionar **F5** também implanta seu aplicativo e anexa o depurador a todas as instâncias do aplicativo.


## Adicionando um serviço ao aplicativo da Malha do Serviço

Você pode adicionar novos Serviços de Malha ao seu aplicativo para estender sua funcionalidade. Para garantir que o serviço esteja incluído no seu pacote de aplicativo, adicione o serviço usando o item de menu **Novo Serviço de Malha...**.

![Adicionar um novo Serviço de Malha ao aplicativo][newservice]

Selecione um tipo de projeto da Malha do Serviço para adicionar ao aplicativo e especifique um nome para o serviço. Consulte [Escolhendo uma estrutura para o serviço](service-fabric-choose-framework.md) para ajudar com a decisão de que tipo de serviço usar.

![Selecionar um tipo de projeto do Serviço de Malha para adicionar ao aplicativo][addserviceproject]

O novo serviço será adicionado à solução e ao pacote de aplicativo existente. As referências de serviço e uma instância de serviço padrão serão adicionadas ao manifesto do aplicativo. O serviço será criado e iniciado na próxima vez que você implantar o aplicativo.

![O novo serviço será adicionado ao manifesto do aplicativo][newserviceapplicationmanifest]

## Empacotando o aplicativo da Malha do Serviço

Um pacote de aplicativo precisa ser criado para implantação do aplicativo e seus serviços em um cluster. O pacote organiza o manifesto do aplicativo, os manifestos do serviço e outros arquivos necessários em um layout específico. O Visual Studio configura e gerencia o pacote na pasta do projeto do aplicativo, no diretório 'pkg'. Clicar em **Pacote** cria ou atualiza o pacote de aplicativo. Convém fazer isso se você implantar o aplicativo usando scripts personalizados do PowerShell.

## Removendo um aplicativo

Você pode remover um aplicativo do cluster local usando o Gerenciador de Servidores. Isso reverterá as etapas de implantação descritas acima:

1. Remover as instâncias de aplicativo em execução
2. Cancelar o registro do tipo de aplicativo
3. Remover o pacote de aplicativo do repositório de imagens

![Remover um aplicativo](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

- [Modelo de aplicativo da Malha do Serviço](service-fabric-application-model.md)
- [Implantação de aplicativo da Malha do Serviço](service-fabric-deploy-remove-applications.md)
- [Depurando o aplicativo da Malha do Serviço](service-fabric-debugging-your-application.md)
- [Visualizando o cluster usando o Gerenciador da Malha do Serviço](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png

<!---HONumber=Oct15_HO3-->