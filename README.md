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
| `black.html` | Landing page da Black Friday, em `/black`. Mesma estrutura, oferta diferente. |
| `og-black-friday.jpg` | Imagem de compartilhamento da página da Black. |
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

## Black Friday

A página da campanha fica em `/black` e vive junto com a padrão, no mesmo deploy.
A padrão (`index.html`) continua intacta, vendendo R$199 por mês e R$597 no trimestre.

Oferta da Black, só no ciclo trimestral:

- R$197 no ciclo de 90 dias, de R$597, ou 3x de R$65,67 sem juros
- Somente 20 vagas
- O plano mensal continua na página, em bloco secundário, por R$199

### Atualizar as vagas vendidas

No fim de `black.html`, uma linha só:

    var VAGAS = { total: 20, vendidas: 0 };

Mude `vendidas` conforme as assinaturas entram. A barra de progresso, os dois contadores
e a tarja do topo se ajustam sozinhos. Chegando em 20, a página troca para esgotado
e os botões passam a apontar para o ciclo normal de R$597, sem precisar mexer em mais nada.

### Falta o link de pagamento da Black

Em `assinar.html`, no objeto `CHECKOUT`, o campo `black` está vazio.
É preciso criar no Mercado Pago um link de R$197 em até 3x sem juros e colar ali.
Enquanto estiver vazio, quem escolher a condição de Black é levado ao WhatsApp
com os dados já preenchidos, em vez de ir para um checkout com o valor errado.

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

Formspree, já configurado em `assinar.html`:

    var LEAD_ENDPOINT = "https://formspree.io/f/xgakdnjo";

Cada envio chega por e-mail e fica registrado no painel do Formspree, com os campos
em português: Responsável, WhatsApp, E-mail, Atleta, Idade, Plano, Valor e Origem.
O assunto do e-mail já vem com o nome e a idade do atleta.

O e-mail informado no formulário entra como responder-para, então dá para responder
o lead direto da caixa de entrada.

Três coisas a saber:

1. **O primeiro envio precisa ser confirmado.** O Formspree manda um e-mail de ativação
   no primeiro recebimento. Faça um envio de teste antes de divulgar a página.
2. **O plano gratuito tem limite mensal de envios.** Se a campanha passar disso,
   os envios seguintes são bloqueados. Vale acompanhar o painel durante a Black.
3. **O envio nunca trava a venda.** Se o Formspree demorar ou falhar, depois de
   2,5 segundos o cliente é mandado para o pagamento do mesmo jeito, e o lead fica
   salvo no navegador dele. Trocar de serviço depois é só trocar a URL:
   o envio é um POST em JSON, que qualquer webhook aceita.

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
