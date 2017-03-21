---
title: Implantar o SAP S/4HANA ou o BW/4HANA em uma VM do Azure | Microsoft Docs
description: Implantar o SAP S/4HANA ou o BW/4HANA em uma VM do Azure
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: 6f345bb816a3fd6f6fb8672b9a43a0d075bd94eb
ms.openlocfilehash: 939c051cad98590acffb6e550ca45bf5fec90d7e
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-sap-s4hana-or-bw4hana-on-microsoft-azure"></a>Implantar o SAP S/4HANA ou o BW/4HANA no Microsoft Azure
Este artigo descreve como implantar o S/4HANA no Microsoft Azure usando o SAP CAL (SAP Cloud Appliance Library) 3.0. Implantar outras soluções baseadas no SAP HANA, como BW/4HANA, funciona da mesma maneira, da perspectiva do processo. Basta selecionar outra solução.

> [!NOTE]
Para obter mais informações sobre a SAP Cloud Appliance Library, consulte a [home page do site](https://cal.sap.com/). Também há um blog do SAP sobre a [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

## <a name="step-by-step-process-to-deploy-the-solution"></a>Processo passo a passo para implantar a solução

As capturas de tela a seguir mostram como implantar o S/4HANA no Azure. O processo funciona da mesma maneira que para outras soluções, como o BW/4HANA.

A primeira captura de tela mostra todas as soluções baseadas no SAP CAL HANA disponíveis no Azure. Observe a **edição SAP S/4HANA local** na parte inferior.

![Captura de tela da janela Soluções da SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

Primeiro, crie uma nova conta do SAP CAL. Em **Contas**, você verá duas opções para o Azure: Microsoft Azure e uma opção do Azure operada pela 21Vianet. Neste exemplo, escolha **Microsoft Azure**.

![Captura de tela da janela Contas da SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

Em seguida, insira a ID da assinatura do Azure que pode ser encontrada no portal do Azure. Em seguida, baixe um certificado de gerenciamento do Azure.

![Captura de tela da janela Contas da SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

> [!NOTE]
Para encontrar a ID de assinatura do Azure, use o portal clássico do Azure, não o portal mais recente do Azure. Isso ocorre porque o CAL SAP não está adaptado ainda para esse novo modelo e ainda exige o portal clássico para trabalhar com certificados de gerenciamento.

A captura de tela a seguir mostra o portal clássico. Em **CONFIGURAÇÕES**, selecione a guia **ASSINATURAS** para encontrar a ID da assinatura a ser inserida na janela Contas do SAP CAL.

![Captura de tela do portal clássico do Azure](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

Em **CONFIGURAÇÕES**, mude para a guia **CERTIFICADOS DE GERENCIAMENTO**. O upload de um certificado de gerenciamento fornece ao SAP CAL as permissões para criar máquinas virtuais em uma assinatura do cliente. (Você está carregando o certificado de gerenciamento baixado antes na SAP CAL.)

![Captura de tela do portal clássico do Azure, guia Certificados de Gerenciamento](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

Uma caixa de diálogo é exibida para que você selecione o arquivo de certificado baixado.

![Captura de tela da caixa de diálogo Carregar um certificado de gerenciamento](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

Depois de carregar o certificado, a conexão entre o SAP CAL e a assinatura do Azure pode ser testada no SAP CAL. Uma mensagem deve ser exibida, indicando que a conexão é válida.

![Captura de tela da janela Contas do SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Em seguida, selecione uma solução que deve ser implantada e crie uma instância.
Insira um nome de instância, escolha uma região do Azure e defina a senha mestra da solução.

![Captura de tela da janela Soluções da SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Após algum tempo, dependendo do tamanho e da complexidade da solução (uma estimativa é fornecida pelo SAP CAL), a solução é mostrada como ativa e pronta para uso.

![Captura de tela da janela Instâncias da SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

É possível ver alguns detalhes da solução, como qual variante de VMs foi implantado. Nesse caso, três VMs do Azure de diferentes tamanhos e finalidades foram criadas.

![Captura de tela da janela Instâncias da SAP Cloud Appliance Library](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

No portal clássico do Azure, essas máquinas virtuais podem ser encontradas começando com o mesmo nome de instância fornecido no SAP CAL.

![Captura de tela que mostra três máquinas virtuais no portal clássico do Azure](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

Agora é possível se conectar à solução usando o botão Conectar no portal do SAP CAL. A caixa de diálogo contém um link para um guia do usuário que descreve todas as credenciais padrão para trabalhar com a solução.

![Captura de tela da caixa de diálogo Conectar à Instância](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

Outra opção é fazer logon na VM Windows cliente e iniciar, por exemplo, a GUI pré-configurada do SAP.

![Captura de tela da GUI pré-configurada do SAP](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

