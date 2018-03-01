---
title: "Solução de problemas do Azure Site Recovery para erros e assuntos de replicação do Azure para Azure | Microsoft Docs"
description: "Solucionando problemas de erros e problemas durante a replicação de máquinas virtuais do Azure para recuperação de desastres"
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: sujayt
ms.openlocfilehash: 7292948c40b184a58eb3e27aecac28e2227a29f8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Solucionar problemas de replicação de VM do Azure para o Azure

Este artigo descreve os problemas comuns no Azure Site Recovery na replicação e na recuperação de máquinas virtuais do Azure de uma região para outra região e explica como solucioná-los. Para obter mais informações sobre configurações com suporte, consulte a [matriz de suporte para replicar máquinas virtuais do Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de cota de recursos do Azure (código de erro 150097)
Sua assinatura deve ser habilitada para criar VMs do Azure na região de destino que você planeja usar como a região de recuperação de desastres. Além disso, sua assinatura deve ter uma cota suficiente habilitada para criar VMs de tamanho específico. Por padrão, a recuperação de Site escolhe o mesmo tamanho para a VM de destino como a VM de origem. Se o tamanho correspondente não estiver disponível, o tamanho mais próximo possível é escolhido automaticamente. Se não houver nenhum tamanho correspondente que dá suporte à configuração de VM de origem, essa mensagem de erro será exibida:

**Código de erro** | **Possíveis causas:** | **Recomendações**
--- | --- | ---
150097<br></br>**Mensagem**: Não foi possível habilitar a replicação para a máquina virtual VmName. | -Sua ID de assinatura não deve estar habilitada para criar quaisquer máquinas virtuais no local de região de destino.</br></br>-Sua ID de assinatura não deve estar habilitada ou não tem uma cota suficiente para criar tamanhos específicos de VM no local de região de destino.</br></br>-Um destino adequado tamanho da VM que corresponda a contagem de NIC (2) de VM de origem não foi encontrado para a ID da assinatura no local de região de destino.| Entre em contato com [suporte à cobrança do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) para habilitar a criação de VM para os tamanhos VM necessários no local de destino para a sua assinatura. Depois que ele é habilitado, repita a operação falhada.

### <a name="fix-the-problem"></a>Corrija o problema
Entre em contato com [suporte à cobrança do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) para habilitar a criação de VM para os tamanhos VM necessários no local de destino para a sua assinatura.

Se o local de destino tiver uma restrição de capacidade, desabilite a replicação e habilite para um local diferente em que sua assinatura tem uma cota suficiente para criar VMs dos tamanhos necessários.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificados de raiz confiável (código de erro 151066)

Se todos os certificados de raiz confiável mais recentes não estiverem presentes na VM, o seu trabalho "habilitar replicação" pode falhar. Sem os certificados, a autenticação e autorização de chamadas de serviço de recuperação de Site da VM falham. A mensagem de erro para o trabalho de recuperação de Site com falha em "habilitar replicação" será exibida:

**Código de erro** | **Possível causa** | **Recomendações**
--- | --- | ---
151066<br></br>**Mensagem**: Falha na configuração do Site Recovery. | Os certificados de raiz confiável obrigatórios usados para a autorização e autenticação não estão presentes no computador. | -Para uma VM que executa o sistema operacional Windows, certifique-se de que os certificados de raiz confiável estão presentes no computador. Para obter informações, consulte [Configurar raízes confiáveis e certificados não permitidos](https://technet.microsoft.com/library/dn265983.aspx).<br></br>-Para uma VM que executa o sistema operacional Linux, siga as orientações para certificados de raiz confiável publicadas pelo distribuidor de versão do sistema operacional Linux.

### <a name="fix-the-problem"></a>Corrija o problema
**Windows**

Instale todas as atualizações mais recentes do Windows na máquina virtual para que todos os certificados de raiz confiável estejam presentes no computador. Se você estiver em um ambiente desconectado, siga o processo de atualização padrão do Windows em sua organização para obter os certificados. Se os certificados necessários não estiverem presentes na VM, as chamadas para o serviço de recuperação de Site falharão por razões de segurança.

Siga o típico gerenciamento de atualização do Windows ou o certificado de processo de gerenciamento de atualização na sua organização para conseguir todos os certificados de raiz mais recentes e a lista de certificados revogados atualizada nas VMs.

Para verificar se o problema estiver resolvido, vá para login.microsoftonline.com em um navegador em sua VM.

**Linux**

Siga a recomendação fornecida pelo seu distribuidor Linux para conseguir os certificados de raiz mais recentes e a lista de certificados revogados atualizada na VM.

Como o SuSE Linux usa links simbólicos para manter uma lista de certificados, siga estes passos:

1.  Entre como um usuário raiz.

2.  Execute este comando para alterar o diretório.

      ``# cd /etc/ssl/certs``

3. Verifique se o certificado de autoridade de certificação raiz da Symantec está presente.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Se o certificado da autoridade de certificação raiz da Symantec não for encontrado, execute o comando a seguir para baixar o arquivo. Verifique se há algum erro e siga a ação recomendada para falhas de rede.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Verifique se o certificado de autoridade de certificação raiz da Baltimore está presente.

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Se o certificado de autoridade de certificação raiz da Baltimore não for encontrado, baixe-o.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. Verifique se o certificado DigiCert_Global_Root_CA está presente.

    ``# ls DigiCert_Global_Root_CA.pem``

8. Se o DigiCert_Global_Root_CA não for encontrado, execute os comandos a seguir para baixar o certificado.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. Execute o script de rehash para atualizar os hashes de entidade de certificado para os certificados baixados recentemente.

    ``# c_rehash``

10. Verifique se a entidade realiza hashes conforme symlinks são criados para os certificados.

    - Get-Help

      ``# ls -l | grep Baltimore``

    - Saída

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Get-Help

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Saída

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Get-Help

      ``# ls -l | grep DigiCert_Global_Root``

    - Saída

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

11. Criar uma cópia do arquivo VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem com o nome de arquivo b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

12. Criar uma cópia do arquivo Baltimore_CyberTrust_Root.pem com nome de arquivo 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Criar uma cópia do arquivo DigiCert_Global_Root_CA.pem com nome de arquivo 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Verifique se os arquivos estão presentes.  

    - Get-Help

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Saída

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para intervalos de IP ou URLs de recuperação de Site (código de erro 151037 ou 151072)

Para replicação de recuperação de Site para o trabalho, conectividade de saída para intervalos específicos de IP ou URLs é necessária da VM. Se a VM estiver atrás de um firewall ou usa regras de grupo de segurança de rede (NSG) para controlar a conectividade de saída, você poderá ver uma das seguintes mensagens de erro:

**Códigos de erro** | **Possíveis causas:** | **Recomendações**
--- | --- | ---
151037<br></br>**Mensagem**: Falha ao registrar a máquina virtual do Azure com o Site Recovery. | -Você está usando o NSG para controlar o acesso de saída na VM e os intervalos requeridos de IP não estão na lista de permissões para acesso de saída.</br></br>-Você está usando ferramentas de firewall de terceiros e os intervalos de IP necessário/URLs não estão na lista de permissões.</br>| -Se você estiver usando o proxy de firewall para controlar a conectividade de rede de saída na VM, certifique-se de que as URLs de pré-requisito ou intervalos de IP de datacenter estão na lista de permissões. Para obter informações, consulte [orientação de proxy do firewall](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>-Se você estiver usando o NSG para controlar a conectividade de rede de saída na VM, certifique-se de que as URLs de pré-requisito ou intervalos de IP de datacenter estão na lista de permissões. Para obter mais informações, veja [Grupo de Segurança de Rede](https://aka.ms/a2a-nsg-guidance).
151072<br></br>**Mensagem**: Falha na configuração do Site Recovery. | Não é possível estabelecer Conexão com pontos de extremidade de serviço de recuperação de Site. | -Se você estiver usando o proxy de firewall para controlar a conectividade de rede de saída na VM, certifique-se de que as URLs de pré-requisito ou intervalos de IP de datacenter estão na lista de permissões. Para obter informações, consulte [orientação de proxy do firewall](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>-Se você estiver usando o NSG para controlar a conectividade de rede de saída na VM, certifique-se de que as URLs de pré-requisito ou intervalos de IP de datacenter estão na lista de permissões. Para obter mais informações, veja [Grupo de Segurança de Rede](https://aka.ms/a2a-nsg-guidance).

### <a name="fix-the-problem"></a>Corrija o problema
Para permitir [as URLs necessárias](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou os [ intervalos de IP necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), siga as etapas no [documento de diretrizes de rede](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disco não encontrado na máquina (código de erro 150039)

Um novo disco anexado à máquina virtual deve ser inicializado.

**Código de erro** | **Possíveis causas:** | **Recomendações**
--- | --- | ---
150039<br></br>**Mensagem**: O disco de dados do Azure (DiskName) (DiskURI) com o LUN (número de unidade lógica) (LUNValue) não foi mapeado para um disco correspondente que está sendo relatado de dentro da máquina virtual que tem o mesmo valor de LUN. | - Um novo disco de dados foi anexado à VM, mas ele não foi inicializado.</br></br>- O disco de dados na máquina virtual não está relatando corretamente o valor do LUN no qual o disco foi conectado à máquina virtual.| Certifique-se de que os discos de dados são inicializados e, em seguida, repita a operação.</br></br>Para Windows: [Anexar e inicializar um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Para Linux: [Inicializar um novo disco de dados no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Corrija o problema
Certifique-se de que os discos de dados são inicializados e, em seguida, repita a operação:

- Para Windows: [Anexar e inicializar um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Para Linux: [Adicionar um novo disco de dados no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Se o problema persistir, contate o Suporte.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Não é possível visualizar a VM do Azure para seleção "habilitar a replicação"

Se VM que você deseja habilitar para replicação não é exibida, pode haver uma configuração obsoleta do Site Recovery na VM do Azure. A configuração obsoleta pode ser deixada em uma VM do Azure nos seguintes casos:

- A replicação foi habilitada para a VM do Azure usando a recuperação de Site e então excluída no cofre de recuperação de Site sem desabilitar explicitamente a replicação na máquina virtual.
- A replicação foi habilitada para a VM do Azure usando a recuperação de Site e então excluída no grupo de recursos contendo o cofre de recuperação de Site sem desabilitar explicitamente a replicação na máquina virtual.

### <a name="fix-the-problem"></a>Corrija o problema

Você pode usar [Remover configuração de script ASR obsoleta](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) e remover a configuração de recuperação de Site obsoleta na máquina virtual da Azure. Você deve ser capaz de ver a VM depois de remover a configuração obsoleta.

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>O estado de provisionamento da VM não é válido (código de erro 150019)

Para habilitar a replicação na VM, o estado de provisionamento deve ser **Com êxito**. Você pode verificar o estado da VM seguindo as etapas abaixo.

1.  Selecione o **Resource Explorer** de **Todos os Serviços** no Portal do Azure.
2.  Expanda a lista **Assinaturas** e selecione sua assinatura.
3.  Expanda a lista **ResourceGroups** e selecione o grupo de recursos da VM.
4.  Expanda a lista **Recursos** e selecione sua máquina virtual
5.  Marque o campo **provisioningState** na exibição Instância, no lado direito.

### <a name="fix-the-problem"></a>Corrija o problema

- Se **provisioningState** estiver com **Falha**, entre em contato com o suporte com detalhes para solucionar o problema.
- Se **provisioningState** for **Atualizando**, outra extensão poderá estar sendo implantada. Verifique se há outras operações em andamento na VM, aguarde a conclusão e tente realizar novamente o trabalho **Habilitar replicação** do Site Recovery.

## <a name="next-steps"></a>Próximas etapas
[Replicar as máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
