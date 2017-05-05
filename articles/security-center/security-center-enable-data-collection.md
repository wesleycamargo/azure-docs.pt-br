---
title: "Habilitar a coleta de dados na Central de Segurança do Azure | Microsoft Docs"
description: " Saiba como habilitar a coleta de dados na Central de Segurança do Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: b942f934ecb25083d779096dc628e342870edc26
ms.lasthandoff: 04/13/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>Habilitar coleta de dados na Central de Segurança do Azure
Para ajudar os clientes a evitar, detectar e responder a ameaças, a Central de Segurança do Azure coleta e processa dados sobre as máquinas virtuais do Azure, incluindo informações de configuração, metadados e logs de eventos. Quando você acessa pela primeira vez a Central de Segurança, a coleta de dados é habilitada em todas as máquinas virtuais em sua assinatura. A coleta de dados é recomendada, mas você pode recusar desativando-a na política da Central de Segurança (confira [Desabilitar a coleta de dados](#disabling-data-collection)). Se você desativar a coleta de dados, a Central de Segurança recomendará ativar a coleta de dados na política de segurança dessa assinatura.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Selecione o bloco **Recomendações** na folha **Central de Segurança**.  Isso abre a folha **Recomendações** .
   ![Folha Central de Segurança][1]
2. Sobre a folha **Recomendações** , selecione **Habilitar coleta de dados para assinaturas**.  Isso abre a folha **Ativar a coleta de dados**.
   ![Folha de recomendações][2]
3. Na folha **Ativar a coleta de dados** , selecione sua assinatura. A folha **Política de segurança** para essa assinatura é aberta.
4. Na folha **Política de segurança**, selecione **Ativado** em **Coleta de dados** para coletar logs automaticamente. A ativação da coleta de dados provisiona a extensão de monitoramento em todas as VMs atuais e novas com suporte na assinatura.

   ![Folha de política de segurança][3]

5. Selecione **Salvar**.
6. Selecione **Escolher uma conta de armazenamento por região**. Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Se você não escolher uma conta de armazenamento para cada região, uma conta de armazenamento será criada e colocada no grupo de recursos securitydata. Neste exemplo, escolhemos **newstoracct**. Você pode alterar a conta de armazenamento mais tarde, retornando à política de segurança de sua assinatura e escolhendo outra conta de armazenamento.
   ![Escolher uma conta de armazenamento][4]
7. Selecione **OK**.

> [!NOTE]
> É recomendável que você ative a coleta de dados e escolha uma conta de armazenamento no nível da assinatura primeiro. As políticas de segurança podem ser definidas no nível da assinatura do Azure e no nível do grupo de recursos, mas a configuração da conta de armazenamento e da coleta de dados ocorre apenas no nível da assinatura.
>
>

## <a name="after-data-collection-is-enabled"></a>Após a coleta de dados ser habilitada
A coleta de dados é habilitada por meio do agente de monitoramento do Azure e da extensão de monitoramento de segurança do Azure. A extensão de Monitoramento de Segurança do Azure examina várias configurações de segurança relevantes e as envia para os rastreamentos do [ETW (Rastreamento de Eventos para Windows)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). Além disso, o sistema operacional cria entradas de log de eventos. O agente de monitoramento do Azure lê as entradas do registro de eventos e os vestígios de ETW e os copia para sua conta de armazenamento para análise. O Agente de Monitoramento também copia os arquivos de despejo de falha para sua conta de armazenamento. Essa é a conta de armazenamento configurada na política de segurança.

## <a name="disabling-data-collection"></a>Desabilitar a coleta de dados
Você pode desabilitar a coleta de dados a qualquer momento, o que removerá automaticamente os Agentes de Monitoramento instalados anteriormente pela Central de Segurança. Você deve selecionar uma assinatura para desativar a coleta de dados.

> [!NOTE]
> As políticas de segurança podem ser definidas no nível da assinatura e do grupo de recursos do Azure, mas é necessário selecionar uma assinatura para desligar a coleta de dados.
>
>

1. Volte para a folha **Central de Segurança** e selecione o bloco **Política**. Isso abre a folha **Política de segurança - definir política por assinatura ou grupo de recursos** .
   ![Selecione o bloco de política][5]
2. Na folha **Política de segurança - definir política por assinatura ou grupo de recursos** , selecione a assinatura para a qual você deseja desabilitar a coleta de dados.
   ![Selecionar a assinatura para desabilitar a coleta de dados][6]
3. A folha **Política de segurança** para essa assinatura é aberta.  Selecione **Desativado** em Coleta de dados.
4. Selecione **Salvar** na faixa de opções.


## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como implementar a recomendação da Central de Segurança "Habilitar a coleta de dados". Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) : saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/security-center-blade.png
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

