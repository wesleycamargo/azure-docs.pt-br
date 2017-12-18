---
title: Armazenamento quente, frio e de arquivo morto para blobs | Microsoft Docs
description: "Armazenamento frequente, esporádico e de arquivo para contas de armazenamento do Azure."
services: storage
documentationcenter: 
author: kuhussai
manager: jwillis
editor: 
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: kuhussai
ms.openlocfilehash: fd3ca18fd7a9d1226d41229d37e637a62478f92a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-blob-storage-hot-cool-and-archive-storage-tiers"></a>Armazenamento de Blobs do Azure: camadas de armazenamento frequentes, esporádicas e de arquivo

## <a name="overview"></a>Visão geral

O armazenamento do Azure oferece três camadas de armazenamento para o armazenamento de objetos de Blobs para que você possa armazenar os dados de uma maneira mais econômica, dependendo de como você os utiliza. A **camada de armazenamento dinâmica** do Azure é otimizada para armazenar dados acessados com frequência. A **camada de armazenamento fria** do Azure é otimizada para armazenar dados acessados com menos frequência e armazenados por pelo menos 30 dias. A **camada de armazenamento de arquivo morto** do Azure é otimizada para armazenar dados acessados raramente e armazenados por pelo menos 180 dias com os requisitos de latência flexível (na ordem de horas). A camada de armazenamento de arquivo só está disponível no nível de blob, e não no nível da conta de armazenamento. Os dados na camada de armazenamento estático podem tolerar uma disponibilidade um pouco menor, mas ainda exigem alta durabilidade e tempo de acesso e taxa de transferência semelhantes aos de dados ativos. Para os dados esporádicos, um SLA com disponibilidade um pouco menor e custos mais altos de acesso, em comparação com os dados frequentes, são compensações aceitáveis, em troca de custos de armazenamento menores. O armazenamento de arquivos está offline e oferece os custos de armazenamento mais baixos, mas também os custos de acesso mais altos.

Hoje, os dados armazenados na nuvem estão aumentando em um ritmo exponencial. Para gerenciar os custos de suas necessidades cada vez maiores de armazenamento, é útil organizar seus dados com base em atributos como frequência de acesso e período de retenção planejado para otimizar os custos. Os dados armazenados na nuvem podem ser diferentes em relação ao modo como são gerados, processados e acessados durante seu tempo de vida. Alguns dados são ativamente acessados e modificados durante seu ciclo de vida. Alguns dados são acessados com frequência no início do seu tempo de vida, mas esse acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente, ou nunca, são acessados após serem armazenados.

Cada um desses cenários de acesso a dados se beneficia de uma camada diferente de armazenamento, otimizada para um padrão de acesso específico. Com as camadas de armazenamento quente, fria e de arquivo morto, o Armazenamento de Blobs do Azure atende a essa necessidade através de camadas de armazenamento diferenciadas com modelos de preços separados.

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que dão suporte a camadas

Você pode definir apenas seus dados de armazenamento de objetos em camadas frequentes, esporádicas ou de arquivo em contas de Armazenamento de Blobs ou de Uso geral v2 (GPv2). Contas de Uso geral v1 (GPv1) não dão suporte a camadas. No entanto, os clientes podem converter facilmente suas contas de Armazenamento de Blob ou de GPv1 existentes para contas de GPv2 por meio de um processo simples de um clique no portal do Azure. O GPv2 fornece uma nova estrutura de preços para acesso a blobs, arquivos, filas e também a uma variedade de outros novos recursos de armazenamento. Além disso, mais adiante, alguns novos recursos e cortes de preços serão oferecidos somente em contas GPv2. Portanto, os clientes devem avaliar usar contas de GPv2, mas apenas usá-las depois de revisar os preços de todos os serviços, uma vez que algumas cargas de trabalho podem ser mais caras em GPv2 do que em GPv1. Consulte [Opções de conta de armazenamento do Azure](storage-account-options.md) para saber mais.

Contas de Armazenamento de Blobs e de GPv2 expõem o atributo **Camada de Acesso** no nível da conta, o que permite que você especifique a camada de armazenamento padrão como frequente ou esporádica para qualquer blob na conta de armazenamento que não tenha o nível definido no nível do objeto. Para objetos com a camada definida no nível do objeto, a camada da conta não será aplicada. A camada de arquivo só pode ser aplicada no nível de objeto. Você pode alternar entre esses níveis de armazenamento a qualquer momento.

