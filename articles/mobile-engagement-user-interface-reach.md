<properties 
   pageTitle="Mobile Engagement do Azure - Interface do usuário - Reach" 
   description="Visão geral da interface de usuário para a seção Reach do Mobile Engagement do Azure" 
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

# <a name="Reach">Reach</a>
 
A seção Reach da interface do usuário é a ferramenta de gerenciamento de campanha Push onde você pode criar/editar/ativar/finalizar/monitorar e obter estatísticas sobre campanhas de notificação de envio e os recursos que também podem ser acessados via a API do Reach (e alguns elementos do nível baixo API de envio). Lembre-se de que, se você estiver usando as APIs ou a interface do usuário, você precisará integrar o Mobile Engagement do Azure e o Reach em seu aplicativo em cada plataforma com o SDK antes que você possa usar as campanhas Reach.

**Consulte também:** 

-  [Documentação da API - API do Reach][Link 4],[Documentação da API - API de envio][Link 4], [Guia solução de problemas - envio/Reach][Link 2]
-  <a href="#ReachCampaign" title="Reach Campaign">Campanha do Reach</a>, <a href="#ReachCriterion" title="Reach Criterion">Critério do Reach</a>, <a href="#ReachContent" title="Reach Content">Conteúdo do Reach</a>
 
## Quatro tipos de notificações de envio:
1.    Anúncios - permitem que sejam enviadas mensagens de anúncio para usuários que redirecioná-las para outro local dentro de seu aplicativo ou para enviá-las para uma página da Web ou armazenamento fora de seu aplicativo. 
2.    Pesquisas - permitem coletar informações dos usuários finais ao fazer perguntas a eles.
3.    Envio de dados - permite enviar um arquivo de dados binário ou base64. As informações contidas em um envio de dados são enviadas ao seu aplicativo para modificar a experiência atual dos usuários em seu aplicativo. Seu aplicativo precisa ser capaz de processar os dados em um envio de dados.
4.    Lado a lado (Windows Phone somente) - permite que você use o serviço de notificação por push da Microsoft (MPNS) para enviar envios nativos do Windows que contenham os dados XML. (Suporte desde a versão 0.9.0 do SDK. A carga final para o lado a lado não pode exceder a 32 quilo bytes).

**Consulte também:** 

-  [Conceitos - Glossário][Link 6]

## Três categorias de estatísticas em tempo real mostradas para cada campanha: 
1.    Enviadas - quantos envios foram feitos com base nos critérios especificados na campanha. 
2.    Respondidas - quantos usuários reagiram à notificação ao abri-la fora do aplicativo ou fechá-la no aplicativo. 
3.    Acionadas - quantos usuários clicaram no link na notificação para serem redirecionados para um novo local no aplicativo, para uma loja ou um navegador da Web. 

> Observação: Mais estatísticas detalhadas de campanha estão disponíveis nas estatísticas de API do Reach

**Consulte também:** 

-  [Conceitos - Glossário][Link 6], [Estatísticas de documentação - API do Reach - Estatísticas][Link 4]


## Detalhes da campanha:
Editar, clonar, excluir ou ativar campanhas que ainda não foram ativadas passando o mouse sobre os nomes ou você pode clicar para abri-las. Você pode clonar campanhas que já foram ativadas passando o mouse sobre os nomes ou você pode clicar para abri-las. No entanto, você não pode alterar uma campanha depois que ela tiver sido ativada.
 
![Reach1][18]

## Comentários do Reach:
Você pode alternar de exibição de detalhes para a de estatísticas de uma campanha aberta que já foi ativada e alternar de exibição das estatísticas simples para avançada para exibir informações mais detalhadas (dependendo de suas permissões). Você também pode usar as informações de comentários do Reach de uma campanha anterior como critérios de direcionamento em uma nova campanha. As estatísticas dos comentários do Reach também podem ser obtidas com as "Estatísticas" da API do Reach. Você também pode personalizar o público-alvo de suas campanhas de envio com base em campanhas anteriores.


**Consulte também:** 

-  <a href="#ReachCampaign">Interface do usuário documentação - Reach - Nova campanha de envio</a>, [Estatísticas de documentação - API do Reach - Estatísticas][Link 4]
![Reach2][19]

## <a name="ReachCampaign">Reach - Nova campanha de envio:</a>
 
