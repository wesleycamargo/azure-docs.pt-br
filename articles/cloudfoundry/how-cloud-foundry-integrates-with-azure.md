---
title: Como o Cloud Foundry se integra com o Azure | Microsoft Docs
description: Descreve como os serviços do Cloud Foundry podem usar os serviços do Azure para aprimorar a experiência empresarial
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 1f4afbe1849210c55c392d014449224f2fe97b04
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658815"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrar o Cloud Foundry com o Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) é uma plataforma de PaaS em execução na plataforma de IaaS de provedores de nuvem. Ele oferece uma experiência de implantação de aplicativo consistente entre provedores na nuvem. Além disso, ele também pode integrar vários serviços do Azure, com alta disponibilidade, escalabilidade e redução de custos de nível corporativo.
Existem [6 subsistemas de Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), que podem escalonados com flexibilidade on-line, incluindo: roteamento, autenticação, gerenciamento de ciclo de vida do aplicativo, serviços de gerenciamento, monitoramento e de mensagens. Para cada um dos subsistemas, você pode configurar o Cloud Foundry para utilizar serviços correspondentes do Azure. 

![Cloud Foundry na arquitetura de integração do Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Alta disponibilidade e escalabilidade
### <a name="managed-disk"></a>Disco Gerenciado
A Bosh utiliza o Azure CPI (Interface de provedor de nuvem) para a criação de discos e rotinas de exclusão. Por padrão, são usados discos não gerenciados. Ele precisa que o cliente crie contas de armazenamento manualmente e configure as contas nos arquivos de manifesto do CF. Isso é devido à limitação do número de discos por conta de armazenamento.
Agora que o [Disco Gerenciado](https://azure.microsoft.com/services/managed-disks/) está disponível, ele oferece um armazenamento de disco gerenciado seguro e confiável para máquinas virtuais. Cliente não precisa mais lidar com a conta de armazenamento para escalonamento e alta disponibilidade. O Azure organiza os discos automaticamente. Seja uma implantação nova ou existente, o Azure CPI tratará da criação ou migração do disco gerenciado durante uma implantação do CF. Ele é compatível com o PCF 1.11. Você também pode explorar as [diretrizes do Disco Gerenciado](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) do software livre Cloud Foundry como referência. 
### <a name="availability-zone-"></a>Zona de disponibilidade *
Como uma plataforma de aplicativo nativo de nuvem, o Cloud Foundry foi projetado com [nível quatro de alta disponibilidade](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Enquanto os três primeiros níveis de falhas de software podem ser tratados pelo próprio sistema CF, a tolerância a falhas na plataforma é fornecida por provedores de nuvem. Os principais componentes do CF devem ser protegidos com uma solução de alta disponibilidade de plataforma de um provedor de nuvem. Isso inclui GoRouters, Diego Brains, bancos de dados e blocos de serviço do CF. Por padrão, o [conjunto de disponibilidade do Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) é usado para tolerância a falhas entre clusters em um data center.
O boa notícia é que já foi lançada a [zona de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview ), levando a tolerância a falhas para outro nível, com redundância de baixa latência entre data centers.
A zona de disponibilidade do Azure consegue alta disponibilidade, colocando um conjunto de VMs em mais de dois data centers, com cada conjunto de VMs redundantes em relação a outros conjuntos. Se uma zona estiver inativa, os outros conjuntos estarão ainda ativos, isolados do desastre.
> [!NOTE] 
> A zona de disponibilidade do Azure não é oferecida a todas as regiões ainda, por isso, verifique o [anúncio para a lista de regiões com suporte](https://docs.microsoft.com/azure/availability-zones/az-overview) mais recente. Para abrir o software livre Cloud Foundry, verifique a [zona de disponibilidade do Azure para obter diretrizes do software livre Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Roteamento de rede
Por padrão, o balanceador de carga básico do Azure é usado para solicitações recebidas de aplicativos da API do CF, encaminhando-as para o Gorouters. Os componentes do CF como Diego Brain, MySQL e ERT também podem usar o balanceador de carga para equilibrar o tráfego para alta disponibilidade. Além disso, o Azure fornece um conjunto de soluções de balanceamento de carga totalmente gerenciado. Se você estiver procurando o término do TLS ("descarregamento de SSL") ou o processamento de camada de aplicativo de solicitação HTTP/HTTPS, considere o Gateway de Aplicativo. Para alta disponibilidade e escalabilidade de balanceamento de carga na camada 4, considere usar o balanceador de carga padrão.
### <a name="azure-application-gateway-"></a>Gateway de Aplicativo do Azure *
O [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferece vários recursos de balanceamento de carga de camada 7, incluindo descarregamento SSL, SSL de ponta a ponta, firewall do aplicativo Web, afinidade de sessão baseada em cookies e muito mais. Você pode [configurar o Gateway de Aplicativo no software livre Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Para o PCF, verifique a [notas de versão do PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) para teste POC.

### <a name="azure-standard-load-balancer-"></a>Azure Standard Load Balancer *
O Azure Load Balancer é um balanceador de carga de Camada 4. Ele é usado para distribuir o tráfego entre as instâncias de serviços em um conjunto com balanceamento de carga. A versão padrão fornece [recursos avançados](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) por cima da versão básica. Por exemplo: 1. O limite máximo do pool de back-end é aumentado de 100 para 1.000 VMs.  2. Os pontos de extremidade agora oferecem suporte a vários conjuntos de disponibilidade, em vez de um único conjunto de disponibilidade.  3. Recursos adicionais, como portas de alta disponibilidade, dados de monitoramento mais avançados, etc. Se você estiver migrando para a Zona de Disponibilidade do Azure, o balanceador de carga padrão é necessário. Para uma nova implantação, recomendamos que você inicie com o Standard Load Balancer do Azure. 

## <a name="3-authentication"></a>3. Autenticação 
[Conta de usuário e autenticação do Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) é o serviço de gerenciamento de identidade central para CF e seus vários componentes. O [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é o serviço de gerenciamento de identidades e diretório multilocatário baseado em nuvem da Microsoft. Por padrão, o UAA é usado para a autenticação do Cloud Foundry. Como uma opção avançada, o UAA também dá suporte ao Azure Active Directory como um repositório de usuário externo. Os usuários do Azure Active Directory podem acessar o Cloud Foundry usando sua identidade LDAP, sem uma conta do Cloud Foundry. Siga estas etapas para [configurar o Azure Active Directory para UAA no PCF](http://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Armazenamento de dados para o Sistema Common Language Runtime do Cloud Foundry
O Cloud Foundry oferece excelente extensibilidade para usar o blobstore do Azure ou os serviços do MySQL/PostgreSQL do Azure para armazenamento do sistema Common Language Runtime do aplicativo.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Blobstore do Azure para blobstore do controlador de nuvem do Cloud Foundry
O bobstore do Cloud Controller é um repositório de dados crítico para buildpacks, droplets, pacotes e pools de recursos. Por padrão, o servidor NFS é usado para o blobstore do Cloud Controller. Para evitar ponto único de falha, use o Armazenamento de Blobs do Azure como armazenamento externo. Confira a [documentação do Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html), para conhecer melhor o cenário, e as [opções no Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL como banco de dados do Elastic Runtime do Cloud Foundry*
O Elastic Runtime do Cloud Foundry requer dois bancos de dados de sistema principais:
#### <a name="ccdb"></a>CCDB 
O banco de dados do Cloud Controller.  O Cloud Controller fornece pontos de extremidade da API REST para os clientes acessarem o sistema. O CCDB armazena tabelas para organizações, espaços, serviços, funções de usuário e mais para o controlador da nuvem.
#### <a name="uaadb"></a>UAADB 
O banco de dados para a conta de usuário e autenticação. Ele armazena a autenticação do usuário relacionada aos dados, por exemplo, nomes de usuário e senhas criptografados.

Por padrão, um banco de dados do sistema local (MySQL) pode ser usado. Para alta disponibilidade e escala, aproveite o MySQL gerenciado do ou os serviços PostgreSQL do Azure. Aqui está a instrução de [como ativar o MySQL/PostgreSQL do Azure para CCDB, UAADB e outros bancos de dados do sistema com o software livre Cloud Foundry ](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Service Broker aberto
O agente de serviços do Azure oferece uma interface consistente para gerenciar o acesso do aplicativo aos serviços do Azure. O novo [Service Broker aberto para projetos do Azure](https://github.com/Azure/open-service-broker-azure) fornece uma maneira simples e única de fornecer serviços para aplicativos no Cloud Foundry, OpenShift e Kubernetes. Consulte o [Service Broker aberto do Azure para o bloco PCF](https://network.pivotal.io/products/azure-open-service-broker-pcf/) para obter instruções de implantação no PCF.

## <a name="6-metrics-and-logging"></a>6. Métricas e logs
O Bocal do Azure Log Analytics é um componente do Cloud Foundry que encaminha métricas do [agregador de logs firehose do Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) para o [Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/). Com o Bocal, é possível coletar, exibir e analisar a integridade do sistema e as métricas de desempenho do CF em várias implantações.
Clique [aqui](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) para saber como implantar o Bocal do Azure Log Analytics em ambientes de software livre e do Cloud Foundry e, em seguida, acessar os dados do console do OMS do Azure Log Analytics. 
> [!NOTE]
> Do PCF 2.0, as métricas de integridade BOSH para VMs são encaminhadas ao Loggregator Firehose por padrão e são integradas no console do OMS do Azure Log Analytics.

## <a name="7-cost-saving"></a>7. Redução de custos
### <a name="cost-saving-for-devtest-environments"></a>Redução de custos para ambientes de desenvolvimento/teste
#### <a name="b-series-"></a>Série B: *
Enquanto as séries de VM D e F comumente recomendadas para o ambiente de produção do Cloud Foundry, a nova [série B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) traz novas opções. As VMs expansíveis série B são ideais para cargas de trabalho que não precisam do desempenho total da CPU continuamente, como servidores web, bancos de dados pequenos e ambientes de desenvolvimento e teste. Normalmente, essas cargas de trabalho têm requisitos de desempenho expansíveis. O custo é de US$ 0,012 por hora (B1) em comparação com US$ 0,05 por hora (F1). Para mais detalhes, consulte a lista completa de [tamanhos de VM](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes-general) e [preços](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). 
#### <a name="managed-standard-disk"></a>Disco Standard gerenciado: 
Os discos Premium foram recomendados para um desempenho confiável na produção.  Com o [Disco Gerenciado](https://azure.microsoft.com/services/managed-disks/), o armazenamento standard também pode fornecer confiabilidade semelhante, com desempenho diferente. Para cargas de trabalho que não são sensíveis ao desempenho, como os ambientes de desenvolvimento/teste não críticos, os discos Standard gerenciados oferecem uma opção alternativa com menor custo.  
### <a name="cost-saving-in-general"></a>Redução de custos em geral 
#### <a name="significant-vm-cost-saving-with-reserved-instances"></a>Redução de custos significativa da VM com as Instâncias Reservadas: 
Atualmente, todas as VMs CF são cobradas usando preços "sob demanda", embora os ambientes normalmente permaneçam indefinidamente. Agora, você pode reservar a capacidade da VM em um período de 1 ou 3 anos e obter descontos de 45 a 65%. Os descontos são aplicados no sistema de cobrança, sem alterações ao seu ambiente. Para obter detalhes, consulte [como as instâncias reservadas funcionam](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Disco Premium gerenciado com tamanhos menores: 
Os discos gerenciados oferecem suporte a tamanhos menores de disco, por exemplo, P4(32 GB) e P6(64 GB) para discos standard e premium. Se você tiver cargas de trabalho pequenas, é possível reduzir o custo ao migrar discos premium standard para discos premium gerenciados.
#### <a name="utilizing-azure-first-party-services"></a>Usando os serviços internos do Azure: 
Aproveite o serviço interno do Azure e reduza os custos de administração em longo prazo, além da alta disponibilidade e confiabilidade mencionadas nas seções anteriores. 

A Pivotal lançou uma [ERT de superfície pequena](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) para clientes PCF, com componentes posicionados em apenas 4 VMs e até 2.500 instâncias de aplicativo em execução. A versão de avaliação agora está disponível por meio do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Próximas etapas
Primeiro, os recursos de integração do Azure estão disponíveis com o [software livre Cloud Foundry ](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), antes de estar disponível no Cloud Foundry da Pivotal. Os recursos marcados com * ainda não estão disponíveis por meio de PCF. A integração do Cloud Foundry com o Azure Stack também não é abordada neste documento.
Para obter suporte ao PCF nos recursos marcados com *, ou na integração do Cloud Foundry com o Azure Stack, entre em contato com seu gerente de conta Pivotal e da Microsoft para obter o status mais recente. 

