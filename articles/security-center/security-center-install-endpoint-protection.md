---
title: "Instalar o Endpoint Protection na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Instalar Endpoint Protection**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: efb86a0ae362c30a6772c391a499154b7ae2a697
ms.contentlocale: pt-br
ms.lasthandoff: 07/12/2017

---
# <a name="install-endpoint-protection-in-azure-security-center"></a>Instalar o Endpoint Protection na Central de Segurança do Azure
A Central de Segurança do Azure recomenda que você instale a proteção de ponto de extremidade nas VMs (máquinas virtuais) do Azure caso ainda não esteja habilitada. Essa recomendação se aplica somente às VMs do Windows.

> [!NOTE]
> Esta implantação de exemplo usa o Microsoft Antimalware. Consulte a [Integração de parceiro na Central de Segurança do Azure](security-center-partner-integration.md#partners-that-integrate-with-security-center) para obter uma lista de parceiros integrados com a Central de Segurança.  
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Este documento não é um guia passo a passo.
>
>

1. Na folha **Recomendações**, selecione **Instalar o Endpoint Protection**.
   ![Selecione Instalar o Endpoint Protection][1]
2. A folha **Instalar o Endpoint Protection** é aberta, exibindo uma lista de VMs sem a proteção de ponto de extremidade. Selecione na lista as VMs em que você deseja instalar a proteção de ponto de extremidade e clique em **Instalar nas VMs**.
   ![Selecione as VMs nas quais instalar o Endpoint Protection][2]
3. A folha **Selecionar Endpoint Protection** será aberta para que você possa selecionar a solução de proteção de ponto de extremidade que deseja usar. Neste exemplo, vamos selecionar **Antimalware da Microsoft**.
   ![Selecionar Endpoint Protection][3]
4. Informações adicionais sobre a solução de proteção de ponto de extremidade são exibidas. Selecione **Criar**.
   ![Criar solução antimalware][4]
5. Insira as configurações necessárias na folha **Adicionar Extensão** e selecione **OK**. Para saber mais sobre as definições de configuração, veja [Configuração personalizada e padrão de antimalware](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

O [Microsoft Antimalware](../security/azure-security-antimalware.md) agora está ativo nas VMs selecionadas.

## <a name="see-also"></a>Confira também
Este artigo mostrou como implementar a recomendação da Central de Segurança "Instalar Endpoint Protection". Para saber mais sobre como habilitar o Microsoft Antimalware no Azure, consulte o seguinte documento:

* [Microsoft Antimalware para Serviços de Nuvem e Máquinas Virtuais](../security/azure-security-antimalware.md) – saiba como implantar o Microsoft Antimalware.

Para saber mais sobre a Central de Segurança, confira os seguintes documentos:

* [Configuração de políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como definir as políticas de segurança.
* [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png

