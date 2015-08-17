
<properties 
    pageTitle="Dados de Perfil do Usuário no Azure RemoteApp"
	description="Saiba como os dados de usuário são armazenados e acessados no Azure RemoteApp"
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
    ms.date="07/16/2015" 
    ms.author="elizapo" />



# Como o Azure RemoteApp salva configurações e dados de usuário?

O Azure RemoteApp salva personalizações e a identidade do usuário em dispositivos e sessões. Esses dados de usuário são armazenados em um disco por coleção por usuário, conhecido como um disco de perfil de usuário (UDP). O disco segue o usuário e lhe garante uma experiência consistente, independentemente de onde ele entra.

Os discos de perfil do usuário são completamente transparentes para o usuário. Os usuários salvam documentos em sua pasta Documentos (no que parece ser uma unidade local) e alteram suas configurações de aplicativo como de costume. Ao mesmo tempo, todas as configurações pessoais persistem durante a conexão ao Azure RemoteApp de qualquer dispositivo. Tudo o que o usuário vê é seus dados no mesmo lugar.

Cada UPD tem 50 GB de armazenamento persistente e contém as configurações de dados e aplicativos do usuário.

Continue lendo para obter informações específicas sobre os dados de perfil do usuário.

## Como um administrador pode chegar aos dados?

Se você precisar acessar os dados para um dos seus usuários (para recuperação de desastres ou se o usuário sair da empresa), entre em contato com o [Azure RemoteApp](mailto:remoteappforum@microsoft.com) e forneça as informações de assinatura para a coleta e a identidade do usuário. A equipe de RemoteApp do Azure fornece uma URL em que você pode acessar os dados. De lá, você pode procurar o local e recuperar quaisquer documentos ou arquivos de que precisa.


## É feito backup dos dados?

Sim, vamos salvar um backup dos dados do usuário por localização geográfica. Os dados são somente leitura e podem ser acessados da mesma forma que os dados comuns (entre em contato com o Azure RemoteApp para saber), se o data center principal estiver inativo.

## Como os usuários veem o UPD no lado do servidor?

Cada usuário terá seu próprio diretório no servidor que mapeia para seu UPD: c:\\Users\\username.

## Qual é a melhor maneira de usar o Outlook e o UPD?

O Azure RemoteApp salva o estado do Outlook (caixas de correio, PSTs) entre as sessões. Para habilitar isso, precisamos que o PST seja armazenado nos dados de perfil do usuário (c:\\users<username>). Esse é o local padrão para os dados. Portanto, desde que você não altere o local, os dados serão mantidos entre sessões.

Também recomendamos que você use o modo "cache" no Outlook e o modo de "servidor/online" para pesquisar.

## Podemos usar soluções de dados compartilhados?
Sim, o Azure RemoteApp é compatível com o uso de soluções de dados compartilhados; particularmente o OneDrive for Business e Dropbox. No entanto, observe que o consumidor do OneDrive (a versão pessoal) e o Box não são compatíveis.

## E quanto ao redirecionamento?
Você pode configurar o RemoteApp do Azure para permitir que os usuários acessem dispositivos locais configurando [redirecionamento](remoteapp-redirection.md). Depois, os dispositivos locais podem acessar os dados no UPD.

## Posso usar meu UPD como um compartilhamento de rede?
Não, porque o UDP não é persistente. Um UPD só fica disponível quando o usuário está conectado ativamente ao Azure RemoteApp.

## Se eu excluir um usuário de uma coleção, seu UPD é excluído?

Não, quando você exclui um usuário, nós não excluímos automaticamente o UPD. Em vez disso, armazenamos os dados até que você exclua a coleção. 90 dias depois de excluir a coleção, nós excluímos todos os UPDs.

Se você precisar excluir um UPD de uma coleção, entre em contato com o Azure RemoteApp, pois podemos excluir o UPD do nosso lado.

## Posso acessar os UPDs dos meus usuários (usuários atuais ou excluídos)?

Sim, basta entrar em contato com o [Azure RemoteApp](mailto:remoteappforum@microsoft.com); podemos configurar uma URL para você acessar os dados. Você tem cerca de 10 horas baixar dados ou arquivos do UPD antes que o acesso expire.

## UPDs ficam disponíveis offline?

No momento, só oferecemos acesso offline a UPDs dentro da janela de acesso de 10 horas descrita acima. Isso significa que não temos uma forma de dar acesso por tempo suficiente para concluir tarefas mais complicadas, como executar um software antivírus nos UPDs ou acessar dados para uma auditoria.

## Posso desativar UPDs para uma coleção?

Sim, você pode pedir ao Azure RemoteApp para desabilitar UPDs para uma assinatura, mas não pode fazer isso por conta própria. Isso significa que os UPDs serão desabilitados para todas as coleções da assinatura.

## Posso impedir que os usuários salvem dados na unidade do sistema?

Sim, mas você precisa configurar isso na imagem de modelo antes de criar a coleção. Use as etapas a seguir para bloquear o acesso à unidade do sistema:

1. Execute **gpedit.msc** na imagem do modelo.
2. Navegue até **Configuração do usuário > Modelos administrativos > Componentes do Windows > Explorer**.
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

Você também pode usar aplicativos de sincronização de dados como o OneDrive for Business.

## Como posso executar um script de inicialização no Azure RemoteApp?

Se você quiser executar um script de inicialização, comece criando uma tarefa agendada na imagem do modelo que você vai usar para a coleção. (Faça isso *antes de* executar sysprep.)

![Criar uma tarefa do sistema](./media/remoteapp-upd/upd1.png)

![Criar uma tarefa do sistema que seja executada quando um usuário fizer logon](./media/remoteapp-upd/upd2.png)

A tarefa agendada abre o script de inicialização usando as credenciais do usuário. Agende a tarefa para ser executada toda vez que um usuário fizer logon.

![Defina o disparador para a tarefa "No logon"](./media/remoteapp-upd/upd3.png)

Você também pode usar [scripts de inicialização baseada em Política de Grupo](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx).

## E quanto a colocar um script de inicialização no menu Iniciar? Isso funcionaria?

Em outras palavras, posso criar um arquivo .bat que execute um script de configuração de janela e salvá-lo na pasta c:\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programs\\StartUp e, em seguida, executar esse script sempre que um usuário inicia uma sessão do RemoteApp?

Não, não há suporte para isso com o Azure RemoteApp, que usa RDSH, que também não oferece suporte a scripts de inicialização no menu Iniciar.

## Posso usar mstsc.exe (o programa Área de Trabalho Remota) para configurar os scripts de logon?

Não, não há suporte para isso com o Azure RemoteApp.

<!---HONumber=August15_HO6-->