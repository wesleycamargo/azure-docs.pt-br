<properties urlDisplayName="Service Management" pageTitle="Como usar a API de gerenciamento de servi&ccedil;os (Python) - guia de recursos" metaKeywords="" description="Saiba como executar tarefas de gerenciamento de servi&ccedil;os comuns de forma program&aacute;tica no Python." metaCanonical="" services="cloud-services" documentationCenter="Python" title="Como usar o Gerenciamento de Servi&ccedil;os no Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo" />

# Como usar o Gerenciamento de Serviços no Python

Este guia mostra como executar tarefas de gerenciamento de serviços comuns de forma programática no Python. A classe **ServiceManagementService** no [SDK do Azure para Python][SDK do Azure para Python] oferece suporte a acesso programático para grande parte da funcionalidade relacionada ao gerenciamento de serviços que está disponível no [portal de gerenciamento][portal de gerenciamento] (como **criação, atualização e exclusão de serviços de nuvem, implantações, serviços de gerenciamento de dados, máquinas virtuais e grupos de afinidade**). Essa funcionalidade pode ser útil na criação de aplicativos que precisam de acesso programático ao gerenciamento de serviços.

## Sumário

-   [O que é o Gerenciamento de Serviços][O que é o Gerenciamento de Serviços]
-   [Conceitos][Conceitos]
-   [Como: Conectar-se ao gerenciamento de serviço][Como: Conectar-se ao gerenciamento de serviço]
-   [Como: Listar os locais disponíveis][Como: Listar os locais disponíveis]
-   [Como: Criar um serviço de nuvem][Como: Criar um serviço de nuvem]
-   [Como: Excluir um serviço de nuvem][Como: Excluir um serviço de nuvem]
-   [Como: Criar uma implantação][Como: Criar uma implantação]
-   [Como: Atualizar uma implementação][Como: Atualizar uma implementação]
-   [Como: Mover as implantações entre o preparo e a produção][Como: Mover as implantações entre o preparo e a produção]
-   [Como: Excluir a implantação][Como: Excluir a implantação]
-   [Como: Criar um serviço de armazenamento][Como: Criar um serviço de armazenamento]
-   [Como: Excluir um serviço de armazenamento][Como: Excluir um serviço de armazenamento]
-   [Como: Criar um grupo de afinidade][Como: Criar um grupo de afinidade]
-   [Como: Excluir um grupo de afinidade][Como: Excluir um grupo de afinidade]
-   [Como: Lista os sistemas operacionais disponíveis][Como: Lista os sistemas operacionais disponíveis]
-   [Como: Cria uma imagem de sistema operacional][Como: Cria uma imagem de sistema operacional]
-   [Como: Exclui uma imagem de sistema operacional][Como: Exclui uma imagem de sistema operacional]
-   [Como: Criar uma máquina virtual][Como: Criar uma máquina virtual]
-   [Como: Exclui uma máquina virtual][Como: Exclui uma máquina virtual]
-   [Próximas etapas][Próximas etapas]

## <a name="WhatIs"> </a>O que é o Gerenciamento de Serviços

A API de Gerenciamento de Serviços fornece acesso programático a grande parte da funcionalidade do gerenciamento de serviços disponível por meio do [Portal de Gerenciamento][portal de gerenciamento]. O SDK do Azure para Python permite que você gerencie os serviços de nuvem, as contas de armazenamento e os grupos de afinidade.

Para usar a API de Gerenciamento de Serviços, será necessário [criar uma conta do Azure][criar uma conta do Azure].

## <a name="Concepts"> </a>Conceitos

O SDK do Azure para Python encapsula a [API de Gerenciamento de Serviços do Azure][API de Gerenciamento de Serviços do Azure], que é uma API REST. Todas as operações da API são executadas por meio do SSL e mutuamente autenticadas usando certificados X.509 v3. O serviço de gerenciamento pode ser acessado em um serviço em execução no Azure ou diretamente pela Internet em qualquer aplicativo que possa enviar uma solicitação HTTPS e receber uma resposta HTTPS.

## <a name="Connect"> </a>Como: Conectar-se ao gerenciamento de serviços

Para conectar-se ao ponto de extremidade do Gerenciamento de Serviços, você precisa da ID de sua assinatura do Azure e um certificado de gerenciamento válido. Você pode obter sua ID de assinatura por meio do [portal de gerenciamento][portal de gerenciamento].

> [WACOM.NOTE] Uma vez que o SDK de Azure para Python versão 0.8.0, é agora impossível usar certificados criados com o OpenSSL quando for executado no Windows. Isso requer o Python 2.7.4 ou posterior.

