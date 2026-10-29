<!--
CONSTRUTOR SOLO: o kit numa sessão só, etapa por etapa (este comentário não é enviado ao Claude)

ESTRUTURA FIXA. NÃO É ALTERÁVEL. Abra o Claude Code sempre no Root.

  Root/
    CLAUDE.md, .claude/     kit do Claude: regras, skills, hooks, permissões
    Projeto/                onde o projeto é construído. Único repositório git. Versionado, sobe para o GitHub.
    Projeto_Documento/      onde fica a documentação do projeto. Não entra em git, GitHub nem em lugar nenhum.
      objetivo.md, arquitetura.md, roadmap.md, etapas/, decisoes/
      sensivel/             chaves, certificados, credenciais, documentos sensíveis (o Claude não lê)

  /iniciar-projeto  → uma vez: objetivo, perguntas, ambiente e tecnologias, roadmap
  /etapa            → a cada etapa: revisa a anterior, apresenta a próxima, perguntas, execução

Travas da estrutura (além das regras abaixo):
  .claude/hooks/proteger-estrutura.sh  nega o Claude criar documentação ou arquivo sensível em Projeto/
                                       e criar qualquer coisa solta no Root
  .claude/git-hooks/pre-commit         nega commit com documentação, arquivo sensível ou segredo
                                       (ligado com: git -C Projeto config core.hooksPath ../.claude/git-hooks)
  Projeto/.gitignore                   ignora .md (menos o README.md), docs/, .env e chaves
  .claude/settings.json                nega leitura e escrita em Projeto_Documento/sensivel/
  .claude/hooks/verificar-ambiente.sh  avisa ao abrir a sessão se a estrutura estiver fora do padrão

Outros hooks: formatação após edição, build ao fim da resposta, bloqueio de comandos destrutivos
e de segredos no código. Comandos de build, formatação e execução: .claude/comandos.conf (rodam
dentro de Projeto/). Requisitos na máquina: git e jq.
-->

# Estrutura de pastas: regra fixa, não alterável

Todo projeto segue exatamente esta estrutura. Ela não muda, não tem exceção e não é negociável, nem a pedido.

```
Root/
├── CLAUDE.md, .claude/     kit do Claude (regras, skills, hooks)
├── Projeto/                onde o projeto é construído
└── Projeto_Documento/      onde fica a documentação do projeto
    └── sensivel/           chaves, certificados, credenciais, documentos sensíveis
```

**`Root/Projeto/` é onde o projeto é construído.**
- Só código e o que é necessário para compilar e rodar.
- É o **único** repositório git: é versionado e sobe para o GitHub.
- Nunca recebe documentação: nenhum `.md` além do `README.md` da raiz de `Projeto/`, nenhuma pasta `docs/`, nenhuma nota, plano ou relatório.
- Nunca recebe nada sensível: nem chave, certificado, `.env` com valores reais, connection string com senha. Na configuração versionada vão só nomes de variáveis ou valores de exemplo; o sistema lê os segredos em tempo de execução (variável de ambiente, user-secrets, cofre).

**`Root/Projeto_Documento/` é onde fica a documentação do projeto.**
- Toda a documentação: objetivo, arquitetura, roadmap, etapas, decisões, notas, relatórios.
- **Não é adicionada a nada**: não entra em git, não vai para o GitHub, não é copiada para `Projeto/` nem para qualquer outro lugar. Existe unicamente nesta pasta. Ponto final.
- `Projeto_Documento/sensivel/` guarda chaves, certificados, credenciais e documentos sensíveis. Você não lê nem grava nessa pasta.

**O que você pode e não pode fazer:**
- Você acessa o Root e tudo dentro dele, para ler.
- Código: só em `Projeto/`. Documentação: só em `Projeto_Documento/`. Não crie nada solto no Root além do que já é do kit (`CLAUDE.md`, `.claude/`).
- Git só dentro de `Projeto/` (`git -C Projeto ...`). Nunca rode `git init` no Root nem em `Projeto_Documento/`.
- Não renomeie, mova, junte nem crie variações destas pastas (`docs/`, `Documentacao/`, uma segunda pasta de documentos).
- Se alguém pedir para versionar ou subir documentação ou dados sensíveis, ou para mudar esta estrutura, não faça: explique que é a regra fixa do kit.
- A sessão deve ser aberta no Root. Se perceber que foi aberta dentro de `Projeto/`, avise o usuário: as skills e os hooks do kit não carregam.

# Projeto

(Esta seção é preenchida no final do `/iniciar-projeto`.)

## Como trabalhamos

- O projeto avança por etapas definidas em `Projeto_Documento/roadmap.md`. Trabalho novo começa pelo `/etapa`.
- Não implemente nada fora da etapa em andamento. Ideia ou pedido novo vai para a seção "Fora do escopo e ideias" do roadmap, e você pergunta antes de agir.
- Depois de aprovado, `Projeto_Documento/objetivo.md` só muda com aprovação explícita do usuário, registrada em `Projeto_Documento/decisoes/`.
- Decisão técnica relevante (biblioteca, padrão, formato de dados, infraestrutura) vira um arquivo em `Projeto_Documento/decisoes/`, no modelo de `.claude/modelos/documentos.md`.
- Entre duas interpretações possíveis, pergunte. Não preencha lacunas com suposições.
- Antes de concordar com uma proposta, sua ou do usuário, aponte um caso concreto em que ela falha, a menor mudança que resolve o problema real e o custo de cada caminho.
- **Testes automatizados são escritos pelo usuário.** Não crie nem altere arquivos de teste. Se existirem, você pode rodá-los como evidência.
- Não diga que algo está pronto, funcionando ou corrigido sem ter rodado agora o comando que prova (build, execução, chamada real) e lido a saída. Cite a evidência.
- Uma etapa só está concluída quando cada critério de aceite foi verificado com evidência.
- Bug, erro ou comportamento inesperado: siga a skill `depurar` antes de mudar o código.
- Não faça commit nem push sem o usuário pedir.

## Como escrever código

- Antes de escrever, pare no primeiro "sim": precisa existir? Já existe no projeto? A biblioteca padrão ou o framework resolvem? Uma dependência já instalada resolve? Só então escreva o mínimo que funciona. Validação de entrada, segurança e proteção contra perda de dados nunca entram nesse corte.
- Siga o estilo dos arquivos vizinhos. Não crie abstração, configuração ou camada sem um segundo uso real.
- Retry, timeout, espera, intervalo, tamanho de lote ou limite: declare valor e unidade, o motivo em uma linha e o que acontece quando esgota. Número sem motivo: pergunte.

## Comandos

Ficam em `.claude/comandos.conf` (build, formatação, execução) e rodam dentro de `Projeto/`. Leia esse arquivo antes de compilar ou rodar o projeto.
