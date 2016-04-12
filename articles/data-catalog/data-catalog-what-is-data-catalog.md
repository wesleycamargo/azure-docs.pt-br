<properties
   pageTitle="O que é o Catálogo de Dados do Azure? | Microsoft Azure"
   description="Este artigo fornece uma visão geral do Catálogo de Dados do Microsoft Azure, incluindo seus recursos e problemas que ele deve endereçar. O Catálogo de Dados do Azure fornece recursos que permitem que qualquer usuário – desde analistas a cientistas de dados e desenvolvedores – registre, descubra, entenda e consuma fontes de dados."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/31/2016"
   ms.author="maroche"/>

# O que é o Catálogo de Dados do Azure?

O **Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em fontes de dados da empresa. O **Catálogo de Dados do Azure** fornece recursos que permitem que qualquer usuário, desde analistas a cientistas de dados e desenvolvedores, registre, descubra, entenda e consuma fontes de dados, assim como contribua com seu conhecimento para criar e dar suporte a uma comunidade e cultura de dados.

## Descrição do problema - motivação e visão geral

Tradicionalmente, a descoberta de fontes de dados da empresa tem sido um processo orgânico com base no conhecimento tribal. Isso apresenta vários desafios para as empresas que desejam obter o valor máximo de seus ativos de informações.

-	Os usuários não estão cientes de que as fontes de dados existem, a menos que entrem em contato com elas como parte de outro processo; não há um local central onde as fontes de dados são registradas.
-	A menos que um usuário saiba o local de uma fonte de dados, ele não pode se conectar aos dados usando um aplicativo cliente; experiências de consumo de dados exigem que os usuários conheçam a cadeia de conexão ou o caminho.
-	A menos que um usuário saiba a localização da documentação da fonte de dados, ele não entenderá o uso pretendido dos dados; documentação e fontes de dados residem em locais diferentes e são consumidas por meio de experiências diferentes.
-	Se um usuário tiver dúvidas sobre um ativo de informações, ele deve localizar o especialista ou a equipe responsável pelos dados e envolver os especialistas offline; não há nenhuma conexão explícita entre os dados e aqueles com perspectivas de especialistas sobre seu uso.
-  A menos que um usuário compreenda o processo de solicitação de acesso à fonte de dados, a descoberta da fonte de dados e de sua documentação ainda não permitirá que ele acesse os dados necessários.

Enquanto esses desafios enfrentam consumidores de dados, os usuários responsáveis por produzir e manter informações enfrentam desafios próprios.

-	A anotação de fontes de dados com metadados descritivos costuma ser um esforço perdido; aplicativos clientes geralmente ignoram descrições armazenadas na fonte de dados.
-	Criar documentação para fontes de dados geralmente é um esforço perdido; manter a documentação em sincronia com a fonte de dados é uma responsabilidade contínua e os usuários não têm confiança na documentação, uma vez que ela é geralmente percebida como desatualizada.
- Restringir o acesso à fonte de dados e garantir que os consumidores de dados saibam como solicitar o acesso são um desafio contínuo.

Criar e manter documentação para uma fonte de dados é um processo complexo e demorado. O desafio de tornar essa documentação prontamente disponível para qualquer pessoa que usa a fonte de dados geralmente é ainda mais.

Quando combinados, esses desafios apresentam uma barreira significativa para as empresas que desejam encorajar e promover o uso e a compreensão dos dados da empresa.

## Descrição do serviço

O **Catálogo de Dados do Azure** foi projetado para resolver esses problemas e permite que as empresas obtenham o máximo de seus ativos de informações existentes, tornando-os facilmente identificáveis e compreensíveis pelos usuários que precisam dos dados que gerenciam.

O **Catálogo de Dados do Azure** fornece um serviço baseado em nuvem no qual a fonte de dados pode ser registrada. Os dados permanecem no local existente, mas uma cópia dos metadados, juntamente com uma referência ao local da fonte de dados, é adicionada ao **Catálogo de Dados do Azure**. Esses metadados também são indexados para tornar cada fonte de dados fácil de descobrir por meio de pesquisa e compreensível para os usuários que os descobrirem.

Depois que uma fonte de dados é registrada, seus metadados podem ser aprimorados pelo usuário que executou o registro ou por outros usuários na empresa. Qualquer usuário pode anotar uma fonte de dados, fornecendo descrições, marcas ou outros metadados, como documentação, e processos para a solicitação de acesso à fonte de dados. Esses metadados descritivos complementam os metadados estruturais (como nomes de colunas e tipos de dados) registrados da fonte de dados, para facilitar a descoberta e o entendimento.

