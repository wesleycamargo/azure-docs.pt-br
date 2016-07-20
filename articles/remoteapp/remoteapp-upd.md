
<properties 
    pageTitle="Como o Azure RemoteApp salva configurações e dados de usuário? | Microsoft Azure"
	description="Saiba como o Azure RemoteApp salva dados de usuário usando o disco de perfil do usuário."
	services="remoteapp"
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/30/2016" 
    ms.author="elizapo" />

# Como o Azure RemoteApp salva configurações e dados de usuário?

O Azure RemoteApp salva personalizações e a identidade do usuário em dispositivos e sessões. Esses dados de usuário são armazenados em um disco por coleção por usuário, conhecido como um disco de perfil de usuário (UDP). O disco segue o usuário e lhe garante uma experiência consistente, independentemente de onde ele entra.

Os discos de perfil do usuário são completamente transparentes para o usuário. Os usuários salvam documentos em sua pasta Documentos (no que parece ser uma unidade local) e alteram suas configurações de aplicativo como de costume. Ao mesmo tempo, todas as configurações pessoais persistem durante a conexão ao Azure RemoteApp de qualquer dispositivo. Tudo o que o usuário vê é seus dados no mesmo lugar.

Cada UPD tem 50 GB de armazenamento persistente e contém as configurações de dados e aplicativos do usuário.

Continue lendo para obter informações específicas sobre os dados de perfil do usuário.

>[AZURE.NOTE] Precisa desabilitar o UPD? Você pode fazer isso agora - confira a postagem do blog de Pavithra, [Desabilitar Discos de Perfil de Usuário (UPDs) no Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/), para obter detalhes.


## Como um administrador pode chegar aos dados?

Se você precisar acessar os dados para um dos seus usuários (para recuperação de desastres ou se o usuário sair da empresa), entre em contato com o [Azure RemoteApp](mailto:remoteappforum@microsoft.com) e forneça as informações de assinatura para a coleta e a identidade do usuário. A equipe do Azure RemoteApp fornece uma URL para o VHD. Baixe esse VHD e recupere quaisquer documentos ou arquivos que você precisa. Observe que o VHD é de 50 GB, por isso levará algum tempo para baixá-lo.


## É feito backup dos dados?

Sim, vamos salvar um backup dos dados do usuário por localização geográfica. Os dados são somente leitura e podem ser acessados da mesma forma que os dados comuns (entre em contato com o Azure RemoteApp para saber), se o data center principal estiver inativo. Os dados são copiados em tempo real no local de backup e não mantemos cópias de versões diferentes. Assim, se houver dados corrompidos, não poderemos restaurá-los para uma versão válida anteriormente conhecida, mas se o data center principal estiver inativo, você poderá obter os dados do usuário de outro local.

## Como os usuários veem o UPD no lado do servidor?

Cada usuário terá seu próprio diretório no servidor que mapeia para seu UPD: c:\\Users\\username.

## Qual é a melhor maneira de usar o Outlook e o UPD?

O Azure RemoteApp salva o estado do Outlook (caixas de correio, PSTs) entre as sessões. Para habilitar isso, precisamos que o PST seja armazenado nos dados de perfil do usuário (c:\\users<username>). Esse é o local padrão para os dados. Portanto, desde que você não altere o local, os dados serão mantidos entre sessões.

Também recomendamos que você use o modo "cache" no Outlook e o modo de "servidor/online" para pesquisar.

Confira [este artigo](remoteapp-outlook.md) para obter mais informações sobre como usar o Outlook e o Azure RemoteApp.

## E quanto ao redirecionamento?
Você pode configurar o Azure RemoteApp para permitir que os usuários acessem dispositivos locais configurando o [redirecionamento](remoteapp-redirection.md). Depois, os dispositivos locais podem acessar os dados no UPD.

## Posso usar meu UPD como um compartilhamento de rede?
Não. Os UPDs não podem ser usados como um compartilhamento de rede. Um UPD só é disponibilizado ao usuário quando ele está conectado ativamente ao Azure RemoteApp.

## Se eu excluir um usuário de uma coleção, seu UPD é excluído?

Não, quando você exclui um usuário, nós não excluímos automaticamente o UPD. Em vez disso, armazenamos os dados até que você exclua a coleção. 90 dias depois de excluir a coleção, nós excluímos todos os UPDs.

Se você precisar excluir um UPD de uma coleção, entre em contato com o Azure RemoteApp, pois podemos excluir o UPD do nosso lado.

## Posso acessar os UPDs dos meus usuários (usuários atuais ou excluídos)?

Sim, se você entrar em contato com o [Azure RemoteApp](mailto:remoteappforum@microsoft.com), poderemos configurar uma URL para você acessar os dados. Você tem cerca de 10 horas baixar dados ou arquivos do UPD antes que o acesso expire.

## UPDs ficam disponíveis offline?

No momento, só oferecemos acesso offline a UPDs dentro da janela de acesso de 10 horas descrita acima. Isso significa que não temos uma forma de dar acesso por tempo suficiente para concluir tarefas mais complicadas, como executar um software antivírus nos UPDs ou acessar dados para uma auditoria.

## As configurações de chave do Registro são persistentes?
Sim, todos os elementos escritos em HKEY\_Current\_User fazem parte do UPD.

