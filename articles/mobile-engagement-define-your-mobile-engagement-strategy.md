<properties 
	pageTitle="Definir sua estratégia do Mobile Engagement" 
	description="Saiba como usar carregamentos e otimizar seu Mobile Engagement com análises e notificações por push." 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="kapiteir" />

#Definir sua estratégia do Mobile Engagement

Você escreveu o seu aplicativo por um motivo: para que os usuários o usem!

Acreditamos que você se esforce bastante tentando torná-lo um grande aplicativo que agradará muito aos usuários. Você provavelmente também investiu uma quantidade considerável de orçamento de marketing para adquirir os usuários. Mas, após o pico inicial animador de usuários, você pode ver que eles, lentamente, vão parando de usar o aplicativo. **E é para isso que o Azure Mobile Engagement serve!**: para fazer com que eles permaneçam por perto e para permitir que você melhore incrementalmente seu aplicativo por meio de testes e aprenda.

Nossa abordagem para melhorar o uso e a retenção baseia-se em atrair os usuários do aplicativo por meio de notificações por push e mensagens no aplicativo, mas de uma maneira muito especial, com mensagens e comunicação adaptadas a eles, cada uma de acordo com seu comportamento em seu aplicativo. Nossa meta é permitir que você se comunique com o público certo no momento certo e o local certo.

Mas, para isso, você terá que começar a **compreender seus usuários**, em seguida, criar grupos com base em suas características (chamamos de segmentos) ou no que eles fizeram e criar comunicações relevantes para cada segmento.

##O Mobile Engagement serve seus objetivos

*Falamos sobre a retenção, o uso, mas para quê?*

**A criação de sua estratégia do Mobile Engagement requer procurar primeiro os KPIs e os objetivos de seu aplicativo.**

Iniciar definindo esses objetivos/KPIs ajuda a definir seus casos de uso do Engagement com o prisma correto. 

Casos de uso são uma lista simples de campanhas que você gostaria de fazer para se comunicar com os usuários, desde uma simples Boas-vindas até a notificação de utilitário muito avançado disparada pelo seu sistema de TI. Um caso de uso bem-construído deve incluir pelo menos o trio  *o que-quando-onde*:

1. Uma designação muito curta (por exemplo, "Campanha de Boas-vindas")
2. **O que**: Um exemplo de mensagem (por exemplo, "Feliz em tê-lo integrado! Lembre-se de fazer logon para obter seu primeiro mês gratuito!"). Esta mensagem não é, de forma alguma, a final; você poderá alterá-la sempre que desejar, mas geralmente é útil para começar a pensar sobre o que queremos dizer
3. **Quem**: O segmento que receberá essa mensagem (por exemplo, "Todos os usuários que iniciaram o aplicativo pela primeira vez 3 dias atrás, visitaram a página de logon, mas não fizeram logon")
	- Sim, você pode fazer isso facilmente com o Azure Mobile Engagement:)
	- Novamente, isso não precisa ser final, pois você pode definir os segmentos a qualquer momento, mas é importante definir seus critérios de segmentação para garantir que você colete os dados certos
4. **Quando**: O tempo de sua campanha. Ele pode ser em uma determinada data ou depois de uma ação específica, com base em um gatilho. O Mobile Engagement oferece uma quantidade importante das possibilidades de tempo corretas em sua comunicação

Uma vez definidos os casos de uso e o segmento, ele fornece uma diretriz para definir os dados que devem ser coletados em um aplicativo. Essa é a função de um *"Plano de Marca"*. Um plano de marca permite garantir que a coleta de dados seja especificada para os desenvolvedores. Dessa forma, os desenvolvedores são capazes de incorporar o Mobile Engagement à configuração certa para você trabalhar suas campanhas com os dados corretos. Também é muito importante executar testes para garantir que a integração esteja correta e que ela colete aquilo que você precisa.

Com base na integração, depois que os aplicativos são publicados, como um Comerciantes poderão ver sua análise em tempo real, segmentar seu público-alvo e, em seguida, começar a enviar notificação por push inteligente e segmentada para se comunicar com usuários finais ou não do aplicativo.

