---
title: "Usando o ponto de extremidade com privilégios na pilha do Azure | Microsoft Docs"
description: Mostra como usar o ponto de extremidade privilegiado (PEP) na pilha do Azure (para um operador de pilha do Azure).
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: mabrigg
ms.openlocfilehash: 7f95014ac9186815d8ea0c7d271e5c6e19252d73
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Usando o ponto de extremidade com privilégios na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Como um operador de pilha do Azure, você deve usar o portal do administrador, PowerShell ou APIs do Gerenciador de recursos do Azure para tarefas de gerenciamento mais diárias. No entanto, para alguns menos operações comuns, você precisa usar o *privilegiado do ponto de extremidade* (PEP). O PEP é um console do PowerShell remoto pré-configurado que fornece recursos suficientes para ajudá-lo a executar uma tarefa obrigatória. O ponto de extremidade usa [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/en-us/powershell/jea/overview) para expor apenas um conjunto restrito de cmdlets. Para acessar o PEP e invocar um conjunto restrito de cmdlets, uma conta de baixo privilégio é usada. Nenhuma conta de administrador é necessária. Para obter segurança adicional, o script não é permitido.

Você pode usar o PEP para executar tarefas como a seguir:

- Para executar tarefas de nível inferior, como [coletar logs de diagnóstico](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Para executar muitas tarefas de integração de pós-implantação datacenter para sistemas integrados, como a adição de encaminhadores do sistema de nome de domínio (DNS) após a implantação, configurar a integração do Microsoft Graph, integração de serviços de Federação do Active Directory (AD FS), rotação de certificado, etc.
- Para trabalhar com o suporte para obter acesso temporário de alto nível para solução de problemas detalhada de um sistema integrado.

O PEP registra cada ação (e sua saída correspondente) que podem ser executadas na sessão do PowerShell. Isso proporciona transparência total e auditoria completa de operações. Você pode manter esses arquivos de log para auditorias no futuros.

> [!NOTE]
> No Azure pilha Development Kit (ASDK), você pode executar alguns dos comandos disponíveis no PEP diretamente de uma sessão do PowerShell no host do kit de desenvolvimento. No entanto, você talvez queira testar algumas operações usando PEP, como coleta de log, porque este é o único método disponível para executar determinadas operações em um ambiente de sistemas integrados.

## <a name="access-the-privileged-endpoint"></a>O ponto de extremidade com privilégios de acesso

Você pode acessar a PEP por meio de uma sessão remota do PowerShell na máquina virtual que hospeda o PEP. ASDK, essa máquina virtual é denominada **AzS ERCS01**. Se você estiver usando um sistema integrado, há três instâncias do PEP, cada um executado em uma máquina virtual (*prefixo*-ERCS01, *prefixo*-ERCS02, ou *prefixo*- ERCS03) em hosts diferentes para garantir a resiliência. 

Antes de iniciar este procedimento para um sistema integrado, certifique-se de que você pode acessar a PEP por endereço IP ou por meio do DNS. Após a implantação inicial da pilha do Azure, você pode acessar o PEP somente pelo endereço IP porque a integração do DNS é ainda não configurada. O fornecedor do hardware OEM fornecerá um arquivo JSON chamado **AzureStackStampDeploymentInfo** que contém os endereços IP PEP.

Recomendamos que você se conecte ao PEP apenas do host de ciclo de vida de hardware ou de um computador dedicado e seguro, como um [estação de trabalho de acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

1. Acesse sua estação de trabalho de acesso privilegiado.

    - Em um sistema integrado, execute o seguinte comando para adicionar o PEP como um host confiável em seu host do ciclo de vida de hardware ou uma estação de trabalho de acesso privilegiado.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Se você estiver executando o ADSK, entre para o host do kit de desenvolvimento.

2. No host do ciclo de vida de hardware ou estação de trabalho de acesso privilegiado, abra uma sessão do Windows PowerShell com privilégios elevados. Execute os seguintes comandos para estabelecer uma sessão remota na máquina virtual que hospeda o PEP:
 
    - Em um sistema integrado:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      O `ComputerName` parâmetro pode ser o endereço IP ou o nome DNS de uma das máquinas virtuais que hospeda o PEP. 
    - Se você estiver executando o ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Quando solicitado, use as seguintes credenciais:

      - **Nome de usuário**: especifique a conta de CloudAdmin, no formato  **&lt; *domínio Azure pilha*&gt;\cloudadmin**. (Para ASDK, o nome de usuário é **azurestack\cloudadmin**.)
      - **Senha**: insira a mesma senha que foi fornecida durante a instalação para a conta de administrador de domínio AzureStackAdmin.
    
3.  Depois de se conectar, o prompt será alterado para **[*endereço IP ou ERCS VM nome*]: PS >** ou **[azs ercs01]: PS >**, dependendo do ambiente. A partir daqui, execute `Get-Command` para exibir a lista de cmdlets disponíveis.

    ![Lista de mostrando de saída do cmdlet Get-Command dos comandos disponíveis](media/azure-stack-privileged-endpoint/getcommandoutput.png)

    Muitos desses cmdlets destinam-se apenas para ambientes de sistema integrado (como os cmdlets relacionados à integração do Data Center). Em ASDK, os cmdlets a seguir foram validados:

    - Clear-Host
    - Close-PrivilegedEndpoint
    - Exit-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Objeto de medida
    - New-CloudAdminUser
    - Out-Default
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Dicas para usar o ponto de extremidade com privilégios 

Conforme mencionado acima, o PEP é um [PowerShell JEA](https://docs.microsoft.com/en-us/powershell/jea/overview) ponto de extremidade. Além de fornecer uma camada de alta segurança, um ponto de extremidade JEA reduz alguns dos recursos básicos do PowerShell, como preenchimento com tab ou script. Se você tentar qualquer tipo de operação de script, a operação falhará com o erro **ScriptsNotAllowed**. Este comportamento é esperado.

Portanto, por exemplo, para obter a lista de parâmetros para um cmdlet, você execute o seguinte comando:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

Como alternativa, você pode usar o [Import-PSSession](https://docs.microsoft.com/en-us/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) para importar todos os cmdlets PEP para a sessão atual no computador local. Ao fazer isso, todos os cmdlets e funções do PEP agora estão disponíveis em seu computador local, junto com o preenchimento com tab e, mais em geral, de script. 

Para importar a sessão PEP em seu computador local, execute as seguintes etapas:

1. Acesse sua estação de trabalho de acesso privilegiado.

    - Em um sistema integrado, execute o seguinte comando para adicionar o PEP como um host confiável em seu host do ciclo de vida de hardware ou uma estação de trabalho de acesso privilegiado.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Se você estiver executando o ADSK, entre para o host do kit de desenvolvimento.

2. No host do ciclo de vida de hardware ou estação de trabalho de acesso privilegiado, abra uma sessão do Windows PowerShell com privilégios elevados. Execute os seguintes comandos para estabelecer uma sessão remota na máquina virtual que hospeda o PEP:
 
    - Em um sistema integrado:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      O `ComputerName` parâmetro pode ser o endereço IP ou o nome DNS de uma das máquinas virtuais que hospeda o PEP. 
    - Se você estiver executando o ADSK:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Quando solicitado, use as seguintes credenciais:

      - **Nome de usuário**: especifique a conta de CloudAdmin, no formato  **&lt; *domínio Azure pilha*&gt;\cloudadmin**. (Para ASDK, o nome de usuário é **azurestack\cloudadmin**.)
      - **Senha**: insira a mesma senha que foi fornecida durante a instalação para a conta de administrador de domínio AzureStackAdmin.

3. Importar a sessão PEP em seu computador local
    ````PowerShell 
        Import-PSSession $session
    ````
4. Agora, você pode usar a conclusão de guia e fazer scripts como de costume na sua sessão do PowerShell local com todas as funções e os cmdlets de PEP, sem prejudicar a postura de segurança da pilha do Azure. Aproveite!


## <a name="close-the-privileged-endpoint-session"></a>Feche a sessão do ponto de extremidade com privilégios

 Como mencionado anteriormente, o PEP registra cada ação (e sua saída correspondente) que podem ser executadas na sessão do PowerShell. Você deve fechar a sessão usando o `Close-PrivilegedEndpoint` cmdlet. Esse cmdlet corretamente fecha o ponto de extremidade e transfere os arquivos de log para um compartilhamento de arquivo externo para retenção.

Para fechar a sessão do ponto de extremidade:

1. Crie um compartilhamento de arquivo externo que seja acessível pelo PEP. Em um ambiente do kit de desenvolvimento, você pode criar apenas um compartilhamento de arquivos no host do kit de desenvolvimento.
2. Execute o `Close-PrivilegedEndpoint` cmdlet. 
3. Você for solicitado a fornecer um caminho para armazenar o arquivo de log de transcrição. Especifique o compartilhamento de arquivo que você criou anteriormente, o formato &#92; & #92. *servername*&#92; *ShareName*. Se você não especificar um caminho, o cmdlet falhar e a sessão permanecerá aberta. 

    ![Saída do cmdlet fechar PrivilegedEndpoint que mostra onde você pode especificar o caminho de destino transcrição](media/azure-stack-privileged-endpoint/closeendpoint.png)

Depois que os arquivos de log transcrição são transferidos com êxito para o compartilhamento de arquivos, eles são automaticamente excluídos do PEP. Se você fechar a sessão PEP usando os cmdlets `Exit-PSSession` ou `Exit`, ou você apenas feche o console do PowerShell, os logs de transcrição não transferir para um compartilhamento de arquivos. Eles permanecem na PEP. Na próxima vez que você executar `Close-PrivilegedEndpoint` e inclui um compartilhamento de arquivos, os logs de transcrição do anterior ões também serão transferidos.

## <a name="next-steps"></a>Próximas etapas
[Ferramentas de diagnóstico do Azure de pilha](azure-stack-diagnostics.md)