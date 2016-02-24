<properties 
	pageTitle="Como usar a CDN | Microsoft Azure" 
	description="Saiba como usar a CDN (Rede de Distribuição de Conteúdo) do Azure para fornecer conteúdo com alta largura de banda armazenando em cache blobs e conteúdo estático." 
	services="cdn" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>


# Usando a CDN para Azure

A CDN (Rede de Distribuição de Conteúdo) do Azure é o bloco de construção fundamental para dimensionar qualquer aplicativo HTTP no Azure. Ele oferece aos clientes do Azure uma solução global armazenando em cache e entregando conteúdo próximo aos usuários finais. Como resultado, em vez de acessar a origem toda vez, as solicitações de usuário são inteligentemente roteadas para os POPs (pontos de presença) de borda da CDN com melhor desempenho. Isso aumenta significativamente o desempenho e a experiência do usuário. Para obter uma lista atual dos locais de nós CDN, consulte [Locais POP da CDN (Rede de Distribuição de Conteúdo) do Azure](cdn-pop-locations.md).

Os benefícios do uso da CDN para armazenar dados do Azure em cache incluem:

- Melhor desempenho e experiência de usuário para usuários finais que estão longe de uma fonte de conteúdo e estão usando aplicativos onde muitas solicitações HTTP são necessárias para carregar conteúdo.
- Grande escala distribuída para lidar melhor com alta carga instantânea, digamos, no início de um evento, como o lançamento de um produto.
- Ao distribuir solicitações de usuário e entregar conteúdo por meio de POPs de borda globais, menos tráfego é enviado para a origem.

>[AZURE.TIP]A CDN do Azure pode distribuir o conteúdo de uma variedade de origens. As origens integradas ao Azure incluem o Serviço de Aplicativo, os Serviços de Nuvem, o armazenamento de blobs e o Serviço de Mídia. Você também pode definir uma origem personalizada usando qualquer endereço da Web acessível publicamente.

##Como habilitar a CDN

1. Criar um perfil CDN com pontos de extremidade apontando para a origem

	Um perfil CDN é uma coleção de pontos de extremidade CDN. Cada perfil contém um ou mais pontos de extremidade CDN. Depois de criar um perfil CDN, você poderá criar um novo ponto de extremidade CDN usando a origem que escolheu.
	
	>[AZURE.NOTE]Por padrão, uma única assinatura do Azure é limitada a quatro perfis da CDN. Cada perfil da CDN é limitado a 10 pontos de extremidade da CDN.
	>
	> Os preços da CDN são aplicados no nível de perfil CDN. Se você quiser usar uma combinação de recursos CDN Standard e Premium, precisará de vários perfis CDN.
	
	Para obter um tutorial detalhado sobre a criação de perfis e de pontos de extremidade CDN, confira [Como habilitar a Rede de Distribuição de Conteúdo para o Azure](cdn-create-new-endpoint.md).
	
2. Definir sua configuração da CDN

	Você pode habilitar uma série de recursos para o ponto de extremidade da CDN, como [política de armazenamento em cache](cdn-caching-policy.md), [cache de cadeia de caracteres de consulta](cdn-query-string.md), [mecanismo de regras](cdn-rules-engine.md), entre outros. Para obter detalhes, confira o menu **Gerenciar** à esquerda.

3. Acesse o conteúdo CDN

	Para acessar o conteúdo armazenado em cache na CDN, utilize a URL da CDN fornecida no portal. Por exemplo, o endereço de um blob armazenado em cache será semelhante ao seguinte: `http://<identifier>.azureedge.net/<myPublicContainer>/<BlobName>`

## Armazenando em cache o conteúdo do armazenamento do Azure

