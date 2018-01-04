---
title: Gerenciar a capacidade de armazenamento na pilha do Azure | Microsoft Docs
description: "Monitorar e gerenciar o espaço de armazenamento disponível para a pilha do Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: b0e694e4-3575-424c-afda-7d48c2025a62
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 7056aefc6bc6203c8961b8a254a2b631c9072c7b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Gerenciar a capacidade de armazenamento para a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

As informações neste artigo ajuda o monitor de operador de nuvem do Azure pilha e gerenciar a capacidade de armazenamento de sua implantação de pilha do Azure. A infraestrutura de armazenamento do Azure pilha aloca um subconjunto da capacidade de armazenamento total da implantação de pilha do Azure, a ser usado para **serviços de armazenamento**. Os serviços de armazenamento armazenam dados de um locatário em compartilhamentos em volumes que correspondem a nós da implantação.

Como um operador de nuvem, você tem uma quantidade limitada de armazenamento para trabalhar com. A quantidade de armazenamento é definida pela solução de que implementar. A solução é fornecida pelo seu fornecedor OEM quando você usar uma solução de vários nó, ou pelo hardware no qual você instalar o Kit de desenvolvimento de pilha do Azure.

Como a pilha do Azure não oferece suporte a expansão da capacidade de armazenamento, é importante para [monitor](#monitor-shares) o armazenamento disponível para garantir operações eficientes são mantidas.  

Quando a capacidade livre restante de um compartilhamento de fica limitada, planeje [gerenciar espaço](#manage-available-space) para impedir que os compartilhamentos da falta de capacidade.

As opções para gerenciar a capacidade incluem:
- Recuperar a capacidade
- Migrar um contêiner

Quando um compartilhamento é 100% de utilização, o armazenamento de serviço não é mais funções para compartilhamento. Para obter assistência na restauração de operações para o compartilhamento, entre em contato com o suporte da Microsoft.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Entender os volumes e compartilhamentos, contêineres e discos
### <a name="volumes-and-shares"></a>Volumes e compartilhamentos
O *serviço de armazenamento* particiona o armazenamento disponível em volumes separados e são iguais que estão alocados para armazenar dados de locatário. O número de volumes é igual ao número de nós na implantação do Azure de pilha:

- Em uma implantação de quatro nós, há quatro volumes. Cada volume tem um único compartilhamento. Em uma implantação de vários nó, o número de compartilhamentos não é reduzido se um nó for removido ou com defeito.
- Se você usar o Kit de desenvolvedor de pilha do Azure, há um único volume, com um único compartilhamento.

Como os compartilhamentos do serviço de armazenamento são para uso exclusivo dos serviços de armazenamento, você deve não diretamente modificar, adicionar ou remover todos os compartilhamentos de arquivos. Somente os serviços de armazenamento devem funcionar em arquivos armazenados nesses volumes.

Compartilhamentos em volumes mantêm dados de locatário. Dados de locatário incluem blobs de página, blobs de blocos, acrescentar blobs, tabelas, filas, os bancos de dados e relacionados ao armazenamento de metadados. Como os objetos de armazenamento (blobs, etc.) são individualmente contidos em um único compartilhamento, o tamanho máximo de cada objeto não pode exceder o tamanho de um compartilhamento. O tamanho máximo de novos objetos depende da capacidade que permanece em um compartilhamento como espaço não utilizado quando o novo objeto é criado.

Quando um compartilhamento é baixo no espaço livre e ações a serem [recuperar](#reclaim-capacity) espaço não são bem-sucedidas ou disponível, o operador de nuvem do Azure pilha pode [migrar](#migrate-a-container-between) os contêineres de blob de um compartilhamento para outro.

- Para obter mais informações sobre contêineres e blobs, consulte [armazenamento de Blob](azure-stack-key-features.md#blob-storage) em recursos de chave e os conceitos na pilha do Azure.
- Para obter informações sobre como os usuários de locatário funcionam com o armazenamento de blog na pilha do Azure, consulte [serviços de armazenamento do Azure pilha](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Contêineres
Os usuários de locatário criar contêineres que são usados para armazenar dados blob. Enquanto o usuário decide o contêiner no qual colocar os blobs, o serviço de armazenamento usa um algoritmo para determinar em qual volume para colocar o contêiner. O algoritmo geralmente escolhe o volume com mais espaço disponível.  

Depois de um blob é colocado em um contêiner, blob pode aumentar para usar mais espaço. Conforme você adiciona novos blobs e existente blobs crescem, o espaço disponível no volume que contém o contêiner for reduzido.  

Contêineres não estão limitados a um único compartilhamento. Quando os dados de blob combinados em um contêiner crescem use 80% ou mais de espaço disponível, o contêiner insere *estouro* modo. No modo de estouro, quaisquer novos blobs que são criados no contêiner são alocados para um volume diferente que tenha espaço suficiente. Ao longo do tempo, um contêiner no modo de estouro pode ter os blobs que são distribuídos entre vários volumes.

Quando 80% e 90% do espaço disponível em um volume é usado, o sistema gera alertas no portal do administrador de pilha do Azure. Operadores de nuvem devem analisar a capacidade de armazenamento disponível e planeja reequilibrar o conteúdo. O serviço de armazenamento para de funcionar quando um disco é 100% usado e não há alertas adicionais são geradas.

### <a name="disks"></a>Discos
Discos de VM são adicionados aos contêineres por locatários e incluem um disco do sistema operacional. Máquinas virtuais também podem ter um ou mais discos de dados. Os dois tipos de discos são armazenados como blobs de página. As diretrizes para locatários são colocar cada disco em um contêiner separado para melhorar o desempenho da VM.
- Cada contêiner que mantém um disco (blob de página) de uma VM é considerado um contêiner anexado à VM que possui o disco.
- Um contêiner que não possui nenhum disco de uma VM é considerado um contêiner livre.

As opções para liberar espaço em um contêiner anexado [são limitados](#move-vm-disks).
> [!TIP]  
> Operadores de nuvem diretamente não gerencia discos, que são anexados às máquinas virtuais (VMs) que locatários podem adicionar a um contêiner. No entanto, ao planejar gerenciar o espaço de armazenamento compartilhamentos, pode ser útil para entender como discos se relacionam com contêineres e compartilhamentos.

## <a name="monitor-shares"></a>Compartilhamentos de monitor
Use o PowerShell ou o portal de administração para monitorar compartilhamentos para que você possa entender quando o espaço livre é limitado. Quando você usar o portal, você recebe alertas sobre compartilhamentos têm espaço suficiente.    

### <a name="use-powershell"></a>Usar o PowerShell
Como um operador de nuvem, você pode monitorar a capacidade de armazenamento de um compartilhamento usando o PowerShell **AzsStorageShare Get** cmdlet. O cmdlet Get-AzsStorageShare retorna o espaço livre total e alocado em bytes em cada um dos compartilhamentos.   
![Exemplo: Espaço livre para compartilhamentos de retorno](media/azure-stack-manage-storage-shares/free-space.png)

- **Capacidade total** é o espaço total em bytes, que estão disponíveis no compartilhamento. Este espaço é usado para dados e metadados que é mantido pelos serviços de armazenamento.
- **Capacidade utilizada** é a quantidade de dados em bytes que são usados por todas as extensões dos arquivos que armazenam os dados de locatário e metadados associados.

### <a name="use-the-administrator-portal"></a>Use o portal do administrador
Como um operador de nuvem, você pode usar o portal de administração para exibir a capacidade de armazenamento de todos os compartilhamentos. **Vá para o armazenamento** > **compartilhamentos de arquivos** para abrir a lista de compartilhamento de arquivo onde você pode exibir as informações de uso.
![Exemplo: Compartilhamentos de arquivos de armazenamento](media/azure-stack-manage-storage-shares/storage-file-shares.png)
- **TOTAL** é o espaço total em bytes, que estão disponíveis no compartilhamento. Este espaço é usado para dados e metadados que é mantido pelos serviços de armazenamento.
- **USADO** é a quantidade de dados em bytes que são usados por todas as extensões dos arquivos que armazenam os dados de locatário e metadados associados.

### <a name="storage-space-alerts"></a>Alertas de espaço de armazenamento
Quando você usar o portal de administração, você recebe alertas sobre compartilhamentos têm espaço suficiente.

> [!IMPORTANT]
> Como um operador de nuvem, mantenha compartilhamentos atinjam o uso completo. Quando um compartilhamento é 100% de utilização, o armazenamento de serviço não é mais funções para compartilhamento. Para recuperar o espaço livre e operações em um compartilhamento de 100% de utilização de restauração, você deve entrar em contato com o suporte da Microsoft.

**Aviso**: quando um compartilhamento de arquivos é acima de 80% de utilização, você receberá um *aviso* alerta no portal de administração: ![exemplo: alerta de aviso](media/azure-stack-manage-storage-shares/alert-warning.png)


**Crítico**: quando um compartilhamento de arquivos é acima de 90% de utilização, você receberá um *crítico* alerta no portal de administração: ![exemplo: alerta crítico](media/azure-stack-manage-storage-shares/alert-critical.png)

**Exibir detalhes**: no portal de administração, você pode abrir os detalhes de um alerta exibir as opções de atenuação: ![exemplo: exibir detalhes do alerta](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Gerenciar o espaço disponível
Quando for necessário para liberar espaço em um compartilhamento, use os métodos menos invasivos primeiro. Por exemplo, tente recuperar espaço antes de você optar por migrar um contêiner.  

### <a name="reclaim-capacity"></a>Recuperar a capacidade
*Essa opção se aplica a implantações de vários nós e o Kit de desenvolvimento de pilha do Azure.*

Você pode recuperar a capacidade usada pelas contas de inquilinos que tenham sido excluídas. Essa capacidade é automaticamente recuperado quando os dados [período de retenção](azure-stack-manage-storage-accounts.md#set-the-retention-period) for atingido, ou você pode agir para solicitá-lo imediatamente.

Para obter mais informações, consulte [recuperar a capacidade de](azure-stack-manage-storage-accounts.md#reclaim) em gerenciar recursos de armazenamento.

### <a name="migrate-a-container-between-volumes"></a>Migrar um contêiner entre volumes
*Essa opção só se aplica a implantações de vários nós.*

Devido a padrões de uso de locatário, alguns compartilhamentos de locatário usam mais espaço do que outras pessoas. O resultado pode ser um compartilhamento que fica com pouco espaço antes de outros compartilhamentos que não são relativamente utilizadas.

Você pode tentar liberar espaço em um compartilhamento com uso excessivo migrando manualmente alguns contêineres de blob para um compartilhamento diferente. Você pode migrar vários contêineres menores para um único compartilhamento com capacidade para manter todos eles. Você pode usar a migração para mover *livre* contêineres. Contêineres livres são contêineres que não contêm um disco de uma VM.   

Migração consolida um blob de contêineres no compartilhamento de novo.

- Se um contêiner entrou no modo de estouro e colocou blobs em volumes adicionais, o novo compartilhamento deve ter capacidade suficiente para manter todos os blobs para o contêiner que você migrar. Isso inclui os blobs que estão localizados em compartilhamentos adicionais.

- O cmdlet do PowerShell *AzsStorageContainer Get* identifica apenas o espaço em uso no volume inicial para um contêiner. O cmdlet não identifica o espaço usado por blobs colocados em volumes adicionais. Portanto, o tamanho total de um contêiner pode não ser evidente. É possível que a consolidação de um contêiner em um novo compartilhamento pode enviar esse novo compartilhamento em uma condição de estouro coloca os dados em compartilhamentos adicionais. Como resultado, talvez seja necessário reequilibrar compartilhamentos novamente.

- Se você não tem permissões para um grupo de recursos e não pode usar o PowerShell para consultar os volumes adicionais para dados de estouro, trabalhe com o proprietário desses grupos de recursos e contêineres para entender o tamanho total dos dados para migrar antes de migrar os dados.  

> [!IMPORTANT]
> Migração de blobs para um contêiner é uma operação offline que requer o uso do PowerShell. Até que a migração é concluída, todos os blobs para o contêiner que você está migrando permanecerão offline e não podem ser usados.

#### <a name="to-migrate-containers-using-powershell"></a>Para migrar usando o PowerShell de contêineres
1. Confirme se você tem [PowerShell do Azure instalado e configurado](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Para obter mais informações, consulte [Usando o PowerShell do Azure com o Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767).
2.  Examine o contêiner para compreender quais dados no compartilhamento que você planeja migrar. Para identificar os contêineres de candidato recomendados para migração em um volume, use o **AzsStorageContainer Get** cmdlet:

    ```
    $shares = Get-AzsStorageShare
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -Intent Migration
    ```
    Em seguida, examine $containers:
    ```
    $containers
    ```
    ![Exemplo: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  Identificar os compartilhamentos de destino melhor para manter o contêiner que migrar:
    ```
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ```
    Em seguida, examine $destinationshares:
    ```
    $destinationshares
    ```    
    ![Exemplo: $destination compartilhamentos](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Inicie a migração para um contêiner. A migração é assíncrona. Se você iniciar a migração de recipientes adicionais antes de concluída a migração primeiro, use a id do trabalho para acompanhar o status de cada um.
  ```
  $jobId = Start-AzsStorageContainerMigration -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares[0].UncPath
  ```
  Em seguida, examine $jobId. No exemplo a seguir, substitua *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* com a id de trabalho que você deseja examinar:
  ```
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ```
5. Use a id do trabalho para verificar o status do trabalho de migração. Quando a migração do contêiner for concluída, **MigrationStatus** é definido como **concluir**.
  ```
  Get-AzsStorageContainerMigrationStatus -JobId $jobId
  ```
  ![Exemplo: Status de migração](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  Você pode cancelar um trabalho de migração em andamento. Cancelado migração trabalhos são processados de forma assíncrona. Você pode acompanhar o cancelamento usando $jobid:

  ```
  Stop-AzsStorageContainerMigration -JobId $jobId
  ```
  ![Exemplo: Status de reversão](media/azure-stack-manage-storage-shares/rollback.png)

7. Você pode executar o comando da etapa 6 novamente, até que o status confirma que o trabalho de migração é **cancelado**:  
    ![Exemplo: Status cancelado](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Mova os discos VM
*Essa opção só se aplica a implantações de vários nós.*

O método mais extremo para gerenciar o espaço envolve a movimentação de discos de máquina virtual. Como mover um contêiner anexado (aquele que contém um disco VM) é complexo, entre em contato com o Microsoft Support para realizar esta ação.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [oferta de máquinas virtuais para os usuários](azure-stack-tutorial-tenant-vm.md).
