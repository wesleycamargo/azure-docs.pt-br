<properties
	pageTitle="Perguntas frequentes sobre as Competições do Cortana Intelligence | Microsoft Azure"
	description="Perguntas frequentes sobre as Competições do Microsoft Cortana Intelligence."
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/05/2016"
	ms.author="haining;chlovel;garye"/>

# Perguntas frequentes sobre as Competições do Microsoft Cortana Intelligence

**O que são as Competições do Cortana Intelligence?**

A Microsoft está anunciando as Competições do Cortana Intelligence. As Competições do Cortana Intelligence permitem unir uma comunidade global de entusiastas de dados para resolver coletivamente alguns dos problemas de ciência de dados mais complexos do mundo. As Competições do Cortana Intelligence permitem que entusiastas de dados em todo o mundo compitam e criem modelos de ciência de dados altamente precisos e inteligentes. As competições que patrocinamos são baseadas em conjuntos de dados exclusivos que são disponibilizados publicamente pela primeira vez. Os participantes podem ganhar prêmios ou reconhecimento em nosso placar de líderes público dos 10 primeiros colocados. Acesse [aqui](http://aka.ms/CIComp) para visitar a home page das Competições.

**Com que frequência a Microsoft lançará novas competições?**

Lançaremos competições patrocinadas pela Microsoft regularmente, mais ou menos a cada 8 ou 12 semanas.

**Onde posso fazer perguntas gerais sobre a ciência de dados?**

Use nosso [fórum do Aprendizado de Máquina do Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**Como faço para participar de uma competição?**

Acesse a home page das Competições usando a Cortana Intelligence Gallery. Essa página contém todas as competições que estão em andamento. Cada competição terá instruções e regras de participação, prêmios e duração detalhados na página de inscrição. Acesse [aqui](http://aka.ms/CIComp) para visitar a home page das Competições.

1. Encontre a competição de que você gostaria de participar na Cortana Intelligence Gallery, leia todas as instruções e assista ao vídeo do tutorial. Em seguida, clique no botão "Ingressar na Competição" para copiar o Teste Inicial para seu espaço de trabalho existente do Aprendizado de Máquina do Azure. Se você ainda não tiver acesso ao espaço de trabalho, crie um com antecedência. Execute o Teste Inicial, observe a métrica de desempenho e use sua criatividade para melhorar o desempenho do modelo. Você provavelmente passará a maior parte do seu tempo nessa etapa.   

2. Crie um Teste Preditivo com o modelo treinado fora do Teste Inicial. Ajuste cuidadosamente o esquema de entrada e saída do serviço Web para fazer com que estejam em conformidade com os requisitos especificados nas regras da Competição. O documento do tutorial geralmente terá instruções detalhadas sobre como fazer isso. Você também pode assistir ao vídeo do tutorial, se ele estiver disponível.

3. Implante um serviço Web fora do Teste Preditivo. Teste o serviço Web usando o botão Teste ou o modelo do Excel criado automaticamente para verificar se ele está funcionando corretamente.

4. Envie seu serviço Web como entrada da competição e veja sua pontuação pública na página Cortana Intelligence Gallery da competição. E comemore se você estiver no placar de líderes! Depois de enviar uma entrada, você pode retornar ao Teste Inicial copiado, iterar e atualizar seu Teste Preditivo, atualizar o serviço Web e enviar uma nova entrada.

**Posso usar ferramentas de software livre para participar dessas competições?**

Os participantes da competição usam o Estúdio de Aprendizado de Máquina do Azure, um serviço baseado em nuvem no Cortana Intelligence Suite para o desenvolvimento de modelos de ciência de dados e a criação de entradas da Competição para envio. O Estúdio AM não só fornece uma interface gráfica para construir testes de aprendizado de máquina, mas também permite colocar seus próprios scripts de R e/ou Python para execução nativa. O tempo de execução de R e Python no Estúdio AM vêm com um amplo conjunto de pacotes de R/Python de software livre e você também pode importar seus próprios pacotes como parte do teste. O Estúdio AM também tem um serviço interno de Anotações do JuPyteR para você explorar dados livremente. Claro, você sempre pode baixar os conjuntos de dados usados na Competição e explorá-los em sua ferramenta favorita fora do Estúdio AM.

**É necessário ser um cientista de dados para participar?**

Não. Na verdade, incentivamos entusiastas de dados, pessoas que têm curiosidade a respeito da ciência de dados e outros aspirantes a cientista de dados para participar de nossa competição. Criamos documentos de suporte para permitir que todos possam participar. Nosso público-alvo é:

* Desenvolvedores de dados, cientistas de dados, profissionais de BI e de análise: aqueles responsáveis por produzir conteúdo de dados e análises para outras pessoas consumirem

* Administradores de dados: aqueles que têm conhecimento sobre os dados, o que eles significam, como se destinam a serem usados e para qual finalidade

* Estudantes e Pesquisadores: aqueles que estão aprendendo e obtendo habilidades relacionadas a dados em programas acadêmicos universitários ou participantes de MOOCs (Massively Open Online Courses, Cursos Online de Acesso Livre)


**Posso me inscrever com meus colegas como uma equipe?**

A plataforma da Competição atualmente não é compatível com a participação de equipes. Cada entrada na Competição está vinculada a uma identidade de usuário exclusiva.

**Preciso pagar para participar de uma competição?**

A participação nas Competições é gratuita. No entanto, você precisa ter acesso a um espaço de trabalho de Aprendizado de Máquina do Azure para participar. Você pode criar um espaço de trabalho livre sem um cartão de crédito. Para isso, basta fazer logon com uma conta da Microsoft válida ou uma conta do Office 365. Se você já for um cliente do Azure ou do Cortana Intelligence Suite, poderá criar e usar um espaço de trabalho Standard com a mesma assinatura do Azure. Se quiser comprar uma assinatura do Azure, poderá fazê-lo [aqui](https://azure.microsoft.com/pricing). Observe que as taxas padrão se aplicam ao uso de um espaço de trabalho Standard para construir testes. Confira as informações sobre preços de Aprendizado de Máquina do Azure [aqui](https://azure.microsoft.com/pricing/details/machine-learning/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Quais são as pontuações públicas e privadas?**

Na maioria das competições, você verá que receberá uma pontuação pública para cada envio que fizer, geralmente dentro de 10 a 20 minutos. No entanto, após o término da competição, você receberá uma pontuação privada que é usada na classificação final. O que ocorre é o seguinte:

* O conjunto de dados inteiro usado na competição será dividido aleatoriamente com estratificação em dados de treinamento e de teste (restantes). A divisão aleatória é estratificada para garantir que as distribuições de rótulos nos dados de treinamento e de teste são consistentes.
 
* Os dados de treinamento são carregados e fornecidos a você como parte do Experimento Inicial na configuração do módulo Importar Dados.

* Os dados de teste adicionais são divididos em dados de teste públicos e privados usando a mesma estratificação.

* Os dados de teste públicos são usados na fase inicial de pontuação. O resultado é chamado de pontuação pública e é isso que você obtém em seu histórico de envio ao enviar sua entrada. Essa pontuação é calculada para cada entrada que você enviar. Essa pontuação pública é usada para classificá-lo no placar de líderes público.

* Os dados de teste privado são usados na fase final de pontuação após o encerramento da competição. Eles são chamados de pontuação privada.

* Para cada participante, um número fixo, que pode variar dependendo da competição, entre as entradas com as pontuações públicas mais altas são selecionadas automaticamente para ingressar na rodada de pontuação privada. A entrada com a pontuação privada mais alta é selecionada para entrar na classificação final, que, em última análise, determina os vencedores do prêmio.

**Os Clientes podem patrocinar uma Competição em nossa plataforma?**

Apreciamos que organizações de terceiros se juntem a nós e patrocinem competições públicas e privadas em nossa plataforma. Temos uma equipe de integração de competições que ficará contente em discutir o processo de integração de tais competições. Entre em contato conosco pelo email [compsupport@microsoft.com](mailto:compsupport@microsoft.com) para obter mais detalhes.

**Quais são as limitações de envios?**

Uma competição típica pode optar por limitar o número de entradas que você pode enviar em um período de 24 horas (fuso horário UTC 00:00:00 a 23:59:59) e o número total de entradas que você pode enviar ao longo da competição. Você receberá mensagens de erro apropriadas quando um limite for excedido.

**O que acontecerá se eu ganhar uma Competição?**

A Microsoft verificará os resultados do placar de líderes privado e, logo em seguida, entrará em contato com você. Certifique-se de que o endereço de email em seu perfil do usuário está atualizado.

**Como faço para receber a recompensa se eu ganhar uma Competição?**

Se você for um vencedor do concurso, precisará assinar uma declaração de qualificação, licença e isenção. Este formulário reitera as Regras da Competição. Os vencedores precisarão preencher um formulário Fiscal dos EUA W-9 ou um formulário W-8BEN se não forem contribuintes dos EUA. Entraremos em contato com todos os vencedores como parte do processo de pagamento de recompensas usando seu email de registro. Consulte nossos [Termos e Condições](http://aka.ms/comptermsandconditions) para obter mais detalhes.

**E se mais de uma entrada receber a mesma pontuação?**

A hora de envio fará o desempate. A entrada enviada mais cedo prevalecerá sobre a entrada enviada mais tarde.

**Posso participar usando um espaço de trabalho Convidado?**

Não. Você deve usar um espaço de trabalho Gratuito ou Standard para participar. Você pode abrir o teste inicial da Competição em um espaço de trabalho Convidado. Mas você não poderá criar uma entrada válida para o envio.

**Posso participar usando um Espaço de Trabalho em qualquer região do Azure?**

Atualmente, a plataforma da Competição só é compatível com entradas de envio de um espaço de trabalho na região do Azure no Centro-Sul dos EUA. Todos os espaços Gratuitos estão no Centro-Sul dos EUA. Mas, se você optar por usar um espaço de trabalho Standard, verifique se que ele reside na região do Azure no Centro-Sul dos EUA. Caso contrário, seu envio não terá êxito.

**Podemos manter as Soluções/Entradas das Competições dos Usuários?**

As entradas de usuário são mantidas somente para fins de avaliação para identificar as soluções vencedoras. Consulte nossos [Termos e Condições](http://aka.ms/comptermsandconditions) para obter informações específicas.

<!---HONumber=AcomDC_0615_2016-->