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
| `assinar.html` | Página de captação. Coleta os dados antes de mandar para o pagamento. |
| `vercel.json` | URLs limpas, cabeçalhos de segurança e cache. |
| `logo.png` | Logo oficial do Base de Campeão, fundo transparente. |
| `og-bdc-performance.jpg` | Imagem 1200x630 do preview no WhatsApp e nas redes. |
| `favicon.ico`, `apple-touch-icon.png`, `icon-192.png`, `icon-512.png` | Ícones. |
| `site.webmanifest` | Nome e ícones quando alguém salva na tela inicial. |
| `politica-de-privacidade.html`, `termos-de-uso.html`, `politica-de-reembolso.html` | Páginas ligadas pelo rodapé. **Rascunhos.** |
| `404.html` | Página de erro. |
| `robots.txt`, `sitemap.xml` | Indexação. |

## Antes de publicar

1. **Webhook dos leads.** Preencher `LEAD_ENDPOINT` em `assinar.html` (ver seção abaixo).
   Sem isso os dados do formulário não chegam até você.
2. **Páginas de política.** Preencher todo campo marcado `[PREENCHER]`
   (razão social, CNPJ, e-mail de contato, cidade do foro) e revisar com apoio jurídico.
3. **Fluxo pós-pagamento.** No Mercado Pago, configurar a URL de retorno de pagamento
   aprovado apontando para o link do questionário de anamnese.

## Fluxo de assinatura

    index.html  →  /assinar?plano=X  →  Mercado Pago  →  questionário de anamnese
      botão          formulário            pagamento        (URL de retorno do MP)

O botão do plano não leva mais direto ao pagamento. Leva para `assinar.html`, que coleta
nome do responsável, WhatsApp e/ou e-mail, nome do atleta e idade, com o aceite da
Política de Privacidade. Só depois redireciona para o link de pagamento.

Links configurados em `assinar.html`, no objeto `CHECKOUT`:

- Mensal R$ 199: `https://mpago.la/2ZcQpEE`
- Trimestral R$ 597: `https://mpago.li/2spKyQy`

Eventos no dataLayer: `inicio_checkout` ao clicar no plano e `lead_capturado` ao enviar o formulário.

### Para onde vão os leads

Abra `assinar.html` e preencha `LEAD_ENDPOINT` com a URL de um webhook.
Serve qualquer um: Make, Zapier, n8n, uma função sua. O caminho mais rápido e sem custo
é o Google Apps Script gravando numa planilha.

1. Crie uma planilha no Google Sheets com os cabeçalhos:
   `data`, `responsavel`, `whatsapp`, `email`, `atleta`, `idade`, `plano`, `valor`, `origem`
2. Menu Extensões, Apps Script, e cole:

```js
function doPost(e) {
  var d = JSON.parse(e.postData.contents);
  SpreadsheetApp.getActiveSheet().appendRow([
    new Date(), d.responsavel, d.whatsapp, d.email,
    d.atleta, d.idade, d.plano, d.valor, d.origem
  ]);
  return ContentService.createTextOutput('ok');
}
```

3. Implantar, Nova implantação, tipo App da Web.
   Executar como: eu. Quem pode acessar: qualquer pessoa.
4. Copie a URL gerada e cole em `LEAD_ENDPOINT`.

Enquanto o campo estiver vazio, o lead ainda vai para o dataLayer e fica salvo no
navegador do visitante, mas não chega até você. O cliente segue para o pagamento
de qualquer forma: o envio nunca trava a venda.

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
