As Máquinas Virtuais do Azure têm suporte para a anexação de vários discos de dados. Este artigo descreve a escalabilidade e metas de desempenho para os discos de dados de uma VM. Use essas metas para ajudar a decidir o número e o tipo de disco que você precisa para atender aos seus requisitos de desempenho e capacidade. 

> [!IMPORTANT]
> Para obter o desempenho ideal, limite a quantidade de discos altamente utilizados anexados à máquina virtual para evitar possíveis limitações. Se todos os discos conectados não forem altamente usados ao mesmo tempo, então a máquina virtual pode dar suporte a um número maior de discos.

* **Para o Azure Managed Disks:** 

> | Recurso | Limite padrão | Limite máximo |
> | --- | --- | --- |
> | Managed Disks Standard | 10.000 | 50.000 |
> | Managed Disks SSD Standard | 10.000 | 50.000 |
> | Managed Disks Premium | 10.000 | 50.000 |
> | Instantâneos de Standard_LRS | 10.000 | 50.000 |
> | Instantâneos de Standard_ZRS | 10.000 | 50.000 |
> | Instantâneos Premium_LRS | 10.000 | 50.000 |
> | Imagem Gerenciada | 10.000 | 50.000 |

* **Para contas de armazenamento padrão:** uma conta de armazenamento padrão tem uma taxa de solicitação total máxima de 20.000 IOPS. O total de IOPS em todos os discos da máquina virtual de uma conta de armazenamento padrão não deve exceder esse limite.
  
    Basicamente, você calcula o número de discos altamente utilizados compatíveis com uma conta de armazenamento padrão com base no limite da taxa de solicitação. Por exemplo, para uma VM da Camada Basic, o número máximo de discos altamente utilizados é de aproximadamente 66 (20.000/300 IOPS por disco) e para uma VM da Camada Standard é de aproximadamente 40 (20.000/500 IOPS por disco). 

* **Para contas de armazenamento premium:** uma conta de armazenamento premium tem uma taxa de transferência total máxima de 50 Gbps. A taxa de transferência total de todos os discos da VM não deve exceder esse limite.

