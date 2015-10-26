<properties 
    title="Elastic Scale Security Configurations" 
    pageTitle="Configurações de segurança de Escala Elástica" 
    description="Configurar certificados x409 para criptografia" 
    metaKeywords="Elastic Database certificates security" 
    services="sql-database" documentationCenter="" 
    manager="jhubbard" 
    authors="sidneyh"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/07/2015" 
    ms.author="sidneyh" />

# Configuração de segurança da divisão e mesclagem  

Para usar o serviço de divisão/mesclagem, você deve configurar corretamente a segurança. O serviço é parte do recurso de Dimensionamento Elástico do Banco de Dados SQL do Microsoft Azure. Para obter mais informações, consulte o [Tutorial de serviço de divisão/mesclagem de Escala Elástica](sql-database-elastic-scale-configure-deploy-split-and-merge.md)

## Configurando certificados

Os certificados são configurados de duas maneiras.

1. [Para configurar o certificado SSL](To#Configure#the#SSL#Certificate)
2. [Para configurar certificados de cliente](To#Configure#Client#Certificates) 

## Para obter certificados

Certificados podem ser obtidos por meio de autoridades de certificação públicas (CAs) ou do [Serviço de Certificado do Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Esses são os métodos preferenciais para obter certificados.

Se essas opções não estiverem disponíveis, você pode gerar **certificados autoassinados**.
 
## Ferramentas para gerar certificados

* [makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### Para executar as ferramentas

* De um Prompt de Comando do Desenvolvedor para o Visual Studio, consulte o [Prompt de Comando do Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) 

    Se instalado, vá para:

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Obter o WDK do [Windows 8.1: baixar kits e ferramentas](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## Para configurar o certificado SSL
É necessário um certificado SSL para criptografar a comunicação e autenticar o servidor. Escolha um dos três cenários abaixo mais aplicável e execute todas as suas etapas:

### Criar um Novo certificado autoassinado

1.    [Criar um certificado autoassinado](Create#a#Self-Signed#Certificate)
2.    [Criar o arquivo PFX para o certificado SSL autoassinado](Create#PFX#file#for#Self-Signed#SSL#Certificate)
3.    [Carregar certificado SSL para o serviço de nuvem](Upload#SSL#Certificate#to#Cloud#Service)
4.    [Atualizar o certificado SSL no arquivo de configuração de serviço](Update#SSL#Certificate#in#Service#Configuration#File)
5.    [Importar a autoridade de certificação SSL](Import#SSL#Certification#Authority)

### Para usar um certificado existente do repositório de certificados
1. [Exportar o certificado SSL do repositório de certificados](Export#SSL#Certificate#From#Certificate#Store)
2. [Carregar certificado SSL para o serviço de nuvem](Upload#SSL#Certificate#to#Cloud#Service)
3. [Atualizar o certificado SSL no arquivo de configuração de serviço](Update#SSL#Certificate#in#Service#Configuration#File)

### Para usar um certificado existente em um arquivo PFX

1. [Carregar certificado SSL para o serviço de nuvem](Upload#SSL#Certificate#to#Cloud#Service)
2. [Atualizar o certificado SSL no arquivo de configuração de serviço](Update#SSL#Certificate#in#Service#Configuration#File)

## Para configurar certificados de cliente
Certificados de cliente são necessários para autenticar solicitações ao serviço. Escolha um dos três cenários abaixo mais aplicável e execute todas as suas etapas:

### Desabilitar certificados de cliente
1.    [Desabilitar a autenticação baseada em certificado do cliente](Turn#Off#Client#Certificate-Based#Authentication)

### Emitir novos certificados de cliente autoassinados
1.    [Criar uma Autoridade de certificado autoassinado](Create#a#Self-Signed#Certification#Authority)
2.    [Carregar o Certificado de Autoridade de Certificação no serviço de nuvem](Upload#CA#Certificate#to#Cloud#Service)
3.    [Atualizar o Certificado de Autoridade de Certificação no arquivo de configuração de serviço](Update#CA#Certificate#in#Service#Configuration#File)
4.    [Emitir certificados de cliente](Issue#Client#Certificates)
5.    [Criar arquivos PFX para certificados de cliente](Create#PFX#files#for#Client#Certificates)
6.    [Importar o certificado de cliente](Import#Client#Certificate)
7.    [Copie as impressões digitais de certificados de cliente](Copy#Client#Certificate#Thumbprints)
8.    [Configurar clientes permitidos no arquivo de configuração de serviço](Configure#Allowed#Clients#in#the#Service#Configuration#File)

### Usar certificados de cliente existente
1.    [Localizar a chave pública de AC] (Localizar a #chave pública de #AC)
2.    [Carregar o Certificado de Autoridade de Certificação no serviço de nuvem](Upload#CA#certificate#to#cloud#service)
3.    [Atualizar o Certificado de Autoridade de Certificação no arquivo de configuração de serviço](Update#CA#Certificate#in#Service#Configuration#File)
4.    [Copie as impressões digitais de certificados de cliente](Copy#Client#Certificate#Thumbprints)
5.    [Configurar clientes permitidos no Arquivo de Configuração de Serviço] (Configurar #clientes #permitidos #no #Arquivo de #Configuração de Serviço)
6.    [Configurar a verificação de revogação de certificado do cliente](Configure#Client#Certificate#Revocation#Check)

## Endereços IP permitidos

Acesso aos pontos de extremidade de serviço pode ser restrito a intervalos específicos de endereços IP.

## Configurar a criptografia para o armazenamento

É necessário um certificado para criptografar as credenciais que são armazenadas no repositório de metadados. Escolha um dos três cenários abaixo mais aplicável e execute todas as suas etapas:

### Usar um novo certificado autoassinado

1.     [Criar um certificado autoassinado](Create#a#Self-Signed#Certificate)
2.     [Criar arquivo PFX de certificado de criptografia autoassinado](Create#PFX#file#for#Self-Signed#Encryption#Certificate)
3.     [Carregar o certificado de criptografia para o serviço de nuvem](Upload#Encryption#Certificate#to#Cloud#Service)
4.     [Atualizar o certificado de criptografia no arquivo de configuração de serviço](Update#Encryption#Certificate#in#Service#Configuration#File)

### Usar um certificado existente no repositório de certificados

1.     [Exportar o certificado de criptografia do repositório de certificados](Export#Encryption#Certificate#From#Certificate#Store)
2.     [Carregar o certificado de criptografia para o serviço de nuvem](Upload#Encryption#Certificate#to#Cloud#Service)
3.     [Atualizar o certificado de criptografia no arquivo de configuração de serviço](Update#Encryption#Certificate#in#Service#Configuration#File)

### Usar um certificado existente em um arquivo PFX

1.     [Carregar o certificado de criptografia para o serviço de nuvem](Upload#Encryption#Certificate#to#Cloud#Service)
2.     [Atualizar o certificado de criptografia no arquivo de configuração de serviço](Update#Encryption#Certificate#in#Service#Configuration#File)

## A configuração padrão

A configuração padrão nega todo os acessos ao ponto de extremidade HTTP. Esta é a configuração recomendada, pois as solicitações para esses pontos de extremidade podem carregar informações confidenciais, como credenciais de banco de dados. A configuração padrão permite todo os acessos ao ponto de extremidade HTTPS. Essa configuração pode ser mais restrita.

### Alterando a configuração

O grupo de regras de controle de acesso que são aplicadas e o ponto de extremidade são configurados na seção **<EndpointAcls>** no **arquivo de configuração de serviço**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

As regras em um grupo de controle de acesso são configuradas em uma seção de <AccessControl name=""> do arquivo de configuração do serviço.

O formato é explicado na documentação de listas de controle de acesso à rede. Por exemplo, para permitir que apenas IPs no intervalo 100.100.0.0 para 100.100.255.255 acessem o ponto de extremidade HTTPS, as regras teriam esta aparência:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## Negação de prevenção de serviço

Há dois mecanismos diferentes com suporte para detectar e impedir ataques de negação de serviço:

*    Restringir o número de solicitações simultâneas por host remoto (desativado por padrão)
*    Restringir a taxa de acesso por host remoto (em por padrão)

Eles se baseiam nos recursos documentados mais adiante na segurança de IP dinâmico no IIS. Ao alterar essa configuração Lembre-se dos seguintes fatores:

* O comportamento de proxies e dispositivos de conversão de endereços de rede sobre as informações do host remoto
* Cada solicitação para qualquer recurso na função web é considerada (por exemplo, carregamento de scripts, imagens, etc)

## Restringir o número de acessos simultâneos

As configurações que configuram esse comportamento são:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Alterar DynamicIpRestrictionDenyByConcurrentRequests como verdadeiro (true) para habilitar essa proteção.

## Restringindo a taxa de acesso

As configurações de configurar esse comportamento são:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## Configurando a resposta para uma solicitação negada

A configuração a seguir configura a resposta para uma solicitação negada:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Consulte a documentação de segurança de IP dinâmico no IIS para outros valores com suporte.

## Operações para configurar certificados de serviço
Este tópico é apenas para referência. Siga as etapas de configuração descritas em:

* Configurar o certificado SSL
* Configurar certificados de cliente

## Crie um certificado autoassinado
Execute:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Para personalizar:

*    -n com a URL do serviço. Há suporte para caracteres curinga ("CN=*.cloudapp .net") e nomes alternativos ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net").
*    -e com a data de validade do certificado Criar uma senha forte e especifique-a quando solicitado.

## Criar o arquivo PFX para o certificado SSL autoassinado

Execute:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Digite a senha e exporte o certificado com as seguintes opções: * Sim, exportar a chave privada * Exportar todas as propriedades estendidas

## Exportar o certificado SSL do repositório de certificados

* Localize o certificado
* Clique em Ações -> todas as tarefas -> Exportar...
* Exportar o certificado em um arquivo .PFX com as seguintes opções:
    * Sim, exportar a chave privada
    * Incluir todos os certificados no caminho de certificação, se possível *Exportar todas as propriedades estendidas

## Carregar certificado SSL para o serviço de nuvem

Carregar certificado com o arquivo .PFX existente ou gerado com o par de chaves SSL:

* Digite a senha que protege as informações da chave privadas

## Atualizar o certificado SSL no arquivo de configuração de serviço

Atualize o valor de impressão digital da seguinte configuração no arquivo de configuração de serviço com a impressão digital do certificado carregado para o serviço de nuvem:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## Importar a autoridade de certificação SSL

Siga estas etapas em todas as contas/computadores que se comunicarão com o serviço:

* Clique duas vezes no arquivo .CER no Windows Explorer
* Na caixa de diálogo do certificado, clique em Instalar certificado...
* Importar certificados para o armazenamento de Autoridades de Certificação Confiáveis

## Desabilitar a autenticação baseada em certificado do cliente

Há suporte somente para autenticação com base em certificado de cliente e desabilitá-la permitirá acesso público para os pontos de extremidade do serviço, a menos que outros mecanismos estão em vigor (por exemplo, Rede Virtual do Microsoft Azure).

Altere essas configurações para false no arquivo de configuração de serviço para desativar o recurso:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Em seguida, copie a mesma impressão digital do certificado SSL na configuração do Certificado de Autoridade de Certificação:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## Criar uma autoridade de certificado autoassinado
Execute as seguintes etapas para criar um certificado autoassinado para atuar como uma autoridade de certificação:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Para personalizá-lo

*    -e com a data de validade do certificado


## Localizar a chave pública da autoridade de certificação

Todos os certificados de cliente devem ter sido emitidos por uma autoridade de certificação confiável pelo serviço. Localize a chave pública para a autoridade de certificação que emitiu o cliente certificados a ser usado para autenticação para carregá-lo ao serviço de nuvem.

Se o arquivo com a chave pública não estiver disponível, você deve exportá-lo a partir do repositório de certificados:

* Localize o certificado
    * Pesquise um certificado de cliente emitido pela mesma autoridade de certificação
* Clique duas vezes no certificado.
* Selecione a guia do Caminho de Certificação na caixa de diálogo de Certificado.
* Clique duas vezes na entrada de AC no caminho.
* Anote as propriedades do certificado.
* Feche a caixa de diálogo do **Certificado**.
* Localize o certificado
    * Pesquise pela AC anotada acima.
* Clique em Ações -> todas as tarefas -> Exportar...
* Exportar o certificado em um .CER com as seguintes opções:
    * **Não, não exportar a chave privada**
    * Incluir todos os certificados no caminho de certificação, se possível.
    * Exportar todas as propriedades estendidas.

## Carregar o Certificado de Autoridade de Certificação no serviço de nuvem

Carregar certificado com o arquivo .CER existente ou gerado com a chave pública da autoridade de certificação.

## Atualizar o Certificado de Autoridade de Certificação no arquivo de configuração de serviço

Atualize o valor de impressão digital da seguinte configuração no arquivo de configuração de serviço com a impressão digital do certificado carregado para o serviço de nuvem:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Atualize o valor de configuração a seguir com a mesma impressão digital:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## Emitir certificados de cliente

Cada pessoa autorizada a acessar o serviço deve ter um certificado de cliente emitido para seu uso exclusivo e deve escolher que sua própria senha forte para proteger sua chave privada.

As etapas a seguir devem ser executadas no mesmo computador onde o Certificado de Autoridade de Certificação autoassinado foi gerado e armazenado:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalizando:

* -n com uma ID para o cliente será autenticado com o certificado
* -e com a data de validade do certificado
* MyID.pvk e MyID.cer com nomes de arquivo exclusivo para o certificado de cliente

Esse comando solicitará uma senha a ser criada e usada uma vez. Use uma senha forte.

## Criar arquivos PFX para certificados de cliente

Para cada certificado de cliente gerado, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizando:

    MyID.pvk and MyID.cer with the filename for the client certificate

Digite a senha e, em seguida, exporte o certificado com as seguintes opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas
* A pessoa para quem o certificado foi emitido deve escolher a senha de exportação

## Importar o certificado de cliente

Cada pessoa para quem um certificado cliente tiver sido emitido deve importar o par de chaves nas máquinas que ele usará para se comunicar com o serviço:

* Clique duas vezes no arquivo .PFX no Windows Explorer
* Importar o certificado para o pessoal armazenar pelo menos essa opção:
    * Incluir todas as propriedades estendidas marcadas

## Copie as impressões digitais de certificados de cliente
Cada pessoa para quem um certificado de cliente tiver sido emitido deve seguir estas etapas para obter a impressão digital do certificado que será adicionado ao arquivo de configuração de serviço: * Execute certmgr.exe * Selecione a guia Pessoal * Clique duas vezes no certificado de cliente a ser usado para autenticação * Na caixa de diálogo Certificado que é aberta, selecione a guia Detalhes * Certifique-se que Mostrar esteja exibindo Todos * Selecione o campo denominado Impressão Digital na lista * Copie o valor da impressão digital * * Exclua os caracteres Unicode não visíveis da frente do primeiro dígito * * Exclua todos os espaços

## Configurar clientes Permitidos no arquivo de configuração de serviço

Atualize o valor da configuração a seguir no arquivo de configuração de serviço com uma lista separada por vírgulas das impressões digitais dos certificados do cliente pode acessar o serviço:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## Configurar a verificação de revogação de certificado do cliente

A configuração padrão não verifica a autoridade de certificação para o status de revogação de certificado de cliente. Para ativar as verificações, se a autoridade de certificação que emitiu os certificados de cliente oferece suporte a essas verificações, altere a configuração a seguir com um dos valores definidos na enumeração X509RevocationMode:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## Criar arquivo PFX para certificados de criptografia autoassinados

Para um certificado de criptografia, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizando:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Digite a senha e exporte o certificado com as seguintes opções: * Sim, exportar a chave privada * Exportar todas as propriedades estendidas * Você precisará da senha para carregar o certificado para o serviço de nuvem.

## Exportar o certificado de criptografia do repositório de certificados

*    Localize o certificado
*    Clique em Ações -> todas as tarefas -> Exportar...
*    Exportar o certificado em um arquivo .PFX com as seguintes opções: 
  *    Sim, exportar a chave privada
  *    Incluir todos os certificados no caminho de certificação, se possível 
*    Exportar todas as propriedades estendidas

## Carregar o certificado de criptografia para o serviço de nuvem

Carregar certificado com o arquivo .PFX existente ou gerado com o par de chaves de criptografia:

* Digite a senha que protege as informações da chave privadas

## Atualizar o certificado de criptografia no arquivo de configuração de serviço

Atualize o valor de impressão digital das seguintes configurações no arquivo de configuração de serviço com a impressão digital do certificado carregado para o serviço de nuvem:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## Operações comuns de certificado

* Configurar o certificado SSL
* Configurar certificados de cliente

## Localize o certificado

Siga estas etapas:

1. Execute mmc.exe.
2. Arquivo -> Adicionar/Remover Snap-in...
3. Selecione **Certificados**.
4. Clique em **Adicionar**.
5. Escolha o local do repositório de certificados.
6. Clique em **Concluir**.
7. Clique em **OK**.
8. Expanda **Certificados**.
9. Expanda o nó do repositório de certificados.
10. Expanda o nó filho Certificado.
11. Selecione um certificado na lista.

## Exportar o certificado
No **Assistente para Exportação de Certificados**:

1. Clique em **Próximo**.
2. Selecione **Sim**, em seguida, **Exportar a chave privada**.
3. Clique em **Próximo**.
4. Selecione o formato de arquivo de saída desejado.
5. Marque as opções desejadas.
6. Marque a **Senha**.
7. Digite uma senha forte e confirme-a.
8. Clique em **Próximo**.
9. Digite ou procure um nome de arquivo onde o certificado deverá ser armazenado (use uma extensão .PFX).
10. Clique em **Próximo**.
11. Clique em **Concluir**.
12. Clique em **OK**.

## Importar certificado

No Assistente para importação de certificados:

1. Selecione o local do repositório.

    * Selecione **Usuário Atual** somente se processos em execução no atual usuário acessarão o serviço
    * Selecione **Computador Local** se outros processos no computador acessarão o serviço
2. Clique em **Próximo**.
3. Se estiver importando um arquivo, verifique seu caminho.
4. Se estiver importando um arquivo .PFX:
    1.     Digite a senha que protege as informações da chave privada
    2.     Selecione as opções de importação
5.     Selecione para “Colocar” os certificados no repositório a seguir
6.     Clique em **Procurar**.
7.     Selecione o repositório desejado.
8.     Clique em **Concluir**.
       
	* Se o repositório da autoridade de certificação raiz confiável foi escolhido, clique em **Sim**.
9.     Clique em **OK** em todas as janelas de diálogo.

## Carregar um certificado

No [Portal de Gerenciamento do Azure](http://portal.azure.com/).

1. Selecione os **Serviços de nuvem**.
2. Selecione o serviço de nuvem.
3. Na parte superior do menu, clique em **Certificados**.
4. Na barra inferior, clique em **Carregar**.
5. Selecione o arquivo de certificado.
6. Se for um arquivo .PFX, digite a senha da chave privada.
7. Depois de concluído, copie a impressão digital do certificado da nova entrada na lista.

## Outras considerações de segurança
 
As configurações de SSL descritas neste documento criptografar a comunicação entre o serviço e seus clientes quando o ponto de extremidade HTTPS é usado. Isso é importante já que as credenciais para acesso ao banco de dados e potencialmente outras informações confidenciais estão contidos na comunicação. No entanto, observe que o serviço persista status interno, incluindo credenciais, em suas tabelas internas no banco de dados SQL do Microsoft Azure que você forneceu para o armazenamento de metadados em sua assinatura do Microsoft Azurre. Esse banco de dados foi definido como parte da seguinte configuração no arquivo de configuração de serviço (arquivo .CSCFG):

    <Setting name="ElasticScaleMetadata" value="Server=…" />

As credenciais armazenadas neste banco de dados são criptografadas. No entanto, como uma prática recomendada, certifique-se de que funções da web e de trabalho de suas implantações de serviço sejam atualizadas e protegidas, visto que elas têm acesso ao banco de dados de metadados e o certificado usado para criptografia e descriptografia de credenciais armazenadas.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!---HONumber=Oct15_HO3-->