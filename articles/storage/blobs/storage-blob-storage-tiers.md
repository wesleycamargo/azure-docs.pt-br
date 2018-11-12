---
title: Armazenamento Premium, Hot, Cool e Archive para blobs - Armazenamento do Azure
description: Armazenamento Premium, Hot, Cool e Archive para contas de armazenamento do Azure.
services: storage
author: kuhussai
ms.service: storage
ms.topic: article
ms.date: 10/18/2018
ms.author: kuhussai
ms.component: blobs
ms.openlocfilehash: 3a980abc7b9611cfd6a3933a54505b0208b67f50
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253713"
---
# <a name="azure-blob-storage-premium-preview-hot-cool-and-archive-storage-tiers"></a>Armazenamento de Blobs do Azure: níveis de armazenamento Premium (pré-visualização), Hot, Cool e Archive

## <a name="overview"></a>Visão geral

O Armazenamento do Azure oferece diferentes camadas de armazenamento que permitem armazenar dados de objeto de blobs da maneira mais econômica. As camadas disponíveis incluem:

- O **armazenamento Premium (versão prévia)** fornece hardware de alto desempenho para dados acessados com frequência.
 
- **Armazenamento frequente**: é otimizado para armazenar dados acessados com frequência. 

- O **armazenamento esporádico** é otimizado para armazenar dados acessados com menos frequência e armazenados por pelo menos 30 dias.
 
- O **armazenamento de arquivos** é otimizado para armazenar dados acessados raramente e armazenados por pelo menos 180 dias com os requisitos de latência flexível (na ordem de horas).

As seguintes considerações acompanham as diferentes camadas de armazenamento:

- A camada de armazenamento de arquivamento só está disponível no nível do blob e não no nível da conta de armazenamento.
 
- Os dados na camada de armazenamento Cool podem tolerar uma disponibilidade um pouco menor, mas ainda exigem alta durabilidade e características de tempo de acesso e taxa de transferência semelhantes aos dados Hot. Para os dados do Cool, um SLA de disponibilidade um pouco menor e custos de acesso mais altos em comparação com os dados do Hot são compromissos aceitáveis para custos de armazenamento mais baixos.

- O armazenamento de arquivos está offline e oferece os custos de armazenamento mais baixos, mas também os custos de acesso mais altos.
 
- Apenas as camadas de armazenamento Hot e Cool podem ser definidas no nível da conta. Atualmente, a camada de arquivamento não pode ser definida no nível da conta.
 
- As camadas Hot, Cool e Archive podem ser definidas no nível do objeto.

Os dados armazenados na nuvem aumentam em ritmo exponencial. Para gerenciar os custos de suas necessidades cada vez maiores de armazenamento, é útil organizar seus dados com base em atributos como frequência de acesso e período de retenção planejado para otimizar os custos. Os dados armazenados na nuvem podem ser diferentes em relação ao modo como são gerados, processados e acessados durante seu tempo de vida. Alguns dados são ativamente acessados e modificados durante seu ciclo de vida. Alguns dados são acessados com frequência no início do seu tempo de vida, mas esse acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente, ou nunca, são acessados após serem armazenados.

Cada um desses cenários de acesso a dados se beneficia de uma camada diferente de armazenamento, otimizada para um padrão de acesso específico. Com os níveis de armazenamento Hot, Cool e Archive, o armazenamento do Azure Blob atende a essa necessidade de camadas de armazenamento diferenciadas com modelos de preços separados.

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que dão suporte a camadas

Você só pode ordenar os dados de armazenamento de objetos para Hot, Cool ou Archive in Blob storage ou General Purpose v2 (GPv2). Contas de Uso geral v1 (GPv1) não dão suporte a camadas. No entanto, os clientes podem converter facilmente suas contas de Armazenamento de Blob ou de GPv1 existentes para contas de GPv2 por meio de um processo simples de um clique no Portal do Azure. O GPv2 fornece uma nova estrutura de preços para acesso a blobs, arquivos, filas e também a uma variedade de outros novos recursos de armazenamento. Além disso, mais adiante, alguns novos recursos e cortes de preços serão oferecidos somente em contas GPv2. Portanto, os clientes devem avaliar usar contas de GPv2, mas apenas usá-las depois de revisar os preços de todos os serviços, uma vez que algumas cargas de trabalho podem ser mais caras em GPv2 do que em GPv1. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