Você pode usar a seção do reach da interface do usuário para criar uma nova campanha de envio com uma fórmula complexa, fornecendo todas as informações necessárias para enviar uma notificação por push. As opções de uma campanha de envio variam um pouco dependendo dos quatro tipos de campanha: Anúncios, pesquisas, envios de dados e lado a lado (apenas no Windows Phone).

**As opções se aplicam a:**

- Idiomas:    Todos (anúncios, pesquisas, dados envios, lado a lado)
- Campanha:    Todos (anúncios, pesquisas, dados envios, lado a lado)
- Notificação     Anúncios, pesquisas
- Conteúdo:    Exclusivo para cada tipo de campanha
- Público:     Todos (anúncios, pesquisas, dados envios, lado a lado)
- Período de tempo:     Anúncios, pesquisas, lado a lado
- Testar:    Todos (anúncios, pesquisas, dados envios, lado a lado)
 
![Reach-Campaign1][20]

## Idiomas:
Você pode usar o menu suspenso de idiomas para enviar uma versão diferente do seu envio para dispositivos que estão configurados para usar diferentes idiomas. Por padrão, todos os dispositivos receberão o mesmo Push independentemente do idioma que estão definidos para usar. Os usuários com seus dispositivos definidos para um idioma diferente receberão a versão de idioma padrão do envio. Muitas das opções de campanha de envio permitem que você especifique um conteúdo alternativo para cada um dos idiomas adicionais que você selecionar. 
 
![Reach-Campaign2][21]

**As diferenças de idioma se aplicam a:**

- Idiomas:    Idiomas exclusivos podem ser selecionados além do idioma padrão
- Campanha:    A mesma para todos os idiomas
- Notificação    Exclusivo para cada idioma além do idioma padrão
- Conteúdo:    Exclusivo para cada idioma além do idioma padrão
- Público:     Pode ser filtrado por um critério de idioma separado
- Período de tempo:     O mesmo para todos os idiomas
- Testar:    Pode ser enviados para cada idioma por vez
 
**Idiomas com suporte:**

- Árabe (ar) 
- Búlgaro (bg) 
- Catalão (ca) 
- Chinês (zh) 
- Croata (h) 
- Tcheco (cs) 
- Dinamarquês (da) 
- Holandês (nl) 
- Inglês (en) 
- Finlandês (fi) 
- Francês (fr) 
- Alemão (de) 
- Grego (el) 
- Hebraico (he) 
- Híndi (Olá) 
- Húngaro (hu) 
- Indonésio (id) 
- Italiano (it) 
- Japonês (ja) 
- Coreano (ko) 
- Letão (lv) 
- Lituano (lt) 
- Malaio (macrolanguage) (ms) 
- Norueguês Bokmål (nb) 
- Polonês (pl) 
- Português (Portugal) 
- Romeno (ro) 
- Russo (ru) 
- Sérvio (sr) 
- Eslovaco (discos) 
- Esloveno (sl) 
- Espanhol (es) 
- Sueco (sv) 
- Tagalo (NFA) 
- Tailandês (th) 
- Turco (tr) 
- Ucraniano (uk) 
- Vietnamita (vi) 
 
## Campanha:
Você pode usar a seção de campanha para definir o nome e a categoria da sua campanha, bem como se você planeja ignorar a seção público de uma campanha de envio e, em vez disso, enviar essa campanha por meio da API do Reach (e alguns elementos com o API de envio de nível baixo). As categorias podem ser usadas com um modelo de notificação personalizado para notificações do controle no aplicativo com base nas configurações predefinidas. Você pode obter uma lista existente de "categorias" por meio da API do Reach.

> Aviso: Se você usar a opção "Ignorar público, o envio será feito aos usuários por meio da API" na seção "Campanha" de uma campanha Reach, a campanha NÃO será enviada automaticamente e você precisará enviá-la manualmente pela API do Reach.
 
![Reach-Campaign3][22]
 
**As opções se aplicam a:**

- Nome:    Todos
- Categoria:    Anúncios, pesquisas
- Ignorar o público, o envio será feito aos usuários por meio da API:    Todos
 
