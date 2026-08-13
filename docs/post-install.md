# Pós-instalação — ThinkPad E480

## 1. Copiar a EFI para o disco interno

Depois do sistema instalado e funcionando pelo pendrive:

1. Monte a partição EFI do disco interno:
   ```sh
   diskutil mount disk0s1
   ```
2. Copie a pasta `EFI/` do pendrive para a partição EFI montada.
3. Reinicie (com o pendrive ainda conectado, por segurança) e confirme que o sistema abre sem o pendrive. Depois pode remover.

## 2. Gerar SMBIOS próprio (IMPORTANTE)

O `config.plist` deste repositório vem **sem** serial/MLB/UUID válidos. Para usar iMessage, FaceTime, iCloud etc., gere os seus:

```sh
# macOS
python macserial -m MacBookPro15,4
```

Abra o `config.plist` no [ProperTree](https://github.com/corpnewt/ProperTree) e use **File → OC Snapshot** (ou faça manualmente):

- `PlatformInfo → Generic → SystemSerialNumber`
- `PlatformInfo → Generic → MLB`
- `PlatformInfo → Generic → SystemUUID`
- `PlatformInfo → Generic → ROM` (12 hex, ex.: `A1B2C3D4E5F6`)

> Valide o serial no [site da Apple](https://checkcoverage.apple.com/). Deve retornar "Por favor, insira um número de série válido" — ou seja, um serial **não usado** por ninguém.

## 3. Remover boot verbose

O `config.plist` traz `boot-args = -v` para depuração. Para boot "limpo":

1. Ajustes → Usuários e Grupos → Opções de Login → mude `-v` em `boot-args` **no config.plist** (não no NVRAM).
2. Ou edite `NVRAM → Add → 7C436110-... → boot-args` no ProperTree: `alcid=15` (sem `-v`).
3. Limpe o NVRAM depois (veja abaixo).

## 4. Limpar NVRAM (quando precisar)

No picker do OpenCore, pressione `Espaço` para mostrar ferramentas e escolha **CleanNvram**. Útil quando:
- Boot travando após mudança de config
- Problemas de áudio/som do boot
- Vars de outro SMBIOS

## 5. Sleep e hibernação

- Sleep normal funciona (HibernationFixup).
- Para hibernação 3/25, ajuste:
  ```sh
  sudo pmset -a hibernatemode 0
  ```
- O "black screen no wake" já tem `ReservedMemory` desabilitado por padrão; se ocorrer, ative o `Fix black screen on wake` no `UEFI → ReservedMemory`.

## 6. Atualizações

- **Ventura 13.x**: Ajustes → Geral → Atualização de Software. Tudo tranquilo.
- **Upgrade para Sonoma/Sequoia**: 
  1. Backup (Time Machine)
  2. Atualize OpenCore + kexts para as últimas versões
  3. Confirme que o `config.plist` continua validando (`ocvalidate`)
  4. Só então rode o upgrade

## 7. Backup do EFI

Sempre guarde uma cópia da pasta `EFI` funcionando (num pendrive ou em outro OS). Vai te salvar.
