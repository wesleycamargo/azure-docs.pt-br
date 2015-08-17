<properties
   pageTitle="Perguntas frequentes sobre o Catálogo de Dados do Azure"
   description="Catálogo de Dados do Azure: perguntas frequentes do Catálogo"
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

# Perguntas frequentes sobre o Catálogo de Dados do Azure

Este artigo oferece respostas para as perguntas mais frequentes relacionadas ao serviço de Catálogo de Dados do Microsoft Azure.

## P.: O que é o Catálogo de Dados do Azure?

R.: O Catálogo de Dados do Microsoft Azure é um serviço totalmente gerenciado hospedado na nuvem do Microsoft Azure que serve como um sistema de registro e descoberta para fontes de dados da empresa. O Catálogo de Dados do Azure fornece recursos que permitem que qualquer usuário – desde analistas a cientistas de dados e desenvolvedores – registre, descubra, entenda e consuma fontes de dados.

## P.: quais clientes desafios faz o Catálogo de Dados do Azure resolver?

O Catálogo de Dados do Azure enfrenta o desafio de descoberta de fonte de dados e os "dados escuros", permitindo aos usuários descobrir e entender as fontes de dados da empresa.

## P.: Quem é o público-alvo do Catálogo de Dados do Azure?

O Catálogo de Dados do Azure fornece recursos para usuários técnicos e não técnicos, incluindo:

- Desenvolvedores de dados, profissionais de BI e análises: responsáveis por produzir conteúdo de dados e análises para outras pessoas consumirem
-	Administradores de dados: aqueles que têm conhecimento sobre os dados, o que eles significam, como se destinam a serem usados e para qual finalidade
- Consumidores de dados: aqueles que precisam ser capazes de descobrir, entender e se conectar aos dados necessários com facilidade para fazer seu trabalho usando a ferramenta de sua preferência
- TI central: aqueles que precisam tornar centenas de fontes de dados detectáveis para os usuários de negócios e que precisam supervisionar a maneira como os dados estão sendo usados e por quem

## P.: Qual é a disponibilidade do Catálogo de Dados do Azure nas regiões?

Durante a visualização, os serviços de Catálogo de Dados do Azure estão disponíveis apenas nos seguintes data centers:

- Oeste dos EUA
- Leste dos EUA
- Oeste da Europa
- Leste da Austrália

## P.: Quais são os limites de número de ativos de dados no Catálogo de Dados do Azure?

A Edição Gratuita do Catálogo de Dados do Azure é limitado a 5 mil ativos de dados registrados.

A Edição Standard do Catálogo de Dados do Azure dá suporte a até 100 mil ativos de dados registrados.

## P.: Quais são os tipos de ativos e fonte de dados com suporte?

Durante a visualização, o Catálogo de Dados do Azure dá suporte atualmente a banco de dados do SQL Server relacional (incluindo o Banco de Dados SQL do Azure) e do SQL Server Analysis Services (multidimensionais e tabulares), bem como do SQL Server Reporting Services (somente em modo nativo) e do Oracle Database.

Durante a visualização, o Catálogo de Dados do Azure dá suporte aos seguintes tipos de ativos:

- Tabela do SQL Server
- Exibição do SQL Server
- Dimensão do SQL Server Analysis Services
- Medida do SQL Server Analysis Services
- KPI do SQL Server Analysis Services 
- Tabela do SQL Server Analysis Services
- Relatório do SQL Server Reporting Services  
- Tabela do Oracle Database
- Exibição do Oracle Database

## P.: Como posso solicitar suporte para outra fonte de dados?

Solicitações de recursos e outros comentários podem ser enviados no [Fórum do Catálogo de Dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).
 
## P.: Como faço para começar a utilizar o Catálogo de Dados do Azure?

O melhor lugar para começar é seguindo as instruções no guia de Introdução ao Catálogo de Dados. Este artigo é um tour de ponta a ponta dos recursos da visualização pública.

