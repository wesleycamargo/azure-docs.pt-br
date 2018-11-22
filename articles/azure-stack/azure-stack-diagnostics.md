---
title: Diagnósticos no Azure Stack
description: Como coletar arquivos de log de diagnóstico no Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/20/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: ef8a01228ff31afa78f469b2ad80b864fc9d3497
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283445"
---
# <a name="azure-stack-diagnostics-tools"></a>Ferramentas de diagnóstico do Azure Stack

O Azure Stack é uma grande coleção de componentes trabalham em conjunto e interagir entre si. Todos esses componentes geram seus próprios logs exclusivos. Isso pode tornar uma tarefa desafiadora, especialmente para erros provenientes de vários componentes do Azure Stack de interação de diagnóstico de problemas. 

Nossas ferramentas de diagnóstico ajudam a garantir que o mecanismo de coleta de log é fácil e eficiente. A diagrama a seguir mostra como logon coleção de ferramentas no trabalho do Azure Stack:

![Ferramentas de diagnóstico do Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Coletor de rastreamento
 
O coletor de rastreamento é habilitado por padrão e é executada continuamente em segundo plano para coletar todos os logs do Event Tracing for Windows (ETW) de serviços de componentes do Azure Stack. Logs ETW são armazenados em um compartilhamento local comum com um limite de duração de cinco dias. Quando esse limite é atingido, os arquivos mais antigos são excluídos conforme novos são criados. O tamanho máximo do padrão permitido para cada arquivo é de 200 MB. Uma verificação de tamanho ocorre a cada 2 minutos, e se o arquivo atual é > = 200 MB, ele será salvo e um novo arquivo é gerado. Também há um limite de 8 GB no tamanho total do arquivo gerado por sessão de evento. 

## <a name="log-collection-tool"></a>Ferramenta de coleta de log
 
O cmdlet do PowerShell **Get-AzureStackLog** pode ser usado para coletar logs de todos os componentes em um ambiente do Azure Stack. Ele os salva em arquivos zip em um local definido pelo usuário. Se a equipe de suporte técnico do Azure Stack precisar seus logs para ajudar a solucionar um problema, eles podem solicitar que você execute essa ferramenta.

> [!CAUTION]
> Esses arquivos de log podem conter informações de identificação pessoal (PII). Leve isso em conta antes de lançar publicamente os arquivos de log.
 
Seguem alguns tipos de log de exemplo que são coletados:
*   **Logs de implantação do Azure Stack**
*   **Logs de eventos do Windows**
*   **Logs de Panther**
*   **Logs do cluster**
*   **Logs de diagnóstico do armazenamento**
*   **Logs do ETW**

Esses arquivos são coletados e salvos em um compartilhamento pelo coletor de rastreamento. O **Get-AzureStackLog** cmdlet do PowerShell, em seguida, pode ser usado para coletá-los quando necessário.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Para executar o Get-AzureStackLog no Azure Stack sistemas integrados 
Para executar a ferramenta de coleta de log em um sistema integrado, você precisa ter acesso para o ponto de extremidade com privilégios (PEP). Aqui está um exemplo de script que você pode executar usando o PEP para coletar os logs em um sistema integrado:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```



### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Para executar o Get-AzureStackLog em um sistema de Kit de desenvolvimento na pilha do Azure (ASDK)
Use estas etapas para executar o Get-AzureStackLog em um computador de host ASDK.

1. Entrar como **AzureStack\CloudAdmin** no computador host ASDK.
2. Abra uma nova janela do PowerShell como administrador.
3. Execute o **Get-AzureStackLog** cmdlet do PowerShell.

**Exemplos:**

  Colete todos os logs de todas as funções:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred
  ```

  Colete logs de VirtualMachines e bare-metal de funções:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

  Colete logs de funções VirtualMachines e bare-metal, com data de filtragem para arquivos de log para as últimas 8 horas:
    
  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Colete logs de funções VirtualMachines e bare-metal, com filtragem para arquivos de log para o período de tempo entre 8 horas atrás e 2 horas atrás de data:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Considerações de parâmetro para ASDK e sistemas integrados

- Os parâmetros **OutputSharePath** e **OutputShareCredential** são usados para armazenar os logs em um usuário especificado local.

- O **FromDate** e **ToDate** parâmetros podem ser usados para coletar logs para um determinado período de tempo. Se esses parâmetros não forem especificados, os logs são coletados para as últimas quatro horas por padrão.

- Use o **FilterByNode** parâmetro para filtrar logs por nome do computador. Por exemplo: 

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```
- Use o **FilterByLogType** parâmetro para filtrar logs por tipo. Você pode optar por filtrar por arquivo, compartilhamento ou WindowsEvent. Por exemplo: 

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByLogType File
    ```
- Você pode usar o **TimeOutInMinutes** para definir o tempo limite para coleta de log. Ele é definido como 150 (2,5 horas) por padrão.
- Coleta de log de arquivo de despejo é desabilitada por padrão. Para habilitá-lo, use o **IncludeDumpFile** Troque o parâmetro. 
- No momento, você pode usar o **FilterByRole** parâmetro à coleção de log de filtros, as seguintes funções:

 |   |   |   |    |
 | - | - | - | -  |   
 |ACS                   |CacheService                   |IBC                            |OEM|
 |ACSDownloadService    |Computação                        |InfraServiceController         |OnboardRP|
 |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE|
 |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
 |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
 |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
 |ACSMonitoringService  |Domínio                         |KeyVaultInternalDataPlane      |SeedRingServices|
 |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
 |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
 |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
 |ACSWac                |ExternalDNS                    |MetricsRP                      |Armazenamento|
 |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
 |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
 |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
 |AzureBridge           |FRP                            |MonRP                          |VirtualMachines   |
 |AzureMonitor          |Gateway                        |NC                             |FOI|
 |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
 |BRP                   |HintingServiceV2               |NRP                            |   |
 |CA                    |HRP                            |OboService                     |   |
 |   |   |   |    |

### <a name="additional-considerations"></a>Considerações adicionais

* O comando leva algum tempo para executar com base em quais funções coletar os logs. Fatores que contribuem também incluem a duração de tempo especificada para a coleta de log e os números de nós no ambiente do Azure Stack.
* Como execuções de coleta de log, verifique a nova pasta criada na **OutputSharePath** parâmetro especificado no comando.
* Cada função possui seus logs em arquivos zip individuais. Dependendo do tamanho dos logs coletados, uma função pode ter seus logs divididos em vários arquivos zip. Para função, se você quiser ter todos os arquivos de log descompactados em para uma única pasta, use uma ferramenta que pode descompactar em massa (como 7zip). Selecione todos os arquivos compactados para a função e selecione **extrair aqui**. Isso descompacta todos os arquivos de log para essa função em uma única pasta mesclada.
* Um arquivo chamado **Get-AzureStackLog_Output.log** também é criado na pasta que contém os arquivos de log compactado. Esse arquivo é um log de saída do comando, que pode ser usado para solucionar problemas durante a coleta de log. Às vezes, o arquivo de log inclui `PS>TerminatingError` entradas que podem ser ignoradas com segurança, a menos que faltam arquivos de log esperado após execuções de coleta de log.
* Para investigar uma falha específica, os logs podem ser necessários de mais de um componente.
    -   Sistema e logs de eventos para todas as VMs de infraestrutura são coletados na *VirtualMachines* função.
    -   Sistema e logs de eventos para todos os hosts são coletados na *bare-metal* função.
    -   Logs de eventos de Cluster de failover e Hyper-V são coletados na *armazenamento* função.
    -   Logs do ACS são coletados na *armazenamento* e *ACS* funções.

> [!NOTE]
> Os logs coletados conforme é essencial para garantir que a utilização eficiente de seu espaço de armazenamento para garantir que nada é inundado com os logs são impostos limites de tamanho e idade. No entanto, para diagnosticar um problema você precisa, às vezes, os logs que talvez não exista mais devido a esses limites. Portanto, é **altamente recomendado** descarregamento de logs para um espaço de armazenamento externo (uma conta de armazenamento do Azure, um dispositivo de armazenamento adicional no local etc.) a cada 8 a 12 horas e mantê-los lá por 1 a 3 meses, dependendo do seu requisitos. Além disso, verifique se que o local de armazenamento é criptografado.

## <a name="next-steps"></a>Próximas etapas
[Solução de problemas do Microsoft Azure Stack](azure-stack-troubleshooting.md)

