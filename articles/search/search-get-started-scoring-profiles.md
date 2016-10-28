<properties 
	pageTitle="Como usar perfis de pontuação na Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado" 
	description="Ajuste a classificação da pesquisa por meio de perfis de pontuação na Pesquisa do Azure, um serviço de pesquisa de nuvem hospedado do Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="08/04/2016" 
	ms.author="heidist"/>

# Como usar os perfis de pontuação na Pesquisa do Azure

Perfis de pontuação são um recurso da Pesquisa do Microsoft Azure que personalizam o cálculo de pontuações de pesquisa, influenciando como os itens são classificados na lista de resultados da pesquisa. Você pode pensar em perfis de pontuação como uma forma de modelar relevância, amplificando itens que atendem a critérios predefinidos. Por exemplo, suponha que seu aplicativo seja um site de reservas de hotel online. Amplificando o campo `location`, as pesquisas que incluem um termo como Seattle resultarão em pontuações mais altas para itens que têm Seattle no campo `location`. Observe que você pode ter mais de um perfil de pontuação, ou nenhum, se a pontuação padrão for suficiente para seu aplicativo.

Para ajudá-lo a experimentar perfis de pontuação, você pode baixar um aplicativo de exemplo que use perfis de pontuação para alterar a ordem de classificação dos resultados da pesquisa. O exemplo é um aplicativo de console – talvez não seja muito realista para desenvolvimento de aplicativos do mundo real – mas é útil como uma ferramenta de aprendizado.

O aplicativo de exemplo demonstra comportamentos de pontuação usando dados fictícios, chamados de `musicstoreindex`. A simplicidade do aplicativo de exemplo torna fácil modificar perfis de pontuação e consultas, e então ver os efeitos de imediatos na ordem de classificação quando o programa for executado.

<a id="sub-1"></a>
## Pré-requisitos

O aplicativo de exemplo é escrito em C# usando o Visual Studio 2013. Experimente gratuitamente o [Visual Studio 2013 Express edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) se você ainda não tiver uma cópia do Visual Studio.

Você precisará de uma assinatura do Azure e um serviço Azure Search para concluir o tutorial. Consulte [Criar um serviço de Pesquisa no portal](search-create-service-portal.md) para obter ajuda com o serviço de configuração.

