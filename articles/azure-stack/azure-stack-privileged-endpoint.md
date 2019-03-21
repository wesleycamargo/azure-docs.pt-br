---
title: Usando o ponto de extremidade privilegiado no Azure Stack | Microsoft Docs
description: Mostra como usar o ponto de extremidade com privilégios (PEP) no Azure Stack (para um operador do Azure Stack).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 9eb2e8ddde13783eabf3d82173e6a2fa75ec2b06
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082663"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Usando o ponto de extremidade privilegiado no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Como um operador do Azure Stack, você deve usar o portal do administrador, PowerShell ou as APIs do Gerenciador de recursos do Azure para tarefas de gerenciamento diárias mais. No entanto, para alguns, menos as operações comuns, você precisa usar o *ponto de extremidade com privilégios* (PEP). O PEP é um console do PowerShell remoto pré-configurado que fornece recursos suficientes para ajudá-lo a realizar uma tarefa obrigatória. Usa o ponto de extremidade [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/jea/overview) expor somente um conjunto restrito de cmdlets. Para acessar o PEP e invocar um conjunto restrito de cmdlets, uma conta de baixo privilégio é usada. Nenhuma conta de administrador é necessária. Para obter segurança adicional, o script não é permitido.

Você pode usar o PEP para executar tarefas como a seguir:

