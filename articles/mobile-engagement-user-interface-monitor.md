<properties 
   pageTitle="Interface do usuário do Mobile Engagement do Azure - Monitoramento" 
   description="Visão geral da Interface de usuário para a seção de Monitoramento do Mobile Engagement do Azure" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Mobile Engagement do Azure - Interface do usuário

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">Introdução</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navegação</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Página inicial</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">Minha Conta</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Análise</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Monitoramento</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Reach</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segmentos</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Painel</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Configurações</a>
</div>

# Monitoramento
 
A seção de Monitoramento da interface do usuário fornece informações de análise em tempo real e permite que você defina alertas quando os limites são atingidos para a maioria das mesmas informações que está disponíveis historicamente na seção "Análise" da interface do usuário. O "Glossário" em "Conceitos" possui definições de termos e abreviações na análise e monitoramento, como os seguintes: Usuário ativo, novo usuário, usuário retidos, sessão, gráficos de caminho do usuário, Mapa de usuários, Rastreamento de URLs, tendências, atividade, eventos, trabalho, erro, informações adicionais, falha e informações do aplicativo.

**Consulte também:** 

-  [Conceitos - Glossário][Link 6], [Guia solução de problemas - Análise][Link 2]

## Monitoramento - Sessões, trabalhos, eventos, erros e falhas:
Você pode ver quantos usuários estão atualmente na sessão e em determinadas telas ou executar ações específicas. Você pode exibir a atividade do usuário dividida por sessões, trabalhos, eventos, erros e falhas. Você pode ver as informações atuais e mostrar as informações da última hora, dia ou semana. Você pode ver todas as informações em cada categoria ou classificar pela sessão específica, trabalho, evento, erro e falha.  Monitoramento em tempo real é útil para usar durante eventos como uma campanha de envio para ver se há um aumento na ação direto após o envio da notificação de envio. 
 
![Monitor1][14]  

## Solução de problemas com Monitoramento - Eventos - Detalhes
Gerando um evento em seu aplicativo do dispositivo de teste e encontrando-o em Monitoramento - Eventos - Detalhes é uma das maneiras mais fáceis de encontrar o ID do dispositivo para o dispositivo de teste e confirmar que integração do Mobile Engagement do Azure de análise, monitoramento e segmentos está trabalhando em seu aplicativo. Uma vez que você tiver a ID do dispositivo do seu dispositivo de teste, você poderá adicioná-lo para seus dispositivos de teste em "Minha conta - dispositivos". Se você não puder gerar um evento, certifique-se de que o Mobile Engagement do Azure está integrado corretamente com o SDK em seu aplicativo do Android/iOS/Web/Windows/Windows Phone.

**Consulte também:** 

-  [Documentação do SDK][Link 5]

![Monitor2][15]  

## Solução de problemas com o Monitoramento - Falhas - Detalhes:
Você pode revisar informações sobre falhas sobre seu aplicativo a partir de Monitoramento - Falhas - Detalhes para ajudar a determinar por que seu aplicativo está falhando. Você também deve procurar problemas conhecidos com cada versão do SDK nas notas de versão para cada versão do SDK para Android/iOS/Web/Windows/Windows Phone. 

**Consulte também:** 

-  [Documentação do SDK - Notas de versão][Link 5]

![Monitor3][16] 

## Monitoramento - Alertas:
Você também pode especificar condições para alertas que serão enviadas automaticamente para você por email ou mensagem instantânea. (Todos os serviços compatíveis com XMPP como GTalk do Google ou iChat da Apple são suportados). Os alertas são baseados em um limite predefinido de detecção maior que (>) ou menor que (<) um número específico de sessões, trabalhos, eventos, erros ou falhas por segundo, minuto ou hora. Os alertas podem monitorar todas as atividades de um determinado tipo, ou apenas monitorarem uma atividade específica de trabalho, evento ou erro. Você também pode especificar uma taxa de detecção mínima, que é a quantidade mínima de tempo que irá separar duas notificações para o mesmo alerta verificar se quando o alerta for disparado, você nunca receberá mais de 1 notificação a cada X minutos.
 
![Monitor4][17]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
