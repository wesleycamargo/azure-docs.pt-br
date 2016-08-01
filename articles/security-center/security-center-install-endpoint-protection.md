<properties
   pageTitle="Instalar o Endpoint Protection na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Instalar Endpoint Protection**."
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
   ms.date="07/15/2016"
   ms.author="terrylan"/>

# Instalar o Endpoint Protection na Central de Segurança do Azure

A Central de Segurança do Azure recomendará que você provisione um programa antimalware para suas VMs (máquinas virtuais) do Azure se o antimalware ainda não estiver habilitado. Essa recomendação se aplica somente às VMs do Windows.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## Implementar a recomendação

1. Na folha **Recomendações**, selecione **Instalar o Endpoint Protection**. ![Selecione Instalar o Endpoint Protection][1]

2. A folha **Instalar o Endpoint Protection** abre exibindo uma lista de máquinas virtuais sem antimalware habilitado. Selecione na lista as VMs em que você deseja instalar o antimalware e clique em **Instalar nas VMs**. ![Selecione VMs para instalar o antimalware][2]

3. A folha **Selecionar Endpoint Protection** será aberta para que você possa selecionar a solução antimalware que deseja usar. Neste exemplo, vamos selecionar **Antimalware da Microsoft**. ![Selecionar Endpoint Protection][3]

4. Informações adicionais sobre a solução antimalware são exibidas. Selecione **Criar**. ![Criar solução antimalware][4]

5. Insira as configurações necessárias na folha **Adicionar Extensão** e selecione **OK**. Para saber mais sobre as definições de configuração, confira [Configuração de Antimalware personalizada e padrão](../azure-security-antimalware.md#default-and-custom-antimalware-configuration).

O [Antimalware da Microsoft](../azure-security-antimalware.md) agora está ativo na VM selecionada.

## Próximas etapas

Este artigo mostrou como implementar a recomendação da Central de Segurança "Instalar Endpoint Protection". Para saber mais sobre como habilitar um programa antimalware no Azure, consulte o seguinte:

- [Microsoft Antimalware para Serviços de Nuvem e Máquinas Virtuais](../azure-security-antimalware.md): saiba como implantar antimalware da Microsoft.

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configuração de políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como definir as políticas de segurança.
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
- [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiro.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço de localização.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]: ./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]: ./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]: ./media/security-center-install-endpoint-protection/create-antimalware-solution.png

<!---HONumber=AcomDC_0720_2016-->