###Casos de uso para começar
1. Estratégia de boas-vindas: Crie várias campanhas de notificação por push com base no comportamento do usuário final na inicialização do aplicativo para envolver novamente D+2/5/10/15 após a primeira sessão e aumentar a primeira a retenção de execução.	
2. Promova um novo conteúdo (recurso, artigo/vídeo, produto, etc.) com base no comportamento do usuário final para enviar as informações somente para os usuários finais que têm mais probabilidade de se envolver.	
3. Classifique o aplicativo: meta de menos de 1% de sua base de usuários com maior probabilidade de classificar o aplicativo como 5 estrelas no repositório.	
4. Aumento de assinaturas: Promova conteúdo valioso para usuários finais que ainda não os viram para aumentar a assinatura.
5. Tutorial: O tutorial não é mais obrigatório para todos. Por que não compilar tutoriais excelentes no aplicativo e, em seguida, acioná-los por meio de mensagens no aplicativo somente se o usuário parecer não usar ou ter dificuldades ao usar um recurso?

##Por que você precisa de Análise no Engage?

Como você pode perceber neste ponto, fazer uma notificação de envio de difusão apenas não é suficiente. O principal conceito do Mobile Engagement é ajudar os comerciantes e os desenvolvedores a envolver os usuários finais certos, no momento certo e no lugar certo. Para saber os três principais conceitos, é essencial reunir as análises de seu aplicativo e, em seguida, usá-las para o público-alvo do segmento. Isso também é ainda mais eficiente quando os segmentos de comportamento complementam os dados de seu banco de dados ou CRM ou de um canal de distribuição. O Mobile Engagement permite recuperar dados de qualquer lugar e usa-o para o público-alvo certo.

Para ser o mais contextual possível quando envolver seu público, é crucial ter conhecimento sobre o comportamento dos usuários finais, para saber o status em tempo real. A coleta de dados permite que os comerciantes se concentrem no que realmente importa para executar casos de uso e atingir os objetivos da estratégia do Mobile Engagement. Atingir os objetivos acima também é a razão pela qual a prática recomendada é, de fato, não reunir tudo na análise, mas apenas aquilo que permite se concentrar naquilo se quer aprender e nos casos de uso. Essa é uma boa maneira de iniciar, experimentar, testar e aprender a usar a solução, direcionar uma notificação por push inteligente e aumentar a retenção de um aplicativo para colocá-lo em um nível de história de sucesso.

>[AZURE.NOTE] Lembre-se: muitos dados matam os dados!

###Casos de uso e práticas recomendadas

A seguir, abordaremos brevemente alguns dos principais casos de uso que poderão ocorrer com os nossos clientes quando eles começarem.

####Mídia

Coletar o tipo de conteúdo que é consumido pelo usuário final e, em seguida, segmentar o público com base nesse comportamento para destinar um tipo específico de conteúdo somente para um público que provavelmente o consumirá. Evita gerar um spam para todos os usuários e garante uma retenção melhor.

####M-comércio

Colete as categorias de produtos mais visitadas dentro do aplicativo e o público alvo para promover um desconto ou um novo produto nessa categoria que o usuário final terá mais probabilidade de comprar. Objetivo de aumentar a receita. Novamente, o objetivo é evitar spam!

####Jogos

Colete o nível de jogo de um usuário final e o tempo gasto em um determinado período para destinar o público que pode ser interrompido e provavelmente saltar para um próximo nível com uma oferta de bônus.

Comunique sobre eventos específicos com um incentivo aos usuários que não jogaram por algum tempo para tentar atraí-los novamente

####Varejo

Colete quais produtos ou marcas é mais provável que um determinado público consuma com base em favorito ou comportamento e leve o público a armazenar para aumentar a receita de compra.

####Serviços bancários

