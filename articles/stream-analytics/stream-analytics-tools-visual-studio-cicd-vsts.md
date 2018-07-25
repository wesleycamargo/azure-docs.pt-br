---
title: Implantar um trabalho do Azure Stream Analytics com CI/CD usando o tutorial do VSTS
description: Este artigo descreve como implantar um trabalho do Stream Analytics com CI/CD usando o VSTS.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: 303c1cfaf2b91712f706c5b78e027bb02739c770
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39074069"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>Tutorial: implantar um trabalho do Azure Stream Analytics com CI/CD usando o VSTS
Esse tutorial descreve como configurar a integração e a implantação contínua para um trabalho do Azure Stream Analytics usando o Visual Studio Team Services. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar controle do código-fonte ao seu projeto
> * Criar uma definição de build no Team Services
> * Criar uma definição de versão no Team Services
> * Implantar e atualizar automaticamente um aplicativo

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você tem:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale o [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) e as cargas de trabalho de **Desenvolvimento do Azure** ou **Armazenamento e processamento de dados**.
* Crie um [projeto do Stream Analytics no Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-vs).
* Crie uma conta do [Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="configure-nuget-package-dependency"></a>Configurar a dependência do pacote NuGet
Para realizar a compilação e a implantação automáticas em um computador aleatório, você precisa usar o pacote do NuGet `Microsoft.Azure.StreamAnalytics.CICD`. Ele fornece as ferramentas de MSBuild, execução local e implantação que dão suporte ao processo de implantação e integração contínua de projetos do Stream Analytics para Visual Studio. Para obter mais informações, consulte [Ferramentas de CI/CD do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md).

Adicione **packages.config** ao diretório do projeto.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Compartilhar sua solução do Visual Studio em um novo repositório Git do Team Services
Compartilhe os arquivos de origem do seu aplicativo em um projeto de equipe no Team Services, para que você possa gerar builds.  

1. Crie um novo repositório Git local para seu projeto escolhendo **Adicionar ao controle do código-fonte** e **Git** na barra de status no canto inferior direito do Visual Studio. 

2. Na exibição **Sincronização** no **Team Explorer**, escolha o botão **Publicar Repositório Git** em **Enviar por Push para o Visual Studio Team Services**.

   ![Enviar por push o repositório Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Verifique seu email e selecione sua conta na lista suspensa **Domínio do Team Services**. Digite o nome do seu repositório e selecione **Publicar Repositório**.

   ![Enviar por push o repositório Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    A publicação do repositório cria um novo projeto de equipe em sua conta com o mesmo nome do repositório local. Para criar o repositório em um projeto de equipe existente, clique em **Avançado** ao lado do **nome do Repositório** e escolha um projeto de equipe. Você pode exibir seu código no navegador escolhendo **Vê-lo na Web**.
 
## <a name="configure-continuous-delivery-with-vsts"></a>Configurar a entrega contínua com VSTS
Uma definição de build do Team Services descreve um fluxo de trabalho composto por etapas de build que são executadas sequencialmente. Saiba mais sobre as [definições de build do Team Services](https://www.visualstudio.com/docs/build/define/create). 

Uma definição de versão do Team Services descreve um fluxo de trabalho que implanta um pacote de aplicativos em um cluster. Quando usadas juntas, a definição de build e a definição de versão executam todo o fluxo de trabalho, começando com os arquivos de origem e terminando com um aplicativo em execução em seu cluster. Saiba mais sobre as [definições de versão](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)do Team Services.

### <a name="create-a-build-definition"></a>Criar a definição de build
Abra um navegador da Web e navegue até o projeto de equipe que você acabou de criar em [Visual Studio Team Services](https://app.vsaex.visualstudio.com/). 

1. Na guia **Build e versão**, escolha **Builds**e **+Novo**.  Escolha **GIT do VSTS** e **Continuar**.
    
    ![Selecionar a origem](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. Em **Selecionar um modelo**, clique em **Processo vazio** para começar com uma definição vazia.
    
    ![Escolher o modelo de build](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. Em **Gatilhos**, habilite a integração contínua marcando o status do gatilho **Habilitar a integração contínua**.  Selecione **Salvar e enfileirar** para iniciar uma compilação manualmente. 
    
    ![Status do gatilho](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. Os builds também serão disparados no envio por push ou no check-in. Para verificar o progresso da compilação, alterne para a guia **Compilações**.  Assim que verificar que o build é executado com êxito, configure uma definição de versão que implante seu aplicativo em um cluster. Clique com o botão direito nas elipses ao lado de sua definição de build e escolha **Editar**.

5.  Em **Tarefas**, insira "Hospedado" como a **Fila do agente**.
    
    ![Escolha fila do agente](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. Em **Fase 1**, clique em **+** e adicione uma tarefa do **NuGet**.
    
    ![Adicionar uma tarefa do NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Expanda **Avançado** e adicione `$(Build.SourcesDirectory)\packages` ao **Diretório de destino**. Mantenha os valores de configuração padrão restantes do NuGet.

   ![Configurar uma tarefa do NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. Em **Fase 1**, clique em **+** e adicione uma tarefa do **MSBuild**.

   ![Adicionar uma tarefa do MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Altere os **Argumentos do MSBuild** para o seguinte:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Configurar a tarefa do MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. Em **Fase 1**, clique em **+** e adicione uma tarefa de **Implantação do Grupo de Recursos do Azure**. 
    
    ![Adicionar uma tarefa de Implantação do Grupo de Recursos do Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Expanda os **Detalhes do Azure** e preencha a configuração da seguinte forma:
    
    |**Configuração**  |**Valor sugerido**  |
    |---------|---------|
    |Assinatura  |  Escolha sua assinatura.   |
    |Ação  |  Criar ou atualizar o grupo de recursos   |
    |Grupo de recursos  |  Insira um nome de grupo de recursos.   |
    |Modelo  | [O caminho da sua solução]\bin\Debug\Deploy\\[O nome do seu projeto].JobTemplate.json   |
    |Parâmetros de modelo  | [O caminho da sua solução]\bin\Debug\Deploy\\[O nome do seu projeto].JobTemplate.parameters.json   |
    |Substituir parâmetros de modelo  | Digite os parâmetros de modelo para substituir a na caixa de texto. Exemplo, –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Essa propriedade é opcional, mas seu build apresentará erros se parâmetros principais não forem substituídos.    |
    
    ![Definir propriedades](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. Clique em **Salvar e enfileirar** para testar a definição de build.
    
    ![Definir parâmetros de substituição](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Falha no processo de build
Você poderá receber erros para os parâmetros de implantação nulos se não tiver substituído os parâmetros de modelo na tarefa **Implantação do Grupo de Recursos do Azure** de sua definição de build. Retorne à definição do build e substitua os parâmetros nulos para resolver o erro.

   ![Falha no processo de build](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Confirmar e enviar alterações por push para disparar uma versão
Verifique se o pipeline de integração contínua está funcionando ao fazer check-in de algumas alterações de código no Team Services.    

Ao escrever seu código, suas alterações são rastreadas automaticamente pelo Visual Studio. Confirme as alterações em seu repositório Git local escolhendo o ícone de alterações pendentes na barra de status no canto inferior direito.

1. Na exibição **Alterações** no Team Explorer, adicione uma mensagem que descreva a atualização e confirme suas alterações.

    ![Confirmar e enviar alterações por push](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Escolha o ícone de barra de status de alterações não publicadas ou a exibição de sincronização no Team Explorer. Selecione **Push** para atualizar seu código no Team Services/TFS.

    ![Confirmar e enviar alterações por push](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

Enviar por push as alterações ao Team Services dispara um build automaticamente.  Quando a definição de build é concluída com êxito, uma versão é criada automaticamente e inicia a atualização do trabalho no cluster.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A exclusão do trabalho evita a cobrança das unidades de streaming consumidas por ele. Se você está planejando usar o trabalho no futuro, poderá interrompê-lo e reiniciar mais tarde, quando necessário. Se você não for mais usar o trabalho, exclua todos os recursos criados neste tutorial usando as seguintes etapas:

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado.  
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para saber como usar as ferramentas do Azure Stream Analytics para Visual Studio para configurar um processo de implantação e de integração contínua, prossiga para o artigo de configuração do pipeline de CI/CD:

> [!div class="nextstepaction"]
> [Integrar e desenvolver continuamente com ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)