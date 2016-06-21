<properties
   pageTitle="Atualizar certificados para um cluster do Azure | Microsoft Azure"
   description="Descreve como carregar um novo certificado no Cofre de Chaves do Azure e atualizar ou remover um certificado para um cluster do Service Fabric existente."
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
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Adicionar ou remover certificados para um cluster do Service Fabric no Azure

O Service Fabric permite que você especifique dois certificados, um principal e um secundário, quando você configura a segurança do certificado durante a criação do cluster. Por padrão, aquele que você especifica no momento da criação é o certificado principal. Após a criação do cluster, você pode adicionar um novo certificado como um secundário ou remover um certificado existente. Para obter mais informações sobre como o Service Fabric usa certificados X.509, leia [Cenários de segurança de Cluster](service-fabric-cluster-security.md).

>[AZURE.NOTE] Para um cluster seguro, você sempre precisará de pelo menos um certificado (primário ou secundário) válido (não revogado ou expirado) implantado ou não poderá acessar o cluster.

## Adicionar um certificado secundário
Para adicionar outro certificado como um secundário, você deve carregar o certificado em um Cofre de chaves do Azure e implantá-lo nas máquinas virtuais no cluster. Para obter informações adicionais, veja [Implantar certificados em VMs por meio de um cofre de chaves gerenciado pelo cliente](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

1. [Carregar um certificado X.509 no cofre de chaves](service-fabric-cluster-azure-secure-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault)
2. Entre no [portal do Azure](https://portal.azure.com/) e navegue até o recurso de clusters ao qual você deseja adicionar esse certificado.
3. Em **Configurações**, clique na configuração do certificado e insira a impressão digital do certificado secundário.
4. Clique em **Salvar**. Uma implantação será iniciada e, após a conclusão bem sucedida da implantação, você poderá usar o certificado principal ou secundário para realizar operações de gerenciamento no cluster.

![Captura de tela das impressões digitais do certificado no portal][SecurityConfigurations_02]

## Remover um certificado
Este é o processo para remover um certificado antigo para que o cluster não o use:

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até as configurações de segurança do cluster.
2. Remova um dos certificados.
3. Clique em **Salvar**, o que inicia uma nova implantação. Após a conclusão da implantação, o certificado que você removeu não poderá ser usado para se conectar ao cluster.

## Próximas etapas
Leia estes artigos para obter mais informações sobre gerenciamento de cluster:

- [Processo de atualização de Cluster de Malha do Serviço e as suas expectativas](service-fabric-cluster-upgrade.md)
- [Configurar o acesso baseado em funções para clientes](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0608_2016-->