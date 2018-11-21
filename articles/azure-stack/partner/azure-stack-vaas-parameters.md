---
title: Parâmetros comuns de fluxo de trabalho na validação de pilha do Azure como um serviço | Microsoft Docs
description: Parâmetros comuns de fluxo de trabalho para validação de pilha do Azure como um serviço
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: ddc6942b56e3ad4d1f5b16c86dde87f408c1a2c1
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262980"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Parâmetros comuns de fluxo de trabalho para validação de pilha do Azure como um serviço

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Parâmetros comuns incluem valores como variáveis de ambiente e o usuário as credenciais necessárias por todos os testes de validação como um serviço (VaaS). Esses valores são definidos no nível do fluxo de trabalho quando você criar ou alterar um fluxo de trabalho. Ao agendar os testes, esses valores são passados como parâmetros para cada teste com o fluxo de trabalho.

> [!NOTE]
> Cada teste define seu próprio conjunto de parâmetros. Em tempo de agendamento, um teste pode exigir que você insira um valor independentemente dos parâmetros comuns, ou pode permitir que você substitua o valor do parâmetro comum.

## <a name="environment-parameters"></a>Parâmetros do ambiente

Parâmetros do ambiente descrevem o ambiente do Azure Stack em teste. Esses valores devem ser fornecidos, gerando e carregando um arquivo de informações de carimbo de data / Azure Stack para a instância específica que você está testando.

> [!NOTE]
> Em fluxos de trabalho de validação oficial, os parâmetros de ambiente não podem ser modificados após a criação de fluxo de trabalho.

### <a name="generate-the-stamp-information-file"></a>Gerar o arquivo de informações de carimbo de data /

1. Faça logon em qualquer máquina que tem acesso ao ambiente do Azure Stack ou o DVM.
2. Execute os seguintes comandos em uma janela elevada do PowerShell:
    ```PowerShell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Localizar valores no arquivo de configuração ECE

Valores de parâmetro de ambiente também podem ser localizadas manualmente na **arquivo de configuração ECE** localizado em `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` sobre o DVM.

## <a name="test-parameters"></a>Parâmetros de teste

Parâmetros comuns de teste incluem informações confidenciais que não podem ser armazenadas em arquivos de configuração. Eles devem ser fornecidos manualmente.

Parâmetro    | Description
-------------|-----------------
Usuário de administrador de locatários                            | Administrador do Azure Active Directory locatário que foi provisionado pelo administrador de serviços no diretório do AAD. Esse usuário executa ações de nível de locatário, como implantação de modelos para configurar recursos (VMs, contas de armazenamento, etc.) e executar cargas de trabalho. Para obter detalhes sobre como provisionar a conta de locatário, consulte [adicionar um novo locatário do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad).
Usuário de administrador de serviço             | Administrador de diretório Active Directory do Azure do locatário de diretório do AAD especificado durante a implantação do Azure Stack. Pesquise `AADTenant` na configuração ECE de arquivo e selecione o valor no `UniqueName` elemento.
Usuário administrador de nuvem               | Conta de administrador de domínio de pilha do Azure (por exemplo, `contoso\cloudadmin`). Pesquise `User Role="CloudAdmin"` na configuração ECE de arquivo e selecione o valor no `UserName` elemento.
Cadeia de Conexão de diagnóstico          | Uma URL de SAS para uma conta de armazenamento do Azure para diagnóstico de quais logs serão copiados durante a execução de teste. Para obter instruções sobre como gerar a URL de SAS, consulte [gerar a cadeia de caracteres de conexão de diagnóstico](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> O **cadeia de conexão de diagnóstico** deve ser válido antes de continuar.

### <a name="generate-the-diagnostics-connection-string"></a>Gerar a cadeia de caracteres de conexão de diagnóstico

A cadeia de caracteres de conexão de diagnóstico é necessária para armazenar os logs de diagnóstico durante a execução de teste. Use a conta de armazenamento do Azure criados durante a instalação (consulte [configurar a validação como um serviço de recursos](azure-stack-vaas-set-up-resources.md)) para criar uma URL de SAS (assinatura) de acesso compartilhado para conceder acesso de VaaS para carregar os logs para sua conta de armazenamento.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Selecione **Blob** partir **opções de serviços permitidos**. Desmarque todas as opções restantes.

1. Selecione **Service**, **contêiner**, e **objeto** do **tipos de recursos permitidos**.

1. Selecione **leitura**, **escrever**, **lista**, **adicionar**, **criar** de **permitido permissões**. Desmarque todas as opções restantes.

1. Definir **hora de início** para a hora atual, e **hora de término** três meses a partir da hora atual.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> A URL de SAS expira na hora de término especificada quando a URL foi gerada.  
Ao agendar os testes, certifique-se de que a URL é válida pelo menos 30 dias, mais o tempo necessário para a execução de teste (três meses é sugerido).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [validação como um conceitos principais do serviço](azure-stack-vaas-key-concepts.md)