<properties
   pageTitle="Como usar o log de auditoria | Microsoft Azure"
   description="Saiba como usar o log de auditoria na extensão do Privileged Identity Management do Azure. "
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Como usar o log de auditoria no Azure AD Privileged Identity Management

Você pode usar o log de auditoria do PIM (Privileged Identity Management) para ver todas as ativações e atribuições de usuário dentro de um determinado período de tempo.

## Navegar até o log de auditoria
No painel do [portal do Azure](https://portal.azure.com), selecione o aplicativo **Azure AD Privileged Identity Management**. Lá, acesse o log de auditoria ao clicar em **Histórico de auditoria** no painel PIM.

## O gráfico do log de auditoria
Você pode usar o log de auditoria para exibir, em um gráfico de linha, o total de ativações, o número máximo de ativações diárias e a média diária de ativações. Você também pode filtrar os dados por função se houver mais de uma função no histórico de auditoria.

Use os botões **tempo**, **ação** e **função** para classificar o log.

## A lista de logs de auditoria
As colunas na lista de logs de auditoria são:

- **Solicitante**: o usuário que solicitou a ativação ou alteração da função. Se o valor for "Sistema Azure", verifique o log de auditoria do Azure para obter mais informações.
- **Usuário**: o usuário que está ativando ou está atribuído a uma função.
- **Função**: a função atribuída ou ativada pelo usuário.
- **Ação**: as ações tomadas pelo solicitante. Isso pode incluir a atribuição, o cancelamento da atribuição, a ativação ou a desativação.
- **Tempo** - quando a ação aconteceu.
- **Raciocínio** -se qualquer texto foi digitado no campo de motivo durante a ativação, ele será mostrado aqui.
- **Expiração**: relevante apenas para a ativação de funções.

## Filtrar o log de auditoria

Você pode filtrar as informações que aparecem no log de auditoria clicando no botão **Filtrar**. A **folha Atualizar parâmetros do gráfico** será exibida.

Depois de definir os filtros, clique em **Atualizar** para filtrar os dados no log. Se os dados não aparecerem imediatamente, atualize a página.


### Alterar o intervalo de data
Use os botões **Hoje**, **Semana Passada**, **Último Mês** ou **Personalizado** para alterar o intervalo de tempo do log de auditoria.

Quando você escolhe o botão **Personalizado**, terá um campo de data **De** e um campo de data **Até** para especificar o intervalo de datas para o log. Você pode digitar as datas no formato DD/MM/AAAA, ou clique no ícone **calendário** e escolha a data em um calendário.

### Alterar as funções incluídas no log

Marque ou desmarque a caixa de seleção **Função** ao lado de cada função para incluí-la ou excluí-la do log.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->