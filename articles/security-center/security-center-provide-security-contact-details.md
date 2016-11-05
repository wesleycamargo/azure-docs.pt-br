---
title: Fornecer detalhes de contato de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como fornecer detalhes de contato de segurança na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: terrylan

---
# Fornecer detalhes de contato de segurança na Central de segurança do Azure
A Central de Segurança do Azure recomendará que você forneça detalhes de contato de segurança para sua assinatura do Azure se ainda não fez isso. Essas informações serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente têm sido acessados por uma pessoa não autorizada ou ilegal. O MSRC executa determinado monitoramento de segurança da rede e da infraestrutura do Azure e recebe reclamações de inteligência e abuso de ameaça de terceiros.

Uma notificação por email é enviada na primeira ocorrência diária de um alerta e apenas para alertas de gravidade alta. As preferências de email só podem ser configuradas para políticas de assinatura. Grupos de recursos dentro de uma assinatura herdarão essas configurações.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.
> 
> 

## Implementar a recomendação
1. Na folha **Recomendações**, selecione **forneça detalhes de contato de segurança**. ![Fornecer contato de segurança][1]
2. Isso abrirá a folha **Fornecer detalhes de contato de segurança**. Selecione a assinatura do Azure para fornecer informações de contato em. ![Fornecer detalhes de contato de segurança][2]
3. Uma segunda folha **Fornecer detalhes de contato de segurança** será aberta.
   
   * Insira o endereço de email de contato de segurança ou endereços separados por vírgulas. Não há um limite para o número de endereços de email que você pode inserir.
   * Insira um número de telefone internacional de contato de segurança.
   * Para receber emails sobre alertas de gravidade alta, ative a opção **Enviar-me emails sobre alertas**.
   * Futuramente, você terá a opção de enviar notificações por email aos proprietários da assinatura. Esta opção está esmaecida no momento.
   * Selecione **OK** para aplicar as informações de contato de segurança à sua assinatura.

## Consulte também
Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]: ./media/security-center-provide-security-contacts/provide-contact-details.png

<!---HONumber=AcomDC_0727_2016-->