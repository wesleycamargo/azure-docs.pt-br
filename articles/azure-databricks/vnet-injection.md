---
title: Implantar o Azure Databricks em sua rede virtual (versão prévia)
description: Este artigo descreve como implantar o Azure Databricks em sua rede virtual, também conhecida como injeção de rede virtual.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003438"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>Implantar o Azure Databricks em sua rede virtual (versão prévia)

A implantação padrão do Azure Databricks é um serviço totalmente gerenciado no Azure: todos os recursos do plano de dados, incluindo uma rede virtual (VNet), são implantados em um grupo de recurso bloqueado. Se você precisar de personalização de rede, no entanto, você pode implantar recursos do Azure Databricks em sua própria rede virtual (também chamada VNet injeção), quando permite que você:

* Conecte o Azure Databricks para outros serviços do Azure (como o armazenamento do Azure) de uma maneira mais segura usando pontos de extremidade de serviço.
* Conecte-se aos dados locais fontes para uso com o Azure Databricks, aproveitando as rotas definidas pelo usuário.
* Conectar o Azure Databricks para um dispositivo de rede virtual para inspecionar o tráfego de saída e tomar ações de acordo com a regras permitir e negar.
* Configure o Azure Databricks para usar o DNS personalizado.
* Configure regras NSG (grupo) de segurança de rede para especificar as restrições de tráfego de saída.
* Implante clusters do Azure Databricks em sua rede virtual existente.

