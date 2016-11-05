
1. Conecte-se à sua assinatura do Azure usando as etapas listadas em [Conectar ao Azure pela CLI do Azure](../articles/xplat-cli-connect.md).
2. Verifique se que você está no modo de implantação clássico usando:
   
        azure config mode asm
3. Localize a imagem do Linux que deseja carregar nas imagens disponíveis:
   
        azure vm image list | grep "Linux"
   
   Em uma janela de prompt de comando do Windows, use **find** em vez de grep.
4. Use `azure vm create` para criar uma nova máquina virtual com a imagem do Linux da lista anterior. Esta etapa cria uma nova conta de armazenamento e um novo serviço de nuvem. Você também pode conectar essa máquina virtual a um serviço de nuvem existente com uma opção `-c`. Ela também cria um ponto de extremidade SSH para fazer logon na máquina virtual Linux com a opção `-e`.
   
        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-pt-BR-30GB -g adminUser -p P@ssw0rd! -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-pt-BR-30GB
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating VM
        info:    vm create command OK
   
   > [!NOTE]
   > Para uma máquina virtual Linux, você deve fornecer a opção `-e` no `vm create`. Não é possível ativar SSH após a máquina virtual ter sido criada. Para obter mais detalhes sobre SSH, leia [Como usar SSH com Linux no Azure](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).
   > 
   > 
   
    A imagem *b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-pt-BR-30GB* é aquela que escolhemos na lista de imagens na etapa anterior. *MyTestVM* é o nome da nossa nova máquina virtual, e *adminUser* é o nome de usuário para fazer a conexão SSH com a máquina virtual. Você pode substituir essas variáveis de acordo com suas necessidades. Para obter mais detalhes sobre esse comando, visite o [Usando a CLI do Azure com o modelo de implantação clássico](../articles/virtual-machines-command-line-tools.md).
5. A máquina virtual Linux recém-criada aparecerá na lista fornecida por:
   
        azure vm list
6. Você pode verificar os atributos da máquina virtual usando o comando:
   
        azure vm show MyTestVM
7. A máquina virtual recém-criada está pronta para começar com o comando `azure vm start`.

## Próximas etapas
Para obter detalhes sobre todos esses comandos de máquina virtual da CLI do Azure, leia [Usando a CLI do Azure com a API da implantação clássica](../articles/virtual-machines-command-line-tools.md).

<!---HONumber=AcomDC_0824_2016-->