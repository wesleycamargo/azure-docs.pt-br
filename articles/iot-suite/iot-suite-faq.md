---
title: Perguntas frequentes sobre o Azure IoT Suite | Microsoft Docs
description: Perguntas frequentes sobre o IoT Suite
services: 
suite: iot-suite
documentationcenter: 
author: aguilaaj
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2016
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5cbbebfcc41ef8c45366c04ddd9a5697f7b91b61


---
# <a name="frequently-asked-questions-for-iot-suite"></a>Perguntas frequentes sobre o IoT Suite
### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em excluir em uma solução pré-configurada no site azureiotsuite.com?
* Se você excluir a solução pré-configurada no site [azureiotsuite.com][lnk-azureiotsuite], excluirá todos os recursos provisionados na criação da solução pré-configurada. Se você tiver adicionado outros recursos ao grupo de recursos, eles também serão excluídos. 
* Se você excluir o grupo de recursos no [portal do Azure][lnk-azure-portal], excluirá apenas os recursos desse grupo de recursos; também será necessário excluir o aplicativo do Azure Active Directory associado à solução pré-configurada no [portal clássico do Azure][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT posso provisionar em uma assinatura?
Dez. Você pode criar um [tíquete de suporte do Azure][link-azuresupportticket] para aumentar esse limite. Porém, por padrão, só pode provisionar dez Hubs IoT por assinatura, conforme descrito em [Limites de assinatura do Azure][link-azuresublimits]. Como resultado, uma vez que cada solução pré-configurada provisiona um novo Hub IoT, você só poderá provisionar até dez soluções pré-configuradas em uma determinada assinatura. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Banco de Dados de Documentos posso provisionar em uma assinatura?
Cinquenta. Você pode criar um [tíquete de suporte do Azure][link-azuresupportticket] para aumentar esse limite, mas por padrão, só será possível provisionar cinquenta instâncias do DocumentDB por assinatura. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs do Bing Mapas Gratuitas posso provisionar em uma assinatura?
Duas. Você só pode criar dois Bing Mapas de Transações Internas de Nível 1 para planos Enterprise em uma assinatura do Azure. A solução de monitoramento remoto é provisionada por padrão com o plano Transações Internas de Nível 1. Como resultado, você só poderá provisionar até duas soluções de monitoramento remotas em uma assinatura sem modificações.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Tenho uma implantação de solução de monitoramento remoto com um mapa estático, como posso adicionar um mapa interativo do Bing?
1. Obtenha a API do Bing Maps para Enterprise QueryKey no [portal do Azure][lnk-azure-portal]: 
   
   1. Navegue até o Grupo de Recursos onde está a API do Bing Maps para Empresa no [portal do Azure][lnk-azure-portal].
   2. Clique em Todas as Configurações e em Gerenciamento de Chave. 
   3. Você perceberá duas chaves: MasterKey e QueryKey. Copie o valor de QueryKey.
      
      > [!NOTE]
      > Você não tem uma conta da API do Bing Maps para Empresa? Crie uma no [portal do Azure][lnk-azure-portal] clicando em + Novo, procurando pela API do Bing Mapas para Empresas e seguindo os prompts.
      > 
      > 
2. Obtenha o código mais recente em [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Execute uma implantação local ou em nuvem seguindo as diretrizes de implantação de linha de comando na pasta /docs/ no repositório. 
4. Depois de executar uma implantação local ou em nuvem, procure na pasta raiz pelo arquivo *.user.config criado durante a implantação. Abra esse arquivo em um editor de texto. 
5. Altere a linha a seguir a fim de incluir o valor que você copiou de QueryKey: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar uma solução pré-configurada se possuo o Microsoft Azure para DreamSpark?
Neste momento, você não pode criar uma solução pré-configurada com uma conta do [Microsoft Azure para DreamSpark][lnk-dreamspark]. No entanto, você pode criar uma [conta de avaliação gratuita do Azure][lnk-30daytrial] em apenas alguns minutos que permitirá a você criar uma solução pré-configurada.

### <a name="how-do-i-delete-an-aad-tenant"></a>Como posso excluir um locatário do AAD?
Confira postagem de blog de Eric Golpe, [Passo a passo para a exclusão de um locatário do Azure AD][lnk-delete-aad-tennant].

## <a name="next-steps"></a>Próximas etapas
Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Visão geral da solução pré-configurada de manutenção preditiva][lnk-predictive-overview]
* [Segurança de IoT desde o início][lnk-security-groundup]

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



<!--HONumber=Nov16_HO3-->


