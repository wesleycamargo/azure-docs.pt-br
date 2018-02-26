---
title: "Notas de versão de agente de Sincronização de Arquivos do Azure | Microsoft Docs"
description: "Notas de versão da Sincronização de Arquivos do Azure"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: tamram
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 9b6dfec6465482efcbf55d0441e44a0278f44a22
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-file-sync-agent-release-notes"></a>Notas de versão do agente da Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure (versão prévia) permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter todos os caches de que precisar ao redor do mundo.

Este artigo aborda as notas de versão para versões com suporte do agente de Sincronização de Arquivos do Azure.

## <a name="supported-versions"></a>Versões com suporte
As seguintes versões têm o suporte da Sincronização de Arquivos do Azure:

| Número de versão do agente | Data do lançamento | Com suporte até |
|----------------------|--------------|------------------|
| 2.0.11.0 | 2018-02-08 | Versão atual |
| 1.1.0.0 | 2017-09-26 | 2018-07-30 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-20110"></a>Versão 2.0.11.0 do agente
As notas de versão a seguir são para a versão 2.0.11.0 do agente que foi lançado em 9 de fevereiro de 2018. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com um servidor do Windows, veja [Planejamento de uma implantação de Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente (MSI) deve ser instalado com permissões de privilégios elevados (admin).
- Sem suporte nas opções de implantação do Windows Server Core ou Nano Server.
- Tem suporte apenas no Windows Server 2016 e 2012 R2.
- O agente requer pelo menos 2 GB de memória física.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados a menos que eles respeitem o atributo offline e ignorem a leitura do conteúdo desses arquivos. Para saber mais, veja [Solução de problemas de Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-troubleshoot.md)
- Novo nessa atualização, há suporte para DFS-R.  Consulte o [guia de planejamento](storage-sync-files-planning.md#distributed-file-system-dfs) para obter mais informações.
- Não use triagens de arquivo do Gerenciador de Recursos de Servidor de Arquivos (ou outros): as triagens de arquivo podem causar infinitas falhas de sincronização caso os arquivos sejam bloqueados por causa da triagem de arquivo.
- A Eliminação de Duplicação de um Servidor Registrado (incluindo a clonagem de máquina virtual) pode levar a resultados inesperados (em particular, a sincronização pode nunca convergir).
- A Eliminação de Duplicação de Dados e as camadas de nuvem não têm suporte no mesmo volume.
 
### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continuará a operar normalmente:
- Caminhos com mais de 2048 caracteres
- A parte DACL de um descritor de segurança, se maior do que 2K (isso só será um problema se você tiver mais do que cerca de 40 Entradas de Controle de Acesso em um único item)
- A parte SACL do descritor de segurança (usado para auditoria)
- Atributos estendidos
- Fluxos de dados alternativos
- Pontos de nova análise
- Links físicos
- A compactação (se definida em um arquivo do servidor) não será preservada quando as alterações forem sincronizadas para o arquivo de outros pontos de extremidade
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem nosso serviço de ler os dados 
    
    > [!Note]  
    > A Sincronização de Arquivos do Azure sempre criptografa os dados em trânsito e os dados podem ser criptografados em repouso no Azure.
 
### <a name="server-endpoints"></a>Pontos de Extremidade do Servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte da Sincronização de Arquivos do Azure no momento.
- Um ponto de extremidade de servidor pode não estar no volume do sistema (por exemplo, C:\MinhaPasta não será um caminho aceitável, a menos que C:\MinhaPasta seja um ponto de montagem).
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados do Cluster).
- Um ponto de extremidade do servidor pode não estar aninhado, mas pode coexistir no mesmo volume em paralelo entre si.
- A exclusão de um grande número de diretórios de um servidor de uma só vez (mais de 10.000) pode causar falhas de sincronização - exclua diretórios em lotes de menos de 10.000 e verifique se as operações de exclusão são sincronizadas com êxito antes de excluir o próximo lote.
- Com esta versão, o suporte adicionado para a raiz de sincronização na raiz de um volume.
- Não armazene um sistema operacional ou o arquivo de paginação do aplicativo dentro de um ponto de extremidade do servidor.
- Mudou com esta versão, adicionados novos eventos para controlar o tempo de execução total para disposição em camadas de nuvem (EventID 9016), sincronizar o andamento do upload (EventID 9302) e arquivos que não foram sincronizados (EventID 9900).
- Mudou com esta versão, o desempenho de sincronização do namespace de recuperação de desastres rápida aumentou significativamente.
 
### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Alterado da versão anterior, novos arquivos serão disponibilizados em camadas em 1 hora, sujeitos à configuração da política de camadas (anteriormente, 32 horas) – fornecemos um cmdlet do PowerShell para camada sob demanda para que você possa avaliar a disponibilização em camadas com mais eficiência e sem esperar pelo processo em segundo plano.
- Se um arquivo em camadas for copiado usando o Robocopy para outro local, o arquivo resultante não ficará em camadas, mas o atributo offline poderá ser definido, já que Robocopy incorretamente inclui esse atributo em operações de cópia.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.
- Alterado da versão anterior, os arquivos agora são baixados como arquivos em camadas em outros servidores, desde que o arquivo seja novo ou já fosse um arquivo em camadas.

## <a name="agent-version-1100"></a>Versão 1.1.0.0 do agente
As notas de versão a seguir são para a versão do agente 1.1.0.0 que foi lançado em 9 de setembro de 2017. Esta versão marca a versão prévia inicial da Sincronização de Arquivos do Azure!

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com um servidor do Windows, veja [Planejamento de uma implantação de Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente (MSI) deve ser instalado com permissões de privilégios elevados (admin).
- Sem suporte nas opções de implantação do Windows Server Core ou Nano Server.
- Tem suporte apenas no Windows Server 2016 e 2012 R2.
- O agente requer pelo menos 2 GB de memória física.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados a menos que eles respeitem o atributo offline e ignorem a leitura do conteúdo desses arquivos. Para saber mais, veja [Solução de problemas de Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-troubleshoot.md)
- Não use triagens de arquivo do Gerenciador de Recursos de Servidor de Arquivos (ou outros): as triagens de arquivo podem causar infinitas falhas de sincronização caso os arquivos sejam bloqueados por causa da triagem de arquivo.
- A Eliminação de Duplicação de um Servidor Registrado (incluindo a clonagem de máquina virtual) pode levar a resultados inesperados (em particular, a sincronização pode nunca convergir).
- A Eliminação de Duplicação de Dados e as camadas de nuvem não têm suporte no mesmo volume.
 
### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continuará a operar normalmente:
- Caminhos com mais de 2048 caracteres
- A parte DACL de um descritor de segurança, se maior do que 2K (isso só será um problema se você tiver mais do que cerca de 40 Entradas de Controle de Acesso em um único item)
- A parte SACL do descritor de segurança (usado para auditoria)
- Atributos estendidos
- Fluxos de dados alternativos
- Pontos de nova análise
- Links físicos
- A compactação (se definida em um arquivo do servidor) não será preservada quando as alterações forem sincronizadas para o arquivo de outros pontos de extremidade
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem nosso serviço de ler os dados 
    
    > [!Note]  
    > A Sincronização de Arquivos do Azure sempre criptografa os dados em trânsito e os dados podem ser criptografados em repouso no Azure.
 
### <a name="server-endpoints"></a>Pontos de Extremidade do Servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte da Sincronização de Arquivos do Azure no momento.
- Um ponto de extremidade de servidor pode não estar no volume do sistema (por exemplo, C:\MinhaPasta não será um caminho aceitável, a menos que C:\MinhaPasta seja um ponto de montagem).
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados do Cluster).
- Um ponto de extremidade do servidor pode não estar aninhado, mas pode coexistir no mesmo volume em paralelo entre si.
- A exclusão de um grande número de diretórios de um servidor de uma só vez (mais de 10.000) pode causar falhas de sincronização - exclua diretórios em lotes de menos de 10.000 e verifique se as operações de exclusão são sincronizadas com êxito antes de excluir o próximo lote.
- Não tem suporte na raiz de um volume.
- Não armazene um sistema operacional ou o arquivo de paginação do aplicativo dentro de um ponto de extremidade do servidor.
 
### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Para garantir que os arquivos possam ser recuperados corretamente, talvez o sistema não crie automaticamente uma camada para arquivos novos ou alterados por até 32 horas, incluindo a primeira criação de camada após a configuração de um Ponto de Extremidade de Servidor - nós fornecemos um cmdlet do PowerShell para criar uma camada sob demanda para que você possa avaliar as camadas com mais eficiência sem esperar pelo processo em segundo plano.
- Se um arquivo em camadas for copiado usando o Robocopy para outro local, o arquivo resultante não ficará em camadas, mas o atributo offline poderá ser definido, já que Robocopy incorretamente inclui esse atributo em operações de cópia.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.
