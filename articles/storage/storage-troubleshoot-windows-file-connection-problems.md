---
title: "Solução de problemas de Armazenamento de Arquivos do Azure no Windows | Microsoft Docs"
description: "Solução de problemas de Armazenamento de Arquivos do Azure File no Windows"
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 810b4c236975a25d12b2e350d0a6cd5a39eb30aa
ms.contentlocale: pt-br
ms.lasthandoff: 06/30/2017


---
# <a name="troubleshoot-azure-file-storage-problems-in-windows"></a>Solução de problemas de Armazenamento de Arquivos do Azure File no Windows

Este artigo lista os problemas comuns relacionados ao Armazenamento de Arquivos do Microsoft Azure quando você se conecta a partir de clientes Windows. Ele também fornece possíveis causas e soluções para esses problemas. Além das etapas de solução de problemas neste artigo, você também pode usar o [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) para garantir que o ambiente de cliente do Windows possui os pré-requisitos corretos. O AzFileDiagnostics automatiza a detecção da maioria dos sintomas mencionados neste artigo e ajuda a configurar seu ambiente para obter um desempenho ideal.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Erro 53, Erro 67 ou Erro 87 ao montar ou desmontar um compartilhamento de arquivos do Azure

Quando você tenta montar um compartilhamento de arquivos do local ou de um datacenter diferente, você poderá receber os seguintes erros:

- Ocorreu um erro de sistema 53. O caminho da rede não foi encontrado.
- Ocorreu um erro de sistema 67. O nome de rede não foi encontrado.
- Ocorreu um erro de sistema 87. O parâmetro está incorreto.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação não criptografado

Por motivos de segurança, as conexões para compartilhamentos de arquivos do Azure são bloqueadas se o canal de comunicação não está criptografado e a tentativa de conexão não é feita do mesmo datacenter onde residem os compartilhamentos de arquivos do Azure. A criptografia de canal de comunicação é fornecida somente quando o sistema operacional do cliente do usuário não dá suporte à criptografia SMB.

O Windows 8, o Windows Server 2012 e versões posteriores de cada solicitação de negociação de sistema que inclua o SMB 3.0, que dá suporte à criptografia.

### <a name="solution-for-cause-1"></a>Solução para a causa 1

Conecte-se a partir de um cliente que faz o seguinte:

- Atende aos requisitos do Windows 8 e Windows Server 2012 ou versões posteriores
- Conecta-se a partir de uma máquina virtual no mesmo datacenter como a conta de armazenamento do Azure que é usada para o compartilhamento de arquivos do Azure

### <a name="cause-2-port-445-is-blocked"></a>Causa 2: A porta 445 está bloqueada

