---
title: Suporte ao firewall do DocumentDB | Microsoft Docs
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
ms.date: 10/17/2016
ms.author: ankshah; kraman
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eb3c5c2adbaedc4bfb1e68f26b88079aeabe50f5


---
# <a name="documentdb-firewall-support"></a>Suporte ao firewall do DocumentDB
Para proteger os dados armazenados em uma conta de banco de dados do DocumentDB do Azure, o DocumentDB forneceu suporte para um segredo com base no [modelo de autorização](https://msdn.microsoft.com/library/azure/dn783368.aspx) que utiliza um HMAC forte (código de autenticação de mensagem com base em Hash). Agora, além do modelo de autorização baseado em segredo, o DocumentDB dá suporte a controles de acesso com base em IP conduzidos por política para suporte de firewall de entrada. Esse modelo é muito semelhante às regras de firewall de um sistema de banco de dados tradicional, e fornece um nível adicional de segurança à conta de banco de dados do DocumentDB. Com esse modelo, agora você pode configurar uma conta de banco de dados do DocumentDB para que possa ser acessada somente a partir de um conjunto aprovado de máquinas e/ou serviços de nuvem. O acesso aos recursos do DocumentDB a partir desses conjuntos aprovados de máquinas e serviços ainda exige que o chamador apresente um token de autorização válido.

## <a name="ip-access-control-overview"></a>Visão geral do controle de acesso de IP
Por padrão, uma conta de banco de dados do DocumentDB é acessível pela internet pública contanto que a solicitação venha acompanhada por um token de autorização válido. Para configurar o controle de acesso baseado em política IP, o usuário deve fornecer o conjunto de endereços IP, ou intervalos de endereços IP, no formato CIDR a serem incluídos como a lista de permissões de IPs de clientes para uma conta de banco de dados especificada. Quando essa configuração é aplicada, todas as solicitações provenientes de computadores fora dessa lista de permissão serão bloqueados pelo servidor.  O fluxo de processamento de conexão para o controle de acesso baseado em IP está descrito no diagrama a seguir.

![Diagrama mostrando o processo de conexão para controle de acesso baseado em IP](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Conexões dos serviços de nuvem
No Azure, os serviços de nuvem são uma maneira muito comum de hospedar a lógica do serviço de camada intermediária usando o DocumentDB. Para habilitar o acesso a uma conta de banco de dados do DocumentDB de um serviço de nuvem, o endereço IP público do serviço de nuvem deve ser adicionado à lista de permissão de endereços IP associados à sua conta de banco de dados do DocumentDB [entrando em contato com o suporte do Azure](#configure-ip-policy).  Isso garante que todas as instâncias de função dos serviços de nuvem tenham acesso à sua conta de banco de dados do DocumentDB. Você pode recuperar endereços IP de seus serviços de nuvem no Portal do Azure, conforme mostra a captura de tela a seguir. 

![Captura de tela mostrando o endereço IP público de um serviço de nuvem exibido no Portal do Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

Quando você escala horizontalmente seu serviço de nuvem adicionando outras instâncias de função, essas novas instâncias terão acesso automático à conta do banco de dados do DocumentDB desde que façam parte do mesmo serviço de nuvem.

## <a name="connections-from-virtual-machines"></a>Conexões de máquinas virtuais
[Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) também podem ser usados para hospedar serviços de camada intermediária usando o DocumentDB.  Para configurar a conta de banco de dados do DocumentDB a fim de permitir o acesso de máquinas virtuais, os endereços IP públicos da máquina virtual e/ou dos conjuntos de dimensionamento de máquinas virtuais devem ser configurados como um dos endereços IP de sua conta de banco de dados do DocumentDB [entrando em contato com o suporte do Azure](#configure-ip-policy). Você pode recuperar endereços IP de máquinas virtuais no Portal do Azure, conforme mostra a captura de tela a seguir.

![Captura de tela mostrando o endereço IP público de uma máquina virtual exibida no Portal do Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

Quando você adiciona outras instâncias de máquina virtual ao grupo, elas recebem automaticamente o acesso à sua conta de banco de dados do DocumentDB.

## <a name="connections-from-the-internet"></a>Conexões pela Internet
Quando você acessa uma conta de banco de dados do DocumentDB de um computador na Internet, o endereço IP do cliente, ou o intervalo de endereços IP da máquina, deve ser adicionado à lista de permissões de endereços IP para a conta de banco de dados do DocumentDB. 

## <a name="a-idconfigure-ip-policya-configuring-the-ip-access-control-policy"></a><a id="configure-ip-policy"></a> Configuração da política de controle de acesso de IP
Use o Portal do Azure para registrar uma solicitação ao [Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a fim de habilitar a política de controle de acesso de IP em sua conta de banco de dados.

1. Na folha [Ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), selecione **Nova solicitação de suporte**.
2. Na folha **Nova solicitação de suporte**, selecione **Noções Básicas**.
3. Na folha **Noções Básicas**, selecione o seguinte:
   * **Tipo de problema**: cota
   * **Assinatura**: a assinatura associada à conta na qual você deseja adicionar a política de controle de acesso de IP.
   * **Tipo de cota**: DocumentDB
   * **Plano de suporte**: suporte a cota - incluído.
4. Na folha **Problema**, faça o seguinte:
   * **Gravidade**: selecione C - impacto mínimo
   * **Detalhes**: copie o texto a seguir na caixa e inclua os nomes de suas contas e endereços IPs: "Gostaria de habilitar o suporte do firewall para minha conta de banco de dados do DocumentDB. Conta de banco de dados: *incluem os nomes de conta*. Intervalos/endereços IP permitidos: *incluem endereço/intervalo IP no formato CIDR, por exemplo, 13.91.6.132, 13.91.6.1/24*."
   * Clique em **Próximo**. 
5. Na folha **Informações de contato**, preencha seus detalhes de contato e clique em **Criar**. 

Depois que a solicitação for recebida, o controle de acesso de IP deve ser habilitado dentro de 24 horas. Você será notificado quando a solicitação estiver concluída.

![Captura de tela das folhas Ajuda + suporte](./media/documentdb-firewall-support/documentdb-firewall-support-request-access.png)

![Captura de tela da folha Problema](./media/documentdb-firewall-support/documentdb-firewall-support-request-access-ticket.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>Solução de problemas da política de controle de acesso de IP
### <a name="portal-operations"></a>Operações no Portal
Ao habilitar uma política de controle de acesso de IP para sua conta de banco de dados do DocumentDB, todo acesso à sua conta de banco de dados do DocumentDB proveniente de computadores fora da lista de permissão configurada de intervalos de endereços IP é bloqueado. Em virtude nesse modelo, a pesquisa da operação do plano de dados a partir do portal também será bloqueada para garantir a integridade do controle de acesso. 

### <a name="sdk--rest-api"></a>SDK e API Rest
Por motivos de segurança, o acesso via SDK ou API REST proveniente de máquinas que não estão na lista de permissão retornará um resposta genérica 404 Não Encontrado sem detalhes adicionais. Verifique a lista de IPs permitidos configurada para sua conta de banco de dados do DocumentDB a fim de assegurar que a configuração de política correta seja aplicada à sua conta de banco de dados do DocumentDB.

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre dicas de desempenho relacionadas à rede, consulte [Dicas de desempenho](documentdb-performance-tips.md).




<!--HONumber=Nov16_HO3-->


