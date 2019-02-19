---
title: Tutorial – criar e gerenciar dados exportados do Gerenciamento de Custos do Azure | Microsoft Docs
description: Este artigo mostra como você pode criar e gerenciar dados exportados do Gerenciamento de Custos do Azure para que você possa usá-los em sistemas externos.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/05/2019
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a7c503fba534b72323472fa58b14188bc412003c
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100668"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutorial: Criar e gerenciar dados exportados

Se leu o tutorial de análise de custo, você está familiarizado com o download manual dos dados do Gerenciamento de Custos. No entanto, você pode criar uma tarefa recorrente que exporta automaticamente seus dados de Gerenciamento de Custos para o Armazenamento do Azure com uma frequência diária, semanal ou mensal. Os dados exportados estão no formato CSV e contém todas as informações que são coletadas pelo Gerenciamento de Custos. Em seguida, você pode usar os dados exportados no Armazenamento do Azure com sistemas externos e combiná-los com seus próprios dados personalizados. Você também pode usar os dados exportados em um sistema externo, tal como um painel ou outro sistema financeiro.

Os exemplos neste tutorial orientam você ao exportar os dados do Gerenciamento de Custos e, em seguida, verificar se eles foram exportados com êxito.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são coletados

## <a name="prerequisites"></a>Pré-requisitos
A exportação de dados está disponível para uma variedade de tipos de conta do Azure, incluindo clientes do [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/). Para exibir a lista completa dos tipos de conta compatíveis, confira [Entender os dados do Gerenciamento de Custos](understand-cost-mgt-data.md). Há suporte para as seguintes permissões do Azure por assinatura para exportações de dados por usuário e por grupo:

- Proprietário – pode criar, modificar ou excluir exportações agendadas de uma assinatura.
- Colaborador – pode criar, modificar ou excluir suas próprias exportações agendadas. Pode modificar o nome de exportações agendadas criadas por outras pessoas.
- Leitor – pode agendar exportações às quais tem permissão.

Para contas de Armazenamento do Azure:
- São necessárias permissões de gravação para alterar a conta de armazenamento configurada, independentemente das permissões de exportação.
- Sua conta de Armazenamento do Azure precisa ser configurada para o Armazenamento de Blobs ou de Arquivos.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Criar uma exportação diária

Gerenciamento de Custos + Cobrança &gt; Gerenciamento de Custos &gt; selecione uma assinatura ou um grupo de recursos em uma assinatura &gt; Exportar &gt;  **Adicionar**.

Digite um nome para a exportação e selecione a opção "Exportação diária de custos do mês atual". Clique em **Próximo**.

![Novo exemplo de exportação mostrando o tipo de exportação](./media/tutorial-export-acm-data/basics_exports.png)

Especifique a assinatura da sua conta de Armazenamento do Azure e, depois, selecione sua conta de armazenamento.  Especifique o contêiner de armazenamento e o caminho do diretório para o qual você gostaria de enviar o arquivo de exportação.  Clique em **Próximo**.

![Novo exemplo de exportação mostrando detalhes da conta de armazenamento](./media/tutorial-export-acm-data/storage_exports.png)

Revise os detalhes da exportação e clique em **Criar**.

Sua nova exportação aparece na lista de exportações. Por padrão, as novas exportações estão habilitadas. Se você quiser desabilitar ou excluir uma exportação agendada, clique em qualquer item na lista e, em seguida, clique em **Desabilitar** ou **Excluir**.

Inicialmente, pode levar uma ou duas horas até que a exportação seja executada. No entanto, pode levar até quatro horas para que os dados sejam mostrados em arquivos exportados.

### <a name="export-schedule"></a>Agendamento de exportação

As exportações agendadas são afetadas pela hora e pelo dia da semana de quando a exportação foi inicialmente criada. Quando você cria uma exportação agendada, a exportação é executada na mesma hora do dia para cada ocorrência seguinte de exportação. Por exemplo, você cria uma exportação diária às 13h. A próxima exportação é executada às 13h do dia seguinte. A hora atual afeta todos os outros tipos de exportação da mesma maneira – eles sempre são executados na mesma hora do dia de quando a exportação foi inicialmente criada. Em outro exemplo, você cria uma exportação semanal às 16h na segunda-feira. O próximo relatório é executado às 16h na próxima segunda-feira. *Os dados exportados ficam disponíveis no prazo de quatro horas após a hora de execução.*

Cada exportação cria um arquivo e, portanto, as exportações mais antigas não são substituídas.

Há três tipos de opções de exportação:

**Exportação diária dos custos do mês atual** – a exportação inicial é executada imediatamente. As exportações seguintes são executadas no dia seguinte, na mesma hora da exportação inicial. Os últimos dados são agregados das exportações diárias anteriores.

**Exportação semanal dos custos dos últimos 7 dias** – a exportação inicial é executada imediatamente. As exportações seguintes são executadas no dia da semana e na mesma hora da exportação inicial. Os custos referem-se aos últimos sete dias.

**Personalizado** – permite que você agende exportações semanal e mensalmente com opções de semana e mês atual. *A exportação inicial será executada imediatamente.*

![Guia Nova exportação – Informações Básicas mostrando uma seleção semanal personalizada da semana atual](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Verificar se os dados são coletados

Você pode verificar facilmente se os dados do Gerenciamento de Custos estão sendo coletados e exibir o arquivo CSV exportado usando o Gerenciador de Armazenamento do Azure.

Na lista de exportação, clique no nome da conta de armazenamento. Na página da conta de armazenamento, clique em Abrir no Gerenciador. Se você vir uma caixa de confirmação, clique em **Sim** para abrir o arquivo no Gerenciador de Armazenamento do Azure.

![Página de conta de armazenamento mostrando informações de exemplo e link para abrir no Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

No Gerenciador de Armazenamento, navegue até o contêiner que você deseja abrir e selecione a pasta que corresponde ao mês atual. É mostrada uma lista de arquivos CSV. Selecione um deles e clique em **Abrir**.

![Informações de exemplo mostradas no Gerenciador de Armazenamento](./media/tutorial-export-acm-data/storage-explorer.png)

O arquivo é aberto com o programa ou aplicativo que está configurado para abrir as extensões de arquivo CSV. Aqui está um exemplo no Excel.

![Exemplo de dados CSV exportados mostrados no Excel](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>Acessar dados exportados de outros sistemas

Uma das finalidades de exportar os dados do Gerenciamento de Custos é acessar os dados de sistemas externos. Você pode usar um sistema de painéis ou outro sistema financeiro. Tais sistemas variam amplamente, portanto, mostrar um exemplo não seria conveniente.  No entanto, você pode obter uma introdução sobre como acessar os dados dos aplicativos na [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são coletados

Avance para o próximo tutorial para otimizar e melhorar a eficiência, identificando recursos ociosos e subutilizados.

> [!div class="nextstepaction"]
> [Examinar recomendações de otimização e agir com base nelas](tutorial-acm-opt-recommendations.md)
