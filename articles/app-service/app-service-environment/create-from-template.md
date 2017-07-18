---
title: "Criando um Ambiente do Serviço de Aplicativo do Azure usando um modelo do Resource Manager"
description: "Explica como criar um Ambiente do Serviço de Aplicativo do Azure ILB ou Externo usando um modelo do Resource Manager"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 370ab7a32634f04b4776a2a9a84ecaacf11ea617
ms.contentlocale: pt-br
ms.lasthandoff: 06/26/2017

---
# <a name="how-to-create-an-ase-using-azure-resource-manager-templates"></a>Como criar um ASE usando modelos do Azure Resource Manager

## <a name="overview"></a>Visão geral
ASEs (Ambientes do Serviço de Aplicativo) podem ser criados com um ponto de extremidade acessível pela Internet ou um ponto de extremidade em um endereço interno na VNet (Rede Virtual) do Azure.  Quando criado com um ponto de extremidade interno, esse ponto de extremidade é fornecido por um componente do Azure chamado ILB (balanceador de carga interno).  O ASE com um ponto de extremidade público é chamado um ASE Externo e o ASE em um endereço IP interno é chamado um ASE ILB.  

Um ASE pode ser criado usando o portal do Azure ou com modelos do Azure Resource Manager.  Este artigo explica as etapas e a sintaxe necessárias para criar um ASE Externo ou um ASE ILB com modelos do Azure Resource Manager.  Para saber mais sobre como criar um ASE no portal, comece [Criando um ASE Externo][MakeExternalASE] ou [Criando um ASE ILB][MakeILBASE].

Ao criar um ASE no portal, você pode optar por criar a VNet ao mesmo tempo ou selecionar uma VNet pré-existente na qual implantar.  Ao criar com base em um modelo, você deve começar com: 
* Uma VNet do Resource Manager
* Uma sub-rede nessa VNet.  É recomendável que a sub-rede do ASE seja um /25 com 128 endereços para acomodar o crescimento futuro.  Isso não pode ser alterado após a criação do ASE
* A ID do recurso da VNET.  Obtenha isso no portal do Azure nas Propriedades da Rede Virtual do Azure
* a assinatura na qual você deseja implantar
* O local em que você deseja implantar

Para automatizar a criação do ASE:

