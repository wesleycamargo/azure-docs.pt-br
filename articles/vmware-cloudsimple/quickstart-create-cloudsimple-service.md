---
title: Solução de VMware do Azure por CloudSimple Quickstart - criar serviço
description: Aprenda a criar o serviço CloudSimple, compra nós e reserva de nós
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b3b95db24f4b0f9a0cf8f5102dfeea5dc51e29f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577564"
---
# <a name="quickstart---create-service"></a>Guia de início rápido - criar serviço

Para começar, crie a solução de VMware do Azure por CloudSimple no portal do Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Solução do VMware CloudSimple - visão geral do serviço

O serviço CloudSimple permite consumir solução CloudSimple do VMware do Azure.  Criando o serviço permite a compra de nós, reserva de nós e criar nuvens privadas.  Você pode adicionar o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível.  O serviço define a rede de borda da solução CloudSimple do VMware do Azure.  Essa rede de borda é usada para serviços que incluem a conectividade VPN, ExpressRoute e da Internet em suas nuvens privadas.

Para adicionar o serviço CloudSimple, você deve criar uma sub-rede de gateway. A sub-rede de gateway é usada ao criar a rede de borda e exige uma de/28 bloco CIDR. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não coincide com nenhum dos seus espaços de endereço de rede local ou o espaço de endereço de rede virtual do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Habilitar o provedor de recursos Microsoft.VMwareCloudSimple

Siga as etapas abaixo para habilitar o provedor de recursos para o serviço CloudSimple.

1. Selecione **Todos os serviços**.
2. Pesquise e selecione **assinaturas**.

    ![Selecione assinaturas](media/cloudsimple-service-select-subscriptions.png)

3. Selecione a assinatura na qual você deseja habilitar o serviço CloudSimple
4. Clique em **provedores de recursos** para a assinatura
5. Use **Microsoft.VMwareCloudSimple** para filtrar o provedor de recursos
6. Selecione o **Microsoft.VMwareCloudSimple** provedor de recursos e clique em **registrar**

    ![Registrar provedor de recursos](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Criar o serviço

>[!NOTE]
> Serviço de CloudSimple deve ser habilitado em sua assinatura. Se sua assinatura não está habilitada, você receberá um erro ao tentar criar o serviço.  Siga as etapas em [CloudSimple habilitar serviço](https://docs.azure.cloudsimple.com/enable-cloudsimple-service) artigo para habilitar o serviço.

1. Selecione **Todos os serviços**.
2. Pesquise **CloudSimple serviço**.

    ![Serviço de pesquisa de CloudSimple](media/create-cloudsimple-service-search.png)

3. Selecione **CloudSimple serviços**.
4. Clique em **adicionar** para criar um novo serviço.

    ![Adicionar serviço CloudSimple](media/create-cloudsimple-service-add.png)

5. Selecione a assinatura onde você deseja criar o serviço CloudSimple.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira um nome para identificar o serviço.
8. Insira o CIDR para o gateway do serviço. Especifique uma de/28 sub-rede que não coincide com nenhum dos sub-redes locais, subredes do Azure ou sub-redes de CloudSimple planejadas. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criando o serviço CloudSimple](media/create-cloudsimple-service.png)

9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="purchase-nodes"></a>Nós de compra

Para configurar a capacidade vá de pagamento medida que para um ambiente de nuvem privada CloudSimple, primeiro provisionar nós no portal do Azure.

1. Selecione **Todos os serviços**.
2. Pesquise **CloudSimple nós**.

    ![Pesquisa CloudSimple nós](media/create-cloudsimple-node-search.png)

3. Selecione **CloudSimple nós**.
4. Clique em **adicionar** para criar nós.

    ![Adicionar nós CloudSimple](media/create-cloudsimple-node-add.png)

5. Selecione a assinatura em que você deseja comprar CloudSimple nós.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o prefixo para identificar os nós.
8. Selecione o local para os recursos do nó.
9. Selecione o local dedicado para hospedar os recursos do nó.
10. Selecione o tipo de nó. Você pode escolher o [opção CS28 ou CS36](cloudsimple-node.md). A última opção inclui a capacidade máxima de memória e computação.
11. Selecione o número de nós para provisionar.
12. Selecione **Examinar + criar**.
13. Examine as configurações. Para modificar as configurações, clique em **anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas

* [Criar a nuvem privada e configurar o ambiente](quickstart-create-private-cloud.md)
* Saiba mais sobre [CloudSimple serviço](https://docs.azure.cloudsimple.com/cloudsimple-service)