Descobrir e entender fontes de dados e seu uso é o principal objetivo de registrar as fontes. Quando usuários corporativos precisam de dados para seus esforços (que podem ser de business intelligence, desenvolvimento de aplicativos, ciência de dados ou qualquer outra tarefa em que os dados certos são necessários), eles podem usar a experiência de descoberta do **Catálogo de Dados do Azure** para localizar rapidamente dados que correspondam às suas necessidades, compreendê-los para avaliar a adequação à finalidade e consumir dados abrindo a fonte de dados na ferramenta de sua preferência. Ao mesmo tempo, o **Catálogo de Dados do Azure** permite que os usuários contribuam com o catálogo, marcando, documentando e anotando as fontes de dados que já foram registradas, e registrando novas fontes de dados que podem ser descobertas, compreendidas e consumidas pela comunidade de usuários do catálogo.

![Recursos do Catálogo de Dados do Azure](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## Registrar fontes de dados

O registro da fonte de dados é realizado usando a ferramenta de registro de fonte de dados do **Catálogo de Dados do Azure**. Esse aplicativo pode ser baixado do portal do **Catálogo de Dados do Azure**.

O processo de registro envolve três etapas básicas:

1.	Conectar a uma fonte de dados - o usuário especifica o local da fonte de dados e as credenciais para conectar à fonte de dados, como uma instância do SQL Server.
2.	Selecionar os objetos a registrar - o usuário seleciona os objetos no local especificado, que deve estar registrado no **Catálogo de Dados do Azure**. Esses podem ser o conjunto completo de tabelas em todos os bancos de dados no servidor ou um subconjunto especificamente selecionado de tabelas e exibições.
3.	Concluir o registro - o usuário conclui o processo e a ferramenta de registro da fonte de dados, extrai os metadados estruturais da fonte de dados e envia esses metadados para o serviço de nuvem do **Catálogo de Dados do Azure**.

> [AZURE.NOTE] Para exibir uma lista dos tipos de fonte de dados e ativos com suporte no **Catálogo de Dados do Azure**, confira: [Fontes de dados com suporte do Catálogo de Dados do Azure](data-catalog-dsr.md)


> [AZURE.IMPORTANT]O registro de uma fonte de dados no **Catálogo de Dados do Azure** não copiará os dados da fonte de dados, a menos que você selecione "Incluir Visualização" na ferramenta de registro de fonte de dados. O registro copia os metadados da fonte de dados, não os dados. Exemplos de metadados incluem nomes das tabelas e outros objetos de fonte de dados, juntamente com tipos de dados e nomes de colunas e outros atributos da fonte de dados. Os metadados também incluem o local da fonte de dados, para que os usuários que descobrirem as fontes de dados usando o **Catálogo de Dados do Azure** possam se conectar à fonte de dados. Se você selecionar "Incluir Visualização", a ferramenta de registro da fonte de dados também copiará para o **Catálogo de Dados do Azure** um pequeno conjunto de registros que serão exibidos aos usuários que descobrirem a fonte de dados no portal do **Catálogo de Dados do Azure**.

## Aprimore os metadados da fonte de dados

Quando o registro for concluído, as fontes de dados poderão ser descobertas e consumidas, mas o verdadeiro valor do **Catálogo de Dados do Azure** provém do fato de haver metadados de negócios descritivos na mesma experiência que os metadados estruturais extraídos da fonte de dados. Esses metadados adicionais oferecem três benefícios significativos:

-	As fontes de dados registrados são mais facilmente identificáveis. Os metadados fornecidos pelo usuário são adicionados ao índice de pesquisa do **Catálogo de Dados do Azure**. Isso permite aos usuários localizar dados usando termos e conceitos que podem não estar presentes na fonte de dados original. Por exemplo, se uma tabela de banco de dados que contém dados do cliente for denominada "tbl\_c45", fornecer um nome amigável de "Cliente" facilitará a descoberta pelos usuários que procuram dados de cliente. Da mesma forma, fornecer uma descrição que inclui nomes de relatórios, painéis ou processos que usam dados facilitará a descoberta da fonte de dados pelos usuários que usam esses artefatos downstream como termos de pesquisa.
-	As fontes de dados registradas são mais facilmente compreendidas uma vez descobertas. Os metadados fornecidos pelo usuário são apresentados a qualquer usuário do **Catálogo de Dados do Azure** que exiba a fonte de dados com anotações, o que ajuda a fornecer contexto e informações adicionais. A maioria das fontes de dados geralmente não inclui descrições significativas ou documentação, e aquelas que o fazem são geralmente voltadas ao público técnico de desenvolvedores de DBA ou banco de dados. Aprimorando as fontes de dados no **Catálogo de Dados do Azure** com descrições e marcas apropriadas ao público-alvo, os usuários podem ajudar a garantir que aqueles que descobrirem os dados possam entender seus detalhes e o uso pretendido.
-  Cada fonte de dados registrada pode incluir informações de acesso de solicitação, para que os usuários possam compreender com facilidade e seguir os processos existentes para a solicitação de acesso à fonte de dados e a seus dados.

> [AZURE.NOTE] Cada usuário do **Catálogo de Dados do Azure** pode adicionar suas próprias marcas e descrições dos ativos de dados e atributos. O **Catálogo de Dados do Azure** acompanhará o valor e a fonte de cada anotação e exibirá o usuário que a adicionou. Essa abordagem crowdsourcing aos metadados garante que cada usuário com uma perspectiva dos dados e seu uso possa compartilhar suas opiniões e recursos com a comunidade de usuários em geral.

## Explorar, descobrir e entender

O objetivo de registrar e enriquecer as fontes de dados no **Catálogo de Dados do Azure** é para que elas possam ser descobertas, compreendidas e usadas por usuários em toda a empresa. O portal do **Catálogo de Dados do Azure** é a principal ferramenta para esse processo.

O portal do **Catálogo de Dados do Azure** fornece dois mecanismos primários para exploração e descoberta: pesquisa e filtragem.

Para pesquisar o **Catálogo de Dados do Azure** em busca de fontes de dados, basta digitar um termo de pesquisa na caixa de pesquisa no portal do **Catálogo de Dados do Azure**. O portal exibirá um bloco para cada fonte de dados registrada que corresponda ao termo da pesquisa; os blocos conterão o nome, a descrição e as marcas atribuídas à fonte de dados, juntamente com outras informações de alto nível.

Para filtrar o conteúdo do **Catálogo de Dados do Azure**, basta selecionar um ou mais filtros apresentados no portal do **Catálogo de Dados do Azure**. Isso restringirá os blocos exibidos no portal a apenas aqueles que correspondem aos critérios de filtro especificados. Você pode filtrar fontes de dados sem pesquisar ou filtrar os resultados de uma pesquisa.

Para exibir informações mais completas para uma fonte de dados e entender se ela é apropriada para a tarefa em mãos, basta clicar no bloco da fonte de dados; o painel Propriedades será exibido e conterá todos os metadados.

Na parte superior do painel Propriedades, haverá botões adicionais:

1.	Visualização: a seleção deste botão exibirá o conjunto estático de registros de visualização da fonte de dados, se a visualização foi selecionada durante o registro da fonte de dados.
2.	Esquema: se esse botão for selecionado, será exibido o esquema da fonte de dados, incluindo nomes de colunas e tipos de dados e todos os metadados em nível de coluna no **Catálogo de Dados do Azure**.

> [AZURE.NOTE] É importante lembrar que a experiência de **Descobrir** pode ser um ponto de entrada para a experiência de **Enriquecer** e não apenas para a experiência de **Consumir**. A abordagem de crowdsourcing que o **Catálogo de Dados do Azure** traz significa que qualquer usuário que descobre uma fonte de dados registrada pode compartilhar suas opiniões sobre os dados, além de usar os dados que descobriu.

## Remover metadados da fonte de dados

Depois que uma fonte de dados é registrada, às vezes, pode ser necessário remover a referência de fonte de dados do **Catálogo de Dados do Azure**. Isso pode ser em razão de exigências comerciais ou desativação do sistema de origem. Independentemente do motivo, o **Catálogo de Dados do Azure** facilita a remoção de fontes de dados; você só precisa selecioná-las para exclusão para que elas não possam mais ser descobertas e consumidas.

> [AZURE.IMPORTANT] A exclusão de uma fonte de dados do **Catálogo de Dados do Azure** só exclui os metadados armazenados no serviço do **Catálogo de Dados do Azure**. A fonte de dados original não é afetada de nenhuma maneira.

## Consumir fontes de dados

O objetivo final da descoberta de dados é localizar os dados necessários e usá-los na ferramenta de dados de sua escolha. A experiência de consumo de dados no Catálogo de Dados do Azure permite essa funcionalidade de duas maneiras.

1.	Para aplicativos clientes com suporte direto do **Catálogo de Dados do Azure**, os usuários poderão clicar no menu **Abrir no** no bloco da fonte de dados encontrado no portal. O aplicativo cliente, em seguida, iniciará uma conexão com a fonte de dados selecionada.
2.	Para todos os aplicativos clientes, os usuários podem usar as informações de conexão exibidas no painel Propriedades para uma fonte de dados selecionada. Essas informações incluem todos os detalhes (como nome do servidor, nome do banco de dados e nome do objeto) necessárias para se conectar aos dados e podem ser copiadas na experiência de conexão da ferramenta cliente. Se os detalhes do acesso da solicitação tiverem sido fornecidos por uma fonte de dados, essas informações serão exibidas ao lado dos detalhes da conexão.

<!---HONumber=AcomDC_0406_2016-->