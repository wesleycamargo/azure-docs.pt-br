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
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7e9ad8cd8c77c57c37dc208b86b3727a4e1dc7b5
ms.contentlocale: pt-br
ms.lasthandoff: 06/17/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>Habilitar coleta de dados na Central de Segurança do Azure

> [!NOTE]
> A partir do início de junho de 2017, a Central de Segurança usará o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, veja [Migração da Plataforma Central de Segurança do Azure](security-center-platform-migration.md). As informações deste artigo representam a funcionalidade da Central de Segurança após a transição para o Microsoft Monitoring Agent.
>
>

A Central de Segurança coleta dados de suas máquinas virtuais (VMs) para avaliar o estado de sua segurança, fornecer recomendações de segurança e alertar sobre ameaças. Quando você acessa pela primeira vez a Central de Segurança, você tem a opção de habilitar a coleta de dados para em todas as VMs em sua assinatura. Se a coleta de dados estiver desabilitada, a Central de Segurança recomendará que você ligue a coleta de dados na política de segurança dessa assinatura.

Quando a coleta de dados é habilitada, a Central de Segurança do Azure provisiona o Microsoft Monitoring Agent em todas as máquinas virtuais do Azure existentes com suporte e em quaisquer novas máquinas virtuais criadas. O Microsoft Monitoring Agent realiza varreduras em busca de várias configurações relacionadas à segurança. Além disso, o sistema operacional cria entradas de log de eventos. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP, usuário registrado e ID do locatário. O Microsoft Monitoring Agent lê as entradas e configurações do log de eventos e copia os dados para seu espaço de trabalho para análise. O Microsoft Monitoring Agent também copia os arquivos de despejo de memória para seu espaço de trabalho.

Se você estiver usando a Camada gratuita da Central de Segurança, você poderá desabilitar a coleta de dados de máquinas virtuais desligando a coleta de dados na política de segurança. Desabilitar a coleta de dados limita as avaliações de segurança para as VMs. Para saber mais, consulte [Desabilitar a coleta de dados](#disabling-data-collection). Os instantâneos de disco da VM e a coleção de artefatos são habilitados mesmo que a coleta de dados tenha sido desabilitada. A coleta de dados é necessária para as assinaturas na camada Standard da Central de Segurança.

> [!NOTE]
> Saiba mais sobre os [tipos de preço](security-center-pricing.md) Gratuito e Standard da Central de Segurança.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo. Este documento não é um guia passo a passo.
>
>

1. Na folha **Recomendações**, selecione **Habilitar coleta de dados em assinaturas**.  Isso abre a folha **Ativar a coleta de dados**.
   ![Folha de recomendações][2]
2. Na folha **Ativar a coleta de dados** , selecione sua assinatura. A folha **Política de segurança** para essa assinatura é aberta.
3. Na folha **Política de segurança**, selecione **Ativado** em **Coleta de dados** para coletar logs automaticamente. A ativação da coleta de dados provisiona a extensão de monitoramento em todas as VMs atuais e novas com suporte na assinatura.
4. Selecione **Salvar**.
5. Selecione **OK**.

## <a name="disabling-data-collection"></a>Desabilitar a coleta de dados
Se você estiver usando a camada Gratuita da Central de Segurança, você poderá desabilitar a coleta de dados de máquinas virtuais a qualquer momento desligando a coleta de dados na política de segurança. A coleta de dados é necessária para as assinaturas na camada Standard da Central de Segurança.

1. Volte para a folha **Central de Segurança** e selecione o bloco **Política**. Isso abre a folha **Política de segurança – definir política por assinatura**.
   ![Selecione o bloco de política][5]
2. Na folha **Política de segurança – definir política por assinatura**, selecione a assinatura para a qual você deseja desabilitar a coleta de dados.
3. A folha **Política de segurança** para essa assinatura é aberta.  Selecione **Desativado** em Coleta de dados.
4. Selecione **Salvar** na faixa de opções.

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como implementar a recomendação da Central de Segurança "Habilitar a coleta de dados". Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) : saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md) – saiba como os dados são gerenciados e protegidos na Central de Segurança do Azure.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

