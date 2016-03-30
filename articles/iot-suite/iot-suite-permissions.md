<properties
  pageTitle="Pacote IoT do Azure e Active Directory do Azure | Microsoft Azure"
  description="Descreve como o Pacote IoT do Azure usa o Active Directory do Azure para gerenciar permissões."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="03/02/2016"
  ms.author="araguila"/>
  
# Permissões no site azureiotsuite.com

## O que acontece quando você entra

Quando você entra pela primeira vez no [azureiotsuite.com][lnk-azureiotsuite], o site determina os níveis de permissão que você tem com base no locatário do Active Directory do Azure (AAD) e na assinatura do Azure selecionados no momento.

1.  Primeiro site descobre no Azure a quais locatários do AAD você pertence para preencher a lista de locatários vista ao lado do seu nome de usuário conectado. Neste momento, o site poderá obter somente os tokens de usuário para um locatário por vez. Como resultado, quando você alternar para um locatário diferente usando o menu suspenso no canto superior direito, o site conectará você novamente ao novo locatário para obter os tokens para ele.

2.  Em seguida, o site descobre no Azure quais assinaturas estão associadas ao locatário selecionado. Você verá as assinaturas quando criar uma nova solução pré-configurada.

3.  Por fim, o site recuperará todos os recursos nas assinaturas e nos grupos de recursos marcados como soluções pré-configuradas e preencherá os blocos na home page.

As seções a seguir descrevem as funções que controlam o acesso às soluções pré-configuradas.

## Funções do AAD

As funções do AAD controlam as soluções pré-configuradas de provisão de capacidade e gerenciam usuários em uma solução pré-configurada.

Você pode encontrar mais informações sobre funções de administrador no AAD em [Atribuindo funções de administrador no AD do Azure][lnk-aad-admin], mas esse artigo se concentra principalmente nas funções **Administrador Global** e **Usuário/Membro do Domínio** como usadas por soluções pré-configuradas.

**Administrador global:** pode haver muitos administradores globais por locatário do AAD. Quando você cria um locatário do AAD, por padrão vira o administrador global desse locatário. O administrador global pode provisionar uma solução pré-configurada e recebe uma função **ADMINISTRADOR** para o aplicativo dentro do seu locatário do AAD. No entanto, se outro usuário no mesmo locatário do AAD criar um aplicativo, a função padrão recebida pelo administrador global será **SOMENTE LEITURA IMPLÍCITA**. Os administradores globais podem atribuir funções para aplicativos que usam o [portal clássico do Azure][lnk-classic-portal].

**Usuário/membro do domínio:** pode haver muitos usuários/membros do domínio por locatário do AAD. Um usuário do domínio pode provisionar uma solução pré-configurada por meio do site [azureiotsuite.com][lnk-azureiotsuite]. A função padrão recebida por ele para o aplicativo provisionado será **ADMINISTRADOR**. Ele pode criar um aplicativo usando o script build.cmd no repositório [azure-iot-solution][lnk-github-repo], mas a função padrão recebida será **SOMENTE LEITURA IMPLÍCITA**, pois ele não tem permissão para atribuir funções. Se outro usuário no locatário do AAD criar um aplicativo, receberá a função **SOMENTE LEITURA IMPLÍCITA** por padrão para o aplicativo. Ele não terá a capacidade de atribuir funções de aplicativos; portanto, não poderá adicionar usuários ou funções para usuários para um aplicativo, mesmo se o tiver provisionado.

**Usuário Convidado/Convidado:** pode haver muitos usuários convidados/convidados por locatário do AAD. Os usuários convidados têm um conjunto limitado de direitos no locatário do AAD. Como resultado, os usuários convidados não podem provisionar uma solução pré-configurada no locatário do AAD.

Para saber mais, consulte os recursos a seguir:

- [Criar ou editar usuários no AD do Azure][lnk-create-edit-users]
- [Atribuir funções de Aplicativo no AAD][lnk-assign-app-roles]

## Funções de administrador da assinatura do Azure

As funções de administrador do Azure controlam a capacidade de mapear uma assinatura do Azure para um locatário do AD.

Você poderá descobrir mais sobre as funções Coadministrador do Azure, Administrador de Serviços e Administrador da Conta no artigo [Como adicionar ou alterar o Coadministrador do Azure, o Administrador de Serviços e o Administrador da Conta][lnk-admin-roles].

## Funções de aplicativo

As funções do aplicativo controlam o acesso a dispositivos em sua solução pré-configurada.

