---
title: Controle de acesso IP e suporte ao firewall do Azure DocumentDB | Microsoft Docs
description: "Saiba como usar políticas de controle de acesso de IP para suporte ao firewall em contas de banco de dados do DocumentDB do Azure."
keywords: Controle de acesso de IP, suporte ao firewall
services: documentdb
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2017
ms.author: ankshah
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 7b4f397e4abb66933f05e5d5ba4afeb8c2c00d5f
ms.lasthandoff: 04/06/2017


---
# <a name="documentdb-firewall-support"></a>Suporte ao firewall do DocumentDB
Para proteger os dados armazenados em uma conta de banco de dados do DocumentDB do Azure, o DocumentDB forneceu suporte para um segredo com base no [modelo de autorização](https://msdn.microsoft.com/library/azure/dn783368.aspx) que utiliza um HMAC forte (código de autenticação de mensagem com base em Hash). Agora, além do modelo de autorização baseado em segredo, o DocumentDB dá suporte a controles de acesso com base em IP conduzidos por política para suporte de firewall de entrada. Esse modelo é muito semelhante às regras de firewall de um sistema de banco de dados tradicional, e fornece um nível adicional de segurança à conta de banco de dados do DocumentDB. Com esse modelo, agora você pode configurar uma conta de banco de dados do DocumentDB para que possa ser acessada somente a partir de um conjunto aprovado de máquinas e/ou serviços de nuvem. O acesso aos recursos do DocumentDB a partir desses conjuntos aprovados de máquinas e serviços ainda exige que o chamador apresente um token de autorização válido.

## <a name="ip-access-control-overview"></a>Visão geral do controle de acesso de IP
Por padrão, uma conta de banco de dados do DocumentDB é acessível pela internet pública contanto que a solicitação venha acompanhada por um token de autorização válido. Para configurar o controle de acesso baseado em política IP, o usuário deve fornecer o conjunto de endereços IP, ou intervalos de endereços IP, no formato CIDR a serem incluídos como a lista de permissões de IPs de clientes para uma conta de banco de dados especificada. Quando essa configuração é aplicada, todas as solicitações provenientes de computadores fora dessa lista de permissão serão bloqueados pelo servidor.  O fluxo de processamento de conexão para o controle de acesso baseado em IP está descrito no diagrama a seguir.

![Diagrama mostrando o processo de conexão para controle de acesso baseado em IP](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Conexões dos serviços de nuvem
No Azure, os serviços de nuvem são uma maneira muito comum de hospedar a lógica do serviço de camada intermediária usando o DocumentDB. Para habilitar o acesso a uma conta de banco de dados do DocumentDB de um serviço de nuvem, o endereço IP público do serviço de nuvem deve ser adicionado à lista de permissão de endereços IP associados à sua conta de banco de dados do DocumentDB [configurando a política de controle de acesso de IP](#configure-ip-policy).  Isso garante que todas as instâncias de função dos serviços de nuvem tenham acesso à sua conta de banco de dados do DocumentDB. Você pode recuperar endereços IP de seus serviços de nuvem no Portal do Azure, conforme mostra a captura de tela a seguir.

![Captura de tela mostrando o endereço IP público de um serviço de nuvem exibido no Portal do Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

Quando você escala horizontalmente seu serviço de nuvem adicionando outras instâncias de função, essas novas instâncias terão acesso automático à conta do banco de dados do DocumentDB desde que façam parte do mesmo serviço de nuvem.

## <a name="connections-from-virtual-machines"></a>Conexões de máquinas virtuais
[Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) também podem ser usados para hospedar serviços de camada intermediária usando o DocumentDB.  Para configurar a conta de banco de dados do DocumentDB a fim de permitir o acesso de máquinas virtuais, os endereços IP públicos da máquina virtual e/ou dos conjuntos de dimensionamento de máquinas virtuais devem ser configurados como um dos endereços IP de sua conta de banco de dados do DocumentDB [configurando a política de controle de acesso de IP](#configure-ip-policy). Você pode recuperar endereços IP de máquinas virtuais no Portal do Azure, conforme mostra a captura de tela a seguir.

![Captura de tela mostrando o endereço IP público de uma máquina virtual exibida no Portal do Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

Quando você adiciona outras instâncias de máquina virtual ao grupo, elas recebem automaticamente o acesso à sua conta de banco de dados do DocumentDB.

## <a name="connections-from-the-internet"></a>Conexões pela Internet
Quando você acessa uma conta de banco de dados do DocumentDB de um computador na Internet, o endereço IP do cliente, ou o intervalo de endereços IP da máquina, deve ser adicionado à lista de permissões de endereços IP para a conta de banco de dados do DocumentDB. 

## <a id="configure-ip-policy"></a> Configuração da política de controle de acesso de IP
A política de controle de acesso por IP pode ser definida no Portal do Azure ou programaticamente na [CLI do Azure](documentdb-automation-resource-manager-cli.md), no [Azure PowerShell](documentdb-manage-account-with-powershell.md) ou na [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) atualizando a propriedade `ipRangeFilter`. Os intervalos/endereços IP devem ser separados por vírgula e não devem conter espaços. Exemplo: "13.91.6.132,13.91.6.1/24". Ao atualizar sua conta do banco de dados por meio desses métodos, preencha todas as propriedades para evitar a restauração para as configurações padrão.

> [!NOTE]
> Ao habilitar uma política de controle de acesso de IP para sua conta de banco de dados do DocumentDB, todo acesso à sua conta de banco de dados do DocumentDB proveniente de computadores fora da lista de permissão configurada de intervalos de endereços IP é bloqueado. Em virtude nesse modelo, a pesquisa da operação do plano de dados a partir do portal também será bloqueada para garantir a integridade do controle de acesso.

Para simplificar o desenvolvimento, o Portal do Azure ajuda a identificar e adicionar o IP de seu computador cliente à lista de permissões, para que os aplicativos em execução em seu computador possam acessar a conta do DocumentDB. Observe que o endereço IP cliente aqui é detectado como visto pelo portal. Pode ser o endereço IP cliente de seu computador, mas também pode ser o endereço IP de seu gateway de rede. Não se esqueça de removê-lo antes de passar para produção.

Para definir a política de controle de acesso por IP no Portal do Azure, navegue até a folha Conta do DocumentDB, clique em **Firewall** no menu de navegação e clique em **ATIVADO** 

![Captura de tela mostrando como abrir a folha do Firewall no Portal do Azure](./media/documentdb-firewall-support/documentdb-azure-portal-firewall.png)

No novo painel, especifique se o Portal do Azure pode acessar a conta e adicione outros endereços e intervalos conforme for apropriado, depois clique em **Salvar**.  

> [!NOTE]
> Quando você habilita uma política de controle de acesso IP, você precisa adicionar o endereço IP do Portal do Azure para manter o acesso. Os endereços IP do portal são:
> |Região|Endereço IP|
> |------|----------|
> |Todas as regiões, exceto aquelas especificadas abaixo| 104.42.195.92|
> |Alemanha|51.4.229.218|
> |China|139.217.8.252|
> |Governo dos EUA do Arizona|52.244.48.71|
>

![Captura de tela mostrando como definir as configurações de firewall no Portal do Azure](./media/documentdb-firewall-support/documentdb-azure-portal-firewall-configure.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>Solução de problemas da política de controle de acesso de IP
### <a name="portal-operations"></a>Operações no Portal
Ao habilitar uma política de controle de acesso de IP para sua conta de banco de dados do DocumentDB, todo acesso à sua conta de banco de dados do DocumentDB proveniente de computadores fora da lista de permissão configurada de intervalos de endereços IP é bloqueado. Portanto se você quiser habilitar operações no plano de dados do portal, como navegar por coleções e consultar documentos, permita explicitamente o acesso ao Portal do Azure usando a folha **Firewall** no portal. 

![Captura de tela mostrando como habilitar o acesso ao Portal do Azure](./media/documentdb-firewall-support/documentdb-azure-portal-access-firewall.png)

### <a name="sdk--rest-api"></a>SDK e API Rest
Por motivos de segurança, o acesso via SDK ou API REST proveniente de máquinas que não estão na lista de permissão retornará um resposta genérica 404 Não Encontrado sem detalhes adicionais. Verifique a lista de IPs permitidos configurada para sua conta de banco de dados do DocumentDB a fim de assegurar que a configuração de política correta seja aplicada à sua conta de banco de dados do DocumentDB.

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre dicas de desempenho relacionadas à rede, consulte [Dicas de desempenho](documentdb-performance-tips.md).


