<properties
   pageTitle="Aplicar a criptografia de disco | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação da Central de Segurança do Azure **Aplicar criptografia de disco**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Aplicar a criptografia de disco

A Central de Segurança do Azure recomendará que você aplique a criptografia de disco se houver discos de VM do Windows ou Linux que não estejam criptografados usando o Azure Disk Encryption. A Criptografia de Disco permite que você criptografe os discos de VM IaaS do Windows e do Linux. A criptografia é recomendada para volumes de dados e do sistema operacional em sua VM.


A Criptografia de Disco aproveita o recurso de norma do setor [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) do Windows e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de dados e do sistema operacional e ajudar a proteger seus dados e atender a seus requisitos organizacionais e de segurança. A Criptografia de Disco é integrada ao [Cofre da Chave do Azure](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de Cofre da Chave, garantindo que todos os dados nos discos de VM sejam criptografados em repouso no [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/).

> [AZURE.NOTE] O Azure Disk Encryption tem suporte nos seguintes sistemas operacionais do Windows Server: Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. A criptografia de disco tem suporte nos seguintes operacionais de servidor Linux: Ubuntu, CentOS, SUSE e SLES (SUSE Linux Enterprise Server).

## Implementar a recomendação

1. Na folha **Recomendações**, selecione **Aplicar a criptografia de disco**.
2. Na folha **Aplicar a criptografia de disco**, você verá uma lista de máquinas virtuais para as quais a Criptografia de Disco é recomendada.
3. Siga as instruções para aplicar a criptografia a essas VMs.

![][1]

## Próximas etapas

Este documento mostrou como implementar a recomendação da Central de Segurança "Aplicar criptografia de disco". Para saber mais sobre a criptografia de disco, confira o seguinte:

- [Criptografia e gerenciamento de chaves com o Cofre da Chave do Azure](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (vídeo, 36 minutos e 39 segundos) – saiba como usar o gerenciamento de criptografia de disco para VMs IaaS e o Cofre da Chave do Azure para ajudar a proteger seus dados.
- [Azure Disk Encryption](../azure-security-disk-encryption.md) (documento) – saiba como habilitar a criptografia de disco para VMs do Windows e do Linux.

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como definir as políticas de segurança.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança.
- [Gerenciamento das recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png

<!---HONumber=AcomDC_0211_2016-->