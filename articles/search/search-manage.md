<properties 
	pageTitle="Gerencie o seu serviço de Pesquisa no Microsoft Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado" 
	description="Gerenciar a Pesquisa do Azure, serviço de pesquisa de nuvem hospedado do Microsoft Azure" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="11/04/2015" 
	ms.author="heidist"/>

# Gerenciar seu serviço de Pesquisa no Microsoft Azure

A Pesquisa do Azure é um serviço baseado em nuvem com uma API baseada em HTTP que pode ser usado em aplicativos de pesquisa personalizados. Nosso serviço de Pesquisa fornece o mecanismo para análise de texto de pesquisa em texto completo, recursos de pesquisa avançada, armazenamento de dados de pesquisa e uma sintaxe de comandos de consulta.

Este artigo explica como administrar um serviço de Pesquisa no [Portal clássico do Azure](https://portal.azure.com).

Como alternativa, você pode usar a API REST de gerenciamento. Confira [Introdução à API REST de Gerenciamento de Pesquisa do Azure](search-get-started-management-api.md) e [referência da API REST do Gerenciamento de Pesquisa do Azure](http://msdn.microsoft.com/library/azure/dn832684.aspx) para obter detalhes.

<a id="sub-1"></a>
## Adicionar o serviço de pesquisa à sua assinatura

Como o administrador configura um Serviço de Pesquisa, uma das primeiras decisões é escolher uma faixa de preço. As opções incluem gratuito e níveis de preços padrão.

Sem custos adicionais para assinantes atuais, você pode escolher um serviço compartilhado, recomendado para fins de aprendizado, testes de prova de conceito e pequenos projetos de desenvolvimento. O serviço compartilhado é limitado a 50 MB de armazenamento, três índices e contagem de documentos - um limite de 10.000 documentos, ainda que o consumo de armazenamento seja inferior aos 50 MB disponibilizados. Não há garantias de desempenho do serviço compartilhado, ou seja, se você for criar um aplicativo de pesquisa de produção, considere usar a pesquisa padrão.

A pesquisa padrão é cobrada porque você se inscreverá para usar infraestrutura e os recursos dedicados, usados apenas pela sua assinatura. A pesquisa padrão é alocada em pacotes de partições (armazenamento) e réplicas (cargas de trabalho do serviço) definidos pelo usuário, e o preço é definido por unidade de pesquisa. Você pode expandir as partições ou réplicas de maneira independente, adicionando o recurso que for necessário.

Para se planejar quanto à capacidade e compreender o impacto da cobrança, recomendamos estes links:

+	[Limites e restrições](search-limits-quotas-capacity.md)
+	[Detalhes de preços](http://go.microsoft.com/fwlink/p/?LinkdID=509792)

Quando você estiver pronto para se inscrever, confira [Criar um serviço de Pesquisa no portal](search-create-service-portal.md).

<a id="sub-2"></a>
## Tarefas administrativas

Embora alguns serviços possam ter coadministradores, um serviço de Pesquisa do Azure pode ter somente um administrador por assinatura. Você precisa ser um administrador para executar as tarefas descritas nesta seção. Além de adicionar Pesquisa à assinatura, o administrador é responsável por estas tarefas adicionais:

+	Distribuição da URL do serviço (definida durante o provisionamento do serviço).
+	Gerenciamento e distribuição das chaves de API.
+	Monitorar o uso de recursos
+	Expandir ou reduzir (aplica-se apenas à pesquisa padrão)
+	Iniciar ou parar o serviço
+	Definir funções para controlar o acesso administrativo

<a id="sub-3"></a>
## URL do Serviço

A URL do serviço é definida como uma propriedade fixa quando você cria o serviço e não pode ser alterada mais tarde.

Os desenvolvedores que estiverem compilando aplicativos de pesquisa precisarão conhecer a URL do serviço para as solicitações HTTP. É possível localizar a URL do serviço rapidamente por meio do painel do serviço.

Para obter a URL do serviço do painel de serviço:

1.	Entre no [Portal Clássico do Azure](https://portal.azure.com).
2.	Clique em **Navegar** | **Tudo** | **Serviços de pesquisa**.
3.	Clique no nome do seu serviço de pesquisa para abrir o painel.
4.	Clique em **PROPRIEDADES** para abrir uma página de propriedades. A URL do serviço está no topo da página. Você pode fixar esta página para acessá-la rapidamente mais tarde.

    ![][8]

Os desenvolvedores podem solicitar também a versão da API. Um requisito de codificação da API de Pesquisa do Azure é sempre especificar a versão da API na solicitação. Este requisito existe para que os desenvolvedores possam continuar usando uma versão anterior e passar a uma versão posterior quando for o momento certo.

A versão da API não é exibida nas páginas do portal, de modo que não se trata de uma informação que você possa fornecer. Para obter informações sobre versões atuais e anteriores da API, consulte [API REST de Pesquisa do Azure](http://go.microsoft.com/fwlink/p/?LinkdID=509922).


<a id="sub-4"></a>
## Gerenciar as chaves de API

Desenvolvedores que estiverem compilando aplicativos de pesquisa precisarão ter uma chave de API para acessar a Pesquisa. Toda solicitação HTTP para seu serviço de pesquisa precisará de uma chave de API gerada especificamente para seu serviço. Esta chave de API é o único mecanismo para autenticar a URL do seu serviço.

Dois tipos de chaves são usados para acessar seu serviço de pesquisa:

+	Admin (válida para qualquer operação)
+	Consulta (válida somente para solicitações de consulta)

Uma chave de API admin é criada com o serviço. Há uma chave primária e uma secundária. Você pode usá-las da mesma forma; nenhuma das duas indica um nível mais alto ou mais baixo de acesso, o que é útil caso você queira sobrepor chaves. Você pode gerar novamente as duas chaves admin, mas não pode aumentar a contagem total de chaves admin. Pode haver no máximo duas chaves admin por serviço de pesquisa.

Chaves de consulta foram criadas para uso em aplicativos cliente que chamam a Pesquisa diretamente. Você pode criar até 50 chaves de consulta.

Para obter ou gerar chaves de API novamente, abra o painel do serviço. Clique em **CHAVES** para abrir a página de gerenciamento de chaves. Os comandos para gerar novamente ou criar chaves estão no topo da página.

 ![][9]


<a id="sub-5"></a>
## Monitorar o uso de recursos

Nesta visualização pública, o monitoramento de recursos é limitado às informações mostradas no painel de serviço e a algumas métricas que você pode obter ao consultar o serviço.

No painel do serviço, na seção Uso, é possível determinar rapidamente se os níveis de recurso da partição estão adequados para o seu aplicativo.

Usando a API do serviço de pesquisa, você pode obter uma contagem dos documentos e índices. Há limites associados a essas contagens com base na camada de preços. Consulte [Limites e restrições](search-limits-quotas-capacity.md) para obter detalhes.

+	[Obter estatísticas de índice](http://msdn.microsoft.com/library/dn798942.aspx)
+	[Contar documentos](http://msdn.microsoft.com/library/dn798924.aspx)

> [AZURE.NOTE]Os comportamentos de cache podem aumentar um limite temporariamente. Por exemplo, ao usar o serviço compartilhado, você pode ver uma contagem de documentos que ultrapassa o limite de 10.000. O aumento é temporário e será detectado na próxima verificação de aplicação do limite.


<a id="sub-6"></a>
## Expandir ou reduzir

Todo serviço de pesquisa começa com um mínimo de uma réplica e uma partição. Se tiver se inscrito para receber serviços dedicados usando a camada de preços Padrão, você pode clicar no bloco **ESCALA** no painel de serviço para reajustar o número de partições e réplicas usadas pelo serviço.

Quando você adiciona um dos recursos, o serviço os utiliza automaticamente. Não é necessária nenhuma outra medida de sua parte, mas haverá um breve atraso antes que ocorra o impacto do novo serviço. Pode levar 15 minutos ou mais para provisionar os recursos adicionais.

 ![][10]

### Adicionar réplicas

O aumento de QPS (consultas por segundo) ou o alcance da alta disponibilidade são feitos adicionando réplicas. Cada réplica tem uma cópia de um índice, de modo que adicionar mais uma réplica se traduz em mais um índice que pode ser usado para atender às solicitações de consulta. Atualmente, a regra de ouro é que você precisa de pelo menos três réplicas para ter uma alta disponibilidade.

Um serviço de pesquisa com mais réplicas é capaz de balancear a carga das solicitações de consulta em um número maior de índices. Em um determinado nível de volume de consultas, a produtividade das consultas será maior quando houver mais cópias do índice disponíveis para atender à solicitação. Caso esteja ocorrendo latência nas consultas, você pode esperar um impacto positivo quando as réplicas adicionais entrarem em atividade.

Embora a produtividade das consultas aumente conforme você adiciona réplicas, ela não necessariamente dobra ou triplica conforme você adiciona réplicas ao seu serviço. Todos os aplicativos de pesquisa estão sujeitos a fatores externos que podem afetar o desempenho das consultas. Consultas complexas e latência da rede são dois fatores que contribuem para variações nos tempos de resposta das consultas.

### Adicionar partições

A maioria dos aplicativos de serviços tem a necessidade de mais réplicas do que de partições, uma vez que a maioria dos aplicativos que utiliza pesquisas cabe facilmente em uma única partição que pode suportar até 15 milhões de documentos.

Para os casos em que é necessária uma contagem maior de documentos, você pode adicionar partições. Observe que as partições são adicionadas em múltiplos de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Trata-se de um artifício de fragmentação; um índice é criado em 12 fragmentos, que podem todos ser armazenados em uma partição ou divididos igualmente em 2, 3, 4, 6 ou 12 partições (um fragmento por partição).

### Remover réplicas

Após períodos de grandes volumes de consulta, é mais provável que você remova réplicas quando as cargas de consulta tiverem se normalizado (por exemplo, após as vendas de final de ano).

Para fazer isso, basta mover o controle deslizante de réplicas para um número mais baixo. Não são necessárias mais medidas de sua parte. Reduzir o número de réplicas libera máquinas virtuais no datacenter. Suas operações de consulta e ingestão de dados passarão a ser executadas em menos VMs do que antes. O limite mínimo é uma réplica.

### Remover partições

Em contraste com a remoção de réplicas, que não requer nenhum esforço extra da sua parte, você pode precisar tomar algumas medidas se estiver usando armazenamento a mais que pode ser reduzido. Por exemplo, se a sua solução estiver usando três partições, tentar reduzir uma ou duas partições gerará um erro se você estiver usando mais espaço de armazenamento do que pode ser armazenado no número reduzido de partições. Neste caso, suas opções são excluir índices ou documentos dentro de um índice associado para liberar espaço ou manter a configuração atual.

Não há um método de detecção que informe quais fragmentos de índices estão armazenados em quais partições. Cada partição fornece cerca de 25 GB de armazenamento, de modo que você precisará reduzir o armazenamento para um tamanho que possa ser acomodado pelo número de partições que possui. Caso queira reverter para uma partição, os 12 fragmentos precisarão se encaixar.

Para se planejar para o futuro, talvez você queira conferir o armazenamento (usando [Obter Estatísticas do Índice](http://msdn.microsoft.com/library/dn798942.aspx)) para ver o quanto você realmente usou.


<a id="sub-7"></a>
## Iniciar ou parar o serviço

Você pode iniciar, parar ou mesmo excluir o serviço usando comandos no painel de serviços.

 ![][11]


Parar ou iniciar o serviço não desativa a cobrança. Você deve excluir o serviço para evitar todas as cobranças. Quaisquer dados associados ao seu serviço são excluídos quando ele for encerrado.

<a id="sub-8"></a>
## Definir funções sobre o acesso administrativo

O Azure fornece um modelo global de autorização baseado em funções para todos os serviços gerenciados por meio do portal ou na API do Gerenciador de Recursos do Azure, se você estiver usando uma ferramenta de administração personalizada. Funções de Leitor, Colaborador e Proprietário definem o nível de administração para usuários do Active Directory, grupos e entidades de segurança que você atribui a cada função. Consulte o [Controle de acesso baseado em função no Portal clássico do Azure](../role-based-access-control-configure.md) para obter detalhes sobre a associação de função.

Em termos de Pesquisa do Azure, controles de acesso baseados em função determinam as seguintes tarefas administrativas:


Função|Tarefa
---|---
Proprietário|Iniciar, interromper ou excluir o serviço.<p>Gerar e exibir as chaves do administrador e as chaves de consulta.<p>Exibir o status do serviço, incluindo a contagem de índice, nomes de índice, contagem de documentos e tamanho de armazenamento.<p>Adicionar ou excluir a associação de função (somente um Proprietário pode gerenciar a associação de função).<p>Os administradores de serviço e assinatura têm associação automática na função Proprietários.
Colaborador|Tem o mesmo nível de acesso que o Proprietário, com exceção de gerenciamento de função. Por exemplo, um Colaborador pode exibir e gerar novamente a `api-key`, mas não pode modificar as associações de função.
Leitor|Exibir chaves de consulta e de status do serviço. Os membros dessa função não podem iniciar ou parar um serviço, nem exibir chaves de administração.

Observe que as funções não concedem direitos de acesso para o ponto de extremidade de serviço. As operações do serviço de pesquisa, como gerenciamento de índices, preenchimento de índice e consultas em dados de pesquisa, são controladas por meio de chaves de api, não funções. Consulte “Autorização para gerenciamento versus operações de dados” no [Controle de acesso baseado em função no Portal clássico do Azure](../role-based-access-control-configure.md) para mais informações.

As funções fornecem controle de acesso depois da criação do serviço. Apenas os gerentes de assinatura podem adicionar um serviço de Pesquisa a uma assinatura.

<!--Anchors-->
[Add search service to your subscription]: #sub-1
[Administrative tasks]: #sub-2
[Service URL]: #sub-3
[Manage the api-keys]: #sub-4
[Monitor resource usage]: #sub-5
[Scale up or down]: #sub-6
[Start or Stop the Service]: #sub-7
[Set roles to control administrative access]: #sub-8

<!--Image references-->
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
[11]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png


 

<!---HONumber=AcomDC_1203_2015-->