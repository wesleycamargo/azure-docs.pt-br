<properties title="How to create a custom template image for RemoteApp" pageTitle="Como criar uma imagem do modelo personalizada para o RemoteApp" description="Saiba como criar uma imagem de modelo personalizado para o RemoteApp. Você pode usar este modelo de implantação de uma nuvem ou híbrida." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/22/2014" ms.author="elizapo" ms.manager="kathyw" />

#Como criar uma imagem do modelo personalizada para o RemoteApp
O RemoteApp do Azure usa uma imagem do modelo do Windows Server 2012 R2 para hospedar todos os programas que deseja compartilhar com os seus usuários. Para criar uma imagem do modelo do RemoteApp personalizada, é possível iniciar com uma imagem existente ou criar uma nova. Os requisitos para a imagem passiva de upload para o uso com o RemoteApp do Azure são:


- O tamanho da imagem deve ser um múltiplo de MBs. Se você tentar carregar uma imagem que não é um múltiplo exato, o carregamento falhará.
- O tamanho da imagem deve ser de 127 GB ou menor. 
- Deve estar em um arquivo VHD (arquivos VHDX atualmente não têm suporte).
- O VHD não deve ser uma máquina virtual de geração 2.
- O VHD pode ter tamanho fixo ou expandir dinamicamente. O VHD de expansão dinâmica é recomendado, pois demora menos para carregar o Azure que o arquivo VHD de tamanho fixo.
- O disco deve ser inicializado usando o estilo de particionamento do MBR (Registro mestre de inicialização). O estilo de particionamento da tabela de partição GUID (GPT) não tem suporte. 
- O VHD deve conter uma instalação única do Windows Server 2012 R2. Ele deve conter vários volumes, mas apenas um que contenha uma instalação do Windows. 
- A função Host da Sessão da Área de Trabalho Remota (RDSH) e o recurso Desktop Experience devem estar instalados.
- A função do Agente de Conexão de Área de Trabalho Remota *não* deve ser instalada.
- O Encrypting File System (EFS) deve estar desabilitado.
- A imagem deve ser SYSPREPed usando os parâmetros **/oobe /generalize /shutdown** (NÃO use o parâmetro **/mode:vm**).


**Antes de começar**

É necessário fazer o seguinte antes de criar o serviço:

- Inscreva-se para a visualização do RemoteApp. É possível fazer isso em [http://azure.microsoft.com/pt-br/services/remoteapp/](http://azure.microsoft.com/pt-br/services/remoteapp/).
- Crie uma conta de usuário no Active Directory para usar como a conta de serviço do RemoteApp. Restrinja as permissões para esta conta para que ela possa conectar-se somente às máquinas no domínio.
- Reúna as informações sobre a sua rede local: As informações de endereço IP e detalhes do dispositivo VPN.
- Instale o módulo [PowerShell do Azure](http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/).
- Reúna as informações sobre os usuários e os grupos aos quais deseja permitir acesso. Pode ser informações da conta da Microsoft ou da conta organizacional do Active Directory para usuários e grupos.



## **Criar uma imagem do modelo**##

Para criar uma nova imagem do modelo do zero:

1.	Localize um DVD do Windows Server 2012 R2 ou a imagem ISO.
2. Crie um arquivo VHD.
4. Instale o Windows Server 2012 R2.
5. Instale a função RDSH (Host da Sessão da Área de Trabalho Remota) e o recurso Desktop Experience.
6.	Instale os recursos adicionais necessários pelos seus aplicativos.
7.	Instale e configure os seus aplicativos.
8.	Execute quaisquer configurações adicionais do Windows necessárias para os seus aplicativos.
9.	Desabilite o EFS (Encrypting File System).
9.	SYSPREP a imagem.

As etapas detalhadas para a criação de uma nova imagem são:

1.	Localize um DVD do Windows Server 2012 R2 ou a imagem ISO. 
2.	Crie um arquivo VHD usando o Disk Management. 
	1.	Inicialize o Disk Management (diskmgmt.msc). 
	2.	Crie um VHD de expansão dinâmica com 40 GB ou com tamanho maior. (Calcule a quantia de espaço necessário para o Windows, os seus aplicativos e as suas personalizações. O Windows Server com a função RDSH e o recurso Desktop Experience instalados precisará de cerca de 10 GB de espaço).
		1.	Clique em **Ação > Criar**.
		2.	Especifique a localização, o tamanho e o formato do VHD. Selecione **Expansão dinâmica** e clique em **OK**.

			A execução demorará vários segundos. Quando a criação do VHD estiver concluída, será possível ver um novo disco sem qualquer letra da unidade e no estado "Não inicializado" no console do Disk Management.

		- Clique com o botão direito do mouse no disco (não no espaço não alocado) e, em seguida, clique em **Inicializar Disco**. Selecione **MBR** (Registro mestre de iniciação) como o estilo de partição e, em seguida, clique em **OK**.
		- Criar um novo volume: clique com o botão direito do mouse no espaço não alocado e, em seguida, clique em **Novo volume simples**. Você pode aceitar os padrões no assistente, mas verifique se atribuiu uma letra da unidade a fim de evitar possíveis problemas quando for carregar a imagem do modelo.
		- Clique com o botão direito do mouse no disco e clique em **Desanexar VHD**.

			



1. Instale o Windows Server 2012 R2:
	1. Crie uma nova máquina virtual. Use o Novo assistente da máquina virtual no Hyper-V Manager ou no Hyper-V Cliente. 
		1. Na página Especificar Geração, escolha **Geração 1**.
		2. Na página Conectar o disco virtual, selecione **Usar um disco virtual existente** e procure pelo VHD criado na etapa anterior.
		2. Na página Opções de instalação, selecione **Instalar um sistema operacional de um CD/DVD_ROM de inicialização** e, em seguida, selecione o local da mídia de instalação do seu Windows Server 2012 R2.
		3. Escolha outras opções necessárias no assistente para instalar o Windows e os seus aplicativos. Conclua o assistente.
	2.  Após a conclusão do assistente, edite as configurações da máquina virtual e faça outras alterações necessárias para instalar o Windows e os seus programas, como o número de processadores virtuais, e clique em **OK**.
	4.  Conecte-se à máquina virtual e instale o Windows Server 2012 R2.
1. Instale a função RDSH (Host da Sessão da Área de Trabalho Remota) e o recurso Desktop Experience:
	1. Inicialize o Gerenciador do Servidor.
	2. Clique em **Adicionar funções e recursos** na tela Bem-vindo ou do menu **Gerenciar**.
	3. Clique em **Avançar** na página Antes de começar.
	4. Selecione **Instalação baseada em função ou recurso** e clique em **Avançar**.
	5. Selecione o computador local na lista e clique em **Avançar**.
	6. Selecione **Serviços de Área de Trabalho Remota** e clique em **Avançar**.
	7. Expanda **Interfaces e Infraestrutura do Usuário** e selecione **Desktop Experience**.
	8. Clique em **Adicionar Recursos** e clique em **Avançar**.
	9. Na página Serviços de Área de Trabalho Remota e clique em **Avançar**.
	10. Clique em **Host da Sessão da Área de Trabalho Remota**.
	11. Clique em **Adicionar Recursos** e clique em **Avançar**.
	12. Na página Confirmar seleções de instalação, selecione **Reiniciar o servidor de destino automaticamente, se necessário** e, em seguida, clique em **Sim** no aviso de reinício.
	13. Clique em **Instalar**. O computador será reiniciado.
1.	Instale os recursos adicionais necessários pelos seus aplicativos, como .NET Framework 3.5. Para instalar os recursos, execute Adicionar funções e Assistente de recursos.
7. Instale e configure os programas e os aplicativos que deseja publicar através do RemoteApp.

 	**Importante:** A Microsoft recomenda instalar a função RDSH antes da instalação dos aplicativos para garantir que quaisquer problemas de compatibilidade do aplicativo sejam descobertos antes de fazer o upload da imagem no RemoteApp.

8.	Execute quaisquer configurações adicionais do Windows necessárias para os seus aplicativos.
9.	Desabilite o EFS (Encrypting File System). Execute o comando a seguir em uma janela de comandos com privilégios elevados:

		Fsutil behavior set disableencryption 1

Alternativamente, é possível configurar ou adicionar o valor DWORD a seguir no Registro: 

		HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.	Se você estiver criando sua imagem dentro de uma máquina virtual do Azure, renomeie o arquivo **\%windir%\Panther\Unattend.xml**, já que ele bloqueará o script de carregamento usado posteriormente no trabalho. Altere o nome deste arquivo para Unattend.old para que você ainda tenha este arquivo no caso de precisar reverter sua implantação.
10.	SYSPREP a imagem. Em um prompt de comandos com privilégios elevados, execute o seguinte comando: 

	**C:\Windows\System32\sysprep\sysprep.exe /generalize /oobe /shutdown**
	
	**Observação:** Não use o comutador **/mode:vm** do comando SYSPREP, mesmo que seja uma máquina virtual. 


## Próximas etapas ##
Agora que você tem sua imagem de modelo personalizada, você precisará atualizar aquela imagem para sua implantação do RemoteApp. Use as informações nos artigos a seguir para criar sua implantação:


- [Como criar uma implantação híbrida do RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-hybrid-deployment/) 
- [Como criar uma implantação de nuvem do RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-cloud-deployment/)

