---
title: Controle de acesso IP e suporte ao firewall do Azure Cosmos DB | Microsoft Docs
description: Saiba como usar políticas de controle de acesso de IP para suporte ao firewall em contas de banco de dados do Azure Cosmos DB.
keywords: Controle de acesso de IP, suporte ao firewall
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sngun
ms.openlocfilehash: e67fc5e00a638f116a69dbb36c60cf183cbde808
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857823"
---
# <a name="azure-cosmos-db-firewall-support"></a>Suporte ao firewall do Azure Cosmos DB
Para proteger os dados armazenados em uma conta de banco de dados do Azure Cosmos DB, o Azure Cosmos DB forneceu suporte para um [modelo de autorização](https://msdn.microsoft.com/library/azure/dn783368.aspx) baseado em segredo que utiliza um HMAC (código de autenticação de mensagem baseado em hash) forte. Agora, além do modelo de autorização baseado em segredo, o Azure Cosmos DB dá suporte a controles de acesso baseados em IP controlados por política para suporte ao firewall de entrada. Esse modelo é semelhante às regras de firewall de um sistema de banco de dados tradicional e fornece um nível adicional de segurança à conta de banco de dados do Azure Cosmos DB. Com esse modelo, agora você pode configurar uma conta de banco de dados do Azure Cosmos DB para que ela possa ser acessada somente em um conjunto aprovado de computadores e/ou serviços de nuvem. O acesso aos recursos do Azure Cosmos DB nesses conjuntos aprovados de computadores e serviços ainda exige que o chamador apresente um token de autorização válido.

> [!NOTE]
> Atualmente, o suporte ao firewall está disponível para as contas de API do Mongo e API do SQL do Azure Cosmos DB. A capacidade de configurar firewalls para outras APIs e nuvens soberanas, como Azure Alemanha ou Azure Governamental, estará disponível em breve. Se você estiver planejando configurar a ACL do ponto de extremidade de serviço para a conta do Azure Cosmos DB que tenha um firewall IP configurado existente, anote a configuração do firewall, remova o firewall IP e configure a ACL do ponto de extremidade de serviço. Depois de configurar o ponto de extremidade de serviço, você poderá habilitar novamente o firewall IP, se necessário.

## <a name="ip-access-control-overview"></a>Visão geral do controle de acesso de IP
Por padrão, uma conta de banco de dados do Azure Cosmos DB é acessível pela Internet pública, desde que a solicitação venha acompanhada por um token de autorização válido. Para configurar o controle de acesso baseado em política IP, o usuário deve fornecer o conjunto de endereços IP, ou intervalos de endereços IP, no formato CIDR a serem incluídos como a lista de permissões de IPs de clientes para uma conta de banco de dados especificada. Quando essa configuração é aplicada, todas as solicitações provenientes de computadores fora dessa lista de permissão serão bloqueadas pelo servidor.  O fluxo de processamento de conexão para o controle de acesso baseado em IP está descrito no diagrama a seguir:

![Diagrama mostrando o processo de conexão para controle de acesso baseado em IP](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> Configuração da política de controle de acesso de IP
A política de controle de acesso por IP pode ser definida no Portal do Azure ou programaticamente na [CLI do Azure](cli-samples.md), no [Azure PowerShell](powershell-samples.md) ou na [API REST](/rest/api/cosmos-db/) atualizando a propriedade **ipRangeFilter**. 

Para definir a política de controle de acesso IP no portal do Azure, navegue até a página da conta do Azure Cosmos DB, clique em **Firewall e redes virtuais** no menu de navegação, altere o valor de **Permitir acesso de** para **Redes selecionadas** e, em seguida, clique em **Salvar**. 

![Captura de tela mostrando como abrir a página do Firewall no Portal do Azure](./media/firewall-support/azure-portal-firewall.png)

Quando o controle de acesso IP estiver ativado, o portal fornecerá a capacidade de especificar endereços IP e intervalos, bem como opções para permitir o acesso a outros serviços do Azure e ao Portal do Azure. Informações adicionais sobre essas opções são fornecidas nas seções a seguir.

> [!NOTE]
> Ao habilitar uma política de controle de acesso de IP para sua conta de banco de dados do Azure Cosmos DB, todo o acesso à conta de banco de dados do Azure Cosmos DB proveniente de computadores fora da lista de permissões configurada de intervalos de endereços IP é bloqueado. Em virtude nesse modelo, a pesquisa da operação do plano de dados a partir do portal também será bloqueada para garantir a integridade do controle de acesso.

## <a name="connections-from-the-azure-portal"></a>Conexões do portal do Azure 

Quando você habilita uma política de controle de acesso IP de forma programática, você precisa adicionar o endereço IP do portal do Azure à propriedade **ipRangeFilter** para manter o acesso. Os endereços IP do portal são:

|Região|Endereço IP|
|------|----------|
|Todas as regiões, exceto aquelas especificadas abaixo|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|Alemanha|51.4.229.218|
|China|139.217.8.252|
|Gov dos EUA|52.244.48.71|

O acesso ao Portal do Azure é habilitado por padrão, quando você altera a configuração do Firewall para **Redes Selecionadas** no Portal do Azure. 

![Captura de tela mostrando como habilitar o acesso ao portal do Azure](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-global-azure-datacenters-or-azure-paas-services"></a>Conexões de datacenters Globais do Azure ou serviços de PaaS do Azure
No Azure, os serviços de PaaS, como o Azure Stream Analytics, o Azure Functions e o Serviço de Aplicativo do Azure, são usados em conjunto com o Azure Cosmos DB. Para habilitar o acesso à conta do banco de dados do Microsoft Azure Cosmos DB desses serviços cujos endereços IP não estão prontamente disponíveis, adicione o endereço IP de 0.0.0.0 à lista permitida de endereços IP associados à sua conta do banco de dados do Microsoft Azure Cosmos DB programaticamente. 

O acesso às conexões de datacenters globais do Azure é habilitado por padrão quando você altera a configuração do Firewall para **Redes selecionadas** no portal do Azure. 

![Captura de tela mostrando como abrir a página do Firewall no Portal do Azure](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>Conexões do seu IP atual

Para simplificar o desenvolvimento, o portal do Azure ajuda você a identificar e adicionar o IP de seu computador cliente à lista de permissões, de forma que os aplicativos em execução no computador possam acessar a conta do Azure Cosmos DB. O endereço IP cliente aqui é detectado como visto pelo portal. Pode ser o endereço IP cliente de seu computador, mas também pode ser o endereço IP de seu gateway de rede. Não se esqueça de removê-lo antes de passar para produção.

Para habilitar o IP atual, selecione **Adicionar meu IP atual**, que adiciona o IP atual à lista de IPs e, em seguida, clique em **Salvar**.

![Captura de tela mostrando como definir as configurações de firewall para o IP atual](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>Conexões dos serviços de nuvem
No Azure, os serviços de nuvem são uma maneira muito comum de hospedar a lógica do serviço de camada intermediária usando o Azure Cosmos DB. Para habilitar o acesso a uma conta de banco de dados do Azure Cosmos DB em um serviço de nuvem, o endereço IP público do serviço de nuvem deve ser adicionado à lista de permissões de endereços IP associados à sua conta de banco de dados do Azure Cosmos DB com a [configuração da política de controle de acesso de IP](#configure-ip-policy). Isso garante que todas as instâncias de função dos serviços de nuvem têm acesso à sua conta de banco de dados do Azure Cosmos DB. Você pode recuperar endereços IP de seus serviços de nuvem no Portal do Azure, conforme mostra a captura de tela a seguir:

![Captura de tela mostrando o endereço IP público de um serviço de nuvem exibido no Portal do Azure](./media/firewall-support/public-ip-addresses.png)

Quando você escala horizontalmente seu serviço de nuvem adicionando outras instâncias de função, essas novas instâncias terão acesso automaticamente à conta de banco de dados do Azure Cosmos DB, desde que façam parte do mesmo serviço de nuvem.

## <a name="connections-from-virtual-machines"></a>Conexões de máquinas virtuais
[Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) também podem ser usados para hospedar serviços de camada intermediária usando o Azure Cosmos DB.  Para configurar a conta de banco de dados do Azure Cosmos DB para permitir o acesso em máquinas virtuais, endereços IP públicos da máquina virtual e/ou conjuntos de dimensionamento de máquinas virtuais devem ser configurados como um dos endereços IP permitidos da conta de banco de dados do Azure Cosmos DB com a [configuração da política de controle de acesso de IP](#configure-ip-policy). Você pode recuperar endereços IP de máquinas virtuais no Portal do Azure, conforme mostra a captura de tela a seguir.

![Captura de tela mostrando o endereço IP público de uma máquina virtual exibida no Portal do Azure](./media/firewall-support/public-ip-addresses-dns.png)

Quando você adiciona outras instâncias de máquina virtual ao grupo, elas recebem automaticamente o acesso à sua conta de banco de dados do Azure Cosmos DB.

## <a name="connections-from-the-internet"></a>Conexões pela Internet
Quando você acessa uma conta de banco de dados do Azure Cosmos DB em um computador na Internet, o endereço IP do cliente ou o intervalo de endereços IP do computador deve ser adicionado à lista de permissões de endereços IP da conta de banco de dados do Azure Cosmos DB. 

## <a name="using-azure-resource-manager-template-to-set-up-the-ip-access-control"></a>Como usar o Modelo do Azure Resource Manager para configurar o controle de acesso IP

Adicione o JSON a seguir a seu modelo para configurar o controle de acesso IP. O modelo do Resource Manager para uma conta terá o atributo ipRangeFilter, que é a lista de intervalos IP, que deve estar na lista de permissões.

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
     "databaseAccountOfferType": "Standard",
     "name": "[parameters('databaseAccountName')]",
     "ipRangeFilter":"10.0.0.1,10.0.0.2,183.240.196.255"
   }
   }
```

## <a name="troubleshooting-the-ip-access-control-policy"></a>Solução de problemas da política de controle de acesso de IP
### <a name="portal-operations"></a>Operações no Portal
Ao habilitar uma política de controle de acesso de IP para sua conta de banco de dados do Azure Cosmos DB, todo o acesso à conta de banco de dados do Azure Cosmos DB proveniente de computadores fora da lista de permissões configurada de intervalos de endereços IP é bloqueado. Portanto se você quiser habilitar as operações do plano de dados do portal, como navegar em contêineres e consultar documentos, será necessário permitir explicitamente o acesso ao portal do Azure usando a página **Firewall** no portal. 

### <a name="sdk--rest-api"></a>SDK e API Rest
Por motivos de segurança, o acesso via SDK ou API REST proveniente de máquinas que não estão na lista de permissão retornará um resposta genérica 404 Não Encontrado sem detalhes adicionais. Verifique a lista de IPs permitidos configurada para a conta de banco de dados do Azure Cosmos DB para garantir que a configuração de política correta seja aplicada à sua conta de banco de dados do Azure Cosmos DB.

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre dicas de desempenho relacionadas à rede, consulte [Dicas de desempenho](performance-tips.md).

