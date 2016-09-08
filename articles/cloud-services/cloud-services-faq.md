<properties
	pageTitle="Perguntas frequentes sobre Serviços de Nuvem | Microsoft Azure"
	description="Perguntas frequentes sobre os Serviços de Nuvem."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="adegeo"/>

# Perguntas frequentes sobre Serviços de Nuvem
Este artigo responde a algumas perguntas frequentes sobre os Serviços de Nuvem do Microsoft Azure. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais sobre preços e suporte do Azure. Você também pode consultar a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

## Certificados

### Não é possível remover o certificado expirado

Azure impede a remoção de um certificado enquanto ele está em uso. É necessário excluir a implantação que usa o certificado ou atualizá-la com um certificado diferente ou renovado.

### Excluir um certificado expirado

Enquanto o certificado não está em uso, você pode usar o cmdlet do PowerShell [AzureCertificate remover](https://msdn.microsoft.com/library/azure/mt589145.aspx) para remover um certificado.

### Tenho certificados expirados denominados Gerenciamento de Serviço do Microsoft Azure para Extensões

Esses certificados são criados sempre que uma extensão é adicionada ao serviço de nuvem, como a extensão de área de trabalho remota. Esses certificados são usados apenas para criptografar e descriptografar a configuração privada da extensão. Não importa se esses certificados expiram. A data de validade não é verificada.

### Certificados que excluí continuam reaparecendo

Estes certificados continuam sendo excluídos provavelmente devido a uma ferramenta que você está usando, como o Visual Studio. Sempre que você se reconectar com uma ferramenta que está usando um certificado, ele será carregado novamente no Azure.

### Meus certificados continuam desaparecendo

Quando a instância de máquina virtual for reciclada, todas as alterações locais serão perdidas. Use um [tarefa de inicialização](cloud-services-startup-tasks.md) para instalar certificados na máquina virtual sempre que a função for iniciada.

### Onde devo instalar o certificado?

**Meu** Certificado de Aplicativo com chave privada (*.pfx, *.p12).

**AC** Todos os certificados intermediários ficam neste repositório (política e Sub ACs).

**RAIZ** O repositório de AC raiz, para que seu certificado de AC raiz seja inserido aqui.

## Solucionar problemas

### Eu não consigo reservar um IP em um serviço de nuvem de vários VIPs

Primeiro, certifique-se de que a instância de máquina virtual que você está tentando reservar o IP esteja ativada. Segundo, certifique-se de que você esteja usando IPs reservados para as implantações de preparo e produção. **Não** altere as configurações enquanto a implantação é atualizada.

<!---HONumber=AcomDC_0824_2016-->