Pode ocorrer um erro de sistema 53 ou erro de sistema 67 se a comunicação de saída na porta 445 para o datacenter do Armazenamento de Arquivos do Azure estiver bloqueada. Para ver o resumo de ISPs que permitem ou proíbem o acesso a partir da porta 445, vá para [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para entender se esse é o motivo da mensagem "Erro de sistema 53", você pode usar o Portqry para consultar o ponto de extremidade TCP:445. Se o ponto de extremidade TCP:445 é exibido como filtrado, a porta TCP está bloqueada. Veja um exemplo de consulta:

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Se a porta TCP 445 está sendo bloqueado por uma regra ao longo do caminho de rede, você verá a seguinte saída:

  `TCP port 445 (microsoft-ds service): FILTERED`

Para saber mais sobre como usar Portqry, confira [Descrição do utilitário de linha de comando Portqry.exe](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Trabalhar com o seu departamento de TI para abrir a porta 445 com saída para [intervalos de IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>Causa 3: NTLMv1 está habilitado

O erro de sistema 53 ou erro de sistema 87 também pode ocorrer se a comunicação NTLMv1 está habilitada no cliente. O Armazenamento de Arquivos do Azure dá suporte apenas à autenticação NTLMv2. Ter NTLMv1 habilitado faz com que o cliente esteja menos seguro. Portanto, a comunicação será bloqueada para o Armazenamento de Arquivos do Azure. 

Para verificar se essa é a causa do erro, verifique se a seguinte subchave do registro está definida como um valor de 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Para saber mais, confira o tópico [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) no TechNet.

### <a name="solution-for-cause-3"></a>Solução para a causa 3

Reverter o valor **LmCompatibilityLevel** para o valor padrão de 3 na seguinte subchave do registro:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Erro 1816 "Não há cota suficiente disponível para processar este comando" quando você copia para um compartilhamento de arquivos do Azure

### <a name="cause"></a>Causa

O Erro 1816 ocorre quando você atingir o limite superior de identificadores abertos simultâneos permitidos para um arquivo no computador onde o compartilhamento de arquivos está sendo montado.

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos simultâneos fechando alguns identificadores e tentando novamente. Para obter mais informações, veja [Lista de verificação de escalabilidade e desempenho do Armazenamento do Microsoft Azure](storage-performance-checklist.md).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-file-storage-in-windows"></a>Cópia de arquivos mais lenta para e do Armazenamento de Arquivos do Azure no Windows

Você pode ver o desempenho lento ao tentar transferir arquivos para o Serviço de Arquivos do Azure.

- Se você não tem um requisito de tamanho de E/S mínimo específico, recomendamos que você use 1 MB de tamanho de E/S para um desempenho ideal.
-   Se você sabe o tamanho final de um arquivo que você está estendendo com gravações e o seu software não tem problemas de compatibilidade com o final ainda não escrito desse arquivo que contém zeros, defina o tamanho do arquivo antecipadamente em vez de realizar cada gravação como uma gravação de extensão.
-   Use o método de cópia correto:
    -   Use o [AzCopy](storage-use-azcopy.md#file-copy) para todas as transferências entre dois compartilhamentos de arquivo.
    -   Use o [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre um compartilhamento de arquivos e um computador local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerações para Windows 8.1 ou Windows Server 2012 R2

Para clientes que estão executando o Windows 8.1 ou o Windows Server 2012 R2, verifique se o hotfix [KB3114025](https://support.microsoft.com/help/3114025) está instalado. Esse hotfix melhora o desempenho dos identificadores de criação e fechamento.

Você pode executar o script abaixo para verificar se o hotfix foi instalado:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se o hotfix foi instalado, a seguinte saída será exibida:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> As imagens do Windows Server 2012 R2 no Azure Marketplace têm o hotfix KB3114025 instalado por padrão desde dezembro de 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Nenhuma pasta com uma letra de unidade em **Meu Computador**

Se você mapear um compartilhamento de arquivos do Azure como administrador por meio do uso de rede, o compartilhamento parece estar ausente.

### <a name="cause"></a>Causa

Por padrão, o Explorador de Arquivos do Windows não é executado como administrador. Se você executar net use em um prompt de comando de administrador, mapeará a unidade de rede como um administrador. Como as unidades mapeadas são focadas no usuário, a conta de usuário que está conectada não exibe as unidades se eles estão montadas em uma conta de usuário diferente.

### <a name="solution"></a>Solução
Monte o compartilhamento de uma linha de comando de não administrador. Como alternativa, você pode seguir [este tópico TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o valor do registro **EnableLinkedConnections**.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>O comando net use falha se a conta de armazenamento contém uma barra

### <a name="cause"></a>Causa

O comando net use interpreta uma barra (/) como uma opção de linha de comando. Se o nome da sua conta de usuário começa com uma barra, o mapeamento da unidade falhará.

### <a name="solution"></a>Solução

Você pode usar qualquer uma das seguintes etapas para contornar o problema:

- Execute o seguinte comando do PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  A partir de um arquivo em lotes, você pode executar o comando desta forma:

  `Echo new-smbMapping ... | powershell -command –`

- Colocando aspas duplas em torno da chave para contornar esse problema, a menos que a barra seja o primeiro caractere. Se for, use o modo interativo e digite sua senha separadamente ou regenere as chaves para obter uma chave que não comece com uma barra.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-file-storage-drive"></a>O aplicativo ou serviço não pode acessar a unidade montada dos Arquivos do Azure

### <a name="cause"></a>Causa

As unidades são montadas por usuário. Se o seu aplicativo ou serviço estiver em execução em uma conta de usuário diferente da que montou a unidade, o aplicativo não verá a unidade.

### <a name="solution"></a>Solução

Utilize uma das seguintes soluções:

-   Monte a unidade a partir da mesma conta de usuário que contém o aplicativo. Você pode usar uma ferramenta como o PsExec.
- Passe o nome da conta de armazenamento e a chave nos parâmetros de nome do usuário e senha do comando net use.

Depois de seguir estas instruções, você pode receber a seguinte mensagem de erro quando você executa o net use para a conta de serviço de rede/sistema: "Ocorreu um erro de sistema 1312. Uma sessão de logon especificada não existe. Talvez ela já tenha sido finalizada.” Se isso ocorrer, verifique se o nome de usuário que é passado para net use inclui informações de domínio (por exemplo: "[nome da conta de armazenamento].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Você está copiando um arquivo para um destino que não dá suporte à criptografia"

Quando um arquivo é copiado pela rede, o arquivo é descriptografado no computador de origem, transmitido em texto não criptografado e criptografado novamente no destino. No entanto, você pode ver o seguinte erro quando estiver tentando copiar um arquivo criptografado: "Você está copiando o arquivo para um destino que não dá suporte à criptografia".

### <a name="cause"></a>Causa
Esse problema pode ocorrer se você estiver usando o sistema de arquivos com criptografia (EFS). Os arquivos criptografados pelo BitLocker podem ser copiados para o Armazenamento de Arquivos do Azure. No entanto, o Armazenamento de Arquivos do Azure não dá suporte a EFS NTFS.

### <a name="workaround"></a>Solução alternativa
Para copiar um arquivo pela rede, você deve primeiro descriptografá-lo. Use um dos seguintes métodos:

- Use o comando **copy /d**. Ele permite que os arquivos criptografados sejam salvos como arquivos descriptografados no destino.
- Defina a seguinte chave do registro:
  - Caminho = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo de valor = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Value = 1

Observe que a definição da chave do registro afeta todas as operações de cópia que são feitas para compartilhamentos de rede.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

