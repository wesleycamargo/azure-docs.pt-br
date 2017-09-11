---
title: "A ferramenta - Azure de modelagem de ameaças do Microsoft | Microsoft Docs"
description: "Conheça todos os recursos disponíveis no Threat Modeling Tool"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 621ff305d7e782f85eeaae6c3fb02031673549c6
ms.contentlocale: pt-br
ms.lasthandoff: 08/28/2017

---

# <a name="threat-modeling-tool-feature-overview"></a>Visão geral do recurso Threat Modeling Tool

Estamos felizes por você ter escolhido usar o Threat Modeling Tool para suas necessidades de modelagem de ameaça! Caso não tenha feito isso, visite **[Introdução ao Threat Modeling Tool](./azure-security-threat-modeling-tool-getting-started.md)** para conhecer os conceitos básicos.

> Nossa ferramenta é atualizada com frequência. Portanto, sempre verifique esse guia para ficar a par de nossos recursos e aprimoramentos mais recentes.

Clicar no botão "Criar um Novo Modelo" abre uma página inicial em branco, semelhante à imagem abaixo:

![Página inicial em branco](./media/azure-security-threat-modeling-tool/tmtstart.png)

Usando o modelo de ameaça criado pela nossa equipe no exemplo em **[Introdução](./azure-security-threat-modeling-tool-getting-started.md)**, vamos conferir todos os recursos disponíveis hoje na ferramenta.

![Modelo Básico de Ameaça](./media/azure-security-threat-modeling-tool/basictmt.png)

## <a name="navigation"></a>Navegação

Antes de adentrarmos nos recursos internos, vamos verificar os principais componentes encontrados na ferramenta

### <a name="menu-items"></a>Itens de menu

A experiência deve ser semelhante a de outros produtos da Microsoft. Vamos começar pelos itens de menu de nível superior:

![Itens de menu](./media/azure-security-threat-modeling-tool/menuitems.png)

| Rótulo                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Arquivo** | <ul><li>Abrir, Salvar e Fechar Arquivos</li><li>Entrar/Sair das contas do OneDrive</li><li>Compartilhar Links (Exibir + Editar)</li><li>Exibir Informações do Arquivo</li><li>Aplicar Novo Padrão a Modelos Existentes</li></ul> |
| **Editar** | Desfazer/refazer ações, bem como copiar, colar e excluir |
| **Exibir** | <ul><li>Alternar entre as exibições **Análise** e **Design**</li><li>Abrir/fechar janelas (por exemplo, estênceis, propriedades do elemento e mensagens)</li><li>Redefinir layout para as configurações padrão</li></ul> |
| **Diagrama** | Adicionar/excluir diagramas e navegar pelas "guias" dos diagramas |
| **Relatórios** | Criar relatórios em HTML para compartilhar com outras pessoas |
| **Ajuda** | Guias para ajudar você a usar a ferramenta |

Os ícones são atalhos para os menus de nível superior:

| ícone                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Abrir** | Abre um novo arquivo |
| **Salvar** | Salva o arquivo atual |
| **Design** | Entra no modo de exibição de design, no qual é possível criar modelos |
| **Analise** | Mostra ameaças geradas e suas propriedades |
| **Adicionar Diagrama** | Adiciona novo diagrama (semelhante às novas guias no Excel) |
| **Excluir Diagrama** | Exclui o diagrama atual |
| **Copiar/Recortar/Colar** | Copia/recorta/cola elementos |
| **Desfazer/Refazer** | Desfaz/refaz ações |
| **Ampliar/Reduzir** | Amplia e reduz o diagrama para uma exibição melhor |
| **Comentários** | Abre o Fórum do MSDN |

### <a name="canvas"></a>Tela

O espaço em que você arrasta e solta elementos. Arrastar e soltar é a maneira mais rápida e eficiente de criar modelos. Também é possível clicar com o botão direito do mouse e selecionar no menu, que adiciona versões genéricas dos elementos que você está usando, como mostrado abaixo.

#### <a name="dropping-the-stencil-on-the-canvas"></a>Descartando o estêncil na tela

![Descarte de tela](./media/azure-security-threat-modeling-tool/canvasdrop1.png)

