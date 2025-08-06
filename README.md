# <p align="center"> 🐧 Guia de Instalação Minimalista Arch Linux 🐧 </p>

1. Baixe a ISO do Arch Linux na página oficial de downloads:
   - Download: **https://archlinux.org/download/**
2. Grave a ISO em um pendrive:
   - Você pode usar Rufus, Ventoy ou o comando dd
3. Reinicie seu computador e configure o BIOS/UEFI para inicializar pelo pendrive:
   - Certifique-se que o Secure Boot está **[DESATIVADO]**

```bash
# Pré-requisitos:
- USB flash drive (4GB+)
- Conexão com a Internet (Ethernet recomendado)
- Secure Boot OFF
```
## **📶 1. Conectar ao Wi-Fi (Ethernet, não precisa fazer isso)**

```bash
# Conectar na rede Wi-Fi
iwctl                         # Inicia o utilitário de Wi-Fi interativo
[iwd] device list               # Lista dispositivos de rede disponíveis
[iwd] adapter phy0 set-property Powered on  # Ativa o adaptador Wi-Fi
[iwd] station SEU_DISPOSITIVO get-networks      # Lista redes disponíveis
[iwd] station SEU_DISPOSITIVO connect SUA_REDE  # Conecta à rede
[iwd] exit                      # Sai do iwctl

# Testar conexão com a internet
ping "archlinux.org ou google.com"
```

## **⏰ 2. Sincronizar o relógio do sistema**
```bash
timedatectl set-ntp true       # Ativa sincronização via NTP
```

## **💽 3. Formatação e Particionamento do disco (usando fdisk)**
```bash
# 3.1 Particionamento do Disco
lsblk                          # Lista dispositivos de bloco/disco

fdisk /dev/SEU_DISCO          # Inicia o particionamento

# Comandos no fdisk:
# [Repita este comando até apagar todas partições existentes]
Command (m for help): d          # Deleta partição

# [Cria partição 1: EFI]
Command (m for help): n          # Nova partição
Partition number (1-128, default 1): Enter  # Aceita padrão
First sector (..., default 2048): Enter      # Aceita padrão
Last sector ...: +1G             # Tamanho de 1GB

# [Cria partição 2: swap]
Command (m for help): n
Partition number (2-128, default 2): Enter
First sector (..., default ...): Enter
Last sector ...: +8G             # Tamanho de 8GB (ajuste conforme RAM)

# [Cria partição 3: / (raiz)]
Command (m for help): n
Partition number (3-128, default 3): Enter
First sector (..., default ...): Enter
Last sector ...: Enter           # Usa todo espaço restante

# [Escreve todas mudanças]
Command (m for help): w          # Grava alterações no disco

# 3.2 Formatação das Partições
mkfs.fat -F 32 /dev/SEU_DISCO_PARTIÇÃO_1  # Formata EFI como FAT32
mkswap /dev/SEU_DISCO_PARTIÇÃO_2          # Cria área de swap
mkfs -t ext4 /dev/SEU_DISCO_PARTIÇÃO_3    # Formata raiz como ext4
```

## **📌 4. Montar sistemas de arquivos**
```bash
mount /dev/SEU_DISCO_PARTIÇÃO_3 /mnt      # Monta partição raiz
mkdir -p /mnt/boot/efi                     # Cria pasta para EFI
mount /dev/SEU_DISCO_PARTIÇÃO_1 /mnt/boot/efi  # Monta partição EFI
swapon /dev/SEU_DISCO_PARTIÇÃO_2         # Ativa swap
```

## **🌍 5. Atualizar mirrors para downloads**
```bash
reflector --country SEU_PAIS --latest 5 --save /etc/pacman.d/mirrorlist --sort rate --verbose   # (Substitua SEU_PAIS pelo seu país - Ex: Brazil)
```

## **📦 6. Instalar os pacotes essenciais**
```bash
# Instalar os pacotes essenciais
pacstrap -K /mnt base linux linux-firmware amd-ucode base-devel grub efibootmgr networkmanager nano sudo

# Gerar o arquivo fstab (contém as montagens automáticas)
genfstab -U /mnt >> /mnt/etc/fstab
```

## **🖥️ 7. Configuração Básica do Sistema**
```bash
# Entrar no sistema instalado
arch-chroot /mnt

# Configurar fuso horário (ex: America/Sao_Paulo)
ln -sf /usr/share/zoneinfo/[REGIÃO]/[CIDADE] /etc/localtime
hwclock --systohc  # Sincroniza o hardware clock

# Localização (descomente pt_BR.UTF-8 OU en_US.UTF-8 em /etc/locale.gen)
nano /etc/locale.gen
locale-gen

# Definir o idioma e a codificação padrão do sistema
echo "LANG=[pt_BR.UTF-8 OU en_US.UTF-8]" > /etc/locale.conf

# Definir o nome da máquina (substitua NOME_DO_PC):
echo NOME_DO_PC > /etc/hostname

# Configurar o arquivo de hosts:
nano /etc/hosts
    # Adicione estas linhas:
    127.0.0.1 localhost
    ::1       localhost
    127.0.1.1 NOME_DO_PC

# Criar o usuário principal:
useradd -m -G wheel,uucp -s /bin/bash SEU_USUARIO

# Definir senhas:
passwd root          # Senha do administrador
passwd SEU_USUARIO  # Senha do seu usuário

# Configuração dos privilégios sudo:
EDITOR=nano visudo
    # Localize e descomente a linha:
    %wheel ALL=(ALL) ALL
```
## **🌐 6. Habilitar Conexão de Rede**
```bash
# Ativar o NetworkManager para iniciar automaticamente:
systemctl enable NetworkManager
```
## **🍔 7. Configurar GRUB**
```bash
# Instalar o GRUB para UEFI:
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --removable

# Gerar arquivo de configuração:
grub-mkconfig -o /boot/grub/grub.cfg
```
## **🔌8. Finalização e Reinício**
```bash
exit      # Sai do ambiente chroot
umount -R /mnt      # Desmonta todas as partições
poweroff ou reboot    # Desliga ou reinicia o computador
```

### ‼️ IMPORTANTE:
 - Remova o pendrive USB após desligar ou reiniciar o computador
 
<div align="center">

## ✨ **Parabéns, bem-vindo ao mundo Arch Linux!** ✨

<img src="https://archlinux.org/static/logos/archlinux-logo-dark-1200dpi.b42bd35d5916.png" width="600">

</div>