[AZURE.INCLUDE [Você precisa de uma conta do Azure para concluir este tutorial:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Baixar o aplicativo de exemplo

Acesse a [Demonstração de perfis de pontuação da Pesquisa do Azure](https://azuresearchscoringprofiles.codeplex.com/) em codeplex para baixar o aplicativo de exemplo descrito neste tutorial.

Na guia Código-fonte, clique em **Baixar** para obter um arquivo zip da solução.

 ![][12]

<a id="sub-3"></a>
## Editar app.config

1. Após extrair os arquivos, abra a solução no Visual Studio para editar o arquivo de configuração.
1. No Gerenciador de Soluções, clique duas vezes em **app.config**. Esse arquivo especifica o ponto de extremidade de serviço e uma `api-key` usada para autenticar a solicitação. Você pode obter esses valores do Portal clássico.
1. Entre no [Portal do Azure](https://portal.azure.com).
1. Vá para o painel do serviço da Pesquisa do Azure.
1. Clique no bloco **Propriedades** para copiar a URL do serviço
1. Clique no bloco **Chaves** para copiar a `api-key`.

Quando tiver terminado de adicionar o URL e a `api-key` para app.config, as configurações de aplicativo devem ter esta aparência:

   ![][11]


<a id="sub-4"></a>
## Explorar o aplicativo

Você está quase pronto para compilar e executar o aplicativo, mas antes examine os arquivos JSON usados para criar e preencher o índice.

**Schema.json** define o índice, incluindo os perfis de pontuação que são enfatizados nesta demonstração. Observe que o esquema define todos os campos usados no índice, incluindo campos não pesquisáveis, como `margin`, que podem ser usados em um perfil de pontuação. A sintaxe do perfil de pontuação é documentada em [Adicionar um perfil de pontuação para um índice da Pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx).

**Data1-3.json** fornece os dados, 246 álbuns incluindo diferentes gêneros. Os dados são uma combinação do álbum real e das informações sobre o artista, com campos fictícios como `price` e `margin` usados para ilustrar as operações de pesquisa. Os arquivos de dados estão em conformidade com o índice e são carregados para o serviço da Pesquisa do Azure. Depois de os dados serem carregados e indexados, você pode emitir consultas em relação a eles.

**Program.cs** realiza as seguintes operações:

- Abre uma janela do console.

- Conecta-se a Pesquisa do Azure usando a URL do serviço e `api-key`.

- Exclui o `musicstoreindex` se ele existir.

- Cria um novo `musicstoreindex` usando o arquivo schema.json.

- Preenche o índice usando os arquivos de dados.

- Consulta o índice usando quatro consultas. Observe que os perfis de pontuação são especificados como um parâmetro de consulta. Todas as consultas pesquisam o mesmo termo, 'melhor'. A primeira consulta demonstra o padrão de pontuação. As demais três consultas usam um perfil de pontuação.

<a id="sub-5"></a>
## Compile e execute o aplicativo

Para descartar problemas de conectividade ou assembly, compile e execute o aplicativo para garantir que não existam problemas ao trabalhar primeiro. Você deve ver um aplicativo de console aberto no segundo plano. Todas as quatro consultas são executadas em sequência, sem pausar. Em muitos sistemas, todo o programa executa em menos de 15 segundos. Se o aplicativo de console incluir uma mensagem informando "Concluído. Pressione Enter para continuar", o programa foi concluído com sucesso.

Para comparar execuções de consulta, você pode marcar-copiar-colar os resultados do console e colá-los em um arquivo do Excel.

A ilustração a seguir mostra os resultados das primeiras três consultas lado a lado. Todas as consultas usam termo de pesquisa, 'melhor', que aparece em vários títulos de álbum.

   ![][10]

A primeira consulta usa a pontuação padrão. Como o termo de pesquisa aparece apenas em títulos de álbum e nenhum outro critério foi especificado, os itens com o "melhor" no título do álbum são retornados na ordem em que o serviço de pesquisa os encontra.

A segunda consulta usa um perfil de pontuação, mas observe que o perfil não teve efeito. Os resultados são idênticos aos da primeira consulta. Isso ocorre porque o perfil de pontuação impulsiona um campo ('gênero') que não é similar à consulta. O termo de pesquisa 'melhor' não existe em nenhum campo 'gênero' de nenhum documento. Quando um perfil de pontuação não tem efeito, os resultados são os mesmos da pontuação padrão.

A terceira consulta é a primeira evidência do impacto do perfil de pontuação. O termo de pesquisa ainda é 'melhor', então estamos trabalhando com o mesmo conjunto de álbuns, mas porque o perfil de pontuação fornece critérios adicionais que amplificam a 'classificação' e a 'última-atualização', alguns itens são levados mais para cima na lista.

A ilustração seguinte mostra a quarta e última consulta, impulsionada por 'margem'. O campo 'margem' não é pesquisável não pode ser retornado nos resultados da pesquisa. O valor de 'margem' foi adicionado manualmente à planilha para ajudar a ilustrar o fato de que itens com margens superiores aparecem em uma posição mais alta na lista de resultados da pesquisa.

   ![][9]

Agora que você experimentou os perfis de pontuação, tente alterar o programa para usar sintaxe de consulta diferentes, perfis de pontuação ou dados avançados diferentes. Links na próxima seção fornecem mais informações.

<a id="next-steps"></a>
## Próximas etapas

Saiba mais sobre perfis de pontuação. Consulte [Adicionar um perfil de pontuação no índice de Pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx) para obter detalhes.

Saiba mais sobre os parâmetros de sintaxe e consulta de pesquisa. Consulte [Pesquisar documentos (API REST da Pesquisa do Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes.

Precisa voltar atrás e aprender mais sobre a criação de índice? [Assista a este vídeo](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) para compreender os fundamentos básicos.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG

<!---HONumber=AcomDC_0907_2016-->