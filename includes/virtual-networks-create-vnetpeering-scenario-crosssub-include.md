## Emparelhamento entre assinaturas

Nesse cenário, você criará um emparelhamento entre duas VNets pertencentes a assinaturas diferentes.

![cenário entre assinaturas](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

O emparelhamento VNet depende do RBAC (Controle de acesso baseado em função) para autorização. Para o cenário entre assinaturas, primeiro você precisa conceder permissão suficiente para os usuários que criarão o link emparelhamento: OBSERVAÇÃO: se o mesmo usuário tiver o privilégio para ambas as assinaturas, ignore as etapas 1 a 4 abaixo.

<!---HONumber=AcomDC_0803_2016-->