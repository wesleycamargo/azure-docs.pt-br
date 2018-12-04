---
title: Use a ferramenta de validação do Azure Stack | Microsoft Docs
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
ms.date: 12/03/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 7f4e956601ee25549d0a0828c4c3dd0e8d6ff85b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840122"
---
# <a name="validate-azure-stack-system-state"></a>Validar o estado do sistema do Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Como um operador do Azure Stack, é essencial ter a capacidade de saber a integridade e o status de seu sistema sob demanda. A ferramenta de validação do Azure Stack (**AzureStack teste**) é um cmdlet do PowerShell que permite que você execute uma série de testes em seu sistema para identificar falhas, se estiver presente. Você normalmente precisará executar essa ferramenta por meio de [privilegiado (PEP) de ponto de extremidade](azure-stack-privileged-endpoint.md) quando você contatar o suporte de serviços de atendimento da Microsoft (CSS) com um problema. Com a integridade de todo o sistema e informações de status em mãos, CSS pode coletar e analisar logs detalhados, concentre-se na área onde ocorreu o erro e trabalhar com você para resolver o problema.

## <a name="running-the-validation-tool-and-accessing-results"></a>Executando a ferramenta de validação e acessando os resultados

Conforme mencionado anteriormente, a ferramenta de validação é executada por meio do PEP. Cada teste retorna uma **aprovação/REPROVAÇÃO** status na janela do PowerShell. Além disso, um relatório detalhado de HTML é criado que pode ser acessada mais tarde durante [coleção de log](azure-stack-diagnostics.md). Aqui está uma descrição da validação de ponta a ponta do processo de teste: 

1. O ponto de extremidade (PEP) com privilégios de acesso. Execute os seguintes comandos para estabelecer uma sessão PEP:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Para acessar o PEP em um computador de host ASDK, use AzS ERCS01 para - ComputerName.

