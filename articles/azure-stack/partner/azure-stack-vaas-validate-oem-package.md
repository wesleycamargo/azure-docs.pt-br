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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aae3ec8ff713959c5cc2485951aba025a6f89a1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113275"
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

1. No [portal do Azure](https://portal.azure.com), vá para a conta de armazenamento criada na [configurar sua validação como um serviço de recursos](azure-stack-vaas-set-up-resources.md).

2. Na folha à esquerda sob **serviço Blob**, selecione **contêineres**.

3. Selecione **+ contêiner** na barra de menus.
    1. Forneça um nome para o contêiner, por exemplo, `vaaspackages`.
    1. Selecione o nível de acesso desejado para clientes não autenticados como VaaS. Para obter detalhes sobre como conceder acesso de VaaS aos pacotes em cada cenário, consulte [nível de acesso do contêiner de manipulação](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Carregar pacote para a conta de armazenamento

1. Prepare o pacote que você deseja validar. Esse é um `.zip` arquivo cujo conteúdo deve corresponder à estrutura descrita em [criar um pacote de OEM](azure-stack-vaas-create-oem-package.md).

    > [!NOTE]
    > Verifique se o `.zip` conteúdo é colocado na raiz do `.zip` arquivo. Não deve haver nenhuma subpasta no pacote.

1. No [portal do Azure](https://portal.azure.com), selecione o contêiner de pacote e carregar o pacote selecionando na **carregar** na barra de menus.

1. Selecione o pacote `.zip` arquivo a ser carregado. Mantenha os padrões para **tipo de Blob** (ou seja, **Blob de blocos**) e **tamanho do bloco**.

### <a name="generate-package-blob-url-for-vaas"></a>Gerar a URL do blob de pacote para VaaS

Ao criar uma **validação de pacote** fluxo de trabalho no portal do VaaS, você precisará fornecer uma URL para o blob de armazenamento do Azure que contém o pacote. Alguns *interativa* testes, incluindo **verificação de atualização mensal AzureStack** e **verificação do pacote de extensão de OEM**, também exigem uma URL para blobs de pacote.

#### <a name="handling-container-access-level"></a>Nível de acesso do contêiner de manipulação

O nível de acesso mínimo exigido pelo VaaS depende se você estiver criando um fluxo de trabalho de validação do pacote ou agendamento de uma *interativa* de teste.

No caso de **privados** e **Blob** níveis, de acesso temporariamente você deve conceder acesso ao blob pacote fornecendo VaaS uma [assinatura de acesso compartilhado](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS). O **contêiner** nível de acesso não requer a geração de URLs da SAS, mas permite o acesso não autenticado para o contêiner e seus blobs.

|Nível de acesso | Requisito de fluxo de trabalho | Requisito de teste |
|---|---------|---------|
|Privado | Gerar uma URL SAS por blob de pacote ([opção 1](#option-1-generate-a-blob-sas-url)). | Gerar uma URL de SAS no nível da conta e adicionar manualmente o nome de blob do pacote ([opção 2](#option-2-construct-a-container-sas-url)). |
|Blob | Fornecer a propriedade de URL do blob ([opção 3](#option-3-grant-public-read-access)). | Gerar uma URL de SAS no nível da conta e adicionar manualmente o nome de blob do pacote ([opção 2](#option-2-construct-a-container-sas-url)). |
|Contêiner | Fornecer a propriedade de URL do blob ([opção 3](#option-3-grant-public-read-access)). | Fornecer a propriedade de URL do blob ([opção 3](#option-3-grant-public-read-access)).

As opções para conceder acesso aos seus pacotes são ordenadas de acesso mínimo a maior acesso.

#### <a name="option-1-generate-a-blob-sas-url"></a>Opção 1: Gerar um URL SAS do blob

Use esta opção se o nível de acesso do seu contêiner de armazenamento é definido como **privada**, em que o contêiner não permite acesso de leitura público para o contêiner ou seus blobs.

> [!NOTE]
> Esse método não funcionará para *interativa* testes. Consulte [opção 2: Construir um URL SAS do contêiner](#option-2-construct-a-container-sas-url).

1. No [portal do Azure](https://portal.azure.com/), vá para sua conta de armazenamento e navegue até o arquivo. zip que contém o pacote.

2. Selecione **gerar SAS** no menu de contexto.

3. Selecione **leitura** partir **permissões**.

4. Definir **hora de início** para a hora atual, e **hora de término** pelo menos de 48 horas da **hora de início**. Se você criar outros fluxos de trabalho com o mesmo pacote, considere aumentar **hora de término** para a duração do teste.

5. Selecione **Gerar URL e token SAS do blob**.

Use o **URL de SAS do Blob** quando fornecendo pacote blob URLs para o portal.

#### <a name="option-2-construct-a-container-sas-url"></a>Opção 2: Construir um URL SAS do contêiner

Use esta opção se o nível de acesso do seu contêiner de armazenamento é definido como **privados** e você precisará fornecer uma URL de blob do pacote para um *interativo* de teste. Essa URL também pode ser usada no nível do fluxo de trabalho.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Selecione **Blob** partir **opções de serviços permitidos**. Desmarque todas as opções restantes.

1. Selecione **recipiente** e **objeto** da **tipos de recursos permitidos**.

1. Selecione **leitura** e **lista** da **permitidas permissões**. Desmarque todas as opções restantes.

1. Selecione **hora de início** como a hora atual e **hora de término** pelo menos 14 dias desde **hora de início**. Se você for executar outros testes com o mesmo pacote, considere aumentar **hora de término** para a duração do teste. Todos os testes agendados pelo VaaS após **hora de término** falham e uma nova SAS será precisará ser gerado.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    O formato deve aparecer da seguinte maneira: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Modificar a URL de SAS gerada para incluir o contêiner de pacote `{containername}`e o nome do seu blob de pacote, `{mypackage.zip}`, da seguinte maneira:  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Use esse valor quando fornecendo pacote blob URLs para o portal.

#### <a name="option-3-grant-public-read-access"></a>Opção 3: Conceder acesso de leitura público

Use esta opção se for aceitável para permitir o acesso a blobs individuais ou, no caso de clientes não autenticados *interativa* testes, o contêiner.

> [!CAUTION]
> Essa opção abre o blob (s) para acesso anônimo de somente leitura.

1. Defina o nível de acesso do contêiner de pacote para **Blob** ou **contêiner** seguindo as instruções na seção [conceder permissões a usuários anônimos a contêineres e blobs](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

    > [!NOTE]
    > Se você estiver fornecendo uma URL de pacote para um *interativa* teste, você deve conceder **acesso de leitura público completo** para o contêiner para prosseguir com o teste.

1. No contêiner do pacote, selecione o blob de pacote para abrir o painel Propriedades.

1. Cópia de **URL**. Use esse valor quando fornecendo pacote blob URLs para o portal.

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

Os testes a seguir são necessários para validação do pacote de OEM:

- Verificação do pacote de extensão de OEM
- Mecanismo de simulação de nuvem

## <a name="run-package-validation-tests"></a>Executar testes de validação de pacote

1. No **resumo de testes de validação do pacote** página, você executará um subconjunto dos testes listados apropriados para seu cenário.

    Nos fluxos de trabalho validação **agendamento** um teste usa os parâmetros comuns de nível de fluxo de trabalho que você especificou durante a criação de fluxo de trabalho (consulte [parâmetros comuns de fluxo de trabalho para validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md)). Se qualquer um dos valores de parâmetro de teste se tornar inválido, deve resupply-las conforme as instruções [modifique os parâmetros de fluxo de trabalho](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Um teste de validação no agendamento sobre uma instância existente, você criará uma nova instância no lugar da instância antiga no portal. Os logs para a instância antiga serão retidos, mas não são acessíveis a partir do portal.  
    > Depois que um teste for concluído com êxito, o **agendamento** ação for desabilitada.

2. Selecione o agente que executará o teste. Para obter informações sobre como adicionar locais agentes de execução de teste, consulte [implantar o agente local](azure-stack-vaas-local-agent.md).

3. Para concluir a verificação de pacote de extensão do OEM, selecione **agendamento** no menu de contexto para abrir um prompt para agendar a instância de teste.

4. Revise os parâmetros de teste e, em seguida, selecione **enviar** para agendar a verificação do pacote de extensão de OEM para execução.

    Verificação do pacote de extensão de OEM é dividida em duas etapas manuais: Atualização da pilha do Azure e atualização de OEM.

   1. **Selecione** "Run" na interface do usuário para executar o script nas pré-verificações. Isso é um teste automatizado que leva cerca de 5 minutos para ser concluído e não requer nenhuma ação.

   1. Depois que o script nas pré-verificações for concluída, execute a etapa manual: **instalar** a atualização mais recente disponível do Azure Stack usando o portal do Azure Stack.

   1. **Executar** AzureStack de teste no carimbo. Se ocorrerem falhas, não continue com o teste e o contato [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

       Para obter informações sobre como executar o comando AzureStack de teste, consulte [estado de sistema validar o Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test).

   1. **Selecione** "Avançar" para executar o script postcheck. Este é um teste automatizado e marca o fim do processo de atualização do Azure Stack.

   1. **Selecione** "Executar" para executar o script nas pré-verificações para atualização de OEM.

   1. Depois que o pré-verificação for concluída, execute a etapa manual: **instalar** o pacote de extensão de OEM por meio do portal.

   1. **Executar** AzureStack de teste no carimbo.

      > [!NOTE]
      > Como antes, não continue com o teste e o contato [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) se ele falhar. Esta etapa é essencial que salva uma reimplantação.

   1. **Selecione** "Avançar" para executar o script postcheck. Isso marca o fim da etapa de atualização de OEM.

   1. Responder a perguntas restantes no final do teste e **selecionar** "Enviar".

   1. Isso marca o final do teste interativa.

5. Examine o resultado de verificação do pacote de extensão de OEM. Depois que o teste tiver sido bem-sucedido, agende o mecanismo de simulação de nuvem para execução.

Para enviar um pacote de solicitação de assinatura, envie [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) o nome da solução e o nome de validação do pacote associado a essa execução.

## <a name="next-steps"></a>Próximas etapas

- [Monitorar e gerenciar testes no portal VaaS](azure-stack-vaas-monitor-test.md)