### Certificados de gerenciamento no Windows (MakeCert)

Você pode criar um certificado de gerenciamento autoassinado em seu computador usando `makecert.exe`. Abra um **prompt de comando do Visual Studio** como **administrador** e use o seguinte comando, substituindo *AzureCertificate* pelo nome do certificado que você deseja usar.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

O comando criará o arquivo `.cer` e o instalará no repositório de certificados **Pessoal**. Para obter mais detalhes, consulte [Criar e carregar um certificado de gerenciamento para Azure (a página pode estar em inglês)][Criar e carregar um certificado de gerenciamento para Azure (a página pode estar em inglês)].

Depois que você tiver criado o certificado, você precisará carregar o arquivo `.cer` no Azure por meio da ação "Carregar" da guia "Configurações" do [portal de gerenciamento][portal de gerenciamento].

Depois que tiver obtido a ID da sua assinatura, criado um certificado e carregado o arquivo `.cer` para o Azure, você poderá conectar-se ao ponto de extremidade de gerenciamento do Azure, passando a id da assinatura e o local do certificado em seu repositório de certificados **Pessoal** para **ServiceManagementService** (novamente, substitua *AzureCertificate* pelo nome do seu certificado):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo acima, a classe `sms` é um objeto **ServiceManagementService**. A classe **ServiceManagementService** é a classe primária usada para gerenciar os serviços do Azure.

### Certificados de gerenciamento no Windows/Mac/Linux (OpenSSL)

Você também pode usar o [OpenSSL][OpenSSL] para criar o certificado de gerenciamento. Na verdade, você precisa criar dois certificados, um para o servidor (um arquivo `.cer`) e um para o cliente (um arquivo `.pem`). Para criar o arquivo `.pem`, execute:

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Para criar o certificado `.cer`, execute:

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Para obter mais informações sobre certificados do Azure, consulte [Gerenciando certificados no Azure (a página pode estar em inglês)][Gerenciando certificados no Azure (a página pode estar em inglês)]. Para obter uma descrição completa dos parâmetros do OpenSSL, consulte a documentação em <http://www.openssl.org/docs/apps/openssl.html>.

Depois de criar esses arquivos, você precisará carregar o arquivo `.cer` no Azure por meio da ação "Carregar" da guia "Configurações" do [portal de gerenciamento][portal de gerenciamento], e anotar o local onde você salvou o arquivo `.pem`.

Depois de ter obtido a ID de sua assinatura, criado um certificado e carregado o arquivo `.cer` no Azure, você pode conectar-se ao ponto de extremidade de gerenciamento do Azure passando a id da assinatura e o caminho para o arquivo `.pem` para **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo acima, a classe `sms` é um objeto **ServiceManagementService**. A classe **ServiceManagementService** é a classe primária usada para gerenciar os serviços do Azure.

## <a name="ListAvailableLocations"> </a>Como: Listar os locais disponíveis

Para listar os locais que estão disponíveis para hospedar serviços, use o método **listLocations**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Quando cria um serviço de nuvem, um serviço de armazenamento ou um grupo de afinidade, você precisa fornecer um local válido. O método **list\_Locations** sempre retornará uma lista atualizada dos locais disponíveis no momento. Na data da criação deste artigo, os locais disponíveis são:

-   Europa Ocidental
-   Sudeste Asiático
-   Ásia Oriental
-   Centro-Norte dos EUA
-   Norte da Europa
-   Centro-Sul dos Estados Unidos
-   Oeste dos EUA
-   Leste dos EUA

## <a name="CreateCloudService"> </a>Como: Criar um Serviço de Nuvem

Quando você cria um aplicativo e o executa no Azure, o código e a configuração, juntos, são chamados de [serviço de nuvem][serviço de nuvem] do Azure (conhecido como *serviço hospedado* em versões anteriores do Azure). O método **create\\hosted\\service** permite que você crie um novo serviço hospedado, fornecendo um nome de serviço hospedado (que deve ser exclusivo no Azure), um rótulo (automaticamente codificado em base64), uma descrição e um local. Você pode especificar um grupo de afinidade em vez de um local para o serviço.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    # You can either set the location or an affinity_group
    sms.create_hosted_service(name, label, desc, location)

Você pode listar todos os serviços hospedados para seu assinatura com o método **list\_hosted\_services**.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Se desejar obter informações sobre um determinado serviço hospedado, você pode fazê-lo passando o nome do serviço hospedado para o método **get\_hosted\_service\_properties**:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
    print('Location: ' + hosted_service.hosted_service_properties.location)
            

