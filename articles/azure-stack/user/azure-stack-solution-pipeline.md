---
title: Implantar seu aplicativo no Azure e o Azure pilha | Microsoft Docs
description: "Saiba como implantar aplicativos do Azure e a pilha do Azure com um pipeline de CI/CD híbrida."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 6292a846a2c3d7e112558ef0d2b62b3a3fdd3c51
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Implantar aplicativos do Azure e o Azure pilha
*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Um híbrido [integração contínua](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[fornecimento contínuo](https://www.visualstudio.com/learn/what-is-continuous-delivery/)pipeline CI/CD () permite que você criar, testar e implantar seu aplicativo em várias nuvens.  Neste tutorial, você deve criar um ambiente de exemplo para saber como um pipeline de CI/CD híbrida pode ajudá-lo:
 
> [!div class="checklist"]
> * Inicie uma nova compilação com base em confirmações de código ao seu repositório do Visual Studio Team Services (VSTS).
> * Implante automaticamente o código recém-criado para o Azure para testes de aceitação do usuário.
> * Quando o seu código de teste, implante automaticamente com a pilha do Azure. 


## <a name="prerequisites"></a>Pré-requisitos
Alguns componentes são necessários para criar um pipeline de CI/CD híbrida e podem levar algum tempo para se preparar.  Se você já tiver alguns desses componentes, verifique se que eles atenderem aos requisitos antes de começar.

Este tópico também pressupõe que você tenha algum conhecimento do Azure e a pilha do Azure. Se você quiser saber antes de prosseguir, certifique-se de que comece por estes tópicos:

- [Introdução ao Azure](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Principais conceitos de pilha do Azure](../azure-stack-key-features.md)

### <a name="azure"></a>As tabelas
 - Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
 - Criar um [aplicativo Web](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)e configurá-la para [de publicação FTP](../../app-service/app-service-deploy-ftp.md).  Anote o novo URL do aplicativo Web, como ele é usado mais tarde.


### <a name="azure-stack"></a>Azure Stack
 - [Implantar o Azure pilha](../azure-stack-run-powershell-script.md).  A instalação levará algumas horas para ser concluída, por isso, planeje adequadamente.
 - Implantar [do serviço de aplicativo](../azure-stack-app-service-deploy.md) serviços de PaaS a pilha do Azure.
 - Criar um aplicativo Web e configurá-la para [de publicação FTP](../azure-stack-app-service-enable-ftp.md).  Anote o novo URL do aplicativo Web, como ele é usado mais tarde.  

### <a name="developer-tools"></a>Ferramentas de desenvolvedor
 - Criar um [espaço de trabalho do VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).  O processo de inscrição cria um projeto chamado "MyFirstProject".  
 - [Instalar o Visual Studio de 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [entrar no VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio)
 - Conecte-se ao projeto e [clonar localmente](https://www.visualstudio.com/docs/git/gitquickstart).
 - Criar um [pool de agente](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues) no VSTS.
 - Instalar o Visual Studio e implantar um [agente de compilação do VSTS](https://www.visualstudio.com/docs/build/actions/agents/v2-windows) a uma máquina virtual na pilha do Azure. 
 

## <a name="create-app--push-to-vsts"></a>Criar aplicativo e enviar por push para VSTS

### <a name="create-application"></a>Criar aplicativo
Nesta seção, você cria um aplicativo simples ASP.NET e enviar por push para VSTS.  Essas etapas representam o fluxo de trabalho do desenvolvedor normais e podem ser adaptadas para linguagens e ferramentas de desenvolvedor. 

1.  Abra o Visual Studio.
2.  Espaço do Team Explorer do e **soluções...**  área, clique em **novo**.
3.  Selecione **Visual C#** > **Web** > **aplicativo Web do ASP.NET (.NET Framework)**.
4.  Forneça um nome para o aplicativo e clique em **Okey**.
5.  Na próxima tela, mantenha os padrões (formulários da Web) e clique em **Okey**.

### <a name="commit-and-push-changes-to-vsts"></a>Confirmar e enviar alterações por push para VSTS
1.  Usando o Team Explorer no Visual Studio, selecione o menu suspenso e clique em **alterações**.
2.  Forneça uma mensagem de confirmação e selecione **confirmar todos os**. Você pode ser solicitado a salvar o arquivo de solução, clique em Sim para salvar todos os.
3.  Uma vez confirmado, o Visual Studio oferece para sincronizar alterações ao seu projeto. Selecione **Sincronizar**.

    ![Imagem mostrando a tela de confirmação após a conclusão de confirmação](./media/azure-stack-solution-pipeline/image1.png)

4.  Na guia sincronização, em *saída*, você verá sua confirmação de novo.  Selecione **Push** para sincronizar a alteração VSTS.

    ![etapas de sincronização de Mostrar imagem](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>Examine o código no VSTS
Depois de ter confirmado uma alteração e enviados por push para VSTS, verifique o seu código do portal do VSTS.  Selecione **código**e, em seguida, **arquivos** no menu suspenso.  Você pode ver a solução que você criou.

## <a name="create-build-definition"></a>Criar definição de build
O processo de compilação define como o aplicativo é criado e empacotado para implantação em cada confirmação das alterações de código. Em nosso exemplo, usamos o modelo incluído para configurar o processo de compilação para um aplicativo ASP.NET, embora essa configuração pode ser adaptada dependendo do seu aplicativo.

1.  Faça logon no seu espaço de trabalho do VSTS em um navegador da web.
2.  Na faixa, selecione **Build e versão** e **cria**.
3.  Clique em **+ nova definição**.
4.  Na lista de modelos, selecione **ASP.NET (visualização)** e selecione **aplicar**.
5.  Modificar o *argumentos de MSBuild* campo *compilar solução* etapa para:

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  Selecione o **opções** guia e selecione a fila do agente para o agente de compilação é implantado em uma máquina virtual na pilha do Azure. 
7.  Selecione o **gatilhos** guia e habilitar **integração contínua**.
7.  Clique em **Salvar & fila** e, em seguida, selecione **salvar** na lista suspensa. 

## <a name="create-release-definition"></a>Criar definição de versão
O processo de liberação define como as compilações da etapa anterior são implantadas em um ambiente.  Neste tutorial, publicamos nosso aplicativo ASP.NET com o FTP para um aplicativo Web do Azure. Para configurar uma versão do Azure, use as seguintes etapas:

1.  Na faixa VSTS, selecione **Build e versão** e **versões**.
2.  Clique em verde **+ nova definição**.
3.  Selecione **vazio** e clique em **próximo**.
4.  Marque a caixa de *implantação contínua*e, em seguida, clique em **criar**.

Agora que você criou uma definição de versão vazia e vinculada a ele para a compilação, adicionamos as etapas para o ambiente do Azure:

1.  Clique em verde  **+**  para adicionar tarefas.
2.  Selecione **todos os**e, em seguida, na lista, adicione **FTP carregar** e selecione **fechar**.
3.  Selecione o **FTP carregar** tarefa você acabou de adicionar e configurar os seguintes parâmetros:
    
    | Parâmetro | Valor |
    | ----- | ----- |
    |Método de autenticação| Insira as credenciais|
    |URL do Servidor | A URL de FTP de aplicativo Web é recuperado do portal do Azure |
    |Nome de Usuário | Nome de usuário configurado ao criar credenciais de FTP para o aplicativo Web |
    |Senha | Senha criados durante o estabelecimento de credenciais de FTP para o aplicativo Web|
    |Diretório de Origem | $(System.DefaultWorkingDirectory)\**\ |
    |Diretório remoto | /site/wwwroot/ |
    |Preservar os caminhos de arquivo | Ativado (marcado)|

4.  Clique em **Salvar**

Por fim, você pode configurar a definição de versão para usar o pool de agente que contém o agente implantado usando as seguintes etapas:
1.  Selecione a definição de versão e clique em **editar**.
2.  Selecione **executados em agente** da coluna do meio.  Na coluna à direita, selecione a fila de agente que contém o agente de compilação em execução na pilha do Azure.  
    ![configuração de Mostrar imagem de definição de versão para usar a fila específica](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>Implantar seu aplicativo no Azure
Esta etapa usa seu pipeline de CI/CD recém-criado para implantar o aplicativo ASP.NET em um aplicativo Web no Azure. 

1.  Na faixa no VSTS, selecione **Build e versão**e, em seguida, selecione **cria**.
2.  Clique em **...**  na definição de compilação criada anteriormente e selecione **enfileirar nova compilação**.
3.  Aceite os padrões e clique em **Okey**.  A compilação começa e exibe o progresso.
4.  Quando a compilação for concluída, você pode acompanhar o status, selecionando **Build e versão** e selecionando **versões**.
5.  Após a compilação for concluída, visite o site usando a URL observada ao criar o aplicativo Web.    


## <a name="add-azure-stack-to-pipeline"></a>Adicionar a pilha do Azure para o pipeline
Agora que você testou o pipeline de CI/CD implantando para o Azure, é hora de adicionar a pilha do Azure para o pipeline.  Nas etapas a seguir, você cria um novo ambiente e adiciona uma tarefa de carregamento de FTP para implantar seu aplicativo no Azure pilha.  Você também pode adicionar um aprovador de versão, que serve como uma maneira de simular "Aprovar" em uma versão de código para a pilha do Azure.  

1.  Na definição de lançamento, selecione **+ adicionar ambiente** e **criar novo ambiente**.
2.  Selecione **vazio**, clique em **próximo**.
3.  Selecione **usuários específicos** e especificar sua conta.  Selecione **Criar**.
4.  Renomear o ambiente, selecionando o nome existente e digitando *Azure pilha*.
5.  Agora, a seleção do ambiente da pilha do Azure, em seguida, selecione **adicionar tarefas**.
6.  Selecione o **FTP carregar** tarefas e selecione **adicionar**, em seguida, selecione **fechar**.


### <a name="configure-ftp-task"></a>Configurar a tarefa de FTP
Agora que você criou uma versão, você configurará as etapas necessárias para publicar o aplicativo Web na pilha do Azure.  Como você configurou a tarefa de Upload de FTP para o Azure, você configura a tarefa para a pilha do Azure:

1.  Selecione o **FTP carregar** tarefa você acabou de adicionar e configurar os seguintes parâmetros:
    
    | Parâmetro | Valor |
    | -----     | ----- |
    |Método de autenticação| Insira as credenciais|
    |URL do Servidor | A URL de FTP de aplicativo Web é recuperado do portal do Azure pilha |
    |Nome de Usuário | Nome de usuário configurado ao criar credenciais de FTP para o aplicativo Web |
    |Senha | Senha criados durante o estabelecimento de credenciais de FTP para o aplicativo Web|
    |Diretório de Origem | $(System.DefaultWorkingDirectory)\**\ |
    |Diretório remoto | /site/wwwroot/|
    |Preservar os caminhos de arquivo | Ativado (marcado)|

2.  Clique em **Salvar**

Finalmente, configure a definição de versão para usar o pool de agente que contém o agente implantado usando as seguintes etapas:
1.  Selecione a definição de versão e clique em **editar**
2.  Selecione **executados em agente** da coluna do meio. Na coluna à direita, selecione a fila de agente que contém o agente de compilação em execução na pilha do Azure.  
    ![configuração de Mostrar imagem de definição de versão para usar a fila específica](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>Implantar novo código
Agora você pode testar o pipeline de CI/CD híbrido, com a etapa final de publicação para a pilha do Azure.  Nesta seção, você modificar o rodapé do site e iniciar a implantação por meio do pipeline.  Uma vez concluído, você verá as alterações implantadas no Azure para análise e, em seguida, quando você aprovar a versão, eles são publicados para a pilha do Azure.

1. No Visual Studio, abra o *site.master* de arquivo e altere esta linha:
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    para isto:

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  Confirmar as alterações e sincronizar com VSTS.  
4.  O espaço de trabalho do VSTS, verificar o status de compilação selecionando **Build e versão** > **de compilação**
5.  Você verá uma compilação em andamento.  Clique duas vezes o status, e você pode assistir o progresso da compilação.  Depois que você vê "Compilação concluída" no console do, vá para verificar a versão do **Build e versão** > **versão**.  Clique duas vezes o lançamento.
6.  Você receberá uma versão requer a revisão de notificação. Verifique a URL do aplicativo Web e verifique se que as novas alterações estão presentes.  Aprove a versão no VSTS.
    ![configuração de Mostrar imagem de definição de versão para usar a fila específica](./media/azure-stack-solution-pipeline/image4.png)
    
7.  Verifique se a publicação com a pilha do Azure estiver concluída, visitando o site usando a URL observada ao criar o aplicativo Web.
    ![Imagem mostrando o ASP. Aplicativo nEt com rodapé alterado](./media/azure-stack-solution-pipeline/image5.png)


Agora você pode usar o novo pipeline de CI/CD híbrida como um bloco de construção para outros padrões de nuvem híbrida.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar um híbrido CI/CD pipeline que:

> [!div class="checklist"]
> * Inicia uma nova compilação com base no código confirma a seu repositório do Visual Studio Team Services (VSTS).
> * Implanta automaticamente o seu código recém-criado para o Azure para testes de aceitação do usuário.
> * Quando o seu código de teste implanta automaticamente a pilha do Azure. 

Agora que você tem um pipeline de CI/CD híbrida, continue para aprender a desenvolver aplicativos para a pilha do Azure.

> [!div class="nextstepaction"]
> [Desenvolver para o Azure Stack](azure-stack-developer.md)


