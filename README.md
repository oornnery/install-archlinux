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

## Pré-instalação

---

**Definindo layout do teclado**

```bash
loadkeys br-abnt2
```

Para listar todos os layout disponpíveis use o comando abaixo:

```bash
localectl list-keymaps
```

**Conectando a internet**

Para saber se estamos conectados a internet, vamos usa o comando `ping`.

```bash
ping 8.8.8.8
```

**Atualizando Chaves**

Caso tenha erros, procure atualizar as chaves do repositorio, para isso utilize o comando abaixo.

```bash
pacman-key --init
pacman-key --populate
pacman -Sy archlinux-keyring
```

### Particioando o disco

Para essa instalação irei usar o seguinte esquema de particionamento, para saber mais sobre esse assunto acesse a documentação.

- `/dev/sda1` boot partition (500MB).
- `/dev/sda2` swap partition (4G). (opcional)
- `/dev/sda3` root partition (20).
- `/dev/sda4` home partition (100G).

Irei utilizar o `cfdisk` para criar as particições.

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

Vamos montar as partições.

```bash
   swapon /dev/sda2
   mount /dev/sda3 /mnt
   mkdir /mnt/{boot,home}
   mount /dev/sda1 /mnt/boot
   mount /dev/sda4 /mnt/home
```

Se você rodar o comando `lsblk` deve ver uma saída como essa:

```bash
 NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
   sda      8:0    0 232.9G  0 disk
   ├─sda1   8:1    0  500MB  0 part /mnt/boot
   ├─sda2   8:2    0     4G  0 part [SWAP]
   ├─sda3   8:3    0    20G  0 part /mnt
   └─sda4   8:4    0   100G  0 part /mnt/home
```
