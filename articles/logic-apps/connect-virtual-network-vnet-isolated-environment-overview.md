---
title: Acessar redes virtuais do Azure a partir do Aplicativo Lógico do Azure com ISEs (Ambientes de Serviço de Integração)
description: Esta visão geral descreve como ISEs (ambientes de serviço de integração) ajudam os aplicativos lógicos a acessarem as VNETs (redes virtuais) do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 12/03/2018
ms.openlocfilehash: 5046b1012e0074e9548cad050c16eef25c00cee0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845188"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acessar recursos de rede virtual do Azure a partir do Aplicativo Lógico do Azure usando ISEs (Ambientes de Serviço de Integração)

> [!NOTE]
> Essa funcionalidade está em *versão prévia privada*. Para solicitar acesso, [crie sua solicitação para ingressar aqui](https://aka.ms/iseprivatepreview).

Às vezes, os aplicativos lógicos e as contas de integração precisam acessar recursos protegidos, como VMs (máquinas virtuais) e outros sistemas ou serviços em uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Para configurar esse acesso, é possível [criar um *ISE* (ambiente de serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) para executar os aplicativos lógicos e as contas de integração. 

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Criar um ISE implanta uma instância privada e isolada dos Aplicativos Lógicos em sua rede virtual do Azure. Essa instância privada usa recursos dedicados, como o armazenamento, sendo executada separadamente do serviço público "global" de Aplicativos Lógicos. Essa separação também ajuda a reduzir o impacto que outros locatários do Azure podem ter no desempenho do seu aplicativo, conhecido como [efeito "vizinhos barulhentos"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Esta visão geral descreve como um ISE fornece acesso direto dos aplicativos lógicos e contas de integração à rede virtual do Azure e compara as diferenças entre um ISE e o serviço global de Aplicativos Lógicos.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolado versus global

Ao criar um ISE (ambiente de serviço de integração), você seleciona uma rede virtual do Azure na qual *injeta* o ambiente. O Azure implanta uma instância privada do serviço de Aplicativos Lógicos na rede virtual. Essa ação cria um ambiente isolado em que é possível criar e executar os aplicativos lógicos em recursos dedicados. Ao criar um aplicativo lógico, é possível selecionar esse ambiente como o local do aplicativo, o que também fornece acesso direto do aplicativo lógico aos recursos na rede virtual. 

Os aplicativos lógicos em um ISE fornecem as mesmas experiências de usuário e funcionalidades semelhantes às do serviço global dos Aplicativos Lógicos. Não só é possível usar as mesmas ações internas e conectores no serviço global de Aplicativos Lógicos, mas também é possível usar conectores específicos do ISE. Por exemplo, veja alguns conectores padrão que oferecem versões que podem ser executadas em um ISE:
 
* Armazenamento de Blobs, Armazenamento de Arquivos e Armazenamento de Tabelas do Azure
* Filas do Azure, Barramento de Serviço do Azure, Hubs de Eventos do Azure e IBM MQ
* FTP e SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 e EDIFACT

A diferença entre os conectores ISE e não ISE está nos locais em que os gatilhos e as ações são executados:

* No ISE, ações e gatilhos internos como HTTP sempre executam no mesmo ISE que o aplicativo lógico. 

* Para conectores que oferecem duas versões, uma versão é executada em um ISE, enquanto a outra é executada no serviço global de Aplicativos Lógicos.  

  Os conectores que têm o rótulo do **ISE** sempre são executados no mesmo ISE do aplicativo lógico. Os conectores sem o rótulo do **ISE** são executados no serviço global dos Aplicativos Lógicos. 

  ![Selecionar conectores ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Os conectores executados em um ISE também estão disponíveis no serviço global de Aplicativos Lógicos. 

> [!IMPORTANT]
> Os aplicativos lógicos, as ações internas e os conectores executados no ISE usam um plano de preços diferente, e não um plano de preços com base no consumo. Para obter mais informações, consulte [Preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Permissões para acesso à rede virtual

Ao criar um ISE (ambiente de serviço de integração), você seleciona uma rede virtual do Azure na qual *injeta* o ambiente. A injeção implanta uma instância privada do serviço de Aplicativos Lógicos na rede virtual. Essa ação resulta em um ambiente isolado em que é possível criar e executar os aplicativos lógicos em recursos dedicados. Ao criar os aplicativos lógicos, selecione o ISE como local dos aplicativos. Esses aplicativos lógicos podem acessar diretamente a rede virtual e conectar aos recursos nessa rede. 

Para sistemas conectados a uma rede virtual, é possível injetar um ISE nessa rede virtual para que os aplicativos lógicos possam ter acesso direto a esses sistemas usando um destes itens: 

* Conector ISE para o sistema, por exemplo, SQL Server

* Ação HTTP 

* Conector personalizado

Para sistemas locais que não estão conectados a uma rede virtual ou não têm conectores ISE, é possível conectar a esses sistemas ao [configurar e usar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md).

Antes de selecionar uma rede virtual do Azure para injetar o ambiente, é necessário configurar permissões de RBAC (controle de acesso baseado em função) na rede virtual para o serviço de Aplicativos Lógicos do Azure. Essa tarefa exige que você atribua as funções de **Colaborador de Rede** e **Colaborador Clássico** ao serviço do Aplicativo Lógico do Azure.
Para definir essas permissões, consulte [Conectar a redes virtuais do Azure a partir de aplicativos lógicos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com ISE

É possível usar contas de integração com aplicativos lógicos dentro de um ISE (ambiente de serviço de integração). No entanto, essas contas de integração devem usar o *mesmo ISE* que os aplicativos lógicos vinculados. Os aplicativos lógicos em um ISE podem fazer referência somente às contas de integração que estão no mesmo ISE. Ao criar uma conta de integração, é possível selecionar o ISE como local para a conta de integração.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Fórum dos Aplicativos Lógicos do Azure</a>.
* Para enviar ou votar em ideias de recurso, visite o <a href="https://aka.ms/logicapps-wish" target="_blank">site de comentários do usuário de Aplicativos Lógicos</a>.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [conectar-se a redes virtuais do Azure de aplicativos lógicos isolados](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
