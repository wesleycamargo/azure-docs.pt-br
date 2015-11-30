<properties
   pageTitle="Como tornar o cluster de Serviço de Malha seguro | Microsoft Azure"
   description="Como tornar o cluster de Serviço de Malha seguro Quais são as opções?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="chackdan"/>

# Como proteger um cluster de Malha de Serviço usando certificados.

Para configurar um cluster de malha de serviço seguro, você precisará de pelo menos um servidor / certificado X509. Que você, em seguida, carrega no cofre da chave do azure e usa no processo de criação de cluster descrito em [Processo de criação de Cluster de Malha de Serviço](service-fabric-cluster-creation-via-portal.md)

Há três etapas distintas

1. Adquirir o X509 certificado
2. Carregar o certificado no Cofre da Chave do Azure:
3. Fornecer o local e os detalhes do certificado para o processo de criação de cluster de malha de serviço.

Antes de entrarmos em detalhes, vamos obter algumas noções básicas dos cenários.

##  Quais são os cenários abordados?

A Malha de Serviço oferece segurança para os seguintes cenários:

1. Proteger um cluster para comunicação de nó para nó.
2. Proteger a comunicação de um cliente de malha com um nó específico no cluster
3. Controle de acesso com base em funções (RBAC) - Capacidade de restringir as operações de administração de operações de somente leitura no cluster para um conjunto de certificados.   

A Malha de Serviço utiliza os certificados do Servidor X509 que você especifica como parte das configurações de Tipo de nó quando você cria um cluster. Para obter uma visão geral rápida sobre o que esses certificados são e como você pode adquirir/criá-los, role até a parte inferior desta página.

 
## Adquirir o(s) certificado(s) X509

