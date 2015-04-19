<properties 
	pageTitle="Gerenciar seu serviço de Pesquisa no Microsoft Azure" 
	description="Gerenciar seu serviço de Pesquisa no Microsoft Azure" 
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
	ms.date="01/16/2015" 
	ms.author="heidist"/>

# Gerenciar seu serviço de Pesquisa no Microsoft Azure


[WACOM.INCLUDE [Este artigo usa o portal de Visualização do Azure](../includes/preview-portal-note.md)]

A Pesquisa do Azure é um serviço baseado em nuvem e uma API baseada em HTTP que pode ser usada em aplicativos de pesquisa personalizados. Nosso serviço de Pesquisa fornece o mecanismo para análise de texto de pesquisa em texto completo, recursos de pesquisa avançada, armazenamento de dados de pesquisa e uma sintaxe de comandos de consulta. 

Este artigo explica como administrar um serviço de pesquisa no novo [portal de Visualização do Azure](https://portal.azure.com).

Como alternativa, você pode usar a API REST de gerenciamento. Consulte [Introdução ao Azure API de REST de gerenciamento de pesquisa](http://azure.microsoft.com/documentation/articles/search-get-started-management-api/) e [referência de API de REST do Gerenciamento de Pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn832684.aspx) para obter detalhes.

<!--TOC-->

+ [Adicionar o serviço de pesquisa à sua assinatura](#sub-1)
+ [Tarefas administrativas](#sub-2)
+ [URL do Serviço](#sub-3)
+ [Gerenciar as chaves de API](#sub-4)
+ [Monitorar o uso de recursos](#sub-5)
+ [Expandir ou reduzir](#sub-6)
+ [Iniciar ou parar o serviço](#sub-7)
+ [Definir funções para controlar o acesso administrativo](#sub-8)

<h2 id="sub-1">Adicionar o serviço de pesquisa à sua assinatura</h2>

Como o administrador configura um Serviço de Pesquisa, uma das primeiras decisões é escolher uma faixa de preço. As opções incluem gratuito e níveis de preços padrão.

Sem custos adicionais para assinantes atuais, você pode escolher um serviço compartilhado, recomendado para fins de aprendizado, testes de prova de conceito e pequenos projetos de desenvolvimento. O serviço compartilhado é limitado a 50 MB de armazenamento, três índices e contagem de documentos - um limite de 10.000 documentos, ainda que o consumo de armazenamento seja inferior aos 50 MB disponibilizados. Não há garantias de desempenho do serviço compartilhado, ou seja, se você for criar um aplicativo de pesquisa de produção, considere usar a pesquisa padrão.

A pesquisa padrão é cobrada porque você se inscreverá para usar infraestrutura e os recursos dedicados, usados apenas pela sua assinatura. A pesquisa padrão é alocada em pacotes de partições (armazenamento) e réplicas (cargas de trabalho do serviço) definidos pelo usuário, e o preço é definido por unidade de pesquisa. Você pode escalar verticalmente as partições ou réplicas de maneira independente, adicionando o recurso que for necessário.

Para se planejar quanto à capacidade e compreender o impacto da cobrança, recomendamos estes links:

+	[Limites e restrições](http://msdn.microsoft.com/library/dn798934.aspx)
+	[Detalhes dos Preços](http://go.microsoft.com/fwlink/p/?LinkdID=509792)

Quando você estiver pronto para se inscrever, consulte [Introdução à Pesquisa do Azure](search-get-started.md).


<h2 id="sub-2">Tarefas administrativas</h2>

Embora alguns serviços possam ter coadministradores, um serviço de Pesquisa do Azure pode ter somente um administrador por assinatura. Você precisa ser um administrador para executar as tarefas descritas nesta seção.
Além de adicionar Pesquisa à assinatura, o administrador é responsável por estas tarefas adicionais:

+	Distribuição do URL do serviço (definido durante o provisionamento do serviço).
+	Gerenciamento e distribuição das chaves de API.
+	Monitoramento do uso de recursos
+ Escala ou redução vertical (aplica-se apenas à pesquisa padrão)
+	Início ou interrupção do serviço
+ Definição de funções para controlar o acesso administrativo

<h2 id="sub-3">URL do Serviço</h2>

O URL do serviço é definido como uma propriedade fixa ao criar o serviço e não pode ser alterado mais tarde. 

Os desenvolvedores que estiverem compilando aplicativos de pesquisa precisarão conhecer o URL do serviço para as solicitações HTTP. É possível localizar o URL do serviço rapidamente por meio do painel do serviço.

Para obter a URL do serviço do painel de serviço:

1.	Entre no [Portal de Visualização do Azure](https://portal.azure.com).
2.	Clique em **Procurar** | **Tudo** | **Serviços de Pesquisa**.
3.	Clique no nome do seu serviço de pesquisa para abrir o painel.
4.	Clique em **PROPRIEDADES** para abrir uma página de propriedades deslizando-a. A URL do serviço está no topo da página. Você pode fixar esta página para acessá-la rapidamente mais tarde.

    ![][8]

Os desenvolvedores podem solicitar também a versão da API. Um requisito de codificação da API de Pesquisa do Azure é sempre especificar a versão da API na solicitação. Este requisito existe para que os desenvolvedores possam continuar usando uma versão anterior e passar a uma versão posterior quando for o momento certo.

A versão da API não é exibida nas páginas do portal, de modo que não se trata de uma informação que você possa fornecer. Para obter informações sobre versões atuais e anteriores da API, consulte [API REST de Pesquisa do Azure](http://go.microsoft.com/fwlink/p/?LinkdID=509922).


<!---->
<h2 id="sub-4">Gerenciar as chaves de API</h2>

Desenvolvedores que estiverem compilando aplicativos de pesquisa precisarão ter uma chave de API para acessar a Pesquisa. Toda solicitação HTTP para seu serviço de pesquisa precisará de uma chave de API gerada especificamente para seu serviço. Esta chave de API é o único mecanismo para autenticar o URL do seu serviço.

Dois tipos de chaves são usados para acessar seu serviço de pesquisa:

+	Administração (válida para qualquer operação)
+	Consulta (válida somente para solicitações de consulta)

Uma chave de API de administração é criada com o serviço. Há uma chave primária e uma secundária. Você pode usá-las da mesma forma; nenhuma das duas indica um nível mais alto ou mais baixo de acesso, o que é útil caso você queira sobrepor chaves. Você pode gerar novamente as duas chaves admin, mas não pode aumentar a contagem total de chaves admin. Pode haver no máximo duas chaves de administração por serviço de pesquisa.

Chaves de consulta foram criadas para uso em aplicativos cliente que chamam a Pesquisa diretamente. Você pode criar até 50 chaves de consulta.

Para obter ou gerar chaves de API novamente, abra o painel do serviço. Clique em **CHAVES** para abrir a página de gerenciamento de chaves. Os comandos para gerar novamente ou criar chaves estão no topo da página.

 ![][9]


<!---->
<h2 id="sub-5">Monitorar o uso de recursos</h2>

Nesta visualização pública, o monitoramento de recursos é limitado às informações mostradas no painel de serviço e a algumas métricas que você pode obter ao consultar o serviço.

No painel do serviço, na seção Uso, é possível determinar rapidamente se os níveis de recurso da partição estão adequados para o seu aplicativo.

Usando a API do serviço de pesquisa, você pode obter uma contagem dos documentos e índices. Há limites associados a essas contagens com base na camada de preços. Consulte [Limites e restrições](http://msdn.microsoft.com/library/dn798934.aspx) para obter detalhes. 

+	[Obter estatísticas de índice](http://msdn.microsoft.com/library/dn798942.aspx)
+	[Contar documentos](http://msdn.microsoft.com/library/dn798924.aspx)

> [WACOM.NOTE] Os comportamentos de cache podem aumentar um limite temporariamente. Por exemplo, ao usar o serviço compartilhado, você pode ver uma contagem de documentos que ultrapassa o limite de 10.000. O aumento é temporário e será detectado na próxima verificação de aplicação do limite. 


<!---->
<h2 id="sub-6">Expandir ou reduzir</h2>

Todo serviço de pesquisa começa com um mínimo de uma réplica e uma partição. Se tiver se inscrito para receber serviços dedicados usando a camada de preços Padrão, você pode clicar no bloco **ESCALA** no painel de serviço para reajustar o número de partições e réplicas usadas pelo serviço.

Quando você adiciona um dos recursos, o serviço os utiliza automaticamente. Não é necessária nenhuma outra medida de sua parte, mas haverá um breve atraso antes que ocorra o impacto do novo serviço. Pode levar 15 minutos ou mais para provisionar os recursos adicionais.

 ![][10]

<h3>Adicionar réplicas</h3>

O aumento de QPS (consultas por segundo) ou o alcance da alta disponibilidade são feitos adicionando réplicas. Cada réplica tem uma cópia de um índice, de modo que adicionar mais uma réplica se traduz em mais um índice que pode ser usado para atender às solicitações de consulta. Atualmente, a regra de ouro é que você precisa de pelo menos três réplicas para ter uma alta disponibilidade. 

Um serviço de pesquisa com mais réplicas é capaz de balancear a carga das solicitações de consulta em um número maior de índices. Em um determinado nível de volume de consultas, a produtividade das consultas será maior quando houver mais cópias do índice disponíveis para atender à solicitação. Caso esteja ocorrendo latência nas consultas, você pode esperar um impacto positivo quando as réplicas adicionais ficarem online.

Embora a produtividade das consultas aumente conforme você adiciona réplicas, ela não necessariamente dobra ou triplica conforme você adiciona réplicas ao seu serviço. Todos os aplicativos de pesquisa estão sujeitos a fatores externos que podem afetar o desempenho das consultas. Consultas complexas e latência da rede são dois fatores que contribuem para variações nos tempos de resposta das consultas.

<h3>Adicionar partições</h3>

A maioria dos aplicativos de serviços tem a necessidade de mais réplicas do que de partições, uma vez que a maioria dos aplicativos que utiliza pesquisas é ajustado facilmente em uma única partição que pode dar suporte a até 15 milhões de documentos. 

Para os casos em que é necessária uma contagem maior de documentos, você pode adicionar partições. Observe que as partições são adicionadas em múltiplos de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Trata-se de um artifício de fragmentação; um índice é criado em 12 fragmentos, que podem todos ser armazenados em uma partição ou divididos igualmente em 2, 3, 4, 6 ou 12 partições (um fragmento por partição).

<h3>Remover réplicas </h3>

Após períodos de grandes volumes de consulta, é mais provável que você remova réplicas quando as cargas de consulta tiverem se normalizado (por exemplo, após as vendas de final de ano).

Para fazer isso, basta mover o controle deslizante de réplicas para um número mais baixo. Não são necessárias mais medidas de sua parte. Reduzir o número de réplicas libera máquinas virtuais no datacenter. Suas operações de consulta e ingestão de dados passarão a ser executadas em menos VMs do que antes. O limite mínimo é uma réplica.

<h3>Remover partições</h3>

Em contraste com a remoção de réplicas, que não requer nenhum esforço extra da sua parte, você pode precisar tomar algumas medidas se estiver usando armazenamento a mais que pode ser reduzido. Por exemplo, se a sua solução estiver usando três partições, tentar reduzir uma ou duas partições gerará um erro se você estiver usando mais espaço de armazenamento do que pode ser armazenado no número reduzido de partições. Neste caso, suas opções são excluir índices ou documentos dentro de um índice associado para liberar espaço ou manter a configuração atual.

Não há um método de detecção que informe quais fragmentos de índices estão armazenados em quais partições. Cada partição fornece cerca de 25 GB de armazenamento, de modo que você precisará reduzir o armazenamento para um tamanho que possa ser acomodado pelo número de partições que possui. Caso queira reverter para uma partição, os 12 fragmentos precisarão ser ajustados.

Para ajudar no planejamento para o futuro, você pode desejar conferir o armazenamento (usando [Obter Estatísticas do Índice](http://msdn.microsoft.com/library/dn798942.aspx)) para ver o quanto você realmente usou. 


<!---->
<h2 id="sub-7">Iniciar ou parar o serviço</h2>

Você pode iniciar, parar ou mesmo excluir o serviço usando comandos no painel de serviços.

 ![][11]


Parar ou iniciar o serviço não desativa a cobrança. Você deve excluir o serviço para evitar todas as cobranças. Quaisquer dados associados ao seu serviço são excluídos quando ele for encerrado.

<!---->
<h2 id="sub-8">Definir funções sobre o acesso administrativo</h2>

O Azure fornece um modelo global de autorização baseado em funções para todos os serviços gerenciados por meio do portal de Visualização ou na API do Gerenciador de Recursos do Azure se você estiver usando uma ferramenta de administração personalizada. Funções de Leitor, Colaborador e Proprietário definem o nível de administração para usuários do Active Directory, grupos e entidades de segurança que você atribui a cada função. Consulte [Controle de acesso baseado em função no Portal de Visualização do Azure](http://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) para detalhes sobre a associação do membro.

Em termos de Pesquisa do Azure, controles de acesso baseados em função determinam as seguintes tarefas administrativas:

<table>
<tr>
<td>Proprietário</td>
<td>
Iniciar, interromper ou excluir o serviço.</br>
Gerar e exibir chaves de administração e de consulta.</br>
Exibir o status do serviço, incluindo a contagem de índice, nomes de índice, contagem de documentos e tamanho de armazenamento.</br>
Adicionar ou excluir a associação de função (somente um Proprietário pode gerenciar a associação de função).</br>
</br>
Os administradores de serviço e assinatura têm associação automática na função Proprietários.
</td>
</tr>
<tr>
<td>Colaborador</td>	
<td>Tem o mesmo nível de acesso que o Proprietário, com exceção de gerenciamento de função. Por exemplo, um Colaborador pode exibir e gerar novamente a 'chave api', mas não pode modificar as associações de função.
</td>
</tr>
<tr>
<td>Leitor</td>
<td>Exibir chaves de consulta e de status do serviço. Os membros dessa função não podem iniciar ou parar um serviço, nem exibir chaves de administração.
</td>
</tr>
</table>

Observe que as funções não concedem direitos de acesso para o ponto de extremidade de serviço. As operações do serviço de pesquisa, como gerenciamento de índices, preenchimento de índice e consultas em dados de pesquisa, são controladas por meio de chaves de api, não funções. Consulte "Autorização para gerenciamento versus operações de dados" no [controle de acesso baseado em função no Portal de Visualização do Azure](http://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) para obter mais informações.

As funções fornecem controle de acesso depois da criação do serviço. Apenas os gerentes de assinatura podem adicionar um serviço de Pesquisa a uma assinatura.


<!--Anchors-->
[Adicionar o serviço de pesquisa à sua assinatura]: #sub-1
[Tarefas administrativas]: #sub-2
[URL do Serviço]: #sub-3
[Gerenciar as chaves de API]: #sub-4
[Monitorar o uso de recursos]: #sub-5
[Expandir ou reduzir]: #sub-6
[Iniciar ou parar o serviço]: #sub-7
[Definir funções para controlar o acesso administrativo]: #sub-8


<!--Image references-->
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
[11]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png


<!--Link references-->
[Introdução à Pesquisa do Azure]: ../search-get-started/
[Fluxo de trabalho de desenvolvimento de Pesquisa do Azure]: ../search-workflow/
[Criar sua primeira solução de pesquisa do Azure]: ../search-create-first-solution/


<!--HONumber=46--> 
