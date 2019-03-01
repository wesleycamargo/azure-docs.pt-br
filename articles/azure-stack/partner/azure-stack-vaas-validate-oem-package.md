---
title: Validar pacotes do fabricante original do equipamento (OEM) na validação de pilha do Azure como um serviço | Microsoft Docs
description: Saiba como validar os pacotes do fabricante original do equipamento (OEM) com a validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 02/19/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f5b884ddda292b1c523a5364d34753ccb3a5bbdf
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194427"
---
# <a name="validate-oem-packages"></a>Validar pacotes de OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Você pode testar um novo pacote de OEM quando houve uma alteração no firmware ou drivers para uma validação de solução completa. Quando seu pacote tiver passado o teste, ele é assinado pela Microsoft. O teste deve conter o pacote de extensão de OEM atualizado com os drivers e firmware que passaram logotipo do Windows Server e testes PCS.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Antes de carregar ou envio de pacotes, examine [criar um pacote de OEM](azure-stack-vaas-create-oem-package.md) para o formato de pacote esperado e o conteúdo.

## <a name="managing-packages-for-validation"></a>Gerenciamento de pacotes para validação

Ao usar o **validação do pacote** fluxo de trabalho para validar um pacote, você precisará fornecer uma URL para um **Azure Storage blob**. Esse blob é que o teste assinou o pacote de OEM que será instalado como parte do processo de atualização. Criar o blob usando a conta de armazenamento do Azure criada durante a instalação (consulte [configurar a validação como um serviço de recursos](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Pré-requisito: Provisionar um contêiner de armazenamento

Crie um contêiner na conta de armazenamento de blobs do pacote. Esse contêiner pode ser usado para todos os seus validação do pacote é executado.

1. No [Portal do Azure](https://portal.azure.com), vá para a conta de armazenamento criada na [configurar sua validação como um serviço de recursos](azure-stack-vaas-set-up-resources.md).
2. Na folha à esquerda sob **serviço Blob**, selecione na **contêineres**.
3. Selecione **+ contêiner** no menu da barra e forneça um nome para o contêiner, por exemplo, `vaaspackages`.

### <a name="upload-package-to-storage-account"></a>Carregar pacote para a conta de armazenamento

1. Prepare o pacote que você deseja validar. Se o seu pacote tiver vários arquivos, compactá-lo em um `.zip` arquivo.
2. No [Portal do Azure](https://portal.azure.com), selecione o contêiner de pacote e carregar o pacote selecionando na **carregar** na barra de menus.
3. Selecione o pacote `.zip` arquivo a ser carregado. Mantenha os padrões para **tipo de Blob** (ou seja, **Blob de blocos**) e **tamanho do bloco**.

> [!NOTE]
> Verifique se o `.zip` conteúdo é colocado na raiz do `.zip` arquivo. Não deve haver nenhuma subpasta no pacote.

### <a name="generate-package-blob-url-for-vaas"></a>Gerar a URL do blob de pacote para VaaS

Ao criar uma **validação de pacote** fluxo de trabalho no portal do VaaS, você precisará fornecer uma URL para o blob de armazenamento do Azure que contém o pacote.

#### <a name="option-1-generating-a-blob-sas-url"></a>Opção 1: Gerando um URL SAS do blob

Use esta opção se você não quiser habilitar o acesso de leitura público ao contêiner de armazenamento ou blobs.

1. No [portal do Azure](https://portal.azure.com/), vá para sua conta de armazenamento e navegue até o arquivo. zip que contém o pacote

2. Selecione **gerar SAS** no menu de contexto

3. Selecione **leitura** de **permissões**

4. Definir **hora de início** para a hora atual, e **hora de término** pelo menos de 48 horas da **hora de início**. Se você for executar outros testes com o mesmo pacote, considere aumentar **hora de término** para a duração do teste. Todos os testes agendados pelo VaaS após **hora de término** falham e uma nova SAS será precisará ser gerado.

5. Selecione **Gerar URL e token SAS do blob**.

Use o **URL de SAS do Blob** quando fornecendo pacote blob URLs para o portal.

#### <a name="option-2-grant-public-read-access"></a>Opção 2: Conceder acesso de leitura público

> [!CAUTION]
> Essa opção abre o blob (s) para acesso anônimo de somente leitura.

1. Grant **acesso somente para blobs de leitura público** ao contêiner de pacote, seguindo as instruções na seção [conceder permissões a usuários anônimos a contêineres e blobs](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

> [!NOTE]
> Se você estiver fornecendo uma URL de pacote para um *teste interativa* (por exemplo, verificação de atualização mensal AzureStack ou verificação de pacote de extensão do OEM), você deve conceder **acesso de leitura público completo** para Continue com testes.

2. No contêiner do pacote, selecione o blob de pacote para abrir o painel Propriedades.

3. Cópia de **URL**. Use esse valor quando fornecendo pacote blob URLs para o portal.

## <a name="apply-monthly-update"></a>Aplicar atualização mensal

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

> [!NOTE]
> Depois de aplicar a atualização mensal, é recomendável que você execute AzureStack de teste para verificar se a atualização foi aplicada corretamente e está em um estado íntegro. Se falhar AzureStack de teste, relate o problema à Microsoft. Não continue com a aprovação do teste até que o problema seja resolvido. Informações sobre como executar o comando Test-Azure Stack podem ser encontradas neste [artigo](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test).

## <a name="create-a-package-validation-workflow"></a>Criar um fluxo de trabalho de validação do pacote

1. Entrar para o [VaaS portal](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Selecione **iniciar** sobre o **validação de pacote** lado a lado.

    ![Bloco de fluxo de trabalho de validações de pacote](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Insira o armazenamento do Azure para o teste de URL do blob assinou o pacote de OEM que exigem uma assinatura da Microsoft. Para obter instruções, consulte [gerar a URL do blob de pacote para VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Parâmetros de ambiente não podem ser modificados depois de criar um fluxo de trabalho.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Você será redirecionado à página de resumo de testes.

## <a name="required-tests"></a>Testes necessários

Testes a seguir são necessários para validação do pacote de OEM:

- Verificação do pacote de extensão de OEM
- Mecanismo de simulação de nuvem

## <a name="run-package-validation-tests"></a>Executar testes de validação de pacote

1. No **resumo de testes de validação do pacote** página, você executará um subconjunto dos testes listados apropriados para seu cenário.

    Nos fluxos de trabalho validação **agendamento** um teste usa os parâmetros comuns de nível de fluxo de trabalho que você especificou durante a criação de fluxo de trabalho (consulte [parâmetros comuns de fluxo de trabalho para validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md)). Se qualquer um dos valores de parâmetro de teste se tornar inválido, deve resupply-las conforme as instruções [modifique os parâmetros de fluxo de trabalho](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Um teste de validação no agendamento sobre uma instância existente, você criará uma nova instância no lugar da instância antiga no portal. Os logs para a instância antiga serão retidos, mas não são acessíveis a partir do portal.  
    Depois que um teste for concluído com êxito, o **agendamento** ação for desabilitada.

2. Selecione o agente que executará o teste. Para obter informações sobre como adicionar locais agentes de execução de teste, consulte [implantar o agente local](azure-stack-vaas-local-agent.md).

3. Selecione completa de verificação do pacote de extensão de OEM **agendamento** no menu de contexto para abrir um prompt para agendar a instância de teste.

4. Revise os parâmetros de teste e, em seguida, selecione **enviar** para agendar a verificação do pacote de extensão de OEM para execução.

5. Examine o resultado de verificação do pacote de extensão de OEM. Depois que o teste tiver sido bem-sucedido, agende o mecanismo de simulação de nuvem para execução.

Quando todos os testes foi concluído com êxito, envie o nome da sua solução VaaS e validação de pacote para [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) para solicitar a assinatura do pacote.

## <a name="next-steps"></a>Próximas etapas

- [Monitorar e gerenciar testes no portal VaaS](azure-stack-vaas-monitor-test.md)
