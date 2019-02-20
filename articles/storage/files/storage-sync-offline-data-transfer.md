---
title: Use o Data Box e outros métodos na ingestão offline na Sincronização de Arquivos do Azure.
description: O processo e as melhores práticas para ativar o suporte à migração em massa compatível com sincronização.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.component: files
ms.openlocfilehash: a184e0563d1ad26671c38cabe07f42d97cbe2885
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212269"
---
# <a name="migrate-to-azure-file-sync"></a>Migrar para Sincronização de Arquivos do Azure
Há várias opções para migrar para a Sincronização de Arquivos do Azure:

### <a name="uploading-files-via-azure-file-sync"></a>Carregamento de arquivos por meio da Sincronização de Arquivos do Azure
A opção mais simples é migrar os arquivos localmente para um Windows Server 2012 R2 ou posterior e instalar o agente de Sincronização de Arquivos do Azure. Após configurar a sincronização, seus arquivos serão carregados do servidor. Atualmente, temos observado uma velocidade média de carregamento em todos os nossos clientes de 1 TB a cada 2 dias.
Considere um [cronograma de otimização de largura de banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter) para garantir que seu servidor funcione corretamente em seu data center.

### <a name="offline-bulk-transfer"></a>Transferência de dados em massa
Embora o carregamento seja certamente a opção mais simples, talvez ele não funcione se a largura de banda disponível não permitir sincronizar seus arquivos com o Azure em um período de tempo razoável. O desafio a superar aqui é a sincronização inicial de todo o conjunto de arquivos. Posteriormente, a Sincronização de Arquivos do Azure só migrará as alterações conforme elas ocorrem no namespace, o que geralmente usa muito menos largura de banda.
Para superar esse desafio de carregamento inicial, ferramentas de migração em massa offline, como a [família do Data Box](https://azure.microsoft.com/services/storage/databox) do Azure, poderão ser usadas. O artigo a seguir enfoca o processo que você precisa seguir para se beneficiar da migração offline de uma maneira compatível com a Sincronização de Arquivos do Azure. Ele descreverá as práticas recomendadas que ajudarão você a evitar arquivos conflituosos e a preservar suas ACLs e registros de data e hora de pastas e arquivos depois de ativar a sincronização.

### <a name="online-migration-tools"></a>Ferramentas para migração online
O processo descrito a seguir não funciona somente para o Data Box. Ele funciona para qualquer ferramenta de migração offline (como o Data Box) ou ferramentas online, como o AzCopy, Robocopy ou ferramentas e serviços de terceiros. Portanto, independentemente do método usado para superar o desafio de carregamento inicial, ainda é importante seguir as etapas descritas abaixo para utilizar essas ferramentas de maneira compatível com a sincronização.


## <a name="offline-data-transfer-benefits"></a>Benefícios da transferência de dados offline
Os principais benefícios da migração offline ao usar um Data Box são:

- Ao migrar arquivos para o Azure por meio de um processo de transferência em massa offline, como o Data Box, você não precisa carregar todos os arquivos do servidor pela rede. Em namespaces grandes, isso poderia significar uma economia significativa na largura de banda e no tempo da rede.
- Quando você usa a Sincronização de Arquivos do Azure, independentemente do modo de transporte usado (Data Box, Importação do Azure etc.), seu servidor ativo carrega apenas os arquivos que foram alterados desde que você os enviou para o Azure.
- A Sincronização de Arquivos do Azure garante que suas ACLs de arquivos e pastas também sejam sincronizadas, mesmo se o produto de migração em massa offline não transporte as ACLs.
- Não haverá tempo de inatividade caso o Azure Data Box e a Sincronização de Arquivos do Azure forem utilizados. O uso do Data Box para transferir dados para o Azure utiliza eficientemente a largura de banda da rede preservando a fidelidade do arquivo. Ele também mantém seu namespace atualizado e envia apenas os arquivos que foram alterados desde o envio do Data Box.

## <a name="plan-your-offline-data-transfer"></a>Planejar sua transferência de dados offline
Antes de começar, analise as seguintes informações:

- Conclua sua migração em massa para um ou vários compartilhamentos de arquivos do Azure antes de ativar a sincronização com a Sincronização de Arquivos do Azure.
- Se você planeja usar o Data Box para a migração em massa: Analise os [pré-requisitos de implantação do Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planeje sua topologia final da Sincronização de Arquivos do Azure: [Planejar uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- Escolha a conta de armazenamento do Azure que conterá os compartilhamentos de arquivos com os quais você deseja sincronizar. Assegure-se de que sua migração em massa ocorra nos compartilhamentos de preparo temporários da mesma Conta de Armazenamento. A migração em massa só poderá ser ativada utilizando um compartilhamento final e um compartilhamento de preparo que residem na mesma conta de armazenamento.
- Uma migração em massa só pode ser utilizada quando você cria uma nova relação de sincronização com um local do servidor. Você não pode habilitar uma migração em massa com uma relação de sincronização existente.

## <a name="offline-data-transfer-process"></a>Processo de transferência de dados offline
Esta seção descreve o processo de configuração da Sincronização de Arquivos do Azure de uma forma compatível com as ferramentas de migração em massa, como o Azure Data Box.

![Visualização das etapas do processo que também são explicadas em detalhes em um parágrafo a seguir](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Etapa | Detalhes |
|---|---------------------------------------------------------------------------------------|
| ![Etapa 1 do processo](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Solicite o Data Box](../../databox/data-box-deploy-ordered.md). Há [várias ofertas dentro da família do Data Box](https://azure.microsoft.com/services/storage/databox/data) para atender às suas necessidades. Receba seu Data Box e siga a documentação do Data Box para [copiar seus dados](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box). Certifique-se de que os dados sejam copiados para esse caminho UNC no Data Box: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>` em que `ShareName` é o nome do compartilhamento de preparo. Envie o Data Box para o Azure. |
| ![Etapa 2 do processo](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Aguarde até que seus arquivos apareçam nos compartilhamentos de arquivos do Azure que você designou como compartilhamentos de preparo temporários. **Não habilite a sincronização para esses compartilhamentos!** |
| ![Etapa 3 do processo](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Crie um novo compartilhamento que esteja vazio para cada compartilhamento de arquivos que o Data Box criou para você. Certifique-se de que esse novo compartilhamento esteja na mesma conta de armazenamento que o compartilhamento do Data Box. [Como criar um novo compartilhamento de arquivos do Azure](storage-how-to-create-file-share.md). |
| ![Etapa 4 do processo](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Crie um grupo de sincronização](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) em um serviço de sincronização de armazenamento e faça referência ao compartilhamento vazio como um ponto de extremidade de nuvem. Repita essa etapa para cada compartilhamento de arquivos do Data Box. Analise o guia [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md) e siga as etapas necessárias para configurar a Sincronização de Arquivos do Azure. |
| ![Etapa 5 do processo](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Adicione seu diretório do servidor ao vivo como um ponto de extremidade do servidor](storage-sync-files-deployment-guide.md#create-a-server-endpoint). No processo, especifique que você já moveu os arquivos para o Azure e faça referência aos compartilhamentos de preparo. Você tem a opção de habilitar ou desabilitar a camada de nuvem conforme necessário. Ao criar um ponto de extremidade do servidor em seu servidor ativo, será necessário referenciar o compartilhamento de preparo. Habilite a "Transferência de dados offline" (imagem abaixo) na nova folha do ponto de extremidade do servidor e faça referência ao compartilhamento de preparo que deve residir na mesma conta de armazenamento que o ponto de extremidade da nuvem. A lista de compartilhamentos disponíveis é filtrada por conta de armazenamento e compartilhamentos que ainda não estão sendo sincronizados. |

![Visualizar a interface do usuário do portal do Azure para habilitar a Transferência de dados offline ao criar um novo ponto de extremidade do servidor.](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronização do compartilhamento
Depois de criar seu ponto de extremidade do servidor, a sincronização será iniciada. Para cada arquivo que existe no servidor, a sincronização determinará se esse arquivo também existe no compartilhamento de preparo, onde o Data Box depositou os arquivos e, em caso afirmativo, a sincronização copiará o arquivo do compartilhamento de preparo em vez de carregá-lo do servidor. Se o arquivo não existir no compartilhamento de preparo ou se uma versão mais nova estiver disponível no servidor local, a sincronização realizará o carregamento do arquivo a partir do servidor local.

> [!IMPORTANT]
> Você só pode ativar o modo de migração em massa durante a criação de um ponto de extremidade do servidor. Depois que um ponto de extremidade do servidor for estabelecido, no momento não há forma de integrar dados migrados em massa de um servidor já em sincronização no namespace.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACLs e carimbos de data/hora em arquivos e pastas
A Sincronização de Arquivos do Azure garantirá que as ACLs de arquivos e pastas sejam sincronizadas desde o servidor ativo, mesmo que a ferramenta de migração em massa usada não tenha transportado as ACLs inicialmente. Isso significa que o compartilhamento de preparo pode não conter ACLs em arquivos e pastas. Quando você ativa o recurso de migração de dados offline à medida que cria um novo ponto de extremidade do servidor, nesse momento, as ACLs serão sincronizadas para todos os arquivos no servidor. O mesmo é verdadeiro para criar e modificar carimbos de data/hora (create- e modified-).

## <a name="shape-of-the-namespace"></a>Forma do namespace
A forma do namespace é determinada pelo que está no servidor quando a sincronização é habilitada. Se os arquivos forem excluídos no servidor local após o "-snapshot" e -migration do Data Box, esses arquivos não serão trazidos para o namespace de sincronização ativo. Eles ainda estarão no compartilhamento de preparo, mas nunca serão copiados. Esse é o comportamento desejado já que a sincronização mantém o namespace de acordo com o servidor ativo. O "instantâneo" do Data Box é apenas uma plataforma para a cópia eficiente de arquivos e não a autoridade para a forma do namespace ativo.

## <a name="finishing-bulk-migration-and-clean-up"></a>Conclusão da migração em massa e limpeza
A captura de tela a seguir mostra a folha de propriedades do ponto de extremidade do servidor no portal do Azure. Na seção Transferência de Dados offline, você pode observar o status do processo. Ela mostrará "Em andamento" ou "Concluída".

Após o servidor concluir a sincronização inicial de todo o namespace, ele já não aproveitará o compartilhamento de arquivos temporários com os arquivos migrados em massa do Data Box. Nas propriedades do ponto de extremidade do servidor, procure a transferência de dados offline que o status estará mudando para "Concluído". Neste momento, você pode limpar o compartilhamento de preparo para economizar custos:

1. Quando o status for "Concluído", clique em "Desabilitar transferência de dados offline" nas propriedades do ponto de extremidade do servidor.
2. Considere excluir o compartilhamento de preparo para economizar custos. É improvável que o compartilhamento de preparo contenha ACLs de arquivos e pastas que têm uso limitado. Para fins de backup "pontual", crie um [instantâneo real do compartilhamento de arquivos de sincronização do Azure](storage-snapshots-files.md). Você pode [habilitar o Backup do Azure para obter instantâneos]( ../../backup/backup-azure-files.md) em um agendamento.

![Visualizar a interface do usuário do portal do Azure para localizar propriedades do ponto de extremidade do servidor onde os controles de status e a desabilitação da Transferência de Dados Offline estão localizados.](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Você só deve desabilitar este modo quando o estado for "Concluído" ou você realmente desejar abortar devido a um erro de configuração. Se você estiver desabilitando o modo intermediário em uma implantação legítima, os arquivos começarão a ser enviados pelo servidor, mesmo que o compartilhamento de preparo ainda esteja disponível.

> [!IMPORTANT]
> Após a desabilitção da transferência de dados offline, não há como habilitá-la novamente, mesmo que o compartilhamento de preparo da migração em massa ainda esteja disponível.

## <a name="next-steps"></a>Próximas etapas
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
