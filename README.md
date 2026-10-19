# exeginhando-bible-data

Texto bíblico das **versões livres** usadas pelo [exeginhando](https://github.com/udoschjr/exeginhando)
— um app de estudo bíblico, gratuito e sem fins lucrativos.

Este repositório existe para **tirar ~26 MB de texto do repositório da aplicação** sem torná-lo
indisponível: cada versão do dataset vira um `.tar.gz` numa release, e a aplicação fixa a URL e o
**SHA-256** num arquivo versionado (`db/bible/source.yml`). Assim o texto fica fora do git, mas
`git clone` + `bin/rails bible:fetch` reconstrói tudo.

## Versões incluídas

| Versão | Sigla | Ano | Licença | Detentor / autoria |
|---|---|---|---|---|
| Almeida 1911 | `ALM1911` | 1911 | domínio público | — |
| Bíblia Livre | `BLIVRE` | 2018 | **CC BY 3.0 Brasil** | Diego Santos, Mario Sérgio e Marco Teles |
| Bíblia Portuguesa Mundial | `PORBRSL` | 2022 | domínio público | World Portuguese Bible |
| Nova Bíblia Viva (Biblica Open) | `PORONBV` | 2007 | **CC BY-SA 4.0** | Biblica, Inc. |
| Tradução para Tradutores | `PORTFT` | 2018 | **CC BY-SA 4.0** | Ellis W. Deibler, Jr. |
| Bíblia Livre Para Todos | `PORBLT` | 2022 | **CC BY-SA 4.0** | Free Bible Ministry, Inc. |

Bíblia Portuguesa Mundial é um rascunho declaradamente em revisão; `PORTFT` é texto de apoio a
tradutores (só Novo Testamento), e não uma tradução de leitura.

> **CC BY / CC BY-SA exigem atribuição.** Quem redistribuir este texto (em qualquer formato) precisa
> manter o nome da versão, a autoria acima e a referência à licença — é o que este README faz.

## Origem

| Fonte | Licença | O que veio |
|---|---|---|
| [damarals/biblias](https://github.com/damarals/biblias) | MIT (código) · textos conforme a tabela | `ALM1911`, `BLIVRE` |
| [eBible.org](https://ebible.org/Scriptures/) | conforme a tabela | `PORBRSL`, `PORONBV`, `PORTFT`, `PORBLT` |

### Por que a Tradução Brasileira não está aqui

A `TB` que circula em datasets públicos é a **edição modernizada da Sociedade Bíblica do Brasil**
(impressa em 2010, digital em 2001), e não a edição de 1917 — a própria SBB descreve a reedição.
Só o texto de 1917 é domínio público, e a SBB afirma deter os direitos da Tradução Brasileira.
Enquanto não houver uma transcrição conferida da edição de 1917, ela fica fora.

## Formato

**`canonical/<SIGLA>/*.json`** — um arquivo por livro, com numeração explícita de capítulo e
versículo (preserva lacuna):

```json
{
  "id": 1, "code": "GEN", "name": "Gênesis", "abbrev": "Gn",
  "chapters": [{ "number": 1, "verses": [{ "number": 1, "text": "No princípio..." }] }]
}
```

**`vpl/<id>.txt`** — um versículo por linha, código de livro USFM:

```
GEN 1:1 No princípio, Deus criou os céus e a terra.
```

O arquivo `SHA256SUMS` lista o hash de cada arquivo do pacote.

## Como consumir

```bash
# fixe version + url + sha256 em db/bible/source.yml, então:
bin/rails bible:fetch    # baixa, confere o SHA-256 e descompacta em tmp/bible_data
bin/rails bible:import   # importa para o banco
```

O detalhamento do formato, do manifesto de versões e do pipeline de importação está no repo da
aplicação, em [`db/bible/README.md`](https://github.com/udoschjr/exeginhando/blob/main/db/bible/README.md).

## Como publicar uma versão nova

```bash
# no repo da aplicação, com o dataset montado em tmp/bible_data:
bin/rails bible:package          # gera tmp/exeginhando-bible-data-<versão>.tar.gz e imprime o sha256
gh release create <versão> tmp/exeginhando-bible-data-<versão>.tar.gz \
  --title "<versão>" --notes "..." -R udoschjr/exeginhando-bible-data
```

Depois, atualizar `version`, `url` e `sha256` em `db/bible/source.yml` no repo da aplicação.