2. Quando você estiver no PEP, execute: 

   ```powershell
   Test-AzureStack
   ```

   Consulte a [considerações sobre parâmetros](azure-stack-diagnostic-test.md#parameter-considerations) e [usar exemplos de caso](azure-stack-diagnostic-test.md#use-cases) seções para obter mais informações.

3. Se qualquer teste de relatório **falhar**, execute:

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   O cmdlet agrupa logs gerados pelo teste AzureStack. Para obter mais informações sobre logs de diagnóstico, consulte [das ferramentas de diagnóstico do Azure Stack](azure-stack-diagnostics.md). Você não deve coletar logs ou entre em contato com o CSS se testa o relatório **AVISAR**.

4. Se você foi orientado a executar a ferramenta de validação, o CSS, o representante de CSS solicita os logs coletados para continuar a solucionar seu problema.

## <a name="tests-available"></a>Testes disponíveis

A ferramenta de validação permite que você execute uma série de testes de nível de sistema e cenários de nuvem básico que fornecem a você uma visão mais detalhada atual de estado e a determinar problemas no seu sistema.

### <a name="cloud-infrastructure-tests"></a>Testes de infraestrutura de nuvem

Esses testes de baixo impacto trabalhar em um nível de infraestrutura e fornecem informações sobre vários componentes do sistema e funções. Atualmente, os testes são agrupados nas seguintes categorias:

| Categoria de teste                                        | O argumento-incluem e - ignorar |
| :--------------------------------------------------- | :-------------------------------- |
| Resumo de alertas do Azure Stack                            | AzsAlertSummary                   |
| Resumo de acessibilidade de compartilhamento de Backup do Azure Stack       | AzsBackupShareAccessibility       |
| Resumo do plano de controle do Azure Stack                    | AzsControlPlane                   |
| Resumo de Defender o Azure Stack                         | AzsDefenderSummary                |
| Resumo de Firmware de infraestrutura de hospedagem de pilha do Azure  | AzsHostingInfraFWSummary          |
| Resumo de infraestrutura de hospedagem em nuvem do Azure Stack     | AzsHostingInfraSummary            |
| Utilização da infraestrutura de hospedagem em nuvem do Azure Stack | AzsHostingInfraUtilization        |
| Capacidade de infraestrutura do Azure Stack                  | AzsInfraCapacity                  |
| Desempenho da infraestrutura do Azure Stack               | AzsInfraPerformance               |
| Resumo de função de infraestrutura do Azure Stack              | AzsInfraRoleSummary               |
| Resumo da atualização do Azure Stack                           | AzsInfraUpdateSummary             |
| Resumo de API e Portal do Azure Stack                   | AzsPortalAPISummary               |
| Eventos de VM de unidade de escala do Azure Stack                     | AzsScaleUnitEvents                |
| Recursos VM de unidade de escala do Azure Stack                  | AzsScaleUnitResources             |
| Resumo de validação de SDN do Azure Stack                   | AzsSDNValidation                  |
| Resumo da função do Azure Stack Service Fabric              | AzsSFRoleSummary                  |
| Resumo do BMC do Azure Stack                              | AzsStampBMCSummary                |
| Resumo de serviços de armazenamento do Azure Stack                 | AzsStorageSvcsSummary             |
| Resumo de Store de SQL do Azure Stack                        | AzsStoreSummary                   |
| Resumo de posicionamento de VM do Azure Stack                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Testes de cenário de nuvem

Além dos testes infraestrutura acima, você também tem a capacidade de executar testes de cenário de nuvem para verificar a funcionalidade entre os componentes de infraestrutura. Credenciais de administrador são necessários para executar esses testes conforme eles envolvem a implantação de recursos. 
    > [!NOTE]
    >
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

Os seguintes cenários de nuvem são testados pela ferramenta de validação:
- Criação de grupo de recursos   
- Criação do plano              
- Criação de oferta            
- Criação da conta de armazenamento   
- Criação da máquina virtual 
- Operação de armazenamento de blob   
- Operação de armazenamento de fila  
- Operação de armazenamento de tabela  

## <a name="parameter-considerations"></a>Considerações sobre parâmetros

- O parâmetro **lista** pode ser usado para exibir todas as categorias de teste disponíveis.

- Os parâmetros **Include** e **ignorar** pode ser usado para incluir ou excluir categorias de teste. Consulte a [testes disponíveis](azure-stack-diagnostic-test.md#tests-available) seção para obter mais informações sobre a forma abreviada a ser usado com estes argumentos.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Um locatário de que VM é implantada como parte de um cenário de nuvem testa. Você pode usar **DoNotDeployTenantVm** para desabilitá-lo. 

- Você precisa fornecer a **ServiceAdminCredential** parâmetro para executar testes de cenário de nuvem, conforme descrito na [usar exemplos de caso](azure-stack-diagnostic-test.md#use-cases) seção.

- **BackupSharePath** e **BackupShareCredential** são usados ao testar as configurações de backup de infraestrutura, conforme mostrado no [usar exemplos de caso](azure-stack-diagnostic-test.md#use-cases) seção.

- A ferramenta de validação também dá suporte a parâmetros comuns do PowerShell: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable e OutVariable. Para obter mais informações, consulte [sobre os parâmetros comuns](http://go.microsoft.com/fwlink/?LinkID=113216).  

## <a name="use-case-examples"></a>Exemplos de caso de uso 

### <a name="run-validation-without-cloud-scenarios"></a>Executar a validação sem cenários de nuvem

Execute a ferramenta de validação sem o **ServiceAdminCredential** parâmetro para ignorar os testes de cenário de nuvem em execução: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Executar a validação com cenários de nuvem

Fornecendo a ferramenta de validação com o **ServiceAdminCredentials** parâmetro executa os testes de cenário de nuvem por padrão: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Se você quiser executar cenários de nuvem apenas sem executar o restante dos testes, você pode usar o **Include** parâmetro para fazer isso: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

O nome de usuário do administrador de nuvem deve ser digitado no formato UPN: serviceadmin@contoso.onmicrosoft.com (Azure AD). Quando solicitado, digite a senha para a conta de administrador de nuvem.

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>Execute a ferramenta de validação para testar a preparação do sistema antes de instalar a atualização ou hotfix

Antes de iniciar a instalação de uma atualização ou hotfix, você deve executar a ferramenta de validação para verificar o status do Azure Stack:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Executar a ferramenta de validação para testar as configurações de backup de infraestrutura

*Antes de* configurar o backup de infraestrutura, você pode testar o caminho de compartilhamento de backup e de credenciais usando o **AzsBackupShareAccessibility** de teste: 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

*Após* configurar o backup, você pode executar **AzsBackupShareAccessibility** validar o compartilhamento é acessível a partir de ERCS:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Para testar as novas credenciais com o compartilhamento de backup configurado, execute: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as ferramentas de diagnóstico do Azure Stack e o log de problema, consulte [das ferramentas de diagnóstico do Azure Stack](azure-stack-diagnostics.md).

Para saber mais sobre como solucionar problemas, consulte [solução de problemas do Microsoft Azure Stack](azure-stack-troubleshooting.md).