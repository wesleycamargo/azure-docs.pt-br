---
title: Acessar redes virtuais do Azure a partir do Aplicativo Lógico do Azure com ISEs (Ambientes de Serviço de Integração)
description: Esta visão geral descreve como ISEs (Ambientes de Serviço de Integração) ajudam os aplicativos lógicos a acessarem as redes virtuais do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: c5fc071e61a3d5304821343b6dc992112f4bd6b1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233133"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acessar recursos de rede virtual do Azure a partir do Aplicativo Lógico do Azure usando ISEs (Ambientes de Serviço de Integração)

> [!NOTE]
> Essa funcionalidade está em *versão prévia privada*. Para solicitar acesso, [crie sua solicitação para ingressar aqui](https://aka.ms/iseprivatepreview).

Às vezes, os aplicativos lógicos e as contas de integração precisam acessar recursos protegidos, como VMs (máquinas virtuais) e outros sistemas ou serviços dentro de uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Para configurar esse acesso, você pode [criar um *ISE* (ambiente de serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) que você usa como o local para os aplicativos lógicos e as contas de integração. 

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Criar um ISE implanta uma instância privada e isolada dos Aplicativos Lógicos em sua rede virtual do Azure. A instância privada usa recursos dedicados, como o armazenamento, e é executada separadamente do serviço público "global" dos Aplicativos Lógicos. Essa separação também ajuda a reduzir o impacto que outros locatários do Azure podem ter no desempenho do seu aplicativo, conhecido como [efeito "vizinhos barulhentos"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Esta visão geral descreve como a criação de um ISE ajuda os aplicativos lógicos e as contas de integração a acessarem diretamente os recursos que fazem parte da rede virtual do Azure, além de comparar as diferenças entre um ISE e o serviço global dos Aplicativos Lógicos.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolado versus global

Ao criar um ISE (ambiente de serviço integrado) no Azure, você pode selecionar uma rede virtual do Azure como *par* do ambiente. O Azure implanta uma instância privada do serviço dos Aplicativos Lógicos na rede virtual, o que gera um ambiente isolado em que é possível criar e executar aplicativos lógicos em recursos dedicados. Ao criar um aplicativo lógico, é possível selecionar esse ambiente como o local dele, o que também permite acesso direto aos recursos da rede virtual.  

Os aplicativos lógicos em um ISE fornecem as mesmas experiências de usuário e funcionalidades semelhantes às do serviço global dos Aplicativos Lógicos. Não só é possível usar as mesmas funções internas e conectores fornecidos pelo serviço global dos Aplicativos Lógicos, mas você também pode escolher entre os conectores que oferecem versões do ISE. Por exemplo, veja alguns conectores padrão que oferecem versões que podem ser executadas em um ISE:
 
* Armazenamento de Blobs, Armazenamento de Arquivos e Armazenamento de Tabelas do Azure
* Filas do Azure
* Barramento de Serviço do Azure
* FTP
* SSH FTP (SFTP)
* SQL Server
* AS2, X12 e EDIFACT

A diferença entre os conectores ISE e não ISE está nos locais em que os gatilhos e as ações são executados:

* Se você usar gatilhos e ações internos no ISE, como HTTP, eles sempre serão executados no mesmo ISE do aplicativo lógico. 

* Para os conectores que oferecem duas versões: uma versão é executada em um ISE; a outra no serviço global dos Aplicativos Lógicos.  

  Os conectores que têm o rótulo do **ISE** sempre são executados no mesmo ISE do aplicativo lógico. Os conectores sem o rótulo do **ISE** são executados no serviço global dos Aplicativos Lógicos. 

  ![Selecionar conectores ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Os conectores que você configura no ISE também estão disponíveis para uso no serviço global dos Aplicativos Lógicos. 

> [!IMPORTANT]
> Os aplicativos lógicos, as ações internas e os conectores executados no ISE usam um plano de preços diferente, e não um plano de preços com base no consumo. Para obter mais informações, consulte [Preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Permissões para acesso à rede virtual

Ao criar um ISE (ambiente de serviço de integração), você pode selecionar uma rede virtual do Azure como *par* do ambiente. No entanto, você *só* poderá criar essa relação, ou *emparelhamento*, quando criar o ISE. Essa relação permite que o ISE acesse os recursos da rede virtual, que, em seguida, permite que os aplicativos lógicos do ISE se conectem diretamente com os recursos na rede virtual. Para sistemas locais em uma rede virtual vinculada a um ISE, os aplicativos lógicos podem ter acesso direto a esses sistemas usando um destes itens: 

* Conector ISE para o sistema, por exemplo, SQL Server

* Ação HTTP 

* Conector personalizado

Para sistemas locais que não estão em uma rede virtual ou não têm conectores ISE, ainda é possível se conectar após [configurar e usar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md).

Antes de selecionar uma rede virtual do Azure como par de seu ambiente, é necessário configurar permissões de RBAC (controle de acesso baseado em função) na rede virtual do Azure para o serviço do Aplicativo Lógico do Azure. Essa tarefa exige que você atribua as funções de **Colaborador de Rede** e **Colaborador Clássico** ao serviço do Aplicativo Lógico do Azure. Para saber mais sobre as permissões de função exigidas para o emparelhamento, confira a [seção Permissões em Criar, alterar ou excluir um emparelhamento de rede virtual](../virtual-network/virtual-network-manage-peering.md#permissions).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com ISE

É possível usar contas de integração com aplicativos lógicos executados em um ISE (ambiente de serviço de integração), mas essas contas de integração também precisam usar o *mesmo ISE* dos aplicativos lógicos vinculados. Os aplicativos lógicos em um ISE podem fazer referência somente às contas de integração que estão no mesmo ISE. Ao criar uma conta de integração, é possível selecionar o ISE como local para a conta de integração.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Fórum dos Aplicativos Lógicos do Azure</a>.
* Para enviar ou votar em ideias de recurso, visite o <a href="https://aka.ms/logicapps-wish" target="_blank">site de comentários do usuário de Aplicativos Lógicos</a>.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [conectar-se a redes virtuais do Azure de aplicativos lógicos isolados](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