Depois que a CDN for habilitada em uma conta de armazenamento do Azure, todos os blobs em contêineres públicos e disponíveis para acesso anônimo serão armazenados em cache por meio da CDN. Somente os blobs que estão disponíveis ao público podem ser armazenados em cache com a CDN do Azure. Para tornar um blob publicamente disponível para acesso anônimo, é preciso marcar seu contêiner como público. Quando você fizer isso, todos os blobs nesse contêiner estarão disponíveis para acesso de leitura anônimo. Você também tem a opção de tornar os dados do contêiner públicos ou restringir o acesso somente aos blobs dentro dele. Confira [Gerenciar o acesso aos Recursos do Armazenamento do Azure](../storage/storage-manage-access-to-resources.md) para saber mais sobre o gerenciamento de controle de acesso para contêineres e blobs.

Para obter o melhor desempenho, use o cache de borda da CDN para fornecer blobs com menos de 10 GB de tamanho.

Quando você habilita o acesso à CDN para uma conta de armazenamento, o Portal de Gerenciamento fornece um nome de domínio da CDN neste formato: `http://<identifier>.azureedge.net/` Esse nome de domínio pode ser usado para acessar blobs em um contêiner público. Por exemplo, ao fornecer um contêiner público de nome música em uma conta de armazenamento de nome minhaconta, os usuários podem acessar os blobs nesse contêiner usando qualquer uma das seguintes URLs:

- **URL do serviço de Blob do Azure**: `http://myAccount.blob.core.windows.net/music/` 
- **URL da CDN do Azure**: `http://<identifier>.azureedge.net/music/` 

## Armazenando em cache o conteúdo dos sites do Azure

Você pode habilitar a CDN de seus sites para armazenar em cache o conteúdo da Web, como imagens, scripts e folhas de estilo. Consulte [Integrar um site do Azure com a CDN do Azure](../app-service-web/cdn-websites-with-cdn.md).

Quando você habilita o acesso à CDN para um site, o Portal de Gerenciamento fornece um nome de domínio da CDN neste formato: `http://<identifier>.azureedge.net/` Esse nome de domínio pode ser usado para recuperar objetos de um site. Por exemplo, com um contêiner público chamado cdn e um arquivo de imagem chamado music.png, os usuários podem acessar o objeto usando uma das duas seguintes URLs:

- **URL do site do Azure**: `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **URL da CDN do Azure**: `http://<identifier>.azureedge.net/cdn/music.png`
 
## Armazenando em cache o conteúdo dos serviços de nuvem do Azure

Você pode armazenar em cache para a CDN os objetos que são fornecidos por um serviço de nuvem do Azure.

O cache para serviços de nuvem tem as seguintes restrições:


- A CDN deve ser usada para armazenar em cache apenas conteúdo estático.

	>[AZURE.WARNING]O armazenamento em cache de conteúdo altamente volátil ou verdadeiramente dinâmico pode prejudicar o desempenho ou causar problemas de conteúdo, com um aumento de custo.
- O serviço de nuvem deve ser implantado em uma implantação de produção.
- O serviço de nuvem deve fornecer o objeto na porta 80 usando HTTP.
- O serviço de nuvem deve colocar o conteúdo a ser armazenado em cache ou entregue na pasta /cdn do serviço de nuvem.

Quando você habilita o acesso à CDN para um serviço em nuvem, o Portal de Gerenciamento fornece um nome de domínio da CDN neste formato: `http://<identifier>.azureedge.net/` Esse nome de domínio pode ser usado para recuperar objetos de um serviço de nuvem. Por exemplo, com a um serviço de nuvem chamado myHostedService e uma página da Web do ASP.NET chamada music.aspx que fornece conteúdo, os usuários podem acessar o objeto usando uma das duas seguintes URLs:


- **URL do Serviço de Nuvem do Azure**: `http://myHostedService.cloudapp.net/music.aspx` 
- **URL da CDN do Azure**: `http://<identifier>.azureedge.net/music.aspx` 

## Armazenando em cache conteúdo de origens personalizadas

Você pode armazenar em cache para a CDN os objetos fornecidos por qualquer aplicativo Web acessível publicamente.

O cache para origens personalizadas tem as seguintes restrições:

- A CDN deve ser usada para armazenar em cache apenas conteúdo estático.

	>[AZURE.WARNING]O armazenamento em cache de conteúdo altamente volátil ou verdadeiramente dinâmico pode prejudicar o desempenho ou causar problemas de conteúdo, com um aumento de custo.
- O conteúdo na origem personalizada deve estar hospedado em um servidor com um endereço IP público. Os nós de borda da CDN não conseguem recuperar ativos de servidores de intranet atrás de um firewall.

Quando você habilita o acesso à CDN a uma origem personalizada, o Portal do Azure oferece um nome de domínio da CDN neste formato: `http://<identifier>.azureedge.net/`. Esse nome de domínio pode ser usado para recuperar objetos de uma origem personalizada. Por exemplo, dado um site localizado em www.contoso.com e uma página da Web ASP.NET chamada music.aspx que fornece conteúdo, os usuários poderão acessar o objeto usando uma das duas seguintes URLs:


- **URL de origem personalizada**: `http://www.contoso.com/music.aspx` 
- **URL da CDN do Azure**: `http://<identifier>.azureedge.net/music.aspx` 

## Armazenando em cache conteúdo específico com cadeias de caracteres de consulta

Você pode usar cadeias de consulta para diferenciar os objetos recuperados de uma origem. Por exemplo, se a origem exibir um gráfico que pode variar, você poderá passar uma cadeia de consulta para recuperar o gráfico específico necessário. Por exemplo:

`http://<identifier>.azureedge.net/chart.aspx?item=1`

Cadeias de caracteres de consulta são passadas como literais de cadeia de caracteres. Se você tiver um serviço que use dois parâmetros, como `?area=2&item=1`, e fizer uma chamada subsequente à origem usando `?item=1&area=2`, armazenará em cache duas cópias do mesmo objeto.

Para saber mais sobre o armazenamento em cache de cadeias de caracteres de consulta, confira [Controlando o comportamento do cache de solicitações de CDN com cadeias de caracteres de consulta](cdn-query-string.md).

## Acessando conteúdo armazenado em cache por HTTPS

O Azure permite que você recupere conteúdo da CDN usando chamadas HTTPS. Isso permite incorporar conteúdo armazenado em cache na CDN em páginas da Web seguras sem receber avisos sobre tipos de conteúdo de segurança mista.

O acesso a conteúdo da CDN usando HTTPS tem as seguintes restrições:


- Você deve usar o certificado fornecido pela CDN. Não há suporte a certificados de terceiros.
- Você deve usar o domínio da CDN para acessar o conteúdo. O suporte a HTTPS não está disponível para CNAMEs (nomes de domínio personalizados), pois a CDN não dá suporte a certificados personalizados no momento.

Para saber mais sobre como habilitar o HTTPS para conteúdo da CDN, consulte [Como habilitar a CDN (Rede de Distribuição de Conteúdo) do Azure](cdn-create-new-endpoint.md).


## Acessando conteúdo armazenado em cache com domínios personalizados

Você pode mapear o ponto de extremidade HTTP da CDN para um nome de domínio personalizado e usar esse nome para solicitar objetos da CDN.

Para saber mais sobre como mapear um domínio personalizado, consulte [Como mapear conteúdo da CDN (Rede de Distribuição de Conteúdo) para um domínio personalizado](cdn-map-content-to-custom-domain.md).

## Gerenciamento da CDN de forma programática

A CDN do Microsoft Azure pode ser gerenciada de forma programática usando a [API REST do Provedor de Recursos da CDN](https://msdn.microsoft.com/library/mt634456.aspx).


## Consulte também

- [Como habilitar a Rede de Distribuição de Conteúdo para o Azure](cdn-create-new-endpoint.md)
- [Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure](cdn-overview.md)
- [Limpar um ponto de extremidade CDN do Azure](cdn-purge-endpoint.md)
- [API REST do Provedor de Recursos CDN](https://msdn.microsoft.com/library/mt634456.aspx)

<!---HONumber=AcomDC_0121_2016-->