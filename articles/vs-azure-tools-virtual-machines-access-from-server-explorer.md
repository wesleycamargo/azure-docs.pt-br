---
title: "Acessando Máquinas Virtuais do Azure do Gerenciador de Servidores | Microsoft Docs"
description: "Obtenha uma visão geral de como exibir e gerenciar máquinas virtuais (VMs) do Azure no Gerenciador de Servidores no Visual Studio."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 08/15/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 009b325054737b63d5bdaefbe005d6a613126f52


---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Acessando máquinas virtuais do Azure por meio do Gerenciador de Servidores
Usando o Gerenciador de Servidores no Visual Studio, você pode exibir informações sobre as máquinas virtuais hospedadas pelo Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>Acessando máquinas virtuais no Gerenciador de Servidores
Se tiver máquinas virtuais hospedadas pelo Azure, você pode acessá-las no Gerenciador de Servidores. Você primeiro deve entrar sua assinatura do Azure para exibir os serviços móveis. Por exemplo, no Gerenciador de Servidores, você pode abrir o menu de atalho do nó do Azure e escolher **Conectar ao Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>Para obter informações sobre suas máquinas virtuais
1. No Gerenciador de Servidores, escolha uma máquina virtual e pressione a tecla F4 para mostrar a janela de propriedades.
   
    A tabela a seguir mostra quais propriedades estão disponíveis, mas elas são somente leitura. Para alterá-las, use o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
   
   | Propriedade | Descrição |
   | --- | --- |
   | Nome DNS |A URL com o endereço de Internet da máquina virtual. |
   | Ambiente |Para uma máquina virtual, o valor dessa propriedade é sempre produção. |
   | Nome |O nome da máquina virtual. |
   | Tamanho |O tamanho da máquina virtual, que reflete a quantidade de memória e espaço em disco disponível. Para obter mais informações, consulte Como configurar os tamanhos de uma máquina virtual. |
   | Status |Os valores incluem Inicial, Iniciado, Parando, Parado e Recuperando Status. Se Recuperando Status aparecer, o status atual é desconhecido. Os valores para essa propriedade diferem dos valores que são usados no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885). |
   | SubscriptionID |A ID de assinatura para sua conta do Azure. Você pode mostrar essas informações no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) exibindo as propriedades de uma assinatura. |
2. Escolha um nó do ponto de extremidade e exiba a janela **Propriedades** .
3. A tabela a seguir descreve as propriedades de pontos de extremidade disponíveis, mas eles são somente leitura. Para adicionar ou editar os pontos de extremidade para uma máquina virtual, use o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885). 
   
   | Propriedade | Descrição |
   | --- | --- |
   | Nome |Um identificador para o ponto de extremidade. |
   | Porta privada |A porta para acesso à rede interna para o seu aplicativo. |
   | Protocolo |O protocolo de camada de transporte que este ponto de extremidade usa: TCP ou UDP. |
   | Porta pública |A porta usada para acesso público ao seu aplicativo. |

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como usar funções do Azure no Visual Studio, consulte [usando a área de trabalho remota com funções do Azure](vs-azure-tools-remote-desktop-roles.md).




<!--HONumber=Nov16_HO3-->


