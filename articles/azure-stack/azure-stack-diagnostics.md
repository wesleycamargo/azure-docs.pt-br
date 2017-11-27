---
title: "Diagnósticos no Azure Stack"
description: "Como coletar arquivos de log de diagnóstico na pilha do Azure"
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/22/2017
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 8afde912ca48297ae60eb7d05aa624a1d72c1637
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2017
---
# <a name="azure-stack-diagnostics-tools"></a>Ferramentas de diagnóstico de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*
 
A pilha do Azure é uma grande coleção de componentes trabalham juntos e interagir com o outro. Todos esses componentes geram seus próprios logs exclusivos. Isso pode tornar uma tarefa difícil, especialmente para erros provenientes de várias, interação de componentes da pilha do Azure para diagnosticar problemas. 

Nossas ferramentas de diagnóstico ajudam a garantir que o mecanismo de coleta de log é fácil e eficiente. O diagrama a seguir mostra como log ferramentas de coleta no trabalho da pilha do Azure:

![Ferramentas de diagnóstico do Azure de pilha](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Coletor de rastreamento
 
O coletor de rastreamento está habilitado por padrão. Ele continuamente é executado em segundo plano e coleta todos os logs de rastreamento de eventos para Windows (ETW) serviços de componentes na pilha do Azure e armazena em um compartilhamento local comum. 

Estas são as coisas importantes a saber sobre o coletor de rastreamento:
 
* O coletor de rastreamento é executado continuamente com limites de tamanho padrão. O padrão de tamanho máximo permitido para cada arquivo (200 MB) é **não** um tamanho de corte. Uma verificação de tamanho ocorre periodicamente (atualmente a cada 2 minutos) e se o arquivo atual > = 200 MB, ele será salvo e um novo arquivo é gerado. Também há um limite de (configurável) de 8 GB no tamanho total do arquivo gerado por sessão de evento. Quando esse limite é atingido, os arquivos mais antigos são excluídos conforme novos arquivos serão criados.
* Há um limite de idade de 5 dias os logs. Esse limite também é configurável. 
* Cada componente define as propriedades de configuração de rastreamento por meio de um arquivo JSON. Os arquivos JSON estiverem armazenados em **C:\TraceCollector\Configuration**. Se necessário, esses arquivos podem ser editados para alterar os limites de idade e tamanho dos logs coletados. As alterações a esses arquivos requerem uma reinicialização do *coletor de rastreamento de pilha do Microsoft Azure* serviço para que as alterações entrem em vigor.

O exemplo a seguir é um arquivo JSON de configuração de rastreamento para operações de FabricRingServices da VM XRP: 

```json
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**. Este parâmetro controla a idade dos arquivos para manter. Arquivos de log antigos são excluídos.
* **MaxSizeInMB**. Este parâmetro controla o limite de tamanho para um único arquivo. Se o tamanho é atingido, um novo arquivo. ETL é criado.
* **TotalSizeInMB**. Este parâmetro controla o tamanho total dos arquivos. etl gerados a partir de uma sessão de evento. Se o tamanho total do arquivo é maior do que esse valor de parâmetro, os arquivos mais antigos são excluídos.
  
## <a name="log-collection-tool"></a>Ferramenta de coleta de log
 
O comando do PowerShell **Get-AzureStackLog** pode ser usado para coletar logs de todos os componentes em um ambiente de pilha do Azure. Ele os salva em arquivos zip em um local definido pelo usuário. Se precisar de nossa equipe de suporte técnico os logs para ajudar a solucionar um problema, eles podem solicitar que você executar essa ferramenta.

> [!CAUTION]
> Esses arquivos de log podem conter informações de identificação pessoal (PII). Leve isso em consideração antes de lançar publicamente os arquivos de log.
 
Seguem alguns exemplos de tipos de log são coletados:
*   **Logs de implantação de pilha do Azure**
*   **Logs de eventos do Windows**
*   **Logs de Panther**
*   **Logs de cluster**
*   **Logs de diagnóstico de armazenamento**
*   **Logs do ETW**

Esses arquivos são coletados pelo coletor de rastreamento e armazenados em um compartilhamento de onde **AzureStackLog Get** recupera-los.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Para executar o Get-AzureStackLog em um sistema de Kit de desenvolvimento na pilha do Azure (ASDK)
1. Faça logon como **AzureStack\CloudAdmin** no host.
2. Abra uma janela do PowerShell como administrador.
3. Execute o **AzureStackLog Get** cmdlet do PowerShell.

   **Exemplos**

    Colete todos os logs de todas as funções:

    ```powershell
    Get-AzureStackLog -OutputPath C:\AzureStackLogs
    ```

    Colete logs de funções VirtualMachines e BareMetal:

    ```powershell
    Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
    ```

    Colete logs de funções VirtualMachines e BareMetal, com data de filtragem para arquivos de log para as últimas 8 horas:
    
    ```powershell
    Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
    ```

    Colete logs de funções VirtualMachines e BareMetal, com filtragem para arquivos de log para o período de tempo entre 8 horas atrás e 2 horas atrás de data:

    ```powershell
    Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
    ```

### <a name="to-run-get-azurestacklog-on-an-azure-stack-integrated-system"></a>Para executar o Get-AzureStackLog em uma pilha do Azure sistema integrado

Para executar a ferramenta de coleta de log em um sistema integrado, você precisa ter acesso para o ponto de extremidade privilegiado (PEP). Aqui está um exemplo de script que você pode executar usando o PEP para coletar logs em um sistema integrado:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- Quando você coletar logs do PEP, especifique o **OutputPath** parâmetro para ser um local no computador Host de ciclo de vida de Hardware (HLH). Certifique-se também de que o local está criptografado.
- Os parâmetros **OutputSharePath** e **OutputShareCredential** são opcionais e são usadas quando você carrega os logs para uma pasta compartilhada externa. Use estes parâmetros *além* para **OutputPath**. Se **OutputPath** não for especificado, a ferramenta de coleta de log usa a unidade do sistema da VM PEP para armazenamento. Isso pode causar falha porque o espaço em disco é limitado no script.
- Conforme mostrado no exemplo anterior, o **FromDate** e **ToDate** parâmetros podem ser usados para coletar logs por um período de tempo específico. Isso pode ser útil para cenários como coletar logs após a aplicação de um pacote de atualização em um sistema integrado.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Considerações de parâmetro para ASDK e sistemas integrados

- Se o **FromDate** e **ToDate** parâmetros não forem especificados, os logs são coletados para as últimas quatro horas por padrão.
- Você pode usar o **TimeOutInMinutes** para definir o tempo limite para coleta de log. Ele é definido como 150 (2,5 horas) por padrão.

- No momento, você pode usar o **FilterByRole** parâmetro à coleção de log de filtros, as seguintes funções:

   |   |   |   |
   | - | - | - |
   | ACSMigrationService     | ACSMonitoringService   | ACSSettingsService |
   | ACS                     | ACSFabric              | ACSFrontEnd        |
   | ACSTableMaster          | ACSTableServer         | ACSWac             |
   | ADFS                    | ASAppGateway           | BareMetal          |
   | BRP                     | CA                     | IDC                |
   | CRP                     | DeploymentMachine      | DHCP               |
   | Domínio                  | ECE                    | ECESeedRing        | 
   | FabricRing              | FabricRingServices     | FRP                |
   | Gateway                 | HealthMonitoring       | HRP                |   
   | IBC                     | InfraServiceController |KeyVaultAdminResourceProvider|
   | KeyVaultControlPlane    | KeyVaultDataPlane      | NC                 |   
   | NonPrivilegedAppGateway | NRP                    | SeedRing           |
   | SeedRingServices        | SLB                    | SQL                |   
   | SRP                     | Armazenamento                | StorageController  |
   | URP                     | UsageBridge            | VirtualMachines    |  
   | FOI                     | WASPUBLIC              | WDS                |


### <a name="additional-considerations"></a>Considerações adicionais

* O comando leva algum tempo para executar com base em quais funções coletar os logs. Fatores também incluem a duração de tempo especificada para a coleta de log e os números de nós no ambiente de pilha do Azure.
* Após a conclusão da coleta de log, verifique a nova pasta criada no **OutputPath** parâmetro especificado no comando.
* Cada função tem seus logs nos arquivos zip individuais. Dependendo do tamanho dos logs coletados, uma função pode ter seus logs dividida em vários arquivos zip. Para função, se você quiser ter todos os arquivos de log foi descompactados em para uma única pasta, use uma ferramenta que pode descompactar em massa (como 7zip). Selecione todos os arquivos compactados para a função e selecione **extrair aqui**. Isso descompacta todos os arquivos de log para essa função em uma única pasta mesclada.
* Um arquivo chamado **Get-AzureStackLog_Output.log** também é criado na pasta que contém os arquivos de log compactado. Esse arquivo é um log de saída do comando, que pode ser usado para solucionar problemas durante a coleta de log.
* Para investigar uma falha específica, os logs podem ser necessários de mais de um componente.
    -   Logs de eventos para todas as VMs de infraestrutura e de sistema são coletados no *VirtualMachines* função.
    -   Sistema e logs de eventos para todos os hosts são coletados no *BareMetal* função.
    -   Logs de eventos de Cluster de failover e Hyper-V são coletados no *armazenamento* função.
    -   ACS logs são coletados no *armazenamento* e *ACS* funções.

> [!NOTE]
> Limites de tamanho e idade são impostos os logs coletados da maneira que é essencial para garantir a utilização eficiente de seu espaço de armazenamento para garantir que ele não tenha inundado com logs. No entanto, para diagnosticar um problema você às vezes precisa logs que talvez não exista mais devido a esses limites. Portanto, é **altamente recomendável** que você descarregar os logs em um espaço de armazenamento externo (uma conta de armazenamento no Azure, um dispositivo de armazenamento local adicional etc.) a cada 8 a 12 horas e mantê-los lá de 1 a 3 meses, dependendo de sua requisitos. Além disso, verifique o local de armazenamento é criptografado.

## <a name="next-steps"></a>Próximas etapas
[Solução de problemas do Microsoft Azure Stack](azure-stack-troubleshooting.md)
