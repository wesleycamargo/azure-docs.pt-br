<properties 
	pageTitle="Visão geral da CDN do Azure" 
	description="Saiba o que é a CDN (Rede de Distribuição de Conteúdo) do Azure e como usá-la para fornecer conteúdo de alta largura de banda armazenando em cache blobs e conteúdo estático." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="mazha"/>

#Visão geral da CDN (Rede de Distribuição de Conteúdo) do Azure

A CDN (Rede de Distribuição de Conteúdo) do Azure armazena em cache conteúdo estático e blobs do Azure usados por serviços de nuvem em locais estrategicamente posicionados para fornecer largura de banda máxima para o fornecimento de conteúdo aos usuários.

Se você for um cliente CDN, poderá gerenciar seus pontos de extremidade de CDN por meio do [Portal de Gerenciamento do Microsoft Azure](https://manage.windowsazure.com).


>[AZURE.NOTE]A CDN do Azure tem um [plano de faturamento](http://www.microsoft.com/windowsazure/pricing/) separado do Armazenamento do Azure ou dos Serviços de Nuvem do Azure.
 

A CDN oferece aos desenvolvedores uma solução global de fornecimento de conteúdo de alta largura de banda armazenando em cache o conteúdo em nós físicos em todo o mundo. Para obter uma lista atual dos locais de nós CDN, consulte [Locais POP da CDN (Rede de Distribuição de Conteúdo) do Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

Os benefícios do uso da CDN para armazenar em cache dados do Azure incluem:

- Melhor desempenho e experiência de usuário para usuários finais que estão longe de uma fonte de conteúdo e estão usando aplicativos em que muitas "viagens à Internet" são necessárias para carregar conteúdo
- Grande escala distribuída para lidar melhor com alta carga instantânea, como no início de um evento de lançamento de produto. 


>[AZURE.IMPORTANT]Quando você cria ou habilita um ponto de extremidade da CDN, pode levar até 60 minutos para que a propagação seja feita no mundo inteiro.
 
Quando uma solicitação de um objeto é feita pela primeira vez à CDN, o objeto é recuperado diretamente do serviço Blob ou do serviço de nuvem. Quando uma solicitação é feita usando a sintaxe da CDN, a solicitação é redirecionada para o ponto de extremidade da CDN mais próximo do local em que a solicitação foi feita para fornecer acesso ao objeto. Se o objeto não for encontrado nesse ponto de extremidade, ele será recuperado do serviço e armazenado em cache no ponto de extremidade, em que uma configuração de TTL (vida útil) será mantida para o objeto armazenado em cache.
 
##Armazenando em cache o conteúdo do armazenamento do Azure

Depois que a CDN for habilitada em uma conta de armazenamento do Azure, todos os blobs em contêineres públicos e disponíveis para acesso anônimo serão armazenados em cache por meio da CDN. Somente os blobs que estão disponíveis ao público podem ser armazenados em cache com a CDN do Azure. Para tornar um blob publicamente disponível para acesso anônimo, é preciso marcar seu contêiner como público. Quando você fizer isso, todos os blobs nesse contêiner estarão disponíveis para acesso de leitura anônimo. Você também tem a opção de tornar os dados do contêiner públicos ou restringir o acesso somente aos blobs dentro dele. Consulte [Restringir o acesso aos contêineres e blobs](http://msdn.microsoft.com/library/azure/dd179354.aspx) para obter informações sobre como gerenciar o controle de acesso para contêineres e blobs.

Para obter o melhor desempenho, use o cache de borda da CDN para fornecer blobs com menos de 10 GB de tamanho.

Quando você habilita o acesso à CDN para uma conta de armazenamento, o Portal de gerenciamento fornece um nome de domínio da CDN no formato a seguir: http://<identifier>.vo.msecnd.net/. Esse nome de domínio pode ser usado para acessar blobs em um contêiner público. Por exemplo, ao fornecer um contêiner público de nome música em uma conta de armazenamento de nome minhaconta, os usuários podem acessar os blobs nesse contêiner usando qualquer uma das seguintes URLs:

- **URL do serviço de Blob do Azure**: `http://myAccount.blob.core.windows.net/music/` 
- **URL da CDN do Azure**: `http://<identifier>.vo.msecnd.net/music/` 

##Armazenando em cache o conteúdo dos sites do Azure

Você pode habilitar a CDN de seus sites para armazenar em cache o conteúdo da Web, como imagens, scripts e folhas de estilo. Consulte [Integrar um site do Azure com a CDN do Azure](../cdn-websites-with-cdn.md).

Quando você habilita o acesso à CDN para um site, o Portal de Gerenciamento fornece um nome de domínio da CDN no seguinte formato: http://<identifier>.vo.msecnd.net/. Esse nome de domínio pode ser usado para recuperar objetos de um site. Por exemplo, com um contêiner público chamado cdn e um arquivo de imagem chamado music.png, os usuários podem acessar o objeto usando uma das duas seguintes URLs:

- **URL do site do Azure**: `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **URL da CDN do Azure**: `http://<identifier>.vo.msecnd.net/cdn/music.png`
 
##Armazenando em cache o conteúdo dos serviços de nuvem do Azure

Você pode armazenar em cache para a CDN os objetos que são fornecidos por um serviço de nuvem do Azure.

O cache para serviços de nuvem tem as seguintes restrições:


- A CDN deve ser usada para armazenar em cache apenas conteúdo estático.

	>[AZURE.WARNING]O armazenamento em cache de conteúdo altamente volátil ou verdadeiramente dinâmico pode prejudicar o desempenho ou causar problemas de conteúdo, com um aumento de custo.
- O serviço de nuvem deve ser implantado em uma implantação de produção.
- O serviço de nuvem deve fornecer o objeto na porta 80 usando HTTP.
- O serviço de nuvem deve colocar o conteúdo a ser armazenado em cache ou entregue na pasta /cdn do serviço de nuvem.

Quando você habilita o acesso à CDN para um serviço de nuvem, o Portal de Gerenciamento fornece um nome de domínio da CDN no seguinte formato: http://<identifier>.vo.msecnd.net/. Esse nome de domínio pode ser usado para recuperar objetos de um serviço de nuvem. Por exemplo, com a um serviço de nuvem chamado myHostedService e uma página da Web do ASP.NET chamada music.aspx que fornece conteúdo, os usuários podem acessar o objeto usando uma das duas seguintes URLs:


- **URL do Serviço de Nuvem do Azure**: `http://myHostedService.cloudapp.net/cdn/music.aspx` 
- **URL da CDN do Azure**: `http://<identifier>.vo.msecnd.net/music.aspx` 


###Armazenando em cache conteúdo específico com cadeias de caracteres de consulta

Você pode usar cadeias de consulta para diferenciar os objetos recuperados de um serviço de nuvem. Por exemplo, se o serviço de nuvem exibir um gráfico que pode variar, você poderá passar uma cadeia de consulta para recuperar o gráfico específico necessário. Por exemplo:

`http://<identifier>.vo.msecnd.net/chart.aspx?item=1`

Cadeias de caracteres de consulta são passadas como literais de cadeia de caracteres. Se você tiver um serviço que usa dois parâmetros, como `?area=2&item=1`, e fizer uma chamada subsequente ao serviço usando `?item=1&area=2`, armazenará em cache duas cópias do mesmo objeto.
 

##Acessando conteúdo armazenado em cache por HTTPS


O Azure permite que você recupere conteúdo da CDN usando chamadas HTTPS. Isso permite incorporar conteúdo armazenado em cache na CDN em páginas da Web seguras sem receber avisos sobre tipos de conteúdo de segurança mista.

O acesso a conteúdo da CDN usando HTTPS tem as seguintes restrições:


- Você deve usar o certificado fornecido pela CDN. Não há suporte a certificados de terceiros.
- Você deve usar o domínio da CDN para acessar o conteúdo. O suporte a HTTPS não está disponível para CNAMEs (nomes de domínio personalizados), pois a CDN não dá suporte a certificados personalizados no momento.



Mesmo quando o HTTPS é habilitado, o conteúdo da CDN pode ser recuperado usando HTTP e HTTPS.

Para saber mais sobre como habilitar o HTTPS para conteúdo da CDN, consulte [Como habilitar a CDN (Rede de Distribuição de Conteúdo) do Azure](http://msdn.microsoft.com/library/azure/gg680301.aspx).


##Acessando conteúdo armazenado em cache com domínios personalizados

Você pode mapear o ponto de extremidade HTTP da CDN para um nome de domínio personalizado e usar esse nome para solicitar objetos da CDN.

Para saber mais sobre como mapear um domínio personalizado, consulte [Como mapear conteúdo da CDN (Rede de Distribuição de Conteúdo) para um domínio personalizado](http://msdn.microsoft.com/library/azure/gg680307.aspx).

 

<!---HONumber=August15_HO6-->