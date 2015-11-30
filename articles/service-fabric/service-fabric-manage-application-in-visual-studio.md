<properties
   pageTitle="Gerenciar seus aplicativos no Visual Studio | Microsoft Azure"
   description="Use o Visual Studio para criar, desenvolver, empacotar, implantar e depurar seus aplicativos e serviços do Service Fabric."
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
   ms.date="11/17/2015"
   ms.author="jesseb"/>

# Usar o Visual Studio para simplificar a escrita e o gerenciamento de seus aplicativos do Service Fabric

É possível gerenciar os serviços e aplicativos do Service Fabric por meio do Visual Studio. Depois de [configurar o ambiente de desenvolvimento](service-fabric-setup-your-development-environment.md),você pode usar o Visual Studio para criar aplicativos da Malha do Serviço, adicionar serviços, ou pacotes, registrar e implantar aplicativos no cluster de desenvolvimento local.

Para gerenciar seu aplicativo, no Gerenciador de Soluções, clique com o botão direito do mouse no projeto do aplicativo.

![Gerencie seu aplicativo da Malha do Serviço clicando com o botão direito do mouse no projeto do Aplicativo][manageservicefabric]

## Implantando o aplicativo da Malha do Serviço

A implantação de um aplicativo combina as etapas a seguir em uma única operação.

1. Criar o pacote de aplicativo
2. Carregar o pacote de aplicativo no repositório de imagens
3. Registrar o tipo de aplicativo
4. Remover as instâncias de aplicativo em execução
5. Criar uma nova instância do aplicativo

No Visual Studio, pressionar **F5** também implanta seu aplicativo e anexa o depurador a todas as instâncias do aplicativo. Você pode usar **Ctrl + F5** para implantar um aplicativo sem depuração ou publicar um cluster local ou remoto usando o perfil de publicação. Consulte [Publicar um aplicativo em um cluster remoto usando o Visual Studio](service-fabric-publish-app-remote-cluster.md)

### Preservar os dados entre as execuções de teste

Geralmente, você testa serviços localmente adicionando entrada de dados de teste, modificando alguns blocos de código e depurando localmente novamente. As ferramentas do Service Fabric do Visual Studio fornecem uma propriedade útil chamada **Preservar dados na inicialização** para manter os dados inseridos na sessão anterior e permitir que você os use novamente.

### Para habilitar a propriedade Preservar dados na inicialização

1. No menu de atalho do projeto de aplicativo, escolha **Propriedades** (ou escolha a tecla **F4**).
1. Na janela **Propriedades**, defina a propriedade **Preservar dados na inicialização** como **Sim**.

	![Definir a propriedade Preservar dados na inicialização][preservedata]

Quando você executar o aplicativo novamente, o script de implantação tratará a implantação como uma atualização usando o modo auto não monitorado para atualizar rapidamente o aplicativo para uma versão mais recente com uma cadeia de caracteres de data anexada. O processo de atualização preserva todos os dados inseridos em uma sessão de depuração anterior.

![Exemplo de nova versão do aplicativo com a data incluída][preservedate]

Dados são preservados, aproveitando o recurso de atualização da plataforma do Service Fabric. Para obter mais informações sobre a atualização de um aplicativo, consulte [Atualização de aplicativo do Service Fabric](service-fabric-application-upgrade.md)

## Adicionando um serviço ao aplicativo da Malha do Serviço

Você pode adicionar novos Serviços de Malha ao seu aplicativo para estender sua funcionalidade. Para garantir que o serviço esteja incluído no seu pacote de aplicativo, adicione o serviço usando o item de menu **Novo Serviço de Malha...**.

![Adicionar um novo Serviço de Malha ao aplicativo][newservice]

Selecione um tipo de projeto da Malha do Serviço para adicionar ao aplicativo e especifique um nome para o serviço. Consulte [Escolhendo uma estrutura para o serviço](service-fabric-choose-framework.md) para ajudar com a decisão de que tipo de serviço usar.

![Selecionar um tipo de projeto do Serviço de Malha para adicionar ao aplicativo][addserviceproject]

O novo serviço será adicionado à solução e ao pacote de aplicativo existente. As referências de serviço e uma instância de serviço padrão serão adicionadas ao manifesto do aplicativo. O serviço será criado e iniciado na próxima vez que você implantar o aplicativo.

![O novo serviço será adicionado ao manifesto do aplicativo][newserviceapplicationmanifest]

## Empacotando o aplicativo da Malha do Serviço

Um pacote de aplicativo precisa ser criado para implantação do aplicativo e seus serviços em um cluster. O pacote organiza o manifesto do aplicativo, os manifestos do serviço e outros arquivos necessários em um layout específico. O Visual Studio configura e gerencia o pacote na pasta do projeto do aplicativo, no diretório 'pkg'. Clicar em **Pacote** no menu de contexto **Aplicativo** cria ou atualiza o pacote de aplicativos. Convém fazer isso se você implantar o aplicativo usando scripts personalizados do PowerShell.

## Removendo um aplicativo

Você pode remover o provisionamento de um tipo de aplicativo do seu cluster local usando o Gerenciador do Service Fabric. O gerenciador de cluster é acessível no ponto de extremidade do gateway HTTP do cluster (normalmente 19080 ou 19007), por exemplo, http://localhost:19080/Explorer. Isso reverterá as etapas de implantação descritas acima:

1. Remover as instâncias de aplicativo em execução
2. Cancelar o registro do tipo de aplicativo

![Remover um aplicativo](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

- [Modelo de aplicativo da Malha do Serviço](service-fabric-application-model.md)
- [Implantação de aplicativo da Malha do Serviço](service-fabric-deploy-remove-applications.md)
- [Gerenciando parâmetros do aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
- [Depurando o aplicativo da Malha do Serviço](service-fabric-debugging-your-application.md)
- [Visualizando o cluster usando o Gerenciador da Malha do Serviço](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[preservedate]: ./media/service-fabric-manage-application-in-visual-studio/preservedate.png

<!---HONumber=Nov15_HO4-->