# BDC Performance — landing page

Site estático, sem build e sem subpastas: todos os arquivos ficam na raiz.
Sobe direto no Vercel.

## Subir no Vercel

**Pelo painel:** New Project, arrastar esta pasta, Framework Preset `Other`,
Build Command vazio, Output Directory vazio. Deploy.

**Pelo CLI:**

    npm i -g vercel
    cd bdc-performance
    vercel --prod

Depois, em Settings > Domains, apontar `bdcperformance.basedecampeao.com.br`.
No DNS do domínio, criar um CNAME do subdomínio para `cname.vercel-dns.com`.

## Arquivos

| Arquivo | Função |
|---|---|
| `index.html` | A landing page inteira. CSS e JS embutidos, sem framework. |
| `vercel.json` | URLs limpas, cabeçalhos de segurança e cache. |
| `logo.svg` | **PLACEHOLDER.** Trocar pelo logo oficial, mantendo o nome do arquivo. |
| `og-bdc-performance.jpg` | Imagem 1200x630 do preview no WhatsApp e nas redes. |
| `favicon.ico`, `apple-touch-icon.png`, `icon-192.png`, `icon-512.png` | Ícones. |
| `site.webmanifest` | Nome e ícones quando alguém salva na tela inicial. |
| `politica-de-privacidade.html`, `termos-de-uso.html`, `politica-de-reembolso.html` | Páginas ligadas pelo rodapé. **Rascunhos.** |
| `404.html` | Página de erro. |
| `robots.txt`, `sitemap.xml` | Indexação. |

## Antes de publicar

1. **Logo.** Substituir `logo.svg` pelo arquivo oficial do Base de Campeão.
   É usado no cabeçalho, no rodapé e nas páginas de política.
2. **Páginas de política.** Preencher todo campo marcado `[PREENCHER]`
   (razão social, CNPJ, e-mail de contato, cidade do foro) e revisar com apoio jurídico.
3. **Fluxo pós-pagamento.** No Mercado Pago, configurar a URL de retorno de pagamento
   aprovado apontando para o link do questionário de anamnese.

## Checkout

Links configurados em `index.html`, no objeto `CHECKOUT` ao final do arquivo:

- Mensal R$ 199: `https://mpago.la/2ZcQpEE`
- Trimestral R$ 597: `https://mpago.li/2spKyQy`

Cada clique dispara o evento `inicio_checkout` no dataLayer, com `plano`, `valor` e `moeda`,
para montar a conversão no GTM.

## Medição

O contêiner `GTM-TM8VZL7R` já está instalado no `<head>` e logo após o `<body>`.
Meta Pixel e GA4 (`G-B0PTPE9K2C`) devem entrar por dentro do GTM, não direto no HTML.

A página ainda não tem banner de cookies. Como o GTM carrega medição de terceiros,
vale decidir se entra um banner de consentimento.

## Editar a página

Tudo em `index.html`. As seções estão marcadas por comentários numerados
(`1. HERO`, `2. O PROBLEMA`, e assim por diante).

As cores saem das variáveis no topo do `<style>`:

    --volt:#C6F806    destaque
    --bg:#090B0E      fundo
    --card:#161B24    cartões
    --border:#232B38  bordas

As armadilhas alternam entre fundo volt e fundo escuro por `nth-child`.
Acrescentar ou reordenar uma armadilha reorganiza o padrão sozinho.
