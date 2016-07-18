<properties
   pageTitle="Backup do Azure - Backup das VMs de IaaS do Azure com discos criptografados | Microsoft Azure"
   description="Saiba como o Backup do Azure trata dados criptografados usando o BitLocker ou dmcrypt durante o backup de VM de IaaS. Este artigo o prepara para as diferenças nas experiências de backup e restauração ao lidar com discos criptografados."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="cfreeman"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="07/01/2016"
   ms.author="markgal; jimpark"/>

# Lidando com discos criptografados durante o backup da VM

Para empresas que buscam criptografar seus dados de VM no Azure, a solução a ser usada é o Bitlocker no Windows ou o dmcrypt em computadores com Linux. Ambos são soluções de criptografia em nível de volume. Este artigo aborda os detalhes de configuração de backup para essas VMs do Azure e o impacto sobre os fluxos de trabalho de restauração devido aos dados criptografados.

## Como funciona o backup

A solução geral consiste em duas camadas - a camada de VM e a camada de Armazenamento.

1. A camada de VM lida com os dados, como vistos pelo sistema operacional convidado, e com os aplicativos em execução na máquina virtual. Ela também é a camada que executa o software de criptografia (Bitlocker ou dmcrypt), criptografando de modo transparente os dados nos volumes antes de gravá-los nos discos.
2. A camada de Armazenamento lida com os blobs de páginas e os discos anexados à VM. Ela não tem conhecimento dos dados que estão sendo gravados no disco, nem se eles são criptografados ou não. Essa é a camada na qual opera a funcionalidade de backup de VM.

![Como a criptografia do Bitlocker e o backup de VM do Azure coexistem](./media/backup-azure-vms-encryption/how-it-works.png)

Toda a criptografia de dados ocorre de maneira transparente e contínua na camada de VM. Portanto, os dados gravados nos blobs de páginas anexados à VM são dados criptografados. Quando o [Backup do Azure tira um instantâneo dos discos da VM e transfere os dados](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines), ele copia os dados criptografados presentes nos blobs de páginas.

## Componentes da solução

Há muitas partes dessa solução que precisam ser configuradas e gerenciadas corretamente para que isto funcione:

| Função | Software usado | Observações adicionais |
| -------- | ------------- | ------- |
| Criptografia | Bitlocker ou dmcrypt | Como a criptografia ocorre em uma camada *diferente* quando comparado ao Backup do Azure, não importa qual software de criptografia é usado. Dito isso, essa experiência foi validada apenas com o CloudLink usando Bitlocker e o dmcrypt.<br><br> Para criptografar os dados, é necessária uma chave. A chave também precisa ser mantida com segurança para garantir o acesso autorizado aos dados. |
| Gerenciamento de chaves | CloudLink SecureVM | A chave é essencial para criptografar ou descriptografar os dados. Sem a chave correta, os dados não podem ser recuperados. Isso se torna *incrivelmente* importante com:<br><li>Principais substituições<li>Retenção de longo prazo<br><br>Por exemplo, é possível que a chave usada para o backup dos dados há sete anos não seja a mesma chave usada hoje. Sem a chave de 7 anos atrás, será impossível usar os dados restaurados daquela época.|
| Backup de dados | Serviço de Backup do Azure | Use o Backup do Azure para fazer backup de suas VMs de IaaS do Azure usando o [portal de gerenciamento do Azure](http://manage.windowsazure.com) ou o PowerShell |
| Restauração de dados | Serviço de Backup do Azure | Use o Backup do Azure para restaurar discos ou uma VM inteira de um ponto de recuperação. Os dados não são descriptografados pelo Backup do Azure como parte da operação de restauração.|
| Descriptografia | Bitlocker ou dmcrypt | Para ler dados de um disco de dados restaurado ou uma VM restaurada, o software precisa da chave do software de Gerenciamento de Chaves. Sem a chave correta, os dados não podem ser descriptografados. |

> [AZURE.IMPORTANT]  O gerenciamento de chaves - incluindo substituição de chave - não é uma parte do Backup do Azure. Esse aspecto deve ser gerenciado de modo independente, mas é muito importante para a operação geral de backup/restauração.

### Cenários com suporte


| &nbsp; | Cofre de backup | Cofre dos Serviços de Recuperação |
| :-- | :-- | :-- |
| VMs V1 do Azure IaaS | Sim | Não |
| VMs V2 do Azure IaaS | N/D | Não |


## CloudLink SecureVM

[CloudLink SecureVM](http://www.cloudlinktech.com/choose-your-cloud/microsoft-azure/) é uma solução de criptografia de VM que pode ser usada para proteger seus dados de VM de IaaS do Azure. Há suporte para o CloudLink SecureVM para uso com o Backup do Azure.

### Informações de suporte

- CloudLink SecureVM versão 4.0 (compilação 21536.121 ou superior)
- Azure PowerShell, versão 0.9.8 ou posterior

### Gerenciamento de Chaves

Quando você precisar substituir ou alterar as chaves para as VMs que têm backups existentes, você precisa garantir que as chaves usadas no momento do backup estejam disponíveis. Uma maneira recomendada é fazer um backup do armazenamento de chaves ou de todo o sistema do SecureVM.

### Documentação e recursos

- [Guia de implantação - PDF](http://www.cloudlinktech.com/Azure/CL_SecureVM_4_0_DG_EMC_Azure_R2.pdf)
- [Implantando e usando o SecureVM - vídeo](https://www.youtube.com/watch?v=8AIRe92UDNg)

<!---HONumber=AcomDC_0706_2016-->