<properties
   pageTitle="Cenários comuns de Catálogo de Dados do Azure"
   description="Reveja cenários comuns no Catálogo de Dados do Azure: registrar, aprimorar, explorar, entender e consumir fontes de dados, bem como remover os metadados da fonte de dados."
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/> <tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>


# Cenários comuns de Catálogo de Dados do Azure

Este artigo apresenta cenários comuns nos quais o Catálogo de Dados do Azure pode ajudar as organizações a obter mais valor de suas fontes de dados existentes.

## Cenário nº 1 - Registro de fontes de dados central

As organizações geralmente têm um número de fontes de dados de alto valor. Essas fontes de dados incluem a linha de negócios de sistemas OLTP, data warehouse e business intelligence / bancos de dados de análise. Geralmente, o número de sistemas e a sobreposição entre os sistemas aumenta ao longo do tempo conforme as necessidades dos negócios evoluem, bem como os negócios em si evoluem através de fusões e aquisições.

Muitas vezes é difícil para os usuários saber onde localizar os dados dentro dessas fontes de dados. Perguntas como essas são todas muito comuns:

- Dos três sistemas de RH usados dentro da empresa, qual devo usar para criar esse tipo de relatório?
- Onde posso ir para obter os números de vendas oficiais para o ano fiscal que acabou de se encerrar?
- Quem devo consultar ou qual é processo devo usar para obter acesso ao data warehouse?
- Não sei se esses números estão certos. A quem posso consultar para obter informações sobre como esses dados devem ser usados para compartilhar esse painel com minha equipe?

Nesse cenário, o Catálogo de Dados do Azure pode ajudar. As fontes de dados central, de alto valor e gerenciada por TI que são usadas em toda a organização costumam ser o ponto de partida lógico para popular o catálogo. Embora qualquer usuário possa registrar uma fonte de dados, iniciar o catálogo com fontes de dados que têm mais probabilidade de fornecer valor para o maior número de usuários ajudará a conduzir a adoção e o uso do sistema. Para os clientes que estão começando no Catálogo de Dados do Azure, identificar e registrar as principais fontes de dados usadas por muitas equipes diferentes de consumidores de dados podem ser as primeiras etapas para o sucesso.

## Cenário nº 2 - Autoatendimento de Business Intelligence

Embora as soluções de business intelligence corporativas tradicionais continuem a ser uma parte valiosa das estruturas de dados de muitas organizações, a alteração ritmo dos negócios tornou o BI de autoatendimento cada vez mais importante. BI de autoatendimento permite que os operadores de informações e analistas criem seus próprios relatórios, pastas de trabalho e painéis sem depender de uma equipe de TI central, nem ficando restrito à programação e disponibilidade dessa equipe de TI.

Em cenários de BI de autoatendimento, é comum os usuários combinarem dados de várias fontes, muitos dos quais podem não terem sido usados anteriormente para BI e análise. Embora algumas dessas fontes de dados possam já serem conhecidas, geralmente há um processo para descobrir o que deve ser feito para localizar e avaliar possíveis fontes de dados para uma determinada tarefa.

Tradicionalmente, esse processo de descoberta é manual: analistas usarão suas conexões de rede ponto a ponto para identificar outras pessoas que trabalham com os dados que estão buscando. Quando uma fonte de dados for encontrada, ela pode ser usada, mas o processo se repetirá novamente para cada esforço de BI de autoatendimento subsequente, com vários usuários executando o mesmo processo manual redundante de descoberta.

Com o Catálogo de Dados do Azure, os usuários podem dividir esse ciclo de esforço redundante. Depois de uma fonte de dados ser descoberta através dos meios tradicionais, um analista pode registrá-la para torná-la mais facilmente identificável no futuro. Embora o usuário possa adicionar mais valor anotando os ativos de dados registrados, isso não precisa acontecer ao mesmo tempo que o registro. Os usuários podem contribuir ao longo do tempo, conforme permitido pelas suas agendas, agregando valor gradualmente para as fontes de dados registradas no catálogo.

Esse crescimento orgânico do conteúdo do catálogo é um complemento natural para o registro inicial de fontes de dados central. Pré-popular o catálogo com dados que muitos usuários precisarão pode ser uma motivação para o uso e descoberta iniciais. Habilitar usuários para registrem e anotarem fontes adicionais pode ser uma maneira de envolver a eles e seus colegas.

Também vale a pena observar que embora esse cenário se concentre especificamente no BI de autoatendimento, os mesmos padrões e desafios também se aplicam a projetos de BI corporativos de grande escala. Qualquer esforço que envolva um processo manual de descoberta de fonte de dados é um esforço que pode agregar valor à organização por meio do uso do Catálogo de Dados do Azure.

## Cenário nº 3 - Capturando o conhecimento do grupo

Como saber quais dados você precisa para fazer seu trabalho e onde encontrá-los?

Se você estiver na sua função por algum tempo, você provavelmente já sabe. Você já passou por um processo de aprendizado gradual e aprendeu ao longo do tempo sobre as fontes de dados que são essenciais para o seu trabalho diário.

Quando um novo funcionário chega à sua equipe, como ele descobre quais dados precisa fazer seu trabalho e onde encontrá-los?

Provavelmente ele perguntará a você.

Essa transferência contínua de conhecimento do grupo faz parte do processo de descoberta de fonte de dados em organizações grandes e pequenas. Membros da equipe mais antigos e experientes acumularam conhecimento ao longo dos anos e os membros mais novos da equipe aprendem a perguntar quando surgem dúvidas. As informações mais importantes geralmente existem apenas na cabeça de algumas pessoas importantes e quando elas estão de férias ou deixam a equipe, a organização sofre.

Às vezes, esses especialistas de dados se esforçarão para documentar seu conhecimento, compartilhando-o por email ou em documentos do Word em um site SharePoint da equipe. Embora isso possa ser útil, há um novo problema de descoberta: como as pessoas sabem quais documentos existem e onde encontrá-los?

O Catálogo de Dados do Azure fornece um local para compartilhar esse conhecimento de grupo, tornando-o facilmente identificável. Especialistas em dados podem anotar diretamente os ativos de dados e também incluir links para a documentação existente. Isso não apenas captura o conhecimento em si, mas também o coloca na mesma experiência usada para descoberta de fonte de dados. Quando alguém usa o catálogo para descobrir um fonte de dados, não encontrará apenas a fonte em si, mas também o conhecimento do especialista que anteriormente existia somente na mente do próprio especialista.

<!---HONumber=July15_HO3-->