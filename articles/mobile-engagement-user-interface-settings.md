<properties 
   pageTitle="Mobile Engagement do Azure - Interface do usuário - Configurações" 
   description="Visão geral da interface de usuário para a seção configurações do Mobile Engagement do Azure" 
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

# Configurações
 
As opções de menu configurações disponíveis para diversos aplicativos, dependendo da plataforma e das permissões que você recebeu para o aplicativo. As configurações incluem o seguinte: Detalhes, projetos, Push nativo, velocidade de envio, SDK, rastreamento, informações do aplicativo, pressão comercial e permissões. Somente a opção de menu Informações do aplicativo na seção Configurações da interface do usuário contém elementos que podem ser gerenciados com o recurso de "Marca" da API do dispositivo. O "Glossário" em "Conceitos" contém as definições de termos e abreviações, como o seguinte: APNS, GCM, IDFA, API, SDK, chave de API, chave do SDK, ID do aplicativo, ID AppStore, planejar marca, ID de usuário, ID do dispositivo, representante do aplicativo, rastreamento de pilha e vinculação profunda.

**Consulte também:** 

- [Documentação da API - API do dispositivo][Link 4], [Conceitos - Glossário][Link 6], [Guia solução de problemas - serviço][Link 2]
 
  ![settings1][46]

## Detalhes:
Permite que você altere o nome e a descrição do seu aplicativo 
Exibe o proprietário do aplicativo e as permissões da função. 
Configuração de análise: Permite exibir ou alterar o dia de início das Semanas, o tempo de Retenção em dia(s)
 
  ![settings2][47]
 
## Projetos:
Permite selecionar todos os projetos em que você quer que o aplicativo apareça. 
Você também pode pesquisar um projeto e exibir o nome, descrição, proprietário e as permissões da função de qualquer projeto de que seu aplicativo faz parte.

**Consulte também:**

-    [Documentação da interface do usuário - página inicial][Link 13]
 
  ![settings3][48]

## Push nativo:
Permite o registro de um novo certificado ou a exclusão de um certificado existente para o uso com o envio nativo. 
O Push nativo permite o Mobile Engagement do Azure a enviar por push para o seu aplicativo a qualquer momento, mesmo quando ele não está em execução. 
Após fornecer as credenciais ou certificados para pelo menos um serviço de push nativo, pode-se selecionar "Sempre" ao criar de campanhas Reach, bem como usar o parâmetro "notificação" na API de envio.

**Consulte também:**

- [Documentação da API - API do Reach] [Link 4],[Documentação da API - API de envio] [Link 4], [Documentação do interface do usuário - Reach - nova campanha de envio][Link 17]
 
**Serviço de notificação por push da Apple (APNS)**

Para habilitar o envio nativo usando o serviço de notificação por push da Apple você precisará registrar seu certificado. Você precisará especificar o tipo de certificado como desenvolvimento (DES) ou de produção (PROD). Em seguida, você precisará carregar o certificado e a senha.

**Consulte também:**

- , [Documentação do SDK - iOS - Como preparar seu aplicativo para notificações por push da Apple][Link 5]
 
![settings4][49]
 
**Serviço de notificação por push do Windows (WPNS)**

Para habilitar o envio nativo usando o serviço de notificação do Windows, você deve fornecer as credenciais do seu aplicativo. 
Você precisará do seu identificador de segurança do pacote (SID) e da chave secreta.
 
![settings5][50]
 
**Push nativo do Android com o GCM ou ADM**

**Google Cloud Messaging para Android (GCM)**

Para habilitar o envio nativo usando o GCM, siga as instruções em http://developer.android.com/guide/google/gcm/gs.html. 
Em seguida, cole uma chave simples de API do servidor, configurada sem restrições de IP. 
Requer integração com o SDK para Android v1.12.0 +.

**Consulte também:**

- [Documentação do SDK - Android - como integrar o GCM][Link 5]
 
