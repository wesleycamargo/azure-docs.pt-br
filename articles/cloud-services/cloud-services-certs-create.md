<properties 
	pageTitle="Serviços de nuvem e certificados de gerenciamento | Microsoft Azure" 
	description="Saiba como criar e usar certificados com o Microsoft Azure" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/09/2015"
	ms.author="adegeo"/>

# Visão geral sobre certificados para os Serviços de Nuvem do Azure
Certificados são usados no Azure para os serviços de nuvem ([certificados de serviço](#what-are-service-certificates)) e para autenticação na API de gerenciamento ([certificados de gerenciamento](#what-are-management-certificates)). Este tópico fornece uma visão geral de ambos os tipos de certificados, como [criá-los](#create) e como [implantá-los](#deploy) no Azure.

Os certificados usados no Azure são certificados x.509 v3, que podem ser assinados por outro certificado confiável ou ser autoassinados. Um certificado autoassinado é assinado por seu próprio criador e, por isso, não é considerado confiável por padrão. A maioria dos navegadores pode ignorar isso. Certificados autoassinados devem ser usados somente por você mesmo ao desenvolver e testar serviços de nuvem.

Os certificados usados pelo Azure podem conter uma chave particular ou pública. Certificados têm uma impressão digital, que é um meio de identificá-los de maneira inequívoca. Essa impressão digital é usada no [arquivo de configuração](cloud-services-configure-ssl-certificate.md) do Azure para identificar qual certificado um serviço de nuvem deve usar.

## O que são certificados de serviço?
Certificados de serviço são anexados aos serviços de nuvem e possibilitam que a comunicação feita com e por meio deles seja segura. Por exemplo, se você implantou uma função web, convém fornecer um certificado que pode autenticar um ponto de extremidade HTTPS exposto. Certificados de serviço, definidos em sua definição de serviço, são implantados automaticamente na máquina virtual que está executando uma instância de sua função.

Você pode carregar certificados de serviço no portal clássico do Azure usando o portal clássico do Azure ou a API de Gerenciamento de Serviços. Certificados de serviço são associados a um serviço de nuvem específico e atribuídos a uma implantação no arquivo de definição de serviço.

Certificados de serviço podem ser gerenciados separadamente de seus serviços e podem ser gerenciados por indivíduos diferentes. Por exemplo, um desenvolvedor pode carregar um pacote de serviço que se refere a um certificado que um gerente de TI carregou anteriormente no Azure. Um gerente de TI pode gerenciar e renovar esse certificado alterando a configuração do serviço sem precisar carregar um novo pacote de serviço. Isso é possível porque o nome lógico do certificado e seu nome de repositório e local são especificados no arquivo de definição de serviço, enquanto a impressão digital do certificado é especificada no arquivo de configuração de serviço. Para atualizar o certificado, é necessário apenas carregar um novo certificado e alterar o valor da impressão digital no arquivo de configuração de serviço.

## O que são certificados de gerenciamento?
Certificados de gerenciamento permitem que você realize autenticação com a API de gerenciamento de serviço fornecida pelo Azure. Muitos programas e ferramentas (como o Visual Studio ou o SDK do Azure) usarão esses certificados para automatizar a configuração e a implantação de diversos serviços do Azure. Eles serviços não estão, na verdade, relacionados a serviços de nuvem.

>[AZURE.WARNING]Portanto, tenha cuidado! Esses tipos de certificados permitem que qualquer pessoa que os utilize para autenticação gerencie a assinatura à qual eles estão associados.

### Limitações
Há um limite de 100 certificados de gerenciamento por assinatura. Também há um limite de 100 certificados de gerenciamento para todas as assinaturas com a ID de usuário de um administrador de serviços específico. Se a ID de usuário do administrador da conta já foi usada para adicionar 100 certificados de gerenciamento e houver necessidade de mais certificados, você pode adicionar um coadministrador para adicionar esses certificados.

Antes de adicionar mais de 100 certificados, veja se é possível reutilizar um certificado existente. O uso de coadministradores adiciona uma complexidade potencial desnecessária ao processo de gerenciamento de certificados.


<a name="create"></a>
## Criar um Novo certificado autoassinado
Você pode usar qualquer ferramenta disponível para criar um certificado autoassinado, desde que ela esteja de acordo com estas configurações:

* Um certificado X.509.
* Contém uma chave privada.
* Criado para troca de chaves (arquivo. pfx).
* O nome de assunto deve corresponder ao domínio usado para acessar o serviço de nuvem.
    > Você não pode adquirir um certificado SSL para o domínio cloudapp.net (ou para qualquer um relacionado ao Azure); o nome de assunto do certificado deve corresponder ao nome do domínio personalizado usado para acessar o aplicativo. Por exemplo, **contoso.net** e não **contoso.cloudapp.net**.
* Mínimo de criptografia de 2048 bits.
* **Apenas Certificado de Serviço**: o certificado do cliente deve residir no repositório de certificados *Pessoal*.

Há duas maneiras fáceis de criar um certificado no Windows, com o utilitário `makecert.exe` ou o IIS.

### Makecert.exe

Esse utilitário é instalado com o Visual Studio 2013/2015. Trata-se de um utilitário de console que permite criar e instalar certificados. Se você iniciar o atalho **Prompt de Comando do Desenvolvedor para VS2015** criado quando você instala o Visual Studio, será exibido um prompt de comando com esta ferramenta no caminho.

    makecert -sky exchange -r -n "CN=[CertificateName]" -pe -a sha1 -len 2048 -ss My -sv [CertificateName].pvk [CertificateName].cer


### IIS (Serviços de Informações da Internet)

Há muitas páginas na Internet que tratam de como fazer isso com o IIS. [Este](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) é um ótimo site que descobri e que acho que explica bem.

### Java
Você pode usar o Java para [criar um certificado](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### Linux
[Este](..\virtual-machines\virtual-machines-linux-use-ssh-key.md) artigo descreve como criar certificados com SSH.

## Próximas etapas

[Carregue seu certificado de serviço no portal clássico do Azure](cloud-services-configure-ssl-certificate.md) (ou no [portal do Azure](cloud-services-configure-ssl-certificate-portal.md)) e [configure-o](cloud-services-xml-certs.md) para o serviço de nuvem.

Carregue um [certificado de API de gerenciamento](../azure-api-management-certs.md) no portal clássico do Azure.

>[AZURE.NOTE]O portal do Azure não usa certificados de gerenciamento para acessar a API, ele usa contas de usuário.

<!---HONumber=AcomDC_1203_2015-->