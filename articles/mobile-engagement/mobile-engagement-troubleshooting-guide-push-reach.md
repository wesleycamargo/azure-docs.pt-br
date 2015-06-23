<properties 
   pageTitle="Guia de Solução de Problemas do Mobile Engagement do Azure - Push/Reach" 
   description="Solucionando problemas de notificação e interação do usuário no Mobile Engagement do Azure" 
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


# Guia de solução para problemas de Push e Reach

Estes são os possíveis problemas que podem ser encontrados em como o Mobile Engagement do Azure envia informações para seus usuários.
 
## Falhas de push

### Problema
- Os pushes não funcionam (no aplicativo, fora do aplicativo ou ambos).

### Causas
- Muitas vezes, uma falha de push é uma indicação de que o Mobile Engagement do Azure, Reach ou outro recurso avançado do Mobile Engagement do Azure não está integrado corretamente ou que uma atualização é necessária no SDK para corrigir um problema conhecido com uma nova plataforma do Sistema operacional ou do Dispositivo.
- Teste apenas um push No Aplicativo e apenas um push Fora do Aplicativo para determinar se algo é um problema No Aplicativo ou Fora do Aplicativo.
- Teste a partir da Interface do Usuário e da API como uma etapa de solução de problemas para ver quais informações de erro adicionais estão disponíveis nos dois lugares.
- Os pushes Fora do Aplicativo não funcionarão a menos que o Mobile Engagement do Azure e o Reach estejam integrados no SDK.
- Os pushes Fora do Aplicativo não funcionarão se os certificados não forem válidos ou estiverem usando PROD versus DEV corretamente (iOS apenas). (* * Observação: * * as notificações por push "Fora do aplicativo" não poderão ser entregues ao iOS, se você tiver as versões de desenvolvimento (DEV) e de produção (PROD) do seu aplicativo instaladas no mesmo dispositivo, uma vez que o token de segurança associado ao certificado pode ser invalidado pela Apple. Para resolver esse problema, desinstale as versões DEV e PROD do seu aplicativo e reinstale a uma versão em seu dispositivo.)
- As contagens do push Fora do Aplicativo são tratadas de maneira diferente em diferentes plataformas (o iOS mostra menos informações que o Android, caso os pushes nativos estejam desabilitados em um dispositivo; a API pode fornecer mais informações do que a interface do usuário nas estatísticas do push).
- Os pushes Fora do Aplicativo podem ser bloqueados pelos clientes ao nível do SO (iOS e Android).
- Os pushes Fora do Aplicativo serão mostrados como desabilitados na interface do usuário do Mobile Engagement do Azure, caso não estejam integrados corretamente, mas poderão falhar sem aviso na API.
- Os pushes No Aplicativo não funcionarão a menos que o Mobile Engagement do Azure e o Reach estejam integrados no SDK.
- Os pushes GCM e ADM não funcionarão a menos que o Mobile Engagement do Azure e o servidor específico estejam integrados no SDK (Android apenas).
- Os pushes No Aplicativo e Fora do Aplicativo devem ser testados separadamente para determinar se é um problema de Push ou Reach.
- Os pushes No Aplicativo requerem que o aplicativo seja aberto para ser recebido.
- Os pushes No Aplicativo geralmente são configurados para serem filtrados por uma marca de informações do aplicativo de aceitação ou recusa.
- Se você usar uma categoria personalizada no Reach para exibir notificações do aplicativo, precisará seguir o ciclo de vida correto da notificação, caso contrário, a notificação não poderá ser limpa quando o usuário descartá-la.
- Se você iniciar uma campanha sem data de término e um dispositivo receber a notificação no aplicativo, mas não exibí-la ainda, o usuário ainda receberá a notificação na próxima vez em que fizer logon no aplicativo, mesmo se você encerrar manualmente a campanha.
- Para os problemas com a API do Push, confirme se você realmente deseja usar a API do Push, em vez da API do Reach (já que a API do Reach é usada com mais frequência) e se não está confundindo os parâmetros "payload" e "notifier".
- Teste sua campanha por push com os dispositivos conectados via WIFI e 3G para eliminar a conexão de rede como uma possível fonte de problemas.