**Dispositivo Amazon Messaging para Android (ADM)**

Para habilitar o envio nativo usando o ADM, vé preciso fornecer < credenciais OAuth > que consiste em uma ID de cliente e o segredo do cliente (requer integração com o SDK para Android versão 2.1.0+).

**Consulte também:**

- [Documentação do SDK - Android - como integrar o ADM][Link 5]

![settings6][51]

## Velocidade de envio:
Mostra a velocidade de envio atual do seu aplicativo e permite que você defina a velocidade de envio do seu aplicativo. A velocidade de envio define o número máximo de envios por segundo que o módulo do Reach executará. Isso pode ser útil em situações onde seus próprios servidores estão sobrecarregados com muitas solicitações por segundo (/s) após a ativação de uma campanha.
 
  ![settings7][52]

## Rastreamento:
O recurso de rastreamento permite rastrear as origens das instalações de seus aplicativos Android e iOS. Ele permite que você saiba de onde os usuários transferiram o aplicativo (ou seja, de qual loja de aplicativo) e qual por qual fonte (ou seja, campanha publicitária, blog, site da web, email, SMS, etc.). O recurso de rastreamento do Mobile Engagement do Azure deve ser integrado ao seu aplicativo do SDK como uma etapa separada. 

**Consulte também:**

- [Documentação do SDK - Android - Como integrar][Link 5], [Documentação do SDK - iOS - Como integrar][Link 5]
 
**Lojas**

Permitem que você registre todas as lojas de onde seu aplicativo pode ser baixado com base em seus nomes e as URLs de download associadas. Isso permite que você crie os locais de rastreamento de URLs do host. As lojas podem ser criadas ou excluídas nessa página. Usando o ícone para criar um novo rastreamento de URL leva você até a página de rastreamento de URLs abordada a seguir. Há várias maneiras de rastrear de onde seus usuários trasnferiram o seu aplicativo:

