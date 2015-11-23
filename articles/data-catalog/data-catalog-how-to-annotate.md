<properties
   pageTitle="Como anotar fontes de dados"
   description="Artigo de instruções que destaca como anotar ativos de dados no Catálogo de Dados do Azure, incluindo nomes amigáveis, marcas, descrições e especialistas."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/10/2015"
   ms.author="maroche"/>


# Como anotar fontes de dados

## Introdução
O**Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em fontes de dados da empresa. Em outras palavras, o **Catálogo de Dados do Azure** ajuda as pessoas a descobrir, entender e usar fontes de dados, ajudando as empresas a obter mais valor de seus dados existentes. Quando uma fonte de dados tiver sido registrada no **Catálogo de Dados do Azure**, seus metadados serão copiados e indexados pelo serviço, mas a história não para por aí. O **Catálogo de Dados do Azure** permite que os usuários forneçam seus próprios metadados descritivos – como descrições e marcas – para complementar os metadados extraídos da fonte de dados e fazer com que a fonte de dados fique mais compreensível para mais pessoas.

## Anotação e crowdsourcing
Todo mundo tem uma opinião. E isso é bom. O **Catálogo de Dados do Azure** reconhece que diferentes usuários têm diferentes perspectivas em fontes de dados empresariais e cada uma dessas perspectivas pode ser valiosa. Considere este cenário:

* O administrador do sistema conhece o contrato de nível de serviço para os servidores ou serviços que hospedam a fonte de dados.
* O administrador de banco de dados conhece a agenda de backup de cada banco de dados e as janelas de processamento de ETL permitidas.
* O proprietário do sistema conhece o processo para que usuários solicitem acesso à fonte de dados.
* O administrador de dados sabe como os ativos e os atributos na fonte de dados são mapeados para o modelo de dados da empresa.
* O analista sabe como os dados são usados no contexto dos processos de negócios aos quais ele oferece suporte.

Cada uma dessas perspectivas é valiosa, e o **Catálogo de Dados do Azure** usa uma abordagem de crowdsourcing para metadados que permitem que cada um seja capturado e usado para fornecer uma visão completa das fontes de dados registradas. Usando o portal do **Catálogo de Dados do Azure**, cada usuário pode adicionar e editar suas próprias anotações, sendo capaz de exibir anotações fornecidas por outros usuários.

## Diferentes tipos de anotações
Durante a visualização do **Catálogo de Dados do Azure**, os seguintes tipos de anotações têm suporte:

| Anotação | Observações |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome amigável | Os nomes amigáveis podem ser fornecidos no nível do ativo de dado, para facilitar a compreensão dos ativos de dados. Os nomes amigáveis serão mais úteis quando o nome do objeto subjacente for confuso, abreviado ou não significativa para os usuários. |
| Descrição | As descrições podem ser fornecidas nos níveis do atributo e do ativo de dados/coluna. As descrições são anotações de texto curto de forma livre que descrevem a perspectiva do usuário sobre o ativo de dados ou seu uso. |
| Marcas | As marcas podem ser fornecidas nos níveis do atributo e do ativo de dados/coluna As marcas são rótulos definidos pelo usuário que podem ser usados para categorizar os ativos de dados ou os atributos. |
| Especialistas | Os especialistas podem ser fornecidos no nível do ativo de dados. Os especialistas identificam usuários ou grupos com perspectivas de especialista sobre os dados e podem servir como pontos de contato para usuários que descobrirem as fontes de dados registradas e que tenham dúvidas não respondidas pelas anotações existentes. |
| Solicitar acesso | As informações sobre a solicitação de acesso podem ser fornecidas no nível do ativo de dados. Essas informações destinam-se aos usuários que descobrirem uma fonte de dados que ainda não tenham permissões para acessar. Os usuários podem inserir o endereço de email do usuário ou grupo que concede acesso, a URL do processo ou a ferramenta que os usuários precisam acessar, ou podem inserir o próprio processo como texto. |


## Anotando vários ativos
Ao selecionarem vários ativos de dados no portal do **Catálogo de Dados do Azure**, os usuários poderão anotar todos os ativos em uma única operação. As anotações serão aplicadas a todos os recursos selecionados, facilitando a seleção e o fornecimento de uma descrição consistente e conjuntos de marcas e de especialistas para ativos de dados relacionados.

> [AZURE.NOTE]Marcas e especialistas também podem ser fornecidos durante o registro de ativos de dados por meio da ferramenta de registro de fontes de dados do **Catálogo de Dados do Azure**.

Quando várias tabelas e exibições forem selecionadas, somente as colunas que todos os ativos de dados selecionados tiverem em comum serão exibidas no portal do **Catálogo de Dados do Azure**. Isso permite que os usuários forneçam marcas e descrições de todas as colunas com o mesmo nome para todos os ativos selecionados.

## Anotações e descoberta
Assim como os metadados extraídos da fonte de dados durante o registro são adicionados ao índice de pesquisa do **Catálogo de Dados do Azure**, os metadados fornecidos pelo usuário também são indexados. Isso significa que não só as anotações fazem com que seja mais fácil para os usuários compreenderem os dados descobertos, como as anotações também facilitam a descoberta dos ativos de dados anotados por meio da pesquisa com os termos que fazem sentido para eles.

## Resumo
O registro de uma fonte de dados no **Catálogo de Dados do Azure** torna os dados descobríveis ao copiar metadados estruturais e descritivos da fonte de dados para o serviço Catálogo. Depois que uma fonte de dados tiver sido registrada, os usuários poderão fornecer anotações para facilitar a descoberta e a compreensão de dentro do portal do **Catálogo de Dados do Azure**.

<!---HONumber=Nov15_HO3-->