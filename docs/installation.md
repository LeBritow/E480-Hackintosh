# Instalação do macOS Ventura / Sonoma no E480

Guia passo a passo para instalar o macOS no ThinkPad E480 usando esta EFI. Testado com **Ventura 13.7** (instalação limpa) e **Sonoma 14.7** (upgrade pela App Store).

## O que você precisa

- **Hardware:** ThinkPad E480 (i5-8250U/i5-8550U), 8 GB+ RAM, SSD
- **Pendrive USB** (≥ 8 GB)
- **Acesso a um macOS** (opcional mas ajuda) ou um sistema com `python3`
- **BIOS configurada** (veja abaixo)

## Passo 1 — Configurar BIOS

Reinicie e entre na BIOS (`F1`):

| Configuração | Valor |
|:---|:---|
| Security → Intel SGX | Software Controlled |
| Startup → Boot Mode | Both (UEFI + Legacy) |
| Startup → Boot Priority | UEFI First |
| Startup → Quick Boot | Enabled |

## Passo 2 — Criar o pendrive instalador

### No macOS

```sh
# 1. Baixe o macOS Ventura
softwareupdate --list-all

# 2. Ou use o script de recovery (mais rápido):
cd Utilities/macrecovery
python3 macrecovery.py -b Mac-B4831CEBD52A0C4C download -os latest
```

Isso gera a pasta `com.apple.recovery.boot/` com `BaseSystem.dmg` e `BaseSystem.chunklist`.

### Montar o pendrive

1. **Utilitário de Discos** → apague o pendrive como:
   - Nome: `USB`
   - Formato: **MS-DOS (FAT)**
   - Esquema: **GUID Partition Map**
2. Monte o pendrive e crie as pastas `EFI` e `com.apple.recovery.boot` na raiz.
3. Copie o conteúdo de `com.apple.recovery.boot/` para a pasta homônima no pendrive.
4. Copie a pasta `EFI/` deste repositório para a raiz do pendrive.

## Passo 3 — Instalar

1. Conecte o pendrive e ligue o E480.
2. Abra o boot menu (`F12`) e escolha o pendrive USB.
3. No picker do OpenCore, escolha a entrada do instalador (ex.: "macOS Base System").
4. **Utilitários → Utilitário de Discos**:
   - Selecione o disco interno (CUIDADO: não apague o pendrive!)
   - Apagar → Nome `Macintosh HD`, Formato **APFS**, Esquema **GUID**
5. Feche o Utilitário de Discos → **Instalar o macOS** → selecione `Macintosh HD`.
6. A máquina reinicia várias vezes. **Deixe o pendrive conectado em TODAS as reinicializações** — o OpenCore no pendrive é quem continua o boot.
7. Se o picker parar de mostrar o instalador e só mostrar o disco, basta escolher a entrada do disco (a instalação continua).

## Passo 4 — Pós-instalação

Siga o [guia de pós-instalação](post-install.md):

1. Gerar SMBIOS próprio
2. Copiar a EFI para o disco interno
3. Remover `-v` do boot

## Passo 5 — Upgrade para Sonoma (opcional)

Com o Ventura instalado e funcionando, o Sonoma sobe **sem pendrive**:

1. Faça backup (Time Machine).
2. Ajustes → Geral → Atualização de Software, **ou** pesquise por "macOS Sonoma" na **App Store** e clique em **Obter**.
3. O instalador baixa (~12 GB), reinicia sozinho e faz tudo — a EFI atual (OC 1.0.7 + kexts novos) já suporta Sonoma sem mudanças.

## Troubleshooting

| Problema | Solução |
|:---|:---|
| Kernel panic logo após `EXITBS:START` | Atualizar OpenCore/kexts (esta EFI já está atualizada) |
| Tela preta | Verifique `boot-args`; tente `igfxonln=1` |
| Áudio mudo | `alcid=15` já configurado |
| WiFi não aparece | AirportItlwm precisa da versão certa para o macOS instalado |
| Fica voltando para o pendrive | Copiou a EFI para o disco interno? (post-install) |
| iMessage não ativa | Gere serial/MLB/UUID próprios (não use os do repo) |

Se nada resolver, abra uma issue com o log:
1. Adicione `-v` nos `boot-args`
2. Pressione `F2` no picker do OpenCore para salvar o log (`opencore-YYYY-MM-DD-HHMMSS.txt`)
3. Anexe o log na issue
