---
title: "Planejando uma implantação de Sincronização de Arquivo do Azure (versão prévia) | Microsoft Docs"
description: "Saiba o que considerar ao planejar uma implantação de Arquivos do Azure."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 241b744f5c5e89f53addb4d41d732245d76ef9a3
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planejando uma implantação de Sincronização de Arquivo do Azure (versão prévia)
Use a Sincronização de Arquivos do Azure (versão prévia) para centralizar os compartilhamentos de arquivos de sua organização em Arquivos do Azure, sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. A Sincronização de Arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo descreve as considerações importantes para uma implantação da Sincronização de Arquivos do Azure. Recomendamos ler também [Planejando uma implantação dos Arquivos do Azure](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Terminologia da Sincronização de Arquivos do Azure
Antes de entrar nos detalhes do planejamento de uma implantação da Sincronização de Arquivos do Azure, é importante entender a terminologia.

### <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento
O Serviço de Sincronização de Armazenamento é o recurso do Azure de nível superior da Sincronização de Arquivos do Azure. O recurso Serviço de Sincronização de Armazenamento é um par do recurso de conta de armazenamento e pode, de maneira semelhante, ser implantado em grupos de recursos do Azure. Um recurso de nível superior distinto do recurso de conta de armazenamento é necessário porque o Serviço de Sincronização de Armazenamento pode criar relações de sincronização com várias contas de armazenamento por meio de vários Grupos de Sincronização. Uma assinatura pode ter vários recursos de Serviço de Sincronização de Armazenamento implantados.

### <a name="sync-group"></a>Grupo de Sincronização
Um Grupo de Sincronização define a topologia de sincronização para um conjunto de arquivos. Os pontos de extremidade em um Grupo de Sincronização são mantidos em sincronização entre si. Se, por exemplo, você tiver dois conjuntos distintos de arquivos que deseja gerenciar com a Sincronização de Arquivos do Azure, crie dois Grupos de Sincronização e adicione pontos de extremidade diferentes a cada um. Um Serviço de Sincronização de Armazenamento pode hospedar quantos Grupos de Sincronização forem necessários.  

### <a name="registered-server"></a>Servidor registrado
O objeto de Servidor Registrado representa uma relação de confiança entre seu servidor (ou cluster) e o Serviço de Sincronização de Armazenamento. Você pode registrar quantos servidores desejar em uma instância do Serviço de Sincronização de Armazenamento. No entanto, um servidor (ou cluster) pode ser registrado apenas em um Serviço de Sincronização de Armazenamento por vez.

### <a name="azure-file-sync-agent"></a>Agente de Sincronização de Arquivo do Azure
O agente de Sincronização de Arquivos do Azure é um pacote baixável que permite que o Windows Server seja sincronizado com um compartilhamento de arquivos do Azure. O agente de Sincronização de Arquivos do Azure contém três componentes principais: 
- **FileSyncSvc.exe**: o serviço Windows em segundo plano responsável por monitorar alterações nos Pontos de Extremidade de Servidor e iniciar as sessões de sincronização com o Azure.
- **StorageSync.sys**: o filtro do sistema de arquivos da Sincronização de Arquivos do Azure, responsável por dispor os arquivos em camadas para os Arquivos do Azure (quando as camadas de nuvem estão habilitadas).
- **Cmdlets de gerenciamento do PowerShell**: cmdlets do PowerShell usados para interagir com o provedor de recursos do Azure Microsoft.StorageSync. Encontre esses cmdlets nos seguintes locais (padrão):
    - C:\Arquivos de Programas\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Arquivos de Programas\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Ponto de extremidade do servidor
Um ponto de extremidade do servidor representa uma localização específica em um servidor registrado, como uma pasta em um volume do servidor ou a raiz do volume. Poderá haver Pontos de Extremidade de Servidor Múltiplos no mesmo volume se seus namespaces não forem sobrepostos (por exemplo, F:\sync1 e F:\sync2). Você pode configurar políticas de disposição em camadas de nuvem individualmente para cada ponto de extremidade do servidor. Se você adicionar uma localização de servidor que tem um conjunto existente de arquivos como um Ponto de Extremidade de Servidor a um Grupo de Sincronização, esses arquivos serão mesclados com os outros arquivos que já estão em outros pontos de extremidade no Grupo de Sincronização.

### <a name="cloud-endpoint"></a>Ponto de extremidade de nuvem
Um Ponto de Extremidade da Nuvem é um compartilhamento de arquivos do Azure que faz parte de um Grupo de Sincronização. Todo o compartilhamento de arquivos do Azure é sincronizado e um compartilhamento de arquivos do Azure pode ser membro de apenas um Ponto de Extremidade da Nuvem. Portanto, um compartilhamento de arquivos do Azure pode ser membro de apenas um Grupo de Sincronização. Se você adicionar um compartilhamento de arquivos do Azure que tem um conjunto existente de arquivos como um Ponto de Extremidade da Nuvem a um Grupo de Sincronização, os arquivos existentes serão mesclados com os outros arquivos que já estão em outros pontos de extremidade no Grupo de Sincronização.

> [!Important]  
> A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alteração é iniciado para um Ponto de Extremidade da nuvem apenas uma vez a cada 24 horas. Para obter mais informações, consulte [Perguntas frequentes sobre os Arquivos do Azure](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem 
A disposição em camadas de nuvem é um recurso opcional da Sincronização de Arquivos do Azure, que permite que arquivos pouco usados ou acessados sejam dispostos em camadas nos Arquivos do Azure. Quando um arquivo está disposto em camadas, o filtro do sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys) substitui o arquivo localmente por um ponteiro ou ponto de nova análise. O ponto de nova análise representa uma URL para o arquivo nos Arquivos do Azure. Um arquivo em camadas tem o atributo “offline” definido em NTFS, de forma que os aplicativos de terceiros possam identificar arquivos em camadas. Quando um usuário abre um arquivo em camadas, a Sincronização de Arquivos do Azure recupera os dados de arquivo diretamente dos Arquivos do Azure, sem o usuário precisar saber que o arquivo não está armazenado localmente no sistema. Essa funcionalidade também é conhecida como HSM (Gerenciamento de Armazenamento Hierárquico).

## <a name="azure-file-sync-interoperability"></a>Interoperabilidade da Sincronização de Arquivos do Azure 
Esta seção aborda a interoperabilidade da Sincronização de Arquivos do Azure com soluções de terceiros, funções e recursos do Windows Server.

### <a name="supported-versions-of-windows-server"></a>Versões com suporte do Windows Server
No momento, as versões com suporte do Windows Server na Sincronização de Arquivos do Azure são:

| Versão | SKUs com suporte | Opções de implantação com suporte |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter e Standard | Completo (servidor com uma interface do usuário) |
| Windows Server 2012 R2 | Datacenter e Standard | Completo (servidor com uma interface do usuário) |

Versões futuras do Windows Server serão adicionadas à medida que forem liberadas. Versões anteriores do Windows podem ser adicionadas de acordo com os comentários do usuário.

> [!Important]  
> Recomendamos manter todos os servidores usados com a Sincronização de Arquivos do Azure atualizados com as últimas atualizações do Windows Update. 

### <a name="file-system-features"></a>Recursos do sistema de arquivos
| Recurso | Status de suporte | Observações |
|---------|----------------|-------|
| ACLs (listas de controle de acesso) | Com suporte total | As ACLs do Windows são preservadas pela Sincronização de Arquivo do Azure e são impostas pelo Windows Server nos pontos de extremidade do servidor. Não há suporte (ainda) para as ACLs do Windows nos Arquivos do Azure, caso os arquivos sejam acessados diretamente na nuvem. |
| Links físicos | Ignorado | |
| Links simbólicos | Ignorado | |
| Pontos de montagem | Com suporte parcial | Pontos de montagem podem ser a raiz de um Ponto de Extremidade de Servidor, mas serão ignorados se estiverem contidos no namespace de um Ponto de Extremidade de Servidor. |
| Junções | Ignorado | |
| Pontos de nova análise | Ignorado | |
| Compactação NTFS | Com suporte total | |
| Arquivos esparsos | Com suporte total | Os arquivos esparsos são sincronizados (não são bloqueados), mas são sincronizados com a nuvem como um arquivo completo. Se o conteúdo do arquivo for alterado na nuvem (ou em outro servidor), o arquivo não será mais esparso quando a alteração for baixada. |
| ADS (Fluxos de Dados Alternativos) | Preservados, mas não sincronizados | |

> [!Note]  
> Há suporte para apenas os volumes NTFS.

### <a name="failover-clustering"></a>Clustering de failover
O clustering de failover do Windows Server tem suporte pela Sincronização de Arquivo do Azure para a opção de implantação “Servidor de Arquivos de uso geral”. Não há suporte para o Clustering de Failover em “SOFS (Servidor de Arquivos de Escalabilidade Horizontal) para dados de aplicativos” ou CSVs (Volumes Compartilhados Clusterizados).

> [!Note]  
> O agente de Sincronização de Arquivos do Azure deve ser instalado em cada nó em um Cluster de Failover para a sincronização funcionar corretamente.

### <a name="data-deduplication"></a>Eliminação de duplicação de dados
Para volumes que não têm a disposição em camadas de nuvem habilitada, a Sincronização de Arquivos do Azure dá suporte à Eliminação de Duplicação de Dados do Windows Server habilitada no volume. Atualmente, não damos suporte à interoperabilidade entre a Sincronização de Arquivos do Azure com a disposição em camadas de nuvem habilitada e a Eliminação de Duplicação de Dados.

### <a name="antivirus-solutions"></a>Soluções de antivírus
Como os antivírus funcionam com o exame de arquivos em busca de códigos mal-intencionados conhecidos, um antivírus pode causar o recall de arquivos em camadas. Como os arquivos em camadas têm o atributo “offline” definido, recomendamos consultar seu fornecedor de software para saber como configurar sua solução para ignorar a leitura de arquivos offline. 

Sabe-se que as seguintes soluções dão suporte à possibilidade de ignorar arquivos offline:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (consulte a seção “Scan only what you need to” [Examinar apenas o necessário] na página 90 do PDF)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Soluções de backup
Como as soluções de antivírus, as soluções de backup podem causar o recall de arquivos em camadas. Recomendamos o uso de uma solução de backup de nuvem para fazer backup do compartilhamento do arquivos do Azure, em vez de um produto de backup local.

### <a name="encryption-solutions"></a>Soluções de criptografia
O suporte para soluções de criptografia depende de como elas são implementadas. A Sincronização de Arquivo do Azure é conhecida por funcionar com:

- Criptografia de BitLocker
- Azure RMS (Azure Rights Management Services) (e Active Directory RMS herdado)

A Sincronização de Arquivo do Azure é conhecida por não funcionar com:

- EFS (Sistema de Arquivos com Criptografia) NTFS

Em geral, a Sincronização de Arquivos do Azure deve dar suporte à interoperabilidade com soluções de criptografia que ficam abaixo do sistema de arquivos, como o BitLocker, e com soluções implementadas no formato de arquivo, como o BitLocker. Não foi feita nenhuma interoperabilidade especial para soluções que ficam acima do sistema de arquivos (como NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções de HSM (Gerenciamento de Armazenamento Hierárquico)
Nenhuma outra solução de HSM deve ser usada com a Sincronização de Arquivos do Azure.

## <a name="region-availability"></a>Disponibilidade de região
A Sincronização de Arquivos do Azure está disponível apenas nas seguintes regiões em versão prévia:

| Região | Localização do Datacenter |
|--------|---------------------|
| Oeste dos EUA | Califórnia, EUA |
| Europa Ocidental | Países Baixos |
| Sudeste da Ásia | Cingapura |
| Leste da Austrália | Nova Gales do Sul, Austrália |

Na versão prévia, damos suporte apenas à sincronização com um compartilhamento de arquivos do Azure na mesma região do Serviço de Sincronização de Armazenamento.

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Próximas etapas
* [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
* [Implantar os Arquivos do Azure](storage-files-deployment-guide.md)
* [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