Implantação de recursos do Azure Databricks para sua própria rede virtual também permite que você aproveite os intervalos de CIDR flexíveis (em qualquer lugar entre /16-/ 24 para a rede virtual e entre /18-/ 26 para as sub-redes).

  > [!NOTE]
  > Você não pode substituir a rede virtual para um espaço de trabalho. Se seu espaço de trabalho atual não puder acomodar o número necessário de nós de cluster ativo, crie outro espaço de trabalho em uma rede virtual maior. Siga [estas etapas de migração detalhadas](howto-regional-disaster-recovery.md#detailed-migration-steps) para copiar recursos (blocos de anotações, configurações de cluster, trabalhos) do ambiente antigo para o novo espaço de trabalho.

## <a name="virtual-network-requirements"></a>Requisitos de rede virtual

Você pode usar a interface de implantação do espaço de trabalho do Azure Databricks no portal do Azure para configurar automaticamente uma rede virtual existente com as sub-redes necessárias, o grupo de segurança de rede e configurações de lista de permissões, ou você pode usar o Azure Resource Manager modelos para configurar sua rede virtual e implantar seu espaço de trabalho.

A rede virtual que você implanta seu espaço de trabalho do Azure Databricks deve atender aos seguintes requisitos:

### <a name="location"></a>Local padrão

A rede virtual deve residir no mesmo local que o espaço de trabalho do Databricks do Azure.

### <a name="subnets"></a>Sub-redes

A rede virtual deve incluir duas sub-redes dedicadas ao Azure Databricks:

   1. Uma sub-rede privada com um grupo de segurança de rede configurada que permite a comunicação interna de cluster

   2. Uma sub-rede pública com um grupo de segurança de rede configurada que permite a comunicação com o plano de controle do Azure Databricks.

### <a name="address-space"></a>Espaço de endereço

Um bloco CIDR entre /16-/ 24 para a rede virtual e um bloco CIDR entre /18-/26 para as sub-redes públicas e privadas.

### <a name="whitelisting"></a>Inclusão na lista de permissões

Todo o tráfego de entrada e saído entre as sub-redes e o plano de controle do Azure Databricks deve estar na lista branca.

## <a name="create-an-azure-databricks-workspace"></a>Criar um workspace do Azure Databricks

Esta seção descreve como criar um espaço de trabalho do Azure Databricks no portal do Azure e implantá-lo em sua própria rede virtual existente. O Azure Databricks atualiza a rede virtual com duas novas sub-redes e grupos de segurança de rede usando intervalos de CIDR fornecidos por você, listas de permissões de entrada e o tráfego de saída da sub-rede e implanta o espaço de trabalho para a rede virtual atualizada.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma rede virtual à qual você implantará o espaço de trabalho do Databricks do Azure. Você pode usar uma rede virtual existente ou crie um novo, mas a rede virtual deve estar na mesma região que o espaço de trabalho do Databricks do Azure que você planeja criar. Um intervalo CIDR entre /16 /24 é necessário para a rede virtual.

  > [!Warning]
  > Um espaço de trabalho com uma rede virtual menor – ou seja, um intervalo de CIDR inferior – pode ficar sem endereços IP (espaço de rede) mais rapidamente do que um espaço de trabalho com uma rede virtual maior. Por exemplo, um espaço de trabalho com um/24 rede virtual e /26 sub-redes podem ter um máximo de 64 nós ativas por vez, enquanto um espaço de trabalho com um /20 rede virtual e /22 sub-redes podem hospedar um máximo de nós de 1024.

  Suas sub-redes serão criadas automaticamente quando você configura seu espaço de trabalho, e você terá a oportunidade de fornecer o intervalo CIDR para as sub-redes durante a configuração.

## <a name="configure-the-virtual-network"></a>Configurar a rede virtual

1. No portal do Azure, selecione **+ criar um recurso > Analytics > Azure Databricks** para abrir a caixa de diálogo de serviço do Azure Databricks.

2. Siga as etapas de configuração descritas na etapa 2: Criar um espaço de trabalho do Azure Databricks no guia de Introdução e selecione o espaço de trabalho de implantar o Azure Databricks em suas opções de rede Virtual.

   ![Criar serviço do Azure Databricks](./media/vnet-injection/create-databricks-service.png)

3. Selecione a rede virtual que você deseja usar.

   ![Opções de rede virtual](./media/vnet-injection/select-vnet.png)

4. Forneça os intervalos de CIDR em um bloco entre /18-/26 para duas sub-redes, dedicadas ao Azure Databricks:

   * Uma sub-rede pública será criada com um grupo de segurança de rede associado que permite a comunicação com o plano de controle do Azure Databricks.
   * Uma sub-rede privada será criada com um grupo de segurança de rede associado que permite a comunicação interna de cluster.

5. Clique em **criar** para implantar o espaço de trabalho do Databricks do Azure para a rede virtual.

## <a name="advanced-resource-manager-configurations"></a>Configurações do Gerenciador de recursos avançados

Se você quiser mais controle sobre a configuração da rede virtual – por exemplo, você deseja usar sub-redes existentes, use grupos de segurança de rede existente ou criar suas próprias regras de segurança – você pode usar os seguintes modelos do Azure Resource Manager, em vez da implantação de espaço de trabalho e a configuração de rede virtual do portal.

### <a name="all-in-one"></a>Tudo em um

Para criar uma rede virtual, os grupos de segurança de rede e espaço de trabalho do Databricks do Azure, tudo em um, use o [em um modelo para espaços de trabalho do Databricks VNet injetado](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Quando você usa esse modelo, você não precisa fazer qualquer lista de permissões de manual do tráfego de sub-rede.

### <a name="network-security-groups"></a>Grupos de segurança de rede

Para criar grupos de segurança de rede com as regras necessárias para uma rede virtual existente, use o [modelo de grupo de segurança de rede para injeção de rede virtual do Databricks](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

Quando você usa esse modelo, você não precisa fazer qualquer lista de permissões de manual do tráfego de sub-rede.

### <a name="virtual-network"></a>Rede virtual

Para criar uma rede virtual com sub-redes públicas e privadas adequadas, use o [modelo de rede Virtual para injeção de rede virtual do Databricks](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Se você usar esse modelo sem usar também o modelo de grupos de segurança de rede, você deve adicionar manualmente regras de lista de permissões para os grupos de segurança de rede que você pode usar com a rede virtual.

### <a name="azure-databricks-workspace"></a>Espaço de trabalho do Databricks do Azure

Para implantar um espaço de trabalho do Databricks do Azure para uma rede virtual existente que tenha sub-redes públicas e privadas e grupos de segurança de rede configurado corretamente já configurados, use o [modelo de espaço de trabalho para injeção de rede virtual do Databricks](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Se você usar esse modelo sem usar também o modelo de grupos de segurança de rede, você deve adicionar manualmente regras de lista de permissões para os grupos de segurança de rede que você pode usar com a rede virtual.

## <a name="whitelisting-subnet-traffic"></a>Tráfego de sub-rede da lista de permissões

Se você não usar o [portal do Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) ou [modelos do Azure Resource Manager](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) para criar grupos de segurança de sua rede, você deve manualmente colocar o seguinte tráfego em suas sub-redes.

|Direção|Protocolo|Fonte|Porta de origem|Destino|Porta de destino|
|---------|--------|------|-----------|-----------|----------------|
|Entrada|\*|VirtualNetwork|\*|\*|\*|
|Entrada|\*|IP de NAT do plano de controle|\*|\*|22|
|Entrada|\*|IP de NAT do plano de controle|\*|\*|5557|
|Saída|\*|\*|\*|IP do aplicativo Web|\*|
|Saída|\*|\*|\*|SQL (marca de serviço)|\*|
|Saída|\*|\*|\*|Armazenamento (marca de serviço)|\*|
|Saída|\*|\*|\*|VirtualNetwork|\*|

Tráfego de sub-rede de lista de permissões usando o seguinte IP endereços. Para o SQL (metastore) e armazenamento (armazenamento de artefato e de log), você deve usar o Sql e armazenamento [marcas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

|Região do Azure Databricks|Serviço|IP público|
|-----------------------|-------|---------|
|Leste dos EUA|Plano de controle de NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Leste dos EUA 2|Plano de controle de NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Centro-Norte dos EUA|Plano de controle de NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Centro dos EUA|Plano de controle de NAT </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Centro-Sul dos Estados Unidos|Plano de controle de NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Oeste dos EUA|Plano de controle de NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Oeste dos EUA 2|Plano de controle de NAT </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Canadá Central|Plano de controle de NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Leste do Canadá|Plano de controle de NAT </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Oeste do Reino Unido|Plano de controle de NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Sul do Reino Unido|Plano de controle de NAT </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europa Ocidental|Plano de controle de NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Norte da Europa|Plano de controle de NAT </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Índia Central|Plano de controle de NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Sul da Índia|Plano de controle de NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Índia Ocidental|Plano de controle de NAT </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Sudeste da Ásia|Plano de controle de NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Ásia Oriental|Plano de controle de NAT </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Leste da Austrália|Plano de controle de NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Sudeste da Austrália|Plano de controle de NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrália Central|Plano de controle de NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Austrália Central 2|Plano de controle de NAT </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Leste do Japão|Plano de controle de NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Oeste do Japão|Plano de controle de NAT </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>solução de problemas

### <a name="workspace-launch-errors"></a>Erros de inicialização do espaço de trabalho

Iniciar um espaço de trabalho em uma rede virtual personalizada falha no Azure Databricks sinal na tela com o seguinte erro: **"Encontramos um erro ao criar seu espaço de trabalho. Verifique se que a configuração de rede personalizada está correta e tente novamente."**

Esse erro é causado por uma configuração de rede que não atende aos requisitos. Confirme se você seguiu as instruções neste tópico, quando você criou o espaço de trabalho.

### <a name="cluster-creation-errors"></a>Erros de criação de cluster

**Instâncias inacessíveis: Não havia recursos acessíveis via SSH.**

Possível causa: o tráfego para os operadores do plano de controle está bloqueado. Corrigi, garantindo que as regras de segurança de entrada atendam aos requisitos. Se você estiver implantando em uma rede virtual existente conectada à sua rede local, examine a configuração usando as informações fornecidas ao conectar-se o seu espaço de trabalho do Databricks do Azure à sua rede local.

**Falha ao iniciar inesperado: Foi encontrado um erro inesperado durante a configuração do cluster. Tente novamente e entre em contato com o Azure Databricks se o problema persistir. Mensagem de erro interno: Tempo limite ao colocar o nó.**

Possível causa: o tráfego de trabalhos para pontos de extremidade do armazenamento do Azure é bloqueado. Corrigi, garantindo que as regras de segurança de saída atendem aos requisitos. Se você estiver usando servidores DNS personalizados, também Verifique o status dos servidores DNS na sua rede virtual.

**Falha de inicialização do provedor de nuvem: Foi encontrado um erro de provedor de nuvem durante a configuração do cluster. Consulte o guia do Databricks do Azure para obter mais informações. Código de erro do Azure: AuthorizationFailed/InvalidResourceReference.**

Possível causa: a rede virtual ou sub-redes não existem mais. Verifique se a rede virtual e sub-redes existem.

**Cluster encerrada. Motivo: Falha de inicialização do Spark: Spark não pôde iniciar no tempo. Esse problema pode ser causado por um metastore do Hive com defeito, configurações inválidas do Spark ou scripts de inicialização com defeito. Consulte os logs de driver do Spark para solucionar esse problema e entre em contato com o Databricks se o problema persistir. Mensagem de erro interno: Spark falhou ao iniciar: Driver falhou ao iniciar no tempo.**

Possível causa: Contêiner não pode se comunicar com a instância de hospedagem ou conta de armazenamento DBFS. Corrigi, adicionando uma rota personalizada para as sub-redes para a conta de armazenamento DBFS com o próximo nó que está sendo Internet.

### <a name="notebook-command-errors"></a>Erros de comandos do bloco de anotações

**Comando não está respondendo**

Possível causa: a comunicação de trabalho para trabalho está bloqueada. Corrigi, certificando-se que as regras de segurança de entrada atender aos requisitos.

**Fluxo de trabalho do notebook falhará com a exceção: com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

Possível causa: o tráfego de trabalhos para o aplicativo Web do Azure Databricks está bloqueado. Corrigi, certificando-se que as regras de segurança de saída atender aos requisitos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar dados usando o Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
