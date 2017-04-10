Você pode usar uma assinatura do Azure para a qual você não seja administrador ou proprietário, como uma assinatura da empresa. Se esse for o caso, você deve verificar que os itens a seguir foram obtidos para seguir as etapas neste artigo:

* Acesso do colaborador. Para entrar no Azure, você precisa de pelo menos acesso de Colaborador para o grupo de recursos do Azure. Esse grupo de recursos é usado para criar um cluster HDInsight e outros recursos do Azure.
* Registro do provedor. Alguém com no mínimo acesso de Colaborador à assinatura do Azure tem que registrar anteriormente o provedor para o recurso que você está usando. O registro do provedor acontece quando um usuário com acesso de Colaborador à assinatura cria um recurso nela pela primeira vez. Isso também pode ser feito sem criar um recurso, [registrando um provedor através do REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Para saber mais sobre como trabalhar com o gerenciamento de acesso, confira os seguintes artigos:

* [Introdução ao gerenciamento de acesso no portal do Azure](../articles/active-directory/role-based-access-control-what-is.md)
* [Usar as atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../articles/active-directory/role-based-access-control-configure.md)
