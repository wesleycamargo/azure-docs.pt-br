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
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 0635120c4e16f3b8531039eee4c6651e7cdeca40
ms.lasthandoff: 04/22/2017


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
* [O net use foi bem-sucedido, mas não vejo o compartilhamento de arquivos do Azure montado nem a letra da unidade na interface do usuário do Windows Explorer](#netuse)
* [Minha conta de armazenamento contém "/" e o comando net use falha](#slashfails)
* [Meu aplicativo/serviço não pode acessar a unidade montada dos Arquivos do Azure.](#accessfiledrive)
* [Recomendações adicionais para otimizar o desempenho](#additional)
* [Erro "Você está copiando um arquivo para um destino que não dá suporte à criptografia" ao carregar/copiar arquivos para os Arquivos do Azure](#encryption)

**Problemas do cliente Linux**

* [Erro de E/S intermitente – erro de "Host inativo (Erro 112)" nos compartilhamentos de arquivo existentes ou o shell trava ao executar comandos de lista no ponto de montagem](#error112)
* [Erro de montagem 115 ao tentar montar os Arquivos do Azure na VM Linux](#error15)
* [Compartilhamento de arquivos do Azure montado na VM Linux apresentando um desempenho lento](#delayproblem)
* [Erro de montagem (11): Recurso temporariamente indisponível durante a montagem no kernel do Ubuntu 4.8+](#ubuntumounterror)

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
* Se você sabe o tamanho final de um arquivo que você está estendendo com gravações e o seu software não tem problemas de compatibilidade com o final ainda não escrito desse arquivo que contém zeros, defina o tamanho do arquivo antecipadamente em vez de realizar cada gravação como uma gravação de extensão.
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

O [Microsoft Message Analyzer](https://www.microsoft.com/en-us/download/details.aspx?id=44226) é capaz de mostrar a você a solicitação de um cliente em texto descriptografado e há uma relação considerável entre solicitações de transferência e transações (supondo aqui o uso de SMB e não de REST).  A desvantagem é que você precisa executar isso em cada cliente, o que será demorado se você tiver muitos trabalhos de VM IaaS.

Se você usar o Message Analyzer com ProcMon, você poderá obter uma boa ideia de qual código do aplicativo é responsável por transações.

<a id="additional"></a>

## <a name="additional-recommendations-to-optimize-performance"></a>Recomendações adicionais para otimizar o desempenho
Nunca crie ou abra um arquivo de E/S em cache que esteja solicitando acesso de gravação, mas não acesso de leitura. Ou seja, quando você chama **CreateFile**, nunca especifique somente **GENERIC_WRITE**, mas sempre **GENERIC_READ | GENERIC_WRITE**. Um identificador somente gravação não pode armazenar em cache pequenas gravações localmente, mesmo quando ele é o único identificador aberto para o arquivo. Isso impõe uma grande penalidade de desempenho em pequenas gravações. Observe que o modo "a" de CRT **fopen()** abre um identificador somente gravação.

<a id="error53"></a>

## <a name="error-53-or-error-67-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>“Erro 53” ou “Erro 67” ao tentar montar ou desmontar um Compartilhamento de Arquivos do Azure
Esse problema pode ser causado pelas seguintes condições:

### <a name="cause-1"></a>Causa 1
"Ocorreu um erro de sistema 53. Acesso negado." Por motivos de segurança, as conexões para compartilhamentos de arquivos do Azure são bloqueadas se o canal de comunicação não está criptografado e a tentativa de conexão não é feita da mesma região do Azure em que residem os compartilhamentos de arquivos do Azure. Não há criptografia de canal de comunicação quando o sistema operacional do cliente do usuário não dá suporte à criptografia SMB. Isso é indicado por uma mensagem de erro "Ocorreu um erro de sistema 53. Acesso negado" quando um usuário tenta montar um compartilhamento de arquivos local ou de um data center diferente. O Windows 8, o Windows Server 2012 e versões mais recentes de cada solicitação de negociação que inclua o SMB 3.0, que dá suporte à criptografia.

### <a name="solution-for-cause-1"></a>Solução para a Causa 1
Conectar-se de um cliente que atenda aos requisitos do Windows 8, do Windows Server 2012 ou de versões posteriores ou que se conectem de uma máquina virtual que está na mesma região do Azure que a conta de Armazenamento do Azure que é usada para o compartilhamento de arquivos do Azure.

### <a name="cause-2"></a>Causa 2
Pode ocorrer um “Erro do Sistema 53” ou “Erro do Sistema 67” ao montar um compartilhamento de arquivos do Azure se a comunicação de saída na porta 445 para a região do Azure dos Arquivos do Azure estiver bloqueada. Clique [aqui](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) para ver o resumo de provedores que permitem ou proíbem o acesso da porta 445.

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

Outra opção para **net use** é passar o nome da conta de armazenamento e a chave nos parâmetros de nome e senha de usuário do comando **net use**.

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

<a id="error112"></a>

## <a name="host-is-down-error-112-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>"Host inativo (Erro 112)" nos compartilhamentos de arquivo existentes ou o shell trava ao executar comandos de lista no ponto de montagem
### <a name="cause"></a>Causa
Esse erro ocorre no cliente Linux quando o cliente ficou ocioso por um longo período de tempo. Quando o cliente está ocioso por um período longo, ele se desconecta e a conexão atinge o tempo limite. 

A conexão pode ficar ociosa por vários motivos. Um motivo são as falhas de comunicação de rede que impedem o restabelecimento de uma conexão TCP com o servidor quando a opção de montagem "soft" é usada, o que é o padrão.

Outro motivo pode ser que também há algumas correções de reconexão que não estão presentes nos kernels anteriores.

### <a name="solution"></a>Solução

Especificando uma montagem de disco rígida será forçar o cliente a esperar até que uma conexão é estabelecida ou interrompida explicitamente e pode ser usada para evitar erros devido a tempos limite de rede. No entanto, os usuários devem estar cientes de que isso pode levar a esperas indefinidas e deve tratar a interrupção de uma conexão conforme necessário.

Esse problema de reconexão no kernel Linux agora é fixo como parte dos próximos conjuntos de alterações

* [Corrigir a reconexão para não adiar a sessão smb3 por muito tempo após a reconexão do soquete](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)

* [Chamar o serviço de eco imediatamente após a reconexão do soquete](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)

* [CIFS: corrigir uma possível corrupção de memória durante a reconexão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)

* [CIFS: Corrigir um possível bloqueio duplo de mutex durante a reconexão – para kernels v4.9 e superior](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183) 

No entanto, essa alteração pode ainda não ser transportada para todas as distribuições de Linux. Esta é a lista de kernels populares do Linux conhecidos que têm essa e outras correções de reconexão: 4.4.40+ 4.8.16+ 4.9.1+.
Você pode passar para as versões de kernel recomendadas acima para obter a correção mais recente.

### <a name="workaround"></a>Solução alternativa
Se você não pode passar para versões do kernel mais recentes, é possível solucionar esse problema mantendo um arquivo no compartilhamento de arquivos do Azure no qual você grava a cada 30 segundos ou menos. Isso deve ser uma operação de gravação, como reescrever a data da modificação/criação no arquivo. Caso contrário, você poderá obter resultados em cache e a operação poderá não disparar a reconexão. 


<a id="error15"></a>

## <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>Erro de montagem 115 ao tentar montar os Arquivos do Azure na VM Linux
### <a name="cause"></a>Causa
As distribuições do Linux ainda não dão suporte ao recurso de criptografia no SMB 3.0. Em algumas distribuições, o usuário poderá receber uma mensagem de erro "115" se eles tentarem montar Arquivos do Azure usando SMB 3.0 devido a um recurso ausente.

### <a name="solution"></a>Solução
Se o cliente Linux SMB usado não dá suporte à criptografia, monte os Arquivos do Azure usando SMB 2.1 de uma VM Linux na mesma região do Azure da conta de armazenamento de arquivos.

<a id="delayproblem"></a>

## <a name="azure-file-share-mounted-on-linux-vm-experiencing-slow-performance"></a>Compartilhamento de arquivos do Azure montado na VM Linux apresentando um desempenho lento

Um possível motivo do desempenho lento poderia ser o cache desabilitado. Para verificar se o caching está habilitado, procure "cache=".  *cache=none* indica que o caching está desabilitado. Remonte o compartilhamento com o comando de montagem padrão ou adicionado explicitamente a opção **cache=strict** ao comando de montagem para garantir que o modo de caching "strict" ou caching padrão seja habilitado.

Em alguns cenários, a opção de montagem serverino pode fazer com que o comando ls execute stat em todas as entradas de diretório e esse comportamento resulta na degradação de desempenho ao listar um diretório grande. Você pode verificar as opções de montagem na sua entrada de "/etc/fstab":

`//<storage-account-name>.file.core.windows.net/<file-share-name> <mount-point> cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Você também pode verificar as opções corretas estão sendo usadas, apenas executando o comando `sudo mount | grep cifs` (exemplo de saída abaixo).

`//<storage-account-name>.file.core.windows.net/<file-share-name> on <mount-point> type cifs
(rw,relatime,vers=3.0,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,
noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777,
dir_mode=0777,persistenthandles,nounix,serverino,
mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

.Se as opções o cache=strict ou serverino não estão presentes, desmonte e monte arquivos do Azure novamente executando o comando mount da [documentação](https://docs.microsoft.com/en-us/azure/storage/storage-how-to-use-files-linux#mount-the-file-share) e verifique novamente se a entrada "/etc/fstab" tem as opções corretas.

<a id="ubuntumounterror"></a>
## <a name="mount-error11-resource-temporarily-unavailable-when-mounting-to-ubuntu-48-kernel"></a>Erro de montagem (11): Recurso temporariamente indisponível durante a montagem no kernel do Ubuntu 4.8+

### <a name="cause"></a>Causa
Problema conhecido no kernel do Ubuntu 16.10 (v.4.8) em que o cliente declara dar suporte à criptografia, mas isso não ocorre. 

### <a name="solution"></a>Solução
Até a correção do Ubuntu 16.10, especifique a opção de montagem "vers=2.1" ou use o Ubuntu 16.04.
## <a name="learn-more"></a>Saiba mais
* [Introdução ao Armazenamento de Arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md)
* [Introdução ao Armazenamento de Arquivos do Azure no Linux](storage-how-to-use-files-linux.md)