## Notificação
Você pode usar a seção de notificação para definir as configurações básicas para o seu envio, incluindo: O título do envio, da mensagem, de uma imagem no aplicativo, ou se é dispensável. Muitas configurações de notificação são específicas para a plataforma do dispositivo. Você pode selecionar se o envio será feito "no aplicativo" ou "fora do aplicativo" ou ambos. (Lembre-se de que os usuários podem "aceitar" ou "recusar" envios de "fora do aplicativo" com o nível de sistema operacional em seus dispositivos e o Mobile Engagement do Azure não poderá substituir essa configuração. Lembre-se também de que a API do Reach manipula o envio "no aplicativo" e "fora do aplicativo". A API de envio pode ser usada para lidar com envios de "fora do aplicativo" também.) Os envios podem ser personalizados com imagens ou conteúdo HTML, incluindo links de vínculos profundos fora de seu aplicativo ou em outro local em seu aplicativo (SDK do Android 2.1.0 ou categorias de tentativa posteriores necessárias). Você pode alterar o ícone ou o emblema iOS e enviar o conteúdo da web ou de texto (um pop-up com link do conteúdo html, link de URL para outro local dentro ou fora do aplicativo). Você também pode fazer com que os dispositivos toquem ou vibrem com o envio. (Lembre-se de que você precisará das permissões corretas do SDK no seu arquivo de manifesto do Android para que o dispositivo toque ou vibre.) Não há atualmente nenhum padrão no setor para tamanhos de "Fotos grandes", haja visto que os tamanhos das telas são diferentes em cada dispositivo, porém imagens de 400 x 100 funciona bem em quase todos os tamanhos de tela.

## Tipos de entrega:
-    Fora do aplicativo somente: a notificação será enviada quando o usuário não estiver usando o aplicativo.
- A notificação do fora aplicativo somente requer um certificado da Apple ou da Google (certificado APNS ou GCM).
- No aplicativo somente: A notificação é exibida somente quando o aplicativo estiver sendo executado.
- A notificação usa o sistema de entrega Capptain para alcançar o usuário. Você pode personalizar totalmente o visual do layout/exibição de seu envio.
- A qualquer momento: Essa opção garante que você envie uma notificação tanto quando o aplicativo estiver em execução como não.

 
![Reach-Campaign4][23]

**As opções se aplicam a:**

- Notificação     Anúncios, pesquisas
 
## Conteúdo:
Você pode usar a seção de conteúdo para modificar o conteúdo de anúncios, pesquisas, envia dados e lado a lado (apenas no Windows Phone). A configuração de conteúdo de campanhas de envio é específica para o tipo de campanha. 

**Consulte também:**

- <a href="#ReachContent">Documentação da interface do usuário - Reach - Enviar conteúdo</a>
 
![Reach-Campaign5][24]

## Público:
Você pode usar a seção público para definir uma lista padrão de itens para limitar sua campanha ou limitar sua campanha com base em critérios personalizados. O conjunto de opções padrões para limitar seu público permite que você envie para usuários novos ou antigos ou somente a usuários de envio nativo. Você também pode definir uma cota para limitar o número de usuários que receberão o envio. Você pode editar manualmente a expressão de como sua campanha é filtrada para incluir um ou mais critérios para os usuários de destino. Você pode digitar manualmente uma expressão de público. Essa expressão deve definir explicitamente a relação entre os critérios. Um critério é descrito por um identificador que deve começar com uma letra maiúscula e não pode conter espaços. A relação entre os critérios pode ser descrita utilizando 'e', 'ou', 'não' operadores, bem como '(',')'. Exemplo: "Criterion1 ou (Criterion1 e não Criterion2)".

> Observação: Com um grande público incluído em campanhas, a verificação de direcionamento do lado do servidor pode ser lenta, especialmente se você tentar iniciar várias campanhas ao mesmo tempo.

- Se possível, só inicie uma campanha por vez.
- No máximo, inicie apenas quatro campanhas por vez.
- Enviar apenas para seus usuários ativos (caixa de seleção "envolver apenas os usuários que podem ser contatados por meio de envio nativo" e "Envolver apenas usuários ativos") para que apenas os usuários que ainda têm o aplicativo instalado e o usa serão verificados.
Quando seu público-alvo for definido, você pode usar o botão simular para descobrir quantos usuários receberão esse envio. Isso irá calcular o número de usuários conhecidos potencialmente direcionados para esse público-alvo (essa é uma estimativa com base em uma amostra aleatória de usuários). Lembre-se de que os usuários que desinstalaram o aplicativo também fazem parte dessa audiência, mas não podem ser alcançados.

