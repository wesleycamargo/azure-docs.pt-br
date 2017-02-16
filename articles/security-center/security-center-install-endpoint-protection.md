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
ms.date: 11/23/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b8a69d89f335c00c5ddc3c201e33a66e1dea1da5
ms.openlocfilehash: 6ccb5367b1c7c6cae7b9d35b6a5471c8edfe6f85


---
# <a name="install-endpoint-protection-in-azure-security-center"></a>Instalar o Endpoint Protection na Central de Segurança do Azure
A Central de Segurança do Azure recomendará que você provisione um programa antimalware para suas VMs (máquinas virtuais) do Azure se o antimalware ainda não estiver habilitado. Essa recomendação se aplica somente às VMs do Windows. Atualmente, essa recomendação verifica a presença do Windows Defender ou do TrendMicro Deep Security. Outras soluções de proteção do Endpoint Protection devem ser adicionadas no futuro.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Ela não é um guia passo a passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **Recomendações**, selecione **Instalar o Endpoint Protection**.
   ![Selecione Instalar o Endpoint Protection][1]
2. A folha **Instalar o Endpoint Protection** abre exibindo uma lista de máquinas virtuais sem antimalware habilitado. Selecione na lista as VMs em que você deseja instalar o antimalware e clique em **Instalar nas VMs**.
   ![Selecione VMs para instalar o antimalware][2]
3. A folha **Selecionar Endpoint Protection** será aberta para que você possa selecionar a solução antimalware que deseja usar. Neste exemplo, vamos selecionar **Antimalware da Microsoft**.
   ![Selecionar Endpoint Protection][3]
4. Informações adicionais sobre a solução antimalware são exibidas. Selecione **Criar**.
   ![Criar solução antimalware][4]
5. Insira as configurações necessárias na folha **Adicionar Extensão** e selecione **OK**. Para saber mais sobre as definições de configuração, veja [Configuração personalizada e padrão de antimalware](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

O [Microsoft Antimalware](../security/azure-security-antimalware.md) agora está ativo nas VMs selecionadas.

## <a name="see-also"></a>Confira também
Este artigo mostrou como implementar a recomendação da Central de Segurança "Instalar Endpoint Protection". Para saber mais sobre como habilitar um programa antimalware no Azure, consulte o seguinte:

* [Microsoft Antimalware para Serviços de Nuvem e Máquinas Virtuais](../security/azure-security-antimalware.md) – saiba como implantar o antimalware da Microsoft.

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configuração de políticas de segurança na Central de Segurança do Azure](security-center-policies.md) : saiba como definir as políticas de segurança.
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



<!--HONumber=Nov16_HO4-->