## <a name="hot-access-tier"></a>Camada de acesso quente

O armazenamento frequente tem custos de armazenamento mais altos do que o armazenamento esporádico e de arquivos, mas custos de acesso mais baixos. Os cenários de uso de exemplo para a camada de armazenamento dinâmico incluem:

* Dados que estão em uso ativo ou devem ser acessados (lidos e gravados) com frequência.
* Dados preparados para processamento e eventual migração para a camada de armazenamento estático.

## <a name="cool-access-tier"></a>Camada de acesso frio

A camada de armazenamento esporádico tem custos de armazenamento mais baixos e custos de acesso mais altos, comparados ao armazenamento frequente. Essa camada destina-se aos dados que permanecerão na camada esporádica por pelo menos 30 dias. Os cenários de uso de exemplo para a camada de armazenamento estático incluem:

* Conjuntos de dados de recuperação de desastre e de backup de curto prazo.
* Conteúdo de mídia mais antigo que não é mais exibido frequentemente, mas ainda deve estar disponível imediatamente quando acessado.
* Grandes conjuntos de dados que precisam ser armazenados de forma econômica enquanto mais dados estão sendo obtidos para processamento futuro. (*por exemplo*, armazenamento de longo prazo de dados científicos; dados brutos de telemetria de uma instalação de manufatura)

## <a name="archive-access-tier"></a>Camada de acesso ao arquivo

O armazenamento de arquivo morto tem o menor custo de armazenamento e os custos mais altos de recuperação de dados comparados com o armazenamento quente e frio. Essa camada destina-se aos dados que podem tolerar várias horas de latência de recuperação e permanecerão na camada de arquivos por pelo menos 180 dias.

Enquanto um blob estiver no armazenamento de arquivos, ele estará offline e não poderá ser lido (exceto os metadados, que estão online e disponíveis), copiado, substituído ou modificado. Você também não pode tirar instantâneos de um blob no armazenamento de arquivo morto. No entanto, você pode usar as operações existentes para excluir, listar, obter propriedades/metadados de blob ou alterar a camada do seu blob.

Os cenários de uso de exemplo para a camada de armazenamento de arquivo morto incluem:

* Conjuntos de dados de recuperação de desastre, arquivo morto e backup de longo prazo
* Dados originais (brutos) que devem ser preservados, mesmo após serem processados em formato utilizável final. (*por exemplo*, arquivos de mídia brutos após transcodificação em outros formatos)
* Dados de conformidade e arquivamento que precisam ser armazenados por um longo tempo e quase nunca são acessados. (*por exemplo*, filmagens de câmeras de segurança, raios X/ressonâncias magnéticas antigos para organizações de serviços de saúde, gravações de áudio e transcrições de chamadas de clientes para serviços financeiros)

### <a name="blob-rehydration"></a>Reidratação de blob
Para ler dados no armazenamento de arquivo morto, primeiro você deve alterar a camada do blob para quente ou fria. Esse processo é conhecido como reidratação e pode levar até 15 horas para ser concluído. Tamanhos grandes de blob são altamente recomendados para otimizar o desempenho. Reidratar vários blobs pequenos simultaneamente pode inserir um tempo adicional.

Durante a reidratação, você pode marcar a propriedade de blob **Status do arquivo** para confirmar se a camada foi alterada. O status exibe "reidratação pendentes para camada quente" ou "reidratação pendente para camada fria" dependendo da camada de destino. Após a conclusão, a propriedade status do arquivo é removida, e a propriedade de blob de **Nível de Acesso** reflete a nova camada frequente ou esporádica.  

## <a name="blob-level-tiering"></a>Camada no nível do blob

As camadas no nível do blob permitem que você altere a camada de seus dados no nível de objeto usando uma única operação chamada [Definir Nível de Blob](/rest/api/storageservices/set-blob-tier). Você pode alterar facilmente o nível de acesso de um blob entre as camadas quente, fria ou de arquivo morto como alteração de padrões de uso, sem a necessidade de mover dados entre contas. Todas as alterações de camada acontecem imediatamente, exceto quando um blob está sendo submetido a uma reidratação a partir do arquivo, o que pode levar muitas horas. A hora da última alteração na camada de blob é exposta por meio da propriedade do blob **Acessar Hora de Alteração da Camada**. Se um blob estiver na camada de arquivamo, não poderá ser substituído e, portanto, o carregamento do mesmo blob não é permitido nesse cenário. Você pode substituir um blob em frequente e esporádico e, nesse caso, o novo blob herda a camada do blob antigo que foi substituído.