Após ter criado um serviço de nuvem, você pode implantar seu código ao serviço com o método **create\_deployment**.

## <a name="DeleteCloudService"> </a>Como: Excluir um serviço de nuvem

Você pode excluir um serviço de nuvem passando o nome do serviço para o método **delete\_hosted\_service**:

    sms.delete_hosted_service('myhostedservice')

Observe que para poder excluir um serviço, todas as implantações do serviço devem ser excluídas primeiro. (Veja [Como: Excluir uma implantação][Como: Excluir a implantação] para obter mais detalhes.)

## <a name="CreateDeployment"> </a>Como: Criar uma implantação

O método **create\_deployment** carrega um novo [pacote de serviço][pacote de serviço] e cria uma nova implantação no ambiente de preparo ou de produção. Os parâmetros para esse método são os seguintes:

-   **name**: o nome do serviço hospedado.
-   **deployment\_name**: o nome da implantação.
-   **slot**: Uma cadeia de caracteres indicando o slot `staging` ou `production`.
-   **package\_url**: a URL do pacote de implantação (um arquivo .cspgk). O arquivo do pacote deve ser armazenado em uma conta de Armazenamento de Blob do Azure sob a mesma assinatura que o serviço hospedado para o qual o pacote está sendo carregado. Você pode criar um pacote de implantação com os [cmdlets do PowerShell do Azure][cmdlets do PowerShell do Azure] ou com a [ferramenta de linha de comando cspack][ferramenta de linha de comando cspack].
-   **configuration**: o arquivo de configuração do serviço (arquivo .cscfg) codificado em base64.
-   **label**: o rótulo do nome do serviço hospedado (automaticamente codificado em base64).

O exemplo a seguir cria uma nova implantação `v1` para um serviço hospedado chamado `myhostedservice`:

    from azure import *
    from azure.servicemanagement import *
    import base64

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'
    slot = 'production'
    package_url = 'URL_for_.cspkg_file'
    configuration = base64.b64encode(open('path_to_cscfg', 'rb'))
    label = 'myhostedservice'

    result = sms.create_deployment(name, slot, deployment_name, package_url, label, configuration)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Observe no exemplo acima, que o status da operação **create\_deployment** pode ser recuperada passando o resultado retornado pelo **create\_deployment** ao método **get\_operation\_status**.

Você pode acessar às propriedades de implantação com os métodos **get\_deployment\_by\_slot** ou **get\_deployment\_by\_name** O exemplo a seguir recupera uma implantação especificando o slot de implantação. O exemplo também reitera por todas as instâncias da implantação:

    result = sms.get_deployment_by_slot('myhostedservice', 'production')

    print('Name: ' + result.name)
    print('Slot: ' + result.deployment_slot)
    print('Private ID: ' + result.private_id)
    print('Status: ' + result.status)
    print('Instances:')
    for instance in result.role_instance_list:
        print('Instance name: ' + instance.instance_name)
        print('Instance status: ' + instance.instance_status)
        print('Instance size: ' + instance.instance_size)

## <a name="UpdateDeployment"> </a>Como: Atualizar uma implementação

Uma implantação pode ser atualizada usando o método **change\_deployment\_configuration** ou o método **update\_deployment\_status**.

O método **change\_deployment\_configuration** permite carregar um novo arquivo de configuração de serviço (`.cscfg`), que alterará qualquer uma das várias configurações de serviço (incluindo o número de instâncias em uma implantação). Para obter mais informações, consulte [Esquema de configuração de serviço do Azure (.cscfg)][Esquema de configuração de serviço do Azure (.cscfg)]. O exemplo a seguir demonstra como carregar um novo arquivo de configuração de serviço:

    from azure import *
    from azure.servicemanagement import *
    import base64

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'
    configuration = base64.b64encode(open('path_to_cscfg', 'rb'))

    result = sms.change_deployment_configuration(name, deployment_name, configuration)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Observe no exemplo acima, que o status da operação **create\_deployment\_configuration** pode ser recuperada passando o resultado retornado pelo **create\_deployment\_configuration** ao método **get\_operation\_status**.

O método **update\_deployment\_status** permite que você defina o status de uma implantação como EXECUTANDO ou SUSPENSO. O exemplo a seguir demonstra como definir o status como EXECUTANDO para uma implantação chamada `v1` de um serviço hospedado chamado `myhostedservice`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    deployment_name = 'v1'

    result = update_deployment_status(name, deployment_name, 'Running')

## <a name="MoveDeployments"> </a>Como: Mover as implantações entre o preparo e a produção

