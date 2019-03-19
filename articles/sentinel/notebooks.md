---
title: Recursos de procura usando blocos de anotações na visualização do Azure Sentinel | Microsoft Docs
description: Este artigo descreve como usar blocos de anotações com os recursos de procura Sentinel do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 63ce2be847017ed7e80fe5e573d5553311f6af2f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107671"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>Usar blocos de anotações de anomalias apresentar

> [!IMPORTANT]
> Sentinela do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinela Azure aproveita o poder da interativos blocos de anotações do Jupyter para fornecer informações e ações para investigar ou hunt anomalias de segurança em seu ambiente. Azure Sentinela vem pré-carregado com blocos de anotações que foram desenvolvidos por analistas de segurança da Microsoft. Cada bloco de anotações é criado com um fluxo de trabalho independente para um caso de uso específicos de uso. Visualizações são incluídas em cada bloco de anotações para exploração de dados mais rápida e procura de ameaças. Você personalizar os blocos de anotações internos para atender às suas necessidades, criar novos notebooks do zero ou importar blocos de anotações do Azure Sentinel' comunidade do GitHub. Notebooks Jupyter são importados como um projeto na página de blocos de anotações do Azure – Isso permite que o usuário acessar todos os blocos de anotações do Azure Sentinel em um só lugar. Blocos de anotações podem ser executados com um clique de um botão ou podem ser configurados pelo usuário para corresponder ao seu ambiente.

A experiência totalmente integrada permite que os blocos de anotações executar no armazenamento e computação em nuvem sem sobrecarga de manutenção subjacente. A capacidade de aproveitar o ecossistema de blocos de anotações do Jupyter existente permite modelos de origem multidão, sem compartilhar os dados do cliente. 


Cada bloco de anotações está hospedado no Azure Notebooks (atualmente em versão prévia), um ambiente de desenvolvimento interativo na nuvem do Azure. Blocos de anotações são sempre acessível, sempre disponível em qualquer navegador, em qualquer lugar do mundo.  O Azure Sentinel' notebooks internos para investigação e procura são clonados para um projeto que pertencem a você, e o que você pode modificar e adaptar ao seu ambiente. Estes são alguns dos mais populares notebooks internos:

- **Alerta de investigação e procura**: Este bloco de anotações interativo permite triagem rápida de classes diferentes de alertas, recuperando atividade relacionada e enriquecer o alerta com a atividade associada e os dados do qual o alerta foi gerado.

- **Host do ponto de extremidade guiado procura**: Permite que você procurar por sinais de violação de segurança, analisando as atividades relevantes de segurança associadas a um host do ponto de extremidade.  

- **Office entrar guiado caçar**: Permite que você vá procurar entradas suspeitas por visualizando as localizações geográficas de logs suspeitos, bem como para exibir padrões de entrada incomuns derivados de dados do Office 365.

## <a name="run-a-notebook"></a>Executar um bloco de anotações
O exemplo a seguir, podemos executar o bloco de anotações interno a ser pesquisado profunda aprofundar anomalias de local para ver se qualquer pessoa em um local inesperado está fazendo algo na sua rede.

1. No portal do Azure Sentinel, clique em **Notebooks** e, em seguida, selecione qualquer um dos notebooks internos.
  
   ![Selecione o bloco de anotações](./media/notebooks/select-notebook.png)

3. Clique em **importação** para clonar o repositório do GitHub em seu projeto de blocos de anotações do Azure.
   ![Bloco de anotações de importação](./media/notebooks/import1.png)
4. Cada notebook orienta você pelas etapas para realizar uma busca ou a investigação. Modelos, bibliotecas e outras dependências e a configuração de conectividade para Azure Sentinel é importado automaticamente para permitir a execução de um clique. Todo o código e bibliotecas necessárias para executar um bloco de anotações são pré-carregados. Você pode começar imediatamente a execução de um bloco de anotações no seu espaço de trabalho do Log Analytics sem nenhuma configuração.

   ![Importar do repositório](./media/notebooks/import2.png)

5. Explore, modifique e execute todos os notebooks de exemplo fornecidos. Eles podem ser usados como blocos de construção para muitos cenários diferentes.

   ![Selecione o bloco de anotações](./media/notebooks/import3.png)



## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a executar uma investigação de procura usando blocos de anotações no Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- [Proativamente de busca de ameaças](hunting.md)
- [Usar indicadores para salvar informações interessantes durante a procura](bookmarks.md)