---
title: Acessar redes virtuais do Azure a partir dos Aplicativos Lógicos do Azure com ISEs (Ambientes de Serviço de Integração)
description: Esta visão geral descreve como ISEs (ambientes de serviço de integração) ajudam os aplicativos lógicos a acessarem as VNETs (redes virtuais) do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: b53cd54afdf6243769602971ab77145cfa9ba9cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506802"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acessar recursos de rede virtual do Azure a partir dos Aplicativos Lógicos do Azure usando ISEs (Ambientes de Serviço de Integração)

> [!NOTE]
> Esse recurso está em [ *visualização pública*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Às vezes, os aplicativos lógicos e as contas de integração precisam acessar recursos protegidos, como VMs (máquinas virtuais) e outros sistemas ou serviços em uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Para configurar esse acesso, é possível [criar um *ISE* (ambiente de serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) para executar os aplicativos lógicos e as contas de integração. Quando você cria um ISE, o Azure implanta uma instância privada e isolada do serviço de aplicativos lógicos em sua rede virtual do Azure. Essa instância privada usa recursos dedicados, como o armazenamento, sendo executada separadamente do serviço público "global" de Aplicativos Lógicos. A separação sua instância privada isolada e a instância global pública também ajuda a reduzir o impacto de outros locatários do Azure podem ter no desempenho de seus aplicativos, que também é conhecido como o [efeito de "vizinhos ruidosos"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Depois de criar seu ISE, quando você vai criar sua conta de integração ou de aplicativo lógico, você pode selecionar seu ISE como local de sua lógica aplicativo ou a integração da conta:

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Seu aplicativo lógico agora pode acessar diretamente os sistemas que estão dentro ou conectado à sua rede virtual usando qualquer um desses itens:

* Um conector com versão do ISE para o sistema, por exemplo, o SQL Server
* Um gatilho internos ou ação, como o gatilho HTTP ou uma ação
* Um conector personalizado

Esta visão geral descreve mais detalhes sobre como um ISE oferece a seus aplicativos lógicos e integração de contas de acesso direto à rede virtual do Azure e compara as diferenças entre um ISE e o serviço global de aplicativos lógicos.
Para sistemas locais que não estão conectados a uma rede virtual ou não tem conectores de versão do ISE, você pode se conectar a esses sistemas por [como configurar e usar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md).

> [!IMPORTANT]
> Os aplicativos lógicos, as ações internas e os conectores executados no ISE usam um plano de preços diferente, e não um plano de preços com base no consumo. Para obter mais informações, consulte [Preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolado versus global

Quando você cria um ambiente de serviço integrado (ISE) no Azure, você pode selecionar a rede virtual do Azure onde você deseja *injetar* seu ISE. Azure injeta ou implanta uma instância privada do serviço aplicativos lógicos em sua rede virtual. Essa ação cria um ambiente isolado em que é possível criar e executar os aplicativos lógicos em recursos dedicados. Quando você cria seu aplicativo lógico, selecione seu ISE como o local do seu aplicativo, que oferece seu aplicativo lógico acesso direto à sua rede virtual e os recursos da rede.

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

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com ISE

É possível usar contas de integração com aplicativos lógicos dentro de um ISE (ambiente de serviço de integração). No entanto, essas contas de integração devem usar o *mesmo ISE* que os aplicativos lógicos vinculados. Os aplicativos lógicos em um ISE podem fazer referência somente às contas de integração que estão no mesmo ISE. Ao criar uma conta de integração, é possível selecionar o ISE como local para a conta de integração.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Fórum dos Aplicativos Lógicos do Azure</a>.
* Para enviar ou votar em ideias de recurso, visite o <a href="https://aka.ms/logicapps-wish" target="_blank">site de comentários do usuário de Aplicativos Lógicos</a>.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [conectar-se a redes virtuais do Azure de aplicativos lógicos isolados](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