**Consulte também:**

- <a href="#ReachCriterion">Documentação da interface do usuário - Reach - Novo critério de envio</a>

![Reach-Campaign6][25]

## Editar expressão
 
![Reach-Campaign7][26]
 
**Limite seu público - sua opção se aplica a:**

- Envolva somente um subconjunto de usuários:    Todos (anúncios, pesquisas, dados envios, lado a lado)
- Envolva apenas usuários antigos:    Todos (anúncios, pesquisas, dados envios, lado a lado)
- Envolva somente novos usuários:    Todos (anúncios, pesquisas, dados envios, lado a lado)
- Envolva apenas os usuários ociosos:    Anúncios, pesquisas, lado a lado
- Envolva apenas usuários ativos:    Todos (anúncios, pesquisas, dados envios, lado a lado)
- Envolva apenas os usuários que podem ser contatados por meio de envio nativo:     Anúncios, pesquisas
 
## Período de tempo:
Você pode usar a seção de período de tempo para definir quando o envio será feito ou você pode deixar o período de tempo em branco para iniciar a campanha imediatamente. Lembre-se de que usar o fuso horário dos usuários finais pode começar a campanha um dia antes que o esperado para os usuários finais na Ásia e fazer pequenos lotes de envios aos poucos até que o período de tempo definido para sua campanha coincida com todos os fusos horários do mundo. Ao usar o fuso horário dos usuários finais também pode causar atrasos em campanhas pois é preciso requisitar o horário do telefone antes de iniciar o envio por push.

> Observação: Campanhas sem uma data de término podem armazenar localmente em cachês de envios e ainda exibi-los após as suas campanhas completas manualmente. Para evitar esse comportamento, especifique uma hora de término para as campanhas.

**Consulte também:**

- [Manuais - Planejamento][Link 3] 
 
![Reach-Campaign8][27]

**As configurações se aplicam a:**

- Período de tempo:     Anúncios, pesquisas, lado a lado
 
## Testar:
Você pode usar a seção de teste para fazer esse envio para o seu próprio dispositivo de teste antes de salvar a campanha. Se você tiver configurado diversos idiomas personalizados para a campanha, você pode testar o envio para cada língua. Você pode configurar um dispositivo de teste para "Minha conta".
> Observação: Nenhum dado do lado do servidor é registrado quando você usa o botão "testar" envios, os dados são registrados apenas para campanhas de envio real.

**Consulte também:**

- [Documentação da interface do usuário - Minha conta][Link 14]
 
![Reach-Campaign9][28]

## <a name="ReachCriterion">Reach - Novo critério de envio (para o público-alvo)</a>

Direcionar seu público-alvo por critérios específicos com o botão "Novos critérios" é um dos conceitos mais poderosos no Mobile Engagement do Azure e ajuda a enviar importantes notificações de envio que os clientes responderão em vez de enviar spam a todos. Você pode limitar seu público com base em critérios padrões e simular envios para determinar quantas pessoas receberão a notificação.

**Consulte também:**

- <a href="#ReachCampaign">Documentação de interface do usuário - Reach - Nova campanha de envio</a>

## Os critérios de audiência podem incluir:

- **Técnicos: **Você pode direcionar com base nas mesmas informações técnicas que você pode ver nas seções de análise e monitoramento. **Consulte também:** [Documentação da interface do usuário - Análise][Link 15], [Documentação de interface do usuário - Monitoramento][Link 16]
- **Local:** aplicativos que usam "relatório de local em tempo real" com geo-fencing podem usar a localização geográfica como um critério para atingir um público-alvo do local de GPS. Chamada de "Relatório lento da área local" podem também ser usados como público-alvo do local de telefone celular ("Relatórios de tempo real local" e "relatório lento da área local" devem ser ativadas no SDK). **Consulte também:**[Documentação do SDK - iOS - Integração][Link 5], [Documentação do SDK- Android - Integração][Link 5]
- **Comentários do reach:** você pode direcionar o público com base nos seus comentários de notificações anteriores do Reach por meio de comentários do Reach em anúncios, pesquisas e envio de dados. Isso permite melhor direcionar o seu público-alvo após duas ou três campanhas do Reach do que seria na primeira vez. Ele também pode ser usado para filtrar os usuários que já receberam uma notificação com conteúdo semelhante, definindo uma campanha para não ser enviada aos usuários que já receberam uma campanha anterior específica. Você pode até mesmo excluir os usuários que estão incluídos em uma campanha específica que ainda está ativa para receber novos envios. **Consulte também:**<a href="#ReachContent">Documentação da interface do usuário - Reach - Enviar conteúdo</a>
- **Instalar rastreamento: **você pode rastrear informações com base onde os seus usuários instalaram seus aplicativos. **Consulte também:**[Documentação de interface do usuário - Configurações][Link 20]
- **Perfil de usuário:** Você pode direcionar com base nas informações de usuário padrão e você pode direcionar com base em informações do aplicativo personalizadas que você criou. Isso inclui usuários que estão conectados no momento e os usuários que responderam a perguntas específicas que você os solicitou ao configurar o aplicativo, em vez de apenas como eles responderam a campanhas anteriores. Todas as suas informações de aplicativo definidas para seu aplicativo aparecem nessa lista.
- Segmentos Também é possível direcionar com base em segmentos que você tenha criado com base no comportamento de um usuário específico contendo diversos critérios. Todos os segmentos definidos para o seu aplicativo aparecem nessa lista. **Consulte também:** [Documentação de interface do usuário - Segmentos][Link 18]
- **Informações de aplicativo:** As marcas de informações de aplicativo personalizado podem ser criadas nas "Configurações" para controlar o comportamento do usuário. **Consulte também:**[Documentação de interface do usuário - Configurações][Link 20]
## Exemplo: 
Se você quiser enviar um anúncio apenas para o subconjunto de seus usuários que executaram uma ação no aplicativo de compra.

1. Vá para a página de configurações do aplicativo, selecione o menu "Informações de aplicativo" e selecione "Nova informação de aplicativo"
2. Registrar uma nova informação de aplicativo booleano chamada "inAppPurchase"
3. Defina as informações de aplicativo como "true" quando o usuário executar com êxito uma compra no aplicativo (usando a função sendAppInfo ("inAppPurchase",...))
4. Se você não quiser fazer isso no seu aplicativo, você pode fazer no seu back-end usando a API do dispositivo)
5. Em seguida, basta criar o seu anúncio, com um critério de limite de seu público-alvo para usuários que têm "inAppPurchase" definido como "true")
 
> Observação: O direcionamento com base em critérios diferentes das marcas de informações de aplicativo requer que o Mobile Engagement do Azure colete informações dos dispositivos de seus usuários antes de efetuar o envio e, portanto, pode causar um atraso. As opções de configuração de envio complexo (como atualização de selos) também podem atrasar o envio. Usar uma campanha de "uma vez" da API de envio é o método de envio mais rápido no Mobile Engagement do Azure. Usar as marcas de informações de aplicativo apenas como critérios de envio para uma campanha Reach (a partir da API do reach ou da interface do usuário) é o próximo método mais rápido haja visto que as marcas de informações do aplicativo estão armazenadas ao lado do servidor. Usar outros critérios de direcionamento para uma campanha de envio é o método mais flexível, porém o método mais lento do envio visto que o Mobile Engagement do Azure tem que consultar os dispositivos para enviar a campanha.
 
![Reach-Criterion1][29] 

**Opções de critério se aplicam a:**

