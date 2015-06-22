<properties 
	pageTitle="Estudo de caso de desenvolvedor de Pesquisa do Azure: Como a WhatToPedia criou um portal de mídia informativa no Microsoft Azure" 
	description="Aprenda a criar um portal de informações e mecanismo de metabusca usando o serviço de Pesquisa no Microsoft Azure" 
	services="search, sql-database,  storage, web-sites" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe"/>

<tags 
	ms.service="search" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="search" 
	ms.date="04/13/2015" 
	ms.author="heidist"/>

<h1>Estudo de caso do desenvolvedor de Pesquisa do Azure</h1>

<font size="5">Como [WhatToPedia.com](http://whattopedia.com/) criou um portal de mídia informativa no Microsoft Azure</font>

 ![][6] &nbsp;&nbsp;&nbsp; <font size="9">A grande ideia</font>


Nossa ideia é criar um portal de informações que ajudem os compradores a conectar-se com os varejistas por meio de uma experiência altamente relevante de pesquisa com escopo, semelhante a como os portais de viagem fazem a correspondência entre os turistas e os hotéis, restaurantes e opções de entretenimento quando estão em um local desconhecido.

O portal que montamos fornecerá uma experiência de pesquisa de qualidade excepcionalmente alta sobre os dados em um determinado mercado varejista, ajudando os compradores a localizar lojas com base no local e outras comodidades que o varejista fornece. Propagaremos o mecanismo de pesquisa com um conjunto de dados inicial, mas um maior valor será criado ao longo do tempo com a ajuda de assinantes varejistas que divulguem informações sobre seus negócios. Promoções, novos produtos, marcas populares, serviços que são especialidade da casa - são todos exemplos dos dados que agregam valor ao nosso site. Esses dados são automaticamente relatados e integrados ao corpo da pesquisa, depois que o varejista se inscreve como um assinante. Publicidade, juntamente com o modelo de assinatura, fornecem o fluxo de receita para nosso novo negócio.

A pesquisa será o modelo de interação de usuário predominante em uma plataforma puramente de nuvem. Para fins de escala e de baixo custo, quase tudo o que fazemos, da experiência do portal ao controle do código-fonte, será por meio de um serviço online. Usando um mecanismo de pesquisa que fornece os recursos que precisamos de imediato, podemos compilar um aplicativo de pesquisa rapidamente, sem a necessidade de criarmos e gerenciarmos um mecanismo de pesquisa por conta própria.

## O que criamos

WhatToPedia é uma empresa de mídia de informação em fase inicial. Criamos a [WhatToPedia.com](http://whattopedia.com/) - atualmente no mercado de teste no Norte da Europa, com uma data de ativação de 2 de fevereiro de 2015. Nossa base de clientes é composta primariamente dos departamentos de tijolos e cimento, que precisam de uma presença online acessível e fácil de gerenciar e manter.

Nossa tarefa será atrair os compradores com de uma experiência de pesquisa online excelente, dinamizando os resultados com base na cidade ou vizinhança, marcas, nomes ou tipos de loja. Atrair os compradores tem um efeito dominó, motivando os varejistas a se inscreverem em nosso site de portal. As assinaturas são pagas, com uma taxa acessível.

 ![][7]

Após a inscrição para uma assinatura, um varejista assume seu perfil existente (criado inicialmente por nós de dados adquiridos), atualizando-o com dados adicionais sobre promoções, marcas em destaque ou anúncios. Recursos internos, como os idiomas falados, moedas aceitas e compras sem cobrança de imposto podem ser automaticamente relatados para melhor atrair os compradores que estão procurando por essas características.

## Quem somos

Meu nome é Thomas Segato (Consultoria Microsoft) e trabalhei com Jesper Boelling, desenvolvedor líder da WhatToPedia, para projetar a solução.

WhatToPedia é um projeto inicial, realizando testes de marketing dos negócios de seu portal na Suécia, onde mais de 60.000 varejistas são PMEs (empresas de pequeno e médio porte) do ramo de tijolos e cimento. Como sabemos que uma pessoa fazendo compras na Europa fala vários idiomas e utiliza várias moedas, podemos criar soluções que atendem um comprador poliglota. Precisávamos de um mecanismo de pesquisa que desse suporte a nossos requisitos multilíngues na Pesquisa do Azure, e encontramos.

A Pesquisa do Azure foi um divisor de águas em nosso projeto. Antes da disponibilidade de Pesquisa do Azure, gastávamos energia considerável trabalhando nos problemas inerentes à criação de nosso próprio mecanismo de pesquisa. Ter a Pesquisa do Azure como um serviço online eliminou o maior obstáculo técnico e administrativo da nossa solução, o que significou para nós chegar ao mercado mais rapidamente e com uma experiência de pesquisa mais robusta.

## Como fizemos isso

Nossa visão era criar uma infraestrutura completa com base somente em serviços de nuvem. A Microsoft foi escolhida como a plataforma estratégica porque foi o provedor que ofereceu os serviços necessários (tanto para colaboração quanto para desenvolvimento), dimensionamento sob demanda e preço acessível.
 
### Componentes de alto nível

Criamos uma empresa, não apenas um site. O suporte para o projeto todo exigiu uma gama completa de ferramentas e aplicativos. Adotamos o Visual Studio e o Visual Studio Online para desenvolvimento, o Team Foundation Service (TFS) Online para controle do código-fonte e gerenciamento de scrum, o Office 365 para comunicação e colaboração e é claro, o Microsoft Azure para todas as operações relacionadas ao site e a armazenamento. Com o Visual Studio, o IDE forneceu provisionamento direto ao Azure, com integração ao TFS Online que ofereceu um aumento de produtividade adicional.

O diagrama a seguir ilustra os componentes de alto nível usados na infraestrutura da WhatToPedia.

   ![][8]

### Como usamos o Microsoft Azure

Observando as caixas verdes no diagrama anterior, você verá que a solução WhatToPedia baseia-se nestes serviços:

- [Pesquisa do Azure](http://azure.microsoft.com/services/search/)
- [Sites do Azure usando MVC 4](http://azure.microsoft.com/services/websites/)
- [Trabalhos Web do Azure para tarefas agendadas](../websites-webjobs-resources.md)
- [Banco de Dados SQL do Azure](http://azure.microsoft.com/services/sql-database/)
- [Armazenamento de BLOB do Azure](http://azure.microsoft.com/services/storage/)
- [Entrega de email SendGrid](http://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

O núcleo da solução é composto de dados e pesquisa. O fluxo de dados do provedor do revendedor para o cliente final é ilustrado abaixo:

  ![][9]

O armazenamento de dados primário é utilizado pelos dados do revendedor e de contabilidade no Banco de Dados SQL do Azure. Isso consiste do conjunto de dados inicial, somado a dados específicos do varejista adicionados ao longo do tempo. Estamos usando um trabalho Web do Azure para enviar atualizações de Banco de Dados SQL para o corpo de pesquisa em Pesquisa do Azure.

### Camada de apresentação

O portal é um site do Azure, implementado em MVC 4 e [Boostrap do Twitter](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Escolhemos o MVC porque ele oferece uma abordagem muito mais limpa para HTML do que o desenvolvimento baseado em formulários do ASP.NET. Para evitar a necessidade de criar aplicativos para vários dispositivos e manter várias plataformas móveis, o Boostrap do Twitter foi escolhido para dar suporte a todos os dispositivos e plataformas.

### Autenticação, permissões e dados confidenciais

Os compradores navegam pelo site anonimamente. Como tal, não há nenhum requisito de logon para compradores nem armazenamento de quaisquer dados do consumidor.

Com os varejistas, a história é outra. Aqui, armazenamos as informações de perfil público, informações de cobrança e conteúdo de mídia que eles desejem expor no site. Cada varejista que assina o site recebe um logon de usuário, usado para autenticar o usuário antes de fazer atualizações no perfil do assinante. Podemos armazenar com segurança todos os dados do assinante no armazenamento de BLOB do Azure e no Banco de Dados SQL do Azure. Optamos por um modelo de autenticação que se fundamenta em autenticação baseada em formulários do .NET. Escolhemos essa abordagem por sua simplicidade; não precisávamos das funções, do suporte de interface do usuário e outros recursos externos que acompanham outras abordagens.

Para garantir que os varejistas vejam apenas os dados que pertencem a eles, criamos uma ID de varejista para cada um deles, que é usada posteriormente em todas as operações leitura e gravação envolvendo dados específicos do varejista. Com essa abordagem, descobrimos que nós não precisávamos conceder permissões de banco de dados para varejistas individuais. Todos os revendedores interagem com o sistema em uma função de banco de dados único, com a ID do fornecedor como nossa técnica de isolamento de dados.

Como nosso negócio trata-se totalmente de efeitos cascata (gerando mais negócios para os varejistas, criando incentivo para que varejistas anunciem e inscrevam-se), podemos abdicar de trabalhar com compras pela Web. Por isso, você não encontrará um carrinho de compras em nosso site, o que simplifica a nossos requisitos de segurança.

Outra simplificação que empregamos foi terceirizar nossas operações de cobrança e de contas a pagar. Roteando informações de pagamento de clientes diretamente para um terceiro ([SveaWebPay](http://www.sveawebpay.se/)), podemos reduzir os riscos associados a armazenar e proteger dados confidenciais em nossos armazenamentos de dados.

### Mecanismo de Pesquisa

O núcleo de nossa solução é o mecanismo de pesquisa baseado no serviço de Pesquisa do Azure. Inicialmente criamos um mecanismo de pesquisa personalizado, mas durante esse processo percebemos que a complexidade e esforço eram de fato muito altos; isso nos levou a considerar outras alternativas.

Os recursos básicos mais importantes para nós incluíam:

- Filtros
- Navegação facetada
- Dinamizar os resultados
- Paginar pelo AJAX

Uma pesquisa na Internet nos trouxe o vídeo a seguir, que nos inspirou a experimentar a Pesquisa do Azure: [Aprofundamento na TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410)

Depois de assistir ao vídeo, estávamos prontos para criar um protótipo com base no que vimos. Como já tínhamos um modelo de dados no MVC, criar o protótipo foi simples porque os dados continham termos pesquisáveis, e já tínhamos decidido os critérios de como queríamos classificar, facetar e filtrar os dados.

É desse modo que criamos o protótipo.

**Configurar o Serviço de Pesquisa do Azure**

1. Faça logon no Portal do Azure e adicione o serviço de Pesquisa à nossa assinatura. Nós usamos a versão compartilhada (gratuita com nossa assinatura).
2. Crie um índice. Para o protótipo, usamos o interface do usuário do portal para definir os campos de pesquisa e criar os perfis de pontuação. Nosso perfil de pontuação é baseado em dados de local: país | cidade |endereço (consulte: Adicionar perfis de pontuação).
3. Copie a URL de serviço e a chave de Api de administrador para nossos arquivos de configuração. Essa chave está na página de serviço de Pesquisa no portal e é usada para autenticar o serviço.
	
**Desenvolver um trabalho do indexador de pesquisa – Console do Windows**

1. Faça a leitura de todos os revendedores no banco de dados.
2. Chame a API do Serviço de Pesquisa do Azure para carregar revendedores individualmente (consulte: http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Defina uma propriedade no banco de dados de que o revendedor está indexado para indexação incremental. Fizemos isso adicionando um campo “indexador”, que armazena o status do índice de cada perfil (indexado ou não). 

Consulte o apêndice do trecho de código que cria o trabalho do indexador.

**Desenvolver um Portal de Pesquisa da Web – MVC**

1. Chame o serviço de Pesquisa do Azure para obter todos os documentos de pesquisa (consulte: http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Extraia os itens a seguir por meio da resposta do serviço de pesquisa (usando o http://james.newtonking.com/json do json.net)
   - Resultados
   - Facetas
   - Contagens de resultados
   - Desenvolva uma interface do usuário para exibir resultados da pesquisa, facetas e contagens (já tínhamos isso).

Este é o código que usamos para obter os resultados da Pesquisa do Azure:

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Dinamização por local**

Provavelmente o requisito mais importante para verificar no protótipo incluía adicionar uma palavra-chave de pesquisa com local à consulta. É fundamental para nosso portal que, se um usuário inserir um nome de cidade na pesquisa para consulta, os revendedores nessa determinada cidade obtenham uma classificação maior do que revendedores com a palavra-chave da cidade em sua descrição. Para esse requisito, utilizamos um perfil de pontuação para classificar o campo de cidade com maior valor do que outros campos.

**Suporte a vários idiomas**

Precisávamos exibir resultados da pesquisa corretos nos idiomas corretos, e fornecer uma opção para localizar os mesmos resultados em diferentes idiomas. Os dois lados encontrados nesse problema foram:

- Pesquisar palavras em vários idiomas
- Exibir resultados da pesquisa no idioma correto

Solucionamos a parte da apresentação adicionando um documento para cada idioma com texto localizado e uma propriedade com o idioma. Quando um usuário insere um termo de pesquisa, usamos expressões `$filter` para filtrar segundo o idioma escolhido pelo usuário.

Cada um dos documentos tem uma propriedade oculta chamada "cidades" criada no tipo de coleção. Essa propriedade armazena nomes de cidades em todos os idiomas, permitindo que o usuário pesquise em vários idiomas.

###Armazenamento de dados

Todos os dados (perfil, assinatura e contabilidade) são armazenados no Banco de Dados SQL. Todos os arquivos de mídia são armazenados no armazenamento de BLOB do Azure, incluindo imagens e vídeos fornecidos pelo varejista. Usar armazenamento de BLOB separado isola os efeitos do carregamento de arquivos; arquivos nunca são mesclados ao site, portanto, não precisamos reconstruir o site toda vez que adicionamos arquivos.

Um benefício importante de nosso modelo de armazenamento é que múltiplos desenvolvedores podem compartilhar um único armazenamento para desenvolvimento. Um dos requisitos para o projeto WhatToPedia era ser capaz de criar um ambiente de desenvolvimento em 15 minutos, incluindo vídeos, imagens e dados de revendedor. Ao obter os dados mais recentes do TFS Online, executar um script SQL e executar do trabalho de importação, um ambiente completo pode ser preparado muito rapidamente. Essa prática também aprimora o processo de preparo.

###Trabalhos Web

Usamos os Trabalhos Web do Azure para atualizar os dados no índice. Pela criação de um trabalho do indexador de pesquisa, a parte de indexação foi muito fácil de integrar em nossa solução. A única alteração de código que fizemos para acomodar o trabalho do indexador foi adicionar um campo `Indexed` ao nosso modelo de dados, para indicar o estado do índice. Sempre que um novo perfil é adicionado ou atualizado, o campo `Indexed` é definido como false. O mesmo se aplica se o varejista altera seus dados de perfil por meio do portal.

O trabalho procura por todas as linhas com `Indexed` definido como false. Quando ele encontra a linha, o documento é enviado para Pesquisa do Azure e o campo `Indexed` é definido como true. Não foi necessário planejarmos procedimentos diferentes para adição e atualização de dados, porque o serviço de Pesquisa do Azure se encarrega disso. Se você adicionar um documento que já está presente, o serviço fará uma atualização automaticamente.

Todos os trabalhos da Web foram desenvolvidos como aplicativos de console que podem ser carregados para sites do Azure como arquivos ZIP, descompactados e então agendados.

O trabalho é programado para ser executado a cada 5 minutos como uma tarefa agendada da web. Calculamos que o serviço leva aproximadamente três minutos para carregar 3.000 documentos, o que estava dentro de nossos requisitos.

> [AZURE.NOTE]Há um recurso de indexador de protótipo que foi introduzido recentemente na Pesquisa do Azure. Esse recurso veio cedo demais para que nós pudéssemos utilizá-lo em nosso primeiro lançamento, mas parece resolver o mesmo problema para o qual utilizamos nosso trabalho de indexador, que é automatizar operações de carregamento de dados.


###Estratégia de backup

Criamos uma estratégia de backup em várias camadas para recuperação de uma variedade de cenários, desde falha catastrófica até recuperação de uma transação individual. Os ativos para proteger incluem três tipos de dados (site da Web, dados de assinante e arquivos de mídia).

Primeiro, mantendo o código-fonte do site da Web no TFS Online, sabemos que, se o site ficar inativo, podemos recriá-lo republicando por meio do TFS.

Os dados do assinante no Banco de Dados SQL do Azure são o ativo mais confidencial. Fazemos backup deles usando o recurso interno (consulte [Backup e restauração de Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)). A agenda de backup inclui um backup completo de banco de dados uma vez por semana, backups diferenciais de banco de dados uma vez por dia e backups de log de transações a cada 5 minutos. Dado o tamanho dos dados, essa solução é mais do que adequada para nosso volumes de dados projetado e imediato.

Em terceiro lugar, armazenamos arquivos de imagem e vídeo no armazenamento de BLOB do Azure. Estamos ainda avaliando o plano definitivo de backup para esses dados, considerando Cloudberry Explorer para Azure como uma solução em potencial. Por enquanto, usamos um trabalho da Web para copiar imagens e vídeos para outro local.

##O que aprendemos

Porque já tínhamos dados, foi fácil estabelecer a prova de conceito. Em horas, tivemos um protótipo com facetas e contadores, paginação, perfis classificados e resultados da pesquisa. Os resultados da pesquisa foram tão precisos, que decidimos remover alguns filtros apresentados ao cliente final.

A maior surpresa para nós foi a rapidez com que pudemos aprender a usar a pesquisa do Azure, e quanto obtivemos de volta. Literalmente, estabelecemos prova de conceito em algumas horas (consulte a observação abaixo), substituindo 500 linhas de código por 3 linhas de código no aplicativo front-end (mais um novo trabalho da Web) e obtendo resultados melhores.

Anteriormente, nosso código implementava paginação, contagens e outros comportamentos que são padrão para pesquisa. Usando a Pesquisa do Azure, os resultados que obtemos incluem as ocorrências de pesquisa, facetas, dados de paginação e contagens – tudo de que precisávamos e tínhamos que fornecer por conta própria. Ela também inclui dinamização e navegação facetada integrada, que não tínhamos em nossa solução original.

O maior desafio durante a implementação foi que essa era uma versão de visualização prévia, e localizar informações e experiências compartilhadas foi difícil. Quando conectamos alguns pontos, descobrimos que usar o serviço de Pesquisa do Azure era muito simples devido a seu formato de dados API REST e JSON. Chamamos a estrutura diretamente na maioria dos plug-ins de software livre aberto, como JQuery JSON.Net, e poderíamos usar ferramentas como o Fiddler para experimentação rápida e depuração.

> [AZURE.NOTE]Além de ter os dados preparados, foi útil o fato de que aqueles de nós criando o protótipo já sabíamos como a tecnologia de pesquisa funciona, o que nos tornou mais produtivos e mais apreciativos dos recursos integrados. Se você precisar aumentar sua construção de consulta de pesquisa, navegação facetada, filtros, etc. você deve pressupor que a prototipagem levará mais tempo.

###Controle de facetas na página de apresentação de pesquisa

Uma das lições aprendidas por nós durante a verificação de conceito foi planejar cuidadosamente as facetas, com antecedência. Depois de carregar muitos dados na solução, vimos que o volume total de facetas era muito alto para apresentar aos usuários.

Solucionamos essa questão restringindo o parâmetro de contagem de facetas. O parâmetro de contagem impõe um limite rígido quanto ao número de facetas retornadas ao usuário. Um link que inclui uma discussão sobre o parâmetro de contagem pode ser encontrado [aqui](search-faceted-navigation.md).

###Trabalhos da Web para o agendamento de tarefas

A Pesquisa do Azure não foi a única surpresa agradável para nós. Descobrimos que usar os Trabalhos da Web para automatizar as nossas operações de carregamento de dados era muito superior à nossa abordagem anterior, que exigia o uso de uma VM dedicada com o Agendador do Windows, com tarefas agendadas para atualizar o índice de pesquisa. Os Trabalhos Web foram mais simples de configurar e depurar, além de obviamente muito mais baratos do que pagar por uma VM dedicada.

###Gerenciador de armazenamento de BLOB do Azure para atualizar as imagens

Descobrimos que usar o [Gerenciador de armazenamento de BLOB do Azure](https://azurestorageexplorer.codeplex.com/) (disponível no codeplex) é muito útil no gerenciamento de atualizações de imagens e vídeo para o site. Nós o utilizamos como uma ferramenta de desenvolvimento para atualizar manualmente as imagens e vídeos que fazem parte do nosso site principal. Descobrimos que ele é mais flexível do que a implantação de alterações para o portal e elimina uma iteração de teste completa quando precisamos atualizar uma imagem.

##Algumas palavras finais

Obrigado aos grandes amigos da WhatToPedia por permitir que compartilhemos sua história!

Esperamos que este estudo de caso lhe seja útil. Se você passar a usar a Pesquisa do Azure, eu recomendo alguns recursos para acelerar seu trabalho:

- [Fórum do MSDN dedicado à Pesquisa do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow também tem uma marca](http://stackoverflow.com/questions/tagged/azure-search)
- [Página de documentação no Azure.com](http://azure.microsoft.com/documentation/services/search/)
- [Documentação da Pesquisa do Azure no MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##Apêndice: Trabalho Web do indexador de pesquisa

O código a seguir cria o indexador mencionado na seção sobre a criação do protótipo.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

<!--HONumber=54--> 