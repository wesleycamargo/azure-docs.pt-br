<properties
   pageTitle="Rotear o tráfego através do Firewall de Próxima Geração apenas na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Rotear o tráfego apenas através do NGFW**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="terrylan"/>

# Rotear o tráfego através do Firewall de Próxima Geração apenas na Central de Segurança do Azure

A Central de Segurança do Azure pode detectar quando você implantou um NGFW (Firewall de Próxima Geração). Se você tiver pontos de extremidade para a Internet, a Central de Segurança do Azure recomendará que você configure regras de grupo de segurança de rede que forcem o tráfego de entrada em sua VM (máquina virtual) por meio de seu NGFW.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## Implementar a recomendação

1. Na **folha Recomendações**, selecione **Rotear o tráfego apenas através do NGFW**. ![Rotear o tráfego apenas através do NGFW][1]

2. Isso abrirá a folha **rotear o tráfego por meio de NGFW**, que lista as VMs pelas quais você pode rotear o tráfego. Selecione uma VM na lista. ![Selecionar uma máquina virtual][2]

3. Uma folha para a VM selecionada é aberta, exibindo as regras de entrada relacionadas. Uma descrição fornece a você mais informações sobre as próximas etapas possíveis. ![Configurar regras para limitar o acesso][3]

## Consulte também

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
- [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-route-traffic-through-ngfw/route-traffic-through-ngfw.png
[2]: ./media/security-center-route-traffic-through-ngfw/select-vm.png
[3]: ./media/security-center-route-traffic-through-ngfw/configure-rules-to-limit-access.png

<!---HONumber=AcomDC_0720_2016-->