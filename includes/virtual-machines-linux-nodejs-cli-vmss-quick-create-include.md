## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não fez isso, obtenha uma [Avaliação gratuita da assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/) e a [CLI do Azure 1.0](../articles/xplat-cli-install.md) [conectada à sua conta do Azure](../articles/xplat-cli-connect.md). Verifique se a CLI do Azure está no modo Resource Manager da seguinte forma:

```azurecli
azure config mode arm
```

## <a name="create-the-scale-set"></a>Criar o conjunto de dimensionamento

Agora, crie seu conjunto de dimensionamento usando o comando `azure vmss quick-create`. O exemplo a seguir cria um conjunto de dimensionamento do Linux chamado `myVMSS` com cinco instâncias de VM no grupo de recursos chamado `myResourceGroup`:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:16.04-LTS:latest
```

O exemplo a seguir cria um conjunto de dimensionamento do Windows com a mesma configuração:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest
```

Se desejar personalizar o local ou o URN de imagem, confira os comandos `azure location list` e `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

Esse comando retorna assim que o conjunto de dimensionamento conclui a implantação. Nesse momento, o conjunto de dimensionamento tem um balanceador de carga com regras NAT mapeando a porta 50.000 + i no balanceador de carga para a porta 22 na VM i. Portanto, assim que descobrirmos o FQDN do balanceador de carga, poderemos nos conectar às nossas VMs via SSH:

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```