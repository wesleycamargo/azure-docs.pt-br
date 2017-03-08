---
title: "Solução de problemas do Armazenamento de Arquivos do Azure | Microsoft Docs"
description: Solucionando problemas do Armazenamento de Arquivos do Azure
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: fbc5f600-131e-4b99-828a-42d0de85fff7
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 7aa2a60f2a02e0f9d837b5b1cecc03709f040898
ms.openlocfilehash: cce72f374e2cc6f1a42428d9f8e1f3ab8be50f7b
ms.lasthandoff: 02/28/2017


---
# <a name="troubleshooting-azure-file-storage-problems"></a>Solucionando problemas do Armazenamento de Arquivos do Azure
Este artigo lista os problemas comuns relacionados ao Armazenamento de Arquivos do Microsoft Azure quando você se conecta de clientes Windows e Linux. Ele também fornece possíveis causas e soluções para esses problemas.

**Problemas gerais (ocorrem em clientes Windows e Linux)**

* [Erro de cota ao tentar abrir um arquivo](#quotaerror)
* [Desempenho lento ao acessar o Armazenamento de Arquivos do Azure do Windows ou do Linux](#slowboth)
* [Como rastrear as operações de leitura e gravação no Armazenamento de Arquivos do Azure](#traceop)

**Problemas do cliente Windows**

* [Desempenho lento ao acessar o Armazenamento de Arquivos do Azure do Windows 8.1 ou do Server 2012 R2](#windowsslow)
* [Erro 53 ao tentar montar um compartilhamento de arquivos do Azure](#error53)
* [Erro 87 – parâmetro incorreto durante a tentativa de montar um compartilhamento de arquivos do Azure](#error87)
* [O net use foi bem-sucedido, mas não vejo o compartilhamento de arquivos do Azure montado no Windows Explorer](#netuse)
* [Minha conta de armazenamento contém "/" e o comando net use falha](#slashfails)
* [Meu aplicativo/serviço não pode acessar a unidade montada dos Arquivos do Azure.](#accessfiledrive)
* [Recomendações adicionais para otimizar o desempenho](#additional)

**Problemas do cliente Linux**

* [Erro "Você está copiando um arquivo para um destino que não dá suporte à criptografia" ao carregar/copiar arquivos para os Arquivos do Azure](#encryption)
* [Erro de E/S intermitente – erro de "Host inativo" nos compartilhamentos de arquivo existentes ou o shell trava ao executar comandos de lista no ponto de montagem](#errorhold)
* [Erro de montagem 115 ao tentar montar os Arquivos do Azure na VM Linux](#error15)
* [A VM Linux está sofrendo atrasos aleatórios em comandos do tipo "ls"](#delayproblem)
* [Erro 112 - erro de tempo limite](#error112)

**Acesso de outros aplicativos**

* [Pode referenciar o compartilhamento de arquivos do azure para meu aplicativo por meio de um trabalho Web?](#webjobs)

<a id="quotaerror"></a>

## <a name="quota-error-when-trying-to-open-a-file"></a>Erro de cota ao tentar abrir um arquivo
No Windows, você recebe mensagens de erro mais ou menos assim:

`1816 ERROR_NOT_ENOUGH_QUOTA <--> 0xc0000044`
`STATUS_QUOTA_EXCEEDED`
`Not enough quota is available to process this command`
`Invalid handle value GetLastError: 53`

No Linux, você recebe mensagens de erro mais ou menos assim:

`<filename> [permission denied]`
`Disk quota exceeded`

### <a name="cause"></a>Causa
O problema ocorre porque você atingiu o limite máximo de identificadores abertos simultâneos permitidos para um arquivo.

### <a name="solution"></a>Solução
Reduza o número de identificadores abertos simultâneos fechando alguns identificadores e tentando novamente. Para saber mais, confira [Lista de verificação de escalabilidade e desempenho do Armazenamento do Microsoft Azure](storage-performance-checklist.md).

<a id="slowboth"></a>

## <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Desempenho lento ao acessar o Armazenamento de Arquivos do Windows ou do Linux
* Se você não tem um requisito de tamanho de E/S mínimo específico, recomendamos que você use 1 MB de tamanho de E/S para um desempenho ideal.
* Se você sabe o tamanho final de um arquivo que está estendendo com gravação e o software não tem problemas de compatibilidade com o final ainda não escrito que contém zeros, defina o tamanho do arquivo antecipadamente em vez de ter cada gravação sendo uma gravação estendida.
* Use o método de cópia correto:
      * Use o AZCopy para todas as transferências entre dois compartilhamentos de arquivo. Para obter mais detalhes, veja [Transferir dados com o Utilitário da Linha de Comando AzCopy](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy#file-copy).
      * Use o Robocopy entre um compartilhamento de arquivos e um computador local. Veja [Robocopy com multi-thread para cópias mais rápidas](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) para obter mais detalhes.
<a id="windowsslow"></a>

## <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Desempenho lento ao acessar o Armazenamento de Arquivos do Windows 8.1 ou do Server 2012 R2
Para clientes que estão executando o Windows 8.1 ou o Windows Server 2012 R2, verifique se o hotfix [KB3114025](https://support.microsoft.com/kb/3114025) está instalado. Esse hotfix melhora o desempenho do identificador de criação e fechamento.

Você pode executar o script abaixo para verificar se o hotfix foi instalado:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se o hotfix foi instalado, a seguinte saída será exibida:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`
`{96c345ef-3cac-477b-8fcd-bea1a564241c}    REG_DWORD    0x1`

> [!NOTE]
> As imagens do Windows Server 2012 R2 no Azure Marketplace têm o hotfix KB3114025 instalado por padrão desde dezembro de 2015.
>
>

<a id="traceop"></a>

### <a name="how-to-trace-the-read-and-write-operations-in-azure-file-storage"></a>Como rastrear as operações de leitura e gravação no Armazenamento de Arquivos do Azure

O [Microsoft Message Analyzer](https://www.microsoft.com/en-us/download/details.aspx?id=44226) é capaz de mostrar a você a solicitação de um cliente em texto descriptografado e há uma relação considerável entre solicitações de conexão e transações (supondo o uso de SMB aqui e não de REST).  A desvantagem é que você precisa executar isso em cada cliente, o que será demorado se você tiver muitos trabalhos de VM IaaS.

Se você usar o Message Analyzer com ProcMon, você poderá obter uma boa ideia de qual código do aplicativo é responsável por transações.

<a id="additional"></a>

## <a name="additional-recommendations-to-optimize-performance"></a>Recomendações adicionais para otimizar o desempenho
Nunca crie ou abra um arquivo de E/S em cache que esteja solicitando acesso de gravação, mas não acesso de leitura. Ou seja, quando você chama **CreateFile**, nunca especifique somente **GENERIC_WRITE**, mas sempre **GENERIC_READ | GENERIC_WRITE**. Um identificador somente gravação não pode armazenar em cache pequenas gravações localmente, mesmo quando ele é o único identificador aberto para o arquivo. Isso impõe uma grande penalidade de desempenho em pequenas gravações. Observe que o modo "a" de CRT **fopen()** abre um identificador somente gravação.

<a id="error53"></a>

## <a name="error-53-or-error-67-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>“Erro 53” ou “Erro 67” ao tentar montar ou desmontar um Compartilhamento de Arquivos do Azure
Esse problema pode ser causado pelas seguintes condições:

### <a name="cause-1"></a>Causa 1
"Ocorreu um erro de sistema 53. Acesso negado." Por motivos de segurança, as conexões para compartilhamentos de arquivos do Azure são bloqueadas se o canal de comunicação não está criptografado e a tentativa de conexão não é feita do mesmo data center em que residem os compartilhamentos de arquivos do Azure. Não há criptografia de canal de comunicação quando o sistema operacional do cliente do usuário não dá suporte à criptografia SMB. Isso é indicado por uma mensagem de erro "Ocorreu um erro de sistema 53. Acesso negado" quando um usuário tenta montar um compartilhamento de arquivos local ou de um data center diferente. O Windows 8, o Windows Server 2012 e versões mais recentes de cada solicitação de negociação que inclua o SMB 3.0, que dá suporte à criptografia.

### <a name="solution-for-cause-1"></a>Solução para a Causa 1
Conectar-se de um cliente que atenda aos requisitos do Windows 8, do Windows Server 2012 ou de versões posteriores, ou que se conectem de uma máquina virtual que está no mesmo data center da conta do Armazenamento do Azure que é usada para o compartilhamento de arquivos do Azure.

### <a name="cause-2"></a>Causa 2
Pode ocorrer um “Erro do Sistema 53” ou “Erro do Sistema 67” ao montar um compartilhamento de arquivos do Azure se a comunicação de saída na porta 445 para o centro de dados dos Arquivos do Azure estiver bloqueada. Clique [aqui](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) para ver o resumo de provedores que permitem ou proíbem o acesso da porta 445.

Comcast e algumas organizações de TI bloqueiam essa porta. Para entender se esse é o motivo da mensagem "Erro de sistema 53", você pode usar o Portqry para consultar o ponto de extremidade TCP:445. Se o ponto de extremidade TCP:445 é exibido como filtrado, a porta TCP está bloqueada. Veja um exemplo de consulta:

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Se o TCP 445 está sendo bloqueado por uma regra ao longo do caminho de rede, você verá a seguinte saída:

**TCP port 445 (microsoft-ds service): FILTERED**

Para saber mais sobre como usar Portqry, confira [Descrição do utilitário de linha de comando Portqry.exe](https://support.microsoft.com/kb/310099).

### <a name="solution-for-cause-2"></a>Solução para a Causa 2
Trabalhar com sua organização de TI para abrir a porta 445 com saída para [intervalos de IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

<a id="error87"></a>
### <a name="cause-3"></a>Causa 3
O "Erro do sistema 53 ou Erro do sistema 87" também pode ser recebido se a comunicação NTLMv1 está habilitada no cliente. Ter NTLMv1 habilitado faz com que o cliente esteja menos seguro. Portanto, a comunicação será bloqueada para Arquivos do Azure. Para verificar se essa é a causa do erro, verifique se a seguinte subchave do registro está definida como um valor de 3:

HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel.

Para saber mais, confira o tópico [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) no TechNet.

### <a name="solution-for-cause-3"></a>Solução para a Causa 3
Para resolver esse problema, reverta o valor LmCompatibilityLevel na chave de registro HKLM\System\CurrentControlSet\Control\Lsa. para o valor padrão de 3.

Os Arquivos do Azure dão suporte apenas a autenticação NTLMv2. Verifique se a Política de Grupo foi aplicada aos clientes. Isso impedirá que esse erro ocorra. E também é considerado como uma prática recomendada de segurança. Para saber mais, confira [como configurar clientes para usar NTLMv2 usando Política De Grupo](https://technet.microsoft.com/library/jj852207\(v=ws.11\).aspx)

A configuração de política recomendada é **Enviar somente resposta NTLMv2**. Isso corresponde a um valor de registro de 3. Os clientes usam somente a autenticação NTLMv2 e usam segurança de sessão NTLMv2 se o servidor dá suporte a ele. Os controladores de domínio aceitam autenticação LM, NTLM e NTLMv2.

<a id="netuse"></a>

## <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>O net use foi bem-sucedido, mas não vejo o compartilhamento de arquivos do Azure montado no Windows Explorer
### <a name="cause"></a>Causa
Por padrão, o Windows Explorer não é executado como administrador. Se você executar **net use** em um prompt de comando de administrador, mapeará a unidade de rede "Como administrador”. Como as unidades mapeadas são focadas no usuário, a conta de usuário que está conectada não exibe as unidades se eles estão montadas em uma conta de usuário diferente.

### <a name="solution"></a>Solução
Monte o compartilhamento de uma linha de comando de não administrador. Como alternativa, você pode seguir [este tópico TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o valor do registro **EnableLinkedConnections**.

<a id="slashfails"></a>

## <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>Minha conta de armazenamento contém "/" e o comando net use falha
### <a name="cause"></a>Causa
Quando o comando **net use** é executado no prompt de comando (cmd.exe), ele é analisado adicionando "/" como uma opção de linha de comando. Isso faz com que o mapeamento de unidade falhe.

### <a name="solution"></a>Solução
Você pode usar qualquer uma das seguintes etapas para contornar o problema:

•   Use o seguinte comando do PowerShell:

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

De um arquivo em lotes, isso pode ser feito

`Echo new-smbMapping ... | powershell -command –`

• Colocando aspas duplas em torno da chave para contornar esse problema, a menos que "/" seja o primeiro caractere. Se for, use o modo interativo e digite sua senha separadamente ou regenere as chaves para obter uma chave que não comece com o caractere de barra invertida (/).

<a id="accessfiledrive"></a>

## <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>Meu aplicativo/serviço não pode acessar a unidade montada dos Arquivos do Azure
### <a name="cause"></a>Causa
As unidades são montadas por usuário. Se seu aplicativo ou serviço é executado em uma conta de usuário diferente, os usuários não veem a unidade.

### <a name="solution"></a>Solução
Monte a unidade da mesma conta de usuário na qual o aplicativo está. Isso pode ser feito usando ferramentas como psexec.

Outra forma é criar um novo usuário que tem os mesmos privilégios da conta de serviço ou sistema de rede e executar **cmdkey** e **net use** nessa conta. O nome de usuário deve ser o nome da conta de armazenamento e a senha deve conter a chave da conta de armazenamento. Outra opção para **net use** é passar o nome da conta de armazenamento e a chave nos parâmetros de nome e senha de usuário do comando **net use**.

Depois de seguir essas instruções, você pode receber a seguinte mensagem de erro: "Ocorreu um erro de sistema 1312. Uma sessão de logon especificada não existe. Ela pode já ter sido finalizada"ao executar **net use** para a conta de serviço de rede/sistema. Se isso ocorrer, verifique se o nome de usuário que é passado para **net use** inclui informações de domínio (por exemplo: "[nome de conta de armazenamento].file.core.windows.net").

<a id="encryption"></a>

## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Você está copiando um arquivo para um destino que não dá suporte à criptografia"
### <a name="cause"></a>Causa
Os arquivos criptografados pelo BitLocker podem ser copiados para os Arquivos do Azure. No entanto, o armazenamento de arquivos não dá suporte a EFS NTFS. Portanto, você provavelmente está usando o EFS nesse caso. Se você tem arquivos criptografados pelo EFS, uma operação de cópia para o Armazenamento de Arquivos pode falhar se o comando de cópia não descriptografa um arquivo copiado.

### <a name="workaround"></a>Solução alternativa
Para copiar um arquivo para o Armazenamento de Arquivos, você deve primeiro descriptografá-lo. Você pode fazer isso usando um dos seguintes métodos:

•   Use **copy /d**.

•   Defina a seguinte chave do registro:

* Path=HKLM\Software\Policies\Microsoft\Windows\System
* Value type=DWORD
* Name = CopyFileAllowDecryptedRemoteDestination
* Value = 1

No entanto, observe que a definição da chave do registro afeta todas as operações de cópia para compartilhamentos de rede.

<a id="errorhold"></a>

## <a name="host-is-down-error-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>Erro "Host inativo" nos compartilhamentos de arquivo existentes ou o shell trava ao executar comandos de lista no ponto de montagem
### <a name="cause"></a>Causa
Esse erro ocorre no cliente Linux quando o cliente ficou ocioso por um longo período de tempo. Quando esse erro ocorre, o cliente se desconecta e a conexão de cliente atinge o tempo limite.

### <a name="solution"></a>Solução
Esse problema agora foi corrigido no kernel do Linux como parte do [change set](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93) e falta apenas a backport na distribuição do Linux.

Para solucionar esse problema, mantenha a conexão e evite entrar em um estado ocioso; mantenha também um arquivo no compartilhamento de arquivos do Azure em que você grava periodicamente. Isso deve ser uma operação de gravação, como reescrever a data da modificação/criação no arquivo. Caso contrário, você poderá obter resultados em cache e a operação poderá não disparar a conexão.

<a id="error15"></a>

## <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>Erro de montagem 115 ao tentar montar os Arquivos do Azure na VM Linux
### <a name="cause"></a>Causa
As distribuições do Linux ainda não dão suporte ao recurso de criptografia no SMB 3.0. Em algumas distribuições, o usuário poderá receber uma mensagem de erro "115" se eles tentarem montar Arquivos do Azure usando SMB 3.0 devido a um recurso ausente.

### <a name="solution"></a>Solução
Se o cliente Linux SMB usado não dá suporte à criptografia, monte os Arquivos do Azure usando SMB 2.1 de uma VM Linux no mesmo data center da conta do Armazenamento de Arquivos.

<a id="delayproblem"></a>

## <a name="linux-vm-experiencing-random-delays-in-commands-like-ls"></a>A VM Linux está sofrendo atrasos aleatórios em comandos do tipo "ls"
### <a name="cause"></a>Causa
Isso pode ocorrer quando o comando mount não inclui a opção **serverino**. Sem **serverino**, o comando ls executa um **stat** em cada arquivo.

### <a name="solution"></a>Solução
Verifique o **serverino** na sua entrada "/etc/fstab":

`//azureuser.file.core.windows.net/cifs        /cifs   cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Você também pode verificar se essa opção está sendo usada, apenas executando o comando **sudo mount | grep cifs** e verificando sua saída:

`//mabiccacifs.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=3.0,sec=ntlmssp,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777,dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Se a opção **serverino** não está lá, desmonte e monte os Arquivos do Azure novamente fazendo com que a opção **serverino** esteja selecionada.+

Outro motivo para o desempenho lento poderia ser o caching desabilitado. Para verificar se o caching está habilitado, procure "cache=".  *cache=none* indica que o caching está desabilitado. Remonte o compartilhamento com o comando de montagem padrão ou adicionado explicitamente a opção **cache=strict** ao comando de montagem para garantir que o modo de caching "strict" ou caching padrão seja habilitado.

<a id="error112"></a>
## <a name="error-112---timeout-error"></a>Erro 112 - erro de tempo limite

Esse erro indica que as falhas de comunicação que impedem a restabelecer uma conexão TCP com o servidor quando a opção de montagem "soft" é usado, que é o padrão.

### <a name="cause"></a>Causa

Esse erro pode ser causado por uma Linux reconectar-se o problema ou outros problemas que impedem a reconexão, como erros de rede. Especificando uma montagem de disco rígida será forçar o cliente a esperar até que uma conexão é estabelecida ou interrompida explicitamente e pode ser usada para evitar erros devido a tempos limite de rede. No entanto, os usuários devem estar cientes de que isso pode levar a esperas indefinidas e deve tratar a interrupção de uma conexão conforme necessário.


### <a name="workaround"></a>Solução alternativa

Foi corrigido o problema de Linux, porém não compilado para distribuições do Linux ainda. Se o problema é causado pelo problema reconectar no Linux, isso pode ser contornado, evitando a entrar em um estado ocioso. Para conseguir isso, mantenha um arquivo no compartilhamento de arquivos do Azure para o qual você escreve a cada 30 segundos ou menos. Isso deve ser uma operação de gravação, como reescrever a data da modificação/criação no arquivo. Caso contrário, você poderá obter resultados em cache e a operação poderá não disparar a conexão. Esta é a lista de kernels populares do Linux que têm essa e outras correções de reconexão: 4.4.40+ 4.8.16+ 4.9.1+

<a id="webjobs"></a>

## <a name="accessing-from-other-applications"></a>Acesso de outros aplicativos
### <a name="can-i-reference-the-azure-file-share-for-my-application-through-a-webjob"></a>Pode referenciar o compartilhamento de arquivos do azure para meu aplicativo por meio de um trabalho Web?
Não é possível montar compartilhamentos SMB em área restrita do serviço de aplicativo. Como alternativa, você pode mapear o compartilhamento de arquivos do Azure como uma unidade mapeada e permitir que o aplicativo para acessá-lo como uma letra de unidade.
## <a name="learn-more"></a>Saiba mais
* [Introdução ao Armazenamento de Arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md)
* [Introdução ao Armazenamento de Arquivos do Azure no Linux](storage-how-to-use-files-linux.md)

