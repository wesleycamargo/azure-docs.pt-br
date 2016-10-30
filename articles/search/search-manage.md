<properties 
    pageTitle="Administração do serviço do Azure Search no portal do Azure" 
    description="Gerenciar o Azure Search, um serviço de nuvem hospedado no Microsoft Azure, usando o portal do Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard" 
    editor=""
    tags="azure-portal"/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>


# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administração do serviço do Azure Search no portal do Azure
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

O Azure Search é um serviço de pesquisa baseado em nuvem, totalmente gerenciado usado para criar uma experiência avançada de pesquisa em aplicativos personalizados. Este artigo aborda as tarefas *administração de serviço* que você pode executar no [portal do Azure](https://portal.azure.com) para um serviço de pesquisa que você já tenha provisionado. *Service administration* é leve por design, limitada às seguintes tarefas:

- Gerenciar e proteger o acesso para as *chaves de api* usadas para acesso de leitura ou gravação para o serviço.
- Ajuste a capacidade de serviço, alterando a alocação de partições e réplicas.
- Monitorar o uso de recursos, em relação aos limites máximos da sua camada de serviço.

**Não está no escopo** 

*Gerenciamento de conteúdo* (ou gerenciamento de índice) refere-se a operações, como análise de tráfego de pesquisa para entender o volume de consultas, descobrir quais termos as pessoas pesquisam e como os resultados da pesquisa bem-sucedida são para a orientação dos clientes para documentos específicos no índice. Gerenciamento de conteúdo está além do escopo deste artigo. Para obter instruções sobre como obter informações sobre operações internas no nível do índice, consulte [Análise de tráfego de pesquisa para o Azure Search](search-traffic-analytics.md).

*desempenho de consulta* também está além do escopo deste artigo. Para obter mais informações, consulte [Desempenho e otimização no Azure Search](search-performance-optimization.md).

O Azure Search não oferece soluções integradas para recuperação de desastres ou backup e restauração. Para clientes que enviam objetos e dados por push e dados para o seu serviço, o código-fonte para criar e preencher um índice é a opção de restauração de fato, se você excluir um índice por engano. Para recuperação de desastres, os clientes optam por redundância por meio de um serviço adicional em um data center regional diferente. Para obter mais informações, consulte [Desempenho e otimização no Azure Search](search-performance-optimization.md).

<a id="admin-rights"></a>
## <a name="administrator-rights-in-azure-search"></a>Direitos de administrador no Azure Search

O provisionamento ou encerramento do serviço em si podem ser feitos por um administrador ou coadministrador de assinatura do Azure.

Em um serviço, qualquer pessoa com acesso à URL do serviço e uma chave de api de administração tem acesso de leitura e gravação para o serviço, com capacidade proporcional para adicionar, excluir ou modificar objetos de servidor, como chaves de api, índices, indexadores, fontes de dados, agendas e atribuições de função, conforme implementado por meio de [funções definidas pelo RBAC](#rbac).

Todas as interações do usuário com o Azure Search se enquadram em um destes modos: acesso de leitura e gravação para o serviço (direitos de administrador) ou acesso somente de leitura ao serviço (direitos de consulta). Para obter mais informações, consulte [Gerenciar as chaves de api](#manage-keys).

<a id="sys-info"></a>
## <a name="logging-in-azure-search-and-system-information"></a>Registro no Azure Search e informações do sistema

O Azure Search não expõe os arquivos de log para um serviço individual por meio do portal ou interfaces programáticas. Na camada Básica e acima, a Microsoft monitora todos os serviços do Azure Search para disponibilidade de 99,9% por contratos de nível de serviço (SLA). Se o serviço estiver lento ou a taxa de transferência de solicitação estiver abaixo dos limites de SLA, as equipes de suporte analisam os arquivos de log disponíveis para resolver o problema.

Em termos de informações gerais sobre o serviço, você pode obter informações das seguintes maneiras:

- No portal, no painel de serviço, por meio de notificações, propriedades e mensagens de status.
- Usando o [PowerShell](search-manage-powershell.md) ou a [API REST de gerenciamento](https://msdn.microsoft.com/library/azure/dn832684.aspx) para [obter propriedades do serviço](https://msdn.microsoft.com/library/azure/dn832694.aspx), ou status no uso de recursos do índice.
- Por meio de [análise de tráfego de pesquisa](search-traffic-analytics.md), conforme observado anteriormente.

<a id="manage-keys"></a>
## <a name="manage-the-api-keys"></a>Gerenciar as chaves de api

Todas as solicitações para um serviço de pesquisa precisam de uma chave de api gerada especificamente para o seu serviço. Esta chave de API é o único mecanismo para autenticar o acesso ao ponto de extremidade do seu serviço de pesquisa. 

Uma chave de api é uma cadeia de caracteres composta de letras e números gerados aleatoriamente. Ela é gerado exclusivamente pelo seu serviço. Por meio de [permissões RBAC](#rbac), você pode excluir ou ler as chaves, mas você não pode substituir uma chave gerada por uma cadeia de caracteres definida pelo usuário (especificamente, se você tiver as senhas que costuma usar, você não pode substituir uma chave de api por uma senha definida pelo usuário). 

Dois tipos de chaves são usados para acessar seu serviço de pesquisa:

+   Admin (válida para qualquer operação de leitura e gravação em relação ao serviço)
+   Consulta (válida para operações somente leitura, como consultas em um índice)

Uma chave de api admin é criada quando o serviço é provisionado. Há duas chaves de administração, designadas como *primária* e *secundária* para mantê-las de forma linear, mas na verdade elas são intercambiáveis. Cada serviço tem duas chaves admin para que você possa derrubar uma sem perder o acesso ao seu serviço. Você pode gerar novamente as duas chaves admin, mas não pode aumentar a contagem total de chaves admin. Pode haver no máximo duas chaves admin por serviço de pesquisa.

Chaves de consulta foram criadas para aplicativos cliente que chamam a Pesquisa diretamente. Você pode criar até 50 chaves de consulta. No código do aplicativo, você pode especificar a URL de pesquisa e uma chave de api de consulta para permitir o acesso somente leitura para o serviço. O código do aplicativo também especifica o índice usado pelo seu aplicativo. Juntos, o ponto de extremidade, uma chave de api para acesso somente leitura e um índice de destino definem o nível de acesso e escopo da conexão de seu aplicativo cliente.

Para obter ou gerar chaves de API novamente, abra o painel do serviço. Clique em **CHAVES** para abrir a página de gerenciamento de chaves. Os comandos para gerar novamente ou criar chaves estão no topo da página. Por padrão, somente as chaves admin são criadas. As chaves de api de consulta devem ser criadas manualmente.

 ![][9]

<a id="rbac"></a>
## <a name="set-rbac-roles-on-administrative-access-for-azure-search"></a>Definir funções RBAC com acesso administrativo para o Azure Search

O Azure fornece um [modelo global de autorização baseado em funções](../active-directory/role-based-access-control-configure.md) para todos os serviços gerenciados por meio do portal ou nas APIs do Gerenciador de Recursos. Funções de Leitor, Colaborador e Proprietário determinam o nível de administração do serviço para usuários, grupos e entidades de segurança do Active Directory, que você atribui a cada função. 

Para o Azure Search, as permissões de RBAC determinam as seguintes tarefas administrativas:

Função|Tarefa
---|---
Proprietário|Criar ou excluir o serviço ou qualquer objeto no serviço, incluindo chaves de api, índices, indexadores, fontes de dados do indexador e agendas do indexador.<p>Exibir o status do serviço, incluindo o tamanho de armazenamento e contagens.<p>Adicionar ou excluir a associação de função (somente um Proprietário pode gerenciar a associação de função).<p>Os administradores de assinatura e proprietários de serviço possuem associação automática na função Proprietários.
Colaborador|Mesmo nível de acesso como Proprietário, menos gerenciamento de funções RBAC. Por exemplo, um Colaborador pode exibir e gerar novamente a `api-key`, mas não pode modificar as associações de função.
Leitor|Exibir chaves de consulta e de status do serviço. Os membros dessa função não podem alterar a configuração do serviço, nem exibir chaves admin.

Observe que as funções não concedem direitos de acesso para o ponto de extremidade de serviço. As operações do serviço de pesquisa, como gerenciamento de índices, preenchimento de índice e consultas em dados de pesquisa, são controladas por meio de chaves de api, não funções. Para mais informações, consulte "Autorização para gerenciamento versus operações de dados" em [O que é controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md).


<a id="secure-keys"></a>
## <a name="secure-the-api-keys"></a>Proteger as chaves de api

A chave de segurança é protegida restringindo o acesso por meio do portal ou interfaces do Gerenciador de Recursos (PowerShell ou interface de linha de comando). Conforme observado, os administradores de assinatura podem exibir e gerar novamente todas as chaves de api. Como precaução, revise as atribuições de função para entender quem tem acesso às chaves admin.

1. No painel de serviço, clique no ícone de acesso para abrir a folha de Usuários.
   ![][7]
2. Em Usuários, analise as atribuições de função existentes. Conforme o esperado, os administradores de Assinatura já possuem acesso total ao serviço por meio da função Proprietário.
3. Para analisar mais, clique em **Administradores de assinatura** e, em seguida, expanda a lista de atribuição de função para ver quem tem direitos de coadministração no seu serviço de pesquisa.

Outra maneira de exibir permissões de acesso é clicar em **Funções** na folha de Usuários. Isso exibe funções disponíveis e o número de usuários ou grupos atribuídos a cada função.

<a id="sub-5"></a>
## <a name="monitor-resource-usage"></a>Monitorar o uso de recursos

No painel, o monitoramento de recursos é limitado às informações mostradas no painel de serviço e a algumas métricas que você pode obter ao consultar o serviço. No painel do serviço, na seção Uso, é possível determinar rapidamente se os níveis de recurso da partição estão adequados para o seu aplicativo.

Usando a API do serviço de pesquisa, você pode obter uma contagem dos documentos e índices. Há limites associados a essas contagens com base na camada de preços. Para saber mais, confira [Limites de serviço de pesquisa](search-limits-quotas-capacity.md). 

+   [Obter estatísticas de índice](http://msdn.microsoft.com/library/dn798942.aspx)
+   [Contar documentos](http://msdn.microsoft.com/library/dn798924.aspx)

> [AZURE.NOTE] Os comportamentos de cache podem aumentar um limite temporariamente. Por exemplo, ao usar o serviço compartilhado, você pode ver uma contagem de documentos que ultrapassa o limite de 10.000. O aumento é temporário e será detectado na próxima verificação de aplicação do limite. 


<a id="scale"></a>
## <a name="scale-up-or-down"></a>Expandir ou reduzir

Todo serviço de pesquisa começa com um mínimo de uma réplica e uma partição. Se você tiver se inscrito em recursos dedicados usando os [tipos de preço Básico ou Standard](search-limits-quotas-capacity.md), será possível clicar no bloco **ESCALA** no painel de serviço para ajustar o número de partições e réplicas usadas pelo serviço.

Quando você adiciona capacidade por meio de qualquer recurso, o serviço as utiliza automaticamente. Não é necessária nenhuma outra medida de sua parte, mas haverá um breve atraso antes que ocorra o impacto do novo serviço. Pode levar 15 minutos ou mais para provisionar recursos adicionais.

 ![][10]

### <a name="add-replicas"></a>Adicionar réplicas

O aumento de QPS (consultas por segundo) ou o alcance da alta disponibilidade são feitos adicionando réplicas. Cada réplica tem uma cópia de um índice, de modo que adicionar mais uma réplica se traduz em mais um índice que pode ser usado para atender às solicitações de consulta. Um mínimo de 3 réplicas são necessários para alta disponibilidade (consulte [Planejamento de capacidade](search-capacity-planning.md) para obter detalhes).

Um serviço de pesquisa com mais réplicas é capaz de balancear a carga das solicitações de consulta em um número maior de índices. Em um determinado nível de volume de consultas, a produtividade das consultas será maior quando houver mais cópias do índice disponíveis para atender à solicitação. Caso esteja ocorrendo latência nas consultas, você pode esperar um impacto positivo quando as réplicas adicionais entrarem em atividade.

Embora a produtividade das consultas aumente conforme você adiciona réplicas, ela não necessariamente dobra ou triplica conforme você adiciona réplicas ao seu serviço. Todos os aplicativos de pesquisa estão sujeitos a fatores externos que podem afetar o desempenho das consultas. Consultas complexas e latência da rede são dois fatores que contribuem para variações nos tempos de resposta das consultas.

### <a name="add-partitions"></a>Adicionar partições

A maioria dos aplicativos de serviço possui uma necessidade integrada de mais réplicas em vez de partições. Nos casos em que seja necessária uma contagem maior de documentos, é possível adicionar partições se você se inscreveu no serviço Standard. A camada Básica não fornece partições adicionais.

Na camada Standard, as partições são adicionadas em múltiplos de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Isso é um artefato de fragmentação. Um índice é criado em 12 fragmentos, que podem todos ser armazenados em 1 partição ou divididos igualmente em 2, 3, 4, 6 ou 12 partições (um fragmento por partição).

### <a name="remove-replicas"></a>Remover réplicas

Após períodos de grandes volumes de consulta, é mais provável que você reduza réplicas quando as cargas de consulta tiverem se normalizado (por exemplo, após as vendas de final de ano).

Para fazer isso, mova o controle deslizante de réplicas para um número mais baixo. Não são necessárias mais medidas de sua parte. Reduzir o número de réplicas libera máquinas virtuais no datacenter. Suas operações de consulta e ingestão de dados passarão a ser executadas em menos VMs do que antes. O limite mínimo é uma réplica.

### <a name="remove-partitions"></a>Remover partições

Em contraste com a remoção de réplicas, que não requer nenhum esforço extra da sua parte, você pode precisar tomar algumas medidas se estiver usando armazenamento a mais que pode ser reduzido. Por exemplo, se a sua solução estiver usando três partições, fazendo o downsizing de uma ou duas partições gerará um erro se o novo espaço de armazenamento é menor do que o necessário. Como é de se esperar, suas opções são excluir índices ou documentos dentro de um índice associado para liberar espaço ou manter a configuração atual.

Não há um método de detecção que informe quais fragmentos de índices estão armazenados em quais partições. Cada partição fornece cerca de 25 GB de armazenamento, de modo que você precisará reduzir o armazenamento para um tamanho que possa ser acomodado pelo número de partições que possui. Caso queira reverter para uma partição, os 12 fragmentos precisarão se encaixar.

Para se planejar para o futuro, talvez você queira conferir o armazenamento (usando [Obter Estatísticas do Índice](http://msdn.microsoft.com/library/dn798942.aspx)) para ver o quanto você realmente usou. 

<a id="advanced-deployment"></a>
## <a name="best-practices-on-scale-and-deployment-(video)"></a>Práticas recomendadas em escala e implantação (vídeo)

Este vídeo de 30 minutos examina as práticas recomendadas para cenários de implantação avançados, incluindo cargas de trabalho distribuídas geograficamente. Você também pode consultar [Desempenho e otimização no Azure Search](search-performance-optimization.md) para páginas de ajuda que abrangem os mesmos pontos.

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<a id="next-steps"></a>
## <a name="next-steps"></a>Próximas etapas

Depois de compreender os tipos de operações relativas à administração do serviço, considere as diversas abordagens para o gerenciamento de serviço:

- [PowerShell](search-manage-powershell.md)
- [API REST de gerenciamento](search-get-started-management-api.md)

Além disso, se você ainda não fez isso, examine o [artigo de desempenho e otimização](search-performance-optimization.md), e, opcionalmente, assista ao vídeo observado na seção anterior para obter mais detalhes e demonstrações técnicas recomendadas.


<!--Image references-->
[7]: ./media/search-manage/rbac-icon.png
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png


 



<!--HONumber=Oct16_HO2-->