## Posso desativar UPDs para uma coleção?

Sim, você pode pedir ao Azure RemoteApp para desabilitar UPDs para uma assinatura, mas não pode fazer isso por conta própria. Isso significa que os UPDs serão desabilitados para todas as coleções da assinatura.

Talvez você queira desabilitar UPDs em qualquer uma das seguintes situações:

- Você precisa concluir o acesso e o controle de dados do usuário (para fins de auditoria e de revisão, como instituições financeiras).
- Você tem soluções de terceiros para o gerenciamento de perfis de usuário e deseja continuar usando-as na implantação do Azure RemoteApp ingressado no domínio. Isso exige que o agente de perfil seja carregado na imagem dourada.
- Você não precisa de um armazenamento de dados local nem de ter todos os dados na nuvem ou em um compartilhamento de arquivos e deseja controlar o salvamento de dados no local usando o Azure RemoteApp.

Veja [Desabilitar Discos de Perfil de Usuário (UPDs) no Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/) para saber mais.

## Posso impedir que os usuários salvem dados na unidade do sistema?

Sim, mas você precisa configurar isso na imagem de modelo antes de criar a coleção. Use as etapas a seguir para bloquear o acesso à unidade do sistema:

1. Execute **gpedit.msc** na imagem do modelo.
2. Navegue até **Configuração do Usuário > Modelos Administrativos > Componentes do Windows > Explorer**.
3. Selecione as seguintes opções:
	- **Ocultar estas unidades especificadas em Meu Computador**
	- **Impedir o acesso a unidades de Meu Computador**

## Eu posso propagar UPDs? Eu quero colocar alguns dados no UPD que fiquem disponíveis na primeira vez que o usuário se conecta.

Sim, ao criar a imagem de modelo, você pode adicionar informações ao perfil padrão. Essa informação é adicionada ao UPD.

## Posso alterar o tamanho do UPD dependendo da quantidade de dados que desejo armazenar?

Não, todos os UPDs têm 50 GB de armazenamento. Se você quiser armazenar diferentes quantidades de dados, tente o seguinte:

1. Desabilite UPDs para a coleção.
2. Configure um compartilhamento de arquivos para que os usuários acessem.
3. Carregue o compartilhamento de arquivos usando um script de inicialização. Consulte abaixo para obter detalhes sobre scripts de inicialização no Azure RemoteApp.
4. Oriente os usuários a salvar todos os dados para o compartilhamento de arquivos.


## Como posso executar um script de inicialização no Azure RemoteApp?

Se você quiser executar um script de inicialização, comece criando uma tarefa agendada na imagem do modelo que você vai usar para a coleção. (Faça isso *antes* de executar o sysprep).

![Criar uma tarefa do sistema](./media/remoteapp-upd/upd1.png)

![Criar uma tarefa do sistema que seja executada quando um usuário fizer logon](./media/remoteapp-upd/upd2.png)

Na guia **Geral**, altere a **Conta de Usuário** em Segurança para "BUILTIN\\Usuários".

![Alterar a conta de usuário para um grupo](./media/remoteapp-upd/upd4.png)

A tarefa agendada abre o script de inicialização usando as credenciais do usuário. Agende a tarefa para ser executada toda vez que um usuário fizer logon.

![Defina o disparador para a tarefa "No logon"](./media/remoteapp-upd/upd3.png)

Você também pode usar [scripts de inicialização baseados na Política de Grupo](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx).

## E quanto a colocar um script de inicialização no menu Iniciar? Isso funcionaria?

Em outras palavras, posso criar um arquivo .bat que execute um script de configuração de janela e salvá-lo na pasta c:\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programs\\StartUp e, em seguida, executar esse script sempre que um usuário inicia uma sessão do RemoteApp?

Não, não há suporte para isso com o Azure RemoteApp, que usa RDSH, que também não oferece suporte a scripts de inicialização no menu Iniciar.

## Posso usar mstsc.exe (o programa Área de Trabalho Remota) para configurar os scripts de logon?

Não, não há suporte para isso com o Azure RemoteApp.

## Posso armazenar dados na VM localmente?

NÃO, os dados armazenados em qualquer lugar na VM que não o UPD serão perdidos. Há uma chance de alta de que o usuário não receba a mesma VM na próxima vez que ele se conectar ao Azure RemoteApp. Nós não mantemos persistência de VM do usuário, assim, o usuário não se conectará à mesma VM e os dados serão perdidos. Além disso, quando atualizamos a coleção, as VMs existentes são substituídas por um novo conjunto de VMs, o que significa que todos os dados armazenados na própria VM são perdidos. A recomendação é armazenar dados no UPD, armazenamento compartilhado, como arquivos do Azure, um servidor de arquivos em uma VNET ou na nuvem usando um sistema de armazenamento de nuvem com suporte, como o DropBox.

## Como posso montar um compartilhamento de Arquivos do Azure em uma VM usando o PowerShell?

Você pode usar o cmdlet PSDrive Net para montar a unidade da seguinte maneira:

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \<storage-account-name>.file.core.windows.net<share-name> -Credential :<storage-account-name>


Você também pode salvar suas credenciais executando o seguinte:

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


Isso permite ignorar o parâmetro -Credential no cmdlet New-PSDrive.

<!---HONumber=AcomDC_0706_2016-->