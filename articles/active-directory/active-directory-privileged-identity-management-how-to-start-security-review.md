<properties
   pageTitle="Privileged Identity Management do Azure: como iniciar uma análise de segurança"
   description="Saiba como criar uma análise de segurança para identidades com privilégios com a extensão do Privileged Identity Management do Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management: como iniciar uma revisão de segurança

## Iniciando uma revisão de segurança
No fim, você poderá realizar as revisões de segurança em outros locais no portal do Azure. Este documento aborda as etapas para iniciar uma análise de segurança na interface do **Privileged Identity Management (PIM)**.

Talvez existam usuários que você não reconheça, ou talvez um usuário tenha mudado de trabalho ou de projeto e não precise mais de acesso privilegiado em seu novo cargo. Para reduzir o risco associado a essas atribuições de função "ultrapassadas", você e outros administradores podem examinar as funções que os usuários receberam ao iniciar uma análise de segurança.

## Caminhos para iniciar uma revisão de segurança
> [AZURE.NOTE] Se você ainda não criou um painel PIM no portal do Azure, consulte as etapas em [Introdução ao Privileged Identity Management do Azure](active-directory-privileged-identity-management-getting-started.md)

No painel PIM do Azure, você pode iniciar um modo de exibição seguindo qualquer um deste caminhos:

- Painel > Análises de segurança > Examinar > botão Examinar
- Painel > Funções > botão Examinar
- Painel > Clique na função a ser examinada na lista de funções > botão Examinar

## Iniciar uma revisão de segurança:

Quando você clica no botão **Revisar**, as folhas **Iniciar revisão de uma função** e **Selecionar a função a ser revisada** serão exibidas e o item **Selecionar uma função a ser revisada** será selecionado.

### Selecionar a função a ser revisada

1. Selecione a função na lista de funções na folha **Selecionar uma função para examinar**. Você só pode escolher uma função por vez. A folha **Selecionar uma função para examinar** será substituída pela folha **Selecionar revisores**. Você tem duas opções ao selecionar revisores:
  - Eu: use esse recurso se desejar visualizar como as revisões de segurança funcionam sem envolver outros administradores.
  - Autoanálise de membros da função - use esse recurso para fazer os usuários revisarem suas próprias atribuições de função.
2. Selecione uma dessas opções para começar a trabalhar com os detalhes da análise. A folha **Alterar padrões** será exibida.

### Revisar por conta própria

1. Nomeie a análise inserindo o nome dela no campo **Nome**. É recomendável dar à análise um nome exclusivo que a descreva e que facilite seu acompanhamento.
2. Insira uma data de início para a análise no campo **Data de início**.
3. Insira uma data de término para a análise no campo **Data de término**. Algumas pontos que você deve considerar ao configurar a data de término da análise são:
  - Quantas pessoas estão sendo revisadas?
  - Com que rapidez os usuários poderão adicionar a extensão e concluir a análise?
4. Clique no botão **OK** na folha **Alterar padrões**. Ela será fechada.
5. Clique no botão **OK** na folha **Iniciar a revisão de uma função**. Ela será fechada. Uma notificação será exibida no menu principal do portal do Azure. Atualize o painel clicando no botão **Atualizar** e a revisão de segurança aparecerá na seção **Revisões de segurança**.
6. Notifique os indivíduos da função de que eles precisarão adicionar a extensão e depois [revisar o próprio acesso administrativo](active-directory-privileged-identity-management-how-to-perform-security-review.md).  

### Examinado por Mim

Se você selecionou a opção "Eu" como o revisor, prossiga para a análise de segurança. Para saber mais sobre como concluir a análise, consulte [Privileged Identity Management do Azure: como realizar uma análise de segurança](active-directory-privileged-identity-management-how-to-perform-security-review.md)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Sumário do PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->