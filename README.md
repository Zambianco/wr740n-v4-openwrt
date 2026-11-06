# OpenWrt customizado para TP-Link TL-WR740N v4

Firmware baseado no OpenWrt 24.10 (target `ath79/tiny`, SoC AR9331, 4 MB flash / 32 MB RAM),
compilado na nuvem pelo GitHub Actions.

O perfil `tplink_tl-wr740n-v4` tem `DEFAULT := n` no OpenWrt, entao nao existe imagem
oficial pronta nem suporte no Image Builder/ASU. Por isso o build parte do fonte.

## O que vem configurado

- LAN privada `192.168.1.1` (Wi-Fi privado so se o segredo `WIFI_PSK` existir)
- Rede de convidados `Convidados` (`192.168.3.0/24`, bridge `br-guest`), clientes isolados,
  firewall: convidados so acessam a WAN (mais DNS/DHCP do roteador)
- Portal cativo com **nodogsplash** (pagina em `files/etc/nodogsplash/htdocs/splash.html`)
- Sem PPPoE e sem IPv6, para caber nos 3904 KB

## Build

1. (Opcional) Em *Settings > Secrets and variables > Actions*:
   - `WIFI_PSK`: senha do Wi-Fi privado
   - `SSH_PUBKEY`: sua chave publica SSH (vai para `/etc/dropbear/authorized_keys`)
2. Aba *Actions* > **Build WR740N v4** > *Run workflow*.
3. Baixe o artefato `openwrt-wr740n-v4`.

O build falha se a imagem passar de 3904 KB.

## Gravacao (pela LAN)

1. Cabo do PC na porta LAN, acesse a pagina do TP-Link (`192.168.0.1`) > Upgrade de firmware.
2. Envie `*-wr740n-v4-squashfs-factory.bin`.
3. Depois do boot, o roteador fica em `192.168.1.1`. Defina a senha de root (`passwd`) por telnet
   ou use a chave SSH.
4. Atualizacoes seguintes: `sysupgrade.bin`.

**Antes de gravar:** faca backup da flash original (a particao ART guarda a calibracao do Wi-Fi).
Se o firmware original for recente e rejeitar a imagem, baixe para uma versao antiga primeiro.

## Proximos passos

- Validar o tamanho da imagem e o funcionamento do nodogsplash com o firewall4 (nftables)
- Agente de controle central
