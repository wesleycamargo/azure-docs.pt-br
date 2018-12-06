---
title: Notas de versão para o agente de Sincronização de Arquivos do Azure | Microsoft Docs
description: Notas de versão para o agente de Sincronização de Arquivos do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 11/13/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 45f77c3065feeb011a10bc345c22082b6a89529c
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582801"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão para o agente de Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. As instalações do Windows Server são transformadas em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS). Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo fornece as notas de versão para versões com suporte do agente de Sincronização de arquivos do Azure.

## <a name="supported-versions"></a>Versões com suporte
As seguintes versões têm suporte pela Sincronização de arquivos do Azure:

| Marco | Número de versão do agente | Data do lançamento | Status |
|----|----------------------|--------------|------------------|
| Lançamento V4 | 4.0.1.0 | 13 de novembro de 2018 | Com suporte (versão recomendada) |
| Pacote cumulativo de atualizações de setembro | 3.3.0.0 | 24 de setembro de 2018 | Com suporte |
| Rollup de atualização de agosto | 3.2.0.0 | 15 de agosto de 2018 | Com suporte |
| Disponibilidade Geral | 3.1.0.0 | 19 de julho de 2018 | Com suporte |
| Rollup de atualização de junho | 3.0.13.0 | 29 de junho de 2018 | Sem suporte – Versão de agente expirada em 1 de outubro de 2018 |
| Atualizar 2 | 3.0.12.0 | 22 de maio de 2018 | Sem suporte – Versão de agente expirada em 1 de outubro de 2018 |
| Pacote cumulativo de atualizações de abril | 2.3.0.0 | 8 de maio de 2018 | Sem suporte – Versão de agente expirada em 1 de outubro de 2018 |
| Pacote cumulativo de atualizações de março | 2.2.0.0 | 12 de março de 2018 | Sem suporte – Versão de agente expirada em 1 de outubro de 2018 |
| Pacote cumulativo de atualizações de fevereiro | 2.1.0.0 | 28 de fevereiro de 2018 | Sem suporte – Versão de agente expirada em 1 de outubro de 2018 |
| Atualizar 1 | 2.0.11.0 | 8 de fevereiro de 2018 | Sem suporte – Versão de agente expirada em 1 de outubro de 2018 |
| Pacote cumulativo de atualizações de janeiro | 1.4.0.0 | 8 de janeiro de 2018 | Sem suporte – Versão de agente expirada em 1 de outubro de 2018 |
| Pacote cumulativo de atualizações de novembro | 1.3.0.0 | 30 de novembro de 2017 | Sem suporte – Versão de agente expirada em 1 de outubro de 2018 |
| Pacote cumulativo de atualizações de outubro | 1.2.0.0 | 31 de outubro de 2017 | Sem suporte – Versão de agente expirada em 1 de outubro de 2018 |
| Versão prévia inicial | 1.1.0.0 | 26 de setembro de 2017 | Sem suporte – Versão de agente expirada em 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-4010"></a>Versão de agente 4.0.1.0
As notas sobre a versão a seguir são para a versão 4.0.1.0 do agente de Sincronização de Arquivos do Azure (lançada em 13 de novembro de 2018).

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do AzureRM PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não tem suporte nas opções de implantação do Windows Server Core ou Nano Server.
- O agente tem suporte somente pelo Windows Server 2016 e pelo Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.
- Um erro de parada 0x3B ou erro de parada 0x1E pode ocorrer quando um instantâneo do VSS é criado.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há suporte à execução de sysprep em um servidor que possua o agente Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.
- A Eliminação de duplicação de dados e as camadas de nuvem não têm suporte no mesmo volume.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
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
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado. Para atualizar o nome do servidor no portal, cancele o registro e registre novamente o servidor.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- A configuração de política de camada de nuvem baseada em data é usada para especificar arquivos que devem ser armazenados em cache, se acessados em um número de dias especificado. Para saber mais, consulte [Visão geral da camada de nuvem](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.

## <a name="agent-version-3300"></a>Versão de agente 3.3.0.0
As notas sobre a versão a seguir são para a versão 3.3.0.0 do agente de Sincronização de Arquivos do Azure lançada em 24 de setembro de 2018. Estas notas complementam as notas de versão listadas para a versão 3.1.0.0.

Essa versão inclui as seguintes correções:
- O estado do servidor registrado será "Aparece offline" depois que o agente de Sincronização de Arquivos do Azure for atualizado para a versão 3.1 ou 3.2.
- O serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) apresenta falhas devido a arquivos com caminhos longos.
- O registro do servidor falhará com erro: não foi possível carregar o arquivo ou o assembly Kailani.Afs.StorageSyncProtocol.V3.

## <a name="agent-version-3200"></a>Versão do agente 3.2.0.0
As notas de versão a seguir são para a versão 3.2.0.0 do agente de Sincronização de Arquivos do Azure lançada em 15 de agosto de 2018. Estas notas complementam as notas de versão listadas para a versão 3.1.0.0.

Essa versão inclui as seguintes correções:
- Falha na sincronização com erro de memória insuficiente (0x8007000e) devido à perda de memória

## <a name="agent-version-3100"></a>Versão do agente 3.1.0.0
As notas de versão a seguir são para a versão 3.1.0.0 do agente de Sincronização de Arquivos do Azure (lançada em 19 de julho de 2018).

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do AzureRM PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não tem suporte nas opções de implantação do Windows Server Core ou Nano Server.
- O agente tem suporte somente pelo Windows Server 2016 e pelo Windows Server 2012 R2.
- O agente requer pelo menos 2 GB de memória física.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- Não use o FSRM (Gerenciador de Recursos de Servidor de Arquivos) ou outras triagens de arquivo. As triagens de arquivo podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido a elas.
- Não há suporte à execução de sysprep em um servidor que possua o agente Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. A instalação do agente e o registro do servidor devem ocorrer depois da implantação da imagem do servidor e da conclusão da mini-instalação do sysprep.
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
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas poderão ficar inutilizáveis se os arquivos não forem recuperados antes da exclusão do ponto de extremidade do servidor.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um sistema operacional ou o arquivo de paginação do aplicativo que esteja dentro de um ponto de extremidade de servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado. Para atualizar o nome do servidor no portal, cancele o registro e registre novamente o servidor.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.
