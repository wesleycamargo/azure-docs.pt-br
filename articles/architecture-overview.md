<properties linkid="develop-net-architecture sublanding" urlDisplayName="" pageTitle="Architecture" metaKeywords="" description="Architecture overview that covers common design patterns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Architecture Overview" authors="robb" solutions="" manager="johndaw" editor="mattshel" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robb"></tags>

# Arquitetura

Aprenda como implementar os padrões de design comuns no Azure.

### Definir ícone/símbolo do Azure

[Baixar o conjunto de ícone/símbolo do Azure][Baixar o conjunto de ícone/símbolo do Azure] para criar materiais de técnicos que descrevem (ou usam) do Azure—coisas como diagramas de arquitetura, materiais de treinamento, apresentações, folhas de dados, infográficos e white papers. Você pode baixar os símbolos nos formatos PPT, Visio ou PNG. Gostaríamos de saber o que você pensa, portanto, há instruções para fornecer comentários no download.

![Definir ícone/símbolo do Azure][Definir ícone/símbolo do Azure]

## Padrões de design

### [Consumidores concorrentes][Consumidores concorrentes]

![Consumidores concorrentes][1]

Permitir que vários consumidores simultâneos processem as mensagens recebidas no mesmo canal de mensagens. Esse padrão permite que um sistema processe várias mensagens simultaneamente para otimizar o resultado, melhorar a escalabilidade e disponibilidade e equilibrar a carga de trabalho.

### [Segregação de responsabilidade da consulta e comando][Segregação de responsabilidade da consulta e comando]

![Segregação de responsabilidade da consulta e comando][2]

Separar as operações que leem dados de operações que atualizam dados usando interfaces separadas. Esse padrão pode maximizar o desempenho, a escalabilidade e a segurança; suporte a evolução do sistema ao longo do tempo por meio de maior flexibilidade; e impedir que os comandos de atualização causem conflitos de mesclagem a nível do domínio.

### [Eleição de líder][Eleição de líder]

![Eleição de líder][3]

Coordene as ações executadas por um conjunto de instâncias de tarefas de colaboração em um aplicativo distribuído elegendo uma instância como líder que assume a responsabilidade pelo gerenciamento das outras instâncias. Esse padrão pode ajudar a garantir que instâncias de tarefa não entrem em conflito umas com as outras, causem a contenção de recursos compartilhados ou inadvertidamente interfira com o trabalho que outras instâncias de tarefa estão realizando.

### [Filtros e tubos][Filtros e tubos]

![Filtros e tubos][4]

Decompor uma tarefa que executa processamento complexo em uma série de elementos discretos que podem ser reutilizados. Esse padrão pode melhorar o desempenho, a escalabilidade e a capacidade de reutilização, permitindo que os elementos de tarefa que executam o processamento para ser implantado e dimensionadas de forma independente.

### [Chave de manobrista][Chave de manobrista]

![Chave de manobrista][5]

Use um símbolo ou a chave que fornece aos clientes acesso restrito a um determinado recurso ou serviço para descarregar operações de transferência de dados do código do aplicativo. Esse padrão é particularmente útil em aplicativos que usam sistemas de armazenamento hospedado na nuvem ou filas e pode diminuir os custos e maximizar o desempenho e a escalabilidade.

### Orientações adicionais

Para obter informações sobre padrões de design mais comuns no Azure, consulte[Padrões de Design em Nuvem][Padrões de Design em Nuvem].

  [Baixar o conjunto de ícone/símbolo do Azure]: http://www.microsoft.com/pt-br/download/details.aspx?id=41937
  [Definir ícone/símbolo do Azure]: ./media/architecture-overview/AzureSymbols.png
  [Consumidores concorrentes]: http://msdn.microsoft.com/pt-br/library/dn568101.aspx
  [1]: ./media/architecture-overview/CompetingConsumers.png
  [Segregação de responsabilidade da consulta e comando]: http://msdn.microsoft.com/pt-br/library/dn568103.aspx
  [2]: ./media/architecture-overview/CQRS.png
  [Eleição de líder]: http://msdn.microsoft.com/pt-br/library/dn568104.aspx
  [3]: ./media/architecture-overview/LeaderElection.png
  [Filtros e tubos]: http://msdn.microsoft.com/pt-br/library/dn568100.aspx
  [4]: ./media/architecture-overview/PipesAndFilters.png
  [Chave de manobrista]: http://msdn.microsoft.com/pt-br/library/dn568102.aspx
  [5]: ./media/architecture-overview/ValetKey.png
  [Padrões de Design em Nuvem]: http://msdn.microsoft.com/pt-br/library/dn568099.aspx
