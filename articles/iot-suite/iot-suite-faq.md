---
title: Perguntas frequentes sobre o Pacote do IoT do Azure | Microsoft Docs
description: Perguntas frequentes sobre o IoT Suite
services: ''
suite: iot-suite
documentationcenter: ''
author: aguilaaj
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2016
ms.author: araguila

---
# Perguntas frequentes sobre o IoT Suite
### Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em excluir em uma solução pré-configurada no site azureiotsuite.com?
* Se você excluir a solução pré-configurada no site [azureiotsuite.com][lnk-azureiotsuite], excluirá todos os recursos provisionados na criação da solução pré-configurada; se você tiver adicionado outros recursos ao grupo de recursos, eles também serão excluídos.
* Se você excluir o grupo de recursos no [portal do Azure][lnk-azure-portal], excluirá apenas os recursos desse grupo de recursos; também será necessário excluir o aplicativo do Active Directory do Azure associado à solução pré-configurada no [portal clássico do Azure][lnk-classic-portal].

### Quantas instâncias do Hub IoT posso provisionar em uma assinatura?
Dez. Você pode criar um [tíquete de suporte do Azure][link-azuresupportticket] para aumentar esse limite mas, por padrão, só será possível provisionar dez Hubs IoT por assinatura, como descrito em [limites de assinatura do Azure][link-azuresublimits]. Como resultado, uma vez que cada solução pré-configurada provisiona um novo Hub IoT, você só poderá provisionar até dez soluções pré-configuradas em uma determinada assinatura.

### Quantas instâncias do Banco de Dados de Documentos posso provisionar em uma assinatura?
Cinquenta. Você pode criar um [tíquete de suporte do Azure][link-azuresupportticket] para aumentar esse limite, mas por padrão, só será possível provisionar cinquenta instâncias do Banco de Dados de Documentos por assinatura.

### Quantas APIs do Bing Mapas Gratuitas posso provisionar em uma assinatura?
Duas. Você só pode criar dois Bing Mapas de Transações Internas de Nível 1 para planos Enterprise em uma assinatura do Azure. A solução de monitoramento remoto é provisionada por padrão com o plano Transações Internas de Nível 1. Como resultado, você só poderá provisionar até duas soluções de monitoramento remotas em uma assinatura sem modificações.

### Tenho uma implantação de solução de monitoramento remoto com um mapa estático, como posso adicionar um mapa interativo do Bing?
1. Obtenha a API do Bing Maps para Enterprise QueryKey no [portal do Azure][lnk-azure-portal]\:
   
   1. Navegue até o Grupo de Recursos onde está a API do Bing Maps para Empresa no [portal do Azure][lnk-azure-portal].
   2. Clique em Todas as Configurações e em Gerenciamento de Chave.
   3. Você perceberá duas chaves: MasterKey e QueryKey. Copie o valor de QueryKey.
      
      > [!NOTE]
      > Você não tem uma conta da API do Bing Maps para Empresa? Crie uma no [portal do Azure][lnk-azure-portal] clicando em + Novo, procurando pela API do Bing Mapas para Empresas e seguindo os prompts.
      > 
      > 
2. Obtenha o código mais recente do [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Execute uma implantação local ou em nuvem seguindo as diretrizes de implantação de linha de comando na pasta /docs/ no repositório.
4. Depois de executar uma implantação local ou em nuvem, procure na pasta raiz pelo arquivo *.user.config criado durante a implantação. Abra esse arquivo em um editor de texto.
5. Altere a linha a seguir a fim de incluir o valor que você copiou de QueryKey:
   
   `<setting name="MapApiQueryKey" value="" />`

### Posso criar uma solução pré-configurada se possuo o Microsoft Azure para DreamSpark?
Neste momento, você não pode criar uma solução pré-configurada com uma conta do [Microsoft Azure para DreamSpark][lnk-dreamspark]. No entanto, você pode criar uma [conta de avaliação gratuita do Azure][lnk-30daytrial] em apenas alguns minutos que permitirá a você criar uma solução pré-configurada.

### Como posso excluir um locatário do AAD?
Consulte a postagem do blog de Eric Golpe, [Passo a passo da exclusão de um locatário do AD do Azure][lnk-delete-aad-tennant].

## Próximas etapas
Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Visão geral da solução pré-configurada de manutenção preditiva][lnk-predictive-overview]
* [Segurança IoT desde o início][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0928_2016-->