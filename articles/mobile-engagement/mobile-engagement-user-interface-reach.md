<properties 
   pageTitle="Mobile Engagement do Azure - Interface do usuário - Reach" 
   description="Como entrar em contato com os usuários do seu aplicativo com notificações por push usando o Mobile Engagement do Azure" 
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


# Como entrar em contato com os usuários do seu aplicativo com notificações por push
A seção Reach da interface do usuário é a ferramenta de gerenciamento de campanha Push onde você pode criar/editar/ativar/finalizar/monitorar e obter estatísticas sobre campanhas de notificação de envio e os recursos que também podem ser acessados via a API do Reach (e alguns elementos do nível baixo API de envio). Lembre-se de que, se você estiver usando as APIs ou a interface do usuário, você precisará integrar o Mobile Engagement do Azure e o Reach em seu aplicativo em cada plataforma com o SDK antes que você possa usar as campanhas Reach.

### Consulte também
-  [Documentação da API - API Reach][Link 4], [Documentação da API - API Push][Link 4], [Guia de solução de problemas - Push/Reach][Link 23]
-  [Reach - Campanha][Link 27], [Reach - Critério][Link 28], [Reach - Conteúdo][Link 29], [Reach - Passo a passo][Link 3]
 
## Quatro tipos de notificações por Push
1.    Anúncios - permitem que sejam enviadas mensagens de anúncio para usuários que redirecioná-las para outro local dentro de seu aplicativo ou para enviá-las para uma página da Web ou armazenamento fora de seu aplicativo. 
2.    Pesquisas - permitem coletar informações dos usuários finais ao fazer perguntas a eles.
3.    Envio de dados - permite enviar um arquivo de dados binário ou base64. As informações contidas em um envio de dados são enviadas ao seu aplicativo para modificar a experiência atual dos usuários em seu aplicativo. Seu aplicativo precisa ser capaz de processar os dados em um envio de dados.
4.    Lado a lado (Windows Phone somente) - permite que você use o serviço de notificação por push da Microsoft (MPNS) para enviar envios nativos do Windows que contenham os dados XML. (Suporte desde a versão 0.9.0 do SDK. A carga final para o lado a lado não pode exceder a 32 quilo bytes).

### Consulte também
-  [Conceitos - Glossário][Link 6]

## Três categorias de estatísticas em tempo real mostradas para cada campanha:
1.    Enviadas - quantos envios foram feitos com base nos critérios especificados na campanha. 
2.    Respondidas - quantos usuários reagiram à notificação ao abri-la fora do aplicativo ou fechá-la no aplicativo. 
3.    Acionadas - quantos usuários clicaram no link na notificação para serem redirecionados para um novo local no aplicativo, para uma loja ou um navegador da Web. 

> Observação: Mais estatísticas detalhadas de campanha estão disponíveis nas estatísticas de API do Reach

### Consulte também
-  [Conceitos - Glossário][Link 6], [Documentação - API Reach - Estatísticas][Link 4]


## Detalhes da campanha
Você pode editar, clonar, excluir ou ativar campanhas que ainda não foram ativadas passando o mouse sobre os nomes ou você pode clicar para abri-las. Você pode clonar campanhas que já foram ativadas passando o mouse sobre os nomes ou você pode clicar para abri-las. No entanto, você não pode alterar uma campanha depois que ela tiver sido ativada.
 
![Reach1][18]

## Comentários do Reach
Você pode alternar de exibição de detalhes para a de estatísticas de uma campanha aberta que já foi ativada e alternar de exibição das estatísticas simples para avançada para exibir informações mais detalhadas (dependendo de suas permissões). Você também pode usar as informações de comentários do Reach de uma campanha anterior como critérios de direcionamento em uma nova campanha. As estatísticas dos comentários do Reach também podem ser obtidas com as "Estatísticas" da API do Reach. Você também pode personalizar o público-alvo de suas campanhas de envio com base em campanhas anteriores.


### Consulte também 
-  [Documentação da interface do usuário - Reach - Nova campanha de Push][27], [Documentação da API - API do Reach - Estatísticas][Link 4]

![Reach2][19]

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
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
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
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
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
 

<!---HONumber=August15_HO6-->