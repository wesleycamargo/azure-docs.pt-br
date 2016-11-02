<properties
   pageTitle="Adicionar um Firewall do Aplicativo Web na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação da Central de Segurança do Azure **Adicionar um Firewall do Aplicativo Web** e **Finalizar a proteção do aplicativo**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# Adicionar um Firewall do Aplicativo Web na Central de Segurança do Azure

A Central de Segurança do Azure pode recomendar que você adicione um WAF (Firewall do Aplicativo Web) de um parceiro da Microsoft para proteger seus aplicativos Web. Este documento guiará você por um exemplo de como fazer isso.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## Implementar a recomendação

1. Na folha **Recomendações**, selecione **Proteger o aplicativo Web usando o Firewall do Aplicativo Web**. ![Aplicativo Web protegido][1]

2. Na folha **Proteger seus aplicativos Web usando o Firewall do Aplicativo Web**, selecione um aplicativo Web. A folha **Adicionar um Firewall do Aplicativo Web** é aberta. ![Adicione um firewall do aplicativo Web][2]
3. Você pode optar por usar um Firewall do Aplicativo Web existente, se disponível, ou criar um novo. Neste exemplo não existem WAFs disponíveis, por isso vamos criar um novo WAF.

4. Para criar um novo WAF, selecione uma solução da lista de parceiros integrados. Selecione **Firewall do Aplicativo Web Barracuda**.
5. A folha **Firewall do Aplicativo Web Barracuda** se abre e fornece informações sobre a solução do parceiro. Selecione **Criar** na folha de informações. ![Folha Informações do firewall][3]

6. A folha **Novo Firewall do Aplicativo Web** é aberta e você pode executar as etapas de **Configuração da VM** e fornecer **Informações do WAF**. Selecione **Configuração da VM**.

7. Na folha **Configuração da VM**, você deve inserir as informações necessárias para criar a máquina virtual que executará o WAF. ![Configuração da VM][4]
8. Volte para a folha **Novo Firewall do Aplicativo Web** e selecione **Informações do WAF**. Na folha **Informações do WAF**, você configura o WAF em si. A Etapa 7 permite configurar a máquina virtual na qual o WAF será executado, e a Etapa 8 permite provisionar o WAF em si.

## Finalizar a proteção do aplicativo

1. Volte para a folha **Recomendações**. Uma nova entrada foi gerada depois que você criou o WAF, chamada **Finalizar a proteção do aplicativo**. Essa entrada informa o que é necessário para concluir o processo de conectar o WAF dentro da Rede Virtual do Azure para que ele possa proteger o aplicativo. ![Finalizar a proteção do aplicativo][5]

2. Selecione **Finalizar a proteção do aplicativo**. Uma nova lâmina é aberta. Você pode ver que há um aplicativo Web que precisa ter seu tráfego redirecionado.
3. Selecione o aplicativo Web. Uma folha será aberta com etapas para concluir a configuração de firewall do aplicativo Web. Conclua as etapas e selecione **Restringir o tráfego**. A Central de Segurança realizará então as estruturas para você. ![Restringir tráfego][6]

> [AZURE.NOTE] Você pode proteger vários aplicativos Web na Central de segurança adicionando-os às suas implantações do WAF existentes. Dispositivos WAF (criados usando o modelo de implantação do Gerenciador de Recursos) precisam ser implantados em uma rede virtual separada. Dispositivos WAF (criados usando o modelo de implantação clássico) são restritos ao uso de um grupo de segurança de rede. No futuro, esse suporte será estendido para uma implantação totalmente personalizada de um dispositivo WAF (clássica). Saiba mais sobre os [modelos de implantação clássica e do Gerenciador de Recursos](../azure-classic-rm.md) para recursos do Azure.

Os logs daquele WAF agora estão totalmente integrados. A Central de Segurança pode iniciar a coleta e a análise dos logs automaticamente para revelar alertas de segurança importantes para você.

## Consulte também

Este documento mostrou como implementar a recomendação da Central de Segurança "Adicionar um aplicativo Web". Para saber mais sobre como configurar um Firewall do Aplicativo Web, consulte o seguinte:

- [Configurando um WAF (Firewall do Aplicativo Web) para Ambiente do Serviço de Aplicativo](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço de localização.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]: ./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png

<!---HONumber=AcomDC_0803_2016-->