## P.: Como posso registrar meus dados?

Para registrar os dados no Catálogo de Dados do Azure, abra a ferramenta de registro do Catálogo de Dados do Azure da área “Publicar” do portal do Catálogo de Dados do Azure. No aplicativo de publicação do Catálogo de Dados do Azure, faça logon usando as mesmas credenciais que você usa para acessar o portal do Catálogo de Dados do Azure e, em seguida, selecione a fonte de dados e os ativos específicos que você deseja registrar.

## P.: Quais propriedades são extraídas para ativos de dados que são registrados?

As propriedades específicas serão diferentes conforme a fonte de dados, porém, em geral, o serviço de publicação do Catálogo de Dados do Azure extrairá as seguintes informações:

- Nome do ativo
- Tipo de Ativo
- Descrição do ativo
- Nomes do atributo/coluna 
- Tipos de dados do atributo/coluna
- Descrição do atributo/coluna

> [AZURE.IMPORTANT]O Catálogo de Dados do Azure não move ou copia os dados para o cloudAzure. Registrar os ativos de uma fonte de dados copiará os metadados dos ativos para o Azure, mas os dados permanecerão no local na fonte de dados de origem. A única exceção a essa regra é se um usuário optar por carregar registros de visualização ao registrar recursos. Nesse caso, até 20 registros serão copiados de cada ativo, sendo armazenados como um instantâneo no **Catálogo de Dados do Azure**.

<br/>

> [AZURE.NOTE]Para fontes de dados como o SQL Server Analysis Services que têm uma propriedade **Descrição** de primeira classe, o aplicativo de publicação do **Catálogo de Dados do Azure** extrairá o valor desta propriedade. Para bancos de dados relacionais do SQL Server, que não têm uma propriedade **Descrição** de primeira classe, o aplicativo de publicação do **Catálogo de Dados do Azure** extrairá o valor da propriedade estendida ms\_description para objetos e colunas. Para obter mais informações, consulte o TechNet [Usando propriedades estendidas em objetos de banco de dados](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).

## P.: Quanto tempo levará para recursos recém-registrados aparecem no Catálogo de Dados do Azure?

Depois de registrar os ativos com o **Catálogo de Dados do Azure**, pode haver um período de espera de 5 a 10 segundos para eles aparecerem no portal do **Catálogo de Dados do Azure**.

## P.: Como posso anotar e enriquecer os metadados para meus ativos de dados registrados?

A maneira mais simples de fornecer metadados para ativos registrados é selecionar o ativo no portal do **Catálogo de Dados do Azure** e inserir os valores de metadados no painel de propriedades ou no painel de esquema do objeto selecionado.

Você também pode fornecer alguns metadados, como marcas e especialistas, durante o processo de registro. Os valores fornecidos no serviço de publicação do **Catálogo de Dados do Azure** serão aplicadas a todos os ativos que estiverem sendo registrados no momento. Para exibir os objetos recém-registrados no portal para anotação adicional, selecione o botão **Exibir Portal** na tela final do aplicativo de publicação do **Catálogo de Dados do Azure**.

## P.: Como posso excluir meus objetos de dados registrados?

Você pode excluir um objeto do **Catálogo de Dados do Azure** selecionando o objeto no portal e, em seguida, clicando o botão **Excluir**. Isso removerá os metadados para o objeto do **Catálogo de Dados do Azure**, mas não afetará a fonte de dados subjacente real.

## P.: O que é um especialista?

Especialista é uma pessoa que tem uma perspectiva bem informada sobre um objeto de dados. Um objeto pode ter muitos especialistas. Um especialista não precisa ser o "proprietário" de um objeto, ele simplesmente é alguém que sabe como os dados podem e devem ser usados.

## P.: Qual é o SLA para visualização?

Durante a visualização do **Catálogo de Dados do Azure**, não há nenhum contrato de nível de serviço explícito.

