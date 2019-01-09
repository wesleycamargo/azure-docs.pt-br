---
title: Copiar ou mover dados para o Armazenamento do Azure com o AzCopy v10 (Versão Prévia) | Microsoft Docs
description: Use o utilitário AzCopy v10 (versão prévia) para mover ou copiar dados para ou de blob, data lake e conteúdo de arquivo. Copie dados para o Armazenamento do Azure de arquivos locais ou copie dados dentro na mesma conta ou entre contas de armazenamento. Migre facilmente seus dados para o Armazenamento do Azure.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.component: common
ms.openlocfilehash: af45081df280f5542b5ba70892ee74c05b3e99cc
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808104"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>Transferir dados com o AzCopy v10 (Versão Prévia)

AzCopy v10 (Versão Prévia) é o utilitário de linha de comando de última geração para copiar dados de e para o Armazenamento de Arquivos e de Blobs do Microsoft Azure, o que oferece uma interface de linha de comando reprojetada e uma nova arquitetura para transferências de dados de alto desempenho confiáveis. Ao usar AzCopy, você pode copiar dados entre um sistema de arquivos e uma conta de armazenamento, ou entre contas de armazenamento.

## <a name="whats-new-in-azcopy-v10"></a>O que há de novo no AzCopy v10

- Sincronize um sistema de arquivos para Blobs do Azure ou vice-versa. Usar o `azcopy sync <source> <destination>`. Ideal para cenários de cópia incremental.
- Dá suporte a APIs do Azure Data Lake Storage Gen2. Use `myaccount.dfs.core.windows.net` como um URI para chamar as APIs de ADLS Gen2.
- Dá suporte à cópia de uma conta inteira (apenas serviço Blob) para outra conta.
- Cópia de conta para conta agora está usando a nova API [Colocar da URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url). Nenhuma transferência de dados para o cliente é necessária, o que torna a transferência mais rápida.
- Liste/remova arquivos e blobs em um determinado caminho.
- Dá suporte a padrões curingas em um caminho, bem como a sinalizadores --include e --exclude.
- Melhor resiliência: cada instância de AzCopy criará um pedido de trabalho e um arquivo de log relacionado. Você pode exibir e, em seguida, retomar trabalhos anteriores e retomar trabalhos com falha. O AzCopy também tentará automaticamente realizar uma nova transferência após uma falha.
- Aprimoramentos de desempenho gerais.

## <a name="download-and-install-azcopy"></a>Baixar e instalar o AzCopy

### <a name="latest-preview-version-v10"></a>Versão prévia mais recente (v10)

Baixe a versão prévia mais recente do AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>Versão de produção mais recente (v8.1)

