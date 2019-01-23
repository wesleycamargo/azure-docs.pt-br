---
title: Integração do controle do código-fonte na Automação do Azure
description: Este artigo descreve a integração de controle de origem com o GitHub na Automação do Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 94d948267286ad716aa200cb5aa858acf2eb2624
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330753"
---
# <a name="source-control-integration-in-azure-automation"></a>Integração de controle de origem na Automação do Azure

O controle do código-fonte permite que você mantenha os runbooks da sua conta de Automação atualizados com seus scripts no repositório de controle do código-fonte do GitHub ou Azure Dev Ops. O controle de origem permite que você colabore com facilidade com sua equipe, controle alterações e reverta para versões anteriores de seus runbooks. Por exemplo, o controle do código-fonte permite sincronizar diferentes branches no controle do código-fonte com suas contas de Automação de desenvolvimento, teste ou produção. Isso facilita a promoção de código que foi testado em seu ambiente de desenvolvimento para sua conta de Automação de produção.

A Automação do Azure é compatível com três tipos de controle do código-fonte:

* GitHub
* Azure DevOps (Git)
* Azure DevOps (TFVC)

## <a name="pre-requisites"></a>Pré-requisitos

* Um repositório de controle do código-fonte (GitHub ou Azure DevOps)
* As [permissões](#personal-access-token-permissions) corretas para o repositório de controle do código-fonte
* Uma [conta Executar como e conexão](manage-runas-account.md)

> [!NOTE]
> Trabalhos de sincronização de controle do código-fonte são executados na Conta de Automação do usuário e são cobrados conforme a mesma taxa que outros trabalhos de Automação.

## <a name="configure-source-control"></a>Configurar o controle de origem

Dentro da sua Conta de Automação, selecione **Controle do código-fonte (Visualização)** e clique em **+ Adicionar**

![Selecionar controle do código-fonte](./media/source-control-integration/select-source-control.png)

Escolha **Tipo de controle do código-fonte** e clique em **Autenticar**.

Analise a página de permissões de solicitação do aplicativo e clique em **Aceitar**.

Na página **Resumo de controle do código-fonte**, preencha as informações e clique em **Salvar**. A tabela a seguir fornece uma descrição dos campos disponíveis.

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|Nome do controle do código-fonte     | Um nome amigável para o controle do código-fonte        |
|Tipo de controle do código-fonte     | O tipo de origem do controle do código-fonte. As opções disponíveis são:</br> GitHub</br>Azure DevOps (Git)</br> Azure DevOps (TFVC)        |
|Repositório     | O nome do repositório ou projeto. O pull desse valor é efetuado do repositório do controle do código-fonte. Exemplo: $/ContosoFinanceTFVCExample         |
|Branch     | O branch do qual o pull dos arquivos de origem é efetuado. O direcionamento de branch não está disponível para o tipo de controle do código-fonte TFVC.          |
|Caminho da pasta     | A pasta que contém os runbooks a serem sincronizados. Exemplo: /Runbooks         |
|Sincronização automática     | Ativa ou desativa a sincronização automática quando uma confirmação é feita no repositório de controle do código-fonte         |
|Publicar runbook     | Se definido como **Ativado**, os runbooks serão automaticamente publicados após serem sincronizados com o controle do código-fonte.         |
|DESCRIÇÃO     | Um campo de texto para fornecer detalhes adicionais        |

![Resumo do controle do código-fonte](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Verifique se você está conectado com a conta correta ao configurar o controle do código-fonte. Se houver alguma dúvida, abra uma nova guia no navegador e faça logoff do visualstudio.com ou do github.com e tente conectar o controle do código-fonte novamente.

## <a name="syncing"></a>Sincronização

Configurando a sincronização automática ao configurar a integração do controle do código-fonte, a sincronização inicial começa automaticamente. Se sincronização automática não estiver definida, selecione o código-fonte da tabela na página **Controle do código-fonte (Visualização)**. Clique em **Iniciar sincronização** para iniciar o processo de sincronização.

É possível exibir o status do trabalho de sincronização atual ou anteriores clicando na guia **Trabalho de sincronização**. Na lista suspensa **Controle do código-fonte**, selecione um controle do código-fonte.

![Status da sincronização](./media/source-control-integration/sync-status.png)

Clicar em um trabalho permite exibir a saída do trabalho. O exemplo a seguir é a saída de um trabalho de sincronização do controle do código-fonte.

```output
========================================================================================================

Azure Automation Source Control Public Preview.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

## <a name="personal-access-token-permissions"></a>Permissões do token de acesso pessoal

O controle do código-fonte requer algumas permissões mínimas para tokens de acesso pessoal. As tabelas a seguir contêm as permissões mínimas necessárias para o GitHub e o Azure DevOps.

### <a name="github"></a>GitHub

|Escopo  |DESCRIÇÃO  |
|---------|---------|
|**repositório**     |         |
|repo:status     | Acessar status de confirmação         |
|repo_deployment      | Acessar status de implantação         |
|public_repo     | Repositórios públicos de acesso         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Escrever ganchos de repositório         |
|read:repo_hook|Ler ganchos de repositório|

### <a name="azure-devops"></a>Azure DevOps

|Escopo  |
|---------|
|Código (leitura)     |
|Projeto e equipe (leitura)|
|Identidade (leitura)      |
|Perfil do usuário (leitura)     |
|Itens de trabalho (leitura)    |
|Conexões de serviço (ler, consultar e gerenciar)<sup>1</sup>    |

<sup>1</sup>a permissão Conexões de Serviço só será necessária se você tiver habilitado a sincronização automática.

## <a name="disconnecting-source-control"></a>Desconectando o controle de origem

Para desconectar-se de um repositório de controle do código-fonte, abra **Controle do código-fonte (Visualização)** em **Configurações de conta** na sua conta de Automação.

Selecione o controle do código-fonte que você deseja remover. Na página **Resumo do controle do código-fonte**, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Para aprender mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