#### <a name="clicking-on-the-stencil"></a>Clicando no estêncil

![Propriedades do Elemento](./media/azure-security-threat-modeling-tool/canvasdrop2.png)

### <a name="stencils"></a>Estênceis

Onde você pode encontrar todos os estênceis disponíveis para uso com base no modelo selecionado. Se você não encontrar os elementos certos, tente usar outro modelo ou modifique um para ajustá-lo às suas necessidades. No geral, você deve ser capaz de encontrar uma combinação de categorias, como as mostradas abaixo:

| Nome do Estêncil                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Processo** | Aplicativos, Plug-ins de Navegador, Threads, Máquinas Virtuais |
| **Interagente Externo** | Provedores de Autenticação, Navegadores, Usuários, Aplicativos Web |
| **Armazenamento de Dados** | Cache, Armazenamento, Arquivos de Configuração, Banco de Dados, Registro |
| **Fluxo de Dados** | Binário, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, Pipe Nomeado, RPC/DCOM, SMB, UDP |
| **Limite de Linha/Borda de Confiança** | Redes Corporativas, Internet, Computador, Área Restrita, Modo de Usuário/Kernel |

### <a name="notesmessages"></a>Observações/Mensagens

| Componente                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Mensagens** | Lógica de ferramenta interna que alerta os usuários sempre que houver um erro, como ausência de fluxos de dados entre elementos |
| **Observações** | Anotações manuais adicionadas ao arquivo pelas equipes de engenharia em todo o processo de design e revisão |

### <a name="element-properties"></a>Propriedades do elemento

Variam de acordo com os elementos selecionados. Com exceção dos Limites de Confiança, todos os outros elementos contêm 3 seleções gerais:

| Propriedade do Elemento                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Nome** | Útil para nomear seus processos, armazenamentos, interagentes e fluxos para serem facilmente reconhecidos |
| **Fora do Escopo** | Se selecionada, o elemento é retirado da matriz de geração de ameaças (não recomendado) |
| **Motivo para Estar Fora do Escopo** | O campo de justificativa para permitir que os usuários saibam por que Fora do Escopo foi selecionada |

As propriedades são alteradas em cada categoria de elemento. Clique em cada elemento para inspecionar as opções disponíveis ou abra o modelo para saber mais. Vamos nos aprofundar nos recursos.

## <a name="welcome-screen"></a>Tela de boas-vindas

A tela de boas-vindas é a primeira coisa que você vê quando você abre o aplicativo.

### <a name="open-a-model"></a>Abrir um Modelo

Passar o mouse sobre o botão "Abrir um Modelo" mostra 2 opções ocultas: "Abrir deste Computador" e "Abrir do OneDrive". A primeira abre a tela Abertura de Arquivos, enquanto a segunda direciona você para o processo de entrada do OneDrive, permitindo que você escolha pastas e arquivos após uma autenticação bem-sucedida.

![Abrir Modelo](./media/azure-security-threat-modeling-tool/openmodel.png)