Baixe a [versão de produção mais recente do AzCopy para Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>AzCopy com suporte para o serviço de armazenamento de Tabela (v7.3)

Baixe o [AzCopy v7.3 que dá à cópia de dados para/do serviço de armazenamento de tabelas do Microsoft Azure](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Etapas Pós-instalação

AzCopy v10 não exige uma instalação. Abra um aplicativo de linha de comando preferencial e navegue para a pasta em que o executável `azcopy.exe` está localizado. Se quiser, poderá adicionar o local da pasta do AzCopy ao caminho do sistema.

## <a name="authentication-options"></a>Opções de Autenticação

O AzCopy v10 permite que você use as seguintes opções ao autenticar-se com o Armazenamento do Azure:
- **Azure Active Directory [Com suporte no Blob e ADLS Gen2]**. Use ```.\azcopy login``` para entrar usando o Azure Active Directory.  O usuário deve ter a [função "Colaborador de Dados de Blob de Armazenamento" atribuída](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) para gravar no Armazenamento de Blobs usando a autenticação do Azure Active Directory.
- **Tokens SAS [Com suporte no serviço Blob e Arquivo]**. Anexe o token SAS ao caminho do blob na linha de comando para usá-lo. Você pode gerar um token SAS usando o Portal do Azure, o [Gerenciador de Armazenamento](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), o [PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-AzStorageblobsastoken) ou outras ferramentas de sua escolha. Para obter mais informações, confira [exemplos](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Introdução

O AzCopy v10 tem uma sintaxe simples autodocumentada. Quando você faz logon no Azure Active Directory, a sintaxe geral é a seguinte:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you are using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

É assim que você pode obter uma lista dos comandos disponíveis:

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

Para ver a página de ajuda e exemplos para um comando específico, execute o comando a seguir:

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Criar um contêiner de Blob ou compartilhamento de Arquivos 

**Criar um contêiner de Blob**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Criar um compartilhamento de Arquivos**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Criar um contêiner de Blob usando ADLS Gen2**

Se você habilitou namespaces hierárquicos na conta de armazenamento de Blobs, poderá usar o seguinte comando para criar um novo sistema de arquivos (contêiner de Blob) para poder carregar arquivos nele.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Copiar dados para o Armazenamento do Azure

Use o comando de cópia para transferir dados da origem para o destino. A origem/destino pode ser:
- Sistema de arquivos local
- URI de Contêiner/Diretório Virtual/Blob do Azure
- URI de Compartilhamento de Arquivo/Diretório/Arquivo do Azure
- Sistema de Arquivos do Azure Data Lake Storage Gen2/Diretório/URI de Arquivo

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

O comando a seguir carrega todos os arquivos da pasta `C:\local\path` recursivamente no contêiner `mycontainer1` criando o diretório `path` no contêiner:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

O comando a seguir carrega todos os arquivos da pasta `C:\local\path` (sem fazer recursão nos subdiretórios) no contêiner `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Para obter mais exemplos, use o seguinte comando:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Copiar dados entre duas contas de armazenamento

Copiar dados entre duas contas de armazenamento usa a API [Colocar Bloco da URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) e não utiliza a largura de banda de rede do computador cliente. Os dados são copiados entre dois servidores de Armazenamento do Azure diretamente enquanto o AzCopy simplesmente orquestra a operação de cópia. Atualmente, essa opção está disponível somente para armazenamento de Blobs.

Para copiar os dados entre duas contas de armazenamento, use o seguinte comando:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> O comando enumerará todos os contêineres de blob e os copiará para a conta de destino. Neste momento, o AzCopy v10 dá suporte para copiar apenas blobs de bloco entre duas contas de armazenamento. Todos os outros objetos de conta de armazenamento (blobs de acrescentar, blobs de páginas, arquivos, tabelas e filas) serão ignorados.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Copiar uma imagem de VHD para uma conta de armazenamento

O AzCopy v10 por padrão carrega dados em blobs de blocos. No entanto, se um arquivo de origem tiver a extensão vhd, o AzCopy v10 por padrão o carregará em um blob de páginas. Atualmente, esse comportamento não é configurável.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Sincronização: cópia e exclusão incremental (somente armazenamento de Blobs)

> [!NOTE]
> O comando de sincronização sincronizará o conteúdo de origem com o de destino, incluindo a EXCLUSÃO de arquivos de destino se eles não existirem na origem. Use o destino que você deseja sincronizar.

Para sincronizar seu sistema de arquivos local com uma conta de armazenamento, use o seguinte comando:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Da mesma forma, você pode sincronizar um contêiner de Blobs para um sistema de arquivos local:

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

O comando permite que você sincronize incrementalmente a origem com o destino com base em carimbos de data/hora da última modificação. Se você adicionar ou excluir um arquivo na origem, o AzCopy v10 fará o mesmo no destino. Antes da exclusão, o AzCopy solicitará a confirmação da exclusão dos arquivos.

## <a name="advanced-configuration"></a>Configuração avançada

### <a name="configure-proxy-settings"></a>Definir configurações de proxy

Para definir as configurações de proxy para AzCopy v10, defina a variável de ambiente https_proxy usando o seguinte comando:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Otimizar a taxa de transferência

Defina a variável de ambiente AZCOPY_CONCURRENCY_VALUE para configurar o número de solicitações simultâneas e controlar o desempenho de taxa de transferência e o consumo de recursos. O valor é definido como 300 por padrão. Reduzir o valor limitará a largura de banda e a CPU usadas pelo AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
```

## <a name="troubleshooting"></a>solução de problemas

O AzCopy v10 cria arquivos de log e arquivos de plano para todos os trabalhos. Você pode usar os logs para investigar e solucionar problemas potenciais. Os logs conterão o status de falha (UPLOADFAILED, COPYFAILED e DOWNLOADFAILED), o caminho completo e o motivo da falha. Os logs de trabalho e os arquivos de plano estão localizados na pasta %USERPROFILE\\.azcopy.

### <a name="review-the-logs-for-errors"></a>Examinar os logs em busca de erros

O comando a seguir obtém todos os erros com o status UPLOADFAILED do log 04dc9ca9-158f-7945-5933-564021086c79:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

### <a name="view-and-resume-jobs"></a>Exibir e retomar trabalhos

Cada operação de transferência criará um trabalho do AzCopy. Você pode exibir o histórico de trabalhos usando o comando a seguir:

```azcopy
.\azcopy jobs list
```

Para exibir as estatísticas de trabalho, use o seguinte comando:

```azcopy
.\azcopy jobs show <job-id>
```

Para filtrar as transferências por status, use o seguinte comando:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Você pode retomar um trabalho com falha/cancelado usando seu identificador juntamente com o token SAS (não é persistente por motivos de segurança):

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

### <a name="change-the-default-log-level"></a>Alterar o nível de log padrão

Por padrão, o nível de log do AzCopy está definido como INFO. Se você quiser reduzir o detalhamento do log para economizar espaço em disco, substitua a configuração usando a opção ``--log-level``. Os níveis de log disponíveis são: DEBUG, INFO, WARNING, ERROR, PANIC e FATAL

## <a name="next-steps"></a>Próximas etapas

Seus comentários sempre são bem-vindos. Se você tiver dúvidas, problemas ou comentários gerais, envie-os em https://github.com/Azure/azure-storage-azcopy. Obrigado!