Existem duas funções definidas e uma implícita definidas no aplicativo criado quando você provisiona uma solução pré-configurada.

-   **ADMINISTRADOR:** tem controle total para adicionar, gerenciar e remover dispositivos

-   **SOMENTE LEITURA:** tem a capacidade de exibir dispositivos

-   **SOMENTE LEITURA IMPLÍCITA:** é igual a Somente Leitura, mas é concedida a todos os usuários do seu locatário do AAD. Isso foi feito para conveniência durante o desenvolvimento. Você poderá remover essa função ao modificar o arquivo de origem [RolePermissions.cs][lnk-resource-cs].

### Alterando as funções de aplicativo

Você deve ser um administrador global do AAD para alterar funções para um usuário:

1. Vá para o [portal clássico do Azure][lnk-classic-portal]

2. Selecione **Active Directory**

3. Clique no nome do seu locatário do AAD

4. Clique em **Aplicativos**.

5. Se você não vir seu aplicativo na lista, alterne na lista suspensa **Mostrar** até **Aplicativos que minha empresa possui** e clique na marca de seleção.

6. Clique no nome do aplicativo que corresponda ao nome da solução pré-configurada.

7. Clique em **Usuários**.

8. Selecione o usuário para o qual você deseja alternar funções.

9. Clique no botão atribuir e na função que você deseja atribuir, clique na marca de seleção.

## Perguntas frequentes

### Sou um administrador de serviços e gostaria de alterar o mapeamento de diretório entre minha assinatura e um locatário do AAD específico. Como faço isso?

1. Vá para o [portal clássico do Azure][lnk-classic-portal], clique em **Configurações** na lista de serviços no lado esquerdo.

2. Selecione a assinatura para a qual você deseja alterar o mapeamento de diretórios.

3. Clique em **Editar Diretório**.

4. Selecione o **Diretório** que você deseja usar na lista suspensa. Clique na seta para frente.

5. Confirme o mapeamento de diretórios e os coadministradores afetados. Observe que, se você estiver movendo de outro diretório, todos os administradores do diretório original serão removidos.

### Sou um usuário/membro do domínio no locatário do AAD e criei uma solução pré-configurada. Como recebo uma função para o meu aplicativo?

Peça ao administrador global para atribuir a você um administrador global do locatário do AAD para obter permissões para atribuir funções a usuários ou peça ao administrador global para atribuir a você uma função. Se desejar alterar o locatário do AAD em que sua solução pré-configurada foi implantada, consulte a próxima pergunta.

### Como alternar o locatário do AAD ao qual minha solução pré-configurada de monitoramento remoto e o aplicativo estão atribuídos?

Você pode executar uma implantação de nuvem do <https://github.com/Azure/azure-iot-remote-monitoring> e reimplantar com um locatário do AAD recém-criado. Como você, por padrão, é um administrador global quando cria um novo locatário do AAD, terá o acesso para adicionar usuários e atribuir funções a eles.

1. Crie um novo diretório do AAD no [portal de Gerenciamento do Azure][lnk-classic-portal].

2. Vá para <https://github.com/Azure/azure-iot-remote-monitoring>.

3. Execute `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (por exemplo, `build.cmd cloud debug myRMSolution`)

4. Quando solicitado, defina a **tenantid** para seu locatário recém-criado em vez do locatário anterior.


### Quero alterar um Administrador de Serviços ou um Coadministrador quando o logon for feito com uma conta organizacional

Consulte o artigo de suporte [Alterando um Administrador de Serviços e u um Coadministrador quando o logon for feito com uma conta organizacional][lnk-service-admins].

### Por que vejo este erro? "Sua conta não tem as permissões adequadas para criar uma solução. Verifique com o administrador da conta ou tente com uma conta diferente."

Examine o diagrama a seguir:

![][img-flowchart]

**Por que estou vendo este erro quando tenho uma assinatura do Azure?** *Uma assinatura do Azure é necessária para criar soluções pré-configuradas. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos.*

Se você tiver certeza de que tem uma assinatura do Azure, valide o mapeamento do locatário para a sua assinatura e certifique-se de que o locatário correto tenha sido selecionado na lista suspensa. Se você tiver validado o locatário desejado corretamente, siga o diagrama acima e valide o mapeamento de sua assinatura e este locatário do AAD.

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-github-repo]: https://github.com/Azure/azure-iot-solution
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs

<!---HONumber=AcomDC_0316_2016-->