---
title: Implantar o agente local e testar máquinas virtuais de imagem na validação do Azure Stack como um serviço | Microsoft Docs
description: Implantar o agente local e testar imagens das máquinas virtuais para a validação do Azure Stack como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 9d32c23f66563988d023df3bf6a33efa30237e57
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235228"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>Implantar as máquinas de virtuais de teste e o agente locais

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Saiba como usar a validação como um agente local de serviço (VaaS) para verificar o seu hardware. O agente local deve ser implantado na solução do Azure Stack que estão sendo validada antes da execução de testes de validação.

> [!Note]  
> Certifique-se de que a máquina, em que o agente local estiver em execução, não perca o acesso à Internet. A máquina só deve ser acessível a usuários que estão autorizados a usar o Azure Stack VaaS.

Para testar suas máquinas virtuais:

1. Instalar o agente local
2. Injetar falhas no seu sistema
3. Executar o agente local

## <a name="download-and-start-the-local-agent"></a>Baixe e inicie o agente local

Baixe o agente para um computador que atenda aos pré-requisitos em seu data center que não faz parte do sistema do Azure Stack, mas que tem acesso a todos os pontos de extremidade do Azure Stack.

### <a name="machine-prerequisites"></a>Pré-requisitos de máquina

Verifique se o seu computador atende aos seguintes critérios:

- Acesso a todos os pontos de extremidade do Azure Stack
- .NET 4.6 e o PowerShell 5.0 instalado
- Pelo menos 8 GB de RAM
- Mínimo de 8 processadores de núcleo
- Espaço em disco mínimo de 200 GB
- Conectividade de rede estável com a internet

O Azure Stack é o sistema em teste. A máquina não deve ser parte da pilha do Azure ou hospedado na nuvem do Azure Stack.

### <a name="download-and-install-the-agent"></a>Baixe e instale o agente

1. Abra o Windows PowerShell em um prompt com privilégios elevados no computador que você usará para executar os testes.
2. Execute o seguinte comando para baixar o agente local:

    ```PowerShell  
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. Execute o seguinte comando para instalar as dependências do agente local:

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **Parâmetros**

    | Parâmetro | DESCRIÇÃO |
    | --- | --- |
    | aadServiceAdminUser | O usuário de administrador global para seu locatário do Azure AD. Por exemplo pode ser, vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | A senha para o usuário administrador global. |
    | AadTenantId | ID de locatário do Azure AD para a conta do Azure registrado com a validação como um serviço. |
    | ExternalFqdn | Você pode obter o nome de domínio totalmente qualificado do arquivo de configuração. Para obter instruções, consulte [parâmetros de validação como um serviço do Azure Stack de teste](azure-stack-vaas-parameters-test.md). |
    | Região | A região do seu locatário do AD do Azure. |

O comando baixa uma imagem do (PIR) do repositório de imagem pública (SO do VHD) e a cópia de um armazenamento de BLOBs do Azure no armazenamento do Azure Stack. 

![Baixar pré-requisitos](media/installingprereqs.png)

> [!Note]  
> Se você estiver tendo baixa velocidade de rede ao baixar essas imagens, baixá-los separadamente em um compartilhamento local e especificar o parâmetro **- LocalPackagePath** *FileShareOrLocalPath*. Você pode encontrar mais diretrizes sobre o download do PIR na seção [manipulam conectividade de rede lento](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) dos [solucionar problemas de validação como um serviço](azure-stack-vaas-troubleshoot.md).

## <a name="fault-injection"></a>Injeção de falha

A Microsoft projetou o Azure Stack para resiliência e a tolerar vários tipos de falhas de hardware e software. Injeção de falha aumenta a taxa de falhas no sistema. Esse aumento ajuda você a descobrir problemas anteriormente para que você pode reduzir o número de incidentes que derrubar o sistema.

Execute os seguintes comandos para injetar falhas no seu sistema.

1. Abra o Windows PowerShell em um prompt elevado.

2. Execute o comando a seguir:

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>Execute o agente

1. Abra o Windows PowerShell em um prompt elevado.

2. Execute o comando a seguir:

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **Parâmetros**  
    
    | Parâmetro | DESCRIÇÃO |
    | --- | --- |
    | VaaSUserId | ID de usuário usada para entrar no Portal do VaaS (por exemplo, UserName@Contoso.com) |
    | VaaSTenantId | ID de locatário do Azure AD para a conta do Azure registrado com a validação como um serviço. |

    > [!Note]  
    > Quando você executa o agente, o diretório de trabalho atual deve ser o local do executável, o host do mecanismo tarefa **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Se você não vir os erros relatados, o agente local foi bem-sucedida. O texto de exemplo a seguir é exibida na janela do console.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Agente iniciado](media/startedagent.png)

Um agente é identificado exclusivamente por seu nome. Por padrão, ele usa o nome FQDN (nome) de domínio totalmente qualificado da máquina de onde ele foi iniciado. Você deve minimizar a janela para evitar qualquer cliques acidentais na janela de como alterar o foco pausa todas as outras ações.

## <a name="next-steps"></a>Próximas etapas

- [Validar uma nova solução do Azure Stack](azure-stack-vaas-validate-solution-new.md)  
- Se você tiver conectividade com a Internet lenta ou intermitente, você pode baixar a imagem do PIR. Para obter mais informações, consulte [manipulam conectividade de rede lenta](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).
- Para saber mais sobre [validação do Azure Stack como um serviço](https://docs.microsoft.com/azure/azure-stack/partner).
