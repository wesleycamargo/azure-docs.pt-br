---
title: "Adicionar um Firewall de Última Geração na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Adicionar um Firewall de Última Geração** e **Rotear o tráfego somente por meio do NGFW**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4cc9541251157a3c8c632a01fafb52ea87ba58dd


---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Adicionar um Firewall de Última Geração na Central de Segurança do Azure
A Central de Segurança do Azure pode recomendar que você adicione um Firewall de Última Geração (NGFW) de um parceiro da Microsoft para aumentar suas proteções de segurança. Este documento guiará você por um exemplo de como fazer isso.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Ela não é um guia passo a passo.
> 
> 

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **Recomendações** , selecione **Adicionar um Firewall de Última Geração**.
   ![Adicionar um Firewall de Última Geração][1]
2. Na folha **Adicionar um Firewall de Última Geração** , selecione um ponto de extremidade.
   ![Selecionar um ponto de extremidade][2]
3. Uma segunda folha **Adicionar um Firewall de Última Geração** será aberta. Você pode optar por usar uma solução existente, se disponível, ou criar uma nova. Neste exemplo não existem soluções disponíveis, por isso, vamos criar um novo NGFW.
   ![Criar um novo Firewall de Última Geração][3]
4. Para criar um novo NGFW, selecione uma solução da lista de parceiros integrados. Neste exemplo, selecionaremos **Check Point**.
   ![Selecionar uma solução de Firewall de Última Geração][4]
5. A folha **Check Point** se abre e fornece informações sobre a solução do parceiro. Selecione **Criar** na folha de informações.
   ![Folha Informações do firewall][5]
6. A folha **Criar máquina virtual** é aberta. Nessa folha, você pode inserir as informações necessárias para criar uma VM (máquina virtual) que executará o NGFW. Siga as etapas e forneça as informações do NGFW necessárias. Selecione OK para aplicar.
   ![Criar uma máquina virtual para executar o NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Rotear o tráfego apenas através do NGFW
Volte para a folha **Recomendações** . Uma nova entrada foi gerada após a adição de um NGFW por meio da Central de Segurança chamada **Rotear o tráfego apenas através do NGFW**. Essa recomendação será criada somente se você tiver instalado o NGFW por meio da Central de Segurança. Se você tiver pontos de extremidade para a Internet, a Central de Segurança recomendará que você configure regras de grupo de segurança de rede que forcem o tráfego de entrada em sua VM por meio de seu NGFW.

1. Na **folha Recomendações**, selecione **Rotear o tráfego apenas através do NGFW**.
   ![Rotear o tráfego apenas através do NGFW][7]
2. Isso abrirá a folha **rotear o tráfego por meio de NGFW** , que lista as VMs pelas quais você pode rotear o tráfego. Selecione uma VM na lista.
   ![Selecionar uma máquina virtual][8]
3. Uma folha para a VM selecionada é aberta, exibindo as regras de entrada relacionadas. Uma descrição fornece a você mais informações sobre as próximas etapas possíveis. Selecione **Editar regras de entrada** para prosseguir com a edição de uma regra de entrada. A expectativa é que **Fonte** não esteja definido como **Qualquer** para os pontos de extremidade voltados para a Internet vinculados com o NGFW. Para saber mais sobre as propriedades da regra de entrada, consulte [Regras de NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).
   ![Configurar regras para limitar o acesso][9]
   ![Editar regra de entrada][10]

## <a name="see-also"></a>Confira também
Este documento mostrou como implementar a recomendação da Central de Segurança para "Adicionar um Firewall de Última Geração". Para saber mais sobre NGFWs e a solução de parceiro da Check Point, consulte:

* [Firewall de Última Geração](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configuração de políticas de segurança na Central de Segurança do Azure](security-center-policies.md) : saiba como definir as políticas de segurança.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png



<!--HONumber=Nov16_HO3-->


