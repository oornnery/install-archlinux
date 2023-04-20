# Como instalar o ArchLinux

---

### Topicos:

- Download
  
- Instalação
  
- Ajuste inicial
  
- Referencias
  

## Documentação

---

Toda a documentação do Arch Linux está disponível no site official do SO, ao meu ver é a documentação mais completa e com o maior numero de pessas contibuindo.

[ArchWiki (archlinux.org)](https://wiki.archlinux.org/)

## Download

---

Para realizar o Download do Arch Linux você deve acessar o site do [Arch Linux - Downloads](https://archlinux.org/download/).

## Midia de instalação

---

...

## Pré-Instalação

---

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