Blobs em todos os três camadas de armazenamento podem coexistir na mesma conta. Qualquer blob que não tenha uma camada atribuída explicitamente herda a camada da configuração de nível de acesso da conta. Se a camada de acesso for inferida da conta, você verá a propriedade de blob **Camada de Acesso Inferida** definida como "true", e a propriedade de blob **Camada de Acesso** corresponde à camada da conta. No Portal do Azure, a propriedade da camada de acesso inferida é exibida com a camada de acesso do blob (por exemplo, Frequente (inferido) ou Esporádico (inferido)).

> [!NOTE]
> O armazenamento de arquivos e as camadas no nível do blob só oferecem suporte aos blobs de bloco. Também não é possível alterar a camada de um blob de blocos que tenha instantâneos.

### <a name="blob-level-tiering-billing"></a>Cobrança da camada no nível do blob

Quando um blob é movido para uma camada mais esporádica (frequente -> esporádico, frequente -> arquivo ou esporádico -> arquivo), a operação é cobrada como uma gravação na camada de destino, e os encargos pela operação de gravação (por 10.000) e gravação de dados (por GB) da camada de destino são aplicados. Quando um blob é movido para uma camada mais frequente (arquivo -> esporádico, arquivo -> frequente ou esporádico -> frequente), a operação é cobrada como uma leitura da camada de origem, e os encargos da operação de leitura (por 10.000) e recuperação de dados (por GB) da camada de origem são aplicados.

Caso altere a camada de conta de frequente para esporádico, você será cobrado por operações de gravação (por 10.000) para todos os blobs sem uma camada de conjunto apenas em contas de GPv2. Não há nenhum custo para isso em contas de Armazenamento de Blobs. Você será cobrado por operações de leitura (por 10.000) e por recuperação de dados (por GB) caso altere a conta de Armazenamento de Blobs ou de GPv2 de esporádica para frequente. Encargos de exclusão antecipada para qualquer blob tirado das camadas esporádica ou de arquivo também podem incorrer.

### <a name="cool-and-archive-early-deletion-effective-february-1-2018"></a>Exclusão antecipada frequente e de arquivo (em vigor a partir de 1 de fevereiro de 2018)

Além dos encargos por GB e por mês, qualquer blob que seja colocado na camada esporádica (apenas para contas de GPv2) está sujeito a um período inicial de exclusão moderada de 30 dias, e qualquer blob que seja colocado na camada de arquivo está sujeito a um período de exclusão antecipada do arquivo de 180 dias. A cobrança é proporcional. Por exemplo, se um blob for movido para o arquivo e depois for excluído ou movido para a camada frequente após 45 dias, será cobrada um valor pela exclusão antecipada equivalente a 135 dias (180 menos 45) de armazenamento de blob no arquivo.

## <a name="comparison-of-the-storage-tiers"></a>Comparação entre camadas de armazenamento

A tabela a seguir mostra uma comparação entre as camadas de armazenamento frequente, esporádica e de arquivo.

| | **Camada de armazenamento quente** | **Camada de armazenamento fria** | **Camada de armazenamento de arquivos**
| ---- | ----- | ----- | ----- |
| **Disponibilidade** | 99,9% | 99% | N/D |
| **Disponibilidade** <br> **(Leituras de RA-GRS)**| 99,99% | 99,9% | N/D |
| **Encargos de uso** | Custos de armazenamento maiores, custos de acesso e de transações menores | Custos de armazenamento menores, custos de acesso e de transações maiores | Custos de armazenamento mais baixos, custos de acesso e de transações mais altos |
| **Tamanho mínimo do objeto** | N/D | N/D | N/D |
| **Duração mínima de armazenamento** | N/D | 30 dias (somente GPv2) | 180 dias
| **Latência** <br> **(Tempo até o primeiro byte)** | milissegundos | milissegundos | < 15 horas
| **Escalabilidade e metas de desempenho** | Igual ao das contas de armazenamento de finalidade geral | Igual ao das contas de armazenamento de finalidade geral | Igual ao das contas de armazenamento de finalidade geral |

