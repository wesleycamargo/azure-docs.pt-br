1. Conecte-se à sua assinatura do Azure usando as etapas listadas em [Conectar ao Azure pela CLI do Azure](../articles/xplat-cli-connect.md).

2. Verifique se você está no modo Gerenciamento de Serviços usando:

        azure config mode asm

3. Localize a imagem do Linux que deseja carregar nas imagens disponíveis:

        azure vm image list | grep "Linux"

4. Use `azure vm create` para criar uma nova máquina virtual com a imagem do Linux da lista acima. Essa etapa cria um novo serviço de nuvem, bem como uma nova conta de armazenamento. Você também pode conectar essa máquina virtual a um serviço de nuvem existente com uma opção `-c`. Ela também cria um ponto de extremidade SSH para fazer logon na máquina virtual Linux com a opção `-e`.

        ~$ azure vm create "MyTestVM" b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64 "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE]Para uma máquina virtual Linux, você deve fornecer a opção `-e` em `vm create`; não é possível habilitar o SSH depois que a máquina virtual tiver sido criada. Para obter mais detalhes sobre SSH, leia [Como usar SSH com Linux no Azure](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md).

    Observe que a imagem *b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-opensuse-13.1-20141216-x86-64* é a que escolhemos na lista de imagens na etapa acima. *MyTestVM* é o nome da nossa nova máquina virtual e *adminUser* é o nome de usuário que usaremos para SSH na máquina virtual. Você pode substituir essas variáveis de acordo com suas necessidades. Para obter mais detalhes sobre esse comando, visite [Usando a CLI do Azure com o Gerenciamento de Serviços do Azure](../articles/virtual-machines/virtual-machines-command-line-tools.md).

5. A máquina virtual Linux recém-criada aparecerá na lista fornecida por:

        azure vm list

6. Você pode verificar os atributos da máquina virtual usando o comando:

        azure vm show MyTestVM

7. A máquina virtual recém-criada está pronta para começar com o comando `azure vm start`.

Para obter detalhes sobre todos esses comandos de máquina virtual da CLI do Azure, leia [Usando a CLI do Azure com a API de Gerenciamento de Serviços](../articles/virtual-machines/virtual-machines-command-line-tools.md).

<!---HONumber=August15_HO8-->