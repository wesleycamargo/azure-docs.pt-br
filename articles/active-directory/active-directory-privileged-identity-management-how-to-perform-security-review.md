<properties
   pageTitle="Privileged Identity Management do Azure: Como Realizar uma Análise de Segurança"
   description="Aprenda a adicionar funções a identidades com privilégios com a extensão Privileged Identity Management do Azure."
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

# Privileged Identity Management do Azure: Como Realizar uma Análise de Segurança

## Executar Análise de Segurança
É muito fácil analisar o acesso privilegiado uma vez que uma [análise de segurança tiver sido iniciada](active-directory-privileged-identity-management-how-to-start-security-review.md).

## Para os Revisores: Aprovar ou Negar Acesso

### Análise feita por mim
1. No menu principal do PIM, clique em **Analisar acesso administrativo**. Será exibida uma lista das análises de segurança.
2. Selecione o(s) **usuário(s)** na lista para o(s) qual(is) você deseja alterar o acesso. OBSERVAÇÃO: o acesso será realmente alterado. Esse processo é simplesmente criar uma lista de verificação para aqueles cujo acesso à função deseja-se alterar. Quando pelo menos um usuário tiver sido selecionado, os botões **Aprovar acesso** e **Negar acesso** serão habilitados.
3. Clique em **Aprovar acesso** ou **Negar acesso** para os usuários que você selecionou. Uma notificação será exibida no menu principal do portal do Azure e a lista de análise desaparecerá. Feche a folha **Funções de análise do AD do Azure**.

### Autoanálise
1. O usuário receberá um email indicando que deve analisar seu acesso. O email conterá um link para fazer logon no portal do Azure.
2. Depois disso, o usuário pode aprovar ou negar seu próprio acesso clicando nos botões **Aprovar acesso** ou **Negar acesso**. O nome desses usuários desaparecerá da lista.

## Para Gerentes de Análise: Gerenciar Análises de Segurança

## Concluir ou Interromper uma Análise
1. Volte para o painel PIM.
2. Clique na análise de segurança que você deseja concluir na lista **Análises de segurança**. A folha de detalhes da análise de segurança será exibida.
3. Clique em **Parar análise** concluir ou interromper a análise. Isso arquivará a análise e a folha desaparecerá.

## Exportar uma análise
Você pode exportar uma análise para uso com o Excel ou outro programa capaz de usar arquivos CSV.

1. Volte para o painel PIM.
2. Clique na seção **Análises de segurança** do painel. A folha **Análises de segurança** será exibida.
3. Clique na análise de segurança que você deseja exportar. A folha de detalhes da análise de segurança será exibida.
4. Clique no botão **Exportar**. O download de um arquivo CSV será iniciado.

## Excluir uma Análise

> AZURE.WARNING [Você não receberá nenhum aviso antes da exclusão, portanto certifique-se que você realmente *deseja* excluir a análise.]

1. Volte para o painel PIM.
2. Clique na seção **Análises de segurança** do painel. A folha **Análises de segurança** será exibida.
3. Clique na análise de segurança que você deseja excluir. A folha de detalhes da análise de segurança será exibida.
4. Clique no botão **Excluir**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0107_2016-->