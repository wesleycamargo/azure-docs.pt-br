---
title: Testar os parâmetros de validação como um serviço do Azure Stack | Microsoft Docs
description: Tópico de referência sobre o arquivo de configuração e o teste passar parâmetros para a validação como um serviço do Azure Stack.
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
ms.openlocfilehash: 9f042779e3463f0d75dc6327b3553156edbf162a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162216"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>Parâmetros de teste para validação como um serviço do Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Antes de executar qualquer conjunto de testes de validações, como um serviço (VaaS), selecione uma solução e crie uma aprovação de teste.

- Entrar com sua VaaS registrado credenciais de locatário.
- Criar uma nova solução (selecionando **adicionar solução**) ou selecione todos os existentes.
- Selecione o **inicie** botão da **teste é aprovado** bloco de fluxo de trabalho.

> [!Note]  
> Crie uma nova entrada de solução para cada configuração de set/hardware de computador exclusivo que você está validando, e um novo teste passar para cada implantação de compilação em que conjunto de máquinas.

Você precisará inserir os parâmetros necessários para executar os testes na **teste passar informações** página. Fornece esses parâmetros ao iniciar uma nova passagem de teste ou execução de validação. A maioria dos parâmetros têm os mesmos valores que você forneceu quando você implantou o Azure Stack.

Você pode inserir os valores manualmente listados na [tabela de parâmetros de teste](#test-parameters), ou carregar o arquivo de configuração de implantação, que contém as informações de carimbo de data / completas do Azure Stack. Uma vez carregado, o portal carrega os valores do arquivo.

> [!Note]  
> Você pode pesquisar e digitando os valores de parâmetro de teste ao encontrar e carregar o arquivo de configuração no portal do.

## <a name="export-the-test-parameters-using-powershell"></a>Exportar os parâmetros de teste usando o PowerShell

1. Entrar DVM ou qualquer máquina que tem acesso ao ambiente do Azure Stack.
2. Abra uma janela do PowerShell com privilégios elevados e execute:

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. Carregue **stampinfoproperties.json** VaaS portal.

## <a name="find-the-test-parameters-in-the-configuration-file"></a>Localizar os parâmetros de teste no arquivo de configuração

Você também pode encontrar os valores de parâmetro de teste abrindo o ECE **arquivo de configuração**. Você pode encontrar o arquivo no seguinte caminho na máquina DVM:  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>Parâmetros de teste 

| Parâmetro    | DESCRIÇÃO |
|-------------|-----------------|
| Compilação do Azure Stack                      | Azure Stack compilar ou número de versão que foi implantado, por exemplo, a 1.0.170330.0 | 
| ID do locatário                              | Locatário do Azure Active Directory especificado durante a implantação do Azure Stack. Pesquise **AADTenant** no arquivo de configuração e selecione o **GUID** valor no `Id` marca. | 
| Região                                 | Região de implantação de pilha do Azure. Pesquise **região** no arquivo de configuração. | 
| Gerenciador de recursos de locatário                | Por exemplo, o ponto de extremidade do Azure Resource Manager, de locatário `https://management.<ExternalFqdn>` | 
| Gerenciador de recursos do administrador                 | Ponto de extremidade de administração do Azure Resource Manager. por exemplo, `https://adminmanagement.<ExternalFqdn>` | 
| FQDN externo                          | FQDN externo do ambiente. Pesquise **ExternalFqdn** no arquivo de configuração. | 
| Usuário do locatário                            | Administrador de locatário do Active Directory que também foi provisionado já ou precisa ser provisionado pelo administrador de serviço no diretório do Azure AD do Azure. Para obter detalhes sobre o provisionamento de conta de locatário, consulte [adicionar uma nova conta de locatário do Azure Stack no Azure Active Directory](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad). Esse valor é usado pelo teste para executar operações de nível de locatário como a implantação de modelos para provisionar recursos (contas da VM, armazenamento etc.) e executar cargas de trabalho. | 
| Usuário de administrador de serviço             | Administrador de diretório Active Directory do Azure do locatário de diretório do AD do Azure especificado durante a implantação do Azure Stack. Pesquise **AADTenant** na configuração do arquivo e selecione o valor no `UniqueName` marca no arquivo de configuração. | 

## <a name="checks-before-starting-the-tests"></a>Verificações antes de iniciar os testes

Os testes de executam operações remotas. O computador que executa os testes deve ter acesso aos pontos de extremidade do Azure Stack. Caso contrário, o teste não funcionará. Se você estiver usando o VaaS no agente local, use a máquina em que o agente será executado. Você pode verificar se o seu computador tem acesso aos pontos de pilha do Azure, executando as seguintes verificações.

1. Verifique se o URI de Base pode ser acessado. Abra um prompt de comando ou shell bash e execute o seguinte comando:

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Abra um navegador da web e navegue até `https://adminportal.<EXTERNALFQDN>` para verificar se o Portal MAS pode ser acessado.

3. Valores de nome e a senha de administrador fornecidos ao criar a aprovação do teste de serviço entrar usando o Azure AD.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre [validação do Azure Stack como um serviço](https://docs.microsoft.com/azure/azure-stack/partner).
