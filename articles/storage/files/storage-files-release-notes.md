---
title: Notas de versão para o agente de Sincronização de arquivos do Azure (versão prévia) | Microsoft Docs
description: Notas de versão para o agente de Sincronização de arquivos do Azure (versão prévia).
services: storage
author: wmgries
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/12/2018
ms.author: wgries
ms.openlocfilehash: bb7fa68809341b5132d551ff1cab187bd4d7eeac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="release-notes-for-the-azure-file-sync-agent-preview"></a>Notas de versão para o agente de Sincronização de arquivos do Azure (versão prévia)
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. As instalações do Windows Server são transformadas em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS). Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo fornece as notas de versão para versões com suporte do agente de Sincronização de arquivos do Azure.

## <a name="supported-versions"></a>Versões com suporte
As seguintes versões têm suporte pela Sincronização de arquivos do Azure:

| Marco | Número de versão do agente | Data do lançamento | Status |
|----|----------------------|--------------|------------------|
| Pacote cumulativo de atualizações de março | 2.2.0.0 | 12 de março de 2018 | Com suporte (versão recomendada) |
| Pacote cumulativo de atualizações de fevereiro | 2.1.0.0 | 28 de fevereiro de 2018 | Com suporte |
| Atualizar 1 | 2.0.11.0 | 8 de fevereiro de 2018 | Com suporte |
| Pacote cumulativo de atualizações de janeiro | 1.4.0.0 | 8 de janeiro de 2018 | Com suporte até 8 de maio de 2018<sup>1</sup> |
| Pacote cumulativo de atualizações de novembro | 1.3.0.0 | 30 de novembro de 2017 | Com suporte até 8 de maio de 2018<sup>1</sup> |
| Pacote cumulativo de atualizações de outubro | 1.2.0.0 | 31 de outubro de 2017 | Com suporte até 8 de maio de 2018<sup>1</sup> |
| Versão prévia inicial | 1.1.0.0 | 26 de setembro de 2017 | Com suporte até 8 de maio de 2018<sup>1</sup> |

\[1\]: as versões do agente de Sincronização de arquivos do Azure durante a versão prévia intencionalmente não estão em conformidade com a política de atualização. A política de atualização será aplicada a partir da primeira versão do agente, depois que a Sincronização de arquivos do Azure for declarada geralmente disponível.

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-2200"></a>Versão 2.2.0.0 do agente
As notas de versão a seguir são para a versão 2.2.0.0 do agente de Sincronização de arquivos do Azure lançada em 12 de março de 2018.  Estas notas complementam as notas de versão listadas para as versões 2.1.0.0 e 2.0.11.0

A instalação da v2.1.0.0 para alguns clientes poderia falhar devido ao FileSyncSvc não parar. Essa atualização corrige esse problema.

## <a name="agent-version-2100"></a>Versão 2.1.0.0 do agente
As notas de versão a seguir são para a versão 2.1.0.0 do agente de Sincronização de arquivos do Azure lançada em 28 de fevereiro de 2018. Estas notas complementam as notas de versão listadas para a versão 2.0.11.0.

Esta versão inclui as seguintes alterações:
- Melhoria no tratamento de failover de cluster.
- Melhorias para lidar com arquivos em camadas de maneira confiável.
- Suporte para instalar o agente em computadores de controlador de domínio adicionados a um ambiente de domínio do Windows Server 2008 R2.
- Corrigido nesta versão: a geração de diagnósticos excessivos em servidores com muitos arquivos.
- Melhorias para tratamento de erros de falhas de sessão.
- Melhorias para tratamento de erros de problemas em transferência de arquivo.
- Alterações nesta versão: o intervalo padrão para executar camadas de nuvem quando ele está habilitado em um ponto de extremidade de servidor agora é de uma hora. 
- Problema de bloqueio temporário: movendo recursos de Sincronização de arquivos do Azure (Serviço de Sincronização de Armazenamento) para uma nova assinatura do Azure.

