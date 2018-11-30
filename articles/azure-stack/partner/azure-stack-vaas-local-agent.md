---
title: Implantar o agente local | Microsoft Docs
description: Implante o agente local para validação de pilha do Azure como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 0608da33e816b40f7fadbeb1b5da3feb926c28aa
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334050"
---
# <a name="deploy-the-local-agent"></a>Implantar o agente local

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Saiba como usar a validação como um agente local de serviço (VaaS) para verificar o seu hardware. O agente local deve ser implantado na solução do Azure Stack que estão sendo validada antes da execução de testes de validação.

> [!Note]  
> Certifique-se de que o computador que está executando o agente local não perde o acesso de saída à internet. Essa máquina deve ser acessível somente aos usuários que você tenha autorizado a usar VaaS em nome do seu locatário.

Para implantar o agente local:

1. Instalar o agente local
2. Executar as verificações de integridade
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
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Execute o seguinte comando para instalar as dependências do agente local:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Parâmetros**

    | Parâmetro | DESCRIÇÃO |
    | --- | --- |
    | aadServiceAdminUser | O usuário de administrador global para seu locatário do Azure AD. Por exemplo pode ser, vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | A senha para o usuário administrador global. |
    | AadTenantId | ID de locatário do Azure AD para a conta do Azure registrado com a validação como um serviço. |
    | ExternalFqdn | Você pode obter o nome de domínio totalmente qualificado do arquivo de configuração. Para obter instruções, consulte [parâmetros comuns de fluxo de trabalho na validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md). |
    | Região | A região do seu locatário do AD do Azure. |

O comando baixa uma imagem do (PIR) do repositório de imagem pública (SO do VHD) e a cópia de um armazenamento de BLOBs do Azure no armazenamento do Azure Stack.

![Baixar pré-requisitos](media/installingprereqs.png)

> [!Note]
> Se você estiver tendo baixa velocidade de rede ao baixar essas imagens, baixá-los separadamente em um compartilhamento local e especificar o parâmetro **- LocalPackagePath** *FileShareOrLocalPath*. Você pode encontrar mais diretrizes sobre o download do PIR na seção [manipulam conectividade de rede lento](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) dos [solucionar problemas de validação como um serviço](azure-stack-vaas-troubleshoot.md).

## <a name="checks-before-starting-the-tests"></a>Verificações antes de iniciar os testes

Os testes executados ações remotas. O computador que executa os testes deve ter acesso aos pontos de extremidade do Azure Stack, caso contrário, os testes não funcionará. Se você estiver usando o agente de VaaS local, use a máquina em que o agente será executado. Você pode verificar se o seu computador tem acesso aos pontos de extremidade do Azure Stack, executando as seguintes verificações:

1. Verifique se o URI de Base pode ser acessado. Abra um prompt de comando ou shell bash, e execute o seguinte comando, substituindo `<EXTERNALFQDN>` com o FQDN externo do seu ambiente:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Abra um navegador da web e vá para `https://adminportal.<EXTERNALFQDN>` para verificar se o Portal MAS pode ser acessado.

3. Valores de nome e a senha de administrador fornecidos ao criar a aprovação do teste de serviço entrar usando o Azure AD.

4. Verifique a integridade do sistema executando o **teste AzureStack** cmdlet do PowerShell conforme descrito em [executar um teste de validação para o Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test). Corrija quaisquer erros e avisos antes de iniciar todos os testes.

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

Um agente é identificado exclusivamente por seu nome. Por padrão, ele usa o nome FQDN (nome) de domínio totalmente qualificado da máquina de onde ele foi iniciado. Você deve minimizar a janela para evitar qualquer seleciona acidental na janela de como alterar o foco pausa todas as outras ações.

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de validação como um serviço](azure-stack-vaas-troubleshoot.md)
- [Validação como um conceitos principais do serviço](azure-stack-vaas-key-concepts.md)
- [Guia de início rápido: Usar a validação como um portal de serviço para agendar seu primeiro teste](azure-stack-vaas-schedule-test-pass.md)