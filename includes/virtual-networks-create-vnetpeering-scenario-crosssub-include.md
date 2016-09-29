## Emparelhamento entre assinaturas

Nesse cenário, você criará um emparelhamento entre duas VNets pertencentes a assinaturas diferentes.

![cenário entre assinaturas](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

O emparelhamento VNet depende do RBAC (Controle de acesso baseado em função) para autorização. Para um cenário entre assinaturas, primeiro você precisa conceder permissão suficiente para os usuários que criarão o link de emparelhamento:

> [AZURE.NOTE] Se o mesmo usuário tiver privilégio em ambas as assinaturas, pule a etapa 1-4 abaixo.

<!---HONumber=AcomDC_0921_2016-->