As contas de armazenamento Blob e GPv2 expõem o atributo **Nível de acesso** no nível da conta, que permite especificar a camada de armazenamento padrão como Quente ou Frio para qualquer blob na conta de armazenamento que não tenha uma camada explícita definida em o nível do objeto. Para objetos com a camada definida no nível do objeto, a camada da conta não será aplicada. A camada de arquivamento só pode ser aplicada no nível do objeto. Você pode alternar entre esses níveis de armazenamento a qualquer momento.

## <a name="premium-access-tier"></a>Camada de acesso Premium

Está disponível em versão prévia uma camada de acesso Premium que disponibiliza dados acessado com frequência por meio de hardware de alto desempenho. Os dados armazenados nessa camada são armazenados em discos SSD, otimizados para taxas mais altas de transação e mais baixas de latência em comparação com os discos rígidos tradicionais. A camada de acesso Premium está disponível apenas pelo tipo de conta de Armazenamento do Blobs de Blocos.

Essa camada é ideal para cargas de trabalho que exigem tempos de resposta rápidos e consistentes. Os dados que envolvem usuários finais como edição de vídeo interativo, conteúdo da Web estático, transações online e a curtida são bons candidatos para a camada de acesso Premium. Essa camada é adaptada para cargas de trabalho que executam transações muito pequenas, como captura de dados de telemetria, mensagens e transformação de dados.

Para usar essa camada, provisione uma nova conta de Armazenamento de Blobs de Blocos e comece a criar contêineres e blobs usando a [API REST do serviço Blob](/rest/api/storageservices/blob-service-rest-api), o [AzCopy](/azure/storage/common/storage-use-azcopy) ou o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

Durante a versão prévia, a camada de acesso Premium:

- Está disponível como LRS (armazenamento com redundância local)
- Só está disponível nas seguintes regiões: Leste dos EUA 2, Centro dos EUA e Oeste dos EUA
- Não dá suporte ao gerenciamento automático de ciclo de vida de dados e de disposição em camadas