-    Use um terceiro servidor Ad (o Mobile Engagement do Azure atualmente suporta o [SmartAd](http://smartadserver.fr/) e o [Surikate](http://www.surikate.com/).
-    Use um serviço de atribuição de terceiros (o Mobile Engagement do Azure atualmente suporta o [Mobile App Tracking]( http://www.mobileapptracking.com/) e o[Trademob](http://www.trademob.com/) também podem ter suporte em breve.)
-    Use uma referência de instalação de terceiros (o Mobile Engagement do Azure atualmente suporta o [Google Play Install Referrer](https://developers.google.com/app-conversion-tracking/docs/third-party-trackers/) - apenas para Android).
-    Use relatórios manuais.
 
  ![settings8][53]
 
**Campanhas publicitárias**
Permitem que você crie novas campanhas publicitárias que consistem em um nome de servidor do Ad, uma ID de campanha e a fonte de onde seu aplicativo pode ser transferido. 
 
  ![settings9][54]
 
**URLs de rastreamento**
Permite que você crie uma URLs de rastreamento usar como URLs de destino em suas fontes (campanha publicitária, blog, site da web, email, SMS, etc.) que podem redirecionar os usuários para as lojas de onde eles podem transferir o seu aplicativo. Também permite que você exiba o rastreamento de todas as URLs que você já criou. Uma URL de rastreamento pode ser usada como a URL da ação de uma campanha publicitária ou um anúncio do Reach para convidar os usuários para transferirem um dos seus aplicativos de outro aplicativo. Uma URL de rastreamento redireciona para o URL de transferência associada a à loja seleciona, permitindo que nosso sistema de rastreamento registre a loja de onde o aplicativo foi transferido no momento da instalação. Se uma fonte for fornecida, nosso sistema de rastreamento também a registra, permitindo a distinção entre as várias campanhas publicitárias criadas para o seu aplicativo.

**Consulte também:**

- [Documentação de interface do usuário - Reach - Nova campanha de envio][Link 17] 
 
Criar um novo controle URL requer que você especifique uma loja e a fonte: nenhum, cliente, ou adicione o servidor. 

-    Uma fonte Nenhum cria um padrão de rastreamento de URL.
-    Uma fonte de personalizada permite que você especifique uma URL em um servidor externo para fazer o download de seu aplicativo.
-    Uma fonte de servidor Ad cria um padrão de rastreamento de URL em um servidor padrão denomidado Ad.
 
**Criar um rastreamento de URL**
Você também pode criar uma URL de rastreamento, permitindo que os usuários baixem um de seus aplicativos de dentro da seção de conteúdo de uma nova campanha do Reach.

**Consulte também:**

- [Documentação da interface do usuário - Reach - Enviar conteúdo][Link 17]
 
![settings10][55]

## Informações do aplicativo:
Permite que você registre informações adicionais associadas aos usuários do seu aplicativo. Essas informações podem ser injetadas pelo seu aplicativo (usando o SDK) ou por seu back-end (usando a API do dispositivo). 
**Consulte também:**

- [Documentação da API - API do dispositivo][Link 4]

Registrar marcas de informações de aplicativo permite que você segmente as campanhas do Reach, criando critérios específicos de audiência do Reach com base nelas. Você pode exibir o nome e o tipo de marcas de informações de aplicativo existentes ou adicionar uma nova informação de aplicativo com base no nome e tipo de cadeia de caracteres, data, inteiro ou booleano.

**Consulte também:**

- [Documentação de interface do usuário - Reach - Nova campanha de envio][Link 17]
 
![settings11][56]
 
## Pressão comercial: 
Cotas push permitem que você defina o número máximo de vezes que um dispositivo pode ser enviado durante um período. Cotas push são usadas somente por campanhas Reach que têm a opção "Aplicar cotas de envio" habilitada. Cotas push podem ser gerenciadas por segmento ou por padrão. As cotas podem ser definidas para enviar apenas um número máximo de envios por um período flexível da última hora, dia, semana ou mês.

**Consulte também:**

- [Documentação de interface do usuário - Reach - Nova campanha de envio][Link 17], [Documentação de interface do usuário - Segmentos][Link 18]
 
**Cotas**

- Cota por padrão: Essa cota é aplicada aos usuários que não correspondem em nenhum segmento de 'cota por segmento' na seção abaixo. Por padrão, nenhuma cota é definida.
- Cota de segmento: Como uma cota deve ser aplicada a um grupo de usuários, você precisa criar um segmento para identificar os usuários que vai para aplicar. Pode ser um segmento de 'grandes usuários' definido pelo número de sessões que eles fazem ou qualquer coisa que você considere interessante para definir o grupo de usuários. Se um dispositivo corresponder a vários segmentos com uma cota definida, será aplicada apenas a cota que tiver o número máximo de envios por hora.

![settings12][57]
 
## Permissões:
Permite que você pesquise e exiba o nível de email, nome, organização e permissão de usuários do seu aplicativo. O conceito de permissões é uma adição à função de projeto. Ele permite que você associe um conjunto de permissões a um usuário específico com acesso ao seu aplicativo.

**Os níveis atuais de permissões disponíveis são:**

-    "Criador da campanha Reach" (um usuário que pode criar campanhas Reach) 
-    "Gerente de campanha Reach" (um usuário que pode criar campanhas Reach e ativar, suspender, finalizar e destruí-las)

> Observação: Quando um usuário tem uma função de projeto e um conjunto de permissões para um determinado aplicativo, será usado o conceito com mais permissões. Portanto, sugerimos que ao usar permissões, você defina a função de projeto de seus usuários para "Visualizador" (a função com menos permissões) e adicione mais permissões no nível do aplicativo.

**Consulte também:**

- [Documentação da interface do usuário - Página inicial][Link 13]  
 
![settings13][58]

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
