---
title: "Guia de solução de problemas do Mobile Engagement do Azure - SDK"
description: "Solucionando problemas de integração do SDK no Mobile Engagement do Azure"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: de265cf1-2f88-43ef-8616-156ada5be7b6
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: dd898a35c64e350fafbf20b4de42a23eecf3f29b


---
# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Guia de solução de problemas de integração do SDK
Estes são os possíveis problemas que podem ser encontrados na maneira como o Mobile Engagement do Azure se integra ao seu aplicativo.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>Problemas SDK descobertos por uma falha em outra área do aplicativo
### <a name="issue"></a>Problema
* Falha de coleta de dados da interface do usuário (em Análise, Monitoramento, Segmentação ou Painéis).
* Falhas de envios por push (os envios por push não funcionam no aplicativo, fora do aplicativo ou ambos).
* Falhas de Recursos Avançados (Rastreamento, Localização geográfica ou Envios por Push de plataformas específicas que não funcionam).
* Falhas de API (geralmente, as falhas de API são silenciosas, sem mensagens de erro).
* Falhas de Serviço (nada no Mobile Engagement do Azure funciona para seu aplicativo).

### <a name="causes"></a>Causas
* A maioria dos problemas com o SDK do Mobile Engagement do Azure que precisam ser resolvidos será descoberta por uma falha em seu aplicativo (por exemplo, uma falha de coleta de dados da interface do usuário, uma falha de envio por push, uma falha de recurso avançado, uma falha de API, falhas do Aplicativo ou uma interrupção de serviço aparente).  
* Se um recurso específico do Mobile Engagement do Azure nunca funcionou em seu aplicativo antes, você precisará concluir a integração. 
* Se um recurso específico do Mobile Engagement do Azure estava funcionando e foi e interrompido, talvez seja necessário atualizar para a versão mais recente usando o SDK do Mobile Engagement do Azure. Lembre-se de que há uma versão diferente do SDK do Mobile Engagement do Azure para cada plataforma suportada pelo Mobile Engagement do Azure (Android, iOS, Windows e Windows Phone).

#### <a name="sdk-integration"></a>Integração do SDK
* O Mobile Engagement do Azure não está corretamente integrado no SDK (Análise).
* O Alcance não está corretamente integrado no SDK (Envio por Push No Aplicativo e Fora do Aplicativo).
* Certificado expirado ou incorreto PROD versus DEV (somente iOS).
* GCM ou ADM não estão corretamente integrados no SDK (somente Android - Envios por Push Específicos do Serviço).
* O rastreamento não está corretamente integrado no SDK (instalar rastreamento de loja).
* Localização lenta ou Localização de GPS não estão corretamente integradas no SDK (direcionamento por localização geográfica).

**Consulte também:**

* [Documentação do SDK - Guias de integração][Link 5] 
* [Guia de solução de problemas - Push][Link 23]

#### <a name="sdk-upgrade"></a>Atualização do SDK
* É necessário atualizar o SDK para resolver problemas com versões anteriores do SDK (geralmente relacionadas às versões mais recentes do sistema operacional do dispositivo).
* Desinstale todas as versões anteriores do aplicativo do dispositivo e reinstale a versão mais recente do seu aplicativo, registre novamente o ID do dispositivo da interface do usuário do Mobile Engagement do Azure para confirmar que o dispositivo está usando a versão mais recente do seu aplicativo.

**Consulte também:**

* [Documentação do SDK - Notas de versão](http://go.microsoft.com/fwlink/?LinkId= 525554) 
* [Documentação do SDK - Guias de atualização](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>Outros problemas no SDK
* Erros no manifesto do aplicativo “Androidmanifest.xml” podem fazer o Mobile Engagement do Azure parar de funcionar (somente Android).
* Um problema comum envolvendo a integração do SDK e o uso da API é confundir a chave do SDK com a chave de API.

**Consulte também:**

* [Conceitos - Glossário][Link 6]

## <a name="advanced-coding-issues"></a>Problemas de codificação avançados
### <a name="issue"></a>Problema
* O código específico da plataforma não relacionado diretamente ao Mobile Engagement do Azure pode causar problemas no iOS, no Android e no Windows Phone.

### <a name="causes"></a>Causas
* Muitos problemas de codificação avançada com o Mobile Engagement do Azure são causados por código específico de plataforma escrito incorretamente não diretamente relacionado ao Mobile Engagement do Azure. Você precisará consultar a documentação específica da plataforma para a qual está desenvolvendo além da documentação do Mobile Engagement do Azure (Android, iOS, Web, Windows e Windows Phone).
* “Categorias” não corretamente configuradas impedem a vinculação de uma notificação para outro local dentro ou fora do aplicativo (somente Android). 
* “UIKit.framework” não definido como “opcional” em seu código do iOS mostra um “Erro de símbolo não encontrado” e/ou falha em dispositivos mais antigos do iOS (somente iOS).
* Os certificados expirados ou que não estejam usando  corretamente a versão DEV ou Prod do certificado causam problemas de envio por push (somente iOS).
* Existem algumas limitações inerentes às plataformas que o Mobile Engagement do Azure não pode controlar (como por exemplo, o funcionamento do system center para envios por push no Android e no iOS).
* O Mobile Engagement do Azure publica uma lista completa dos pacotes internos usados pelo Mobile Engagement do Azure para iOS e Android para referência. Tenha em mente que alguns recursos do Mobile Engagement do Azure são específicos para a plataforma (Android, iOS, Web, Windows e Windows Phone).

### <a name="see-also"></a>Consulte também
* [Guia de solução de problemas - Push][Link 23] 
* [Documentação do SDK - Notas de versão][Link 5]
* [Documentação do SDK - Guias de atualização][Link 5]

## <a name="application-crashes"></a>Falhas do aplicativo
### <a name="issue"></a>Problema
* O aplicativo falha no dispositivo dos usuários finais.

### <a name="causes"></a>Causas
* As informações de falha podem ser exibidas na *interface do usuário de análise* ou na *API de análise*
* Você pode encontrar a ID de dispositivo do seu dispositivo de teste e executar a mesma ação que causou a falha do aplicativo para um usuário final para ajudar a identificar a causa de sua falha.
* Problemas conhecidos com o SDK do Mobile Engagement do Azure que causam falhas de aplicativos são às vezes resolvidos com a atualização para a versão mais recente do SDK. Verifique as notas de versão sobre a plataforma ao investigar falhas.

### <a name="see-also"></a>Consulte também
* [Documentação do SDK - Notas de versão][Link 5]
* [Documentação do SDK - Guias de atualização][Link 5]

## <a name="app-store-upload-failures"></a>Falhas de carregamento nas lojas de aplicativos
### <a name="issue"></a>Problema
* Erros relacionados ao upload da versão mais recente do seu aplicativo para Apple, Google ou Windows Store.

### <a name="causes"></a>Causas
* As lojas de aplicativos algumas vezes bloqueiam os aplicativos com determinados recursos habilitados (por exemplo, a Apple Store impede o uso de IDFV em aplicativos no repositório e a GooglePlay store impede o compartilhamento de informações do aplicativo entre os aplicativos). 
* Verifique as notas de versão sobre a plataforma e a versão atual do SDK se você tiver dificuldade para carregar um aplicativo na loja.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
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




<!--HONumber=Nov16_HO3-->