![Abrir do Computador ou do OneDrive](./media/azure-security-threat-modeling-tool/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Comentários, sugestões e problemas

A seleção dessa opção levará você aos Fóruns do MSDN para Ferramentas SDL. É uma ótima maneira de conferir o que as outras pessoas estão dizendo sobre a ferramenta, incluindo soluções alternativas e novas ideias.

![Comentários](./media/azure-security-threat-modeling-tool/feedback.png)

## <a name="design-view"></a>Modo de exibição de Design

Sempre que abrir ou criar um novo modelo, você será direcionado para o modo de exibição de design.

### <a name="adding-elements"></a>Adicionando elementos

Há 2 modos de adicionar elementos na grade:

- **Arrastar e soltar**: arraste o elemento desejado na grade e, em seguida, use as propriedades do elemento para fornecer informações adicionais.
- **Clicar com o botão direito**: clique com o botão direito do mouse em qualquer lugar na grade e faça a seleção no menu suspenso. Uma representação genérica desse elemento aparecerá na tela.

### <a name="connecting-elements"></a>Conectando elementos

Há 2 maneiras de conectar elementos na ferramenta:

- **Arrastar e soltar**: arraste o fluxo de dados desejado para a grade e conecte ambas as extremidades aos elementos apropriados.
- **Clique + Shift**: clique no primeiro elemento (enviando dados), pressione e segure a tecla Shift e selecione o segundo elemento (recebendo dados). Clique com o botão direito do mouse e selecione "Conectar". Se você estiver usando um fluxo de dados bidirecional, a ordem não é tão importante.

### <a name="properties"></a>Propriedades

Mostra todas as propriedades que podem ser modificadas nos estênceis colocados no diagrama. Para ver as propriedades, basta clicar no estêncil e as informações serão populadas adequadamente. O exemplo abaixo mostra o antes e o depois que o estêncil de um "Banco de Dados" é arrastado para o diagrama:

#### <a name="before"></a>Antes

![Antes](./media/azure-security-threat-modeling-tool/properties1.png)

#### <a name="after"></a>após

![após](./media/azure-security-threat-modeling-tool/properties2.png)

### <a name="messages"></a>Mensagens

Se você criar um modelo de ameaça e se esquecer de conetar os fluxos de dados aos elementos, a janela de mensagem solicitará a ação. Você pode optar por ignorá-la ou seguir as instruções para corrigir o problema. 

![Mensagens](./media/azure-security-threat-modeling-tool/messages.png)

### <a name="notes"></a>Observações

Alternar da guia Mensagens para Observações permite adicionar observações ao seu diagrama a fim de capturar todas as suas ideias

## <a name="analysis-view"></a>Modo de exibição de análise

Quando terminar de criar seu diagrama, alterne para o modo de exibição de análise indo até as seleções do menu superior e escolhendo a lupa próxima à paleta de desenho.

![Modo de Exibição de Análise](./media/azure-security-threat-modeling-tool/analysisview.png)

### <a name="generated-threat-selection"></a>Seleção de ameaça gerada

Ao clicar em uma ameaça, você pode aproveitar três funções exclusivas:

| Recurso                               | Informações      |
| --------------------------------------- | ------------ |
| **Indicador de Leitura** | <p>A ameaça agora está marcada como lida, o que pode ajudar você a acompanhar facilmente os itens pelos quais já passou</p><p>![Indicador de Lido/Não Lido](./media/azure-security-threat-modeling-tool/readmode.png)</p> |
| **Foco de Interação** | <p>A interação no diagrama que pertence a essa ameaça é realçada</p><p>![Foco de Interação](./media/azure-security-threat-modeling-tool/interactionfocus.png)</p> |
| **Propriedades da Ameaça** | <p>As informações adicionais sobre a ameaça são populadas na janela de propriedades da ameaça</p><p>![Propriedades da Ameaça](./media/azure-security-threat-modeling-tool/threatproperties.png)</p> |

### <a name="priority-change"></a>Alteração de prioridade

Alterar o nível de prioridade de cada ameaça gerada também muda suas cores para facilitar a identificação das ameaças de prioridade alta, média e baixa.

![Alteração de Prioridade](./media/azure-security-threat-modeling-tool/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Campos editáveis das propriedades da ameaça

Conforme mostrado na imagem acima, os usuários podem alterar as informações geradas pela ferramenta e também adicionar informações a determinados campos, como a justificativa. Esses campos são gerados pelo modelo, de modo que se você precisar de mais informações para cada ameaça, incentivamos a fazer as modificações.

![Propriedades da Ameaça](./media/azure-security-threat-modeling-tool/threatproperties.png)

## <a name="reports"></a>Relatórios

Quando terminar de alterar as prioridades e atualizar o status de cada ameaça gerada, você poderá salvar o arquivo e/ou imprimir um relatório indo até "Relatório" e "Criar Relatório Completo". Será solicitado que você dê um nome ao relatório e, depois de fazer isso, algo parecido com a imagem abaixo será exibido:

![Relatório](./media/azure-security-threat-modeling-tool/report.png)

## <a name="next-steps"></a>Próximas etapas

Para contribuir com um modelo para a comunidade, visite nossa página no **[GitHub](https://github.com/Microsoft/threat-modeling-templates)**. **[Baixe](https://aka.ms/tmtpreview)**  a ferramenta para começar a usá-la hoje mesmo.

