<properties 
   pageTitle="Retenção de dados da Automação do Azure"
   description="Descreve a política de retenção de dados para a Automação do Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Retenção de dados da Automação do Azure

Quando você exclui um recurso na Automação do Azure, ele é retido por 90 dias para fins de auditoria antes de ser removido permanentemente. Você não pode ver nem usar o recurso durante esse período. Essa política também se aplica a recursos que pertencem a uma conta de automação que é excluída.

A Automação do Azure exclui de forma automática e remove de forma permanente os trabalhos com mais de 90 dias.

A tabela a seguir resume a política de retenção para diferentes recursos.

|Dados|Política|
|:---|:---|
|Contas|Removidas permanentemente 90 dias depois que a conta é excluída por um usuário.|
|Ativos|Removidos permanentemente 90 dias depois que o ativo é excluído por um usuário ou 90 dias depois que a conta que contém o ativo é excluída por um usuário.|
|Módulos|Removidos permanentemente 90 dias depois que o módulo é excluído por um usuário ou 90 dias depois que a conta que contém o módulo é excluída por um usuário.|
|Runbooks|Removidos permanentemente 90 dias depois que o recurso é excluído por um usuário ou 90 dias depois que a conta que contém o recurso é excluída por um usuário.|
|Trabalhos|Excluído e removidos permanentemente 90 dias após a última modificação. Isso pode ocorrer depois que o trabalho é concluído, interrompido ou suspenso.|

A política de retenção se aplica a todos os usuários e, atualmente não, pode ser personalizada.

## Artigos relacionados
- [Fazendo backup da Automação do Azure](https://msdn.microsoft.com/library/dn643635.aspx) 

<!---HONumber=July15_HO3-->