<properties
   pageTitle="Gerenciamento de soluções de parceiros na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento fornece uma orientação de como a Central de Segurança do Azure permite que você monitore rapidamente o status de integridade de suas soluções de parceiro integradas com sua assinatura do Azure."
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# Monitoramento de soluções de parceiros com a Central de Segurança do Azure

Este documento orienta sobre como monitorar o status de integridade de suas soluções de parceiro na Central de Segurança do Azure.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## Monitoramento das soluções de parceiros

O bloco **Soluções de parceiros** na folha **Central de Segurança** permite que você monitore rapidamente o status de integridade de suas soluções de parceiro integradas com sua assinatura do Azure. ![Bloco Soluções de parceiros][1]

O bloco **Soluções de parceiros** exibe o número de soluções de parceiros e um resumo de status para essas soluções.

O **STATUS** de uma solução de parceiro pode ser:

- Protegido (verde) - não há qualquer problema de integridade
- Não íntegro (vermelho) - há um problema de integridade que requer atenção imediata
- Parou de relatar (laranja) - a solução interrompeu o envio de relatórios sobre sua integridade
- Status de proteção desconhecido (laranja) - a integridade da solução é desconhecida no momento devido a um processo para adicionar um novo recurso à solução existente com falha.
- Não relatado (cinza) - a solução não reportou nada ainda. O status da solução pode não ser relatado se ele foi conectado e ainda está sendo implantado

Se não houver soluções integradas à sua assinatura, o bloco informará que não há uma solução. A seleção do bloco **Soluções de parceiros** permitirá que você abra o bloco **Recomendações** para implantar soluções de segurança de parceiro. ![Sem soluções de parceiros][2]

Para exibir a integridade das soluções de seu parceiro:

1. Selecione o bloco **Soluções de parceiros**. Uma folha será aberta exibindo uma lista de suas soluções de parceiros conectadas à Central de Segurança. ![Soluções de parceiros][3]

2. Selecione uma solução de parceiro. Neste exemplo, permite selecionar a solução **F5-WAF2**. Uma folha será aberta mostrando o status da solução de parceiro e dos recursos associados a ela. Selecione **Console da solução** para abrir a experiência de gerenciamento do parceiro para essa solução. ![Detalhes da solução de parceiro][4]

3. Volte para a folha **F5-WAF2** folha e selecione **Vincular aplicativo**. A folha **Vincular Aplicativos** é aberta. Nela, você pode conectar recursos à solução de parceiro. ![Vincular recursos à solução de parceiro][5]

## Consulte também
Neste documento, você foi apresentado às **Soluções de Parceiros** na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

<!---HONumber=AcomDC_0727_2016-->