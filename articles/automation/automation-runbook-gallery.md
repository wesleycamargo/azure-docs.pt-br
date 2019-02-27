---
title: Galerias de runbook e de módulos para a Automação do Azure
description: Os runbooks e os módulos da Microsoft e da comunidade estão disponíveis para instalação e uso em seu ambiente da Automação do Azure.  Este artigo descreve como você pode acessar esses recursos e contribuir com seus runbooks para a Galeria.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 68eb3f3e5c568bb518251aca2a4e76932ce2eee5
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416200"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerias de runbook e de módulos para a Automação do Azure

Em vez de criar seus próprios runbooks e módulos na Automação do Azure, você pode acessar cenários que já foram desenvolvidos pela Microsoft e pela comunidade.

Você pode obter os runbooks na [Galeria do Runbook](#runbooks-in-runbook-gallery) e os módulos na [Galeria do PowerShell](#modules-in-powerShell-gallery).  Você também pode contribuir com a comunidade compartilhando os cenários que desenvolver. Para saber mais sobre isso, consulte [Adicionando um runbook à galeria](automation-runbook-gallery.md#adding-a-runbook-to-the-runbook-gallery)

## <a name="runbooks-in-runbook-gallery"></a>Runbooks na Galeria de Runbook

A [Galeria do Runbook](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) fornece vários runbooks da Microsoft e da comunidade que você pode importar para a Automação do Azure. Para usar algum deles, baixe um runbook da galeria, que fica hospedada no [Script Center da TechNet](https://gallery.technet.microsoft.com/scriptcenter/site/upload), ou importe runbooks diretamente da galeria no portal do Azure.

Somente é possível importar diretamente da Galeria de Runbook usando o Portal do Azure. Não é possível executar essa função usando o Windows PowerShell.

> [!NOTE]
> Você deverá validar o conteúdo de todos os runbooks obtidos na Galeria de Runbooks e tomar muito cuidado ao instalá-los e executá-los em um ambiente de produção.

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Para importar um runbook da Galeria de Runbook com o portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. Em **Automação de Processo**, clique em **Galeria de Runbooks**
3. Localize o item da galeria desejado e selecione-o para exibir seus detalhes. À esquerda, você pode inserir parâmetros de pesquisa adicionais para o tipo e o fornecedor.

   ![Procurar na galeria](media/automation-runbook-gallery/browse-gallery.png)

4. Clique em **Exibir projeto de origem** para exibir o item no [Script Center do TechNet](https://gallery.technet.microsoft.com/).
5. Para importar um item, clique nele para exibir seus detalhes e clique no botão **Importar** .

   ![Botão Importar](media/automation-runbook-gallery/gallery-item-detail.png)

6. Opcionalmente, altere o nome do runbook e clique em **OK** para importar o runbook.
7. O runbook aparece na guia **Runbooks** da Conta de Automação.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Adicionando um runbook à galeria de runbook

A Microsoft incentiva você a adicionar à Galeria de Runbook os runbooks que, em sua opinião, seriam úteis para outros clientes.  Você pode adicionar um runbook [carregando-o no Script Center](https://gallery.technet.microsoft.com/site/upload) levando em consideração os detalhes a seguir.

* Você deve especificar *Windows Azure* para a **Categoria** e *Automação* para a **Subcategoria** do runbook ser exibido no assistente.  
* O carregamento deve ser um arquivo único `.ps1` ou `.graphrunbook`.  Se o runbook exigir módulos, runbooks filhos ou ativos, você deverá listar esses itens na descrição do envio e na seção de comentários do runbook.  Se você tiver um cenário que exija vários runbooks, carregue cada um separadamente e liste os nomes dos runbooks relacionados em cada uma das descrições. Use as mesmas marcas, de forma que elas sejam mostradas na mesma categoria. Um usuário terá de ler a descrição para saber quais outros runbooks serão necessários para que o cenário funcione.
* Adicione a marca "GraphicalPS" se estiver publicando um **Runbook gráfico** (e não um Fluxo de Trabalho Gráfico).
* Insira um snippet de código do PowerShell ou do Fluxo de Trabalho do PowerShell na descrição usando o ícone **Inserir seção de código**.
* O Resumo do upload é exibido nos resultados da Galeria de Runbooks e, portanto, você deve fornecer informações detalhadas que ajudam um usuário a identificar a funcionalidade do runbook.
* Você deve atribuir ao carregamento de uma a três das Marcas a seguir.  O runbook é listado no assistente nas categorias correspondentes às suas marcas.  As marcas que não estiverem nessa lista serão ignoradas pelo assistente. Se você não especificar nenhuma marca correspondente, o runbook será listado na categoria Outros.
  
  * Backup
  * Gerenciamento de Capacidade
  * Controle de Alterações
  * Conformidade
  * Ambientes de Desenvolvimento/Teste
  * Recuperação de desastre
  * Monitoramento
  * Aplicação de patch
  * Provisionamento
  * Correção
  * Gerenciamento do ciclo de vida de VMs

* A automação atualiza a Galeria uma vez por hora e, portanto, você não verá suas contribuições imediatamente.

## <a name="modules-in-powershell-gallery"></a>Módulos na Galeria do PowerShell

Os módulos do PowerShell contêm cmdlets que você pode usar em seus runbooks, e os módulos existentes que podem ser instalados na Automação do Azure estão disponíveis na [Galeria do PowerShell](https://www.powershellgallery.com). Você pode iniciar a galeria no portal do Azure e instalá-los diretamente na Automação do Azure. Você também pode baixá-los e instalá-los manualmente.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Para importar um módulo da Galeria de Módulos de Automação com o portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Módulos** em **Recursos Compartilhados** para abrir a lista de módulos.
3. Clique em **Procurar na Galeria** na parte superior da página.

   ![Galeria de módulos](media/automation-runbook-gallery/modules-blade.png)

4. Na página **Procurar na Galeria**, é possível pesquisar pelos seguintes campos:

   * Nome do Módulo
   * Marcas
   * Autor
   * Nome do recurso do DSC/cmdlet

5. Localize o módulo em que você está interessado e selecione-o para exibir seus detalhes.  

   Quando você analisar um módulo específico, poderá exibir mais informações. Essas informações incluem um link para a Galeria do PowerShell, para as dependências necessárias e para todos os cmdlets ou recursos de DSC que o módulo contém.

   ![Detalhes do módulo do PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Para instalar o módulo diretamente na Automação do Azure, clique no botão **Importar** .
7. Ao clicar no botão Importar, no painel **Importar**, você verá o nome do módulo que está prestes a importar. Se todas as dependências estiverem instaladas, o botão **OK** será ativado. Em caso de dependências ausentes, será necessário importar tais dependências antes de importar o módulo.
8. Na página **Importar**, clique em **OK** para importar o módulo. Enquanto a Automação do Azure importa um módulo para sua conta, ela extrai os metadados sobre o módulo e os cmdlets. A ação acima pode demorar alguns minutos, pois cada atividade precisa ser extraída.
9.  Você recebe uma notificação inicial informando que o módulo está sendo implantado e outra notificação quando o processo é concluído.
10. Depois que o módulo é importado, você pode ver as atividades disponíveis. É possível usar os recursos em seus runbooks e Desired State Configuration.

> [!NOTE]
> Os módulos que suportam apenas o núcleo do PowerShell não são suportados na Automação do Azure e não podem ser importados no portal do Azure ou implantados diretamente da Galeria do PowerShell.

## <a name="python-runbooks"></a>Runbooks Python

Runbooks Python estão disponíveis na [Galeria do Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Você pode contribuir com runbooks do Python na Galeria do Script Center. Quando você fizer, certifique-se de que você adicione a marca **Python** ao carregar sua contribuição.

## <a name="requesting-a-runbook-or-module"></a>Solicitando um runbook ou um módulo

Você pode enviar solicitações para a [Voz do Usuário](https://feedback.azure.com/forums/246290-azure-automation/).  Se precisar de ajuda para escrever um runbook ou se tiver dúvidas em relação ao PowerShell, poste uma pergunta em nosso [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Próximas etapas

* Para se familiarizar com os runbooks, confira [Gerenciar runbook na Automação do Azure](manage-runbooks.md)
* Para entender as diferenças entre o PowerShell e o os fluxos de trabalho do PowerShell com runbooks, confira [Aprendendo sobre o fluxo de trabalho do PowerShell](automation-powershell-workflow.md)