1. Crie o ASE com base em um modelo.  Se você estiver criando um ASE Externo, não haverá nada mais a fazer nesta etapa.  Se estiver criando um ASE ILB, haverá algumas coisas extras a serem feitas
2. Depois que o ASE ILB for criado, um certificado SSL correspondente ao domínio do ASE ILB será carregado.
3. O certificado SSL carregado é atribuído ao ASE ILB como seu certificado SSL “padrão”.  Esse certificado SSL será usado para o tráfego SSL dos aplicativos no ASE ILB quando eles são endereçados utilizando o domínio-raiz comum atribuído ao ASE (por exemplo, https://someapp.mycustomrootcomain.com)


## <a name="creating-the-ase"></a>Criando o ASE
Um modelo do Azure Resource Manager de exemplo que cria um ASE e seu arquivo de parâmetros associado, estão disponíveis no GitHub [aqui][quickstartasev2create].

Se você desejar criar um ASE ILB, deverá usar os modelos do Resource Manager [aqui][quickstartilbasecreate].  Eles atendem a esse caso de uso.  A maioria dos parâmetros no arquivo *azuredeploy.parameters.json* é comum à criação de ASEs ILB e ASEs Externos.  A lista abaixo, ao criar um ASE ILB, chama parâmetros de anotação especial ou exclusivos:

* *interalLoadBalancingMode*: na maioria dos casos, define para 3, o que significa que o tráfego HTTP/HTTPS nas portas 80/443 e as portas do canal de dados/controle atendidas pelo serviço FTP no ASE serão associados a um endereço interno da rede virtual alocada do ILB.  Se essa propriedade for definida como 2, somente as portas relacionadas ao serviço FTP (canais de controle e de dados) serão associadas a um endereço ILB, ao passo que o tráfego HTTP/HTTPS permanecerá no VIP público.
* *dnsSuffix*: esse parâmetro define o domínio-raiz padrão que será atribuído ao ASE.  A variação pública do Serviço de Aplicativo do Azure, o domínio de raiz padrão para todos os aplicativos Web, é *azurewebsites.net*.  No entanto, como um ASE ILB é interno na rede virtual do cliente, não faz sentido usar o domínio de raiz padrão do serviço público.  Em vez disso, um ASE ILB deve ter um domínio de raiz padrão que faça sentido para uso dentro da rede virtual interna da empresa.  Por exemplo, uma empresa hipotética Contoso pode usar um domínio raiz padrão *internal-contoso.com* para aplicativos que se destinam apenas a serem resolvidos e acessados na rede virtual da Contoso. 
* *ipSslAddressCount*: esse parâmetro é automaticamente padronizado para um valor 0 no arquivo *azuredeploy.json* porque os ASEs ILB têm apenas um único endereço ILB.  Não há nenhum endereço IP SSL explícito para um ASE ILB e, portanto, o pool de endereços IP SSL para um ASE ILB deve ser definido como zero. Caso contrário, ocorrerá um erro de provisionamento. 

Depois que o arquivo *azuredeploy.parameters.json* for preenchido, o ASE poderá ser criado usando o trecho de código do PowerShell a seguir.  Altere os CAMINHOS do arquivo para corresponder ao local em que os arquivos do modelo do Azure Resource Manager estão localizados no seu computador.  Além disso, lembre-se de fornecer seus próprios valores para o nome de implantação do Azure Resource Manager e para o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois que o modelo do Azure Resource Manager for enviado, levará algumas horas para que o ASE seja criado.  Depois de concluída a criação, o ASE será mostrado na experiência do usuário do portal na lista de Ambientes do Serviço de Aplicativo da assinatura que disparou a implantação.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Carregando e configurando o certificado SSL "Padrão"
Quando o ILB ASE é criado, um certificado SSL deve ser associado ao ASE como o certificado SSL “padrão” usado para estabelecer conexões SSL com aplicativos.  Continuando com o exemplo hipotético da Contoso Corporation, se o sufixo DNS padrão do ASE for *internal-contoso.com*, então, uma conexão com *https://some-random-app.internal-contoso.com* irá requerer um certificado SSL válido para **.internal-contoso.com*. 

Há várias maneiras de obter um certificado SSL válido, incluindo CAs internos, adquirir certificado de um emissor externo e usar um certificado autoassinado.  Independentemente da origem do certificado SSL, os seguintes atributos de certificado devem ser configurados corretamente:

* *Assunto*: esse atributo deve ser definido para **.domínio-raiz-aqui.com*
* *Nome Alternativo da Entidade*: esse atributo deve incluir **.your-root-domain-here.com* e **.scm.your-root-domain-here.com*.  O motivo para a segunda entrada é que as conexões SSL para o site SCM/Kudu associado a cada aplicativo serão feitas usando um endereço do formulário *nome-do-aplicativo.scm.seu-domínio-raiz-aqui.com*.

Com um certificado SSL válido em mãos, são necessárias mais duas etapas preparatórias.  O certificado SSL deve ser convertido/salvo como um arquivo.pfx.  Lembre-se de que o arquivo .pfx deve incluir todos os certificados intermediários e raiz e também precisa ser protegido com uma senha.

Em seguida, o arquivo .pfx resultante deve ser convertido em uma cadeia de caracteres de base64 porque o certificado SSL será carregado usando um modelo do Azure Resource Manager.  Já que os modelos do Azure Resource Manager são arquivos de texto, o arquivo .pfx deve ser convertido em uma cadeia de caracteres de base64 para poder ser incluída como um parâmetro do modelo.

O trecho de código do Powershell abaixo mostra um exemplo de como gerar um certificado autoassinado, exportar o certificado como um arquivo .pfx, converter o arquivo .pfx em uma cadeia de caracteres codificada em base64 e salvar essa cadeia de caracteres em um arquivo separado.  O código do PowerShell para a codificação de base64 foi adaptado do [Blog de Scripts do PowerShell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Depois que o certificado SSL for gerado com êxito e convertido em uma cadeia de caracteres codificada em base64, o modelo do Azure Resource Manager de exemplo no GitHub para [configurar o certificado SSL padrão][quickstartconfiguressl] poderá ser usado.

Os parâmetros no arquivo *azuredeploy.parameters.json* estão listados abaixo:

* *appServiceEnvironmentName*: o nome do ASE ILB sendo configurado.
* *existingAseLocation*: a cadeia de caracteres de texto que contém a região do Azure onde o ASE ILB foi implantado.  Por exemplo: "Centro-Sul dos EUA".
* *pfxBlobString*: a representação de cadeia de caracteres codificada em base 64 do arquivo .pfx.  Usando o trecho de código mostrado anteriormente, copie a cadeia de caracteres contida no "exportedcert.pfx.b64" e cole-a como o valor do atributo *pfxBlobString* .
* *password*: a senha usada para proteger o arquivo .pfx.
* *certificateThumbprint*: a impressão digital do certificado.  Se você recuperar esse valor do Powershell (por exemplo, *$certificate.Thumbprint* do trecho de código anterior), poderá usar o valor como estiver.  No entanto se você copiar o valor da caixa de diálogo Certificado Windows, lembre-se de retirar os espaços estranhos.  O *certificateThumbprint* deve ser semelhante a: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: um identificador da cadeia de caracteres amigável de sua escolha usado para identificar o certificado.  O nome é usado como parte do identificador exclusivo do Azure Resource Manager para a entidade *Microsoft.Web/certificates* que representa o certificado SSL.  O nome **deve** terminar com o seguinte sufixo:  \_seuNomeASEAqui_InternalLoadBalancingASE.  Esse sufixo é usado pelo portal como um indicador de que o certificado é usado para proteger um ASE habilitado por ILB.

Um exemplo abreviado de *azuredeploy.parameters.json* é mostrado abaixo:

    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Quando o arquivo *azuredeploy.parameters.json* tiver sido preenchido, o certificado SSL padrão poderá ser configurado usando o trecho de código do Powershell a seguir.  Altere os CAMINHOS do arquivo para corresponder ao local em que os arquivos do modelo do Azure Resource Manager estão localizados no seu computador.  Além disso, lembre-se de fornecer seus próprios valores para o nome de implantação do Azure Resource Manager e para o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois que o modelo do Azure Resource Manager for enviado, levará aproximadamente quarenta minutos por front-end do ASE para aplicar a alteração.  Por exemplo, com um ASE padrão dimensionado usando dois front-ends, o modelo levará aproximadamente uma hora e vinte minutos para concluir.  Enquanto o modelo estiver em execução, o ASE não poderá ser dimensionado.  

Quando o modelo for concluído, os aplicativos no ASE ILB poderão ser acessados via HTTPS e as conexões serão protegidas usando o certificado SSL padrão.  O certificado SSL padrão será usado quando aplicativos no ASE ILB forem endereçados utilizando uma combinação de nome do aplicativo com o nome de host padrão.  Por exemplo *https://mycustomapp.internal-contoso.com* usaria o certificado SSL padrão para **.internal-contoso.com*.

No entanto, assim como os aplicativos em execução no serviço público multilocatário, os desenvolvedores podem também configurar nomes de host personalizados para aplicativos individuais e configurar associações de certificado SSL SNI exclusivas para aplicativos individuais.  

## <a name="asev1"></a>ASEv1 ##
O Ambiente do Serviço de Aplicativo tem duas versões: ASEv1 e ASEv2. As informações anteriores tiveram como foco o ASEv2. Esta seção mostra as diferenças entre o ASEv1 e o ASEv2.

No ASEv1, você precisa gerenciar todos os recursos manualmente. Isso inclui os front-ends, as funções de trabalho e os endereços IP usados para o SSL baseado em IP. Antes de poder escalar horizontalmente o plano do Serviço de Aplicativo, primeiro você precisa escalar horizontalmente o pool de trabalho no qual você deseja hospedá-lo.

O ASEv1 usa um modelo de preço diferente do ASEv2. No ASEv1, você precisa pagar por cada núcleo alocado. Isso inclui os núcleos usados para front-ends ou funções de trabalho que não hospedam nenhuma carga de trabalho. No ASEv1, o tamanho máximo de escala padrão de um Ambiente do Serviço de Aplicativo é de 55 hosts no total. Isso inclui funções de trabalho e front-ends. A vantagem do ASEv1 é que ele pode ser implantado em uma rede virtual clássica, bem como em uma rede virtual do Resource Manager. Saiba mais sobre o ASEv1 aqui: [Introdução ao Ambiente do Serviço de Aplicativo v1][ASEv1Intro]

Se desejar criar um ASEv1 usando um modelo do Resource Manager, comece aqui em [Criando um ASE v1 ILB com um modelo do Resource Manager][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: ../../app-service-web/app-service-app-service-environment-create-ilb-ase-resourcemanager.md

