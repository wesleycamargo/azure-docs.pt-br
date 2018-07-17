---
title: Gerenciar a capacidade de armazenamento no Azure Stack | Microsoft Docs
description: Monitorar e gerenciar o espaço de armazenamento disponível para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: b0e694e4-3575-424c-afda-7d48c2025a62
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: cdfdaf9195f14e3cbe3db2a4507bd91a3133a26e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071378"
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Gerenciar a capacidade de armazenamento para o Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

As informações neste artigo ajuda a monitorar de operador de nuvem do Azure Stack e gerenciar a capacidade de armazenamento de sua implantação do Azure Stack. A infraestrutura de armazenamento do Azure Stack aloca um subconjunto da capacidade de armazenamento total da implantação do Azure Stack, a ser usado para **serviços de armazenamento**. Os serviços de armazenamento armazenam dados de um locatário em compartilhamentos em volumes que correspondem a nós da implantação.

Como um operador de nuvem, você tem uma quantidade limitada de armazenamento para trabalhar com. A quantidade de armazenamento é definida pela solução de que implementar. Sua solução é fornecida pelo seu fornecedor OEM quando você usar uma solução com vários nó, ou pelo hardware no qual você instala o Kit de desenvolvimento do Azure Stack.

Como o Azure Stack não dá suporte a expansão da capacidade de armazenamento, é importante [monitor](#monitor-shares) o armazenamento disponível para garantir operações eficientes são mantidas.  

Quando a capacidade livre restante de um compartilhamento de fica limitada, planeje [gerenciar o espaço](#manage-available-space) para impedir que os compartilhamentos da falta de capacidade.

As opções para gerenciar a capacidade de incluem:
- Recuperar a capacidade
- Migrar de um contêiner

Quando um compartilhamento é 100% de utilização, o armazenamento de serviço não for mais funções para esse compartilhamento. Para obter assistência em operações para o compartilhamento de restauração, entre em contato com o suporte da Microsoft.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Entender os compartilhamentos e volumes, contêineres e discos
### <a name="volumes-and-shares"></a>Volumes e compartilhamentos
O *serviço de armazenamento* particiona o armazenamento disponível em volumes separados e iguais que estão alocados para armazenar dados de locatário. O número de volumes é igual ao número de nós na implantação do Azure Stack:

- Em uma implantação de quatro nós, há quatro volumes. Cada volume tem um único compartilhamento. Em uma implantação de vários nó, o número de compartilhamentos não é reduzido se um nó for removido ou com defeito.
- Se você usar o Kit de desenvolvedor do Azure Stack, há um único volume com um único compartilhamento.

Como os compartilhamentos do serviço de armazenamento são para uso exclusivo de serviços de armazenamento, você deve não diretamente modificar, adicionar ou remover todos os arquivos em compartilhamentos. Somente os serviços de armazenamento devem funcionar em arquivos armazenados nesses volumes.

Compartilhamentos em volumes contêm dados de locatário. Dados de locatário inclui blobs de página, blobs de blocos, acrescentar blobs, tabelas, filas, os bancos de dados e relacionados ao armazenamento de metadados. Como os objetos de armazenamento (blobs, etc.) individualmente estão contidos dentro de um único compartilhamento, o tamanho máximo de cada objeto não pode exceder o tamanho de um compartilhamento. O tamanho máximo de novos objetos depende da capacidade que permanece em um compartilhamento como espaço não utilizado quando esse novo objeto é criado.

Quando um compartilhamento está baixo no espaço livre e ações a serem [recuperar](#reclaim-capacity) espaço não estão disponíveis ou bem-sucedida, o operador de nuvem do Azure Stack pode [migrar](#migrate-a-container-between) os contêineres de blob de um compartilhamento para outro.

- Para obter mais informações sobre contêineres e blobs, consulte [armazenamento de BLOBs](azure-stack-key-features.md#blob-storage) nos principais recursos e conceitos do Azure Stack.
- Para obter informações sobre como os usuários de locatário funcionam com o armazenamento de BLOBs do Azure Stack, consulte [serviços de armazenamento do Azure Stack](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Contêineres
Os usuários de locatário criar contêineres que são usados para armazenar dados blob. Enquanto o usuário decide o contêiner no qual colocar blobs, o serviço de armazenamento usa um algoritmo para determinar em qual volume no qual colocar o contêiner. Normalmente, o algoritmo escolhe o volume com mais espaço disponível.  

Depois que um blob é colocado em um contêiner, blob pode aumentar para usar mais espaço. Conforme você adiciona novos blobs e existente blobs crescem, o espaço disponível no volume que contém esse contêiner é reduzido.  

Contêineres não estão limitados a um único compartilhamento. Quando os dados de blob combinados em um contêiner crescem use 80% ou mais de espaço disponível, o contêiner entra *estouro* modo. No modo de estouro, quaisquer novos blobs que são criados nesse contêiner são alocados para um volume diferente que tenha espaço suficiente. Ao longo do tempo, um contêiner no modo de estouro pode ter blobs que são distribuídos entre vários volumes.

Quando 80% e 90% do espaço disponível em um volume é usado, o sistema gerará alertas no portal do administrador do Azure Stack. Operadores de nuvem, examine a capacidade de armazenamento disponível e pretende redistribuir o conteúdo. O serviço de armazenamento para de funcionar quando um disco é 100% de uso e não há alertas adicionais são geradas.

### <a name="disks"></a>Discos
Discos de VM são adicionados a contêineres por locatários e incluem um disco do sistema operacional. VMs também podem ter um ou mais discos de dados. Ambos os tipos de discos são armazenados como blobs de página. As diretrizes para locatários são colocar cada disco em um contêiner separado para melhorar o desempenho da VM.
- Cada contêiner que mantém um disco (blob de páginas) de uma VM é considerado um contêiner anexado à VM que possui o disco.
- Um contêiner que não possui qualquer disco de uma VM é considerado um contêiner gratuito.

As opções para liberar espaço em um contêiner anexado [são limitadas](#move-vm-disks).
> [!TIP]  
> Operadores de nuvem não gerenciam discos, que estão anexados às máquinas virtuais (VMs) que os locatários podem adicionar a um contêiner diretamente. No entanto, ao planejar gerenciar o espaço em compartilhamentos de armazenamento, ele pode ser usados para entender como os discos se relacionam aos contêineres e compartilhamentos.

## <a name="monitor-shares"></a>Compartilhamentos de monitor
Use o PowerShell ou o portal de administração para monitorar compartilhamentos para que você possa entender quando o espaço livre é limitado. Quando você usa o portal, você receberá alertas sobre compartilhamentos que têm espaço suficiente.    

### <a name="use-powershell"></a>Usar o PowerShell
Como um operador de nuvem, você pode monitorar a capacidade de armazenamento de um compartilhamento usando o PowerShell **Get-AzsStorageShare** cmdlet. O cmdlet Get-AzsStorageShare retorna o espaço total, alocado e livre em bytes em cada um dos compartilhamentos.   
![Exemplo: Retornar espaço livre para compartilhamentos](media/azure-stack-manage-storage-shares/free-space.png)

- **Capacidade total** é o espaço total em bytes, que estão disponíveis no compartilhamento. Esse espaço é usado para dados e metadados que é mantido pelos serviços de armazenamento.
- **Capacidade utilizada** é a quantidade de dados em bytes que são usados por todas as extensões dos arquivos que armazenam os dados de locatário e metadados associados.

### <a name="use-the-administrator-portal"></a>Usar o portal do administrador
Como um operador de nuvem, você pode usar o portal de administração para exibir a capacidade de armazenamento de todos os compartilhamentos. **Vá para o armazenamento** > **compartilhamentos de arquivos** para abrir a lista de compartilhamento de arquivo onde você pode exibir as informações de uso.
![Exemplo: Compartilhamentos de arquivos do armazenamento](media/azure-stack-manage-storage-shares/storage-file-shares.png)
- **TOTAL** é o espaço total em bytes, que estão disponíveis no compartilhamento. Esse espaço é usado para dados e metadados que é mantido pelos serviços de armazenamento.
- **USADO** é a quantidade de dados em bytes que são usados por todas as extensões dos arquivos que armazenam os dados de locatário e metadados associados.

### <a name="storage-space-alerts"></a>Alertas de espaço de armazenamento
Quando você usa o portal de administração, você receberá alertas sobre compartilhamentos que têm espaço suficiente.

> [!IMPORTANT]
> Como um operador de nuvem, mantenha compartilhamentos atinjam o uso completo. Quando um compartilhamento é 100% de utilização, o armazenamento de serviço não for mais funções para esse compartilhamento. Para recuperar o espaço livre e operações em um compartilhamento que é 100% de utilização de restauração, você deve contatar o suporte da Microsoft.

**Aviso**: quando um compartilhamento de arquivos é mais de 80% de utilização, você recebe um *aviso* alerta no portal de administração: ![exemplo: alerta de aviso](media/azure-stack-manage-storage-shares/alert-warning.png)


**Críticos**: quando um compartilhamento de arquivos é mais de 90% de utilização, você recebe um *críticas* alerta no portal de administração: ![exemplo: alerta crítico](media/azure-stack-manage-storage-shares/alert-critical.png)

**Exibir detalhes**: no portal de administração, você pode abrir os detalhes de um alerta para exibir as opções de atenuação: ![exemplo: exibir detalhes do alerta](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Gerenciar o espaço disponível
Quando for necessário para liberar espaço em um compartilhamento, use os métodos menos invasivos primeiro. Por exemplo, ao tente recuperar o espaço antes de você optar por migrar de um contêiner.  

### <a name="reclaim-capacity"></a>Recuperar a capacidade
*Essa opção se aplica a implantações de vários nós e o Kit de desenvolvimento do Azure Stack.*

Você pode recuperar a capacidade usada pelas contas de locatário foram excluídas. Essa capacidade é automaticamente recuperado quando os dados [período de retenção](azure-stack-manage-storage-accounts.md#set-the-retention-period) for atingido, ou você pode agir para solicitá-lo imediatamente.

Para obter mais informações, consulte [recuperar capacidade](azure-stack-manage-storage-accounts.md#reclaim) em gerenciar recursos de armazenamento.

### <a name="migrate-a-container-between-volumes"></a>Migrar de um contêiner entre volumes
*Essa opção só se aplica a implantações de vários nós.*

Devido a padrões de uso do locatário, alguns compartilhamentos de locatário usam mais espaço do que outras pessoas. O resultado pode ser um compartilhamento que fica com pouco espaço antes de outros compartilhamentos que estão relativamente não utilizados.

Você pode tentar liberar espaço em um compartilhamento com uso excessivo por migrar manualmente alguns contêineres de blob para um compartilhamento diferente. Você pode migrar vários contêineres menores para um único compartilhamento que tem capacidade para manter todos eles. Você pode usar a migração para mover *livre* contêineres. Livres contêineres são contêineres que não contêm um disco de uma VM.   

Migração consolida todas as um blob de contêineres para o novo compartilhamento.

- Se um contêiner entrou no modo de estouro e colocou os blobs em volumes adicionais, o novo compartilhamento deve ter capacidade suficiente para conter todos os blobs para o contêiner que você migra. Isso inclui os blobs que estão localizados em compartilhamentos adicionais.

- O cmdlet do PowerShell *Get-AzsStorageContainer* identifica apenas o espaço em uso no volume inicial para um contêiner. O cmdlet não identifica o espaço usado por blobs de colocar em volumes adicionais. Portanto, o tamanho total de um contêiner pode não ser evidente. É possível que a consolidação de um contêiner em um novo compartilhamento pode enviar esse novo compartilhamento em uma condição de estouro coloca os dados em compartilhamentos adicionais. Como resultado, talvez seja necessário rebalancear compartilhamentos novamente.

- Se você não tem permissões para um grupo de recursos e não é possível usar o PowerShell para consultar os volumes adicionais para dados de estouro, trabalhe com o proprietário desses grupos de recursos e contêineres para entender o tamanho total dos dados para migrar antes de migrar esses dados.  

> [!IMPORTANT]
> Migração de blobs para um contêiner é uma operação offline que requer o uso do PowerShell. Até que a migração for concluída, todos os blobs para o contêiner que você está migrando permanecerão offline e não podem ser usados. Você também deve evitar a atualização do Azure Stack até a conclusão da migração contínua de todos os.

#### <a name="to-migrate-containers-using-powershell"></a>A migração de contêineres usando o PowerShell
1. Confirme se você tem [Azure PowerShell instalado e configurado](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Para obter mais informações, consulte [Usando o PowerShell do Azure com o Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767).
2.  Examine o contêiner para compreender quais dados no compartilhamento que você planeja migrar. Para identificar os contêineres de candidato práticas para migração em um volume, use o **Get-AzsStorageContainer** cmdlet:

    ````PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    $shares = Get-AzsStorageShare -FarmName $farm_name
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
    ````
    Em seguida, examine $containers:

    ````PowerShell
    $containers
    ````

    ![Exemplo: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  Identificar os melhores compartilhamentos de destino para manter o contêiner que você migra:

    ````PowerShell
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ````

    Em seguida, examine $destinationshares:

    ````PowerShell 
    $destinationshares
    ````

    ![Exemplo: $destination compartilhamentos](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Inicie a migração para um contêiner. A migração é assíncrona. Se você iniciar a migração de contêineres adicionais antes da primeira migração ser concluída, use a id do trabalho para acompanhar o status de cada um.

  ````PowerShell
  $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
  ````

  Em seguida, examine $jobId. No exemplo a seguir, substitua *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* com a id de trabalho que você deseja examinar:

  ````PowerShell
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ````

5. Use a id do trabalho para verificar o status do trabalho de migração. Quando a migração do contêiner for concluída, **MigrationStatus** é definido como **concluir**.

  ````PowerShell 
  Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
  ````

  ![Exemplo: Status de migração](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  Você pode cancelar um trabalho de migração em andamento. Cancelado migração trabalhos são processados de forma assíncrona. Você pode acompanhar o cancelamento usando $jobid:

  ````PowerShell
  Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
  ````

  ![Exemplo: Status de reversão](media/azure-stack-manage-storage-shares/rollback.png)

7. Você pode executar o comando da etapa 6 novamente, até que o status confirma que o trabalho de migração está **cancelado**:  

    ![Exemplo: Status cancelado](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Mova os discos VM
*Essa opção só se aplica a implantações de vários nós.*

O método mais graves para gerenciar o espaço envolve a movimentação de discos de máquina virtual. Como mover um contêiner anexado (um que contém um disco de VM) é complexo, entre em contato com o Microsoft Support para realizar esta ação.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [oferecendo as máquinas virtuais para os usuários](azure-stack-tutorial-tenant-vm.md).
