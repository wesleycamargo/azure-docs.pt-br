<properties linkid="manage-services-what-is-a-storage-account" urlDisplayName="What is a Storage Account" pageTitle="What is a storage account? | Microsoft Azure" metaKeywords="" description="Learn about the different types of storage accounts available in Azure, and get definitions for key storage terms." metaCanonical="" services="storage" documentationCenter="" title="What is a Storage Account?" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# O que é uma conta de armazenamento?

O Armazenamento do Azure inclui três serviços: armazenamento de Blob, armazenamento de Tabela e armazenamento de Fila. Esses serviços são incluídos em todas as contas de armazenamento. Uma conta de armazenamento fornece seu namespace exclusivo para trabalhar com blobs, filas e tabelas.

Para obter mais informações sobre limites de contas de armazenamento, consulte [Escalabilidade e metas de desempenho do armazenamento do Azure][Escalabilidade e metas de desempenho do armazenamento do Azure].

Todas as informações sobre sua conta de armazenamento, inclusive a data de criação, estão disponíveis no Portal de Gerenciamento, na página **Painel** do **Armazenamento**.

Os custos de armazenamento baseiam-se em quatro fatores: capacidade de armazenamento, esquema de replicação, transações de armazenamento e saída de dados. A capacidade do armazenamento refere-se a quanto de sua alocação de conta de armazenamento você está usando para armazenar dados. O custo de simplesmente armazenar seus dados é determinado pela quantidade de dados que você está armazenando e como eles são replicados. As transações referem-se a todas as operações de leitura e gravação no Armazenamento do Azure. A saída de dados refere-se a dados transferidos para fora de uma região do Azure. Quando os dados de sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, quer esse aplicativo seja um serviço de nuvem ou algum outro tipo de aplicativo, você é cobrado pela saída de dados. (Nos serviços do Azure, você pode tomar medidas para agrupar seus dados e serviços nos mesmos data centers para reduzir ou eliminar encargos de saída de dados.)

A página [Detalhes de preços de armazenamento][Detalhes de preços de armazenamento] fornece informações detalhadas de preços para capacidade de armazenamento, replicação e transações. A página [Detalhes de preços de transferências de dados][Detalhes de preços de transferências de dados] fornece informações detalhadas de preços para saída de dados.

## Conceitos da conta de armazenamento

### Opções de replicação de conta de armazenamento

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### Pontos de extremidade da conta de armazenamento

Os *pontos de extremidade* da conta de armazenamento representam o nível mais alto de namespace para acessar blobs, tabelas ou filas. Os pontos de extremidade padrão de uma conta de armazenamento têm os seguintes formatos:

-   Serviço Blob: http://*mystorageaccount\*.blob.core.windows.net

-   Serviço Tabela: http://*mystorageaccount\*.table.core.windows.net

-   Serviço Fila: http://*mystorageaccount\*.queue.core.windows.net

-   Serviço Arquivo: http://*mystorageaccount\*.file.core.windows.net

A URL para acessar um objeto em uma conta de armazenamento é compilada acrescentando o local do objeto na conta de armazenamento ao ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob\*.

### Segurança da conta de armazenamento

Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ao fornecer as duas chaves de acesso de armazenamento, o Azure permite que você regenere as chaves sem interrupção para o serviço de armazenamento ou o acesso a esse serviço.

Recomendamos que você evite compartilhar suas chaves de acesso a contas de armazenamento com outras pessoas. Se você achar que sua conta foi comprometida, pode gerar suas chaves de acesso novamente de dentro do portal. Selecione sua conta de armazenamento e escolha **Gerenciar Chaves de Acesso**.

Para permitir o acesso a recursos de armazenamento sem dar as suas chaves de acesso, você pode usar uma *assinatura de acesso compartilhado*. Uma assinatura de acesso compartilhado fornece acesso a um recurso na sua conta para um intervalo que você definir e com as permissões que você especificar. Consulte o [Tutorial de assinatura de acesso compartilhado][Tutorial de assinatura de acesso compartilhado] para obter mais informações.

### Métricas e registro em log da conta de armazenamento

-   **métricas mínimas x detalhadas** Você pode configurar métricas mínimas ou detalhadas nas configurações de monitoramento de sua conta de armazenamento. *As métricas mínimas* coletam as métricas dos dados, como ingresso/egresso, disponibilidade, latência e porcentagens de êxitos, agregadas para os serviços Blob, Tabela e Fila. *As métricas detalhadas* coletam o detalhe no nível da operação além dos agregados do nível de serviço para as mesmas métricas. As métricas no modo detalhado permitem uma análise mais próxima dos problemas que ocorrem durante operações de aplicativo. Para obter uma lista completa das métricas disponíveis, consulte [o esquema da tabela de métricas da análise do armazenamento][o esquema da tabela de métricas da análise do armazenamento] Para obter mais informações sobre o monitoramento de armazenamento, consulte [sobre métricas de análise de armazenamento][sobre métricas de análise de armazenamento].

-   **log** O log é um recurso configurável de contas de armazenamento que habilita o log das solicitações para ler, gravar e excluir blobs, tabelas e filas. Você configura o log no Portal de Gerenciamento do Azure, mas não pode exibir os logs no Portal de Gerenciamento. Os logs são armazenados e acessados na conta de armazenamento, no recipiente $logs. Para obter mais informações, consulte [Visão geral da análise de armazenamento (a página pode estar em inglês)][Visão geral da análise de armazenamento (a página pode estar em inglês)].

### Grupos de afinidade para colocalização de Armazenamento do Azure e outros serviços

Um *grupo de afinidades* é um agrupamento geográfico de seus serviços Azure e VMs na conta de armazenamento do Azure. Um grupo de afinidade pode melhorar o desempenho do serviço localizando cargas de trabalho do computador no mesmo data center ou próximo do público-alvo de usuários. Além disso, não há encargos de cobrança incorridos para saída quando os dados em uma conta de armazenamento são acessados por um serviço em execução do mesmo grupo de afinidade.

  [Escalabilidade e metas de desempenho do armazenamento do Azure]: http://msdn.microsoft.com/pt-br/library/dn249410.aspx
  [Detalhes de preços de armazenamento]: http://www.windowsazure.com/pt-br/pricing/details/#storage
  [Detalhes de preços de transferências de dados]: http://www.windowsazure.com/pt-br/pricing/details/data-transfers/
  [storage-replication-options]: ../includes/storage-replication-options.md
  [Tutorial de assinatura de acesso compartilhado]: ../storage-dotnet-shared-access-signature-part-1/
  [o esquema da tabela de métricas da análise do armazenamento]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh343264.aspx
  [sobre métricas de análise de armazenamento]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh343258.aspx
  [Visão geral da análise de armazenamento (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh343268.aspx