### Consulte também

- [Guia de Solução de Problemas - SDK][Link 25], [Guia de Solução de Problemas - Push][Link 23], [Documentação do SDK - iOS - Como Preparar seu Aplicativo para notificações por Push da Apple][Link 5]
 
## Teste do Push

### Problema
- Os pushes podem ser enviados para um dispositivo específico com base em uma ID do Dispositivo.

### Causas

- Os dispositivos de teste são configurados de modo diferente para cada plataforma, mas causar um evento em seu aplicativo em um dispositivo de teste e procurar a ID do Dispositivo no portal deverão funcionar para localizar a ID do dispositivo para todas as plataformas.
- Os dispositivos de teste funcionam de forma diferente com IDFA versus IDFV (iOS apenas).

### Consulte também

- [Documentação da Interface do Usuário - Reach][Link 17]
 
## Personalização do Push

### Problema
- O item de conteúdo do push avançado não funcionará (notificação, toque, vibração, imagem etc.).
- Os links dos pushes não funcionam (fora do aplicativo, no aplicativo, em um site, em um local no aplicativo).
- As estatísticas do push mostram que um push não foi enviado para muitas pessoas conforme o esperado (muitas ou insuficientes).
- Push duplicado e recebido duas vezes.
- Não é possível registrar o dispositivo de teste para os Pushes do Mobile Engagement do Azure (com seu próprio aplicativo PROD ou DEV).

### Causas

- Vincular a um local específico no aplicativo requer "categorias" (Android apenas).
- Os esquemas de vinculação profundos para redirecionar os usuários para um local alternativo depois de clicarem em uma notificação por push precisam ser criados e gerenciados por seu aplicativo e o SO do dispositivo, não pelo Mobile Engagement diretamente. (* * Observação: * * As notificações fora do aplicativo não podem vincular diretamente os locais do aplicativo com o iOS, como podem com o Android.)
- Os servidores de imagem externos precisam ser capazes de usar o HTTP "GET" e "HEAD" para os pushes gerais funcionarem (Android apenas).
- Em seu código, você pode desabilitar o agente Mobile Engagement do Azure quando o teclado é aberto e fazer com que seu código reative o agente assim que o teclado é fechado para que o teclado não afete a aparência de sua notificação (iOS apenas).
- Alguns itens não funcionam nas simulações de teste, mas somente nas campanhas reais (notificação, toque, vibração, imagem etc.).
- Nenhum dado no lado do servidor é registrado quando você usa o botão para “testar” os pushes. Os dados só são registrados para as campanhas de push reais.
- Para ajudar a isolar o problema, solucione os problemas com: teste, simulação e uma campanha real, pois cada um deles funciona de forma um pouco diferente.
- O período de tempo no qual as campanhas "no aplicativo" e "sempre" estão agendadas para a execução pode afetar os números de entrega, pois uma campanha só será entregue aos usuários que estão "no aplicativo" durante a execução dela (e os usuários com as configurações do dispositivo definidas para receber as notificações "fora do aplicativo").
- As diferenças entre como o Android e o iOS lidam com as notificações fora do aplicativo dificultam comparar diretamente as estatísticas do push entre as versões Android e iOS de seu aplicativo. O Android fornece mais informações de notificação ao nível do SO do que o iOS. O Android informa quando uma notificação nativa é recebida, clicada ou excluída no centro de notificações, mas o iOS não informa isto, a menos que a notificação seja clicada. 
- O principal motivo para os números enviados por “push” serem diferentes dos números “entregues” para as campanhas Reach é que as notificações “no aplicativo” e “fora do aplicativo” são contadas de modo diferente. As notificações "No aplicativo" são lidadas pelo Mobile Engagement, mas as notificações "Fora do aplicativo" são lidadas pelo centro de notificações no SO do dispositivo.

### Consulte também

