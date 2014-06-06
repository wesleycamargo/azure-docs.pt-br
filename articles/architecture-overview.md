<properties linkid="" urlDisplayName="" pageTitle="Arquitetura" metaKeywords="" description="Visão geral da arquitetura que aborda os padrões de design comuns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Visão geral da arquitetura" authors="" solutions="" writer="waltpo" manager="bjsmith" editor="mattshel"  />

#Arquitetura
Aprender como implementar os padrões de design comuns no Azure.

###Definir ícone/símbolo do Azure

[Baixar o conjunto de ícone/símbolo do Azure](http://www.microsoft.com/pt-br/download/details.aspx?id=41937) para criar materiais de técnicos que descrevem (ou usam) do Azure—coisas como diagramas de arquitetura, materiais de treinamento, apresentações, folhas de dados, infográficos e white papers. Você pode baixar os símbolos nos formatos PPT, Visio ou PNG. Gostaríamos de saber o que você pensa, portanto, há instruções para fornecer comentários no download.

![Definir ícone/símbolo do Azure][azure_symbols]

##Padrões de design

###[Consumidores concorrentes](http://msdn.microsoft.com/pt-br/library/dn568101.aspx)

![Consumidores concorrentes][competing_consumers]

Permitir que vários consumidores simultâneos processem as mensagens recebidas no mesmo canal de mensagens. Esse padrão permite que um sistema processe várias mensagens simultaneamente para otimizar o resultado, melhorar a escalabilidade e disponibilidade e equilibrar a carga de trabalho. 

###[Segregação de responsabilidade da consulta e comando](http://msdn.microsoft.com/pt-br/library/dn568103.aspx)

![Segregação de responsabilidade da consulta e comando][cqrs]

Separar as operações que leem dados de operações que atualizam dados usando interfaces separadas. Esse padrão pode maximizar o desempenho, a escalabilidade e a segurança; suporte a evolução do sistema ao longo do tempo por meio de maior flexibilidade; e impedir que os comandos de atualização causem conflitos de mesclagem a nível do domínio.

###[Eleição de líder](http://msdn.microsoft.com/pt-br/library/dn568104.aspx)

![Eleição de líder][leader_election]

Coordene as ações executadas por um conjunto de instâncias de tarefas de colaboração em um aplicativo distribuído elegendo uma instância como líder que assume a responsabilidade pelo gerenciamento das outras instâncias. Esse padrão pode ajudar a garantir que instâncias de tarefa não entrem em conflito umas com as outras, causem a contenção de recursos compartilhados ou inadvertidamente interfira com o trabalho que outras instâncias de tarefa estão realizando.

###[Filtros e tubos](http://msdn.microsoft.com/pt-br/library/dn568100.aspx)

![Filtros e tubos][pipes_and_filters]

Decompor uma tarefa que executa processamento complexo em uma série de elementos discretos que podem ser reutilizados. Esse padrão pode melhorar o desempenho, a escalabilidade e a capacidade de reutilização, permitindo que os elementos de tarefa que executam o processamento para ser implantado e dimensionadas de forma independente.

###[Chave de manobrista](http://msdn.microsoft.com/pt-br/library/dn568102.aspx)

![Chave de manobrista][valet_key]

Use um símbolo ou a chave que fornece aos clientes acesso restrito a um determinado recurso ou serviço para descarregar operações de transferência de dados do código do aplicativo. Esse padrão é particularmente útil em aplicativos que usam sistemas de armazenamento hospedado na nuvem ou filas e pode diminuir os custos e maximizar o desempenho e a escalabilidade.

### Orientações adicionais

Para obter informações sobre padrões de design mais comuns no Azure, consulte [Padrões de Design em Nuvem](http://msdn.microsoft.com/pt-br/library/dn568099.aspx).


[competing_consumers]: ./media/architecture-overview/CompetingConsumers.png
[cqrs]: ./media/architecture-overview/CQRS.png
[leader_election]: ./media/architecture-overview/LeaderElection.png
[pipes_and_filters]: ./media/architecture-overview/PipesAndFilters.png
[valet_key]: ./media/architecture-overview/ValetKey.png
[azure_symbols]: ./media/architecture-overview/AzureSymbols.png


