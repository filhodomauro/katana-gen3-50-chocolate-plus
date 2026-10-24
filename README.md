# katana-gen3-50-chocolate-plus

Configuração entre o amplificador **Boss Katana Gen 3 50 W** e o pedal controlador **M-VAVE Chocolate Plus**, para trocar de preset e ligar/desligar efeitos do amplificador direto do pedal, sem computador ou celular no meio.

## Objetivo

Reunir em um só lugar o que é necessário para controlar o Katana a partir do Chocolate Plus:

- a lista de comandos MIDI (SysEx) que o amplificador entende: [`commands.md`](commands.md);
- arquivos de exportação (`.fcp`) do FootCtrlPlus prontos para importar no pedal;
- uma explicação de como esses comandos são enviados.

## Equipamentos e versões

| Equipamento | Detalhe | Versão |
|---|---|---|
| Boss Katana Gen 3 50 W | amplificador, modelo KATANA-50 Gen 3 | System Program Ver.1.06 |
| Boss BT-DUAL | adaptador Bluetooth de áudio e MIDI, instalado na porta USB do amplificador | firmware: _a preencher_ |
| M-VAVE Chocolate Plus | pedal controlador MIDI com 4 footswitches | firmware: _a preencher_ |
| FootCtrlPlus | aplicativo de configuração do Chocolate Plus (macOS) | versão: _a preencher_ |
| Boss Tone Studio for Katana Gen 3 | editor do amplificador, usado como referência dos nomes dos efeitos | Ver.1.1.0 (Build 26) |

Os comandos foram validados **somente** nessa combinação. Outros modelos da linha Katana (100 W, Head, Artist, EX) e as gerações anteriores (Mk I e Mk II) podem usar outro layout de presets, outros endereços ou outro ID de modelo, e não foram testados.

## Como os comandos são enviados

### O amplificador só aceita SysEx pelo Bluetooth

Com o BT-DUAL, o Katana **ignora mensagens de Program Change e de Control Change**, que são as mensagens MIDI usuais para trocar de preset e ligar efeitos. O que ele aceita é **SysEx no formato Roland**, o mesmo protocolo usado pelo Boss Tone Studio. Por isso o pedal precisa ser configurado com mensagens do tipo **SysEx**, e não com PC ou CC.

### Formato da mensagem

Toda mensagem de escrita tem esta forma:

```
F0 41 10 01 05 07 12 <endereço> <dados> <checksum> F7
```

| Bytes | Significado |
|---|---|
| `F0` | início do SysEx |
| `41` | fabricante (Roland) |
| `10` | Device ID (valor padrão) |
| `01 05 07` | ID do modelo (Katana Gen 3) |
| `12` | comando de escrita (DT1) |
| `<endereço>` | 4 bytes com o parâmetro a alterar |
| `<dados>` | valor a gravar |
| `<checksum>` | 1 byte calculado a partir do endereço e dos dados |
| `F7` | fim do SysEx |

### Cálculo do checksum

Some todos os bytes de `<endereço>` e `<dados>`, e o checksum é o valor que completa o total até o próximo múltiplo de 128:

```
checksum = (128 - (soma % 128)) % 128
```

Exemplo, ligar o Booster (endereço `20 00 08 00`, dado `01`):

```
soma = 0x20 + 0x00 + 0x08 + 0x00 + 0x01 = 0x29 = 41
checksum = (128 - 41) % 128 = 87 = 0x57
```

Mensagem completa: `F0 41 10 01 05 07 12 20 00 08 00 01 57 F7`.

### Modo de edição (somente para trocar de preset)

A troca de preset só é aceita depois que o **modo de edição** é ligado, o que se faz enviando uma mensagem antes da troca. O modo de edição continua ativo enquanto o amplificador estiver ligado e conectado. Já os comandos de efeito **não** exigem esse passo. Os detalhes estão em [`commands.md`](commands.md).

### Configuração no pedal

No FootCtrlPlus, cada mensagem é cadastrada como **SysEx**, em hexadecimal com espaços, **incluindo `F0` e `F7` e o checksum**. Um mesmo botão aceita várias mensagens em sequência, o que permite enviar o modo de edição e a troca de preset com um único toque. Para alternar um efeito entre ligado e desligado em um botão, use o modo de dois bancos: o banco A liga e o banco B desliga.

## Arquivos de exportação

Os arquivos ficam na pasta [`exports/`](exports/). Para usar um deles, abra o FootCtrlPlus com o Chocolate Plus conectado, clique em **Import** e selecione o arquivo.

### Cena 1: `cube-suite-kat3-50-scene-1.fcp`

Configuração com **4 grupos**, cada um com os 4 footswitches (A, B, C e D) do Chocolate Plus. Todas as mensagens são SysEx e estão listadas em [`commands.md`](commands.md).

| Grupo | Função | A | B | C | D |
|---|---|---|---|---|---|
| 1 | Presets | A: CH-1 | B: CH-1 | A: CH-2 | B: CH-2 |
| 2 | Efeitos | Booster | Mod | Delay 1 | Reverb |
| 3 | Efeitos | Solo | FX | EQ | Delay 2 |
| 4 | Efeitos | NS | Variation | Pedal FX | EQ2 |

- **Grupo 1 (presets):** cada botão está no modo "Single step (single Bank information)" e envia duas mensagens em sequência: o modo de edição e a troca de preset.
- **Grupos 2 a 4 (efeitos):** cada botão está no modo "Single step (switch between two Banks)". O banco A liga o efeito e o banco B desliga, então cada toque alterna o estado.
- O pedal não lê o estado do amplificador. Se um efeito for alterado pelo painel, ou se o preset mudar, o toggle do pedal pode ficar fora de sincronia (veja a nota em [`commands.md`](commands.md#efeitos)).
- O número máximo de grupos está configurado em 4.

Testada no Katana System Program Ver.1.06 (veja a tabela de equipamentos).

## TODO

- [ ] Preencher as versões de firmware do BT-DUAL e do Chocolate Plus e a versão do FootCtrlPlus.
- [ ] Validar os comandos também no Katana System Program Ver.1.07 e, se funcionarem, documentar as duas versões.
- [ ] Mapear os comandos dos blocos ainda não documentados: Send/Return, Contour e Global EQ.
- [ ] Adicionar outras cenas de exportação (`.fcp`), caso existam.

## Aviso

Projeto independente e não oficial. Não tem relação com Roland Corporation, Boss ou M-VAVE. "Katana", "Boss" e "Chocolate Plus" são marcas dos respectivos donos e aparecem aqui apenas para indicar compatibilidade. Testado somente em um Boss Katana Gen 3 50 W com BT-DUAL e um M-VAVE Chocolate Plus. Use por sua conta e risco.

## Licença

[CC0 1.0 Universal](LICENSE): domínio público, sem exigência de atribuição.