O Azure fornece dois ambientes de implantação: de preparo e de produção. Normalmente, um serviço é implantado no ambiente de preparo para que seja testado antes da implantação do serviço para o ambiente de produção. Quando for a hora de promover o serviço em preparo para o ambiente de produção, você pode fazê-lo sem reimplantar o serviço. Isso pode ser feito alternando as implantações. (Para obter mais informações sobre como permutar implantações, consulte [Implantando um Serviço do Azure][Implantando um Serviço do Azure].)

O exemplo a seguir mostra como usar o método **swap\_deployment** para permutar duas implantações (com os nomes de implantação `v1` e `v2`). No exemplo, antes de chamar o método **swap\_deployment**, a implantação `v1` está no slot de produção e a implantação `v2` está no slot de preparo. Após chamar o método **swap\_deployment**, a implantação `v2` está em produção e a implantação `v1` está em preparo.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

## <a name="DeleteDeployment"> </a>Como: Excluir a implantação

Para excluir uma implantação, use o método **delete\_deployment**. O exemplo a seguir mostra como excluir uma implantação chamada `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Como: Criar um serviço de armazenamento

Um [serviço de armazenamento][serviço de armazenamento] fornece acesso a [Blobs][Blobs], [Tabelas][Tabelas] e [Filas][Filas] do Azure. Para criar um serviço de armazenamento, você precisa de um nome para o serviço (com 3 a 24 caracteres minúsculos e exclusivo no Azure), uma descrição, um rótulo (até 100 caracteres, automaticamente codificado em base64) e um local ou um grupo de afinidade. O exemplo a seguir mostra como criar um serviço de armazenamento especificando um local. Se quiser usar um grupo de afinidade, você precisará primeiro criar um grupo de afinidade (consulte [Como: Criar um grupo de afinidade][Como: Criar um grupo de afinidade]) e defini-lo com o parâmetro **affinity\_group**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Observe no exemplo acima, que o status da operação **create\_storage\_configuration** pode ser recuperada passando o resultado retornado pelo **create\_storage\_configuration** ao método **get\_operation\_status**.

Você pode listar suas contas de armazenamento e suas propriedades com o método **list\_storage\_services**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Affinity group: ' + account.storage_service_properties.affinity_group)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Como: Excluir um serviço de armazenamento

Você pode excluir um serviço de armazenamento passando o nome do serviço para o método **delete\_storage\_account**: A exclusão de um serviço de armazenamento excluirá todos os dados armazenados no serviço (blobs, tabelas e filas).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="CreateAffinityGroup"> </a>Como: Criar um grupo de afinidade

Um grupo de afinidade é um agrupamento lógico dos serviços do Azure que informa ao Azure para localizar os serviços para obter desempenho otimizado. Por exemplo, você pode criar um grupo de afinidade no local "Oeste dos Estados Unidos" e, em seguida, criar um [serviço de nuvem][Como: Criar um serviço de nuvem] nesse grupo de afinidade. Se você criar um serviço de armazenamento no mesmo grupo de afinidade, o Azure saberá colocá-lo no local "Oeste dos Estados Unidos" e otimizá-lo no data center para obter melhor desempenho com os serviços de nuvem no mesmo grupo de afinidade.

Para criar um grupo de afinidade, você precisa de um nome, de um rótulo (codificado automaticamente em base64) e local. Opcionalmente, você pode fornecer uma descrição:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myAffinityGroup'
    label = 'myAffinityGroup'
    location = 'West US'
    desc = 'my affinity group'

    sms.create_affinity_group(name, label, location, desc)

Depois de criar um grupo de afinidade, você pode especificar o grupo (em vez de um local) ao [criar um serviço de armazenamento][Como: Criar um serviço de armazenamento].

Você pode listar grupos de afinidade e inspecionar suas propriedades chamando o método **list\_affinity\_groups**:

    result = sms.list_affinity_groups()

    for group in result:
        print('Name: ' + group.name)
        print('Description: ' + group.description)
        print('Location: ' + group.location)
        print('')

## <a name="DeleteAffinityGroup"> </a>Como: Excluir um grupo de afinidade

Você pode excluir um grupo de afinidade passando o nome do grupo para o método **delete\_affinity\_group**. Observe que para poder excluir um grupo de afinidade, o grupo de afinidade deve estar desassociado de qualquer serviço (ou os serviços que usam o grupo de afinidade devem ser excluídos).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_affinity_group('myAffinityGroup')

## <a name="ListOperatingSystems"> </a>Como: Lista os sistemas operacionais disponíveis

Para listar os sistemas operacionais que estão disponíveis para hospedar serviços, use o método **list\_operating\_systems**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Como alternativa, você pode usar o método **list\_operating\_system\_families** que agrupa os sistemas operacionais por família:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Como: Cria uma imagem de sistema operacional

Para adicionar uma imagem do sistema operacional ao repositório de imagens, use o método **add\_os\_image**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Para listar as imagens do sistema operacional estão disponíveis, use o método **list\_os\_images**. Isso inclui todas as imagens da plataforma e imagens do usuário:

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Affinity group: ' + image.affinity_group)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Como: Exclui uma imagem de sistema operacional

Para excluir uma imagem de usuário, use o método **delete\_os\_image**.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Como: Criar uma máquina virtual

Para criar uma máquina virtual, você precisa primeiro criar um [serviço de nuvem][Como: Criar um serviço de nuvem]. Em seguida, criar a implantação da máquina virtual usando o método **create\_virtual\_machine\_deployment**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    # You can either set the location or an affinity_group
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-pt-br-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>Como: Excluir uma máquina virtual

Para excluir uma máquina virtual, primeiro você exclui a implantação usando o método **delete\_deployment**:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

O serviço de nuvem pode ser excluído usando o método **delete\_hosted\_service**:

    sms.delete_hosted_service(service_name='myvm')

## <a name="NextSteps"> </a>Próximas etapas

Agora que você já conhece os conceitos básicos do gerenciamento de serviços, siga estes links para fazer tarefas mais complexas.

-   Consulte a referência de MSDN: [Serviços de Nuvem][pacote de serviço]
-   Consulte a referência de MSDN: [Máquinas Virtuais][Máquinas Virtuais]

  [SDK do Azure para Python]: https://www.windowsazure.com/pt-br/develop/python/common-tasks/install-python/
  [portal de gerenciamento]: https://manage.windowsazure.com/
  [O que é o Gerenciamento de Serviços]: #WhatIs
  [Conceitos]: #Concepts
  [Como: Conectar-se ao gerenciamento de serviço]: #Connect
  [Como: Listar os locais disponíveis]: #ListAvailableLocations
  [Como: Criar um serviço de nuvem]: #CreateCloudService
  [Como: Excluir um serviço de nuvem]: #DeleteCloudService
  [Como: Criar uma implantação]: #CreateDeployment
  [Como: Atualizar uma implementação]: #UpdateDeployment
  [Como: Mover as implantações entre o preparo e a produção]: #MoveDeployments
  [Como: Excluir a implantação]: #DeleteDeployment
  [Como: Criar um serviço de armazenamento]: #CreateStorageService
  [Como: Excluir um serviço de armazenamento]: #DeleteStorageService
  [Como: Criar um grupo de afinidade]: #CreateAffinityGroup
  [Como: Excluir um grupo de afinidade]: #DeleteAffinityGroup
  [Como: Lista os sistemas operacionais disponíveis]: #ListOperatingSystems
  [Como: Cria uma imagem de sistema operacional]: #CreateVMImage
  [Como: Exclui uma imagem de sistema operacional]: #DeleteVMImage
  [Como: Criar uma máquina virtual]: #CreateVM
  [Como: Exclui uma máquina virtual]: #DeleteVM
  [Próximas etapas]: #NextSteps
  [criar uma conta do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/
  [API de Gerenciamento de Serviços do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee460799.aspx
  [Criar e carregar um certificado de gerenciamento para Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg551722.aspx
  [OpenSSL]: http://www.openssl.org/
  [Gerenciando certificados no Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg981929.aspx
  [serviço de nuvem]: http://windowsazure.com/pt-br/documentation/articles/cloud-services-what-is
  [pacote de serviço]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj155995.aspx
  [cmdlets do PowerShell do Azure]: https://www.windowsazure.com/pt-br/develop/php/how-to-guides/powershell-cmdlets/
  [ferramenta de linha de comando cspack]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg432988.aspx
  [Esquema de configuração de serviço do Azure (.cscfg)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758710.aspx
  [Implantando um Serviço do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433027.aspx
  [serviço de armazenamento]: https://www.windowsazure.com/pt-br/manage/services/storage/what-is-a-storage-account/
  [Blobs]: https://www.windowsazure.com/pt-br/develop/python/how-to-guides/blob-service/
  [Tabelas]: https://www.windowsazure.com/pt-br/develop/python/how-to-guides/table-service/
  [Filas]: https://www.windowsazure.com/pt-br/develop/python/how-to-guides/queue-service/
  [Máquinas Virtuais]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj156003.aspx
