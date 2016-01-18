<properties
   pageTitle="Privileged Identity Management do Azure: como iniciar uma análise de segurança"
   description="Saiba como criar uma análise de segurança para identidades com privilégios com a extensão do Privileged Identity Management do Azure."
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

# Privileged Identity Management do Azure: como iniciar uma análise de segurança

## Iniciando uma análise de segurança
No fim, você poderá realizar as revisões de segurança em outros locais no portal do Azure. Este documento aborda as etapas para iniciar uma análise de segurança na interface do **Privileged Identity Management (PIM)**.

Talvez existam usuários que você não reconheça, ou talvez um usuário tenha mudado os trabalhos ou projetos e não exige mais acesso com privilégios em sua nova posição. Para reduzir o risco associado a essas atribuições de função "ultrapassadas", você e outros administradores podem examinar as funções que os usuários receberam ao iniciar uma análise de segurança.

## Caminhos para iniciar uma análise de segurança
> [AZURE.NOTE]Se você ainda não criou um painel PIM no portal do Azure, consulte as etapas em [Introdução ao Privileged Identity Management do Azure](active-directory-privileged-identity-management-getting-started.md)

No painel PIM do Azure, você pode iniciar uma análise por:

- Painel > Análises de segurança > Examinar > botão Examinar
- Painel > Funções > botão Examinar
- Painel > Clique na função a ser examinada na lista de funções > botão Examinar

## Iniciar uma análise de segurança

Quando você clicar no botão **Examinar**, as folhas **Iniciar análise de uma função** e **Selecionar uma função para examinar** aparecerão e o item **Selecionar uma função para examinar** será selecionado.

### Selecionar a função para examinar

1. Selecione a função na lista de funções na folha **Selecionar uma função para examinar**. Você só pode escolher uma função por vez. A folha **Selecionar uma função para examinar** será substituída pela folha **Selecionar revisores**. Você tem duas opções ao selecionar revisores:
  - Eu: use esse recurso se desejar visualizar como as revisões de segurança funcionam sem envolver outros administradores.
  - Autoanálise por membros da função: use esse recurso para que os usuários revisem as próprias atribuições de função por si mesmos.
2. Selecione uma dessas opções para começar a trabalhar com os detalhes da análise. A folha **Alterar padrões** será exibida.

### Examinar por Autoanálise

1. Nomeie a análise inserindo o nome dela no campo **Nome**. É recomendável dar à análise um nome exclusivo que a descreva e que facilite seu acompanhamento.
2. Insira uma data de início para a análise no campo **Data de início**.
3. Insira uma data de término para a análise no campo **Data de término**. Algumas pontos que você deve considerar ao configurar a data de término da análise são:
  - Quantas pessoas estão sendo revisadas?
  - Com que rapidez os usuários poderão adicionar a extensão e concluir a análise?
4. Clique no botão OK na folha **Alterar padrões**. Ela será fechada.
5. Clique no botão OK na folha **Iniciar a análise de uma função**. Ela será fechada. Uma notificação será exibida no menu principal do portal. Atualize o painel clicando no botão **Atualizar** e a análise de segurança aparecerá na seção **Revisões de segurança**.
6. Notifique os indivíduos na função de que eles precisarão adicionar a extensão e depois examinar o próprio acesso administrativo. Veja a próxima etapa.
6. [Examinar acesso administrativo](active-directory-privileged-identity-management-how-to-perform-security-review.md)

### Examinado por Mim

Se você selecionou a opção "Eu" como o revisor, prossiga para a análise de segurança. Para saber mais sobre como concluir a análise, consulte [Privileged Identity Management do Azure: como realizar uma análise de segurança](active-directory-privileged-identity-management-how-to-perform-security-review.md)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Sumário do PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->