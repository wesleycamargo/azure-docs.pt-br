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
   ms.date="06/18/2015"
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
- O envio fora do aplicativo não funcionará a menos que o Mobile Engagement do Azure e Reach estejam integrados no SDK.
- Os pushes não funcionarão se os certificados não forem válidos ou estiverem usando PROD versus DEV corretamente (iOS apenas). (* * Observação: * * as notificações por push "Fora do aplicativo" não poderão ser entregues ao iOS, se você tiver as versões de desenvolvimento (DEV) e de produção (PROD) do seu aplicativo instaladas no mesmo dispositivo, uma vez que o token de segurança associado ao certificado pode ser invalidado pela Apple. Para resolver esse problema, desinstale as versões DEV e PROD do seu aplicativo e reinstale a uma versão em seu dispositivo.)
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
- Teste sua campanha de envio com tanto com o dispositivo conectado por WiFI e quanto por 3G para eliminar a conexão de rede como uma possível fonte de problemas.

## Teste do Push

### Problema
- Os pushes podem ser enviados para um dispositivo específico com base em uma ID do Dispositivo.

### Causas

- Os dispositivos de teste são configurados de modo diferente para cada plataforma, mas causar um evento em seu aplicativo em um dispositivo de teste e procurar a ID do Dispositivo no portal deverão funcionar para localizar a ID do dispositivo para todas as plataformas.
- Os dispositivos de teste funcionam de forma diferente com IDFA versus IDFV (iOS apenas).


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
- A mensagem no “idioma padrão” é enviada para qualquer cliente que não tenha o seu dispositivo definido para um dos idiomas alternativos que você especificar.


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
- Se você usar uma categoria personalizada no Reach para exibir notificações no aplicativo, você deve seguir o ciclo de vida correto de uma notificação, caso contrário, a notificação não poderá ser apagada quando o usuário descartá-la.

 

<!---HONumber=July15_HO2-->