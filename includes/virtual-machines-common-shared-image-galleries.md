---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 0fe1de9bb674c66d1b665de25ee579bc86e42c75
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192380"
---
Galeria de Imagens Compartilhadas é um serviço que ajuda você a criar a estrutura e a organização em torno de suas imagens de VM gerenciadas personalizadas. Galerias de imagens compartilhadas fornecem:

- Replicação global gerenciado de imagens.
- Controle de versão e o agrupamento de imagens para facilitar o gerenciamento.
- Verifique suas imagens altamente disponível com contas de armazenamento com redundância de zona (ZRS) em regiões que dão suporte a zonas de disponibilidade. O ZRS oferece uma melhor resiliência contra falhas de zona.
- Compartilhando entre assinaturas e até mesmo entre locatários, usando o RBAC.

Usando uma Galeria de Imagens Compartilhadas, é possível compartilhar suas imagens com diferentes usuários, entidades de serviço ou grupos do AD dentro de sua organização. As imagens compartilhadas podem ser replicadas para várias regiões para dimensionar suas implantações mais rápido.

Uma imagem gerenciada é uma cópia de uma VM completa (incluindo quaisquer discos de dados anexados) ou apenas o disco do SO, dependendo de como você cria a imagem. Quando você cria uma VM a partir da imagem, uma cópia dos VHDs na imagem é usada para criar os discos da nova VM. A imagem gerenciada permanece no armazenamento e pode ser usada repetidamente para criar novas VMs.

Se você tiver um grande número de imagens gerenciadas que você precisa manter e gostaria de disponibilizá-los em toda a empresa, você pode usar uma galeria de imagens compartilhado como um repositório que torna mais fácil de compartilhar suas imagens. 

O recurso Galeria de Imagens Compartilhadas tem vários tipos de recursos:

| Resource | DESCRIÇÃO|
|----------|------------|
| **Imagem gerenciada** | Uma imagem básica que pode ser usada sozinho ou usada para criar uma **versão da imagem** em uma galeria de imagens. Imagens gerenciadas são criadas de VMs generalizadas. Uma imagem gerenciada é um tipo especial de VHD que pode ser usado para criar várias VMs e agora pode ser usado para criar versões de imagem compartilhada. |
| **Galeria de imagens** | Como o Azure Marketplace, uma **galeria de imagens** é um repositório para gerenciar e compartilhar imagens, mas você controla quem tem acesso. |
| **Definição da imagem** | Imagens são definidas dentro de uma galeria e transportam informações sobre a imagem e requisitos para usá-lo em sua organização. Você pode incluir informações como se a imagem é Windows ou Linux, requisitos de memória mínima e máxima e notas de versão. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é usada para criar uma VM ao usar uma galeria. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Como uma imagem gerenciada, quando você usa uma **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes. |

<br>


