---
title: Tarefa de DevOps do Azure para o Data Explorer do Azure
description: Neste tópico, você aprenderá a criar um pipeline de lançamento e implantar
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: a70a887ccb19d9c1cbdb5f8ebf6aa8d4b25a0dfd
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65161064"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Tarefa de DevOps do Azure para o Gerenciador de dados do Azure

[Os serviços do Azure DevOps](https://azure.microsoft.com/services/devops/) fornece ferramentas de colaboração como pipelines de alto desempenho, os repositórios Git privados gratuitos, quadros Kanban configuráveis e recursos abrangentes de testes automatizados e contínuos de desenvolvimento. [Pipelines do Azure](https://azure.microsoft.com/services/devops/pipelines/) é um recurso de DevOps do Azure que permite que você gerencie o CI/CD para implantar seu código com pipelines de alto desempenho que funcionam com qualquer linguagem, a plataforma e a nuvem.
[Gerenciador de dados do Azure - Admin comandos](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) é a tarefa de Pipelines do Azure que permite que você crie pipelines de liberação e implantar seu banco de dados é alterado para seus bancos de dados do Data Explorer do Azure. Ele está disponível gratuitamente na [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Este documento descreve um exemplo simples sobre o uso do **Data Explorer do Azure – administrador comandos** tarefas para implantar o seu esquema é alterado para seu banco de dados. Para pipelines de CI/CD completos, consulte [documentação do Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Configuração do Cluster do Gerenciador de dados do Azure:
    * Um [cluster Data Explorer do Azure e banco de dados](/azure/data-explorer/create-cluster-database-portal)
    * Criar aplicativo do Azure Active Directory (Azure AD) por [provisionamento de um aplicativo do Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Conceder acesso ao aplicativo do AD do Azure em seu banco de dados do Data Explorer do Azure por [gerenciamento de permissões de banco de dados do Data Explorer do Azure](/azure/data-explorer/manage-database-permissions).
* Configuração do DevOps do Azure:
    * [Inscreva-se para uma organização livre](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Criar uma organização](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Criar um projeto no DevOps do Azure](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Código com Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Criar pastas

Crie as seguintes pastas de exemplo (*funções*, *diretivas*, *tabelas*) em seu repositório Git. Copie os arquivos da [aqui](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) nas respectivas pastas, como visto abaixo e confirmar as alterações. Os arquivos de exemplo são fornecidos para executar o seguinte fluxo de trabalho.

![Criar pastas](media/devops/create-folders.png)

> [!TIP]
> Ao criar seu próprio fluxo de trabalho, é recomendável fazer idempotente seu código. Por exemplo, use [tabela de mesclagem. Crie](/azure/kusto/management/tables#create-merge-tables) em vez de [criar tabela](/azure/kusto/management/tables#create-table)e usar [criar ou alterar](/azure/kusto/management/functions#create-or-alter-function) de função em vez de [. Crie](/azure/kusto/management/functions#create-function) função.

## <a name="create-a-release-pipeline"></a>Criar um pipeline de lançamento

1. Entrar no seu [organização de DevOps do Azure](https://dev.azure.com/).
1. Selecione **Pipelines** > **versões** no menu esquerdo e selecione **novo pipeline**.

    ![Novo pipeline](media/devops/new-pipeline.png)

1. O **novo pipeline da versão** janela é aberta. No **Pipelines** guia, o **selecionar um modelo** painel, selecione **trabalhos vazias**.

     ![Selecione um modelo](media/devops/select-template.png)

1. Selecione **estágio** botão. Na **estágio** painel, adicione o **nome do estágio**. Selecione **salvar** para salvar seu pipeline.

    ![O estágio de nome](media/devops/stage-name.png)

1. Selecione **adicionar um artefato** botão. No **adicionar um artefato** painel, selecione o repositório em que seu código existe, preencha as informações relevantes e clique em **Add**. Selecione **salvar** para salvar seu pipeline.

    ![Adicionar um artefato](media/devops/add-artifact.png)

1. No **variáveis** guia, selecione **+ adicionar** para criar uma variável para o **URL de ponto de extremidade** que será usado na tarefa. Gravar o **nome** e o **valor** do ponto de extremidade. Selecione **salvar** para salvar seu pipeline. 

    ![Criar variável](media/devops/create-variable.png)

    Para localizar seu Endpoint_URL, a página de visão geral de seu **Cluster do Azure Data Explorer** no Azure portal contém o URI de cluster do Data Explorer do Azure. Construir o URI no seguinte formato `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Por exemplo, https://kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![URI do cluster Data Explorer do Azure](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Criar tarefas para implantar

1. No **Pipeline** guia, clique em **1 trabalho, 0 tarefa** para adicionar tarefas. 

    ![Adicionar tarefas](media/devops/add-task.png)

1. Crie três tarefas para implantar **tabelas**, **funções**, e **políticas**, nessa ordem. 

1. No **tarefas** guia, selecione **+** pelo **trabalho do agente**. Pesquise **Azure Data Explorer**. Na **Marketplace**, instale o **Data Explorer do Azure – administrador comandos** extensão. Em seguida, selecione **Add** na **executar comando de Gerenciador de dados do Azure**.

     ![Adicionar comandos de administração](media/devops/add-admin-commands.png)

1. Clique em **Kusto comando** à esquerda e atualizar a tarefa com as seguintes informações:
    * **Nome de exibição**: Nome da tarefa
    * **Caminho do arquivo**: No **tabelas** da tarefa, especifique */Tables/{nome_da_tabela}/Rows*.csl, pois os arquivos de criação de tabela estão no *tabela* pasta.
    * **URL de ponto de extremidade**: insira o `EndPoint URL`variável criada na etapa anterior.
    * Selecione **ponto de extremidade de serviço de uso** e selecione **+ novo**.

    ![Atualizar tarefa de comando do Kusto](media/devops/kusto-command-task.png)

1. Conclua as seguintes informações na **conexão de serviço do Gerenciador de dados do Azure adicionar** janela:

    |Configuração  |Valor sugerido  |
    |---------|---------|
    |**Nome da conexão**     |    Insira um nome para identificar esse ponto de extremidade de serviço     |
    |**Url do cluster**    |    Valor pode ser encontrado na seção Visão geral do seu Cluster do Azure Data Explorer no portal do Azure | 
    |**Id da entidade de serviço**    |    Insira a ID de aplicativo do AAD (criado como pré-requisito)     |
    |**Chave de entidade de serviço do aplicativo**     |    Insira a chave de aplicativo do AAD (criado como pré-requisito)    |
    |**Id de locatário do AAD**    |      Insira o seu locatário do AAD (por exemplo, microsoft.com, contoso.com...)    |

    Selecione **permitir todos os pipelines usar essa conexão** caixa de seleção. Selecione **OK**.

    ![Adicionar conexão de serviço](media/devops/add-service-connection.png)

1. Repita etapas 1 a 5 outro duas vezes para implantar os arquivos do *funções* e *diretivas* pastas. Clique em **Salvar**. No **tarefas** guia, consulte as três tarefas criadas: **Implantar tabelas**, **implantar funções**, e **implantar políticas**.

    ![Implantar todas as pastas](media/devops/deploy-all-folders.png)

1. Selecione **+ versão** > **criar versão** para criar uma versão.

    ![Criar uma versão](media/devops/create-release.png)

1. No **Logs** guia, verifique o status da implantação é bem-sucedida.

    ![A implantação foi bem-sucedida](media/devops/deployment-successful.png)

Agora você concluiu a criação de um pipeline de lançamento para a implantação de três tarefas para pré-produção.