Para saber como se registrar para a versão prévia da camada de acesso Premium, confira [Introdução ao Armazenamento de Blobs Premium do Azure](https://aka.ms/premiumblob).

## <a name="hot-access-tier"></a>Camada de acesso quente

O armazenamento a quente tem custos de armazenamento mais altos do que o armazenamento Cool e Archive, mas os custos de acesso mais baixos. Cenários de uso de exemplo para a camada de armazenamento Hot incluem:

* Dados que estão em uso ativo ou devem ser acessados (lidos e gravados) com frequência.
* Dados que são preparados para processamento e eventual migração para o nível de armazenamento Cool.

## <a name="cool-access-tier"></a>Camada de acesso frio

O nível de armazenamento fresco tem custos de armazenamento mais baixos e custos de acesso mais altos em comparação com o armazenamento a quente. Essa camada é destinada a dados que permanecerão no nível Cool por pelo menos 30 dias. Cenários de uso de exemplo para o nível de armazenamento Cool incluem:

* Conjuntos de dados de recuperação de desastre e de backup de curto prazo.
* Conteúdo de mídia mais antigo que não é mais exibido frequentemente, mas ainda deve estar disponível imediatamente quando acessado.
* Grandes conjuntos de dados que precisam ser armazenados de forma econômica enquanto mais dados estão sendo obtidos para processamento futuro. (*por exemplo*, armazenamento de longo prazo de dados científicos; dados brutos de telemetria de uma instalação de manufatura)

## <a name="archive-access-tier"></a>Camada de acesso ao arquivo

O armazenamento em arquivo morto tem o menor custo de armazenamento e maiores custos de recuperação de dados em comparação com o armazenamento Hot e Cool. Essa camada destina-se a dados que podem tolerar várias horas de latência de recuperação e permanecerão na camada de arquivamento por pelo menos 180 dias.

Enquanto um blob está no armazenamento do arquivo morto, ele está off-line e não pode ser lido (exceto os metadados, que estão on-line e disponíveis), copiados, sobrescritos ou modificados. Também não é possível tirar instantâneos de um blob no armazenamento do arquivo morto. No entanto, você pode usar as operações existentes para excluir, listar, obter propriedades/metadados de blob ou alterar a camada do seu blob.

Cenários de uso de exemplo para a camada de armazenamento de arquivamento incluem:

* Backup de longo prazo, backup secundário e conjuntos de dados de arquivamento 
* Dados originais (brutos) que devem ser preservados, mesmo após serem processados em formato utilizável final. (*por exemplo*, arquivos de mídia brutos após transcodificação em outros formatos)
* Dados de conformidade e arquivamento que precisam ser armazenados por um longo tempo e quase nunca são acessados. (*por exemplo*, filmagens de câmeras de segurança, raios X/ressonâncias magnéticas antigos para organizações de serviços de saúde, gravações de áudio e transcrições de chamadas de clientes para serviços financeiros)

### <a name="blob-rehydration"></a>Reidratação de blob
Para ler dados no armazenamento de arquivo morto, primeiro você deve alterar a camada do blob para quente ou fria. Esse processo é conhecido como reidratação e pode levar até 15 horas para ser concluído. Tamanhos grandes de blob são recomendados para otimizar o desempenho. Reidratar vários blobs pequenos simultaneamente pode inserir um tempo adicional.

Durante a reidratação, você pode marcar a propriedade de blob **Status do arquivo** para confirmar se a camada foi alterada. O status exibe "reidratação pendentes para camada quente" ou "reidratação pendente para camada fria" dependendo da camada de destino. Após a conclusão, a propriedade status do arquivo é removida, e a propriedade de blob de **Nível de Acesso** reflete a nova camada frequente ou esporádica.  

## <a name="blob-level-tiering"></a>Camada no nível do blob

As camadas no nível do blob permitem que você altere a camada de seus dados no nível de objeto usando uma única operação chamada [Definir Nível de Blob](/rest/api/storageservices/set-blob-tier). Você pode alterar facilmente o nível de acesso de um blob entre as camadas quente, fria ou de arquivo morto como alteração de padrões de uso, sem a necessidade de mover dados entre contas. Todas as alterações de camadas acontecem imediatamente. No entanto, reidratar um blob dos arquivos pode levar várias horas. 

A hora da última alteração na camada de blob é exposta por meio da propriedade do blob **Acessar Hora de Alteração da Camada**. Se um blob estiver na camada de armazenamento de arquivos, ele não poderá ser substituído; portanto, carregar o mesmo blob não é permitido nesse cenário. Você pode sobrescrever um blob em um nível Hot ou Cool. Nesse caso, o novo blob herda a camada do blob que foi sobrescrita.

Blobs em todos os três camadas de armazenamento podem coexistir na mesma conta. Qualquer blob que não tenha uma camada atribuída explicitamente herda a camada da configuração de nível de acesso da conta. Se a camada de acesso for inferida da conta, você verá a propriedade de blob **Camada de Acesso Inferida** definida como "true", e a propriedade de blob **Camada de Acesso** corresponde à camada da conta. No Portal do Azure, a propriedade da camada de acesso inferida é exibida com a camada de acesso do blob (por exemplo, Frequente (inferido) ou Esporádico (inferido)).

> [!NOTE]
> O armazenamento de arquivos e as camadas no nível do blob só oferecem suporte aos blobs de bloco. Também não é possível alterar a camada de um blob de blocos que tenha instantâneos.

Os dados armazenados na camada de acesso Premium não podem ser dispostos em camadas para acesso frequente, esporádico ou de arquivos usando [Definir camada de blob](/rest/api/storageservices/set-blob-tier) ou usando o gerenciamento de ciclo de vido do Armazenamento de Blobs do Azure. Para mover dados, é necessário copiar blobs sincronicamente da camada de acesso Premium para o acesso esporádico usando [Colocar bloco da API de URL](/rest/api/storageservices/put-block-from-url) ou uma versão do AzCopy compatível com essa API. A API *Colocar bloco pela URL* copia dados sincronicamente no servidor, o que significa que a chamada é concluída apenas depois que todos os dados são movidos do local do servidor de origem para o local de destino.

### <a name="blob-lifecycle-management"></a>Gerenciamento de ciclo de vida de blob
O gerenciamento de ciclo de vida do Armazenamento de Blobs (versão prévia) oferece uma avançada política baseada em regra que pode ser usada para fazer a transição dos seus dados para a melhor camada de acesso e para expirar os dados ao fim do seu ciclo de vida. Ver [gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts) para saber mais.  

### <a name="blob-level-tiering-billing"></a>Cobrança da camada no nível do blob

Quando um blob é movido para uma camada mais esporádica (frequente -> esporádico, frequente -> arquivos ou esporádico -> arquivos), a operação é cobrada como uma operação de gravação na camada de destino, em que os encargos pela operação de gravação (por 10.000) e gravação de dados (por GB) da camada de destino são aplicados. Quando um blob é movido para uma camada mais frequente (arquivo -> esporádico, arquivo -> frequente ou esporádico -> frequente), a operação é cobrada como uma leitura da camada de origem, em que os encargos da operação de leitura (por 10.000) e a recuperação de dados (por GB) da camada de origem são aplicados.

Caso altere a camada de conta de frequente para esporádico, você será cobrado por operações de gravação (por 10.000) para todos os blobs sem uma camada de conjunto apenas em contas de GPv2. Não há nenhum encargo para essa alteração em contas de Armazenamento de Blobs. Você será cobrado por operações de leitura (por 10.000) e por recuperação de dados (por GB) caso altere a conta de Armazenamento de Blobs ou de GPv2 de esporádica para frequente. Encargos de exclusão antecipada para qualquer blob tirado das camadas esporádica ou de arquivo também podem incorrer.

### <a name="cool-and-archive-early-deletion"></a>Arrefecer e arquivar eliminação antecipada

Além da cobrança por GB, por mês, qualquer blob movido para a camada legal (apenas para as contas GPv2) está sujeito a um período de exclusão antecipada Cool de 30 dias, e qualquer blob transferido para a camada Arquivo está sujeito a um período de exclusão antecipada de arquivamento de 180 dias. A cobrança é proporcional. Por exemplo, se um blob for movido para o arquivo e depois for excluído ou movido para o nível quente após 45 dias, será cobrada uma taxa de exclusão antecipada equivalente a 135 (180 menos 45) dias após o armazenamento desse blob no arquivo.

## <a name="comparison-of-the-storage-tiers"></a>Comparação entre camadas de armazenamento

A tabela a seguir mostra uma comparação das camadas de armazenamento Hot, Cool e Archive.

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
> As contas de armazenamento de Blobs dão suporte às mesmas metas de desempenho e escalabilidade que as contas de armazenamento de finalidade geral. Confira [Metas de desempenho e de escalabilidade do Armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para saber mais.

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

## <a name="pricing-and-billing"></a>Preços e cobrança

Todas as contas de armazenamento usam um modelo de preços para o Armazenamento de Blobs com base na camada de cada blob. Tenha em mente as seguintes considerações de cobrança:

* **Custos de armazenamento**: além da quantidade de dados armazenados, o custo de armazenamento de dados varia de acordo com a camada de armazenamento. O custo por gigabyte diminui conforme a camada fica mais esporádica.
* **Custos de acesso a dados**: os encargos de acesso a dados aumentam conforme a camada fica mais esporádica. Para dados no nível de armazenamento esporádico e arquivos, será cobrada uma taxa de acesso a dados por gigPara dados no nível de armazenamento Cool and Archive, será cobrada uma taxa de acesso a dados por gigabyte para leituras.abyte para leituras.
* **Custos de transações**: há um encargo por transação para todas as camadas que aumenta à medida que a camada fica mais esporádica.
* **Custos de transferência de dados de replicação geográfica**: isso só se aplica a contas com replicação geográfica configurada, incluindo GRS e RA-GRS. A transferência de dados de replicação geográfica acarreta um encargo por gigabyte.
* **Custos de transferência de dados de saída**: transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) acarretam a cobrança por uso de largura de banda por gigabyte, de forma consistente com as contas de armazenamento de finalidade geral.
* **Alterando a camada de armazenamento**: alterar a camada de armazenamento de conta de esporádico para frequente implica uma cobrança igual à leitura de todos os dados existentes na conta de armazenamento. No entanto, a alteração da camada de armazenamento de conta de frequente para esporádico gera uma cobrança igual à gravação de todos os dados na camada Cool (somente contas GPv2).

> [!NOTE]
> Para saber mais informações sobre preços para contas de Armazenamento de Blobs, confira a página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para saber mais informações sobre os encargos de transferência de dados de saída, confira a página [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Perguntas frequentes

**Devo usar contas de Armazenamento de Blobs ou de GPv2 se quiser colocar meus dados em camadas?**

Recomendamos que você use o GPv2 em vez de contas de Armazenamento de Blobs para camadas. O GPv2 dá suporte a todos os recursos aos quais as contas de Armazenamento de Blobs dão suporte e muito mais. O preço entre o Armazenamento de Blobs e o GPv2 são quase idênticos, mas alguns novos recursos e cortes de preços só estarão disponíveis em contas de GPv2. Contas de GPv1 não dão suporte a camadas.

A estrutura de preços entre contas de GPv1 e GPv2 é diferente, e os clientes devem avaliar cuidadosamente antes de decidir usar contas de GPv2. Você pode converter facilmente uma conta existente de Armazenamento de Blobs ou de GPv1 em GPv2 por meio de um processo simples de um clique. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

**Posso armazenar objetos em todas as três camadas de armazenamento (frequente, esporádico e Arquivos) na mesma conta?**

Sim. O atributo **Camada de Acesso** definido no nível de conta é a camada padrão aplicada a todos os objetos na conta sem uma camada de conjunto explícita. No entanto, camadas no nível do blob permitem que você defina a camada de acesso no nível de objeto, independentemente da configuração de camada de acesso da conta. Blobs em qualquer um dos três níveis de armazenamento (frequente, esporádico ou Arquivos) podem existir na mesma conta.

**Posso alterar a camada de armazenamento padrão da minha conta de Armazenamento de Blobs ou de GPv2?**

Sim, você pode alterar a camada de armazenamento padrão definindo o atributo **Access Tier** na conta de armazenamento. A alteração da camada de armazenamento é aplicada a todos os objetos armazenados na conta que não têm uma camada explícita definida. Alternar o nível de armazenamento de Hot para Cool gera operações de gravação (por 10.000) para todos os blobs sem um nível definido nas contas GPv2 e alternar de Cool para Hot incorre em taxas de leitura (por 10.000) e recuperação de dados (por GB) para todos blobs em armazenamento Blob e contas GPv2.

**Posso definir meu nível de acesso à conta padrão como Arquivo?**

Não. Somente níveis de armazenamento frequente e esporádicos podem ser definidos como o nível de acesso à conta padrão. A camada arquivo só pode ser definida no nível do objeto.

**Em quais regiões os níveis de armazenamento Hot, Cool e Archive estão disponíveis?**

Os níveis de armazenamento frequente e Esporádico, juntamente com o nível de blob, estão disponíveis em todas as regiões. O armazenamento de arquivo estará disponível, inicialmente, apenas em regiões selecionadas. Para obter uma lista completa, consulte [Produtos do Azure por região](https://azure.microsoft.com/regions/services/).

**Os blobs no nível de armazenamento Esporádico se comportam de maneira diferente dos que estão no nível de armazenamento Frequente?**

Os blobs na camada de armazenamento frequentes têm a mesma latência que os blobs nas contas de armazenamento GPv1, GPv2 e Blob. Os blobs na camada de armazenamento Esporádico têm latência semelhante (em milissegundos) como blobs nas contas de armazenamento GPv1, GPv2 e Blob. Os blobs na camada de armazenamento de arquivamento têm várias horas de latência nas contas de armazenamento GPv1, GPv2 e Blob.

Os blobs na camada de armazenamento Esporádico têm um nível de serviço de disponibilidade (SLA) ligeiramente menor que os blobs armazenados na camada de armazenamento Frequente. Para obter mais informações, veja [SLA para armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**As operações entre as camadas Frequente, Esporádico e Arquivos são as mesmas?**

Sim. Todas as operações entre frequente e esporádico são 100% consistentes. Todas as operações de arquivamento válidas, incluindo excluir, listar, obter propriedades / metadados de blob e definir níveis de blob, são 100% consistentes com frequente e esporádico. Um blob não pode ser lido ou modificado enquanto estiver na camada Arquivo.

**Ao reidratar um blob do nível de arquivamento para o nível quente ou frio, como saberei quando a reidratação estiver concluída?**

Durante a reidratação, você pode usar a operação de obtenção de propriedades de blob para sondar o atributo **Status de Arquivo** para confirmar quando a alteração de camada estiver concluída. O status exibe "reidratação pendentes para camada quente" ou "reidratação pendente para camada fria" dependendo da camada de destino. Após a conclusão, a propriedade status do arquivo é removida, e a propriedade de blob de **Nível de Acesso** reflete a nova camada frequente ou esporádica.  

**Depois de definir o nível de um blob, quando vai começar a ser cobrada a taxa apropriada?**

Cada blob é sempre cobrado de acordo com a camada indicada pelo propriedade **Camada de Acesso** do blob. Quando você define uma nova camada para um blob, a propriedade **Camada de acesso** reflete imediatamente a nova camada para todas as transições. No entanto, a reidratação de um blob do nível Arquivo para um nível Frequente ou Esporádico pode levar várias horas. Nesse caso, você é cobrado nas taxas de arquivamento até que a rehidratação seja concluída. Nesse momento, a propriedade **Nível de acesso** reflete a nova camada. Nesse ponto, você é cobrado pelo blob na taxa Frequente ou Esporádico.

**Como posso determinar se incorrerá em uma cobrança antecipada de exclusão ao excluir ou remover um blob do nível Esporádico ou Arquivos?**

Qualquer blob que seja excluído ou movido para fora do Esporádico (somente contas do GPv2) ou do arquivamento antes de 30 dias e 180 dias, respectivamente, incorrerá em uma taxa de exclusão antecipada proporcional. Você pode determinar quanto tempo um blob ficou na camada de Arrefecimento ou de Arquivo, verificando a propriedade de blob **Tempo de alteração da Camada de Acesso**, que fornece um carimbo da última alteração da camada. Veja a seção [ Exclusão antecipada de esporádico e Arquivo](#cool-and-archive-early-deletion) para mais detalhes.

**Quais ferramentas e SDKs do Azure suportam armazenamento em camadas e armazenamento em bloco no nível de blobs?**

O portal do Azure, as ferramentas PowerShell e CLI e as bibliotecas cliente .NET, Java, Python e Node.js oferecem suporte a armazenamento em camadas e armazenamento em bloco no nível de blob.  

**Quantos dados posso armazenar nas camadas frequente, Esporádico e Arquivos?**

O armazenamento de dados e outros limites são definidos no nível da conta e não por camada de armazenamento. Portanto, é possível optar por usar todo o seu limite em uma camada ou em todas as três camadas. Para saber mais, consulte [Metas de desempenho e escalabilidade do Armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Avalie as contas de armazenamento Frequente, Esporádico e Arquivos em GPv2 Blob

[Verifique a disponibilidade de Frequente, Esporádico e Arquivos por região](https://azure.microsoft.com/regions/#services)

[Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts)

[Avaliar o uso de suas contas de armazenamento atuais, habilitando as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md)

[Verificar o preço de frequente, esporádico e de arquivo em contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
