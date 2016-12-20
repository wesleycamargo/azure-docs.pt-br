---
title: "Como configurar o Glossário de Negócios para a marcação governada | Microsoft Docs"
description: "O artigo de instruções destaca o glossário de negócios no Catálogo de Dados do Azure para definir e usar um vocabulário de negócios comum para marcar ativos de dados registrados."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: b3d63dbe-1ae7-499f-bc46-42124e950cd6
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/21/2016
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8a528f8bccaeb55851ad550aee1da93bf4876730


---
# <a name="how-to-set-up-the-business-glossary-for-governed-tagging"></a>Como configurar o glossário de negócios para Marcação Governada
## <a name="introduction"></a>Introdução
O Catálogo de Dados do Azure fornece recursos para descoberta de fonte de dados, habilitando os usuários a descobrir e entender facilmente as fontes de dados de que precisam para executar análises e tomar decisões. Esses recursos de descoberta têm maior impacto quando os usuários podem localizar e compreender a mais ampla variedade de fontes de dados disponíveis.

Um recurso do Catálogo de Dados que promove maior compreensão dos dados dos ativos é a marcação. A marcação permite que os usuários associem palavras-chave a um ativo ou uma coluna, o que torna mais fácil de descobrir o ativo ao procurar ou navegar, e permite que os usuários entendam mais facilmente o contexto e a intenção do ativo.

No entanto, a marcação às vezes pode causar seus próprios problemas. Alguns exemplos dos problemas que podem ser introduzidos pela marcação são:

1. Usuários usando abreviações de alguns ativos e texto expandido em outros durante a marcação. Essa inconsistência impede a descoberta de ativos, mesmo que o objetivo tenha sido marcar os ativos com a mesma marca.
2. Marcas que significam coisas diferentes em diferentes contextos. Por exemplo, uma marca chamada "Receita" em um conjunto de dados do cliente pode significar a receita por cliente, mas a mesma marca em um conjunto de dados de vendas trimestral pode significar a receita trimestral da empresa.  

Para ajudar a resolver esses e outros desafios semelhantes, o Catálogo de Dados inclui um Glossário de Negócios.

O Glossário de Negócios do Catálogo de Dados permite que às organizações documentar os principais termos de negócios e suas definições para criar um vocabulário comum de negócios. Esse controle proporciona consistência no uso de dados em toda a organização. Uma vez definidos os termos no glossário de negócios, eles podem ser atribuídos a ativos de dados no catálogo, usando a mesma abordagem que a marcação, habilitando assim a *marcação governada*.

> [!NOTE]
> A funcionalidade descrita neste artigo está disponível apenas na Edição Standard do Catálogo de Dados do Azure. A Edição Gratuita não fornece recursos para marcação controlada ou um glossário de negócios.
> 
> 

## <a name="glossary-availability-and-privileges"></a>Disponibilidade de glossário e privilégios
*O glossário de negócios está disponível na Edição Standard do Catálogo de Dados do Azure. A Edição Gratuita do Catálogo de Dados não inclui um glossário.*

O glossário de negócios pode ser acessado por meio da opção "Glossário" no menu de navegação do portal do Catálogo de Dados.  

![Acessando o glossário de negócios](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Os administradores do Catálogo de Dados e membros da função Administradores do Glossário podem criar, editar e excluir os termos do glossário no glossário de negócios. Todos os usuários do Catálogo de Dados podem exibir as definições de termos e podem marcar os ativos com os termos do glossário.

![Adicionar um novo termo de glossário](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Criar termos do glossário
Os administradores do Catálogo de Dados e administradores do Glossário podem criar novos termos do glossário clicando no botão Novo Termo para criar termos de glossário com os seguintes campos:

* Uma definição de negócios para o termo
* Uma descrição que captura o uso pretendido ou regras de negócios para o ativo/coluna
* Uma lista de participantes sabem mais sobre o termo
* O termo pai, que define a hierarquia na qual o termo é organizado

## <a name="glossary-term-hierarchies"></a>Hierarquias de termos do glossário
O glossário de negócios do Catálogo de Dados fornece a capacidade de descrever seu vocabulário de negócios como uma hierarquia de termos. Isso permite que as organizações criem uma classificação de termos que melhor representa a taxonomia de seus negócios.

O nome de um termo deve ser exclusivo em um determinado nível da hierarquia; não são permitidos nomes duplicados. Não há nenhum limite para o número de níveis em uma hierarquia, mas uma hierarquia geralmente é mais facilmente compreendida quando há três níveis ou menos.

O uso de hierarquias no glossário de negócios é opcional. Deixar o campo de termo pai em branco para termos de glossário criará uma lista plana (não hierárquica) dos termos no glossário.  

## <a name="tagging-assets-with-glossary-terms"></a>Marcar ativos com os termos de glossário
Depois que termos glossário tiverem sido definidos no catálogo, a experiência de ativos de marcação é otimizada para pesquisar o glossário à medida que o usuário digita sua marca. O portal do Catálogo de Dados exibe uma lista de termos de glossário correspondentes para o usuário escolher. Se o usuário selecionar um termo da lista, ele será adicionado ao ativo como uma marca (também conhecido como marca de glossário). O usuário também pode optar por criar uma nova marca digitando um termo que não está no glossário (também conhecido como marca de usuário).

![Ativo de dados marcado com uma marca de usuário e duas de glossário](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Marcas de usuário são o único tipo de marca com suporte na Edição Gratuita do Catálogo de Dados.
> 
> 

### <a name="hover-behavior-on-tags"></a>Comportamento ao passar o mouse sobre marcas
No portal do Catálogo de Dados, os dois tipos de marcas são visualmente distintas, com comportamentos diferentes ao passar o mouse sobre elas. Quando o usuário passa o mouse sobre uma marca de usuário, ele pode ver o texto da marca e o usuário que a adicionou. Quando o usuário passa o mouse sobre uma marca de glossário, ele também vê a definição do termo e um link para abrir o glossário de negócios para exibir a definição completa do termo.

### <a name="search-filters-for-tags"></a>Filtros de pesquisa para marcas
Ambas as marcas de glossário e de usuário podem ser pesquisadas e aplicadas como filtros em uma pesquisa.

## <a name="summary"></a>Resumo
O glossário de negócios no Catálogo de Dados do Azure, e a marcação habilitada por ele, permite que os ativos de dados sejam identificados, gerenciados e descobertos de maneira consistente. O glossário de negócios pode promover a aprendizagem do vocabulário de negócios entre usuários de uma organização e dar suporte à captura de metadados significativos, facilitando muito a descoberta e a compreensão dos ativos.

## <a name="see-also"></a>Consulte também
* [Documentação da API REST para operações de glossário de negócios](https://msdn.microsoft.com/library/mt708855.aspx)




<!--HONumber=Nov16_HO3-->


