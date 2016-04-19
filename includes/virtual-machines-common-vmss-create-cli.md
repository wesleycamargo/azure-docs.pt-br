Conjuntos de Escala de Máquina Virtual permitem gerenciar diversas Máquinas Virtuais como um conjunto. Em um alto nível, conjuntos de escala têm os seguintes prós e contras:

Prós:

1. Alta disponibilidade. Cada conjunto de escala coloca suas VMs em um Conjunto de Disponibilidade com 5 Domínios de Falha (FDs) e 5 Domínios de Atualização (UDs) para garantir a disponibilidade (para obter mais informações sobre UDs e de FDs, consulte [disponibilidade da VM](../articles/virtual-machines/virtual-machines-linux-manage-availability.md)).
2. Fácil integração com o Balanceador de Carga do Azure e o Gateway de Aplicativo.
3. Fácil integração com Autoescala do Azure.
4. Simplifica implantação, gerenciamento e limpeza de VMs.
5. Suporte a tipos comuns do Windows e Linux, bem como imagens personalizadas.

Contras:

1. Não é possível anexar discos de dados para instâncias de VM em um conjunto de escala. Em vez disso, deve-se usar Armazenamento de Blobs, Arquivos do Azure, Tabelas do Azure ou outras soluções de armazenamento.

## Criação rápida usando a CLI do Azure

Se você ainda não fez isso, você pode obter uma [avaliação gratuita de assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/) e [CLI do Azure](../articles/xplat-cli-install.md) [conectado à sua conta do Azure](../articles/xplat-cli-connect.md). Depois que você fizer isso, você pode executar os seguintes comandos para criar rapidamente um conjunto de escala da VM:

```bash
# make sure we are in resource manager mode 
azure config mode arm

# quick-create a VM scale set
#
# generic syntax:
# azure vmss quick-create -n VMSS-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4$$w0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

Se desejar personalizar o local ou a urn da imagem, veja os comandos `azure location list` e `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

Depois que esse comando foi retornado, o conjunto de escala terá sido criado. Esse conjunto de escala terá um balanceador de carga com regras NAT de mapeamento de porta 50.000+ i no balanceador de carga para a porta 22 na VM i. Portanto, depois que podemos descobrir o FQDN do balanceador de carga, poderemos fazer SSH em nossas VMs:

```bash
# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g nsgvmsrg | grep nsgvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated with it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LB-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g nsgvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g nsgvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can ssh on port 50,000+i to ssh into VM i (where i is 0-indexed)
#
# example to ssh into VM "0":
ssh -p 50000 $FQDN
```

## Próximas etapas

Para obter informações gerais, confira a [página de aterrissagem principal para conjuntos de escala VM](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Para obter documentação, confira a [página de documentação principal para conjuntos de escala VM](https://azure.microsoft.com/documentation/services/virtual-machines-scale-sets/).

Para modelos do Azure Resource Manager de exemplo usando conjuntos de escala de VM, procure por "vmss" no [repositório github de Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

<!---HONumber=AcomDC_0406_2016-->