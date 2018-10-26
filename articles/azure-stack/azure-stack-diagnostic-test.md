---
title: Executar um teste de validação no Azure Stack | Microsoft Docs
description: Como coletar arquivos de log de diagnóstico no Azure Stack.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 10/24/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 671818ca8f8fa07b506252d5d4d0c7a768577b19
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087467"
---
# <a name="run-a-validation-test-for-azure-stack"></a>Executar um teste de validação para o Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*
 
Você pode validar o status do Azure Stack. Quando você tiver um problema, entre em contato com o serviços de atendimento ao cliente Microsoft. Suporte solicita que você execute **AzureStack teste** em seu nó de gerenciamento. O teste de validação isola a falha. Suporte, em seguida, pode analisar os logs detalhados, concentre-se na área onde ocorreu o erro e trabalhar com você na resolução do problema.

## <a name="run-test-azurestack"></a>Run Test-AzureStack

Quando você tiver um problema, entre em contato com o serviços de atendimento ao cliente Microsoft e, em seguida, execute **executar teste-AzureStack**.

1. Você tem um problema.
2. Suporte de serviços de Atendimento Microsoft.
3. Execute **AzureStack teste** do ponto de extremidade com privilégios.
    1. O ponto de extremidade com privilégios de acesso. Para obter instruções, consulte [usando o ponto de extremidade privilegiado no Azure Stack](azure-stack-privileged-endpoint.md). 
    2. Em ASDK, entrar no host de gerenciamento, como **AzureStack\CloudAdmin**.  
    Em um sistema integrado, você precisará usar o endereço IP para o ponto com privilégios de extremidade para o gerenciamento fornecido por seu fornecedor de hardware de OEM.
    3. Abra o PowerShell como administrador.
    4. Execute: `Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint`
    5. Execute: `Test-AzureStack`
