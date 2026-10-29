# Construtor Solo

Kit para o [Claude Code](https://claude.com/claude-code) que conduz um projeto de software do início ao fim, **etapa por etapa, numa sessão só**.

Você não começa pedindo código. Primeiro o Claude entende o objetivo, faz as perguntas que faltam, define com você o ambiente e as tecnologias e monta um roadmap. Depois o projeto avança uma etapa por vez, cada uma com critérios de aceite verificáveis. Hooks e regras impedem que documentação ou segredos vazem para o repositório.

> Quer um gerente coordenando uma sessão separada para cada etapa? Veja o **[Construtor Equipe](https://github.com/jeyjunior/construtor-equipe)**.

---

## Como funciona

### Estrutura fixa

Todo projeto segue esta estrutura, que o kit não deixa alterar:

```
meu-projeto/                ← o "Root": abra o Claude Code sempre aqui
├── CLAUDE.md               regras do kit (carregadas em toda sessão)
├── .claude/                skills, hooks, permissões e modelos do kit
├── Projeto/                o código. É o ÚNICO repositório git e o que vai para o GitHub
└── Projeto_Documento/      a documentação. Nunca entra em git nem vai para lugar nenhum
    ├── objetivo.md
    ├── arquitetura.md
    ├── roadmap.md
    ├── etapas/             um arquivo por etapa (plano, execução, evidências)
    ├── decisoes/           uma decisão técnica por arquivo
    └── sensivel/           chaves, certificados e credenciais (o Claude não lê nem grava)
```

A ideia é separar o que é **produto** (`Projeto/`, público) do que é **processo** (`Projeto_Documento/`, privado). Assim você publica o código sem expor anotações, planos ou segredos.

### O fluxo

1. **`/iniciar-projeto`** (uma vez): o Claude conduz o começo em fases e não escreve código.
   - **Mapeamento:** só se já existir código. Ele lê `Projeto/` e descreve o que o sistema faz hoje.
   - **Objetivo:** o que o sistema precisa fazer e para quem.
   - **Perguntas:** tudo o que ficou ambíguo é perguntado, e nada é suposto.
   - **Ambiente e tecnologias:** para cada escolha relevante, 2 ou 3 opções com prós e contras. A decisão é sua.
   - **Roadmap:** etapas pequenas, cada uma com entregáveis e critérios de aceite verificáveis.
2. **`/etapa`** (a cada etapa): a mesma ordem, sempre.
   1. Revisa a etapa anterior com você.
   2. Apresenta a próxima.
   3. Faz as perguntas da etapa e mostra o plano.
   4. Executa depois do seu ok.
   5. Fecha com evidência: o build, a execução ou a chamada real que prova cada critério.
3. **`depurar`** (automática): em caso de bug ou erro, o Claude é obrigado a achar a causa antes de mexer no código, com no máximo três tentativas antes de parar e reavaliar.

Toda decisão vai para `Projeto_Documento/`. Se você fechar a sessão e abrir outra, o Claude retoma de onde parou lendo esses arquivos.

### Travas

| Trava | O que faz |
|---|---|
| `hooks/proteger-estrutura.sh` | Nega ao Claude criar documentação ou arquivo sensível em `Projeto/` e criar qualquer coisa solta no Root. |
| `git-hooks/pre-commit` | Recusa o commit (de qualquer pessoa) que tenha documentação, arquivo sensível ou segredo. |
| `hooks/bloquear-segredos.sh` | Nega edições que escrevem chaves, tokens ou senhas literais no código. |
| `hooks/bloquear-comandos.sh` | Bloqueia comandos destrutivos. |
| `hooks/verificar-ambiente.sh` | Ao abrir a sessão, avisa se a estrutura está fora do padrão ou se falta alguma ferramenta. |
| `hooks/formatar.sh` e `verificar-build.sh` | Formatam o arquivo depois de cada edição e compilam ao fim de cada resposta, se configurados. |
| `settings.json` | Nega leitura e escrita em `Projeto_Documento/sensivel/` e pede confirmação para `git push`, `git init` e `reset --hard`. |
| `Projeto/.gitignore` | Ignora `.md` (menos o `README.md`), `docs/`, `.env` e chaves. |

---

## Requisitos

- [Claude Code](https://claude.com/claude-code) (CLI, app desktop ou extensão de IDE)
- `git`
- `bash`: no Windows, o Git Bash que vem com o Git for Windows
- [`jq`](https://jqlang.org): sem ele, os bloqueios de comandos e de segredos ficam desligados (o kit avisa)

## Instalação

O kit é uma **pasta modelo**: você copia para um projeto novo e descarta o git do kit, porque o único repositório deve ser `Projeto/`.

```bash
git clone https://github.com/jeyjunior/construtor-solo meu-projeto
```

```bash
cd meu-projeto && rm -rf .git README.md
```

Também dá para baixar o ZIP pelo GitHub (**Code → Download ZIP**) e apagar o `README.md`.

> Por que apagar o `.git` e o `README.md`? O kit trata o Root como área protegida: nada solto e nenhum git ali. Se ficarem, o `verificar-ambiente.sh` avisa ao abrir a sessão.

**Tem um projeto existente?** Coloque o código (incluindo o `.git` dele) dentro de `Projeto/`. O `/iniciar-projeto` faz o mapeamento e propõe mover para `Projeto_Documento/` o que for documentação ou sensível.

## Uso

1. Abra o Claude Code na pasta `meu-projeto/` (o Root, nunca dentro de `Projeto/`).
2. Rode `/iniciar-projeto` e responda às fases. Ele pergunta se pode criar o git em `Projeto/` e ligar a trava de commit:
   ```bash
   git -C Projeto config core.hooksPath ../.claude/git-hooks
   ```
3. Com o roadmap aprovado, rode `/etapa`. Repita a cada etapa.
4. Quando a etapa 1 criar o esqueleto do projeto, preencha `.claude/comandos.conf` (build, formatação, execução) para ligar o build e a formatação automáticos.

## Personalização

- **`.claude/comandos.conf`**: os comandos de build, formatação e execução da sua stack. Os exemplos são de .NET.
- **`.claude/modelos/convencoes/`**: modelos de convenção de código (`dotnet.md`, `banco-relacional.md`). No `/iniciar-projeto`, o Claude parte deles para criar `.claude/rules/<stack>.md`.
- **`.claude/modelos/documentos.md`**: os modelos de objetivo, arquitetura, roadmap, etapa e decisão.
- **`.claude/hooks/padroes-segredos.txt`**: as expressões regulares de segredo usadas pelo hook e pelo pre-commit.

## Regras que o Claude segue

- Não implementa nada fora da etapa em andamento. Uma ideia nova vai para "Fora do escopo e ideias" do roadmap.
- Não diz que algo está pronto sem rodar o comando que prova e citar a evidência.
- Não cria nem altera testes automatizados: eles são escritos por você.
- Não faz commit nem push sem você pedir.
- Entre duas interpretações, pergunta.
