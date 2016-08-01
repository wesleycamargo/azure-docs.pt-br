<properties
   pageTitle="Adicionar um Firewall de Última Geração na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Adicionar um Firewall de Última Geração**."
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

# Adicionar um Firewall de Última Geração na Central de Segurança do Azure

A Central de Segurança do Azure pode recomendar que você adicione um Firewall de Última Geração (NGFW) de um parceiro da Microsoft para aumentar suas proteções de segurança. Este documento guiará você por um exemplo de como fazer isso.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## Implementar a recomendação

1. Na folha **Recomendações** , selecione **Adicionar um Firewall de Última Geração**. ![Adicionar um Firewall de Última Geração][1]

2. Na folha **Adicionar um Firewall de Última Geração**, selecione um ponto de extremidade. ![Selecionar um ponto de extremidade][2]

3. Uma segunda folha **Adicionar um Firewall de Última Geração** será aberta. Você pode optar por usar uma solução existente, se disponível, ou criar uma nova. Neste exemplo não existem soluções disponíveis, por isso, vamos criar um novo NGFW. ![Criar um novo Firewall de Última Geração][3]

4. Para criar um novo NGFW, selecione uma solução da lista de parceiros integrados. Neste exemplo, selecionaremos **Check Point**. ![Selecionar uma solução de Firewall de Última Geração][4]

5. A folha **Check Point** se abre e fornece informações sobre a solução do parceiro. Selecione **Criar** na folha de informações. ![Folha Informações do firewall][5]

6. A folha **Criar máquina virtual** é aberta. Nessa folha, você pode inserir as informações necessárias para criar uma máquina virtual que executará o NGFW. Siga as etapas e forneça as informações do NGFW necessárias. Selecione OK para aplicar. ![Criar uma máquina virtual para executar o NGFW][6]

## Próximas etapas

Este documento mostrou como implementar a recomendação da Central de Segurança para "Adicionar um Firewall de Última Geração". Para saber mais sobre NGFWs e a solução de parceiro da Check Point, consulte:

- [Firewall de Última Geração](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Check Point vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como definir as políticas de segurança.
- [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
- [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) -- saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png

<!---HONumber=AcomDC_0720_2016-->