- [Instruções - Primeiro Push][Link 27], [Guia de Solução de Problemas - Push][Link 23], [Informações do Protocolo HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
- [Desenvolvedor Apple - Informações da Interface do Usuário de "Recusa"](http://support.apple.com/kb/HT3576), [Desenvolvedor Apple - Informações de Desenvolvimento de "Recusa"](https://developer.apple.com/notifications/), [Desenvolvedor Apple - Solução de Problemas de “Recusa”](https://developer.apple.com/library/ios/technotes/tn2265/), [Desenvolvedor Apple - Esquema da URL](https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html) 
- [Desenvolvedor Android - Informações da Interface do Usuário de "Recusa"](http://developer.android.com/about/versions/jelly-bean.html), [Desenvolvedor Android - Padrões de "Recusa"](http://developer.android.com/design/patterns/notifications.html), [Desenvolvedor Android - Notificador de "Recusa"](http://developer.android.com/guide/topics/ui/notifiers/notifications.html), [Desenvolvedor Android - Visualização da "Recusa"](https://developer.android.com/preview/notifications.html), [Desenvolvedor Android - Referência da "Recusa"](http://developer.android.com/reference/android/app/Notification.html), [Desenvolvedor Android - Esquema do Filtro de Intenção](http://developer.android.com/guide/components/intents-filters.html#DataTest), [Desenvolvedor Android – Guia de Referência do Filtro de Intenção](http://developer.android.com/reference/android/content/Intent.html#toUri)

## Direcionamento do push

### Problema
- O direcionamento incorporado não funciona conforme o esperado.
- O direcionamento da Marca de Informações do Aplicativo não funciona conforme o esperado.
- O direcionamento da Localização Geográfica não funciona conforme o esperado.
- As opções de idioma não funcionam conforme o esperado.

### Causas

- Verifique se você carregou as marcas de informações do aplicativo por meio da interface do usuário do Mobile Engagement do Azure ou da API.
- Diminuir a velocidade do push ou a cota do push no nível do aplicativo, ou limitar o público no nível da campanha pode impedir que uma pessoa receba um push específico, mesmo que atenda a outros critérios de direcionamento. 
- Definir um "Idioma" é diferente de direcionar com base no país ou na localidade, que também é diferente de direcionar com base na Localização geográfica em um telefone local ou GPS.
- A mensagem em "idioma padrão" é enviada para qualquer cliente que não tem seu dispositivo definido para um dos idiomas alternativos especificados.

### Consulte também

- [Documentação da Interface do Usuário - Reach][Link 17], [Documentação de Interface do Usuário - Configurações][Link 20], [Documentação da API - Reach][Link 4], [Documentação da API - Push][Link 4], [Documentação da API - Dispositivo][Link 4]
 
## Agendamento do push

### Problema
- O agendamento do push não funciona conforme o esperado (enviado muito cedo ou atrasado).

### Causas

- Os fusos horários podem ter problemas com o agendamento, especialmente ao usar o fuso horário dos usuários finais.
- Os recursos avançados do push podem atrasar os pushes.
- O direcionamento com base nas configurações do telefone (em vez das Marcas de Informações do Aplicativo) pode atrasar os pushes, uma vez o Mobile Engagement do Azure pode ter que solicitar dados do telefone em tempo real antes de enviar um push.
- As campanhas criadas sem uma data de término armazenam o envio por push localmente no dispositivo e mostram-no na próxima vez em que o aplicativo é aberto, mesmo que a campanha seja encerrada manualmente.
- Iniciar mais de uma campanha ao mesmo tempo pode levar mais tempo para verificar sua base de usuários (tente iniciar apenas uma campanha por vez, com um máximo de quatro, também destine apenas para os usuários ativos, para que os antigos usuários não precisem ser verificados).
- Se você usar a opção "Ignorar Público, o push será enviado para os usuários por meio da API" na seção "Campanha" de uma campanha Reach, a campanha NÃO será enviada automaticamente e você precisará enviá-la manualmente através da API do Reach.
- Se você usar uma categoria personalizada no Reach para exibir as notificações no aplicativo, precisará seguir o ciclo de vida correto da notificação; caso contrário, a notificação poderá não ser limpa quando o usuário descartá-la.

### Consulte também

- [Instruções do Reach - Agendar Campanha][Link 3], [Documentação da Interface do Usuário - Nova Campanha por Push do Reach][Link 27]
 
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