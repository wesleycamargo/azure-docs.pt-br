<properties 
	pageTitle="Adicionar seu nome de domínio ao AD do Azure" 
	description="Um tópico que explica como adicionar seu próprio nome de domínio ao AD do Azure e informações relacionadas." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2015" 
	ms.author="Justinha"/>

# Adicionar seu nome de domínio ao AD do Azure

Quando você se inscreve em um serviço de nuvem da Microsoft, recebe um nome de domínio com o seguinte formato: contoso.onmicrosoft.com. Você pode continuar a usar esse nome de domínio inicial, ou pode adicionar seu próprio nome de domínio personalizado ao serviço de nuvem. Este tópico explica como adicionar seu próprio nome de domínio e informações relacionadas.

Usuários do Office 365 também podem estar interessados nestes tópicos relacionados:

- [Noções básicas de DNS](https://support.office.com/article/854b6b2b-0255-4089-8019-b765cff70377)
- [Encontrar seu registrador de domínio ou provedor de hospedagem de DNS](https://support.office.com/article/Find-your-domain-registrar-or-DNS-hosting-provider-b5b633ba-1e56-4a98-8ff5-2acaac63a5c8/)
- [Trabalhar com nomes de domínio no Office 365](https://support.office.com/article/Work-with-domain-names-in-Office-365-4f1bd681-337a-4bd3-a7b7-cf77b18d0870/)

## Sobre o domínio onmicrosoft.com

Você pode usar seu domínio onmicrosoft.com junto a outros serviços. Por exemplo, você pode usar o domínio com o Exchange Online e o Lync Online para criar listas de distribuição e contas de logon para que os usuários possam acessar o SharePoint Online e conjuntos de sites.

Se você adicionar seus próprios nomes de domínio ao diretório, poderá continuar usando seu domínio onmicrosoft.com.

Depois de escolher o nome a ser usado com o serviço de nuvem durante a inscrição, como contoso.onmicrosoft.com, ele não poderá ser alterado.

## Como adicionar meu próprio domínio?

Se sua organização já tiver um nome de domínio personalizado, como um administrador, você pode adicioná-lo ao diretório do AD do Azure para usar com todos os serviços online da Microsoft aos quais você se inscreveu. Depois de adicionar seu nome de domínio ao AD do Azure, você poderá começar associando seu nome de domínio a vários serviços de nuvem.

Você pode adicionar até 900 nomes de domínio ao locatário do AD do Azure usando:

- O Portal de Gerenciamento do Azure, o portal do Office 365 ou o portal do Microsoft Intune.
- Módulo do Active Directory do Azure para Windows PowerShell. Para obter mais informações sobre qual cmdlet você pode usar para isso, consulte [Gerenciar domínios](https://msdn.microsoft.com/library/azure/dn919677.aspx).

Você já deve ter registrado um nome de domínio e tem as credenciais de entrada necessárias para o registrador de nome de domínio (por exemplo, Go Daddy ou Register.com).

Você pode adicionar vários domínios ao seu diretório. No entanto, você não pode adicionar o mesmo domínio a diretórios diferentes. Assim, por exemplo, se você adicionar seu domínio ao diretório, não poderá adicionar outro diretório do AD do Azure e adicionar o mesmo nome de domínio a ele.

Se você planeja usar o logon único com o serviço de nuvem, é recomendável que ajude a preparar o ambiente do Active Directory, executando a ferramenta de preparação de implantação da Microsoft. Essa ferramenta inspeciona o ambiente do Active Directory e fornece um relatório que inclui informações sobre se você está pronto para configurar o logon único. Caso contrário, ele lista as alterações que você precisa fazer para se preparar para logon único. Por exemplo, inspeciona se seus usuários têm UPNs e se esses UPNs estão no formato correto. Para baixar a ferramenta, consulte [Ferramenta de preparação de implantação da Microsoft](http://go.microsoft.com/fwlink/?linkid=235650).

> [AZURE.NOTE]Usando o Office 365? Depois que você configurar seu domínio, você pode começar a criar endereços de email, conta do Lync Online e grupos de distribuição que usam seu nome de domínio personalizado. Você também pode usar seu nome de domínio para um site público hospedado no SharePoint Online.

- [Adicionar e verificar um nome de domínio usando o Portal de Gerenciamento do Azure](#add-and-verify-a-domain-using-the-azure-management-portal)
- [Editar registros DNS para serviços de nuvem](#edit-dns-records-for-your-cloud-services)
- [Verificar um domínio em qualquer registrador de nome de domínio](#verify-a-domain-at-any-domain-name-registrar)

### Adicionar e verificar um nome de domínio usando o Portal de Gerenciamento do Azure

1. No portal, clique em **Active Directory**, em seguida, clique no nome do diretório da organização. Você pode executar uma das seguintes ações:
    1. Na página do diretório padrão, clique em **Adicionar Domínio** na seção **Melhorar a experiência de logon do usuário **. 2. Clique em **Domínios** e, em seguida, clique em **Adicionar um domínio de cliente** ou no botão **Adicionar**.
2. Na página **Adicionar domínio**, digite o nome de domínio que você deseja adicionar e execute uma das seguintes ações:
    1. Se você não planeja integrar o Active Directory local com o AD do Azure, faça o seguinte:
        1. Deixe a caixa de seleção **Eu planejo configurar este domínio para logon único com o Active Directory local** desmarcada e clique no botão **Adicionar**.
        2. Depois de ver a mensagem de que o domínio foi adicionado com êxito ao AD do Azure, clique na seta para ir para a próxima página e verificar seu domínio.
        3. Siga as instruções na próxima página para verificar se o nome de domínio que você adicionou nas etapas anteriores pertence a você. Para instruções passo a passo, consulte Verificar um domínio em qualquer registrador de nome de domínio.
    2. Se você quiser integrar o Active Directory local com AD do Azure, faça o seguinte:
        1. Deixe a caixa de seleção **Eu planejo configurar este domínio para logon único com o Active Directory local** marcada e clique no botão **Adicionar**.
        2. Depois de ver a mensagem de que o domínio foi adicionado com êxito ao AD do Azure, clique na seta para ir para a próxima página e, em seguida, siga as instruções na página para configurar o domínio adicionado para logon único.

> [AZURE.NOTE]Depois de adicionar seu nome de domínio ao AD do Azure, você pode alterar o nome de domínio padrão para novos endereços de email. Para obter mais informações, consulte [Como alterar o nome de domínio primário para os usuários?](#how-can-i-change-the-primary-domain-name-for-users?) Você também pode editar o perfil de uma conta de usuário existente para atualizar o endereço de email (que também é a ID de usuário) para usar o nome de domínio personalizado em vez do domínio onmicrosoft.com.

### Editar os registros DNS para os serviços de nuvem

> [AZURE.NOTE]Usando o Microsoft Intune? Você não precisa editar registros DNS para o serviço de nuvem Microsoft Intune.

Depois de adicionar e verificar seu nome de domínio personalizado, a próxima etapa é editar os registros DNS no seu registrador de domínio ou provedor de hospedagem de DNS que aponta o tráfego para seu serviço de nuvem. O AD do Azure fornece as informações de DNS que você precisa.

Se você acabou de concluir o assistente **Adicionar um domínio**, clique em **Configurar registros DNS**. Caso contrário, siga estas etapas:

1. No portal, no painel esquerdo, clique em **Domínios**.
2. Dependendo de qual portal você está usando, clique no nome de domínio que deseja configurar e, em seguida, clique em **Configurações de DNS** ou **Exibir configurações de DNS**. A página **Configurações de DNS** lista os registros DNS para o serviço de nuvem.

    Para configurar um serviço que não é exibido na guia Configurações de DNS, verifique suas seleções de serviços de domínio para garantir que você escolheu esse serviço para esse nome de domínio. Para alterar as configurações, por exemplo, para adicionar o Lync Online, consulte Especificar os serviços que você usará com seu domínio.

3. No site de registrador de nome de domínio, adicione os registros necessários ao arquivo de DNS.

Geralmente, leva cerca de 15 minutos para que as alterações entrem em vigor. Mas pode levar até 72 horas para que o registro DNS que você criou se propague através do sistema DNS. Se você precisar exibir essas configurações do registro novamente, na pagina **Domínios**, clique no domínio e, em seguida, em **Propriedades de domínio** e clique na guia **Configurações de DNS**.

Para verificar o status do seu domínio, na página **Domínios**, clique no domínio e, em seguida, na página **Propriedades de domínio** clique em **Solucionar problemas do domínio**.

### Verificar um domínio em qualquer registrador de nome de domínio

Se você já tiver um domínio registrado com um registrador de nome de domínio e quiser configurá-lo para funcionar com o AD do Azure, a verificação de domínio é necessária para confirmar que ele é de sua propriedade. Para verificar seu domínio, você cria um registro DNS no registrador de nome de domínio, ou onde seu DNS estiver hospedado e, em seguida, o AD do Azure usa esse registro para confirmar que o domínio é de sua propriedade.

Antes que você possa verificar seu domínio, deve adicionar um domínio personalizado ao AD do Azure. Quando você adiciona um domínio personalizado, mas o domínio ainda não foi verificado, o status mostrará **Clique para verificar o domínio** ou **Não verificado**.

#### Coletar informações de domínio 

Com base no portal que você está usando para administrar o diretório do AD do Azure, você precisará coletar algumas informações sobre seu domínio para que, mais tarde, possa criar um registro DNS que será usado durante o processo de verificação.

Se você estiver usando o Microsoft Intune ou o Portal de Conta do Azure:

1. Na página **Domínios**, na lista de nomes de domínio, encontre o domínio que você está verificando. Na coluna **Status**, clique em **Clicar para verificar domínio**.
2. Na página **Verificar domínio**, na lista suspensa**Consultar instruções para executar esta etapa com:**, selecione o provedor de hospedagem de DNS. Se seu provedor não aparecer na lista, escolha **Instruções gerais**.
3. Na lista suspensa **Selecionar um método de verificação:**, escolha **Adicionar um registro TXT (método preferencial)** ou **Adicionar um registro MX (método alternativo)**.

    Se seu provedor de hospedagem de DNS permitir a criação de registros TXT, recomendamos o uso de um registro TXT para verificação. Por quê? Registros TXT são fáceis de criar e não apresentam a possibilidade de interferir com a entrega de email se um valor incorreto for inserido acidentalmente.

4. Na tabela, copie ou registre as informações de **Destino ou Pontos para endereçamento**.

Se você estiver usando o Portal de Gerenciamento do Azure:

1. No portal, clique em **Active Directory**, clique no nome do diretório, clique em **Domínios**. 
2. Na página **Domínios**, na lista de nomes de domínio, clique no domínio que você quer verificar e, em seguida, clique em **Verificar**.
2. Na página **Verificar**, na lista suspensa **Tipo de Registro**, escolha **Registro TXT** ou **Registro MX**.
3. Copie ou registre as informações abaixo dele.

#### Adicionar um registro DNS ao seu registrador de nome de domínio 

O AD do Azure usa um registro DNS que você cria no seu registrador de nome de domínio para confirmar que possui o domínio. Use as instruções a seguir para criar o tipo de registro TXT ou MX para um domínio que está registrado no seu registrador.

Se o seu registrador de domínios não aceitar "@" como um nome de host, entre em contato com o registrador de domínio para descobrir como representar o “pai da zona atual”.

O Office 365 possui [instruções específicas para registradores de domínio comuns](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

Para obter instruções gerais, siga estas etapas para adicionar um registro TXT ou MX:

1. Entre no site do registrador de nome do domínio e, em seguida, selecione o domínio que você está verificando.
2. Na área de gerenciamento do DNS de sua conta, selecione a opção para adicionar um TXT ou MX ao domínio.
3. Na caixa **TXT** ou **MX** do domínio, digite o seguinte: @
4. Na caixa **Nome de domínio totalmente qualificado (FQDN)** ou **Pontos para**, digite ou cole o **Destino ou Pontos para endereçamento** que você registrou na etapa anterior.
5. Para um registro TXT, ele pede informações de **TTL**. Digite **1** para definir o TTL como uma hora. 

    Para um registro MX, ele solicita uma prioridade (ou preferência). Digite um número que seja maior do que o número especificado para os registros MX existentes. Isso pode ajudar a impedir que o novo registro MX interfira no roteamento de email para o domínio. Em vez de uma prioridade, você verá as seguintes opções: **Baixa**, **Média**, **Alta**. Nesse caso, escolha **Baixa**.

6. Salve suas alterações e saia do site do registrador de nome do domínio.

Depois de criar o registro TXT ou MX e sair do site, retorne ao serviço de nuvem para verificar o domínio. Geralmente, leva cerca de 15 minutos para que as alterações entrem em vigor. Mas pode levar até 72 horas para que o registro que você criou se propague no sistema DNS.

#### Verifique o seu domínio 

Depois que o registro que você criou para seu domínio se propagar com êxito no sistema DNS, faça o seguinte para concluir a verificação do domínio com o AD do Azure.

1. No portal, clique em **Domínios**.
2. Na lista **Domínios**, localize o domínio que você está verificando e, em seguida, com base no portal que você está usando, clique em **Clicar para verificar o domínio** ou **Verificar**.
3. Siga as instruções fornecidas para completar o processo de verificação.
    - Se a verificação for bem-sucedida, você será notificado de que o domínio foi adicionado à sua conta.
    - Se a verificação falhar, as alterações feitas no registrador de domínio podem precisar de mais tempo para se propagarem. Cancele o processo de verificação e retorne depois para tentar a verificação novamente.

Se mais de 72 horas tiverem passado desde que você fez as alterações no domínio, entre no site do registrador de domínio e verifique se você inseriu as informações de alias corretamente. Se você inseriu as informações incorretamente, deve remover o registro DNS incorreto e criar um novo com as informações corretas, usando os procedimentos deste tópico.

Depois de verificar seu domínio, você pode configurá-lo para operar com suas contas.

## Como alterar o nome de domínio primário para os usuários?

Depois de adicionar seu nome de domínio ao AD do Azure, você pode alterar o nome de domínio que aparece como o padrão quando você cria uma nova conta de usuário. Para fazer isso, siga estas etapas:

1. Na página de portal, no canto superior esquerdo, clique no nome da sua organização.
2. Clique em **Editar**.
3. Escolha um novo nome de domínio padrão, como o nome de domínio personalizado que você adicionou.

## Como remover um domínio?

Antes de remover um nome de domínio, recomendamos que você leia as seguintes informações:

- O nome de domínio contoso.onmicrosoft.com original que foi fornecido ao diretório quando você se inscreveu não pode ser removido. 
- Qualquer domínio de nível superior que tenha subdomínios associados a ele não pode ser removido até que os subdomínios sejam removidos. Por exemplo, não é possível remover o adatum.com se você tiver o corp.adatum.com ou outro subdomínio que usa o nome de domínio de nível superior. Para obter mais informações, consulte o [Artigo de suporte](https://support.microsoft.com/kb/2787792/).
- Você ativou a sincronização do diretório? Se ativou, um domínio semelhante a este foi adicionado automaticamente à sua conta: contoso.mail.onmicrosoft.com. Esse nome de domínio não pode ser removido.
- Antes de remover um nome de domínio, você deve primeiro remover o nome de domínio de todas as contas de usuário ou de email associadas ao domínio. Você pode remover todas as contas, ou você pode editar contas de usuário em massa para alterar informações de nomes de domínio e endereços de email. Para obter mais informações, consulte [Criar ou editar usuários no AD do Azure](active-directory-create-users.md).
- Se você estiver hospedando um site SharePoint Online em um nome de domínio que está sendo usado para um conjunto de sites do SharePoint Online, deve excluir o conjunto de sites antes de remover o nome de domínio.

Para remover um nome de domínio:

1. Na página do portal, no painel esquerdo, clique em **Domínios**.
2. Na página **Domínios**, selecione o nome de domínio que você quer remover e clique em **Remover domínio**.
3. Na página **Remover domínio** clique em **Sim**.

Se seu nome de domínio não puder ser removido neste momento, o status do nome de domínio será mostrado como Remoção pendente na página Domínios. Se você continuar vendo esse status, tente novamente remover o nome do domínio.

## Solucionando problemas após a alteração do nome de domínio 

### Fiz alterações no meu domínio, mas ele ainda não mostra as alterações.

Em razão do modo pelo qual as atualizações se movem através do DNS (Sistema de Nomes de Domínio), pode levar até 72 horas antes que as alterações feitas em um registrador de domínio ou provedor de hospedagem se propaguem totalmente pela Internet e você possa começar a usar seu nome de domínio com os serviços.

Além disso, as edições feitas no registrador de domínio devem ser exatamente corretas. Se você voltar para corrigir um erro, pode levar vários dias até que a configuração atualizada apareça no site do portal do serviço de nuvem.

Quanto tempo levará? Depende em parte da configuração do TTL (Vida Útil) que você especificou para o registro DNS que está substituindo ou atualizando. Até que o TTL expire, os servidores de Internet que armazenaram em cache os dados anteriores não consultarão o servidor de nome autoritativo para solicitar o novo valor.

### Eu adicionei um domínio, verifiquei e configurei os registros DNS no site do registrador de domínio. Por que as novas contas de email ainda não estão recebendo emails? 

Depois de adicionar ou atualizar registros DNS para o domínio, pode levar até 72 horas para que as alterações entrem em vigor.

Além disso, as informações de configuração devem ser exatamente corretas no site do registrador de domínio. Verifique atentamente suas configurações e certifique-se de ter permitido tempo suficiente para que os registros DNS alterados se propaguem no sistema.

### Não consigo verificar meu nome de domínio. Como descobrir o que está errado? 

Uma maneira de rastrear problemas é usar o assistente de solução de problemas de domínios. Para iniciar o assistente, faça o seguinte: no portal do serviço de nuvem, na página de administração, clique em **Domínios** e depois clique duas vezes no nome de domínio que você quer verificar. Em seguida, em **Solucionando problemas**, clique em **Solucionar problemas do domínio**.

O assistente de solução de problemas solicita informações sobre onde você está no processo de verificação e fornece informações para ajudá-lo a concluir a verificação.

### Eu adicionei e verifiquei meu domínio, mas o novo nome de domínio não está funcionando para endereços de email de usuários existentes. 

Se você adicionar seu nome de domínio personalizado ao serviço de nuvem depois de adicionar as contas de usuário, terá de fazer atualizações para usar o novo nome de domínio. Por exemplo, você precisará editar suas contas de usuários para definir seus endereços de email para usar seu domínio personalizado.

## O que vem a seguir?

- [Fórum do AD do Azure](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
- [Inscrever-se no Azure como uma organização](sign-up-organization.md)
- [Gerenciar domínios no AD do Azure](https://msdn.microsoft.com/library/azure/dn919677.aspx) 

<!---HONumber=July15_HO3-->