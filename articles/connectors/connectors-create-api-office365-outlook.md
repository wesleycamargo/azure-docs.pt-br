---
title: "Adicionar o conector Outlook do Office 365 em seus Aplicativos Lógicos | Microsoft Docs"
description: "Crie aplicativos lógicos com o conector do Office 365 para permitir a interação com o Office 365. Por exemplo: criação, edição e atualização de itens de calendário e contatos."
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2f6cc2c-bba2-493a-b0ba-841785462a80
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 92c0892128655141f29380890c31451e62ca8853


---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Introdução ao conector do Outlook do Office 365
O conector do Outlook do Office 365 permite interação com o Outlook no Office 365. Use esse conector para criar, editar e atualizar contatos e itens de calendário, bem como para obter, enviar e responder a emails.

Com o Outlook do Office 365, você pode:

* Compilar o fluxo de trabalho usando os recursos de email e calendário no Office 365. 
* Usar gatilhos para iniciar o fluxo de trabalho quando houver um novo email, quando um item de calendário for atualizado e muito mais.
* Usar ações que enviem um email, criem um novo evento de calendário e muito mais. Por exemplo, quando houver um novo objeto em Salesforce (um gatilho), envie um email ao seu Outlook do Office 365 (uma ação). 

Este tópico mostra como usar o conector do Outlook do Office 365 em um aplicativo lógico, além de listar os gatilhos e as ações.

> [!NOTE]
> Esta versão do artigo se aplica à disponibilidade de Aplicativos Lógicos em geral (GA).
> 
> 

