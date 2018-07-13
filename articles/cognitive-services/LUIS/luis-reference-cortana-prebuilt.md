---
title: Referência do aplicativo predefinido Cortana | Microsoft Docs
description: Referência para o assistente pessoal do Cortana, um aplicativo predefinido dos Serviços Inteligentes de Reconhecimento Vocal (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363532"
---
# <a name="cortana-prebuilt-app-reference"></a>Referência do aplicativo predefinido Cortana
Esta referência lista as intenções e entidades que o aplicativo predefinido Cortana reconhece.

## <a name="cortana-prebuilt-intents"></a>Intenções predefinidas do Cortana
O aplicativo do assistente de pessoal predefinido pode identificar os seguintes propósitos:

| Intenção | Exemplo de enunciados |
|--------| ------------------|
| builtin.intent.alarm.alarm_other|atualizar meu alarme de 7h30 para oito horas <br/>alterar meu alarme de 8h às 9h |
| builtin.intent.alarm.delete_alarm|excluir um alarme <br/>excluir meu alarme "ativar"|
| builtin.intent.alarm.find_alarm|a que horas o alarme de ativação está definido? <br/> meu alarme está ativado? |
| builtin.intent.alarm.set_alarm|ativar o alarme de ativação<br/>você pode definir um alarme para 12 para tomar antibióticos?|
| builtin.intent.alarm.snooze|adiar alarme para 5 minutos<br/>adiar alarme|
| builtin.intent.alarm.time_remaining| quanto tempo devo eu tenho até “acordar”? <br/> quanto tempo até o próximo alarme?|
| builtin.intent.alarm.turn_off_alarm | desligar o alarme 7h <br/> desligar meu alarme de ativação |
| builtin.intent.calendar.change_calendar_entry| a mudança é um compromisso<br/>mover minha reunião de hoje para amanhã|
|builtin.intent.calendar.check_availability|tim está ocupado na sexta-feira?<br/>carlos está livre no sábado|
|builtin.intent.calendar.connect_to_meeting|conectar-se a uma reunião<br/>participar da reunião online|
|builtin.intent.calendar.create_calendar_entry|Eu preciso agendar um compromisso com tony para sexta-feira<br/>Faça um compromisso com Lisa às 14h no domingo|
|builtin.intent.calendar.delete_calendar_entry|excluir meu compromisso<br/>remover a reunião às 15h amanhã do meu calendário|
|builtin.intent.calendar.find_calendar_entry|mostrar meu calendário<br/>exibir meu calendário semanal|
|builtin.intent.calendar.find_calendar_when|quando é a próxima reunião com Jon?<br/>qual é o horário do meu compromisso com Larry na segunda-feira?|
|builtin.intent.calendar.find_calendar_where|mostre-me o local da minha reunião às 6 horas<br/>onde é essa reunião com jon?|
|builtin.intent.calendar.find_calendar_who|onde é essa reunião com?<br/>quem mais foi convidado?|
|builtin.intent.calendar.find_calendar_why|quais são os detalhes da minha reunião às 11h?<br/>qual é o assunto dessa reunião com Jon?|
|builtin.intent.calendar.find_duration|em quanto tempo é a minha próxima reunião<br/>quantos minutos será minha reunião essa manhã|
|builtin.intent.calendar.time_remaining|quanto tempo até o próximo compromisso?<br/>quanto mais tempo é necessário antes da reunião?|
|builtin.intent.communication.add_contact|salve esse número e coloque-o como José<br/>coloque jason na minha lista de contatos|
|builtin.intent.communication.answer_phone|resposta<br/>responder à chamada|
|builtin.intent.communication.assign_nickname|editar o apelido para nickolas<br/>adicionar um apelido para john doe|
|builtin.intent.communication.call_voice_mail|mensagem de voz<br/>chamada de correio de voz|
|builtin.intent.communication.find_contact|mostre-me meus contatos<br/>encontrar contatos|
|builtin.intent.communication.forwarding_off|interromper o encaminhamento minhas chamadas<br/>desativar o encaminhamento de chamada|
|builtin.intent.communication.forwarding_on|definir encaminhamento de chamada para enviar chamadas para telefone residencial<br/>encaminhar chamadas para telefone residencial|
|builtin.intent.communication.ignore_incoming|não responder essa chamada<br/>agora não, estou ocupado|
|builtin.intent.communication.ignore_with_message|não responder a chamada, mas enviar uma mensagem em vez disso<br/>ignorar e enviar um texto de volta|
|builtin.intent.communication.make_call|chamar bob e john<br/>chame mão e pai|
|builtin.intent.communication.press_key|estrela de discagem<br/>pressione a 1 2 3|
|builtin.intent.communication.read_aloud|leia o texto<br/>o que ela disse na mensagem|
|builtin.intent.communication.redial|disque novamente minha última chamada<br/>disque novamente|
|builtin.intent.communication.send_email|email de Mike Waters: Mike, se uma refeição última semana foi esplêndida<br/>enviar um email para Bob|
|builtin.intent.communication.send_text|enviar texto para bob e john<br/>Message|
|builtin.intent.communication.speakerphone_off|tire-me do alto-falante<br/>desative o viva-voz|
|builtin.intent.communication.speakerphone_on|modo viva-voz<br/>ativar viva-voz|
|builtin.intent.mystuff.find_attachment|localize o documento que John enviou por email ontem <br/>encontre o documento de john|
|builtin.intent.mystuff.find_my_stuff|Desejo editar minha lista de compra de ontem<br/>localize minhas notas de química de setembro
|builtin.intent.mystuff.search_messages|abrir mensagem <br/>da nuvem para o dispositivo|
|builtin.intent.mystuff.transform_my_stuff|compartilhe minha lista de compra com meu marido<br/>excluir minha lista de compra|
|builtin.intent.ondevice.open_setting|abrir as configurações do cortana <br/>pular para notificações|
|builtin.intent.ondevice.pause|desligar a música<br/>música desativada|
|builtin.intent.ondevice.play_music|Quero ouvir o proprietário de um coração solitário<br/>reproduzir música gospel|
|builtin.intent.ondevice.recognize_song|conte-me qual é essa música<br/>analise e recupere o título de música|
|builtin.intent.ondevice.repeat|repetir essa faixa<br/>reproduzir esta música novamente|
|builtin.intent.ondevice.resume|reiniciar música<br/>iniciar música novamente|
|builtin.intent.ondevice.skip_back|realizar backup de uma faixa<br/>música anterior|
|builtin.intent.ondevice.skip_forward|próxima música<br/>pular uma faixa|
|builtin.intent.ondevice.turn_off_setting|wifi desligado<br/>desabilitar modo avião|
|builtin.intent.ondevice.turn_on_setting|wifi ligado<br/>ativar o bluetooth|
|builtin.intent.places.add_favorite_place|adicionar esse endereço em meus favoritos<br/>salvar este local nos meus favoritos|
|builtin.intent.places.book_public_transportation|comprar um tíquete de trem<br/>reservar uma passagem de bonde|
|builtin.intent.places.book_taxi|você pode me encontrar um passeio a essa hora?<br/>encontre um táxi|
|builtin.intent.places.check_area_traffic|como está o trânsito na 520<br/>como está o trânsito na i-5|
|builtin.intent.places.check_into_place|verificar no joya<br/>fazer check-in aqui|
|builtin.intent.places.check_route_traffic|mostre-me o trânsito no caminho para kirkland<br/>como está o trânsito para Seattle?|
|builtin.intent.places.find_place|onde eu moro <br/>envie-me os 3 restaurantes japoneses principais|
|builtin.intent.places.get_address|mostre-me o endereço de Guu na rua Robson<br/>qual é o endereço do Starbucks mais próximo?|
|builtin.intent.places.get_coupon|encontre as ofertas em TVs na minha área<br/>descontos no modo de exibição de montanha|
|builtin.intent.places.get_distance|a que distância fica o Holiday Inn?<br/>qual a distância para Tahoe|
|builtin.intent.places.get_hours|qual é o horário do bar del corso’s às segundas-feiras?<br/>quando a biblioteca está aberta?|
|builtin.intent.places.get_menu|mostre-me o menu do Applebee’s<br/>o que há no cardápio do Sizzler|
|builtin.intent.places.get_phone_number|indique o número para o Home Depot<br/>qual é o número de telefone do Starbucks mais próximo?|
|builtin.intent.places.get_price_range|quanto custa um jantar no red lobster<br/>qual o valor do purple cafe|
|builtin.intent.places.get_reviews|mostrar revisões de lojas de hardware locais<br/>Desejo ver avaliações do restaurante|
|builtin.intent.places.get_route|envie-me instruções para|é possível obter chegar no pizza hut a pé|
|builtin.intent.places.get_star_rating|quantas estrelas tem a lavanderia francesa?<br/>o aquário em Monterrey é bom?|
|builtin.intent.places.get_transportation_schedule|que horas o barco de são francisco sai?<br/>quando é o último trem para Seattle?|
|builtin.intent.places.get_travel_time|Qual é o tempo de condução para Denver a partir de SF<br/>quanto tempo vai demorar para chegar a San Francisco daqui|
|builtin.intent.places.make_call|chamada de Dr. Smith em bellevue<br/>chamar o home depot na primeira rua|
|builtin.intent.places.rate_place|fornecer uma classificação para este restaurante<br/>classificar o il fornaio 5 stars no yelp|
|builtin.intent.places.show_map|qual é a minha localização atual <br/>qual é a minha localização|
|builtin.intent.places.takes_reservations|é possível fazer uma reserva no Olive Garden<br/>a galeria de arte aceita reservas|
|builtin.intent.reminder.change_reminder|altere um lembrete<br/>mover para cima meu lembrete diário de imagem de 30 minutos|
|builtin.intent.reminder.create_single_reminder|lembrar-me para ativar às 7h<br/>lembrar-me do go trick or treating com luca às 16h40|
|builtin.intent.reminder.delete_reminder|excluir este lembrete<br/>excluir meu lembrete de imagem|
|builtin.intent.reminder.find_reminder|eu tenho algum lembre configurado para hoje<br/>eu tenho um lembrete configurado para a festa de luca|
|builtin.intent.reminder.read_aloud|ler o lembrete em voz alta<br/>ler esse lembrete|
|builtin.intent.reminder.snooze|adiar este lembrete até amanhã<br/>adiar este lembrete|
|builtin.intent.reminder.turn_off_reminder|desativar o lembrete<br/>descartar o lembrete de embarque no aeroporto|
|builtin.intent.weather.change_temperature_unit|alterar de fahrenheit para kelvin<br/>alterar de fahrenheit para celsius|
|builtin.intent.weather.check_weather|mostre-me a previsão para minha futura viagem para Seattle<br/>como será o clima neste fim de semana|
|builtin.intent.weather.check_weather_facts|como é o clima no Havaí em dezembro?<br/>qual foi a temperatura nessa época ano passado?|
|builtin.intent.weather.compare_weather|envie-me uma comparação entre as temperaturas altas e baixas de Dallas em Houston, TX<br/>qual a comparação de clima entre slc e nyc|
|builtin.intent.weather.get_frequent_locations|envie-me meu local mais frequente<br/>mostrar paradas mais frequentes|
|builtin.intent.weather.get_weather_advisory|avisos de clima<br/>mostrar a consultoria de clima para sacramento|
|builtin.intent.weather.get_weather_maps|exibir um mapa de clima<br/>mostre-me mapas de clima para a África|
|builtin.intent.weather.question_weather|estará nublado amanhã de manhã?<br/>será necessário pá de neve essa semana?|
|builtin.intent.weather.show_weather_progression|mostrar radar climático local<br/>iniciar radar|
|builtin.intent.none|quantos anos você tem<br/>abrir a câmera|

## <a name="prebuilt-entities"></a>Entidades predefinidas 

Aqui estão alguns exemplos de entidades que o aplicativo de assistente pessoal pré-configurado pode identificar:

|Entidade |Exemplo de entidade no enunciado |
|-------|------------------|
|builtin.alarm.alarm_state | ativar `off` meu alarme    <br/> meu alarme está ativado`on`  | 
|builtin.alarm.duration |adiar para `10 minutes`    <br/> adiar alarme para `5 minutes`  |
|builtin.alarm.start_date | definir um alarme para `monday` às 7h   <br/> definir um alarme para `tomorrow` meio dia   |
|builtin.alarm.start_time | criar um alarme para `30 minutes`    <br/> definir que o alarme seja desativado `in 20 minutes`   |
|builtin.alarm.title | meu `wake up` alarme está ativado <br/> você pode definir um alarme trimestral para às 12h de segunda a sexta-feira chamado de `take antibiotics` |
|builtin.calendar.absolute_location | criar um compromisso para amanhã às `123 main street`   <br/> a reunião ocorrerá em `cincinnati` 5 de junho    |
|builtin.calendar.contact_name|colocar uma reunião de marketing no meu calendário e certificar-se de que `joe` há <br/>Desejo configurar um almoço no il fornaio e convidar `paul` |   
|builtin.calendar.destination_calendar|adicionar isso à minha `work` agenda   <br/>colocar isso em meu `personal` calendário |
|builtin.calendar.duration| configurar um compromisso para `an hour` 6h hoje à noite <br/>  Catálogo de uma `2 hour` reunião com joe |  
|builtin.calendar.end_date | criar uma entrada do calendário chamada férias a partir de amanhã até `next monday` <br/>    bloquear meu horário como ocupado até `monday, october 5th` | 
|builtin.calendar.end_time | a reunião termina às `5:30 PM` <br/> agendar das 11h às `noon`  |   
|builtin.calendar.implicit_location | cancelar o compromisso no dmv <br/> alterar o local de aniversário de miles para `poppy restaurant` |    
|builtin.calendar.move_earlier_time | enviar por push o encaminhamento da reunião `an hour` <br/> mover consulta odontológica para `30 minutes`       |
|builtin.calendar.move_later_time | mover minha consulta odontológica para `30 minutes`    <br/> enviar por push a reunião `an hour` |  
|builtin.calendar.original_start_date | reagendar o compromisso em barbearia de terça-feira para quarta-feira <br/> mover a reunião com ken de `monday` para terça-feira |
|builtin.calendar.original_start_time | reagendar minha reunião de `2:00` para 15h  <br/> mover minha consulta odontológica de `3:30` para 16h |  
|builtin.calendar.start_date | a que horas minha festa começa `flag day` <br/> agendar almoço para o `friday after next` ao meio-dia 
|builtin.calendar.start_time | Desejo agendar para `this morning` <br/> Desejo agendar em `morning` |  
|builtin.calendar.title | `vet appointment`  <br/> `dentist` Terça-feira |
|builtin.communication.audio_device_type | fazer a chamada usando `bluetooth`  <br/> chamar usando meu `headset` | 
|builtin.communication.contact_name | enviar SMS `bob jones`  <br/> | chamada `sarah`|
|builtin.communication.destination_platform | chamar dave em `london` <br/> chamar seu `work line` |    
|builtin.communication.from_relationship_name | mostrar chamadas do meu `daughter` <br/> ler o e-mail a partir de `mom`   |   
|builtin.communication.key | discagem `star` <br/>  pressione a `hash` chave    |
|builtin.communication.message | enviar e-mail para carly para dizer `i'm running late` <br/> enviar SMS para angus smith `good luck on your exam` |    
|builtin.communication.message_category | novo email marcado para `follow up`  <br/> novo email marcado `high priority` |    
|builtin.communication.message_type | enviar um `email`   <br/> ler minhas `text` mensagens em voz alta |
|builtin.communication.phone_number | Eu desejo discar `1-800-328-9459` <br/>     chamar `555-555-5555` |   
|builtin.communication.relationship_name | enviar SMS para meu `husband` <br/>  email `family`| 
|builtin.communication.slot_attribute | alterar o `recipient` <br/>    alterar o `text` | 
|builtin.comminication.source_platform | chamá a partir de `skype` <br/> chamá a partir do meu `personal line` |
|builtin.mystuff.attachment| com documentos `attached` <br/> localizar o email que `attachment` bob enviou |
|builtin.mystuff.contact_name| encontrar a planilha que Lisa enviou para `me` <br/> onde está o documento que enviei para `susan` ontem à noite |
|builtin.mystuff.data_source | `c:\dev\` <br/> meu `desktop` <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|builtin.mystuff.data_type | localize o `document` que eu trabalhei ontem à noite <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> ativar mike `visio diagram`  |
|builtin.mystuff.end_date| mostrar os documentos em que eu trabalhei `between yesterday and today`   <br/> localizar o documento que eu estava trabalhando em `before thursday the 31st` |
|builtin.mystuff.end_time| localizar arquivos salvos `before noon` <br/> localizar o documento que eu estava trabalhando em `before 4pm`|      
|builtin.mystuff.file_action| abra a planilha que eu `saved` ontem <br/> localize a planilha que kevin `created`| 
|builtin.mystuff.from_contact_name | encontre a proposta que `jason` me enviou <br/> abra o último e-mail de `isaac` |
|builtin.mystuff.keyword | mostre-me os arquivos `french conjugation` <br/> localize o `marketing plan` que escrevi ontem |
|builtin.mystuff.location | o documento que editei em `work` <br/> fotos que tirei em `paris` |
|builtin.mystuff.message_category | procure meus `new` emails <br/> pesquisar por meu `high priority`email |
|builtin.mystuff.message_type | verificar meu `email` <br/>  mostre-me minhas `text` mensagens  |
|builtin.mystuff.source_platform | pesquisar meu `hotmail` email para o email de João    <br/> localize o documento que enviei de `work` |
|builtin.mystuff.start_date | encontra as notas de `january` <br/> localizar o e-mail que enviei para rob `after january 1st` |
|builtin.mystuff.start_time | encontre o e-mail que enviei para rob em algum momento antes das 14h mas `after noon`? <br/> localize a planilha que Kristin enviou para mim que eu editei `last night` | 
|builtin.mystuff.title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|builtin.mystuff.transform_action | `download` o arquivo que john enviou para mim <br/> `open` meu documento com as diretrizes de anotação |
|builtin.note.note_text | criar uma lista de compras incluindo `pork chops, applesauce and milk` <br/> fazer uma nota para `buy milk` |
|builtin.note.title | fazer uma nota chamada `grocery list` <br/> fazer uma nota chamada `people to call` |
|builtin.ondevice.music_artist_name | executar tudo por `rufus wainwright` <br/> tocar `garth brooks` música |   
|builtin.ondevice.music_genre | mostrar `classic rock` músicas <br/> tocar minha `classical` música do período barroco |
|builtin.ondevice.music_playlist | ordem aleatória de todas as músicas da britney spears da `workout` lista de reprodução <br/> reproduzir `breakup` lista de reprodução
|builtin.ondevice.music_song_name | reproduzir `summertime` <br/> reproduzir `me and bobby mcgee` | 
|builtin.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|builtin.places.absolute_location | leve-me para a interseção de `5th and pike` <br/> não, quero direções para `1 microsoft way redmond wa 98052` |
|builtin.places.atmosphere | procure `interesting` locais de saída    <br/> onde posso encontrar um `casual` restaurante |  
|builtin.places.audio_device_type |ligar para a agência dos correios em `hands free` <br/> chamada papa john com `speakerphone` |    
|builtin.places.avoid_route | evitar a `toll road` <br/> leve-me para san francisco evitando o `construction on 101` |  
|builtin.places.cuisine | `halal` lanchonete próximo a mountain view   <br/> `kosher` encontrar refeições na península |
|builtin.places.date | fazer uma reserva para `next friday the 12th` <br/>  mashiko está aberto em `mondays` ? |
|builtin.places.discount_type | localizar um `coupon` para macy’s <br/> encontre um `coupon` |
|builtin.places.distance | há um bom jantar `within 5 miles` aqui <br/> localizar `within 15 miles` |   
|builtin.places.from_absolute_location | instruções `45 elm street` para casa <br/> me dê instruções `san francisco` para palo alto  |
|builtin.places.from_place_name | dirigindo de `post office` para a 56 center street <br/> me dê as instruções de `home depot` para lowes |
|builtin.places.from_place_type | instruções para o centro de `work` <br/>  me dê as instruções de `drug store` para casa |
|builtin.places.meal_type | locais próximos para `dinner` <br/> localizar um bom lugar para uma empresa `lunch` | 
|builtin.places.meal_type | mostre-me algumas lojas interessantes `near`  <br/> há algum restaurante libanês bom `around` aqui? |
|builtin.places.open_status | onde é o shopping `closed` <br/> mostre-me o horário `opening` de funcionamento da loja | 
|builtin.places.place_name | leve-me para `central park` <br/> consultar `eiffel tower` |   
|builtin.places.place_type | `atms` <br/> `post office` |
|builtin.places.place_type | mostre-me as instruções pela `shortest` rota <br/> pegue a `fastest` rota | 
|builtin.places.price_range| me dê locais que estão `moderately affordable` <br/>  Desejo um `expensive`   |
|builtin.places.product | onde posso conseguir `fresh fish` por aqui  <br/> o que é vendido ao redor `bare minerals` |
|builtin.places.public_transportation_route | horário de ônibus para `m2`  <br/> rota de ônibus `3x` |  
|builtin.places.rating | mostrar `3 star` restaurantes <br/> mostrar resultados que são `3 stars or higher`  |
|builtin.places.reservation_number | reserve uma mesa para `seven` pessoas <br/>  faça uma reserva para `two` no il fornaio |
|builtin.places.results_number | mostre-me os `10` cafés mais próximos aqui <br/> mostre-me os aquários `3` principais  
|builtin.places.service_provided | onde posso ir `whale watch` de ônibus? <br/> Preciso de um mecânico para `fix my brakes` |    
|builtin.places.time | Desejo locais que estão abertos no sábado às `8 am`| mashiko está aberto `now` |
|builtin.places.transportation_company | programação de trem para `new jersey transit` <br/> como posso chegar em `bart` | 
|builtin.places.transportation_type | onde há uma loja de música que eu posso obter `on foot`? <br/>  envie-me `biking` instruções para mashiko|
|builtin.places.travel_time | Quero poder dirigir `less than 15 minutes` <br/>   Desejo algum lugar que posso chegar em `under 15 minutes` |   
|builtin.reminder.absolute_location | lembrar-me de ligar para meu pai quando eu chegar em `chicago` <br/> quando eu voltar, `seattle` lembrar-me de abastecer |
|builtin.reminder.contact_name | quando `bob` ligar, lembrar-me de contar piadas a ele <br/> criar um lembrete para mencionar o horário do ônibus escolar quando eu conversar com `arthur` |
|builtin.reminder.leaving_absolute_location | lembrar de pegar carig ao sair `1200 main` |
|builtin.reminder.leaving_implicit_location | lembrar de abastecer quando eu sair `work`  |
|builtin.reminder.original_start_date | alterar o lembrete sobre grama de `saturday` para domingo <br/> mover meu lembrete sobre escola de `monday` para terça-feira   |
|builtin.reminder.relationship_name | quando meu `husband` ligar, lembrar-me de informá-lo da reunião pta <br/> lembrar-me novamente quando `mom` ligar|
|builtin.reminder.reminder_text | pode lembrar-me para `bring up my small spot of patchy skin` quando dr smith ligar  <br/> lembrar-me `pick up dry cleaning` às 16h40   |
|builtin.reminder.start_date | lembrar-me `thursday after next` às 8h  <br/> lembrar-me `next thursday the 18th` às 8h    |
|builtin.reminder.start_time | crie um lembrete `in 30 minutes` <br/> criar um lembrete para regar as plantas `this evening at 7` |  
|builtin.weather.absolute_location | irá chover em `boston` <br/> mostre-me a previsão para `seattle`?  |
|builtin.weather.date_range | clima em nyc `this weekend` <br/>   pesquisar a `five day` previsão em hollywood Flórida |
|builtin.weather.suitable_for | poderei usar `hiking` shorts este fim de semana?   <br/> será adequado para `walk` ao jogo hoje? | 
|builtin.weather.temperature_unit | qual é a temperatura hoje em `kelvin`   <br/> mostre-me as temperaturas em `celsius` |  
|builtin.weather.time_range | parece que vai nevar `tonight`? <br/> estará ventando `now`?  |
|builtin.weather.weather_condition | mostrar `precipitation` <br/> qual é a espessura do lago `snow` tahoe agora?  |

