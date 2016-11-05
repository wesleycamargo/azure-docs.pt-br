---
title: Glossário do Azure - dicionário do Azure | Microsoft Docs
description: Use o glossário do Azure para entender a terminologia de nuvem na plataforma do Azure Este pequeno dicionário do Azure oferece definições para termos comuns de nuvem do Azure.
keywords: Dicionário do Azure, terminologia de nuvem, Glossário do Azure, definições de terminologia, termos de nuvem
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''

ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: monicar

---
# Glossário do Microsoft Azure: um dicionário de terminologia de nuvem na plataforma do Azure
O glossário do Microsoft Azure é um pequeno dicionário de terminologia de nuvem para a plataforma do Azure

## Encontre definições de serviço e outros termos relacionados à nuvem
* Para obter definições dos serviços do Azure e de suas contrapartes em AWS, confira [Microsoft Azure e Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).
* Para consultar termos gerais de nuvem do setor, confira [Termos de computação em nuvem](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

O glossário do Azure e as duas referências acima fornecem uma taxonomia completa para o Azure e para o setor de nuvem.

## Lista do glossário do Azure
### <a name="account"></a>conta
Uma conta da Microsoft corporativa ou de estudante, ou ainda pessoal, usada para acessar e gerenciar uma assinatura do Azure. Confira também [Como as assinaturas do Azure estão associadas ao Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)

### <a name="availability-set"></a>conjunto de disponibilidade
Uma coleção de máquinas virtuais gerenciadas em conjunto a fim de fornecer redundância e confiabilidade de aplicativo. O uso de um conjunto de disponibilidade garante que durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível. Confira também [Gerenciar a disponibilidade de máquinas virtuais do Windows](virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gerenciar a disponibilidade de máquinas virtuais do Linux](virtual-machines/virtual-machines-linux-manage-availability.md)

### <a name="classic-model"></a>modelo de implantação clássico do Azure
Um dos dois [modelos de implantação](resource-manager-deployment-model.md) usados para implantar recursos no Azure (o novo modelo é o Azure Resource Manager). Alguns recursos do Azure podem ser implantados apenas em um modelo ou no outro, enquanto outros podem ser implantados nos dois modelos. A orientação para recursos individuais do Azure detalha com quais modelos um recurso pode ser implantado.

### <a name="cli"></a>CLI (interface de linha de comando) do Azure
Uma [interface de linha de comando](xplat-cli-install.md) que pode ser usada para gerenciar os serviços do Azure em PCs com Windows, OSX e Linux.

### <a name="powershell"></a>Azure PowerShell
Uma [interface de linha de comando](powershell-install-configure.md) para gerenciar os serviços do Azure por meio de uma linha de comando em PCs com Windows. Alguns serviços, ou recursos do serviço, podem ser gerenciados apenas por meio do PowerShell ou da CLI. A orientação para cada recurso individual do Azure detalha com quais modelos um recurso pode ser implantado. Confira também [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

### <a name="arm-model"></a>modelo de implantação do Azure Resource Manager
Um dos dois [modelos de implantação](resource-manager-deployment-model.md) usados para implantar recursos no Microsoft Azure (o outro é o modelo de implantação clássico). Alguns recursos do Azure podem ser implantados apenas em um modelo ou no outro, enquanto outros podem ser implantados nos dois modelos. A orientação para recursos individuais do Azure detalha com quais modelos um recurso pode ser implantado.

### <a name="fault-domain"></a>domínio de falha
A coleção de máquinas virtuais em um conjunto de disponibilidade que podem falhar ao mesmo tempo. Um exemplo disso é um grupo de máquinas em um rack que compartilham uma fonte de alimentação e um comutador de rede comum. No Azure, as máquinas virtuais em um conjunto de disponibilidade são separadas automaticamente em vários domínios de falha. Confira também [Gerenciar a disponibilidade de máquinas virtuais do Windows](virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gerenciar a disponibilidade de máquinas virtuais do Linux](virtual-machines/virtual-machines-linux-manage-availability.md)

### <a name="geo"></a>área geográfica
Um limite definido para a residência dos dados e que geralmente contém duas ou mais regiões. Os limites podem ser dentro ou fora de fronteiras nacionais e são influenciados pela regulamentação fiscal. Cada área geográfica tem pelo menos uma região. Entre os exemplos de áreas geográficas estão Pacífico Asiático e Japão. Também é chamada de *geografia*. Confira também [Regiões do Azure](best-practices-availability-paired-regions.md)

### <a name="geo-replication"></a>replicação geográfica
O processo de replicação automaticamente de conteúdo como blobs, tabelas e filas dentro de um par regional. Confira também, [Replicação geográfica ativa para o Banco de Dados SQL do Azure](sql-database/sql-database-geo-replication-overview.md)

### <a name="image"></a>imagem
Um arquivo que contém o sistema operacional e a configuração de aplicativo que pode ser usada para criar qualquer quantidade de máquinas virtuais. No Azure, há dois tipos de imagens: a imagem da VM e a imagem do SO. Uma imagem de VM inclui um sistema operacional e todos os discos anexados a uma máquina virtual quando a imagem é criada. Uma imagem do SO contém apenas um sistema operacional generalizado sem qualquer configuração de disco de dados. Confira também [Navegue e selecione imagens da máquina virtual do Windows no Azure com o PowerShell ou a CLI](virtual-machines/virtual-machines-windows-cli-ps-findimage.md)

### <a name="limits"></a>limites
O número de recursos que podem ser criados, ou o parâmetro de comparação de desempenho que pode ser atingido. Geralmente, os limites estão associados a assinaturas, serviços e ofertas. Confira também [Assinatura do Azure e limite de serviços, cotas e restrições](azure-subscription-service-limits.md)

### <a name="load-balancer"></a>balanceador de carga
Um recurso que distribui o tráfego recebido entre computadores em uma rede. No Azure, um balanceador de carga distribui o tráfego para máquinas virtuais definidas em um conjunto de balanceadores de carga. Um [balanceador de carga](load-balancer/load-balancer-overview.md) pode ser voltado à Internet ou interno.

### <a name="offer"></a>oferta
Os preços, os créditos e os termos relacionados aplicáveis a uma assinatura do Azure. Confira a página [Detalhes da oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/)

### <a name="portal"></a>portal
O portal da Web seguro usado para implantar e gerenciar serviços do Azure. Há dois portais: o [portal do Azure](http://portal.azure.com/) e o [portal clássico](http://manage.windowsazure.com/). Alguns serviços estão disponíveis nos dois portais, enquanto outros só estão disponíveis em um ou no outro. O [Gráfico de disponibilidade do portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/) lista quais serviços estão disponíveis em qual portal.

### <a name="region"></a>região
Uma área dentro de uma área geográfica que não ultrapassa fronteiras nacionais e contém um ou mais datacenters. Preços, serviços regionais e tipos de oferta são expostos no nível da região. Normalmente, uma região é emparelhada com outra região, que pode estar a até várias centenas de quilômetros de distância, a fim de formar um par regional. Os pares regionais podem ser usados como um mecanismo para cenários de alta disponibilidade e de recuperação de desastres. Também é chamado normalmente de *local*. Confira também [Regiões do Azure](best-practices-availability-paired-regions.md)

### <a name="resource"></a>recurso
Um item que faz parte de sua solução do Azure. Cada serviço do Azure permite que você implante tipos diferentes de recursos, como bancos de dados ou máquinas virtuais. Confira também [Visão geral do Azure Resource Manager](resource-group-overview.md)

### <a name="resource-group"></a>grupo de recursos
Um contêiner no Resource Manager que armazena os recursos relacionados para um aplicativo. O grupo de recursos pode incluir todos os recursos de um aplicativo ou apenas os recursos que são agrupados logicamente. Você pode decidir como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Confira também [Visão geral do Azure Resource Manager](resource-group-overview.md)

### <a name="arm-template"></a>modelo do Resource Manager
Um arquivo JSON que define declarativamente um ou mais recursos do Azure e que define as dependências entre os recursos implantados. O modelo pode ser usado para implantar os recursos de forma consiste e repetida. Confira também [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md)

### <a name="resource-provider"></a>provedor de recursos
Um serviço que fornece os recursos que você pode implantar e gerenciar por meio do Resource Manager. Cada provedor de recursos oferece operações para trabalhar com os recursos implantados. Os provedores de recursos podem ser acessados por meio do portal do Azure, Azure PowerShell e vários SDKs de programação. Confira também [Visão geral do Azure Resource Manager](resource-group-overview.md)

### <a name="role"></a>função
Um meio para controlar o acesso que pode ser atribuído a usuários, grupos e serviços. As funções são capazes de executar ações, por exemplo, criar, gerenciar e ler recursos do Azure. Confira também [RBAC: funções internas](active-directory/role-based-access-built-in-roles.md)

### <a name="sla"></a>SLA (contrato de nível de serviço)
O contrato que descreve os compromissos da Microsoft em relação ao tempo de atividade e à conectividade. Cada serviço do Azure tem um SLA específico. Confira também [Contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/)

### <a name="storage-account"></a>conta de armazenamento
Uma conta de armazenamento que fornece acesso aos serviços de Blob, Fila, Tabela e Arquivo do Azure no Armazenamento do Azure. Sua conta de armazenamento fornece um namespace exclusivo para os objetos de dados de Armazenamento do Azure. Confira também [About Azure storage accounts](storage/storage-create-storage-account.md) (Sobre as contas de armazenamento do Azure)

### <a name="subscription"></a>assinatura
Um contrato do cliente com a Microsoft que permite a obtenção dos serviços do Azure. Os preços da assinatura e os termos relacionados são governados pela oferta escolhida para a assinatura. Confira [Contrato de Assinatura do Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/). Confira também [Como as assinaturas do Azure estão associadas ao Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)

### <a name="tag"></a>marcação
Um termo de indexação que permite a classificação dos recursos de acordo com suas necessidades de gerenciamento ou cobrança. Use as marcas quando você tiver um conjunto complexo de grupos de recursos e recursos, e precisar visualizar os ativos da maneira que fizer mais sentido. Por exemplo, você pode marcar os recursos que servem para uma função semelhante em sua organização ou pertencem ao mesmo departamento. Confira também [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md)

### <a name="update-domain"></a>domínio de atualização
A coleção de máquinas virtuais em um conjunto de disponibilidade que são atualizadas ao mesmo tempo. As máquinas virtuais no mesmo domínio de atualização são reiniciadas juntas durante a manutenção planejada. O Azure nunca reinicia mais de um domínio de atualização por vez. Isso também é chamado de domínio de upgrade. Confira também [Gerenciar a disponibilidade de máquinas virtuais do Windows](virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gerenciar a disponibilidade de máquinas virtuais do Linux](virtual-machines/virtual-machines-linux-manage-availability.md)

### <a name="vm"></a>máquina virtual
A implementação de software de um computador físico que executa um sistema operacional. Diversas máquinas virtuais podem ser executadas simultaneamente no mesmo hardware. No Azure, as máquinas virtuais estão disponíveis em vários tamanhos. Confira também [Documentação sobre Máquinas Virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)

### <a name="vm-extension"></a>extensão da máquina virtual
Um recurso que implementa comportamentos ou recursos que ajudam outros programas a funcionar, ou que fornecem a capacidade de interação com um computador em execução. Por exemplo, você pode usar a extensão de Acesso da Máquina Virtual para redefinir ou modificar valores de acesso remoto em uma máquina virtual do Azure. Confira também [Sobre os recursos e extensões de máquina virtual (Windows)](virtual-machines/virtual-machines-windows-extensions-features.md) ou [Sobre os recursos e extensões de máquina virtual (Linux)](virtual-machines/virtual-machines-linux-extensions-features.md)

### <a name="vnet"></a>rede virtual
Uma rede que fornece conectividade entre seus recursos do Azure que estão isolados de todos os outros locatários do Azure. Ela pode ser conectada a outras redes virtuais do Azure por meio de um [Gateway de VPN do Azure](vpn-gateway/vpn-gateway-about-vpngateways.md) e à sua rede local usando [várias opções](./vpn-gateway/vpn-gateway-cross-premises-options.md). Você pode controlar os blocos de endereços IP, as configurações de DNS, as políticas de segurança e as tabelas de rotas na rede. Confira também [Visão geral da rede virtual](virtual-network/virtual-networks-overview.md)

### **Consulte também**
* [Introdução ao Azure](https://azure.microsoft.com/get-started/)
* [Centro de Recurso de Nuvem](https://azure.microsoft.com/resources/)
* [Azure para seus aplicativos de negócios](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure em seu datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/)

<!---HONumber=AcomDC_0803_2016-->