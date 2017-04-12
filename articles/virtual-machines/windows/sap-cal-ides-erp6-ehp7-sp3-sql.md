---
title: Implantando SAP IDES EHP7 SP3 para SAP ERP 6.0 no Microsoft Azure | Microsoft Docs
description: "Implantação de SAP IDES EHP7 SP3 para o SAP ERP 6.0 no Microsoft Azure"
services: virtual-machines-windows
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5741f604321f50db371c7c80e727d95c7c4c5605
ms.lasthandoff: 03/31/2017


---
# <a name="deploying-sap-ides-ehp7-sp3-for-sap-erp-60-on-microsoft-azure"></a>Implantação de SAP IDES EHP7 SP3 para o SAP ERP 6.0 no Microsoft Azure
Este artigo descreve como implantar o SAP IDES em execução com o SQL Server e o Windows OS no Microsoft Azure via SAP Cloud Appliance Library 3.0. As capturas de tela mostram o processo passo a passo. A implantação de outras soluções na lista funciona da mesma maneira de uma perspectiva de processo. Basta selecionar uma solução diferente.

Para iniciar com a SAP Cloud Appliance Library (SAP CAL), acesse [aqui](https://cal.sap.com/). Há um blog do SAP sobre a nova [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

As seguintes capturas de tela mostram passo a passo como implantar o SAP IDES no Microsoft Azure. O processo funciona da mesma forma para as outras soluções.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

A primeira figura mostra todas as soluções disponíveis no Microsoft Azure. A solução SAP IDES baseada em Windows destacada, disponível apenas no Azure, foi escolhida para o processo.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

Primeiro, uma nova conta SAP CAL precisa ser criada. Atualmente, há duas opções para o Azure - Azure padrão e Azure na China continental operado pelo parceiro 21Vianet.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

Então, é preciso inserir a ID de assinatura do Azure que pode ser encontrada no portal do Azure - veja também mais abaixo como obtê-la. Depois, um certificado de gerenciamento do Azure precisa ser baixado.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

No novo portal do Azure, localiza-se o item "Assinaturas" no lado esquerdo. Clique para mostrar todas as assinaturas ativas para o usuário.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

Selecionar uma das assinaturas e escolher "Certificados de Gerenciamento" explica que existe um novo conceito usando as "entidades de serviço" para o novo modelo do Azure Resource Manager.
O CAL SAP não está adaptado ainda para esse novo modelo e ainda requer o modelo "clássico" e o antigo portal do Azure para trabalhar com os certificados de gerenciamento.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

Aqui, é possível ver o antigo portal do Azure. O carregamento de um certificado de gerenciamento fornece ao SAP CAL permissões para criar máquinas virtuais dentro de uma assinatura do cliente. Na guia "ASSINATURAS", é possível encontrar a ID da assinatura a ser inserida no portal do SAP CAL.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

Na segunda guia, é possível carregar o certificado de gerenciamento que foi baixado anteriormente no SAP CAL.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

Uma pequena caixa de diálogo é exibida para selecionar o arquivo do certificado baixado.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

Depois do certificado ser carregado, a conexão entre o SAP CAL e a assinatura do Azure do cliente pode ser testada no SAP CAl. Uma pequena mensagem deve ser exibida informando que a conexão é válida.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

Após a configuração de uma conta, é preciso selecionar uma solução que deve ser implantada e criar uma instância.
Com o modo "básico", é realmente simples. Insira um nome de instância, escolha uma região do Azure e defina a senha principal para a solução.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

Após algum tempo, dependendo do tamanho e da complexidade da solução (uma estimativa é fornecida pelo SAP CAL), aparece como "ativo" e pronto para o uso. É muito simples.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

Examinando alguns detalhes da solução, é possível ver qual tipo de VM foi implantada. Nesse caso, há uma única VM do Azure de tamanho D12 criada pelo SAP CAL.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

No portal do Azure, as máquinas virtuais podem ser encontradas começando com o mesmo nome de instância fornecido no SAP CAL.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

Agora, é possível conectar a solução por meio do botão Conectar no portal do SAP CAL. A pequena caixa de diálogo contém um link para um guia do usuário que descreve todas as credenciais padrão para trabalhar com a solução.
[Aqui](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) está o link para o guia da solução IDES.

![](./media/sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Uma opção é fazer logon na VM Windows e iniciar, por exemplo, a GUI do SAP pré-configurada.


