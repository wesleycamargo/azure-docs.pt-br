<properties 
   pageTitle="Interface de usuário do Mobile Engagement do Azure - Página inicial" 
   description="Visão geral da Interface de usuário para a seção da Página inicial do Mobile Engagement do Azure" 
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

# Página inicial
 
A seção da página inicial da interface do usuário contém a lista de todos os aplicativos em Meus aplicativos, bem como a capacidade de conceder a outros usuários permissões para seus aplicativos. Qualquer pessoa pode acessar a página inicial da interface do usuário ao criar uma conta, mas você precisa conceder aos outros usuários permissão para que eles tenham acesso aos seus aplicativos personalizados em Meus projetos.

**Consulte também:** 

-  [Solução de problemas - Serviço][Link 2]
 
## Meus Aplicativos:
 
![Home1][2]

Esta visão geral rápida dos seus aplicativos permite selecionar quais aplicativos você gostaria de abrir para exibir a faixa de opções detalhada. Você pode clicar no nome do seu aplicativo para retornar para o local da faixa de opções visitado recentemente em seu aplicativo ou clicar no ícone de engrenagem para ir diretamente para a página "Configurações" do seu aplicativo. Você pode pesquisar, filtrar ou classificar as informações exibidas nas tabelas de aplicativos. Você também pode arrastar e soltar os cabeçalhos de coluna para alterar a ordem. 
 
A visão geral dos seus aplicativos inclui:

- Total de usuários (Número Total de usuários)
- Nova tendência de usuários (evolução de novos usuários sobre as últimas duas semanas)
- Usuários ativos (número de usuários ativos nos últimos 30 dias)
- Tendência de usuários ativos (evolução de usuários ativos sobre as últimas duas semanas)
 
Você também pode ver um gráfico de comparação de seus aplicativos.

- Mostrar gráfico de comparação (pode ser usado para mostrar os dados do aplicativo no formato de gráfico)
- Caixas de seleção (adicionar/remover este aplicativo para o/do gráfico de comparação)
 
![Home2][3]

## Meus Projetos:
 
![Home5][6]

Você pode usar os projetos para agrupar seus aplicativos e conceder permissões aos seus aplicativos. O botão de Novos projetos permite que você crie um novo projeto, inserindo apenas um "nome" e uma "Descrição" do novo projeto. Quando um projeto é criado, você pode clicar no nome do projeto para editar o nome e a descrição do produto e selecionar todos os aplicativos que você deseja ver neste projeto. Você também pode excluir este projeto, o que não destruirá os aplicativos referenciados por ele. No entanto, você perderá o acesso a todos os aplicativos que você não possui e que não são acessíveis a partir de outro projeto. Portanto, tenha cuidado! 
Você também pode convidar um usuário para o seu projeto com base em seu endereço de email. Os usuários precisam ter uma conta já criada no Mobile Engagement do Azure antes que você possa conceder permissões a eles. 

**As funções incluem:**

- Visualizador: Um visualizador é um usuário que só pode visualizar os aplicativos associados a um Projeto. Um visualizador pode acessar os dados da análise e monitoramento e examinar os resultados de Reach. Um visualizador não pode alterar qualquer informação, nem gerenciar Aplicativos ou Usuários. Um visualizador não pode criar ou alterar o status da campanha Reach.
- Developer: Um desenvolvedor é um usuário que pode fazer tudo que um Visualizador pode fazer, bem como gerenciar aplicativos. Um desenvolvedor pode ativar e desativar aplicativos, alterar informações de aplicativos (como o pacote e assinatura) e criar campanhas Reach. Um desenvolvedor não pode gerenciar usuários. 
- Administrador: Um administrador é um usuário que pode fazer tudo o que um desenvolvedor pode fazer, bem como gerenciar usuários. Um administrador pode convidar usuários para ingressar em um projeto, pode alterar as funções de usuário e pode alterar informações do projeto. Permissões de nível de aplicativo também podem ser definidas em "configurações".
 
**Consulte também:** 

-  [Documentação de interface do usuário - Configurações][Link 20]

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
