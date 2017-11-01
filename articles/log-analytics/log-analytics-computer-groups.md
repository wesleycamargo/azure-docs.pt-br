---
title: Grupos de computadores em pesquisas de log do Log Analytics | Microsoft Docs
description: "Os grupos de computadores no Log Analytics permitem analisar pesquisas de log para um conjunto específico de computadores.  Este artigo descreve os diferentes métodos que você pode usar para criar grupos de computadores e como usá-los em uma pesquisa de log."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.openlocfilehash: f27f038e0507270c0bfe200cb8c86622ebac5372
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2017
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Grupos de computadores em pesquisas de log do Log Analytics

Os grupos de computadores no Log Analytics permitem analisar [pesquisas de log](log-analytics-log-search-new.md) para um conjunto específico de computadores.  Cada grupo é preenchido com computadores usando uma consulta que você define ou importando grupos de fontes diferentes.  Quando o grupo é incluído em uma pesquisa de log, os resultados são limitados aos registros que correspondem os computadores do grupo.

## <a name="creating-a-computer-group"></a>Criando um grupo de computadores
Você pode criar um grupo de computadores no Log Analytics usando qualquer um dos métodos na tabela a seguir.  Detalhes sobre cada método são fornecidos nas seções a seguir. 

| Método | Descrição |
|:--- |:--- |
| Pesquisa de log |Criar uma pesquisa de logs que retorna uma lista de computadores. |
| API da Pesquisa de Log |Use a API da Pesquisa de Log para criar um grupo de computadores programaticamente com base nos resultados de uma pesquisa de log. |
| Active Directory |Verifique automaticamente a associação de grupo dos computadores de agente que são membros de um domínio do Active Directory e crie um grupo no Log Analytics para cada grupo de segurança. |
| Gerenciador de Configurações | Importar coleções do System Center Configuration Manager e criar um grupo no Log Analytics cada uma. |
| Windows Server Update Services |Verifique servidores ou clientes de WSUS para direcionar grupos e criar um grupo no Log Analytics para cada um. |

### <a name="log-search"></a>Pesquisa de log
Grupos de computadores criados por meio de uma Pesquisa de Logs contêm todos os computadores retornados por uma consulta que você definir.  Essa consulta é executada sempre que o grupo de computadores é utilizado para que qualquer alteração desde que o grupo foi criado seja refletida.  

Você pode usar qualquer consulta de um grupo de computadores, mas ela deve retornar um conjunto distinto de computadores usando `distinct Computer`.  A seguir está um exemplo de pesquisa típica que você pode usar como um grupo de computadores.

    Heartbeat | where Computer contains "srv" | distinct Computer

A tabela a seguir descreve as propriedades que definem um grupo de computadores.

| Propriedade | Descrição |
|:---|:---|
| Nome de exibição   | Nome da pesquisa para exibir no portal. |
| Categoria       | Categoria para organizar as pesquisas no portal. |
| Consultar          | A consulta para o grupo de computadores. |
| Alias da função | Um alias exclusivo usado para identificar o grupo de computadores em uma consulta. |

Use o procedimento a seguir para criar um grupo de computadores de uma pesquisa de logs no portal do Azure.

2. Abra **Pesquisa de Logs** e, em seguida, clique em **Pesquisas Salvas** na parte superior da tela.
3. Clique em **Adicionar** e forneça valores para cada propriedade para o grupo de computadores.
4. Selecione **Salvar esta consulta como um grupo de computadores** e clique em **OK**.


Use o procedimento a seguir para criar um grupo de computadores de uma pesquisa de logs no portal do OMS.

1. Abra **Pesquisa de Logs** e crie a pesquisa de logs para o grupo de computadores.  
2. Clique no botão **Salvar** na parte superior da tela.
3. Selecione **Sim** para **Salvar esta consulta como um grupo de computadores**.
5. Forneça valores para cada propriedade para o grupo de computadores. 


>[!NOTE]
> Se o seu espaço de trabalho ainda estiver usando a [linguagem de consulta de do Log Analytics herdada](log-analytics-log-search-upgrade.md), use o mesmo procedimento para criar um grupo de computadores, mas você deve usar a sintaxe da linguagem de consulta herdada.


