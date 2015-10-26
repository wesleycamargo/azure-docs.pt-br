<properties 
   pageTitle="Adicionar Armazenamento do Azure usando serviços conectados no Visual Studio | Microsoft Azure"
   description="Adicionar Armazenamento do Azure ao seu aplicativo usando a caixa de diálogo Adicionar Serviços Conectados do Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="08/12/2015"
   ms.author="patshea" />

# Adicionando armazenamento do Azure usando os serviços conectados do Visual Studio

## Visão geral

Com o Visual Studio 2015, você pode conectar qualquer serviço de nuvem do C#, serviço móvel back-end do .NET, site ou serviço ASP.NET, serviço 5 ASP.NET ou serviço WebJob do Azure ao Armazenamento do Azure usando a caixa de diálogo **Adicionar Serviços Conectados**. A funcionalidade do serviço conectado adiciona todas as referências necessárias e o código de conexão, bem como modifica os arquivos de configuração adequadamente. A caixa de diálogo também dá acesso à documentação que informa quais são as próximas etapas para iniciar o armazenamento de blob, as filas e as tabelas.

## Tipos de projeto com suporte

Você pode usar a caixa de diálogo Serviços Conectados para se conectar ao Armazenamento do Azure nos tipos de projeto a seguir.

- Projetos Web ASP.NET

- Projetos 5 ASP.NET

- Projetos de Função de Trabalho e Função Web do serviço de nuvem do .NET

- Projetos de serviços móveis do .NET

- Projetos WebJob do Azure


## Conectar-se ao Armazenamento do Azure usando a caixa de diálogo Serviços Conectados

1. Verifique se você tem uma conta do Azure. Se não tiver uma conta do Azure, poderá se inscrever para uma [avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=518146). Quando você tiver uma conta Azure, poderá criar contas de armazenamento, de serviços móveis, e configurar o Active Directory do Azure.

1. Abra o projeto no Visual Studio, abra o menu de contexto do nó **Referências** no Gerenciador de Soluções e escolha **Adicionar Serviço Conectado**.

    ![Adicionando um serviço conectado](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Na caixa de diálogo **Adicionar Serviço Conectado**, escolha **Armazenamento do Azure**, e, em seguida, escolha o botão **Configurar**. Você será solicitado a fazer logon no Azure, se ainda não o fez.

    ![Caixa de diálogo Adicionar Serviço Conectado — Armazenamento](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. Na caixa de diálogo **Armazenamento do Azure**, escolha uma conta de armazenamento existente e clique em **Adicionar**.

    Se você precisar criar uma nova conta de armazenamento, passe para a próxima etapa. Caso contrário, pule para a etapa 6.

    ![Caixa de diálogo Armazenamento do Azure](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. Para criar uma nova conta de armazenamento:

    1. Escolha o botão **Criar uma Nova Conta de Armazenamento** na parte inferior da caixa de diálogo Armazenamento do Azure.

    1. Preencha a caixa de diálogo **Criar Conta de Armazenamento** e escolha o botão **Criar**.
    
        ![Caixa de diálogo Armazenamento do Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        Quando estiver de volta na caixa de diálogo **Armazenamento do Azure**, o novo armazenamento aparecerá na lista.

    1. Selecione o novo armazenamento na lista e clique em **Adicionar**.

1. O serviço conectado de armazenamento aparece sob o nó Referências de Serviço do seu projeto WebJob.

    ![Armazenamento do Azure no projeto de trabalhos na Web](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. Examine a página de introdução que aparece e descubra como o projeto foi modificado. Uma página de introdução aparece no navegador sempre que você adiciona um serviço conectado. É possível examinar as próximas etapas sugeridas e os exemplos de código ou alternar para a página "O que aconteceu" para ver quais referências foram adicionadas ao projeto e como o código e os arquivos de configuração foram modificados.

## Como o projeto é modificado

Quando você acaba de fazer suas escolhas na caixa de diálogo, o Visual Studio adiciona referências e modifica determinados arquivos de configuração. As alterações específicas dependem do tipo de projeto.

 - Para projetos ASP.NET, consulte [O que aconteceu — projetos ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126). 
 - Para projetos 5 ASP.NET, consulte [O que aconteceu — projetos 5 ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
 - Para projetos de serviço de nuvem (funções Web e funções de trabalho), consulte [O que aconteceu — projetos de serviço de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=516965). 
 - Para projetos de Trabalho Web, consulte [O que aconteceu — projetos de Trabalho Web](vs-storage-webjobs-what-happened/).

## Próximas etapas

1. Usando os exemplos de código de Introdução como um guia, crie o tipo de armazenamento que deseja e, em seguida, comece a escrever o código para acessar sua conta de armazenamento!

1. Faça perguntas e obtenha ajuda
     - [Fórum do MSDN: Armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)

     - [Armazenamento em azure.microsoft.com](http://azure.microsoft.com/services/storage)

     - [Documentação do Armazenamento em azure.microsoft.com](http://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=Oct15_HO3-->