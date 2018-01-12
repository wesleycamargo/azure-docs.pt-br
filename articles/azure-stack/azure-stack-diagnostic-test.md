---
title: "Executar um teste de validação na pilha do Azure | Microsoft Docs"
description: "Como coletar arquivos de log de diagnóstico na pilha do Azure"
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: mabrigg
ms.openlocfilehash: 53ef19628b40c4a008143c867c9e7867ac91854d
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>Executar um teste de validação para a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*
 
Você pode validar o status da pilha do Azure. Quando você tiver um problema, entre em contato com o serviços de atendimento ao cliente Microsoft. Suporte solicita a execução de teste AzureStack de seu nó de gerenciamento. O teste de validação isola a falha. Suporte pode, em seguida, analise os logs detalhados, enfocam a área onde ocorreu o erro e trabalhar com você na resolução do problema.

## <a name="run-test-azurestack"></a>Executar teste AzureStack

Quando você tiver um problema, entre em contato com o serviços de atendimento ao cliente Microsoft e, em seguida, execute **executar teste-AzureStack**.

1. Você tem um problema.
2. Suporte de serviços de Atendimento Microsoft.
3. Executar **teste AzureStack** do ponto de extremidade com privilégios.
    1. O ponto de extremidade com privilégios de acesso. Para obter instruções, consulte [usando o ponto de extremidade com privilégios na pilha do Azure](azure-stack-privileged-endpoint.md). 
    2. Faça logon como **AzureStack\CloudAdmin** no host de gerenciamento.
    3. Abra o PowerShell como administrador.
    4. Execute: `Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. Execute: `Test-AzureStack`
4. Se quaisquer testes relatarem falha, execute: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` o cmdlet agrupa os logs de AzureStack de teste. Para obter mais informações sobre logs de diagnóstico, consulte [ferramentas de diagnóstico do Azure pilha](azure-stack-diagnostics.md).
5. Enviar o **SeedRing** logs de serviços de atendimento ao cliente Microsoft. Serviços de atendimento ao cliente Microsoft trabalha com você para resolver o problema.

## <a name="reference-for-test-azurestack"></a>Referência para teste AzureStack

Esta seção contém uma visão geral para o cmdlet Test-AzureStack e um resumo do relatório de validação.

### <a name="test-azurestack"></a>Teste AzureStack

Valida o status da pilha do Azure. O cmdlet relata o status do hardware de pilha do Azure e do software. A equipe de suporte pode usar este relatório para reduzir o tempo para resolver os casos de suporte de pilha do Azure.

> [!Note]  
> Teste AzureStack pode detectar falhas que não estão resultando em interrupções de nuvem, como uma única falha de disco ou uma falha de nó único host físico.

#### <a name="syntax"></a>Sintaxe

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>parâmetros

| Parâmetro               | Valor           | Obrigatório | Padrão |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | Não        | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | Não        | FALSE   |
| AdminCredential         | PSCredential    | Não        | ND      |
| StorageConnectionString | Cadeia de caracteres          | Não        | ND      |
| Listar                    | SwitchParameter | Não        | FALSE   |
| Ignorar                  | Cadeia de caracteres          | Não        | ND      |
| Incluir                 | Cadeia de caracteres          | Não        | ND      |

O cmdlet Test-AzureStack oferece suporte a parâmetros comuns: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable e OutVariable. Para obter mais informações, consulte [sobre parâmetros comuns de](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Exemplos de teste AzureStack

Os exemplos a seguir pressupõem que você está conectado como **CloudAdmin** e acessar o ponto de extremidade privilegiado (PEP). Para obter instruções, consulte [usando o ponto de extremidade com privilégios na pilha do Azure](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Executar teste AzureStack interativamente sem cenários de nuvem

Em uma sessão PEP, execute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Executar teste AzureStack com cenários de nuvem

Você pode usar AzureStack de teste para executar os cenários de nuvem em sua pilha do Azure. Esses cenários incluem:

 - Criar grupos de recursos
 - Criação de planos
 - Criação de ofertas
 - Criar contas de armazenamento
 - Criar uma máquina virtual
 - Executar operações de blob usando a conta de armazenamento criada no cenário de teste
 - Executar operações de fila usando a conta de armazenamento criada no cenário de teste
 - Executar operações de tabela usando a conta de armazenamento criada no cenário de teste

Cenários de nuvem exigem credenciais de administrador. 
> [!Note]  
> Você não pode executar os cenários de nuvem usando as credenciais do Active Directory Federated Services (ADFS). O **AzureStack teste** cmdlet só é acessível por meio de PEP. Porém, o PEP não dá suporte a credenciais do AD FS.

Digite o nome de usuário do administrador de nuvem no formato UPN serviceadmin@contoso.onmicrosoft.com (AAD). Quando solicitado, digite a senha para a conta de administrador de nuvem.

Em uma sessão PEP, execute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Executar teste AzureStack sem cenários de nuvem

Em uma sessão PEP, execute:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Lista os cenários de teste disponíveis:

Em uma sessão PEP, execute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Executar um teste especificado

Em uma sessão PEP, execute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Para excluir testes específicos:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>Teste de validação

A tabela a seguir resume os testes de validação executados por AzureStack de teste.

| NOME                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Resumo de infraestrutura de hospedagem em nuvem do Azure de pilha                                                                                  |
| Resumo de serviços de armazenamento do Azure de pilha                                                                                              |
| Resumo de instância da função de infraestrutura do Azure de pilha                                                                                  |
| Utilização da infraestrutura de hospedagem em nuvem do Azure de pilha                                                                              |
| Capacidade de infraestrutura de pilha do Azure                                                                                               |
| Resumo de API e Portal do Azure de pilha                                                                                                |
| Resumo de certificado do Gerenciador de recursos do Azure de pilha do Azure                                                                                               |
| Controlador de gerenciamento de infraestrutura, controlador de rede, serviços de armazenamento e funções de infraestrutura de ponto de extremidade com privilégios          |
| Controlador de gerenciamento de infraestrutura, controlador de rede, serviços de armazenamento e instâncias de função de infraestrutura de ponto de extremidade com privilégios |
| Resumo de função de pilha de infraestrutura do Azure                                                                                           |
| Serviços de malha de serviço de nuvem de pilha do Azure                                                                                         |
| Desempenho de instância de função de infraestrutura de pilha do Azure                                                                              |
| Resumo do desempenho de Host de nuvem de pilha do Azure                                                                                        |
| Resumo de consumo de recursos de serviço de pilha do Azure                                                                                  |
| Azure pilha escala unidade eventos críticos (últimas 8 horas)                                                                             |
| Resumo de discos físicos de serviços de armazenamento do Azure de pilha                                                                               |

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre as ferramentas de diagnóstico de pilha do Azure e o log de problema, consulte [ ferramentas de diagnóstico do Azure pilha](azure-stack-diagnostics.md).
 - Para saber mais sobre como solucionar problemas, consulte [a solução de problemas de pilha do Microsoft Azure](azure-stack-troubleshooting.md)