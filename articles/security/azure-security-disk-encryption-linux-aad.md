---
title: Azure Disk Encryption com VMs de IaaS do Linux de aplicativo do Azure AD (versão anterior) | Microsoft Docs
description: Este artigo fornece instruções sobre como habilitar as VMs da IaaS do Microsoft Azure Disk Encryption para Linux.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 95695b12-0218-4ebf-a0dd-21de52787477
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2018
ms.author: mstewart
ms.openlocfilehash: f711e5acb37eeb45cc2285b64b72102bfe2f44e6
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889052"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms-previous-release"></a>Habilitar Azure Disk Encryption para VMs de IaaS do Linux (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo do Azure AD para habilitar a criptografia de disco de VM. Com a nova versão, você não precisa fornecer credenciais do Azure AD durante a etapa de habilitação de criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo do Azure AD, usando a nova versão. Para exibir instruções para habilitar a criptografia de disco de VM usando a nova versão, veja [Azure Disk Encryption para VMs do Linux](azure-security-disk-encryption-linux.md). As VMs que já foram criptografadas com os parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.**

Você pode habilitar muitos cenários de criptografia de disco, e as etapas podem variar de acordo com o cenário. As seções a seguir abordam os cenários com mais detalhes para VMs da IaaS do Linux. Antes de poder usar a criptografia de disco, os [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) deverão ser cumpridos e a seção dos [pré-requisitos adicionais para VMs da IaaS do Linux](azure-security-disk-encryption-prerequisites-aad.md#bkmk_LinuxPrereq) deverá ser revisada.

Tire um [instantâneo](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou faça backup antes que os discos sejam criptografados. Os backups garantem que uma opção de recuperação seja possível, no caso de uma falha inesperada durante a criptografia. VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. Depois que um backup é feito, você poderá usar o cmdlet Set-AzureRmVMDiskEncryptionExtension para criptografar discos gerenciados, especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte o artigo [Backup do Microsoft Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 >Para garantir que os segredos de criptografia não ultrapassem os limites regionais, o Azure Disk Encryption precisa que o Key Vault e as VMs sejam colocados na mesma região. Crie e use um cofre de chaves que esteja na mesma região da VM a ser criptografada.</br></br>

> Ao criptografar volumes de SO Linux, o processo poderá demorar algumas horas. É normal que os volumes de SO Linux demorem mais que os volumes de dados para criptografar. 

>Desabilitar criptografia nas VMs do Linux tem suporte apenas para volumes de dados. Não haverá suporte em dados ou volumes de SO, se o volume de SO tiver sido criptografado.  


## <a name="bkmk_NewLinux"></a> Implantar uma nova VM da IaaS do Linux com criptografia de disco habilitada 

1. Use o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel) para criar uma nova VM da IaaS do Linux criptografada. O modelo criará uma nova VM RedHat Linux 7.2 com uma matriz RAID-0 de 200 GB e criptografia de disco completo usando discos gerenciados. No artigo de [Perguntas Frequentes](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport), será possível notar que algumas distribuições do Linux somente dão suporte a criptografia para discos de dados. No entanto, esse modelo oferece uma oportunidade de familiarizar-se com a implantação de modelos e a verificação do status de criptografia com vários métodos. 
 
1. Clique em **Implantar no Azure** no modelo do Azure Resource Manager.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Clique em **Criar** para habilitar a criptografia na VM da IaaS em execução ou existente.

3. Depois de implantar o modelo, verifique o status de criptografia da VM usando o método preferido:
     - Verifique com a CLI do Azure, usando o comando [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Verifique com o Azure PowerShell, usando o cmdlet [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     - Selecione a VM e, em seguida, clique em **Discos** sob o cabeçalho **Configurações** para verificar o status da criptografia no portal. No gráfico em **Criptografia**, você verá se ela está habilitada. 

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| ID do cliente AAD | ID do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| Segredo do Cliente AAD | Segredo do cliente do aplicativo AD do Azure que tem permissões para gravar segredos para o cofre de chaves. |
| Nome do cofre de chaves | Nome do cofre de chaves em que a chave deve ser colocada. |
| Grupo de recursos do Key Vault | Grupo de recursos do cofre de chaves. |


## <a name="bkmk_RunningLinux"> </a> Habilitar criptografia em uma VM da IaaS do Linux existente ou em execução
Nesse cenário, é possível habilitar a criptografia usando o modelo do Resource Manager, os cmdlets do PowerShell ou os comandos da CLI. 

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode ser usado para criptografar Managed Disks especificando o parâmetro -skipVmBackup. O comando Set-AzureRmVMDiskEncryptionExtension falhará nas VMs baseadas em disco gerenciado até que um backup seja feito e esse parâmetro tenha sido especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Habilitar criptografia em uma VM do Linux existente ou em execução usando CLI do Azure 
É possível habilitar a criptografia de disco no VHD criptografado, instalando e usando a ferramenta de linha de comando da [Azure CLI 2.0](/cli/azure)l. Você pode usá-lo em seu navegador com o [Azure Cloud Shell](../cloud-shell/overview.md), ou você pode instalá-lo em seu computador local e usá-lo em qualquer sessão do PowerShell. Para habilitar a criptografia em VMs da IaaS do Linux existentes ou em execução no Azure, use os comandos a seguir da CLI:

Use o comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) para habilitar a criptografia em uma máquina virtual da IaaS em execução no Azure.

-  **Criptografar uma VM em execução usando um segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografar uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão criptografados:** para verificar o status de criptografia de uma VM da IaaS, use o comando [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Desabilitar criptografia:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a>Habilitar criptografia em uma VM do Linux existente ou em execução usando PowerShell
Use o cmdlet [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) para habilitar a criptografia em uma máquina virtual da IaaS em execução no Azure. 

-  **Criptografar uma VM em execução usando um segredo de cliente:** o script abaixo inicializa as variáveis e executa o cmdlet Set-AzureRmVMDiskEncryptionExtension. O grupo de recursos, VM, cofre de chaves, aplicativo AAD e segredo do cliente já devem ter sido criados como pré-requisitos. Substitua MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret por seus valores. Talvez seja necessário adicionar o parâmetro -VolumeType se você estiver criptografando discos de dados e não o disco de SO. 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Criptografar uma VM em execução usando KEK para encapsular o segredo do cliente:** o Azure Disk Encryption permite que você especifique uma chave existente no cofre de chaves para encapsular segredos de criptografia de disco que foram gerados ao habilitar a criptografia. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para agrupar os segredos de criptografia antes de gravar no Key Vault. Talvez seja necessário adicionar o parâmetro -VolumeType se você estiver criptografando discos de dados e não o disco de SO. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Verifique se os discos estão criptografados:** para verificar o status de criptografia de uma VM da IaaS, use o cmdlet [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **Desabilitar criptografia de disco:** para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Desabilitar criptografia somente é permitida em volumes de Dados para VMs do Linux.
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Habilitar criptografia em uma VM da IaaS do Linux existente ou em execução com um modelo

Você pode habilitar a criptografia de disco em uma VM Linux IaaS existente ou em execução no Azure usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Clique em **Implantar no Azure** no modelo de início rápido do Azure.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Clique em **Criar** para habilitar a criptografia na VM da IaaS em execução ou existente.

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos existente ou VMs em execução que usam uma ID de cliente do Azure AD:

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| AADClientID | ID do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| AADClientSecret | Segredo do cliente do aplicativo AD do Azure que tem permissões para gravar segredos para o cofre de chaves. |
| keyVaultName | Nome do cofre de chaves em que a chave deve ser carregada. É possível obtê-lo, usando o comando da CLI do Azure `az keyvault show --name "MySecureVault" --query resourceGroup`. |
|  keyEncryptionKeyURL | URL da chave de criptografia de chave usada para criptografar a chave gerada. Esse parâmetro será opcional se você selecionar **nokek** na lista suspensa UseExistingKek. Se selecionar **kek** na lista suspensa UseExistingKek, você deverá inserir o valor _keyEncryptionKeyURL_. |
| volumeType | Tipo de volume em que a operação de criptografia é executada. Os valores válidos com suporte são _SO_ ou _Todos_ (consulte as distribuições do Linux com suporte e as versões para SO e discos de dados na seção de pré-requisitos anterior). |
| sequenceVersion | Versão de sequência da operação de BitLocker. Aumente esse número de versão sempre que uma operação de criptografia de disco for executada na mesma VM. |
| vmName | Nome da VM em que a operação de criptografia deve ser executada. |
| senha | Digite uma frase secreta forte como chave de criptografia de dados. |



## <a name="bkmk_EFA"> </a>Use o recurso EncryptFormatAll para discos de dados em VMs da IaaS do Linux
O parâmetro **EncryptFormatAll** reduz o tempo de criptografia dos discos de dados do Linux. As partições que atendem a determinados critérios serão formatadas (com o sistema de arquivos atual). Em seguida, serão remontadas de volta para onde estavam antes da execução do comando. Se você quiser excluir um disco de dados que atenda aos critérios, será possível desmontá-lo antes de executar o comando.

 Após executar esse comando, todas as unidades que foram montadas anteriormente serão formatadas. Em seguida, a camada de criptografia será iniciada na parte superior da unidade agora vazia. Quando essa opção for selecionada, o disco de recurso efêmero anexado à VM também será criptografado. Se a unidade temporária for reiniciada, ela será reformatada e criptografada novamente para a VM pela solução do Azure Disk Encryption na próxima oportunidade.

>[!WARNING]
> O EncryptFormatAll não deverá ser usado quando houver dados necessários nos volumes de dados de uma VM. É possível excluir discos da criptografia, desmontando-os. Primeiro será necessário testar o EncryptFormatAll, primeiro em uma VM de teste, e compreender o parâmetro de recurso e a respectiva implicação antes de testá-lo na VM de produção. A opção EncryptFormatAll formata o disco de dados e todos os dados nele serão perdidos. Antes de prosseguir, verifique se os discos que você quer excluir estão devidamente desmontados. </br></br>
 >Se você estiver configurando esse parâmetro ao atualizar as configurações de criptografia, isso poderá levar a um reinício antes da criptografia real. Nesse caso, é recomendável também remover o disco que você não quer que seja formatado no arquivo fstab. Da mesma forma, é necessário adicionar a partição que deseja criptografar ao arquivo fstab antes de iniciar a operação de criptografia. 

### <a name="bkmk_EFACriteria"> </a> Critérios do EncryptFormatAll
O parâmetro passa por todas as partições e criptografa-as desde que atendam a **todos** os critérios abaixo: 
- Não é uma partição de inicialização/SO/raiz
- Ainda não está criptografado
- Não é um volume BEK
- Está montado


### <a name="bkmk_EFATemplate"> </a> Usar o parâmetro EncryptFormatAll com um modelo
Para usar a opção EncryptFormatAll, use qualquer modelo do Azure Resource Manager pré-existente que criptografa uma VM do Linux e altere o campo **EncryptionOperation** do recurso AzureDiskEncryption.

1. Como exemplo, use o [modelo do Resource Manager para criptografar uma VM da IaaS do Linux em execução](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Clique em **Implantar no Azure** no modelo de início rápido do Azure.
3. Altere o **EncryptionOperation** de **EnableEncryption** para **EnableEncryptionFormatAl**
4. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, outros parâmetros, termos legais e contrato. Clique em **Criar** para habilitar a criptografia na VM da IaaS em execução ou existente.


### <a name="bkmk_EFAPSH"> </a> Usar o parâmetro EncryptFormatAll com um cmdlet do PowerShell
Use o cmdlet [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) com o [parâmetro EncryptFormatAll](https://www.powershellgallery.com/packages/AzureRM/5.0.0). 

**Criptografar uma VM em execução usando um segredo do cliente e EncryptFormatAll:** por exemplo, o script abaixo inicializa as variáveis e executa o cmdlet Set-AzureRmVMDiskEncryptionExtension com o parâmetro EncryptFormatAll. O grupo de recursos, VM, cofre de chaves, aplicativo AAD e segredo do cliente já devem ter sido criados como pré-requisitos. Substitua MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret por seus valores.
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Usar o parâmetro EncryptFormatAll com LVM (Gerenciador de Volume Lógico) 
É recomendável uma configuração LVM-on-crypt. Para todos os exemplos a seguir, substitua o caminho do dispositivo e os pontos de montagem pelo que melhor adequar-se ao seu caso de uso. Essa configuração pode ser feita da seguinte maneira:

- Adicione os discos de dados que irão compor a VM.
- Formatar, montar e adicionar esses discos ao arquivo fstab.

    1. Formate o disco adicionado recentemente. Usamos symlinks gerados pelo Azure aqui. O uso de symlinks evita problemas relacionados à alteração de nomes de dispositivos. Para obter mais informações, consulte o artigo [Solucionar problemas de Nomes do Dispositivo](../virtual-machines/linux/troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Monte os discos.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Adicione ao fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Execute o cmdlet Set-AzureRmVMDiskEncryptionExtension PowerShell com -EncryptFormatAll para criptografar esses discos.
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResouceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Configure a LVM sobre esses novos discos. Observe que as unidades criptografadas serão desbloqueadas após a VM concluir a inicialização. Portanto, a montagem de LVM também deverá ser subsequentemente atrasada.




## <a name="bkmk_VHDpre"> </a> Novas VMs da IaaS criadas a partir de chaves de criptografia e VHD criptografado pelo cliente
Nesse cenário, você pode habilitar a criptografia usando o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicam detalhadamente o modelo do Gerenciador de Recursos e comandos da CLI. 

Use as instruções no apêndice para preparar imagens previamente criptografadas que podem ser usadas no Azure. Depois que a imagem for criada, você poderá usar as etapas na próxima seção para criar uma VM do Azure criptografada.

* [Preparar um VHD do Windows previamente criptografado](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparar um VHD Linux previamente criptografado](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode ser usado para criptografar Managed Disks especificando o parâmetro -skipVmBackup. O comando Set-AzureRmVMDiskEncryptionExtension falhará nas VMs baseadas em disco gerenciado até que um backup seja feito e esse parâmetro tenha sido especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 



### <a name="bkmk_VHDprePSH"> </a> Usar o Azure PowerShell para criptografar VMs da IaaS com VHDs previamente criptografados 
É possível habilitar a criptografia de disco no VHD criptografado usando o cmdlet do PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). O exemplo abaixo fornece alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```

### <a name="bkmk_VHDpreRM"> </a> Usar o modelo do Resource Manager para criptografar VMs da IaaS com VHDs previamente criptografados 
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Clique em **Criar** para habilitar a criptografia na nova VM da IaaS.

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos para seu VHD criptografado:

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| newStorageAccountName | Nome da conta de armazenamento para armazenar o VHD de sistema operacional. Esta conta de armazenamento já deve ter sido criada no mesmo grupo de recursos e no mesmo local da VM. |
| osVhdUri | URI do VHD do sistema operacional da conta de armazenamento. |
| osType | Tipo de produto do sistema operacional (Windows/Linux). |
| virtualNetworkName | Nome da VNet à qual a NIC da VM deve pertencer. O nome já deve ter sido criado no mesmo grupo de recursos e no mesmo local que a VM. |
| subnetName | O nome da sub-rede na VNet à qual a VM NIC deve pertencer. |
| vmSize | O tamanho da VM. Atualmente, há suporte somente para séries Standard A, D e G. |
| keyVaultResourceID | O ResourceID que identifica o recurso de cofre de chaves no Azure Resource Manager. É possível obtê-lo, usando o cmdlet do PowerShell `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` ou usando o comando da CLI do Azure `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | URL da chave de criptografia de disco que é configurada no cofre de chaves. |
| keyVaultKekUrl | URL da chave de criptografia de chave para criptografar a chave de criptografia de disco gerada. |
| vmName | Nome da VM IaaS. |
## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Habilitar criptografia em um disco de dados adicionado recentemente
É possível adicionar um novo disco de dados usando [az vm disk attach](../virtual-machines/linux/add-disk.md) ou [por meio do portal do Azure](../virtual-machines/linux/attach-disk-portal.md). Antes de poder criptografar, primeiro será necessário montar o disco de dados anexado recentemente. É necessário solicitar a criptografia da unidade de dados, pois a unidade ficará inutilizável enquanto a criptografia estiver em andamento. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Habilitar criptografia em um disco adicionado recentemente com CLI do Azure
 O comando da CLI do Azure fornecerá automaticamente uma nova versão da sequência quando você executar o comando para habilitar a criptografia. 
-  **Criptografar uma VM em execução usando um segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografar uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Habilitar criptografia em um disco adicionado recentemente com Azure PowerShell
 Ao usar o Powershell para criptografar um novo disco para Linux, uma nova versão da sequência deverá ser especificada. A versão da sequência deverá ser exclusiva. O script abaixo gera um GUID para a versão da sequência. 
 

-  **Criptografar uma VM em execução usando um segredo de cliente:** o script abaixo inicializa as variáveis e executa o cmdlet Set-AzureRmVMDiskEncryptionExtension. O grupo de recursos, VM, cofre de chaves, aplicativo AAD e segredo do cliente já devem ter sido criados como pré-requisitos. Substitua MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret por seus valores. O parâmetro -VolumeType é configurado para discos de dados e não para o disco de SO. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **Criptografar uma VM em execução usando KEK para encapsular o segredo do cliente:** o Azure Disk Encryption permite que você especifique uma chave existente no cofre de chaves para encapsular segredos de criptografia de disco que foram gerados ao habilitar a criptografia. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para agrupar os segredos de criptografia antes de gravar no Key Vault. Talvez seja necessário adicionar o parâmetro -VolumeType se você estiver criptografando discos de dados e não o disco de SO. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Desabilitar criptografia para VMs do Linux
É possível desabilitar a criptografia usando o Azure PowerShell, a CLI do Azure ou com um modelo do Resource Manager. 

>[!IMPORTANT]
>Desabilitar criptografia com Azure Disk Encryption em VMs do Linux tem suporte apenas para volumes de dados. Não haverá suporte em dados ou volumes de SO, se o volume de SO tiver sido criptografado.  

- **Desabilitar criptografia de disco com Azure PowerShell:** para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Desabilitar criptografia com a CLI do Azure:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Desabilitar criptografia com um Modelo do Resource Manager:** use o modelo [Desabilitar criptografia em uma VM do Linux em execução](https://aka.ms/decrypt-linuxvm) para desabilitar a criptografia.
     1. Clique em **Implantar no Azure**.
     2. Selecione a assinatura, o grupo de recursos, o local, a VM, os termos legais e o contrato.
     3.  Clique em **Comprar** para desabilitar a criptografia de disco em uma VM do Windows em execução. 


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Habilitar o Azure Disk Encryption para Windows](azure-security-disk-encryption-windows-aad.md)