### <a name="log-search-api"></a>API da Pesquisa de Log
Grupos de computadores criados com a API da Pesquisa de Log são iguais a pesquisas criadas com uma Pesquisa de Log.  Para obter detalhes sobre como criar um grupo de computadores usando a API da Pesquisa de Log, consulte [Grupos de computadores na API REST de pesquisa de log do Log Analytics](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory
Ao configurar o Log Analytics para importar associações de grupo do Active Directory, ele analisa a associação de grupo de todos os computadores associados ao domínio com o agente do OMS.  Um grupo de computadores é criado no Log Analytics para cada grupo de segurança no Active Directory, e cada computador é adicionado aos grupos de computadores que correspondem aos grupos de segurança de que são membros.  Essa associação é atualizada continuamente a cada 4 horas.  

Configure o Log Analytics para importar os grupos de segurança do Active Directory de **Configurações avançadas** do Log Analytics no portal do Azure.  Selecione **Grupos de Computadores**, **Active Directory** e **Importe as associações de grupo do Active Directory dos computadores**.  Não é necessária nenhuma configuração.

![Grupos de computadores do Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Quando os grupos são importados, o menu lista o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em qualquer um desses links para retornar os registros de **ComputerGroup** com essas informações.

### <a name="windows-server-update-service"></a>Serviços de Atualização do Windows Server
Ao configurar o Log Analytics para importar associações de grupo do WSUS, ele analisa a associação de grupo de destino de todos os computadores com o agente do OMS.  Se você estiver utilizando o direcionamento do lado do cliente, qualquer computador que estiver conectado ao OMS e fizer parte de qualquer grupo de direcionamento do WSUS terá sua associação de grupo importada para o Log Analytics. Se você estiver usando o direcionamento do lado do servidor, o agente do OMS deverá ser instalado no servidor do WSUS para que as informações de associação do grupo sejam importadas ao OMS.  Essa associação é atualizada continuamente a cada 4 horas. 

Configure o Log Analytics para importar grupos do WSUS das **configurações avançadas** do Log Analytics no portal do Azure.  Selecione **Grupos de Computadores**, **WSUS** e, em seguida, **Importar associações de grupo do WSUS**.  Não é necessária nenhuma configuração.

![Grupos de computadores do WSUS](media/log-analytics-computer-groups/configure-wsus.png)

Quando os grupos são importados, o menu lista o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em qualquer um desses links para retornar os registros de **ComputerGroup** com essas informações.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Quando você configura o Log Analytics para importar associações de coleção do Configuration Manager, ele cria um grupo de computadores para cada coleção.  As informações de associação de coleção são recuperadas a cada 3 horas para manter os grupos de computadores atualizados. 

Antes de importar coleções do Configuration Manager, você deve [conectar o Configuration Manager ao Log Analytics](log-analytics-sccm.md).  Então você pode configurar a importação das **Configurações avançadas** do Log Analytics no portal do Azure.  Selecione **Grupos de Computadores**, **SCCM** e, em seguida, **Importar associações de coleção do Configuration Manager**.  Não é necessária nenhuma configuração.

![Grupos de computadores do SCCM](media/log-analytics-computer-groups/configure-sccm.png)

Quando as coleções tiverem sido importadas, o menu listará o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em qualquer um desses links para retornar os registros de **ComputerGroup** com essas informações.

## <a name="managing-computer-groups"></a>Gerenciando grupos de computadores
Você pode exibir grupos de computadores que foram criados de uma pesquisa de logs ou da API de Pesquisa de Logs das **Configurações avançadas** do Log Analytics no portal do Azure.  Selecione **Grupos de Computadores** e então **Grupos Salvos**.  

Clique no **x** na coluna **Remover** para excluir o grupo de computadores.  Clique no ícone **Exibir membros** para que um grupo execute a pesquisa de log do grupo que retorna seus membros.  Não é possível modificar um grupo de computadores, assim, em vez disso, você precisar excluir e recriar o grupo com as configurações modificadas.

![Grupos de computadores salvados](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Usando um grupo de computadores em uma pesquisa de log
Você pode usar um grupo de Computadores em uma consulta tratando seu alias como uma função, normalmente com a seguinte sintaxe:

  `Table | where Computer in (ComputerGroup)`

Por exemplo, você pode usar o seguinte para retornar registros de UpdateSummary somente para os computadores em um grupo de computadores chamado mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`

>[!NOTE]
> Se o seu espaço de trabalho ainda estiver usando a [linguagem de consulta do Log Analytics herdada](log-analytics-log-search-upgrade.md)>, use a seguinte sintaxe para se referir a um grupo de computadores em uma pesquisa de logs.  Especificar a **Categoria** >é opcional e necessário somente se você tiver grupos de computadores com o mesmo nome em diferentes categorias. 
>
>    `$ComputerGroups[Category: Name]`
>
>Grupos de computadores normalmente são usados com a cláusula **IN** na pesquisa de logs, como no exemplo a seguir:
>
>    `Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]`



## <a name="computer-group-records"></a>Registros de grupo de computadores
Um registro é criado no repositório do OMS para cada associação do grupo do computadores criada no Active Directory ou no WSUS.  Esses registros de desempenho têm um tipo de **ComputerGroup** e têm as propriedades na tabela a seguir.  Registros não são criados para grupos de computadores com base em pesquisas de log.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Computador |Nome do computador membro. |
| Agrupar |Nome do grupo. |
| GroupFullName |Caminho completo para o grupo, incluindo a fonte e o nome da fonte. |
| GroupSource |Fonte da qual o grupo foi coletado. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Nome da origem da qual o grupo foi coletado.  Para o Active Directory, este é o nome de domínio. |
| ManagementGroupName |Nome do grupo de gerenciamento de agentes do SCOM.  Para outros agentes, ele é AOI-\<ID do espaço de trabalho\> |
| TimeGenerated |Data e hora em que o grupo de computadores foi criado ou atualizado. |

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.  

