---
title: Integrar ambientes em Pipelines do Azure no Azure DevTest Labs | Microsoft Docs
description: Aprenda a integrar ambientes do Azure DevTest Labs em seu Azure DevOps CI (integração contínua) e os pipelines de entrega contínua (CD).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357399"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrar ambientes em seus pipelines do Azure DevOps CI/CD
Você pode usar a extensão de tarefas do Azure DevTest Labs é instalada nos serviços de DevOps do Azure (anteriormente conhecido como Visual Studio Team Services) para integrar facilmente a CI (integração contínua) / recurso build e versão entrega contínua (CD) de pipeline com o Azure DevTest Labs. Essas extensões torná-lo mais fácil de implantar rapidamente uma [ambiente](devtest-lab-test-env.md) para uma determinada tarefa de teste e, em seguida, excluí-lo quando o teste for concluído. 

Este artigo mostra como criar e implantar um ambiente e, em seguida, excluir o ambiente, tudo em um pipeline completo. Você normalmente executaria cada uma dessas tarefas individualmente em seu próprio pipeline personalizado de build-teste-implantação. As extensões usadas neste artigo são Além dessas [criar/excluir tarefas da VM de DTL](devtest-lab-integrate-ci-cd-vsts.md):

- Criar um ambiente
- Excluir um ambiente

## <a name="before-you-begin"></a>Antes de começar
Antes de você pode integrar seu pipeline de CI/CD com o Azure DevTest Labs, instale [tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) extensão do Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Criar e configurar o laboratório para ambientes
Esta seção descreve como criar e configurar um laboratório em que o ambiente do Azure será implantado.

1. [Criar um laboratório](devtest-lab-create-lab.md) se você ainda não tiver um. 
2. Configure o laboratório e criar um modelo de ambiente seguindo as instruções deste artigo: [Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. Para este exemplo, use um modelo de início rápido do Azure existentes [ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/).
4. Cópia de **201-web-app-redis-cache-sql-database** pasta para o **ArmTemplate** pasta no repositório configurado na etapa 2.

## <a name="create-a-release-definition"></a>Criar uma definição de versão
Para criar uma definição da versão, faça o seguinte:

1.  Sobre o **versões** guia da **hub de versão e compilação**, selecione o **sinal de adição (+)** botão.
2.  Na janela **Criar definição da versão**, selecione o modelo **Vazio** e selecione **Avançar**.
3.  Selecione **Escolher Depois** e selecione **Criar** para criar uma nova definição da versão com um ambiente padrão e nenhum artefato vinculado.
4.  Para abrir o menu de atalho, na nova definição de versão, selecione o **reticências (...)**  próximo ao nome do ambiente e, em seguida, selecione **configurar variáveis**.
5.  Na janela **Configurar – ambiente**, para as variáveis usadas nas tarefas de definição da versão, insira os seguintes valores:
1.  Para **administratorLogin**, insira o nome de logon do administrador do SQL.
2.  Para **administratorLoginPassword**, insira a senha a ser usada pelo logon de administrador do SQL. Use o ícone de "cadeado" para ocultar e proteger a senha.
3.  Para **databaseName**, insira o nome do banco de dados SQL.
4.  Essas variáveis são específicas para os ambientes de exemplo, ambientes diferentes podem ter variáveis diferentes.

## <a name="create-an-environment"></a>Criar um ambiente
O próximo estágio da implantação é criar o ambiente a ser usado para fins de teste ou desenvolvimento.

1. Na definição da versão, selecione **Adicionar tarefas**.
2. Sobre o **tarefas** guia, adicione uma tarefa de criar ambiente do Azure DevTest Labs. Configure as tarefas da seguinte forma:
    1. Para **Assinatura do Azure RM**, selecione uma conexão na lista **Conexões de Serviço do Azure Disponíveis** ou crie uma conexão de permissões mais restritas para sua assinatura do Azure. Para saber mais, confira [Ponto de extremidade de serviço do Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
2. Para **nome do laboratório**, selecione o nome da instância que você criou anteriormente *.
3. Para **nome do repositório**, selecione o repositório em que o modelo do Gerenciador de recursos (201) foi enviado para *.
4. Para **nome do modelo**, selecione o nome do ambiente que você salvou em seu repositório de código de origem *. 
5. O **nome do laboratório**, **nome do repositório**, e **nome do modelo** são as representações amigáveis das IDs do recurso do Azure. Inserir manualmente o nome amigável, causar falhas, use as listas suspensas para selecionar as informações.
6. Para **nome do ambiente**, insira um nome para identificar exclusivamente a instância do ambiente no laboratório.  Ele deve ser exclusivo no laboratório.
7. O **arquivo de parâmetro** e o **parâmetros**, permitir que os parâmetros personalizados a serem passados para o ambiente. Um ou ambos podem ser usados para definir os valores de parâmetro. Neste exemplo, a seção de parâmetros será usada. Use os nomes das variáveis que você definiu no ambiente, por exemplo: `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. Informações dentro do modelo de ambiente podem ser passadas na seção de saída do modelo. Verifique **criar variáveis de saída com base na saída do modelo de ambiente** para que outras tarefas podem usar os dados. `$(Reference name.Output Name)` é o padrão a seguir. Por exemplo, se o nome de referência foi DTL e o nome de saída no modelo era local a variável seria `$(DTL.location)`.

## <a name="delete-the-environment"></a>Excluir o ambiente
O estágio final é excluir o ambiente que você implantou em sua instância do Azure DevTest Labs. Normalmente, você excluiria o ambiente depois de executar as tarefas de desenvolvimento ou executar os testes que você precisa sobre os recursos implantados.

Na definição da versão, selecione **adicionar tarefas**e, em seguida, no **Deploy** guia, adicione um **excluir ambiente do Azure DevTest Labs** tarefa. Configure-o da seguinte maneira:

1. Para excluir a VM, consulte [tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Para **Assinatura do Azure RM**, selecione uma conexão na lista **Conexões de Serviço do Azure Disponíveis** ou crie uma conexão de permissões mais restritas para sua assinatura do Azure. Para saber mais, confira [Ponto de extremidade de serviço do Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
    2. Para **nome do laboratório**, selecione o laboratório onde existe o ambiente.
    3. Para **nome do ambiente**, insira o nome do ambiente a ser removido.
2. Insira um nome para a definição da versão e, em seguida, salve-a.

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos: 
- [Criar ambientes de várias VMs com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
- Modelos do Gerenciador de recursos de início rápido para a automação do DevTest Labs do [repositório GitHub do DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
- [Página de solução de problemas do VSTS](/azure/devops/pipelines/troubleshooting)

