# Como instalar o ArchLinux

## Topicos:
Continuar...

## Documentação
Toda a documentação do Arch Linux está disponível no site official do SO, ao meu ver é a documentação mais completa e com o maior numero de pessas contibuindo.

[ArchWiki (archlinux.org)](https://wiki.archlinux.org/)

## Download
Para realizar o Download do Arch Linux você deve acessar o site do [Arch Linux - Downloads](https://archlinux.org/download/).

## Midia de instalação

Continuar...

## Pré-Instalação

**Definindo layout do teclado:**

```bash
loadkeys br-abnt2
```

Para listar todos os layout disponíveis use:

```bash
localectl list-keymaps
```

**Conectando a internet:**

Para saber se estamos conectados a internet, vamos usa o comando `ping`.

```bash
ping 8.8.8.8
```
Continuar...

**Atualizando Chaves:**

Para evitar erros, vamos atualizar as chaves:

```bash
pacman-key --init
pacman-key --populate
pacman -Sy archlinux-keyring
```

**Particionado o disco:**

Para essa instalação irei usar o seguinte esquema de particionamento, para saber mais sobre esse assunto acesse a wiki.

- `/dev/sda1` boot partition (500MB para o /boot/efi).
- `/dev/sda2` swap partition (4GB para o swap, isso é opcional).
- `/dev/sda3` root partition (20GB para /).
- `/dev/sda4` home partition (100GB para o /home).

Nesse caso irei usar todo o resto do meu disco para o `/home`.


Para ver as partições use o `fdisk`.

Listando partições existentes.

```bash
fdisk -l /dev/sda
```

Para criar as partições vamos usar o `cfdisk`.

```bash
cfdisk /dev/sda/
```

Após definir as partições vamos formatar as partições.

```bash
   mkfs.fat -F32 /dev/sda1
   mkswap /dev/sda2
   mkfs.ext4 /dev/sda3
   mkfs.ext4 /dev/sda4
```

Definindo ponto de montagem das partições.

```bash
   swapon /dev/sda2
   mount /dev/sda3 /mnt
   mkdir /mnt/{boot,home}
   mount /dev/sda1 /mnt/boot
   mount /dev/sda4 /mnt/home
```

Você pode conferir usando o comando `lsblk`:

```bash
 NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
   sda      8:0    0 232.9G  0 disk
   ├─sda1   8:1    0  500MB  0 part /mnt/boot
   ├─sda2   8:2    0     4G  0 part [SWAP]
   ├─sda3   8:3    0    20G  0 part /mnt
   └─sda4   8:4    0   100G  0 part /mnt/home
```

**Configurando os mirror:**

Configurando mirror usando o `reflector`.

```bash
pacman -Sy
pacman -S reflector
reflector --latest 5 --sort rate --save /etc/pacman.d/mirrorlist
```

Se você preferir definir os mirror manualmente.

```bash
vim /etc/pacman.d/mirrorlist
```

Você pode mover o mirror desejado para cima, ou comentar a linha que você não deseja usar com um #.

**Atualizar relogio do sistema:**

```bash
timedatectl set-ntp true
```

**Instalar pacotes essensiais:**
```bash
pacstrap /mnt base base-devel linux-zen linux-firmware neovim nano git
```

**Gerarando arquivo fstab:**

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

## Configurações basicas
---
**Acessar novo sistema:**

```bash
arch-chroot /mnt
```

### **Instalando o bootloader (GRUB):**
---
**BIOS-Legacy**

```bash
pacman -S grub
grub-install --target=i386-pc --recheck /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

**UEFI**

```bash
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=arch_grub --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```

### **Atualizando o idioma e layout do teclado:**
---
Para atualizar o idioma, você deve editar o arquivo `/etc/locale.conf`.

```bash
nvim /etc/locale.conf
```

Você precisa descomentar a linha referente ao seu idioma desejado, por exemplo `en_US.UTF-8 UTF-8`.

Após editar o arquivo, atualize o arquivo com o comando `locale-gen`.

```bash
locale-gen
```

Para atualizar o layout do teclado, você precisa editar o arquivo `/etc/vconsole.conf`.

```bash
echo KEYMAP=br-abnt2 >> /etc/vconsole.conf
```


### **Configurando o fuso horário:**
---
Para exemplo eu estou em "America/Sao_Paulo", você deve adptar de acordo com o seu fuso.

```bash
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
hwclock --systohc
```

Para conferir se a data do sistema está correta, use o comando `date`.

```bash
date
```

### **Configurações de rede e bluetooth:**
---
```bash
pacman -S dhcpcd iw openssh network-manager-applet networkmanage wpa_supplicant wireless_tools dialog bluez bluez-utils blueman
systemctl enable sshd
systemctl enable dhcpcd
systemctl enable NetworkManager
systemctl enable bluetooth
```

**Definindo um Hostname:**

```bash
hostnamectl set-hostname nomedoseuhost
```

### **Instalando drivers:**
---
**Instalando drivers de audio:**

```bash
pacman -S pulseaudio pulseaudio-alsa alsa-utils alsa-plugins alsa-lib alsa-firmware pavucontrol pulseaudio-bluetooth
```

**Instalando drivers de video:**

**Intel**

```bash
pacman -S xf86-video-intel
```

**Nvidia**

```bash
pacman -S nvidia nvidia-settings
```

**AMD**

```
pacman -S xf86-video-amdgpu
```

**Configurando o SSD TRIM:**

```bash
systemctl enable fstrim.timer
```

**Instalando pacotes adicionais:**

```bash
pacman -S dialog git reflector lshw unzip htop wget xdg-user-dirs dosfstools os-prober mtools nemo firefox
```

**Criando senha root:**

Para configurar a nova senha do seu usuário root, basta rodar o comando:

```bash
passwd
```

**Criando usuario:**


```
useradd -m -g users -G wheel,storage,power,audio seuusuario
passwd seuusuario
```
Conceder acesso root ao nosso usuário

```bash
EDITOR=nvim visudo
```

Se preferir não lhe ser pedida uma palavra-passe sempre que executar um comando com privilégios "sudo" você precisa descomentar esta linha:

```bash
%wheel ALL=(ALL) NOPASSWD: ALL
```

Ou se você preferir o comportamento padrão da maioria das distribuições Linux, você precisa descomente esta linha:

```bash
%wheel ALL=(ALL) ALL
```
Login no usuário recém-criado

```bash
su - seuusuario
xdg-user-dirs-update
```

**reboot:**

```bash
exit
umount -R /mnt
reboot
```

## **Instalando o gerenciador de janelas I3-gaps:**

**Instalando o gerenciador de pacotes AUR:**
   
```bash
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

**Ajustes no arquivo de configuração**

```bash
nvim /etc/pacman.conf
```
...

**Instalando o xorg:**

```bash
pacman -S xorg-server xorg-xinit xorg-apps mesa
```

**Tela de login:**

```bash
pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings
systemctl enable lightdm
```


## Instalando o I3-gaps:

```bash
paru -S i3-gaps-rounded-git i3status i3lock i3blocks i3lock-color dmenu dunst redshift xfce4-settings autotiling-rs-git rofi picom nitrogen lxappearance power-profiles-daemon arandr alacritty polybar
```
---

## Referencias:
- https://wiki.archlinux.org/title/Installation_guide
- https://gist.github.com/m4jrt0m/2125d5ad87fad7216a8e7591337709cf
- https://diolinux.com.br/sistemas-operacionais/arch-linux/como-instalar-arch-linux-tutorial-iniciantes.html
