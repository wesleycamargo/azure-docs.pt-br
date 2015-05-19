<properties 
   pageTitle="Guia de Solução de Problemas do Mobile Engagement do Azure - Análise" 
   description="Solução para problemas de Análise, Monitoramento, Segmentação e Painel no Mobile Engagement do Azure" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>

# Guia de solução para problemas de Análise, Monitoramento, Segmentação e Painel

Estes são os possíveis problemas que podem ser encontrados em como o Mobile Engagement do Azure reúne informações sobre seus aplicativos, dispositivos e usuários.

## Informações Ausentes/Atrasadas

### Problema
- As informações ficam atrasadas ao aparecerem na Análise, Segmentação ou Painel.
- Faltam informações no Monitoramento.
- As informações estão ausentes na Análise, Segmentação ou Painel.
- Limite de segmentação atingido.

### Causas

- Você pode usar a API da Análise, API do Monitor e API dos Segmentos para verificar se todos os dados que faltam na interface do usuário são visíveis através das APIs.
- Se o SDK do Mobile Engagement do Azure não estiver integrado corretamente em seu aplicativo, você não conseguirá ver informações na Análise, Segmentação, Monitoramento ou Painéis.
- Os segmentos não podem ser alterados depois de criados; os segmentos só podem ser "clonados" \(copiados\) ou "destruídos" \(excluídos\). Os segmentos podem conter apenas 10 critérios.
- A melhor maneira de testar as informações que faltam no monitoramento \(Configurar um dispositivo de teste, desinstalar e/ou reinstalar o aplicativo no dispositivo de teste\).
- As informações são atualizadas a cada 24 horas para a Análise, Segmentação ou Painéis.
- As informações nos novos segmentos não podem ser exibidas até 24 horas após serem criadas \(mesmo que o segmento seja baseado nas informações anteriores\).
- Filtrar seus dados de análise na interface do usuário mostrará todos os exemplos desse tipo, independentemente da versão de seu aplicativo \(por exemplo, as "Falhas" filtradas pelo nome aparecerão a partir das versões 1 e 2 de seu aplicativo\).
- O período de tempo para a Análise baseia-se na data das configurações do dispositivo dos usuários, portanto, um usuário cujo telefone tem a data definida incorretamente pode aparecer no período de tempo incorreto.
- Não há dados no lado do servidor registrados quando você usa o botão para "testar" os pushes; os dados são registrados apenas para as campanhas por push reais.

### Consulte também

- [Guia de Solução de Problemas - SDK][Link 25], [Documentação da API][Link 4], [Documentação da Interface do Usuário - Segmentos][Link 18]

## Não é possível localizar os itens na interface do usuário

### Problema
- Não é possível criar segmentos com base em certos critérios incorporados ou de marca de informações do aplicativo personalizado.
- Não é possível localizar certos critérios incorporados ou de marca de informações do aplicativo personalizado na Análise, Monitoramento ou Painéis.
- Não é possível interpretar os dados na Análise, Monitoramento, Segmentação ou Painéis.

### Causas

- Alguns itens incorporados e marcas de informações do aplicativo só estão disponíveis como critérios por push, mas podem não estar adicionados a um segmento nem visíveis na Análise, Monitoramento ou Painel. 
- Para os itens incorporados e marcas de informações do aplicativo que não podem ser adicionados a um segmento, você precisará configurar uma lista de critérios de destino em cada campanha para executar a mesma função como o destino com base em um segmento.
- Consulte os menus contextuais nas seções Análise, Monitoramento, Segmentação e Painéis da interface do usuário do Mobile Engagement do Azure para obter mais ajuda e informações.

### Consulte também

- [Documentação da Interface do Usuário - Novos Critérios do Push e do Reach para o Público-Alvo][Link 28]
 
## Solucionar problemas de falhas

### Problema
- Falhas do Aplicativo que aparecem na Análise, Monitoramento ou Painel.

### Causas

- Para solucionar as Falhas do Aplicativo vistas na Análise, Monitoramento ou Painel, verifique as notas de versão para os problemas conhecidos com as versões anteriores do SDK.
- Para solucionar outras falhas do aplicativo, execute um evento a partir de um dispositivo de teste com o aplicativo instalado e procure a ID do dispositivo na seção "Monitor – Eventos" da interface do usuário do Mobile Engagement do Azure. Em seguida, execute o evento que está fazendo seu aplicativo falhar e pesquise informações adicionais na seção "Monitor – Falha" da interface do usuário do Mobile Engagement do Azure. 

### Consulte também

- [Conceitos - Perguntas Frequentes][Link 6], [Conceitos - Glossário][Link 6], [Documentação da Interface do Usuário][Link 1], [Documentação do SDK - Notas de Versão][Link 5], [Documentação do SDK - Guias de Atualização][Link 5]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
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

<!--HONumber=54-->