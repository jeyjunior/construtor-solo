---
name: etapa
description: Conduz uma etapa do roadmap em ordem fixa (revisão da anterior, apresentação, perguntas, execução, fechamento), lendo e atualizando Projeto_Documento/.
disable-model-invocation: true
argument-hint: "[número da etapa, opcional]"
---

# Etapa

Siga os passos **na ordem**, sem pular nenhum. Regras que valem do começo ao fim:

- **Pare e espere o "ok" do usuário** ao fim dos passos 2, 3 e 4. Não emende um passo no outro na mesma resposta.
- **Implemente só o que está no plano da etapa.** Qualquer coisa nova vai para "Fora do escopo e ideias" em `Projeto_Documento/roadmap.md`, e você avisa o usuário em vez de fazer.
- Se surgir uma decisão que o plano não cobre, pare e pergunte. Depois da resposta, registre em `Projeto_Documento/decisoes/`.
- `Projeto_Documento/objetivo.md` não muda dentro de uma etapa sem aprovação explícita.
- **Código só em `Projeto/`; documentação só em `Projeto_Documento/`.** Notas, planos e relatórios da etapa nunca vão para `Projeto/`.
- **Não escreva nem altere testes automatizados.** Os testes são do usuário.
- Use os modelos de `.claude/modelos/documentos.md`.
- Se o usuário informou um número de etapa (`$ARGUMENTS`), trabalhe nela. Senão, descubra pelo roadmap.

## 1. Situar

1. Leia `Projeto_Documento/roadmap.md` e `.claude/comandos.conf`. Se `Projeto_Documento/objetivo.md`, `Projeto_Documento/arquitetura.md` ou `Projeto_Documento/roadmap.md` não existir ou não estiver `aprovado`, diga que o projeto ainda não foi iniciado e indique `/iniciar-projeto`. Pare.
2. Pela tabela do roadmap, ache a situação e diga ao usuário de onde está partindo:
   - Etapa `em andamento`: leia `Projeto_Documento/etapas/NN-*.md` e continue no passo 5, a partir do primeiro item não marcado do plano.
   - Etapa `planejada`: leia o arquivo da etapa e retome no passo 4.5 (mostrar o plano e pedir o ok).
   - Etapa `concluída` (ainda não revisada): vá ao passo 2.
   - Senão: a próxima etapa é a primeira `pendente`; vá ao passo 3.
   - Todas `revisadas`: diga que o roadmap terminou e pergunte se o usuário quer planejar novas etapas.

## 2. Revisão da etapa anterior

1. Resuma o que foi entregue e mostre a verificação dos critérios de aceite registrada no arquivo da etapa.
2. Rode de novo o `BUILD` de `.claude/comandos.conf` (dentro de `Projeto/`) e informe o resultado.
3. Pergunte se o usuário já testou, se o resultado ficou como esperado e se há algo a ajustar antes de seguir.
4. Para cada ponto levantado, confirme o problema (reproduza ou olhe o código) antes de mudar; se discordar, diga por quê, com arquivo e linha. Ajuste pequeno e dentro do escopo daquela etapa: faça agora (bug: skill `depurar`). Algo maior: vira item em "Fora do escopo e ideias" ou uma nova etapa, com aprovação.
5. Registre a conversa na seção "Revisão" do arquivo da etapa e marque-a como `revisada` no roadmap e no arquivo. Espere o ok para apresentar a próxima.

## 3. Apresentação da próxima etapa

1. Mostre, a partir do roadmap: objetivo, entregáveis, critérios de aceite e o que fica fora.
2. Diga em poucas linhas como pretende fazer.
3. Pergunte se confirma. Se o usuário quiser mudar a etapa, atualize o roadmap só depois de mostrar a alteração e receber o ok.

## 4. Perguntas da etapa

1. Antes de perguntar, confira para você mesmo o que a etapa precisa saber: escopo (o que entra e o que fica fora), dados (entidades, formatos, unicidade), comportamento em erro e quando algo externo falha, números (volume, tempo, limite, sempre com unidade), nomes já usados no projeto, como saber que terminou. Pergunte só o que estiver faltando ou ambíguo **e** mudar a implementação. O que já está nos documentos ou no código não vira pergunta.
2. No máximo 4 perguntas por rodada, numeradas. Quando as alternativas forem poucas e claras, use a ferramenta de múltipla escolha (AskUserQuestion).
3. Crie `Projeto_Documento/etapas/NN-nome.md` com as respostas, os padrões a seguir (arquivos existentes a imitar) e o plano em checklist, cada item com os arquivos de `Projeto/` que vai tocar. Marque a etapa como `planejada` no roadmap.
4. Revise o plano antes de mostrar: nada "a definir", nenhuma contradição com os documentos, nada que exija adivinhar, cada critério de aceite verificável por um comando ou comportamento observável.
5. Mostre o plano. Com o ok, marque a etapa como `em andamento` no roadmap e no arquivo da etapa.

## 5. Execução

1. Siga o checklist, marcando cada item ao terminar.
2. Rode o `BUILD` ao longo do trabalho, não só no final. Erro ou comportamento inesperado: siga a skill `depurar`, sem tentativas no escuro.
3. Registre em "Notas da execução" (no arquivo da etapa, em `Projeto_Documento/`) qualquer desvio, problema ou escolha feita no caminho.

## 6. Fechamento

1. Revise o próprio diff da etapa (`git -C Projeto diff` e arquivos novos) e corrija o que achar, dentro do escopo:
   - código que não precisava existir: abstração sem segundo uso, wrapper que só repassa, configuração para algo que não varia;
   - duplicação: algo que já existia no projeto e foi reescrito;
   - falha silenciosa: erro capturado sem log, `|| true` ou valor padrão escondendo falha real.
2. Confira `git -C Projeto status`: nenhum arquivo de documentação (`.md` além do `README.md`, pasta `docs/`) e nenhum arquivo sensível (`.env`, chave, certificado). Se houver, mova para `Projeto_Documento/` antes de seguir.
3. Verifique cada critério de aceite agora (build, execução, chamada real, testes do usuário se existirem) e registre a evidência: comando executado e trecho da saída. Critério sem evidência fresca não passou: a etapa não está concluída, diga o que falta.
4. Se comandos ou convenções mudaram, atualize `.claude/comandos.conf` ou o arquivo de convenções em `.claude/rules/` e mostre a alteração.
5. Marque a etapa como `concluída` no roadmap e no arquivo da etapa.
6. Mostre um resumo curto do que foi feito e do que a revisão do diff corrigiu. Sugira uma mensagem de commit no formato `<Tipo>: <descrição>`, com o tipo coerente com o diff: `Adicao`, `Correcao` ou `Refatoracao`. O commit é sempre em `Projeto/` e só se o usuário pedir.
7. Encerre dizendo que o usuário pode testar e que o próximo `/etapa` começa pela revisão desta. Não comece a próxima etapa.