- **Técnicos:**
- Nome do firmware:    Nome do firmware
- Versão do firmware:    Versão do firmware
- Modelo do dispositivo:    Modelo do dispositivo
- Fabricante do dispositivo:    Fabricante do dispositivo
- Versão do aplicativo:    Versão do aplicativo
- Nome da transportadora:    Nome da transportadora, indefinido
- País de transporte:    País de transporte, indefinido
- Tipo de rede:    Tipo de rede
- Local:    Localidade
- Tamanho da tela:    Tamanho da tela
- **Local**
- Última área conhecida:    País, região, localidade
- Geo-instalação em tempo real:    Lista de destinos especiais (nome, ações), circular de destinos especiais (nome, latitude, longitude, rádio em metros)
- **Comentários do Reach** 
- Comentários de lançamento:    Comentários de lançamento
- Comentários de pesquisa:    Comentários de pesquisa
- Comentários de resposta de pesquisa:    Comentários de resposta de pesquisa, pergunta, escolha
- Comentários de envio de dados:    Comentários de envio de dados
- **Instalar o rastreamento**
- Loja:    Loja, indefinido
- Origem:    Origem, indefinido
- **Perfil de usuário**
- Sexo:    macho ou fêmea, indefinido
- Data de nascimento:    operador, data, indefinido
- Aceitar:    true ou false, indefinido
- **Informações do aplicativo**
- Cadeia de caracteres:    Cadeia de caracteres, indefinida
- Data:    operador, data, indefinido
- Número inteiro:    operador, número, indefinido
- Booliano:    true ou false, indefinido
- **Segmento**
- Nome dos segmentos (da lista suspensa), Exclusão (usuários de destino que não fazem parte deste segmento).

## <a name="ReachContent">Reach - Enviar conteúdo (para cada tipo de campanha)</a>
 
Você pode usar a seção de conteúdo de uma nova campanha Reach para modificar o conteúdo de anúncios, pesquisas, envia dados e lado a lado (apenas no Windows Phone). A configuração de conteúdo de campanhas de envio é específica para o tipo de campanha. 
 
## Tipo de conteúdo
- Anúncios
- Pesquisas
- Envios de dados
- Lado a lado (apenas no Windows Phone)
 
## Conteúdo de anúncios
 
 ![Reach-Content1][30] 

## Escolha o tipo de anúncio:
-    Notificação apenas: É uma notificação simples padrão. Isso significa que se um usuário clicar nela, nenhum modo de exibição adicional será exibido, mas apenas a ação associada a ela ocorrerá.
-    Anúncio de texto: É uma notificação que leva o usuário a dar uma olhada em uma exibição de texto.
-    Anúncio Web: É uma notificação que leva o usuário a dar uma olhada em uma exibição Web.

**Consulte também:**

- [Manuais - Anúncios][Link 3] 

**Sobre os anúncios de exibição Web:**

Ocorrências do padrão de "{deviceid}" no código HTML ou código JavaScript que você fornecer aqui serão substituídas automaticamente pelo identificador do dispositivo exibindo o anúncio. Esta é uma maneira fácil de recuperar identificadores de dispositivos do Mobile Engagement do Azure em um serviço Web externo hospedado no seu back office.
Se você quiser criar uma exibição Web em tela inteira (sem os botões de ação e sair de padrão que fornecemos) você pode usar as seguintes funções do código de JavaScript da exibição Web do anúncio: 

-    execute a ação do anúncio: ReachContent.actionContent()
-    sair do anúncio: ReachContent.exitContent()
 
## Escolha a ação:

**Sobre as URLs de ação:**

Qualquer URL que possa ser interpretada pelo sistema operacional de destino do dispositivo pode ser usada como uma URL de ação.
Qualquer URL dedicada que possa dar suporte a seu aplicativo (por exemplo, para fazer com que os usuários mudem para uma determinada tela) também pode ser usada como uma URL de ação.
Cada ocorrência do padrão {deviceid} é substituída automaticamente pelo identificador do dispositivo de execução da ação. Esta é uma maneira fácil de recuperar identificadores de dispositivos do Mobile Engagement do Azure por meio de um serviço Web externo hospedado no seu back office.

- **Ações do Android + iOS**
    - Abra uma página Web
    - http://[domínio de site da Web] 
    - Exemplo:http://www.azure.com
    - Enviar um email
    - mailto:[e-mail-recipient]?subject=[subject]&body=[message] 
    - Exemplo:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Enviar um SMS
    - sms:[número de telefone] 
    - Exemplo:sms:2125551212
    - Discar um número de telefone
    - tel:[número de telefone] 
    - Exemplo:tel:2125551212
- **Ações únicas do Android**
    - Transferir um aplicativo na Play Store
    - market://Details?ID=[pacote de aplicativos] 
    - Exemplo:market://details?id=com.microsoft.office.word
    - Inicie uma pesquisa localizada geograficamente
    - geo:0,0?q=[consulta de pesquisa] 
    - Exemplo:geo:0,0?q=starbucks,paris
