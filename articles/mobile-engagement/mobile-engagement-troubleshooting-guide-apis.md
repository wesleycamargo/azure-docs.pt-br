---
title: "Guia de solução de problemas do Mobile Engagement do Azure - APIs"
description: "Guias de solução de problemas para o Mobile Engagement do Azure - APIs"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a7ae0a83046f2d67b790f672dcd3ae261987357a


---
# <a name="troubleshooting-guide-for-api-issues"></a>Guia de solução de problemas de API
Estes são os possíveis problemas que podem ser encontrados na maneira como administradores interagem com o Mobile Engagement do Azure por meio das APIs.

## <a name="syntax-issues"></a>Problemas de sintaxe
### <a name="issue"></a>Problema
* Erros de sintaxe na utilização da API (ou comportamento inesperado).

### <a name="causes"></a>Causas
* Problemas de sintaxe:
  * Verifique a sintaxe da API específica que você está usando para confirmar se a opção está disponível.
  * Um problema comum com o uso da API é confundir a API de Alcance e a API de Envio por Push (a maioria das tarefas deve ser executada com a API de Alcance em vez da API de Envio por Push). 
  * Outro problema comum com a integração do SDK e o uso da API é confundir a Chave do SDK e a Chave de API.
  * Os scripts que se conectam às APIs precisam enviar dados pelo menos a cada 10 minutos ou a conexão atingirá o tempo limite (comum especialmente em scripts de API do Monitor aguardando dados). Para evitar atingir o tempo limite, faça seu script enviar um ping XMPP a cada 10 minutos para manter ativa a sessão com o servidor.

### <a name="see-also"></a>Consulte também
* [Documentação da API][Link 4]
* [Informações do protocolo XMPP](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Não é possível usar a API para executar a mesma ação disponível na interface de usuário do Mobile Engagement do Azure
### <a name="issue"></a>Problema
* Uma ação que funciona da interface do usuário do Mobile Engagement do Azure não funciona da API do Mobile Engagement do Azure relacionada.

### <a name="causes"></a>Causas
* A confirmação de que você pode executar a mesma ação da interface do usuário do Mobile Engagement do Azure mostra que esse recurso do Mobile Engagement do Azure foi corretamente integrado ao SDK.

### <a name="see-also"></a>Consulte também
* [Documentação da Interface do Usuário][Link 1]

## <a name="error-messages"></a>Mensagens de erro
### <a name="issue"></a>Problema
* Códigos de erro de uso da API exibida em tempo de execução ou nos logs.

### <a name="causes"></a>Causas
* Veja uma lista composta de números de códigos de status comuns da API para referência e a solução de problemas preliminar:
  
        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Consulte também
* [Documentação da API – para erros detalhados sobre cada API específica][Link 4]

## <a name="silent-failures"></a>Falhas silenciosas
### <a name="issue"></a>Problema
* A ação da API falha sem nenhuma mensagem de erro exibida em tempo de execução ou nos logs.

### <a name="causes"></a>Causas
* Muitos itens serão desabilitados na interface do usuário do Mobile Engagement do Azure caso não tenham sido integrados corretamente, mas falharão de forma silenciosa a partir da API; dessa forma, lembre-se de testar a mesma funcionalidade da interface do usuário para ver se ela funciona.
* O Mobile Engagement do Azure e muitos recursos avançados do Mobile Engagement do Azure que você está tentando usar precisarão ser individualmente integrados ao seu aplicativo com o SDK em etapas distintas antes que você possa usá-los.

### <a name="see-also"></a>Consulte também
* [Guia de Solução de Problemas ‑ SDK][Link 25]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md




<!--HONumber=Feb17_HO3-->


