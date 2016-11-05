---
title: Adicionar o conector do Dynamics CRM Online aos seus Aplicativos Lógicos | Microsoft Docs
description: Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O Provedor de Conexão do Dynamics CRM Online fornece uma API para trabalhar com as entidades no Dynamics CRM Online.
services: logic-apps
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/15/2016
ms.author: mandia

---
# Introdução ao conector do Dynamics CRM Online
Conecte-se ao Dynamics CRM Online para criar um novo registro, atualizar um item e muito mais. Com o CRM Online, você pode:

* Compilar seu fluxo de negócios baseado nos dados que obtém do CRM Online.
* Usar ações que excluem um registro, obtêm entidades e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um item é atualizado no CRM, você pode enviar um email usando o Office 365.

Este tópico mostra como usar o conector do Dynamics CRM Online em um aplicativo lógico e também lista os gatilhos e as ações.

> [!NOTE]
> Esta versão do artigo se aplica à disponibilidade de Aplicativos Lógicos em geral (GA).
> 
> 

Para saber mais sobre os Aplicativos Lógicos, consulte [O que são aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) e [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conector o Dynamics CRM Online
Antes do aplicativo lógico poder acessar qualquer serviço, primeiro crie uma *conexão* com o serviço. Uma conexão fornece uma conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para conectar o Dynamics, primeiro é necessário uma *conexão* do Dynamics CRM Online. Para criar uma conexão, insira as credenciais que você normalmente usa para acessar o serviço ao qual deseja conectar-se. Assim, com o Dynamics, insira as credenciais de sua conta do Dynamics CRM Online para criar a conexão.

### Criar a conexão
> [!INCLUDE [Etapas para criar uma conexão com o Provedor de Conexão do Dynamics CRM Online](../../includes/connectors-create-api-crmonline.md)]
> 
> 

## Usar um gatilho
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. Gatilhos "sondam" o serviço no intervalo e na frequência desejados. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. No aplicativo lógico, digite "dynamics" para obter uma lista de gatilhos:
   
    ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Selecione **Dynamics CRM Online - Quando um registro é criado**. Se já existir uma conexão, selecione uma organização e a entidade na lista suspensa.
   
    ![](./media/connectors-create-api-crmonline/select-organization.png)
   
    Se você for solicitado a entrar, insira os detalhes do logon para criar a conexão. [Criar a conexão](connectors-create-api-crmonline.md#create-the-connection) neste tópico lista as etapas.
   
   > [!NOTE]
   > Neste exemplo, o aplicativo lógico é executado quando um registro é criado. Para ver os resultados deste gatilho, adicione outra ação que envia um email. Por exemplo, adicione a ação *Enviar um email* do Office 365 que envia um email para você quando um registro é adicionado.
   > 
   > 
3. Selecione o botão **Editar** e defina os valores **Frequência** e **Intervalo**. Por exemplo, se você quiser que o gatilho faça uma sondagem a cada 15 minutos, defina a **Frequência** para **Minuto** e **Intervalo** para **15**.
   
    ![](./media/connectors-create-api-crmonline/edit-properties.png)
4. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## Usar uma ação
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Selecione o sinal de mais. Você tem várias opções: **Adicionar uma ação**, **Adicionar uma condição** ou uma das opções **Mais**.
   
    ![](./media/connectors-create-api-crmonline/add-action.png)
2. Escolha **Adicionar uma ação**.
3. Na caixa de texto, digite "dynamics" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-crmonline/dynamics-actions.png)
4. Em nosso exemplo, escolha **Dynamics CRM Online - Atualizar um registro**. Se já existir uma conexão, escolha **Nome da Organização**, **Nome da Entidade** e outras propriedades:
   
    ![](./media/connectors-create-api-crmonline/sample-action.png)
   
    Se as informações de conexão forem solicitadas, insira os detalhes para criar a conexão. [Criar a conexão](connectors-create-api-crmonline.md#create-the-connection) neste tópico descreve estas propriedades.
   
   > [!NOTE]
   > Neste exemplo, atualizamos um registro existente no CRM Online. Você pode usar a saída de outro gatilho para atualizar o registro. Por exemplo, adicione o gatilho *Quando um item existente é modificado* do SharePoint. Em seguida, adicione a ação *Atualizar um registro* do CRM Online que usa os campos do SharePoint para atualizar o registro existente no CRM Online.
   > 
   > 
5. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## Detalhes técnicos
## Gatilhos
| Gatilho | Descrição |
| --- | --- |
| [Quando um registro é criado](connectors-create-api-crmonline.md#when-a-record-is-created) |Dispara um fluxo quando um objeto é criado no CRM. |
| [Quando um registro é atualizado](connectors-create-api-crmonline.md#when-a-record-is-updated) |Dispara um fluxo quando um objeto é modificado no CRM. |
| [Quando um registro é excluído](connectors-create-api-crmonline.md#when-a-record-is-deleted) |Dispara um fluxo quando um objeto é excluído no CRM. |

## Ações
| Ação | Descrição |
| --- | --- |
| [Listar registros](connectors-create-api-crmonline.md#list-records) |Esta operação obtém os registros de uma entidade. |
| [Criar um novo registro](connectors-create-api-crmonline.md#create-a-new-record) |Esta operação cria um novo registro de uma entidade. |
| [Obter registro](connectors-create-api-crmonline.md#get-record) |Esta operação obtém o registro específico de uma entidade. |
| [Excluir um registro](connectors-create-api-crmonline.md#delete-a-record) |Essa operação exclui um registro de uma coleção de entidades. |
| [Atualizar um registro](connectors-create-api-crmonline.md#update-a-record) |Esta operação atualiza um registro existente de uma entidade. |

### Detalhes de gatilho e ação
Nesta seção, consulte os detalhes específicos sobre cada gatilho e ação, incluindo todas as propriedades de entrada obrigatórias ou opcionais, assim como toda saída correspondente associada ao conector.

#### Quando um registro é criado
Dispara um fluxo quando um objeto é criado no CRM.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do CRM, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a obter (padrão = 256) |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir as entradas retornadas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da Saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### Quando um registro é atualizado
Dispara um fluxo quando um objeto é modificado no CRM.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do CRM, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a obter (padrão = 256) |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir as entradas retornadas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da Saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### Quando um registro é excluído
Dispara um fluxo quando um objeto é excluído no CRM.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do CRM, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a obter (padrão = 256) |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir as entradas retornadas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da Saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### Listar registros
Esta operação obtém os registros de uma entidade.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do CRM, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a obter (padrão = 256) |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir as entradas retornadas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da Saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### Criar um novo registro
Esta operação cria um novo registro de uma entidade.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do CRM, como Contoso |
| table* |Nome da entidade |O nome da entidade |

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da Saída
Nenhuma.

#### Obter registro
Esta operação obtém o registro específico de uma entidade.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do CRM, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| id* |Identificador do item |Especificar o Identificador do registro |

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da Saída
Nenhuma.

#### Excluir um registro
Essa operação exclui um registro de uma coleção de entidades.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do CRM, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| id* |Identificador do item |Especificar o identificador do registro |

Um asterisco (*) significa que a propriedade obrigatória.

#### Atualizar um registro
Esta operação atualiza um registro existente de uma entidade.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do CRM, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| id* |Identificador do registro |Especificar o identificador do registro |

Um asterisco (*) significa que a propriedade obrigatória.

##### Detalhes da Saída
Nenhuma.

## Respostas HTTP
As ações e os gatilhos podem retornar um ou mais dos seguintes códigos de status HTTP:

| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceita |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido. |
| padrão |Falha na Operação. |

## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0817_2016-->