1. Para clusters que executam cargas de trabalho de produção, você deve usar um certificado x509 da [Autoridade de Certificação (CA)](https://en.wikipedia.org/wiki/Certificate_authority) assinado para proteger o cluster. Para obter detalhes sobre como obter esses certificados, vá para [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Para clusters que usados apenas para fins de teste, você pode optar por usar um certificado assinado automaticamente.


## Criando um certificado assinado automaticamente para fins de teste

Os detalhes sobre como criar um certificado assinado automaticamente estão em [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx)
    
Aqui está o PS usado para criar meus certificados de Teste, mas certifique-se de ler o documento acima para garantir que ele atenda às suas necessidades. ```
$password = Read-Host -AsSecureString 
``` ```
New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName ChackdanTestCertificate | Export-PfxCertificate -FilePath E:\MyCertificates\ChackdanTestCertificate.pfx -Password $password
```

**Observação** O DnsName <String> especifica um ou mais nomes DNS para colocar na extensão de nome alternativo do assunto do certificado quando um certificado a ser copiado não é especificado por meio do parâmetro CloneCert. O primeiro nome DNS também é salvo como o Nome da assunto. Se nenhum certificado de autenticação for especificado, o primeiro nome DNS também será salvo como o Nome do emissor.

## Carregando o certificado X509 no Cofre da Chave

Instruções sobre como carregar um certificado no cofre da chave no [link para a documentação do cofre da chave](https://azure.microsoft.com/documentation/articles/key-vault-get-started/).

Certifique-se de observar a URL do Cofre de Origem, a URL de certificado e a impressão digital do certificado. elas serão necessárias na configuração do cluster de Malha de Serviço seguro. Os dados que você precisa serão semelhantes ao seguinte



1. **ID do recurso da URL de Cofre da Chave/Cofre de Origem** : /subscriptions/6c653126-e4ba-42cd-a1dd-f7bf96af7a47/resourceGroups/chackdan-keyvault/providers/Microsoft.KeyVault/vaults/chackdan-kmstest
2. **URL para o local do Certificado no Cofre da Chave** : https://chackdan-kmstest.vault.azure.net:443/secrets/MyCert/dcf17bdbb86b42ad864e8e827c268431 
3. **Impressão digital do certificado** : 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A



##Configurando um cluster seguro 

Os certificados que precisam ser usados são especificados no nível NodeType sob as Configurações de segurança. Você precisa especificar isso para cada NodeType no cluster. Embora este documento explique como fazer isso usando o portal, você pode fazer o mesmo usando um modelo ARM.

![SecurityConfigurations\_01][SecurityConfigurations_01]

Parâmetros obrigatórios

- **Modo de segurança** certifique-se de selecionar 'x509 certificate'. ele indica ao serviço de malha que você pretende configurar um cluster seguro. 
- **Nível de proteção de cluster** consulte este [documento de Nível de proteção](https://msdn.microsoft.com/library/aa347692.aspx) para entender o que cada um desses valores significam. Apesar de permitimos três valores aqui - EncryptAndSign, Sign, None. É melhor manter o padrão de "EncryptAndSign", a menos que você saiba o que está fazendo.
- **Cofre de Origem** refere-se à ID de recurso do cofre de chave e deve estar no formato de ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```

- **Certificado URL** refere-se à URL de local no seu cofre da chave onde o certificado foi carregado, e deve estar no formato de ```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```
- **Impressão digital do certificado** refere-se à impressão digital do certificado, que pode ser encontrada no URL especificado anteriormente.

Parâmetros opcionais - você pode opcionalmente especificar certificados adicionais que as máquinas do cliente usam para executar operações no cluster. Por padrão, a impressão digital que você especificou nos parâmetros obrigatórios é adicionada à lista autorizada de impressões digitais que são permitidas conforme as operações do cliente.

Cliente de administração - essas informações são usadas para validar se o cliente que está conectando-se ao ponto de extremidade de gerenciamento de cluster está, de fato, apresentando a credencial correta para executar ações de administrador e somente de leitura no cluster. você pode especificar mais de um certificado que você deseja autorizar para operações de Administração.


- **Autorizar por** - indica para a malha de serviço se a mesma precisa buscar esse certificado usando o nome do assunto ou pela impressão digital. O uso do nome do assunto para autorizar é não uma boa prática de segurança, no entanto, permite mais flexibilidade.


- **Nome de assunto** só é necessário caso você tenha especificado que a autorização é pelo Nome do assunto
- **Impressão digital do emissor** isso possibilita um nível adicional de verificação que o servidor pode executar quando um cliente apresenta suas credenciais para o servidor.

Cliente de Somente Leitura - essas informações são usadas para validar se o cliente que está conectando-se ao ponto de extremidade de gerenciamento de cluster está, de fato, apresentando a credencial correta para executar ações de administrador e somente de leitura no cluster. você pode especificar mais de um certificado que você deseja autorizar para operações de somente leitura.


- **Autorizar por** - indica para a malha de serviço se a mesma precisa buscar esse certificado usando o nome do assunto ou pela impressão digital. O uso do nome do assunto para autorizar é não uma boa prática de segurança, no entanto, permite mais flexibilidade.

- **Nome de assunto** só é necessário caso você tenha especificado que a autorização é pelo Nome do assunto
- **Impressão digital do emissor** isso possibilita um nível adicional de verificação que o servidor pode executar quando um cliente apresenta suas credenciais para o servidor.


## Como atualizar os certificados no cluster

A malha de serviços permite que você especifique dois certificados, um principal e um secundário. O que você especificou no momento da criação é, por padrão, o principal Para adicionar outro certificado, você precisa implantar esse certificado para as VMs no cluster. Consulte o documento [Implantar certificados para VMs do cofre da chave gerenciado pelo cliente](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

Depois que a operação for concluída com êxito, vá para o portal ou por meio do ARM, indique na Malha de serviço que você possui um certificado secundário que pode ser usado também. Tudo o que você precisa é uma impressão digital.

No portal, navegue até o recurso de cluster para o qual você deseja adicionar esse certificado, clique na configuração de certificado e insira a impressão digital do certificado secundário e pressione salvar. Uma implantação será iniciada e após uma conclusão bem sucedida da implantação, você pode usar o certificado principal ou o secundário para realizar operações de gerenciamento no cluster.

![SecurityConfigurations\_02][SecurityConfigurations_02]

se você deseja remover um certificado agora, você pode fazer isso. Certifique-se de pressionar salvar após removê-lo, para que uma nova implantação seja iniciada. Depois que a implantação for concluída, o certificado que é removido não pode ser usado para conectar-se ao cluster. Para um cluster seguro, você sempre precisará de pelo menos um certificado válido (não revogado ou expirado) implantado ou você não poderá acessar o cluster.

Há um evento de diagnóstico que permite que você saiba se qualquer um dos certificados estão próximos de expirar.



## O que são certificados X509?

Os certificados digitais X509 são usados para autenticar clientes e servidores, criptografar e assinar mensagens digitalmente. Para saber mais detalhes sobre esses certificados, vá para [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx)

**Observação**

1. Os certificados usados em clusters que executam cargas de trabalho de produção devem ser criados usando um serviço de certificado do Windows Server configurado corretamente ou através de uma [Autoridade de Certificação (CA)](https://en.wikipedia.org/wiki/Certificate_authority) aprovada.
2. Nunca use certificados temporários ou de teste criados com ferramentas como MakeCert.exe em produção
3. Para clusters que usados apenas para fins de teste, você pode optar por usar um certificado assinado automaticamente. 


##O que são Certificados do servidor e Certificados do cliente?

**Certificados do Servidor/X509**

Os Certificados de servidor têm a tarefa principal de autenticar o servidor (nó) para clientes ou servidor (nó) para servidor (nó). Uma das verificações iniciais quando um cliente ou um nó autentica um nó é comparar o valor do nome comum no campo Assunto, para garantir que ele esteja presente na lista de nomes comuns permitidos que tenha sido configurada. Esse nome comum ou um dos nomes alternativos do assunto dos certificados deve estar presente na lista de nomes comuns permitidos.

O artigo a seguir descreve como gerar certificados com nomes alternativos de assunto (SAN). [http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351)
 
**Observação:** o campo assunto pode conter vários valores, cada um prefixado com uma inicialização para indicar o valor. Geralmente, a inicialização é "CN" para o nome comum, por exemplo, "CN = www.contoso.com". Também é possível deixar o campo Assunto em branco. Observe também o campo de Nome alternativo da assunto opcional; se usado, deve conter o nome comum do certificado e uma entrada por nome alternativo do assunto. Eles são inseridos como valores de Nome DNS.

Além disso, observe que o valor do campo Finalidades pretendidas do certificado deve incluir um valor apropriado, como "Autenticação do servidor" ou "Autenticação do cliente".

**Certificados do cliente**

Normalmente, os certificados de cliente não são emitidos por uma autoridade de certificação de terceiros. Em vez disso, o repositório Pessoal do local atual do usuário geralmente contém os certificados colocados lá por uma autoridade raiz, com uma finalidade de "Autenticação do cliente". O cliente pode usar esse certificado quando a autenticação mútua é necessária. Todas as operações de gerenciamento no cluster de malha de serviço exigem certificados de Servidor. Certificados de cliente não devem ser usados.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
- [Processo de atualização de Cluster de Malha do Serviço e as suas expectativas](service-fabric-cluster-upgrade.md)
- [Gerenciando seu aplicativo da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introdução ao modelo de Integridade da Malha de Serviço](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png

<!---HONumber=Nov15_HO4-->