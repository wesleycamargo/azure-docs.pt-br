---
title: Parâmetros comuns de fluxo de trabalho na validação do Azure Stack como um serviço | Microsoft Docs
description: Parâmetros comuns de fluxo de trabalho para a validação do Azure Stack como um serviço
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 81a7be973739cfd6eb3f8fb8dc7a0723623c2b8e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235184"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Parâmetros comuns de fluxo de trabalho para a validação do Azure Stack como um serviço

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Parâmetros comuns incluem valores como variáveis de ambiente e o usuário as credenciais necessárias por todos os testes de validação como um serviço (VaaS). Você define esses valores no nível do fluxo de trabalho. Você salve os valores quando você cria ou modifica um fluxo de trabalho. No tempo de agendamento, o fluxo de trabalho carrega os valores para o teste. 

## <a name="environment-parameters"></a>Parâmetros do ambiente

Parâmetros do ambiente descrevem o ambiente do Azure Stack em teste. Esses valores devem ser fornecidos por gerar e carregar o arquivo de configuração de carimbo de data / `&lt;link&gt;. [How to get the stamp info link].`

| Nome do parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
|----------------------------------|----------|------|---------------------------------------------------------------------------------------------------------------------------------|
| Compilação de pilha do Azure | Obrigatório |  | Número de compilação de implantação do Azure Stack (por exemplo, 1.0.170330.9) |
| Versão de OEM | Sim |  | Número de versão do pacote OEM usado durante a implantação do Azure Stack. |
| Assinatura de OEM | Sim |  | Assinatura do pacote OEM usado durante a implantação do Azure Stack. |
| ID de locatário do AAD | Obrigatório |  | GUID especificado durante a implantação do Azure Stack de locatário do Active Directory do Azure.|
| Região | Obrigatório |  | Região de implantação de pilha do Azure. |
| Ponto de extremidade do Gerenciador de recursos de locatário | Obrigatório |  | Ponto de extremidade para operações do locatário do Azure Resource Manager (por exemplo, https://management.<ExternalFqdn>) |
| Ponto de extremidade do Gerenciador de recursos do administrador | Sim |  | Ponto de extremidade para operações de locatário do Azure Resource Manager (por exemplo, https://adminmanagement.<ExternalFqdn>) |
| FQDN externo | Sim |  | Externo totalmente qualificado de nome de domínio usada como o sufixo para pontos de extremidade. (por exemplo, local.azurestack.external ou redmond.contoso.com). |
| Número de nós | Sim |  | Número de nós na implantação. |

## <a name="test-parameters"></a>Parâmetros de teste

Parâmetros comuns de teste incluem informações confidenciais que não podem ser armazenadas em arquivos de configuração e devem ser fornecidos manualmente.

| Nome do parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
|--------------------------------|------------------------------------------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome de usuário do locatário | Obrigatório |  | Administrador de locatário do Active Directory que também foi provisionado já ou precisa ser provisionado pelo administrador de serviço no diretório do AAD do Azure. Para obter detalhes sobre o provisionamento de conta de locatário, consulte [Introdução ao Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). Esse valor é usado pelo teste para executar operações de nível de locatário como a implantação de modelos para provisionar recursos (VMs, contas de armazenamento etc.) e executar cargas de trabalho. Esse valor é usado pelo teste para executar operações de nível de locatário como a implantação de modelos para provisionar recursos (VMs, contas de armazenamento etc.) e executar cargas de trabalho. |
| Senha do locatário | Obrigatório |  | Senha do usuário do locatário. |
| Nome de usuário do administrador de serviços | Obrigatório: Validação de solução, a validação do pacote<br>Não é necessário: aprovação do teste |  | Administrador de diretório Active Directory do Azure do locatário de diretório do AAD especificado durante a implantação do Azure Stack. |
| Senha de administrador de serviço | Obrigatório: Validação de solução, a validação do pacote<br>Não é necessário: aprovação do teste |  | Senha do usuário do administrador de serviços. |
| Nome de usuário do administrador de nuvem | Obrigatório |  | Azure Stack conta administrador do domínio (por exemplo, contoso\cloudadmin). Pesquisa de função de usuário = "CloudAdmin" no arquivo de configuração e selecione o valor na marca de nome de usuário no arquivo de configuração. |
| Senha do administrador de nuvem | Obrigatório |  | Senha do usuário do administrador de nuvem. |
| Cadeia de Conexão de diagnóstico | Obrigatório |  | Um URI de SAS para uma conta de armazenamento do Azure para diagnóstico de quais logs serão copiados durante a execução de teste. Instruções para gerar o URI de SAS estão localizadas [configurar uma conta de armazenamento de blob](azure-stack-vaas-set-up-account.md). |


## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre [validação do Azure Stack como um serviço](https://docs.microsoft.com/azure/azure-stack/partner).
