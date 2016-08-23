<properties
   pageTitle="Protegendo sua rede na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento endereça as recomendações na Central de Segurança do Azure que ajudam a proteger sua rede do Azure e cumprir as políticas de segurança."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# Protegendo sua rede na Central de Segurança do Azure

A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações que orientam você durante o processo de configuração dos controles necessários. As recomendações se aplicam aos tipos de recursos do Azure: máquinas virtuais (VMs), rede, aplicativos e SQL.

Este artigo endereça as recomendações que se aplicam à rede. As recomendações da rede giram em torno da próxima geração de firewalls, Grupos de Segurança da Rede, configuração das regras do tráfego de entrada e muito mais. Use a tabela a seguir como referência para ajudá-lo a entender as recomendações disponíveis da rede e o que cada uma delas fará se você as aplicar.

## Recomendações disponíveis da rede

|Recomendações|Descrição|
|-----|-----|
|[Adicionar um Firewall de Última Geração](security-center-add-next-generation-firewall.md)|Recomenda que você adicione um Firewall de Última Geração (NGFW) de um parceiro da Microsoft para aumentar suas proteções de segurança.|
|[Rotear o tráfego apenas através do NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Recomenda que você configure regras para o grupo de segurança de rede (NSG) que forcem o tráfego de entrada em sua VM a passar pelo NGFW.|
|[Habilitar Grupos de Segurança de Rede em sub-redes ou máquinas virtuais](security-center-enable-network-security-groups.md)|Recomenda que você habilite NSGs em sub-redes ou VMs.|
|[Restringir o acesso por meio de ponto de extremidade para a Internet](security-center-restrict-access-through-internet-facing-endpoints.md)|Recomenda que você configure regras de tráfego de entrada para NSGs.|

## Confira também

Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

- [Protegendo suas máquinas virtuais na Central de Segurança do Azure](security-center-virtual-machine-recommendations.md)
- [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
- [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
- [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.

<!---HONumber=AcomDC_0810_2016-->