4. Se qualquer teste de relatório **falhar**, execute: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` o cmdlet agrupa os logs de teste AzureStack. Para obter mais informações sobre logs de diagnóstico, consulte [das ferramentas de diagnóstico do Azure Stack](azure-stack-diagnostics.md). Você deve coletar logs ou entre em contato com o suporte de serviços de atendimento da Microsoft (CSS) se não testa o relatório **AVISAR**.
5. Enviar o **SeedRing** logs de serviços de atendimento ao cliente Microsoft. Serviços de atendimento ao cliente Microsoft trabalha com você para resolver o problema.

## <a name="reference-for-test-azurestack"></a>Referência para o teste AzureStack

Esta seção contém uma visão geral para o cmdlet Test-AzureStack e um resumo do relatório de validação.

### <a name="test-azurestack"></a>Test-AzureStack

Valida o status do Azure Stack. O cmdlet relata o status do Azure Stack de hardware e software. A equipe de suporte pode usar este relatório para reduzir o tempo para resolver casos de suporte do Azure Stack.

> [!Note]  
> **Teste AzureStack** pode detectar falhas que não estão resultando em interrupções de nuvem, como uma única falha de disco ou uma falha de nó único host físico.

#### <a name="syntax"></a>Sintaxe

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>parâmetros

| Parâmetro               | Valor           | Obrigatório | Padrão |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | Cadeia de caracteres    | Não        | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | Não        | FALSE   |
| AdminCredential         | PSCredential    | Não        | ND      |
| Listar                    | SwitchParameter | Não        | FALSE   |
| Ignorar                  | Cadeia de caracteres          | Não        | ND      |
| Incluir                 | Cadeia de caracteres          | Não        | ND      |
| BackupSharePath         | Cadeia de caracteres          | Não        | ND      |
| BackupShareCredential   | PSCredential    | Não        | ND      |


O cmdlet Test-AzureStack dá suporte a parâmetros comuns: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable e OutVariable. Para obter mais informações, consulte [sobre os parâmetros comuns](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Exemplos de teste AzureStack

Os exemplos a seguir pressupõem que você está conectado como **CloudAdmin** e acessar o ponto de extremidade com privilégios (PEP). Para obter instruções, consulte [usando o ponto de extremidade privilegiado no Azure Stack](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Executar teste AzureStack interativamente sem cenários de nuvem

Em uma sessão PEP, execute:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Executar teste AzureStack com cenários de nuvem

Você pode usar **AzureStack teste** executar cenários de nuvem no Azure Stack. Esses cenários incluem:

 - Criar grupos de recursos
 - Criação de planos
 - Criação de ofertas
 - Criar contas de armazenamento
 - Criar uma máquina virtual
 - Executar operações de blob usando a conta de armazenamento criada no cenário do teste
 - Executar operações de fila usando a conta de armazenamento criada no cenário do teste
 - Executar operações de tabela usando a conta de armazenamento criada no cenário do teste

Os cenários de nuvem exigem credenciais de administrador de nuvem. 
> [!Note]  
> É possível executar os cenários de nuvem usando as credenciais do Active Directory Federated Services (AD FS). O **AzureStack teste** cmdlet só é acessível por meio do PEP. Porém, o PEP não dá suporte a credenciais do AD FS.

Digite o nome de usuário de administrador de nuvem no formato UPN serviceadmin@contoso.onmicrosoft.com (Azure AD). Quando solicitado, digite a senha para a conta de administrador de nuvem.

Em uma sessão PEP, execute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Executar teste AzureStack sem cenários de nuvem

Em uma sessão PEP, execute:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Liste os cenários de teste disponíveis:

Em uma sessão PEP, execute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Executar um teste especificado

Em uma sessão PEP, execute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Para excluir testes específicos:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="run-test-azurestack-to-test-infrastructure-backup-settings"></a>Executar AzureStack de teste para testar as configurações de backup de infraestrutura

Antes de configurar o backup de infraestrutura, você pode testar o caminho de compartilhamento de backup e de credenciais usando o **AzsBackupShareAccessibility** de teste.

Em uma sessão PEP, execute:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
````
Após configurar o backup, você pode executar AzsBackupShareAccessibility para validar o compartilhamento pode ser acessado de ERCS, de uma sessão PEP executar:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility
````

Para testar as novas credenciais com o compartilhamento de backup configurado, em uma sessão PEP execute:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential <PSCredential for backup share>
````

### <a name="validation-test"></a>Teste de validação

A tabela a seguir resume os testes de validação executados pelo **AzureStack teste**.

| NOME                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Resumo de infraestrutura de hospedagem em nuvem do Azure Stack                                                                                  |
| Resumo de serviços de armazenamento do Azure Stack                                                                                              |
| Resumo de instância da função de infraestrutura do Azure Stack                                                                                  |
| Utilização da infraestrutura de hospedagem em nuvem do Azure Stack                                                                              |
| Capacidade de infraestrutura do Azure Stack                                                                                               |
| Resumo de API e Portal do Azure Stack                                                                                                |
| Resumo de certificado do Azure Resource Manager de pilha do Azure                                                                                               |
| Controlador de gerenciamento de infraestrutura, controlador de rede, serviços de armazenamento e funções de infraestrutura de ponto de extremidade com privilégios          |
| Controlador de gerenciamento de infraestrutura, controlador de rede, serviços de armazenamento e instâncias de função de infraestrutura de ponto de extremidade com privilégios |
| Resumo da função de pilha de infraestrutura do Azure                                                                                           |
| Serviços de malha de serviço de nuvem do Azure Stack                                                                                         |
| Desempenho de instância de função de infraestrutura do Azure Stack                                                                              |
| Resumo de desempenho de Host de nuvem do Azure Stack                                                                                        |
| Resumo de consumo de recursos de serviço do Azure Stack                                                                                  |
| Eventos do Azure Stack escala unidade críticos (últimas 8 horas)                                                                             |
| Resumo de discos físicos de serviços de armazenamento do Azure Stack                                                                               |
|Resumo de acessibilidade de compartilhamento de Backup do Azure Stack                                                                                     |

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre as ferramentas de diagnóstico do Azure Stack e o log de problema, consulte [ das ferramentas de diagnóstico do Azure Stack](azure-stack-diagnostics.md).
 - Para saber mais sobre como solucionar problemas, consulte [solução de problemas do Microsoft Azure Stack](azure-stack-troubleshooting.md)