## <a name="agent-version-20110"></a>Versão 2.0.11.0 do agente
As notas de versão a seguir são para a versão 2.0.11.0 do agente de Sincronização de arquivos do Azure lançada em 9 de fevereiro de 2018. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-planning.md) e [Como implantar a Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente (MSI) deve ser instalado com permissões de privilégios elevados (admin).
- O agente não tem suporte nas opções de implantação do Windows Server Core ou Nano Server.
- O agente tem suporte somente pelo Windows Server 2016 e pelo Windows Server 2012 R2.
- O agente requer pelo menos 2 GB de memória física.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, veja [Solução de problemas de Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-troubleshoot.md).
- Esta versão adiciona suporte a DFS-R. Para saber mais, confira o [guia de planejamento](storage-sync-files-planning.md#distributed-file-system-dfs).
- Não use o FSRM (Gerenciador de Recursos de Servidor de Arquivos) ou outras triagens de arquivo. As triagens de arquivo podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido a elas.
- A duplicação de Servidores Registrados (incluindo a clonagem de VM) pode levar a resultados inesperados. Mais especificamente, a sincronização pode nunca convergir.
- A Eliminação de duplicação de dados e as camadas de nuvem não têm suporte no mesmo volume.
 
### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados. 
    
    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoints"></a>Pontos de extremidade de servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Um ponto de extremidade de servidor não pode estar no volume do sistema. Por exemplo, C:\MyFolder não é um caminho aceitável, a menos que C:\MyFolder seja um ponto de montagem.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Esta versão adiciona suporte à sincronização de raiz na raiz de um volume.
- Não armazene um sistema operacional ou o arquivo de paginação do aplicativo que esteja dentro de um ponto de extremidade de servidor.
- Mudou com esta versão: adicionados novos eventos para controlar o tempo de execução total para disposição em camadas de nuvem (EventID 9016), sincronizar o andamento do upload (EventID 9302) e arquivos que não foram sincronizados (EventID 9900).
- Melhorado nesta versão: 
- O desempenho de sincronização do namespace de recuperação de desastres rápida aumentou significativamente.
- A exclusão de números grandes (mais de 10.000) de diretórios não precisa ser feita em lotes com v2*.
 
### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Alterado da versão anterior: novos arquivos são organizadas em camadas em uma hora (anteriormente, 32 horas), sujeito à configuração de política de camadas. Fornecemos um cmdlet do PowerShell para camada sob demanda. Você pode usar o cmdlet para avaliar a criação de camadas com mais eficiência sem esperar pelo processo em segundo plano.
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.
- Mudou em relação à versão anterior: os arquivos agora são baixados como arquivos em camadas em outros servidores, desde que o arquivo seja novo ou já fosse um arquivo em camadas.

## <a name="agent-version-1100"></a>Versão 1.1.0.0 do agente
As notas de versão a seguir são para a versão 1.1.0.0 do agente de Sincronização de arquivos do Azure (lançada em 9 de fevereiro de 2017, versão prévia inicial). 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-planning.md) e [Como implantar a Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente (MSI) deve ser instalado com permissões de privilégios elevados (admin).
- O agente não tem suporte nas opções de implantação do Windows Server Core ou Nano Server.
- O agente tem suporte apenas nas versões do Windows Server 2016 e 2012 R2.
- O agente requer pelo menos 2 GB de memória física.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, veja [Solução de problemas de Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-troubleshoot.md).
- Não use FSRM ou outras triagens de arquivo. As triagens de arquivo podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido a elas.
- A duplicação de Servidores Registrados (incluindo a clonagem de VM) pode levar a resultados inesperados. Mais especificamente, a sincronização pode nunca convergir.
- A Eliminação de duplicação de dados e as camadas de nuvem não têm suporte no mesmo volume.
 
### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Caminhos com mais de 2.048 caracteres.
- A parte DACL de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs em um único item.)
- A parte SACL de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados. 
    
    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoints"></a>Pontos de extremidade de servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Um ponto de extremidade de servidor não pode estar no volume do sistema. Por exemplo, C:\MyFolder não é um caminho aceitável, a menos que C:\MyFolder seja um ponto de montagem.
- O clustering de failover tem suporte somente com discos de cluster, não com CSVs.
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- A exclusão de um número grande de diretórios (mais de 10.000) de um servidor por vez pode causar falhas de sincronização. Exclua diretórios em lotes de menos de 10.000. Verifique se as operações de exclusão foram sincronizadas com êxito antes de excluir o próximo lote.
- Atualmente, não há suporte para um ponto de extremidade do servidor na raiz de um volume.
- Não armazene um sistema operacional ou o arquivo de paginação do aplicativo que esteja dentro de um ponto de extremidade de servidor.
 
### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Para fazer com que os arquivos possam ser recuperados corretamente, o sistema pode não criar camadas arquivos novos ou alterados automaticamente por até 32 horas. Esse processo inclui as camadas de primeira vez depois que um novo ponto de extremidade do servidor é configurado. Fornecemos um cmdlet do PowerShell para camada sob demanda. Você pode usar o cmdlet para avaliar a criação de camadas com mais eficiência sem esperar pelo processo em segundo plano.
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.