Colete se os usuários finais criaram uma conta na primeira inicialização do aplicativo. O objetivo é implantar uma estratégia de boas-vindas com notificação por push de destino e aumentar o número de assinaturas da conta. 

###Como criar um bom plano de marca?

Um plano de marca deve ser como uma descrição do caminho de usuário ou um tipo de fluxo de trabalho do aplicativo, fornecendo todas as marcas necessárias (dados) que devem ser coletadas para que se tenha uma análise suficiente para entender o comportamento do usuário e segmentar corretamente a base de usuários. Isso não é um processo técnico. Portanto, os comerciantes são capazes de especificar os dados que desejam coletar, com base em sua estratégia do Mobile Engagement.
 
O mínimo é marcar pelo menos todas as telas (chamadas de  `Atividades` no Mobile Engagement) de um aplicativo. Isso permitirá noções básicas sobre o caminho do usuário. 

Uma atividade pode incorporar  `Eventos` que coletam informações sobre a ação como clicar em um botão. Isso permite coletar interação dentro do aplicativo. Portanto, os comerciantes são capazes de saber o que os usuários da tela estão visitando e o que eles estão fazendo. 

`Trabalhos` são ações com uma duração. Isso é muito útil para o comerciante entender quanto tempo levará para um usuário criar uma conta ou fazer logon, por exemplo. Isso também pode ser útil para os desenvolvedores monitorarem quanto tempo levará para chamar um serviço Web. 

Os `Erros` também podem ser monitorados para saber se os usuários estão tendo problemas em seu aplicativo. Por exemplo, obter muitas vezes os problemas de conexão. 

Todos esses tipos de dados podem ser aumentados com Parâmetros (`informações extra` no Mobile Engagement) que permite coletar dados dinâmicos do aplicativo. Isso é importante para permitir uma segmentação bem refinada. Por exemplo, os comerciantes poderiam segmentar o usuário com base no tipo de conteúdo que consomem. O tipo de conteúdo será essas informações dinâmicas de uma  `atividade` ou de um  `evento`. 

As `Informações de aplicativos`  são dados que permitem saber, em tempo real, o que é o status do aplicativo ou do usuário. Isso ajuda também a categorizar um público de base e de destino rapidamente. Pode ser, por exemplo, um status verdadeiro/falso se o usuário estiver fazendo logon ou não, ou sua data de expiração da assinatura.

####Exemplo de marcas

*Caso de uso: Comportamento do público do segmento para direcionar o usuário final certo para o conteúdo certo da notificação por push*

1.	Envie notificação por push para promover uma categoria de produto: colete dados de comportamento para o público do segmento com base na categoria de produto que eles visitaram x as vezes em um determinado período ou um item específico que eles adicionaram em um carrinho. Os dados coletados permitirão segmentar e, em seguida, enviar notificação por push para o público certo.
2.	Avalie o aplicativo: Colete dados de acordo com o conteúdo compartilhado pelo público em rede social. O objetivo é o público-alvo do segmento, determinando os "Embaixadores" de seu aplicativo, que são o melhor público de seu aplicativo para fazer a classificação 5 estrelas de seu aplicativo na loja com um aplicativo de notificação por push.

	![][1]

*Caso de uso: Dados declarativos*
1.	Notícias de alerta de segmento: Colete dados declarativos para o público do segmento com base nas suas preferências. Ele permite enviar notificação por push de um tópico específico que realmente interessa a um público específico.
2.	Público do segmento com base no status de logon. Colete dados para saber se um usuário está conectado ou se criou uma conta. Ajuda no direcionamento de usuários finais que ainda não se conectaram e enviaram notificação por push para atrair os usuários finais a participarem.
	![][2]

### Próximas etapas
- Visite os [Conceitos do Mobile Engagement] para saber mais sobre os conceitos básicos do Mobile Engagement.
- Visite os [Tutoriais] para saber mais sobre a implementação.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-engagement-strategy/use-case2.png

<!-- URLs. -->
[Conceitos do Mobile Engagement]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Tutoriais]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/

<!--HONumber=47-->
