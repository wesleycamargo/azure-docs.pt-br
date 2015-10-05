<properties
   pageTitle="Privileged Identity Management do Azure: Como Usar o Log de Auditoria"
   description="Saiba como usar o log de auditoria na extensão do Privileged Identity Management do Azure. "
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Privileged Identity Management do Azure: Como Usar o Log de Auditoria

## Usar o Log de Auditoria
Você pode usar o log de áudio do Privileged Identity Management para ver todas as ativações e atribuições de usuário dentro de um determinado período de tempo.

## Navegação até o Log de Auditoria
Você pode acessar o log de auditoria clicando em Histórico de auditoria, no painel PIM.

## O Gráfico do Log de Auditoria
Você pode usar o log de auditoria para exibir, em um gráfico de linha, o total de ativações, o máx. de ativações diárias e a média diária de ativações. Você também pode filtrar os dados por função se houver mais de uma função no histórico de auditoria.

Classifique por hora, ação ou função usando os botões **tempo**, **ação** ou **função**.

## A Lista de Logs de Auditoria
As colunas na lista de logs de auditoria são:

- **Solicitante** - quem solicitou a ativação de função.
- **Usuário** - o usuário da ativação de função.
- **Função** - a função atribuída ao usuário.
- **Ação** - as ações realizadas com a função/usuário.
- **Tempo** - quando a ação aconteceu.
- **Raciocínio** -se qualquer texto foi digitado no campo de motivo durante a ativação, ele será mostrado aqui.
- **Expiração** - se o ano de expiração é 9999, o usuário tem a função permanentemente.

## Filtrar o Log de Auditoria

Você também pode filtrar as informações que aparecem no log de auditoria clicando no botão **Filtrar**. A **folha Atualizar parâmetros do gráfico** será exibida.

### Alterar o intervalo de data
Alterar o intervalo de tempo do log de auditoria, selecionando um dos botões **Hoje**, **Semana Passada**, **Último Mês**, ou **Personalizado**. Quando você escolhe o botão **Personalizado**, você terá um campo de data **De** e um campo de data **Para** que usará para especificar o intervalo de datas desejado para o log. Você pode digitar as datas no formato DD/MM/AAAA, ou clique no ícone **calendário** e escolha a data em um calendário.

### Alterar as funções incluídas no log

Marque ou desmarque a caixa de seleção **Função** ao lado de cada função que você quer que seja incluída ou excluída do log.

Uma vez que você tenha todos os filtros para o conjunto de log de auditoria, clique em atualizar para filtrar os dados no log. Se os dados não aparecem imediatamente, clique no botão **Atualizar**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Sept15_HO4-->