- Para executar tarefas de nível inferior, como [coletar logs de diagnóstico](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Para executar muitas tarefas de integração de pós-implantação de datacenter para sistemas integrados, como a adição de encaminhadores do sistema de nome de domínio (DNS) após a implantação, configurando a integração do Microsoft Graph, integração de serviços de Federação do Active Directory (AD FS) rotação de certificado, etc.
- Para trabalhar com o suporte para obter acesso temporário e de alto nível para solução de problemas detalhada de um sistema integrado.

O PEP registra cada ação (e sua saída correspondente) que podem ser executadas na sessão do PowerShell. Isso fornece total transparência e a auditoria completa de operações. Você pode manter esses arquivos de log para auditorias no futuros.

> [!NOTE]
> No Azure Stack desenvolvimento ASDK (Kit), você pode executar alguns dos comandos disponíveis no PEP diretamente de uma sessão do PowerShell no host do kit de desenvolvimento. No entanto, você talvez queira testar algumas operações usando PEP, como coleta de log, como esse é o único método disponível para executar determinadas operações em um ambiente de sistemas integrados.

## <a name="access-the-privileged-endpoint"></a>O ponto de extremidade com privilégios de acesso

Você pode acessar o PEP por meio de uma sessão remota do PowerShell na máquina virtual que hospeda o PEP. ASDK, essa máquina virtual é denominada **AzS ERCS01**. Se você estiver usando um sistema integrado, há três instâncias do PEP, cada execução dentro de uma máquina virtual (*prefixar*-ERCS01, *prefixo*-ERCS02, ou *prefixo*- ERCS03) em hosts diferentes para garantir a resiliência. 

Antes de iniciar este procedimento para um sistema integrado, certifique-se de que você pode acessar o PEP pelo endereço IP ou por meio do DNS. Após a implantação inicial do Azure Stack, você pode acessar o PEP somente pelo endereço IP porque a integração do DNS é ainda não configurada. O fornecedor do hardware OEM fornecerá um arquivo JSON denominado **AzureStackStampDeploymentInfo** que contém os endereços IP PEP.


> [!NOTE]
> Por motivos de segurança, exigimos que você conectar ao PEP apenas de uma máquina de virtual protegido executando sobre o host de ciclo de vida do hardware ou de um computador dedicado e seguro, como um [estação de trabalho de acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). A configuração original do host de ciclo de vida do hardware não deve ser modificada de sua configuração original, incluindo a instalação de novo software, nem deve ser usado para conectar-se para o PEP.

1. Estabelece a relação de confiança.

    - Em um sistema integrado, execute o seguinte comando em uma sessão do Windows PowerShell com privilégios elevados para adicionar o PEP como um host confiável na máquina virtual protegida em execução no host de ciclo de vida do hardware ou a estação de trabalho de acesso privilegiado.

      ```PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Se você estiver executando o ASDK, entre para o host do kit de desenvolvimento.

2. Na máquina virtual protegida em execução no host de ciclo de vida do hardware ou a estação de trabalho de acesso privilegiado, abra uma sessão do Windows PowerShell. Execute os seguintes comandos para estabelecer uma sessão remota na máquina virtual que hospeda o PEP:
 
   - Em um sistema integrado:
     ```PowerShell
       $cred = Get-Credential

       Enter-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     O `ComputerName` parâmetro pode ser o endereço IP ou o nome DNS de uma das máquinas virtuais que hospeda o PEP. 
   - Se você estiver executando o ASDK:
     
     ```PowerShell
       $cred = Get-Credential

       Enter-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Quando solicitado, use as seguintes credenciais:

     - **Nome de usuário**: Especifique a conta CloudAdmin, no formato  **&lt; *domínio do Azure Stack*&gt;\cloudadmin**. (Para ASDK, é o nome de usuário **azurestack\cloudadmin**.)
     - **Senha**: Insira a mesma senha que foi fornecida durante a instalação para a conta de administrador de domínio AzureStackAdmin.

     > [!NOTE]
     > Se você não conseguir se conectar ao ponto de extremidade ERCS, tente as etapas um e dois novamente com o endereço IP de uma VM ERCS aos quais você já ainda não tentou se conectar.

3. Depois de se conectar, o prompt será alterado para **[*endereço IP ou VM ERCS nome*]: PS >** ou a **[azs-ercs01]: PS >**, dependendo do ambiente. A partir daqui, execute `Get-Command` para exibir a lista de cmdlets disponíveis.

   Muitos desses cmdlets destinados apenas para ambientes com sistemas integrados (por exemplo, os cmdlets relacionados à integração do datacenter). No ASDK, os seguintes cmdlets foram validados:

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Exit-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Measure-Object
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Dicas para usar o ponto de extremidade com privilégios 

Conforme mencionado acima, o PEP é um [JEA PowerShell](https://docs.microsoft.com/powershell/jea/overview) ponto de extremidade. Além de fornecer uma camada de segurança forte, um ponto de extremidade JEA reduz alguns dos recursos básicos do PowerShell, como o preenchimento com tab ou scripts. Se você tentar qualquer tipo de operação de script, a operação falhará com o erro **ScriptsNotAllowed**. Esse comportamento é esperado.

Portanto, por exemplo, para obter a lista de parâmetros para um cmdlet, você execute o seguinte comando:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

Como alternativa, você pode usar o [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) cmdlet para importar todos os cmdlets PEP para a sessão atual no computador local. Ao fazer isso, todos os cmdlets e funções do PEP agora estão disponíveis em seu computador local, juntamente com o preenchimento com tab e, muito mais em geral, de script. 

Para importar a sessão PEP em seu computador local, execute as seguintes etapas:

1. Estabelece a relação de confiança.

    -Em um sistema integrado, execute o seguinte comando em uma sessão do Windows PowerShell com privilégios elevados para adicionar o PEP como um host confiável na máquina virtual protegida em execução no host de ciclo de vida do hardware ou a estação de trabalho de acesso privilegiado.

      ```PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Se você estiver executando o ASDK, entre para o host do kit de desenvolvimento.

2. Na máquina virtual protegida em execução no host de ciclo de vida do hardware ou a estação de trabalho de acesso privilegiado, abra uma sessão do Windows PowerShell. Execute os seguintes comandos para estabelecer uma sessão remota na máquina virtual que hospeda o PEP:
 
   - Em um sistema integrado:
     ```PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     O `ComputerName` parâmetro pode ser o endereço IP ou o nome DNS de uma das máquinas virtuais que hospeda o PEP. 
   - Se você estiver executando o ASDK:
     
     ```PowerShell
      $cred = Get-Credential

      $session = New-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Quando solicitado, use as seguintes credenciais:

     - **Nome de usuário**: Especifique a conta CloudAdmin, no formato  **&lt; *domínio do Azure Stack*&gt;\cloudadmin**. (Para ASDK, é o nome de usuário **azurestack\cloudadmin**.)
     - **Senha**: Insira a mesma senha que foi fornecida durante a instalação para a conta de administrador de domínio AzureStackAdmin.

3. Importar sessão PEP para seu computador local
    ```PowerShell 
        Import-PSSession $session
    ```
4. Agora, você pode usar o preenchimento de guias e fazer o script como de costume sua sessão do PowerShell local com todas as funções e cmdlets do PEP, sem diminuir a postura de segurança do Azure Stack. Aproveite!


## <a name="close-the-privileged-endpoint-session"></a>Feche a sessão com privilégios de ponto de extremidade

 Como mencionado anteriormente, o PEP registra cada ação (e sua saída correspondente) que podem ser executadas na sessão do PowerShell. Você deve fechar a sessão usando o `Close-PrivilegedEndpoint` cmdlet. Esse cmdlet corretamente fecha o ponto de extremidade e transfere os arquivos de log para um compartilhamento de arquivo externo para retenção.

Para fechar a sessão do ponto de extremidade:

1. Crie um compartilhamento de arquivo externo que seja acessível pelo PEP. Em um ambiente do kit de desenvolvimento, você pode criar apenas um compartilhamento de arquivos no host do kit de desenvolvimento.
2. Execute o `Close-PrivilegedEndpoint` cmdlet. 
3. Você será solicitado para um caminho no qual armazenar o arquivo de log de transcrição. Especifique o compartilhamento de arquivos que você criou anteriormente, no formato &#92; &#92; *nome_do_servidor*&#92;*sharename*. Se você não especificar um caminho, o cmdlet falhar e a sessão permanecerá aberta. 

    ![Saída do cmdlet fechar PrivilegedEndpoint que mostra onde você pode especificar o caminho de destino de transcrição](media/azure-stack-privileged-endpoint/closeendpoint.png)

Depois que os arquivos de log da transcrição são transferidos com êxito para o compartilhamento de arquivos, eles são excluídos automaticamente o PEP. 

> [!NOTE]
> Se você fechar a sessão PEP usando os cmdlets `Exit-PSSession` ou `Exit`, ou apenas fechar o console do PowerShell, os logs de transcrição não são transferidas para um compartilhamento de arquivos. Eles permanecem no PEP. Na próxima vez que você executar `Close-PrivilegedEndpoint` e inclui um compartilhamento de arquivos, os logs de transcrição do anterior ões também serão transferidos. Não use `Exit-PSSession` ou `Exit` para fechar a sessão PEP; use `Close-PrivilegedEndpoint` em vez disso.


## <a name="next-steps"></a>Próximas etapas

[Ferramentas de diagnóstico do Azure Stack](azure-stack-diagnostics.md)