> [!NOTE]
> As contas de Armazenamento de Blobs dão suporte às mesmas metas de desempenho e escalabilidade que as contas de armazenamento de finalidade geral. Confira [Metas de desempenho e de escalabilidade do Armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para saber mais.

## <a name="quickstart-scenarios"></a>Cenários de início rápido

Nesta seção, os cenários a seguir são demonstrados usando o Portal do Azure:

* Como alterar a camada de acesso de conta padrão de uma conta de Armazenamento de Blobs ou de GPv2.
* Como alterar a camada de um blob em uma conta de Armazenamento de Blobs ou de GPv2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar a camada de acesso de conta padrão de uma conta de Armazenamento de Blobs ou de GPv2

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Para navegar até sua conta de armazenamento, selecione Todos os Recursos e sua conta de armazenamento.

3. Na folha Configurações, clique em **Configuração** para exibir e/ou alterar a configuração da conta.

4. Selecione a camada de armazenamento adequada para suas necessidades: defina a **Camada de acesso** como **Estática** ou **Dinâmica**.

5. Clique em Salvar na parte superior da folha.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Alterar a camada de um blob em uma conta de Armazenamento de Blobs ou de GPv2.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Para navegar até seu blob em sua conta de armazenamento, selecione Todos os Recursos, selecione sua conta de armazenamento, selecione seu contêiner e depois seu blob.

3. Na folha de propriedades do Blob, clique no menu suspenso **Camada de acesso** para selecionar a camada de armazenamento **Frequente**, **Esporádica**, ou **Arquivo**.

5. Clique em Salvar na parte superior da folha.

## <a name="faq"></a>Perguntas frequentes

**Devo usar contas de Armazenamento de Blobs ou de GPv2 se quiser colocar meus dados em camadas?**

Recomendamos que você use o GPv2 em vez de contas de Armazenamento de Blobs para camadas. O GPv2 dá suporte a todos os recursos aos quais as contas de Armazenamento de Blobs dão suporte e muito mais. O preço entre o Armazenamento de Blobs e o GPv2 são quase idênticos, mas alguns novos recursos e cortes de preços só estarão disponíveis em contas de GPv2. Contas de GPv1 não dão suporte a camadas.

A estrutura de preços entre contas de GPv1 e GPv2 é diferente, e os clientes devem avaliar cuidadosamente antes de decidir usar contas de GPv2. Você pode converter facilmente uma conta existente de Armazenamento de Blobs ou de GPv1 em GPv2 por meio de um processo simples de um clique. Consulte [Opções de conta de armazenamento do Azure](storage-account-options.md) para saber mais.

**Posso armazenar objetos em todas as três camadas de armazenamento (frequente, esporádica e arquivo) na mesma conta?**

Sim. O atributo **Camada de Acesso** definido no nível de conta é a camada padrão aplicada a todos os objetos na conta sem uma camada de conjunto explícita. No entanto, camadas no nível do blob permitem que você defina a camada de acesso no nível de objeto, independentemente da configuração de camada de acesso da conta. Os blobs em qualquer uma das três camadas de armazenamento (frequente, esporádica ou arquivo) podem existir na mesma conta.

**Posso alterar a camada de armazenamento padrão da minha conta de Armazenamento de Blobs ou de GPv2?**

Sim, você pode alterar a camada de armazenamento padrão definindo o atributo **Access Tier** na conta de armazenamento. A alteração da camada de armazenamento é aplicada a todos os objetos armazenados na conta que não têm uma camada explícita definida. Alternar a camada de armazenamento de frequente para esporádica incorre em operações de gravação (por 10.000) para todos os blobs, sem uma camada de definida apenas em contas de GPv2, e alternar de esporádico para frequente incorre em encargos de operações de leitura (por 10.000) e de recuperação de dados (por GB) para todos os blobs em contas de Armazenamento de Blobs e de GPv2.

**Posso configurar minha camada de acesso de conta padrão para arquivar?**

Não. Apenas as camadas de armazenamento frequente e esporádica podem ser definidas como a camada de acesso de conta padrão. A camada arquivo só pode ser definida no nível do objeto.

**Em quais regiões ficam disponíveis as camadas de armazenamento frequente, esporádica e arquivo?**

As camadas de armazenamento frequente e esporádica, junto com camadas no nível do blob, estão disponíveis em todas as regiões. O armazenamento de arquivo estará disponível, inicialmente, apenas em regiões selecionadas. Para obter uma lista completa, consulte [Produtos do Azure por região](https://azure.microsoft.com/regions/services/).

**Os blobs na camada de armazenamento fria se comportam de forma diferente daqueles na camada de armazenamento quente?**

Os blobs na camada de armazenamento dinâmico têm a mesmo latência que os blobs nas contas de Armazenamento de GPv1, GPv2 e Blobs. Os blobs na camada de armazenamento esporádica têm uma latência parecida (em milissegundos) que os blobs nas contas de Armazenamento de GPv1, GPv2 e Blobs. Os blobs na camada de armazenamento de arquivo têm várias horas de latência em contas de Armazenamento de GPv1, GPv2 e Blobs.

Os Blobs na camada de armazenamento fria têm um SLA (acordo de nível de serviço) com disponibilidade ligeiramente menor do que os blobs armazenados na camada de armazenamento quente. Para obter mais detalhes, confira [SLA para armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**As operações entre as camadas frequente, esporádica e de arquivo são a mesma?**

Sim. Todas as operações entre frequente e esporádica são 100% consistentes. Todas as operações válidas de arquivo, incluindo excluir, listar, obter propriedades/metadados de blob e definir a camada de blob são 100% consistentes com frequente e esporádica. Um blob não pode ser lido ou modificado enquanto estiver na camada de arquivo.

**Ao reidratar um blob da camada de arquivo para a camada frequente ou esporádica, como vou saber quando a reidratação estiver concluída?**

Durante a reidratação, você pode usar a operação de obtenção de propriedades de blob para sondar o atributo **Status de Arquivo** para confirmar quando a alteração de camada estiver concluída. O status exibe "reidratação pendentes para camada quente" ou "reidratação pendente para camada fria" dependendo da camada de destino. Após a conclusão, a propriedade status do arquivo é removida, e a propriedade de blob de **Nível de Acesso** reflete a nova camada frequente ou esporádica.  

**Depois de definir o nível de um blob, quando vai começar a ser cobrada a taxa apropriada?**

Cada blob é sempre cobrado de acordo com a camada indicada pelo propriedade de blob **Camada de Acesso**. Ao definir uma nova camada em um blob, a propriedade **Camada de Acesso** imediatamente refletirá a nova camada para todas as transições, exceto ao reidratar um blob de arquivo para frequente ou esporádico que pode levar várias horas. Nesse caso, continuarão a ser cobradas tarifas de arquivo até que a reidratação seja concluída, quando a **Camada de Acesso** refletirá a nova camada. Somente nesse momento, a cobrança será feita com base na nova taxa de frequente ou esporádica.

**Como determino se vou incorrer um custo de exclusão antecipada ao excluir ou tirar um blob da camada esporádica ou de arquivo?**

Qualquer blob que for excluído ou tirado da camada esporádica (somente para contas de GPv2) ou de arquivo antes de 30 e 180 dias respectivamente incorrerá em uma carga de exclusão antecipada proporcional (em vigor a partir de 1 de fevereiro de 2018). Você pode determinar quanto tempo um blob está na camada esporádica ou de arquivo verificando a propriedade **Acessar hora de alteração da camada**, a qual fornece um carimbo da última alteração da camada. Consulte a seção [Exclusão antecipada de esporádica e de arquivo](#cool-and-archive-early-deletion) para obter mais detalhes.

**Quais ferramentas e SDKs do Azure dão suporte a armazenamento de camada no nível do blob e de arquivo?**

As ferramentas do portal do Azure, do PowerShell e da CLI e as bibliotecas de cliente .NET, Java, Python e Node.js, todas dão suporte ao armazenamento de camada no nível do blob e de arquivo.  

**Qual a quantidade de dados que posso armazenar nas camadas frequente, esporádica ou de arquivo?**

O armazenamento de dados e outros limites são definidos no nível da conta e não por camada de armazenamento. Portanto, você pode optar por usar todo o seu limite em uma camada ou em todas as três camadas. Confira [Metas de desempenho e de escalabilidade do Armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para saber mais.

## <a name="next-steps"></a>Próximas etapas

### <a name="evaluate-hot-cool-and-archvie-in-gpv2-blob-storage-accounts"></a>Avaliar frequente, esporádica e de arquivo em contas de Armazenamento de Blobs e de GPv2

[Verificar a disponibilidade de frequente, esporádica e de arquivo por região](https://azure.microsoft.com/regions/#services)

[Avaliar o uso de suas contas de armazenamento atuais, habilitando as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Verificar o preço de frequente, esporádica e de arquivo em contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