## P.: Como posso compartilhar informações com a equipe do Catálogo de Dados do Azure se eu encontrar problemas?

Use o fórum do **Catálogo de Dados do Azure** para relatar problemas, compartilhar informações e fazer perguntas. Ele encontra-se em http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

##P.: O Catálogo de Dados do Azure funciona com essa outra fonte de dados na qual estou interessado? 
Estamos trabalhando ativamente para adicionar mais fontes de dados ao **Catálogo de Dados do Azure**. Se houver uma fonte de dados que você gostaria que tivesse suporte, sugira-a (ou apoie a sugestão, caso já exista) no [Fórum do Catálogo de Dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## P.: Como o Catálogo de Dados do Azure se relaciona com o Catálogo de Dados no Power BI para Office 365?

Pense no **Catálogo de Dados do Azure** como uma evolução do Catálogo de Dados. O **Catálogo de Dados do Azure** oferece recursos semelhantes para a descoberta e a publicação de fonte de dados, mas é voltado para cenários mais amplos e não depende do Office 365. Logo após o Catálogo de Dados do Azure entrar em disponibilidade pública, ambos os catálogos serão mesclados em um único serviço.

## P.: De quais permissões um usuário precisa para registrar ativos com o Catálogo de Dados do Azure?

O usuário que executa a ferramenta de registro do **Catálogo de Dados do Azure** precisa ter permissões na fonte de dados para permitir a leitura dos metadados da fonte. Se o usuário também escolher incluir uma visualização, ele também precisará de permissão para a leitura dos dados dos objetos que estão sendo registrados.

## P.: O Catálogo de Dados do Azure também ficará disponível para implantação local?

O **Catálogo de Dados do Azure** é um serviço de nuvem que pode trabalhar com fontes de dados locais e na nuvem, fornecendo uma solução de descoberta de fonte de dados híbrida. Atualmente não há planos para uma versão do serviço do **Catálogo de Dados do Azure** executado localmente.

##P.: É possível extrair mais metadados/metadados mais detalhados das fontes de dados que registramos?

Estamos trabalhando ativamente para expandir as capacidades do **Catálogo de Dados do Azure**. Se houver metadados adicionais que você gostaria de extrair da fonte de dados durante o registro, sugira-a (ou vote nela caso já tenha sido sugerida) no [Fórum do Catálogo de Dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). No futuro, permitiremos que terceiros adicionem novos tipos de fonte de dados por meio de uma API de extensibilidade.

## P.: Como posso restringir a visibilidade dos ativos de dados registrado para que apenas determinadas pessoas possam descobri-los?

R.: Selecione os ativos de dados no Catálogo de Dados do Azure e clique no botão "Apropriar-se". Os proprietários dos ativos de dados no Catálogo de Dados do Azure podem alterar as configurações de visibilidade para permitir que todos os usuários do Catálogo possam descobrir os ativos de sua propriedade ou para restringir a visibilidade a usuários específicos.

## P.: Como faço para atualizar o registro de um ativo de dados para que as alterações na fonte de dados sejam refletidas no Catálogo?

R.: Para atualizar os metadados de ativos de dados que já estão registrados no Catálogo, basta registrar novamente a fonte de dados que contém os ativos. Todas as alterações na fonte de dados, como colunas que são adicionadas ou removidas de tabelas ou exibições, serão atualizadas no Catálogo, e todas as anotações fornecidas pelos usuários serão mantidas.

## P:. Como posso fazer perguntas ou obter ajuda ao trabalhar com o Catálogo de Dados do Azure?

Se você tiver problemas ou precisar de ajuda com a visualização do Catálogo de Dados do Azure, participe do [Fórum do Catálogo de Dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## P.: Minha dúvida não foi respondida aqui. O que devo fazer?

Vá diretamente para o [Fórum do Catálogo de Dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). As perguntas feitas serão posteriormente incluídas aqui.

<!---HONumber=August15_HO6-->