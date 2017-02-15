---
title: Validar a VNET do Azure para uso com o Azure RemoteApp | Microsoft Docs
description: "Saiba como verificar se o VNET do Azure está pronto para uso com o RemoteApp do Azure"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b573ba02-4587-4be5-9821-27bd891a73b2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 30d18bdbdc6293bab5a8876fb1e503e125829e2e


---
# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Validar o VNET do Azure para usar com o RemoteApp do Azure
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Antes de usar um VNET do Azure com o RemoteApp do Azure, é recomendado validar o VNET. Isso ajuda a evitar problemas com conectividade.

Para validar seu VNET do Azure, faça o seguinte:

1. Crie uma máquina virtual do Azure dentro da sub-rede do VNET do Azure que deseja usar com o RemoteApp do Azure.
2. Conecte-se à VM usando a opção **Conectar** no portal de gerenciamento.
3. Adicione a máquina virtual no mesmo domínio que você deseja usar com o RemoteApp do Azure. Se estiver criando uma coleção híbrida que se conecta à sua rede local, ingresse a máquina virtual no domínio local.

Se essa ação for bem-sucedida, o VNET do Azure está pronto para uso com o RemoteApp.

Para obter mais informações sobre o fluxo de trabalho de coleção híbrido de ponta a ponta, consulte os seguintes artigos:

* [Como planejar sua rede virtual para o RemoteApp do Azure](remoteapp-planvnet.md)
* [Criar uma coleção híbrida](remoteapp-create-hybrid-deployment.md)
* [Implantar a coleção do RemoteApp do Azure à sua Rede Virtual do Azure (com suporte para Rota Expressa)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)




<!--HONumber=Dec16_HO2-->