Para saber mais sobre os Aplicativos Lógicos, consulte [O que são aplicativos lógicos](../logic-apps/logic-apps-what-are-logic-apps.md) e [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-office-365"></a>Conectar ao Office 365
Antes do aplicativo lógico poder acessar qualquer serviço, primeiro crie uma *conexão* com o serviço. Uma conexão fornece uma conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao Outlook do Office 365, primeiramente é necessária uma *conexão* do Office 365. Para criar uma conexão, insira as credenciais que você normalmente usa para acessar o serviço ao qual deseja conectar-se. Assim, com o Outlook do Office 365, insira as credenciais de sua conta do Office 365 para criar a conexão.

## <a name="create-the-connection"></a>Criar a conexão
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Usar um gatilho
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. Gatilhos "sondam" o serviço no intervalo e na frequência desejados. [Saiba mais sobre gatilhos](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. No aplicativo lógico, digite "office 365" para obter uma lista de gatilhos:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Escolha **Outlook do Office 365 – quando um evento futuro for iniciado em breve**. Se já existir uma conexão, escolha um calendário na lista suspensa.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Se você for solicitado a entrar, insira os detalhes do logon para criar a conexão. [Criar a conexão](connectors-create-api-office365-outlook.md#create-the-connection) neste tópico lista as etapas. 
   
   > [!NOTE]
   > Neste exemplo, o aplicativo lógico é executado quando um evento de calendário é atualizado. Para ver os resultados desse gatilho, adicione outra ação que envie uma mensagem de texto para você. Por exemplo, adicione a ação *Enviar mensagem* do Twilio que avisa você, por texto, quando o evento de calendário estiver iniciando em 15 minutos. 
   > 
   > 
3. Selecione o botão **Editar** e defina os valores **Frequência** e **Intervalo**. Por exemplo, se você quiser que o gatilho faça uma sondagem a cada 15 minutos, defina a **Frequência** como **Minuto** e **Intervalo** como **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="use-an-action"></a>Usar uma ação
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Selecione o sinal de mais. Você tem várias opções: **adicionar uma ação**, **adicionar uma condição** ou uma das opções **Mais**.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Escolha **Adicionar uma ação**.
3. Na caixa de texto, digite "office 365" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. Em nosso exemplo, escolha **Outlook do Office 365 – Criar contato**. Se já existir uma conexão, escolha **ID da Pasta**, **Nome Fornecido** e outras propriedades:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Se as informações de conexão forem solicitadas, insira os detalhes para criar a conexão. [Criar a conexão](connectors-create-api-office365-outlook.md#create-the-connection) neste tópico descreve estas propriedades. 
   
   > [!NOTE]
   > Neste exemplo, criamos um novo contato no Outlook do Office 365. Você pode usar a saída de outro gatilho para criar o contato. Por exemplo, adicione o gatilho *Quando um objeto é criado* do SalesForce. Em seguida, adicione a ação *Criar contato* do Outlook do Office 365 que usa os campos do SalesForce para criar o novo contato no Office 365. 
   > 
   > 
5. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="technical-details"></a>Detalhes técnicos
Veja abaixo os detalhes sobre os gatilhos, as ações e as respostas que essa conexão permite:

## <a name="office-365-triggers"></a>Gatilhos do Office 365
| Gatilho | Descrição |
| --- | --- |
| [Quando um evento futuro for iniciado em breve](connectors-create-api-office365-outlook.md#when-an-upcoming-event-is-starting-soon) |Esta operação dispara um fluxo quando um evento futuro do calendário está iniciando. |
| [Quando um novo email chegar](connectors-create-api-office365-outlook.md#when-a-new-email-arrives) |Esta operação dispara um fluxo quando um novo email chega |
| [Quando um novo evento é criado](connectors-create-api-office365-outlook.md#when-a-new-event-is-created) |Esta operação dispara um fluxo quando um novo evento é criado em um calendário. |
| [Quando um evento é modificado](connectors-create-api-office365-outlook.md#when-an-event-is-modified) |Esta operação dispara um fluxo quando um evento é modificado em um calendário. |

## <a name="office-365-actions"></a>Ações do Office 365
| Ação | Descrição |
| --- | --- |
| [Obter emails](connectors-create-api-office365-outlook.md#get-emails) |Esta operação obtém emails de uma pasta. |
| [Enviar um email](connectors-create-api-office365-outlook.md#send-an-email) |Esta operação envia uma mensagem de email. |
| [Excluir email](connectors-create-api-office365-outlook.md#delete-email) |Esta operação exclui um email por ID. |
| [Marcar como lido](connectors-create-api-office365-outlook.md#mark-as-read) |Esta operação marca um email como lido. |
| [Responder ao email](connectors-create-api-office365-outlook.md#reply-to-email) |Esta operação responde a um email. |
| [Obter anexo](connectors-create-api-office365-outlook.md#get-attachment) |Esta operação obtém um anexo de email por ID. |
| [Enviar email com opções](connectors-create-api-office365-outlook.md#send-email-with-options) |Esta operação envia um email com várias opções e aguarda que o destinatário responda com uma das opções. |
| [Enviar email de aprovação](connectors-create-api-office365-outlook.md#send-approval-email) |Esta operação envia um email de aprovação e aguarda uma resposta do destinatário. |
| [Obter calendários](connectors-create-api-office365-outlook.md#get-calendars) |Esta operação lista os calendários disponíveis. |
| [Obter eventos](connectors-create-api-office365-outlook.md#get-events) |Esta operação obtém os eventos de um calendário. |
| [Criar evento](connectors-create-api-office365-outlook.md#create-event) |Esta operação cria um novo evento em um calendário. |
| [Obter evento](connectors-create-api-office365-outlook.md#get-event) |Esta operação obtém um evento de um calendário. |
| [Excluir evento](connectors-create-api-office365-outlook.md#delete-event) |Esta operação exclui um evento em um calendário. |
| [Atualizar evento](connectors-create-api-office365-outlook.md#update-event) |Esta operação atualiza um evento em um calendário. |
| [Obter pastas de contatos](connectors-create-api-office365-outlook.md#get-contact-folders) |Esta operação lista pastas de contatos disponíveis. |
| [Obter contatos](connectors-create-api-office365-outlook.md#get-contacts) |Esta operação obtém contatos de uma pasta de contatos. |
| [Criar contato](connectors-create-api-office365-outlook.md#create-contact) |Esta operação cria um novo contato em uma pasta de contatos. |
| [Obter contato](connectors-create-api-office365-outlook.md#get-contact) |Esta operação obtém um contato específico de uma pasta de contatos. |
| [Excluir contato](connectors-create-api-office365-outlook.md#delete-contact) |Esta operação exclui um contato de uma pasta de contatos. |
| [Atualizar contato](connectors-create-api-office365-outlook.md#update-contact) |Esta operação atualiza um contato em uma pasta de contatos. |

### <a name="trigger-and-action-details"></a>Detalhes de gatilho e ação
Nesta seção, consulte os detalhes específicos sobre cada gatilho e ação, incluindo todas as propriedades de entrada obrigatórias ou opcionais, assim como toda saída correspondente associada ao conector.

#### <a name="when-an-upcoming-event-is-starting-soon"></a>Quando um evento futuro for iniciado em breve
Esta operação dispara um fluxo quando um evento futuro do calendário está iniciando. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID de Calendário |Identificador exclusivo do calendário |
| lookAheadTimeInMinutes |Busca antecipada |Tempo (em minutos) para procurar eventos futuros |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
CalendarItemsList: a ista de itens de calendário

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| value |array |Lista de itens de calendário |

#### <a name="get-emails"></a>Obter emails
Esta operação obtém emails de uma pasta. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderPath |Caminho da pasta |Caminho da pasta para recuperar emails (padrão: “Caixa de Entrada”) |
| top |Top |Número de emails a serem recuperados (padrão: 10) |
| fetchOnlyUnread |Buscar Apenas Mensagens Não Lidas |Recuperar somente emails não lidos? |
| includeAttachments |Incluir Anexos |Se for definido como true, os anexos também serão recuperados junto com o email |
| searchQuery |Consulta de Pesquisa |Consulta de pesquisa para filtrar emails |
| skip |Skip |Número de emails a serem ignorados (padrão: 0) |
| skipToken |Skip Token |Ignorar o token para buscar nova página |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
ReceiveMessage: Receber Mensagem de Email

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| Da |string |Da |
| Para |string |Para |
| Assunto |string |Assunto |
| Corpo |string |Corpo |
| Importância |string |Importância |
| HasAttachment |booleano |Tem Anexo |
| ID |string |ID da Mensagem |
| IsRead |booleano |Lido |
| DateTimeReceived |string |Data/hora do recebimento |
| Anexos |array |Anexos |
| Co |string |Especifique endereços de email separados por ponto-e-vírgula, como someone@contoso.com |
| Cco |string |Especifique endereços de email separados por ponto-e-vírgula, como someone@contoso.com |
| IsHtml |booleano |É Html |

#### <a name="send-an-email"></a>Enviar um email
Esta operação envia uma mensagem de email. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| emailMessage* |Email |Email |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhum.

#### <a name="delete-email"></a>Excluir email
Esta operação exclui um email por ID. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| messageId* |ID da Mensagem |ID do email a ser excluído |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhum.

#### <a name="mark-as-read"></a>Marcar como lido
Esta operação marca um email como lido. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| messageId* |ID da Mensagem |ID do email a ser marcado como lido |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhum.

#### <a name="reply-to-email"></a>Responder ao email
Esta operação responde a um email. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| messageId* |ID da Mensagem |ID do email a ser respondido |
| comment* |Comentário |Responder comentário |
| replyAll |Responder a Todos |Responder a todos os destinatários |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhum.

#### <a name="get-attachment"></a>Obter anexo
Esta operação obtém um anexo de email por ID. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| messageId* |ID da Mensagem |ID do email |
| attachmentId* |ID do Anexo |Id do anexo a ser baixado |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhum.

#### <a name="when-a-new-email-arrives"></a>Quando um novo email chegar
Esta operação dispara um fluxo quando um novo email chega.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderPath |Caminho da pasta |Pasta de email a ser recuperada (padrão: caixa de entrada) |
| para |Para |Endereços de email do destinatário |
| de |Da |Do endereço |
| importância |Importância |Importância do email (Alta, Normal, Baixa) (padrão: Normal) |
| fetchOnlyWithAttachment |Tem Anexos |Recuperar somente emails com anexo |
| includeAttachments |Incluir Anexos |Incluir anexos |
| subjectFilter |Filtro de Assunto |Cadeia de caracteres a ser procurada no assunto |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
TriggerBatchResponse[ReceiveMessage]

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### <a name="send-email-with-options"></a>Enviar email com opções
Esta operação envia um email com várias opções e aguarda que o destinatário responda com uma das opções. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| optionsEmailSubscription* |Solicitação de assinatura para email de opções |Solicitação de assinatura para email de opções |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
SubscriptionResponse: Modelo para Assinatura de Email de Aprovação

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| ID |string |ID da assinatura |
| recurso |string |Recurso da solicitação de assinatura |
| notificationType |string |Tipo de notificação |
| notificationUrl |string |URL de notificação |

#### <a name="send-approval-email"></a>Enviar email de aprovação
Esta operação envia um email de aprovação e aguarda uma resposta do destinatário. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| approvalEmailSubscription* |Solicitação de assinatura para email de aprovação |Solicitação de assinatura para email de aprovação |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
SubscriptionResponse: Modelo para Assinatura de Email de Aprovação

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| ID |string |ID da assinatura |
| recurso |string |Recurso da solicitação de assinatura |
| notificationType |string |Tipo de notificação |
| notificationUrl |string |URL de notificação |

#### <a name="get-calendars"></a>Obter calendários
Esta operação lista os calendários disponíveis. 

Não existem parâmetros para esta chamada.

##### <a name="output-details"></a>Detalhes da Saída
TablesList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### <a name="get-events"></a>Obter eventos
Esta operação obtém os eventos de um calendário. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID de Calendário |Selecione um calendário |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir as entradas retornadas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a serem recuperadas (padrão = 256) |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
CalendarEventList: a lista de itens de calendário

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| value |array |Lista de itens de calendário |

#### <a name="create-event"></a>Criar evento
Esta operação cria um novo evento em um calendário. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID de Calendário |Selecione um calendário |
| item* |Item |Evento a ser criado |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
CalendarEvent: classe do modelo de evento de calendário específica do conector.

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| ID |string |O identificador exclusivo do Evento. |
| Attendees |array |Lista de participantes do evento. |
| Corpo |não definido |O corpo da mensagem associado ao evento. |
| BodyPreview |string |A visualização da mensagem associada ao evento. |
| Categorias |array |As categorias associadas ao evento. |
| ChangeKey |string |Identifica a versão do objeto de evento. Sempre que o evento for alterado, ChangeKey também é alterada. |
| DateTimeCreated |string |A data e a hora em que o evento foi criado. |
| DateTimeLastModified |string |A data e hora em que o evento foi modificado pela última vez. |
| End |string |A hora de término do evento. |
| EndTimeZone |string |Especifica o fuso horário da hora de término da reunião. Esse valor deve ser definido tal como no Windows (exemplo: 'Hora Padrão do Pacífico'). |
| HasAttachments |booleano |Definida como verdadeira se o evento tiver anexos. |
| Importância |string |A importância do evento: Baixa, Normal ou Alta. |
| IsAllDay |booleano |Definida como verdadeira se o evento durar o dia todo. |
| IsCancelled |booleano |Definida como verdadeira se o evento tiver sido cancelado. |
| IsOrganizer |booleano |Definida como verdadeira se o remetente da mensagem também for o organizador. |
| Local |não definido |O local do evento. |
| Organizer |não definido |O organizador do evento. |
| Recorrência |não definido |O padrão de recorrência para o evento. |
| Reminder |inteiro |Tempo em minutos antes do início do evento para lembrança. |
| ResponseRequested |booleano |Definida como verdadeira se o remetente quiser uma resposta quando o evento for aceito ou recusado. |
| ResponseStatus |não definido |Indica o tipo de resposta enviado em resposta a uma mensagem de evento. |
| SeriesMasterId |string |Identificador exclusivo para o tipo de evento Mestre da Série. |
| ShowAs |string |Mostra como livre ou ocupada. |
| Iniciar |string |A hora de início do evento. |
| StartTimeZone |string |Especifica o fuso horário da hora de início da reunião. Esse valor deve ser definido tal como no Windows (exemplo: "Hora Padrão do Pacífico"). |
| Assunto |string |Assunto do evento. |
| Tipo |string |O tipo de evento: Única Instância, Ocorrência, Exceção ou Mestre da Série. |
| WebLink |string |A visualização da mensagem associada ao evento. |

#### <a name="get-event"></a>Obter evento
Esta operação obtém um evento de um calendário. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID de Calendário |Selecione um calendário |
| id* |Id do item |Selecione um evento |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
CalendarEvent: classe do modelo de evento de calendário específica do conector.

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| ID |string |O identificador exclusivo do Evento. |
| Attendees |array |Lista de participantes do evento. |
| Corpo |não definido |O corpo da mensagem associado ao evento. |
| BodyPreview |string |A visualização da mensagem associada ao evento. |
| Categorias |array |As categorias associadas ao evento. |
| ChangeKey |string |Identifica a versão do objeto de evento. Sempre que o evento for alterado, ChangeKey também é alterada. |
| DateTimeCreated |string |A data e a hora em que o evento foi criado. |
| DateTimeLastModified |string |A data e hora em que o evento foi modificado pela última vez. |
| End |string |A hora de término do evento. |
| EndTimeZone |string |Especifica o fuso horário da hora de término da reunião. Esse valor deve ser definido tal como no Windows (exemplo: 'Hora Padrão do Pacífico'). |
| HasAttachments |booleano |Definida como verdadeira se o evento tiver anexos. |
| Importância |string |A importância do evento: Baixa, Normal ou Alta. |
| IsAllDay |booleano |Definida como verdadeira se o evento durar o dia todo. |
| IsCancelled |booleano |Definida como verdadeira se o evento tiver sido cancelado. |
| IsOrganizer |booleano |Definida como verdadeira se o remetente da mensagem também for o organizador. |
| Local |não definido |O local do evento. |
| Organizer |não definido |O organizador do evento. |
| Recorrência |não definido |O padrão de recorrência para o evento. |
| Reminder |inteiro |Tempo em minutos antes do início do evento para lembrança. |
| ResponseRequested |booleano |Definida como verdadeira se o remetente quiser uma resposta quando o evento for aceito ou recusado. |
| ResponseStatus |não definido |Indica o tipo de resposta enviado em resposta a uma mensagem de evento. |
| SeriesMasterId |string |Identificador exclusivo para o tipo de evento Mestre da Série. |
| ShowAs |string |Mostra como livre ou ocupada. |
| Iniciar |string |A hora de início do evento. |
| StartTimeZone |string |Especifica o fuso horário da hora de início da reunião. Esse valor deve ser definido tal como no Windows (exemplo: "Hora Padrão do Pacífico"). |
| Assunto |string |Assunto do evento. |
| Tipo |string |O tipo de evento: Única Instância, Ocorrência, Exceção ou Mestre da Série. |
| WebLink |string |A visualização da mensagem associada ao evento. |

#### <a name="delete-event"></a>Excluir evento
Esta operação exclui um evento em um calendário. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID de Calendário |Selecione um calendário |
| id* |ID |Selecione um evento |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhum.

#### <a name="update-event"></a>Atualizar evento
Esta operação atualiza um evento em um calendário. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID de Calendário |Selecione um calendário |
| id* |ID |Selecione um evento |
| item* |Item |Eventos a serem atualizados |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
CalendarEvent: classe do modelo de evento de calendário específica do conector.

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| ID |string |O identificador exclusivo do Evento. |
| Attendees |array |Lista de participantes do evento. |
| Corpo |não definido |O corpo da mensagem associado ao evento. |
| BodyPreview |string |A visualização da mensagem associada ao evento. |
| Categorias |array |As categorias associadas ao evento. |
| ChangeKey |string |Identifica a versão do objeto de evento. Sempre que o evento for alterado, ChangeKey também é alterada. |
| DateTimeCreated |string |A data e a hora em que o evento foi criado. |
| DateTimeLastModified |string |A data e hora em que o evento foi modificado pela última vez. |
| End |string |A hora de término do evento. |
| EndTimeZone |string |Especifica o fuso horário da hora de término da reunião. Esse valor deve ser definido tal como no Windows (exemplo: 'Hora Padrão do Pacífico'). |
| HasAttachments |booleano |Definida como verdadeira se o evento tiver anexos. |
| Importância |string |A importância do evento: Baixa, Normal ou Alta. |
| IsAllDay |booleano |Definida como verdadeira se o evento durar o dia todo. |
| IsCancelled |booleano |Definida como verdadeira se o evento tiver sido cancelado. |
| IsOrganizer |booleano |Definida como verdadeira se o remetente da mensagem também for o organizador. |
| Local |não definido |O local do evento. |
| Organizer |não definido |O organizador do evento. |
| Recorrência |não definido |O padrão de recorrência para o evento. |
| Reminder |inteiro |Tempo em minutos antes do início do evento para lembrança. |
| ResponseRequested |booleano |Definida como verdadeira se o remetente quiser uma resposta quando o evento for aceito ou recusado. |
| ResponseStatus |não definido |Indica o tipo de resposta enviado em resposta a uma mensagem de evento. |
| SeriesMasterId |string |Identificador exclusivo para o tipo de evento Mestre da Série. |
| ShowAs |string |Mostra como livre ou ocupada. |
| Iniciar |string |A hora de início do evento. |
| StartTimeZone |string |Especifica o fuso horário da hora de início da reunião. Esse valor deve ser definido tal como no Windows (exemplo: "Hora Padrão do Pacífico"). |
| Assunto |string |Assunto do evento. |
| Tipo |string |O tipo de evento: Única Instância, Ocorrência, Exceção ou Mestre da Série. |
| WebLink |string |A visualização da mensagem associada ao evento. |

#### <a name="when-a-new-event-is-created"></a>Quando um novo evento é criado
Esta operação dispara um fluxo quando um novo evento é criado em um calendário. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID de Calendário |Selecione um calendário |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir as entradas retornadas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a serem recuperadas (padrão = 256) |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
CalendarItemsList: a ista de itens de calendário

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| value |array |Lista de itens de calendário |

#### <a name="when-an-event-is-modified"></a>Quando um evento é modificado
Esta operação dispara um fluxo quando um evento é modificado em um calendário. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID de Calendário |Selecione um calendário |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir as entradas retornadas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a serem recuperadas (padrão = 256) |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
CalendarItemsList: a ista de itens de calendário

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| value |array |Lista de itens de calendário |

#### <a name="get-contact-folders"></a>Obter pastas de contatos
Esta operação lista pastas de contatos disponíveis. 

Não existem parâmetros para esta chamada.

##### <a name="output-details"></a>Detalhes da Saída
TablesList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### <a name="get-contacts"></a>Obter contatos
Esta operação obtém contatos de uma pasta de contatos. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID da pasta |Identificador exclusivo da pasta Contatos a ser recuperada |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir as entradas retornadas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |Ignorar contagem |Número de entradas a serem ignoradas (padrão = 0) |
| $top |Obter Contagem Máxima |Número máximo de entradas a serem recuperadas (padrão = 256) |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
ContactList: a lista de contatos

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| value |array |Lista de contatos |

#### <a name="create-contact"></a>Criar contato
Esta operação cria um novo contato em uma pasta de contatos. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID da pasta |Selecione uma pasta de contatos |
| item* |Item |Contato a ser criado |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Contact: contato

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| ID |string |O identificador exclusivo do contato. |
| ParentFolderId |string |A ID da pasta pai do contato |
| Birthday |string |A data de nascimento do contato. |
| FileAs |string |O nome sob o qual o contato é arquivado. |
| DisplayName |string |O nome de exibição do contato. |
| GivenName |string |O nome fornecido do contato. |
| Initials |string |As iniciais do contato. |
| MiddleName |string |O nome do meio do contato. |
| NickName |string |O apelido do contato. |
| Sobrenome |string |O sobrenome do contato. |
| Title |string |A posição do contato. |
| Generation |string |A geração do contato. |
| EmailAddresses |array |Os endereços de email do contato. |
| ImAddresses |array |Os endereços de IM (mensagens instantâneas) do contato. |
| JobTitle |string |O cargo do contato. |
| CompanyName |string |O nome da empresa do contato. |
| Departamento |string |O departamento do contato. |
| OfficeLocation |string |O local do escritório do contato. |
| Profession |string |A profissão do contato. |
| BusinessHomePage |string |A home page comercial do contato. |
| AssistantName |string |O nome do assistente do contato. |
| Gerente |string |O nome do gerente do contato. |
| HomePhones |array |Os números de telefone residencial do contato. |
| BusinessPhones |array |Os números de telefone comercial do contato |
| MobilePhone1 |string |O número do celular do contato. |
| HomeAddress |não definido |O endereço residencial do contato. |
| BusinessAddress |não definido |O endereço comercial do contato. |
| OtherAddress |não definido |Outros endereços do contato. |
| YomiCompanyName |string |O nome japonês fonético da empresa do contato. |
| YomiGivenName |string |O nome japonês fonético (primeiro nome) do contato. |
| YomiSurname |string |O sobrenome japonês fonético (último nome) do contato |
| Categorias |array |As categorias associadas ao contato. |
| ChangeKey |string |Identifica a versão do objeto de evento |
| DateTimeCreated |string |A hora em que o contato foi criado. |
| DateTimeLastModified |string |A hora em que o contato foi modificado. |

#### <a name="get-contact"></a>Obter contato
Esta operação obtém um contato específico de uma pasta de contatos. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID da pasta |Selecione uma pasta de contatos |
| id* |Id do item |Identificador exclusivo de um contato a ser recuperado |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Contact: contato

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| ID |string |O identificador exclusivo do contato. |
| ParentFolderId |string |A ID da pasta pai do contato |
| Birthday |string |A data de nascimento do contato. |
| FileAs |string |O nome sob o qual o contato é arquivado. |
| DisplayName |string |O nome de exibição do contato. |
| GivenName |string |O nome fornecido do contato. |
| Initials |string |As iniciais do contato. |
| MiddleName |string |O nome do meio do contato. |
| NickName |string |O apelido do contato. |
| Sobrenome |string |O sobrenome do contato. |
| Title |string |A posição do contato. |
| Generation |string |A geração do contato. |
| EmailAddresses |array |Os endereços de email do contato. |
| ImAddresses |array |Os endereços de IM (mensagens instantâneas) do contato. |
| JobTitle |string |O cargo do contato. |
| CompanyName |string |O nome da empresa do contato. |
| Departamento |string |O departamento do contato. |
| OfficeLocation |string |O local do escritório do contato. |
| Profession |string |A profissão do contato. |
| BusinessHomePage |string |A home page comercial do contato. |
| AssistantName |string |O nome do assistente do contato. |
| Gerente |string |O nome do gerente do contato. |
| HomePhones |array |Os números de telefone residencial do contato. |
| BusinessPhones |array |Os números de telefone comercial do contato |
| MobilePhone1 |string |O número do celular do contato. |
| HomeAddress |não definido |O endereço residencial do contato. |
| BusinessAddress |não definido |O endereço comercial do contato. |
| OtherAddress |não definido |Outros endereços do contato. |
| YomiCompanyName |string |O nome japonês fonético da empresa do contato. |
| YomiGivenName |string |O nome japonês fonético (primeiro nome) do contato. |
| YomiSurname |string |O sobrenome japonês fonético (último nome) do contato |
| Categorias |array |As categorias associadas ao contato. |
| ChangeKey |string |Identifica a versão do objeto de evento |
| DateTimeCreated |string |A hora em que o contato foi criado. |
| DateTimeLastModified |string |A hora em que o contato foi modificado. |

#### <a name="delete-contact"></a>Excluir contato
Esta operação exclui um contato de uma pasta de contatos. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID da pasta |Selecione uma pasta de contatos |
| id* |ID |Identificador exclusivo do contato a excluir |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhum.

#### <a name="update-contact"></a>Atualizar contato
Esta operação atualiza um contato em uma pasta de contatos. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| table* |ID da pasta |Selecione uma pasta de contatos |
| id* |ID |Identificador exclusivo do contato a atualizar |
| item* |Item |Item de contato a ser atualizado |

Um asterisco (*) significa que a propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Contact: contato

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| ID |string |O identificador exclusivo do contato. |
| ParentFolderId |string |A ID da pasta pai do contato |
| Birthday |string |A data de nascimento do contato. |
| FileAs |string |O nome sob o qual o contato é arquivado. |
| DisplayName |string |O nome de exibição do contato. |
| GivenName |string |O nome fornecido do contato. |
| Initials |string |As iniciais do contato. |
| MiddleName |string |O nome do meio do contato. |
| NickName |string |O apelido do contato. |
| Sobrenome |string |O sobrenome do contato. |
| Title |string |A posição do contato. |
| Generation |string |A geração do contato. |
| EmailAddresses |array |Os endereços de email do contato. |
| ImAddresses |array |Os endereços de IM (mensagens instantâneas) do contato. |
| JobTitle |string |O cargo do contato. |
| CompanyName |string |O nome da empresa do contato. |
| Departamento |string |O departamento do contato. |
| OfficeLocation |string |O local do escritório do contato. |
| Profession |string |A profissão do contato. |
| BusinessHomePage |string |A home page comercial do contato. |
| AssistantName |string |O nome do assistente do contato. |
| Gerente |string |O nome do gerente do contato. |
| HomePhones |array |Os números de telefone residencial do contato. |
| BusinessPhones |array |Os números de telefone comercial do contato |
| MobilePhone1 |string |O número do celular do contato. |
| HomeAddress |não definido |O endereço residencial do contato. |
| BusinessAddress |não definido |O endereço comercial do contato. |
| OtherAddress |não definido |Outros endereços do contato. |
| YomiCompanyName |string |O nome japonês fonético da empresa do contato. |
| YomiGivenName |string |O nome japonês fonético (primeiro nome) do contato. |
| YomiSurname |string |O sobrenome japonês fonético (último nome) do contato |
| Categorias |array |As categorias associadas ao contato. |
| ChangeKey |string |Identifica a versão do objeto de evento |
| DateTimeCreated |string |A hora em que o contato foi criado. |
| DateTimeLastModified |string |A hora em que o contato foi modificado. |

## <a name="http-responses"></a>Respostas HTTP
As ações e os gatilhos acima podem retornar um ou mais dos seguintes códigos de status HTTP: 

| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceita |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).




<!--HONumber=Jan17_HO3-->


