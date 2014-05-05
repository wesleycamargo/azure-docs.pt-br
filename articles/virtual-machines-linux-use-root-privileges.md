<properties linkid="manage-linux-common-tasks-user-root-privileges" urlDisplayName="Usar privilégios de raiz" pageTitle="usar privilégios de raiz nas máquinas virtuais Linux Azure" metaKeywords="" description="aprender a usar privilégios de raiz no computador virtual Linux no Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Utilizando privilégios de raiz nas máquinas virtuais Linux do Azure" authors="" solutions="" manager="" editor="" />




#Utilizando privilégios de raiz nas máquinas virtuais Linux do Azure

Os usuários podem executar comandos com privilégios elevados em uma máquina virtual do Linux usando o `sudo` comando. No entanto, a experiência pode variar dependendo de como o sistema foi fornecido.

1. **Chave SSH e senha ou apenas** - a máquina virtual foi configurada com um dos certificados (`.CER` arquivo), bem como uma senha, ou apenas um nome de usuário e uma senha. Neste caso `sudo` solicitará a senha do usuário antes de executar o comando.

2. **Somente a chave SSH** - a máquina virtual foi configurada com um certificado (`. cer` ou `.pem` arquivo), mas nenhuma senha.  Neste caso `sudo` **não** solicitará a senha do usuário antes de executar o comando.


##SSH chave e a senha ou a senha apenas

Faça logon na máquina virtual Linux usando autenticação de senha ou chave SSH, em seguida, executar comandos usando `sudo`, por exemplo:

	# sudo <comando>
	[sudo] senha para azureuser:

Nesse caso, o usuário será solicitado para uma senha. Depois de inserir a senha `sudo` executará o comando com `raiz` privilégios.


##SSH chave somente

Faça logon na máquina virtual Linux usando autenticação de chave SSH, em seguida, execute comandos usando `sudo`, por exemplo:

	# sudo <comando>

Nesse caso, o usuário **não** será solicitado para uma senha. Depois de pressionar `<enter>`, `sudo` executará o comando com privilégios `raiz`.