- **Ações apenas do iOS**
    - Transferir um aplicativo na App Store
    - http://iTunes.Apple.com/[país]/app/[nome do aplicativo]/id[id do aplicativo]?mt=8 
    - Exemplo:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Ações do Windows
    - Abra uma página Web
    - http://[domínio de site da Web] 
    - Exemplo:http://www.azure.com
    - Enviar um email
    - mailto:[e-mail-recipient]?subject=[subject]&body=[message] 
    - Exemplo:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Enviar um SMS (necessário o aplicativo do Skype)
    - sms:[número de telefone] 
    - Exemplo:sms:2125551212
    - Discar um número de telefone (necessário o aplicativo do Skype)
    - tel:[número de telefone] 
    - Exemplo:tel:2125551212
    - Transferir um aplicativo na Play Store
    - ms-windows-store:PDP?PFN=[ID do pacote de aplicativo] 
    - Exemplo: ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Inicia uma pesquisa bingmaps
    - bingmaps:?q=[consulta de pesquisa] 
    - Exemplo:bingmaps:? q=starbucks,paris
    - Usar um esquema personalizado
    - [esquema personalizado]://[parâmetros de esquema personalizado]
    - Exemplo:myCustomProtocol://myCustomParams
    - Usar um pacote de dados (necessário o aplicativo da loja para a leitura da extensão)
    - [pasta][dados].[extensão]
    - Exemplo:myfolderdata.txt
 
## Criar uma URL de rastreamento:
-    Consulte a seção "Configurações" da <Documentação da interface do usuário > para obter instruções sobre a criação de uma URL de rastreamento que permitirá que os usuários transfiram um de seus outros aplicativos.
 
## Definir os textos do anúncio
Preencha o título, conteúdo e textos de botão do seu anúncio. 
Você pode direcionar um público-alvo de uma campanha futura com base nos comentários do reach de como os usuários responderam a essa campanha. O público-alvo pode ser com base nos comentários de se essa campanha foi apenas enviada, respondidas, acionada ou encerrada.

**Consulte também:**
- <a href="#ReachCriterion">Documentação da interface do usuário - Reach - Novo critério de envio</a>

## Conteúdo da pesquisa
 
![Reach-Content2][31] 
Preencha o título, descrição e textos de botão do seu anúncio. 
Em seguida, adicione as perguntas e opções de respostas para as suas perguntas.
Você pode direcionar um público-alvo de uma campanha futura com base nos comentários do reach de como os usuários responderam a essa campanha. O público-alvo pode ser baseado em se essa campanha foi apenas enviada, respondidas, acionada ou encerrada. O público-alvo também pode ser baseado nos comentários de resposta de pesquisa, onde a pergunta e resposta de opção são usadas como critérios.

**Consulte também:**

- <a href="#ReachCriterion">Documentação da interface do usuário - Reach - Novo critério de envio</a>
 
## Conteúdo de envio de dados
 
![Reach-Content3][32] 

## Escolha o tipo de dados
- Texto
- Dados binários
- Dados Base64

## Definir o conteúdo dos seus dados
- Se você optou por enviar dados de texto, copie e cole o texto na caixa "conteúdo".
- Se você optou por enviar dados binários ou base64, use o botão "carregar o arquivo" para carregar o seu arquivo.
- Você pode direcionar um público-alvo de uma campanha futura com base nos comentários do reach de como os usuários responderam a essa campanha. O público-alvo pode ser baseado em se essa campanha foi apenas enviada, respondidas, acionada ou encerrada.

**Consulte também:**

- <a href="#ReachCriterion">Documentação da interface do usuário - Reach - Novo critério de envio</a>

## Conteúdo de lado a lado (somente para o Windows Phone)

![Reach-Content4][33]

## Definir o conteúdo do seu lado a lado
A carga de bloco é o texto a ser exibido no lado a lado do seu aplicativo em dispositivos Windows Phone.
Um envio de lado a lado é a versão do serviço de notificação por push da Microsoft (MPNS) um envio nativo para o Windows Phone. O tipo de envio do lado a lado é o único tipo de envio que não tem uma resposta, e então o público-alvo de futuras campanhas não pode ser criado com os resultados de uma campanha de envio do lado a lado. 

**Consulte também:**

- [Documentação da API - API do Reach - Envio nativo][Link 4]

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