![Gráfico mostrando como você pode ter várias versões de uma imagem na sua galeria](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definições da imagem

As definições de imagem são um agrupamento lógico de versões de uma imagem. A definição da imagem contém informações sobre por que a imagem foi criada, qual sistema operacional é para e informações sobre como usar a imagem. Uma definição de imagem é como um plano para todos os detalhes em torno da criação de uma imagem específica. Você não implanta uma VM de uma definição de imagem, mas da versão da imagem criada da definição.


Há três parâmetros para cada definição de imagem que são usados em combinação - **Publisher**, **oferecem** e **SKU**. Eles são usados para encontrar uma definição de imagem específico. Você pode ter versões de imagem que compartilham um ou dois, mas não todos os três valores.  Por exemplo, aqui estão três definições de imagem e seus valores:

|Definição de imagem|Publicador|Oferta|Sku|
|---|---|---|---|
|myImage1|Contoso|Finanças|Back-end|
|myImage2|Contoso|Finanças|Front-end|
|myImage3|Testando|Finanças|Front-end|

Todos os três têm conjuntos exclusivos de valores. O formato é semelhante a como você pode especificar atualmente os Editor, oferta e SKU para [imagens do Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) no Azure PowerShell para obter a versão mais recente de uma imagem do Marketplace. Cada definição de imagem precisa ter um conjunto exclusivo desses valores.

Estes são os outros parâmetros que podem ser definidos em sua definição de imagem para que você possa acompanhar mais facilmente seus recursos:

* Estado do sistema operacional - você pode definir o estado do sistema operacional generalizada ou especializada, mas apenas generalizado é suportado atualmente. Imagens devem ser criadas das VMs que têm sido generalizadas usando o Sysprep para Windows ou `waagent -deprovision` para Linux.
* Sistema operacional - pode ser Windows ou Linux.
* Descrição - descrição de uso para fornecer informações mais detalhadas sobre por que a definição da imagem existe. Por exemplo, você pode ter uma definição de imagem para o servidor front-end que tem o aplicativo já instalado.
* EULA - pode ser usado para apontar para um contrato de licença de usuário final específico para a definição da imagem.
* Declaração de privacidade e notas de versão - armazenar notas de versão e declarações de privacidade no armazenamento do Azure e forneça um URI para acessá-los como parte da definição de imagem.
* Fim da vida útil data - anexar uma data de fim da vida útil à sua definição de imagem para poder usar a automação para excluir as definições de imagem antiga.
* Marca – você pode adicionar marcas quando você cria sua definição de imagem. Para obter mais informações sobre marcas, consulte [usando marcas para organizar seus recursos](../articles/azure-resource-manager/resource-group-using-tags.md)
* Mínima e máxima de vCPU e recomendações da memória - se a imagem tiver recomendações de memória e vCPU, você pode anexar essas informações à sua definição de imagem.
* Não permitido de tipos de disco – você pode fornecer informações sobre as necessidades de armazenamento para sua VM. Por exemplo, se a imagem não é adequada para discos padrão da unidade de disco rígido, você adicioná-los à lista de não permitir.


## <a name="regional-support"></a>Suporte regional

Regiões de origem são listados na tabela a seguir. Todas as regiões públicas podem ser regiões de destino, mas para replicar para centrais da Austrália e na Austrália Central 2, você precisa ter sua lista de permissões de assinatura. Para solicitar a inclusão na lista de permissões, acesse: https://www.microsoft.com/en-au/central-regions-eligibility/


| Regiões de origem |
|---------------------|-----------------|------------------|-----------------|
| Austrália Central   | EUA Central EUAP | Coreia Central    | Sul do Reino Unido 2      |
| Austrália Central 2 | Ásia Oriental       | Sul da Coreia      | Oeste do Reino Unido         |
| Leste da Austrália      | Leste dos EUA         | Centro-Norte dos EUA | Centro-Oeste dos EUA |
| Sudeste da Austrália | Leste dos EUA 2       | Norte da Europa     | Europa Ocidental     |
| Sul do Brasil        | Leste dos EUA 2 EUAP  | Centro-Sul dos Estados Unidos | Índia Ocidental      |
| Canadá Central      | França Central  | Sul da Índia      | Oeste dos EUA         |
| Leste do Canadá         | Sul da França    | Sudeste Asiático   | Oeste dos EUA         |
| Índia Central       | Leste do Japão      | Norte do Reino Unido         | Oeste dos EUA 2       |
| Centro dos EUA          | Oeste do Japão      | Sul do Reino Unido         |                 |



## <a name="limits"></a>limites 

Há limites por assinatura, para a implantação de recursos usando Shared galerias de imagens:
- 100 galerias de imagem compartilhada, por assinatura por região
- 1.000 definições de imagem, por assinatura por região
- versões de imagem de 10.000, por assinatura por região

Para obter mais informações, consulte [verificar o uso de recursos em relação aos limites](https://docs.microsoft.com/azure/networking/check-usage-against-limits) para obter exemplos sobre como verificar seu uso atual.
 

## <a name="scaling"></a>Dimensionamento
A Galeria de Pesquisa de Imagem permite que você especifique o número de réplicas que você deseja que o Azure mantenha das imagens. Isso ajuda em cenários de implantação de várias VMs, já que as implantações de VM podem ser distribuídas para diferentes réplicas, reduzindo a chance de o processamento de criação de instância ser limitado devido à sobrecarga de uma única réplica.

![Gráfico mostrando como você pode dimensionar imagens](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replicação
A Galeria de Imagens Compartilhadas também permite replicar imagens para outras regiões do Azure automaticamente. Cada versão de imagem compartilhada pode ser replicada para diferentes regiões, dependendo do que faz sentido para sua organização. Um exemplo é sempre replicar a imagem mais recente em várias regiões, enquanto todas as versões mais antigas só estão disponíveis em uma região. Isso pode ajudar a economizar nos custos de armazenamento das versões de imagem compartilhada. 

As regiões para as quais uma versão de Imagem compartilhada é replicada podem ser atualizadas após o horário de criação. O tempo necessário para replicar em diferentes regiões depende da quantidade de dados copiados e do número de regiões para as quais a versão é replicada. Isso pode levar algumas horas em alguns casos. Enquanto a replicação está em andamento, você pode exibir o status da replicação por região. Depois que a replicação de imagem for concluída em uma região, em seguida, você pode implantar uma VM ou conjunto de dimensionamento usando essa versão de imagem na região.

![Gráfico mostrando como você pode replicar imagens](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access

Como a versão da Galeria de Imagem Compartilhada, Imagem Compartilhada e Imagem Compartilhada são todos recursos, eles podem ser compartilhados usando os controles nativos do Azure RBAC. Usando o RBAC, você pode compartilhar esses recursos para outros usuários, as entidades de serviço e grupos. Você pode até compartilhar o acesso a indivíduos fora do locatário do qual que eles foram criados dentro. Depois que um usuário tem acesso para a versão de imagem compartilhada, eles podem implantar uma VM ou um conjunto de dimensionamento de máquina Virtual.  Aqui está a matriz de compartilhamento que ajuda a entender ao que o usuário obtém acesso:

| Compartilhado com o usuário     | Galeria de imagens compartilhadas | Imagem Compartilhada | Versão de imagem compartilhada |
|----------------------|----------------------|--------------|----------------------|
| Galeria de imagens compartilhadas | Sim                  | sim          | Sim                  |
| Imagem Compartilhada         | Não                    | sim          | Sim                  |
| Versão de imagem compartilhada | Não                    | Não            | Sim                  |

É recomendável que o compartilhamento no nível de galeria para a melhor experiência. Para obter mais informações sobre o RBAC, consulte [gerenciar o acesso aos recursos do Azure usando o RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Imagens também podem ser compartilhadas, em grande escala, entre locatários usando um registro de aplicativo multilocatário. Para obter mais informações sobre como compartilhar imagens entre locatários, consulte [compartilhar imagens da Galeria de VM entre locatários do Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Cobrança
Não há custo adicional para usar o serviço de Galeria de Imagens Compartilhadas. Você será cobrado pelos seguintes recursos:
- Custos de armazenamento do armazenamento das versões de imagem compartilhada. Custo depende do número de réplicas da versão da imagem e o número de regiões em que a versão é replicada para. Por exemplo, se você tiver 2 imagens e ambos são replicadas em 3 regiões, em seguida, você será alterada para 6 discos gerenciados com base em seu tamanho. Para obter mais informações, consulte [preço do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
- Encargos de saída de rede para replicação da primeira versão de imagem da região de origem para as regiões replicadas. As réplicas subsequentes são tratadas dentro da região, portanto, não há nenhum encargo adicional. 

## <a name="updating-resources"></a>Atualizando recursos

Depois de criado, você pode fazer algumas alterações para os recursos da Galeria de imagem. Eles são limitados a:
 
Galeria de imagens compartilhadas:
- DESCRIÇÃO

definição da imagem:
- vCPUs recomendadas
- Memória recomendada
- DESCRIÇÃO
- Data de fim da vida útil

Versão da imagem:
- Contagem de réplicas regionais
- Regiões de destino
- Exclusão da mais recente
- Data de fim da vida útil


## <a name="sdk-support"></a>Suporte a SDK

Os seguintes SDKs dão suporte à criação de Galerias de Imagens Compartilhadas:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Modelos

Você pode criar um recurso de Galeria de Imagens Compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão da Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM por meio de uma Versão da Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Perguntas frequentes 

**P.** Como posso listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas? 
 
 a. Para listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas às quais você tem acesso no portal do Azure, siga as etapas abaixo:

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Vá para **Todos os recursos**.
1. Selecione todas as assinaturas sob as quais você gostaria de listar todos os recursos.
1. Procure recursos do tipo **Galeria privada**.
 
   Para ver as definições de imagem e as versões da imagem, você também deve selecionar **Mostrar tipos ocultos**.
 
   Para listar todos os recursos da Galeria de Imagens Compartilhada entre assinaturas para as quais você tem permissões, use o seguinte comando na CLI do Azure:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**P.** Posso mover minha imagem existente para a galeria de imagens compartilhadas?
 
 a. Sim. Há três cenários com base nos tipos de imagens que você pode ter.

 Cenário 1: se você tiver uma imagem gerenciada, poderá criar uma definição de imagem e a versão da imagem usando essa definição.

 Cenário 2: se você tiver uma imagem generalizada não gerenciada, poderá criar uma imagem gerenciada com base nela e então criar uma definição de imagem e a versão da imagem com base nessa definição. 

 Cenário 3: se você tiver um VHD em seu sistema de arquivos local, precisará carregar o VHD, criar uma imagem gerenciada e então criar a definição da imagem e a versão da imagem com base nela.
- Se o VHD for de uma VM do Windows, veja [Carregar um VHD generalizado](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se o VHD for para uma VM do Linux, veja [Carregar um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**P.** Posso criar uma versão da imagem de um disco especializado?

 a. Não, no momento não damos suporte para discos especializados como imagens. Se você tiver um disco especializado, será necessário [criar uma VM a partir do VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk), anexando o disco especializado a uma nova VM. Depois de ter uma VM em execução, você precisa seguir as instruções para criar uma imagem gerenciada a partir da [VM do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) ou [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Quando você tiver uma imagem gerenciada generalizada, poderá iniciar o processo para criar uma descrição da imagem compartilhada e a versão da imagem.

 
**P.** Depois de criada, posso mover o recurso de Galeria de Imagens Compartilhadas para uma assinatura diferente?

 a. Não, não é possível mover o recurso de Galeria de Imagens Compartilhadas para uma assinatura diferente. No entanto, você poderá replicar as versões da imagem na galeria para outras regiões conforme necessário.

**P.** É possível replicar minhas versões de imagem entre nuvens – 21Vianet do Azure China, Azure Alemanha e Nuvem do Azure Governamental? 

 a. Não, você não pode replicar as versões de imagem entre nuvens.

**P.** Posso replicar minhas versões de imagem entre assinaturas? 

 a. Não, você não pode replicar as versões de imagem entre regiões em uma assinatura e usá-las em outras assinaturas por meio de RBAC.

**P.** Posso compartilhar versões de imagens entre os locatários do Azure AD? 

 a. Sim, você pode usar o RBAC para compartilhar a indivíduos entre locatários. Mas, para compartilhar em grande escala, consulte "Galeria de compartilhamento de imagens em locatários do Azure" usando [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) ou [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).


**P.** Quanto tempo leva para replicar as versões de imagem entre todas as regiões de destino?

 a. O tempo de replicação de versão de imagem depende inteiramente do tamanho da imagem e do número de regiões para as quais ela está sendo replicada. No entanto, como uma melhor prática, é recomendável manter a imagem pequena e as regiões de origem e destino perto para melhores resultados. Você pode verificar o status da replicação usando o sinalizador -ReplicationStatus.


**P.** Qual é a diferença entre região de origem e região de destino?

 a. Região de origem é a região em que sua versão da imagem será criada e regiões de destino são as regiões em que uma cópia da sua versão da imagem será armazenada. Para cada versão de imagem, você pode ter apenas uma região de origem. Além disso, passe o local da região de origem como uma das regiões de destino ao criar uma versão da imagem.  


**P.** Como faço para especificar a região de origem ao criar a versão da imagem?

 a. Durante a criação de uma versão de imagem, você pode usar a marca **--location** na CLI e a marca **-Location** no PowerShell para especificar a região de origem. Verifique se a imagem gerenciada que você está usando como imagem base para criar a versão da imagem está no local em que você pretende criar a versão da imagem. Além disso, passe o local da região de origem como uma das regiões de destino ao criar uma versão da imagem.  


**P.** Como faço para especificar o número de réplicas de versão da imagem a serem criadas em cada região?

 a. Há duas maneiras de especificar o número de réplicas de versão da imagem a serem criadas em cada região:
 
1. A contagem de réplica regionais que especifica o número de réplicas que você deseja criar por região. 
2. A contagem de réplicas comuns, que é a contagem padrão por região caso a contagem de réplicas regionais não seja especificada. 

Para especificar a contagem de réplicas regionais, passe o local, juntamente com o número de réplicas que você deseja criar nessa região: "Centro-Sul dos EUA=2". 

Se a contagem de réplicas regionais não for especificada com cada local, o número de réplicas padrão será a contagem de réplicas comuns que você especificou. 

Para especificar a contagem de réplicas comuns na CLI, use o argumento **--replica-count** no comando `az sig image-version create`.


**P.** Posso criar a Galeria de Imagens Compartilhadas em um local diferente daquele em que desejo criar a definição de imagem e a versão da imagem?

 a. Sim, isso é possível. Mas, como uma prática recomendada, incentivamos você a manter o grupo de recursos, Galeria de imagens compartilhadas, definição de imagem e versão da imagem no mesmo local.


**P.** Quais são os encargos para usar a Galeria de Imagens Compartilhadas?

 a. Não há encargos para usar o serviço Galeria de Imagens Compartilhadas, exceto pelos encargos de armazenamento para armazenar as versões de imagem e os encargos de saída de rede para replicar as versões de imagem da região de origem para as regiões de destino.

**P.** Que versão de API devo usar para criar a Galeria de Imagens Compartilhadas, a definição de imagem, a versão da imagem e a VM/VMSS com base na Versão da Imagem?

 a. Para implantações de conjunto de dimensionamento de máquina virtual e VM usando uma versão de imagem, recomendamos usar a versão da API 2018-04-01 ou superior. Para trabalhar com galerias de imagens compartilhadas, definições de imagem e versões de imagem, é recomendável usar a API versão 2018-06-01. 
