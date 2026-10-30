# Santa Helena Consultoria Agrícola — site institucional

Site estático de página única da Santa Helena Consultoria Agrícola, consultoria em heveicultura (seringais).
Substitui o site antigo da empresa.

- **Produção:** https://santahelenaagricola.github.io
- **Repositório:** `SantaHelenaAgricola/santahelenaagricola.github.io`

Este documento registra **o que foi decidido e por quê**, para quem for dar manutenção depois.

---

## Sumário

1. [Estrutura do projeto](#estrutura-do-projeto)
2. [Como rodar e editar](#como-rodar-e-editar)
3. [Publicação (deploy)](#publicação-deploy)
4. [Decisões e motivos](#decisões-e-motivos)
5. [Origem do conteúdo](#origem-do-conteúdo)
6. [Convenção de commits](#convenção-de-commits)
7. [Pendências e pontos de atenção](#pendências-e-pontos-de-atenção)

---

## Estrutura do projeto

```
.
├── index.html          página única (todas as seções)
├── css/style.css       estilos, com tokens de cor e escala de espaçamento no :root
├── js/main.js          menu mobile, botões do carrossel e ano do rodapé
├── images/
│   ├── logo.png        logo original da empresa
│   ├── favicon.svg
│   └── campo/          13 fotos da galeria "Em campo" (350×223 px)
├── .nojekyll           faz o GitHub Pages servir os arquivos como estão
└── README.md
```

Seções de `index.html`, na ordem: topo (hero), Quem somos, Serviços, Em campo (galeria), Contato, rodapé.

## Como rodar e editar

Não há build, gerenciador de pacotes nem dependências instaláveis. Abra o `index.html` no navegador.

Para servir localmente (opcional):

```bash
python -m http.server 8000
# http://localhost:8000
```

Tarefas comuns:

| Tarefa | Onde |
|---|---|
| Trocar telefone, e-mail ou pessoa de contato | seção `#contato` em `index.html` (telefones em `tel:+55…`, WhatsApp em `wa.me/55…`) |
| Editar o texto de um serviço | lista `.services` em `index.html` |
| Adicionar foto à galeria | colocar o arquivo em `images/campo/` e copiar um `<li>` dentro de `ul.rail` |
| Mudar cores, fontes ou espaçamento | variáveis no início de `css/style.css` |

## Publicação (deploy)

- GitHub Pages, **fonte = branch `main`, pasta raiz**. Todo push na `main` publica sozinho em ~1 min.
- O GitHub roda o workflow interno "pages build and deployment". Não há arquivo em `.github/workflows`.
- Se o navegador mostrar a versão antiga, use Ctrl+F5, porque o Pages guarda cache por alguns minutos.

## Decisões e motivos

### Tecnologia

**HTML, CSS e JS puros, sem framework nem build.**
O site tem uma página e conteúdo que muda raramente. Sem build, qualquer pessoa edita um arquivo e dá push, e não há dependência para ficar desatualizada. Se o site crescer para várias páginas ou precisar de formulário, vale reavaliar.

**Página única com menu por âncoras.**
O site antigo tinha 6 páginas, mas cada uma tinha um parágrafo. Reunir tudo em uma página deixa a navegação mais rápida e simples.

**Caminhos sempre relativos** (`images/…`, `css/…`).
Funciona em qualquer endereço, seja na raiz do `github.io`, em subpasta ou em domínio próprio, sem alterar código.

**Fontes via Google Fonts** (Fraunces para títulos, Inter para texto), com fallback para fontes do sistema.
Dá um acabamento mais profissional que as fontes padrão. O custo é depender de um serviço externo, que também envia o IP do visitante ao Google. Se isso for um problema (LGPD), baixar as fontes e hospedá-las em `css/` ou `fonts/`.

### Visual

**Paleta: verde-floresta, creme e terracota.**
O verde vem do logo da empresa. O creme remete ao látex. A terracota vem do solo vermelho que aparece nas fotos de campo. Foi uma troca consciente do primeiro layout, um gradiente verde genérico com cards de template, que foi substituído por parecer genérico demais.

**Serviços como lista numerada (01–04), não cards com ícones.**
Texto mais legível, visual menos genérico e fácil de escanear.

**Escala de espaçamento em múltiplos de 8** (`--s-1` a `--s-7` no `:root`).
O primeiro layout tinha valores soltos e ficava desalinhado. Toda margem e espaço novo deve usar a escala, e não números avulsos.

**Fotos sempre no tamanho original (350×223 px).**
As fotos vieram de um Flash e têm baixa resolução. Esticar fica borrado, então a colagem do topo, a foto de "Quem somos" e o carrossel as mostram em 350 px ou menos. Se houver originais em alta, substituir e então liberar layouts maiores.

**Galeria em carrossel horizontal** (rolagem nativa com `scroll-snap`, mais botões).
Foi a segunda tentativa. A grade de fotos pequenas esticadas parecia ruim. O carrossel reproduz a ideia do slideshow original do Flash, mantém o tamanho nativo e funciona com toque, mouse e teclado.

**Setas do carrossel em SVG, não em caracteres `←` `→`.**
Os glifos de texto ficam abaixo do centro do círculo, e a posição varia conforme a fonte. O SVG centraliza com precisão.

### Conteúdo

**Nenhum número, depoimento ou cliente inventado.**
Todo texto vem do site antigo, reformulado. "Quem somos" é uma reformulação da apresentação original. Não há estatísticas.

**Removidas as seções "Como trabalhamos" e a faixa de chamada.**
Eram preenchimento criado sem base no site original.

**Lema "Estratégias com resultados"** está no selo do topo e no título da galeria. Vem do slideshow original.

**Contatos públicos** (telefone, WhatsApp, e-mail) em links diretos, sem formulário.
Um formulário exigiria backend, antispam e LGPD, e a equipe já atende por telefone e WhatsApp.

### Acessibilidade e comportamento

- Link "Ir para o conteúdo" para navegação por teclado.
- Menu mobile com `aria-expanded` e rótulo que muda entre "Abrir menu" e "Fechar menu".
- Botões e carrossel com `aria-label`. O carrossel recebe foco por teclado.
- Respeita `prefers-reduced-motion`.
- Fotos com `alt` descritivo e `loading="lazy"`.

### Bugs que já aconteceram

**Clique no logo não ia ao topo.**
O `id="topo"` estava no `<header>`, que é `position: sticky`. Como o cabeçalho já está parado no alto da tela, o navegador quase não rolava. O `id` agora fica no `<body>`. Mantenha o alvo `#topo` em elemento que não seja fixo.

**Rótulo "CONTATO" ilegível.**
O estilo `.eyebrow` padrão é verde-limão, feito para fundo escuro. Em fundo claro precisa da variante `.dark` ou de cor própria, como na seção de contato.

## Origem do conteúdo

Tudo foi extraído do site antigo da empresa em outubro de 2026.

| Item | Origem |
|---|---|
| Textos de Projetos, Auditoria, Gestão e Assessoria | páginas de serviços do site antigo |
| Apresentação da empresa | página inicial |
| Contatos | página de contato |
| Logo | imagem do cabeçalho do site antigo |
| **14 fotos e legendas da galeria** | **animação Flash da página inicial**, cujo conteúdo o HTML não mostrava como texto |

> **Lição:** na primeira versão, os arquivos Flash foram ignorados por serem obsoletos, e o conteúdo deles foi perdido. Só foi recuperado depois, lendo o arquivo da animação diretamente: as fotos são JPEGs embutidos e as legendas, textos do arquivo. Ao migrar conteúdo de um site antigo, inspecione também os binários, não só o HTML.

O banner do topo do site antigo tinha 5 slides com fotos de banco de imagens (moedas, calendário, caneta). Foram descartados de propósito, porque os textos já estavam no site novo.

Foram **removidos de propósito** também: o Google Analytics antigo (descontinuado), o widget de compartilhamento e o JavaScript do Flash.

## Convenção de commits

[Conventional Commits](https://www.conventionalcommits.org/pt-br/): `tipo(escopo): descrição` em português, no imperativo, com o escopo opcional.

| Tipo | Quando usar |
|---|---|
| `feat` | funcionalidade ou conteúdo novo |
| `fix` | correção de bug |
| `style` | mudança visual ou de espaçamento, sem alterar o comportamento |
| `refactor` | reorganização de código sem mudar o resultado |
| `docs` | documentação, como este README |
| `chore` | tarefas de manutenção |

Exemplo: `fix(galeria): centraliza as setas do carrossel nos botões`.

Os 5 primeiros commits do repositório, anteriores a esta convenção, estão em texto livre. Foram mantidos para não reescrever o histórico publicado.

**Regra de trabalho acordada:** nenhuma alteração visual vai para a `main` sem ser vista e aprovada antes, localmente.

## Pendências e pontos de atenção

- [x] **Legendas das fotos.** As legendas foram ligadas às fotos pela ordem interna da animação Flash. As fotos de "Café e Seringueira" e "Manejo de plantas daninhas" estavam trocadas; corrigido.
- [ ] **Qualidade das fotos.** São de 350×223 px. Pedir os originais à empresa.
- [ ] **Contatos.** Foram copiados do site antigo. Confirmar com a empresa se continuam atuais, inclusive os domínios de e-mail de cada pessoa.
- [ ] **Imagens de pessoas.** Algumas fotos mostram pessoas. Confirmar com a empresa a autorização para manter as imagens publicadas.
- [ ] **Domínio próprio.** Exige um arquivo `CNAME` na raiz do repositório e um registro DNS apontando para o GitHub Pages. O HTTPS é emitido automaticamente.
- [ ] **Fontes hospedadas localmente**, se a dependência do Google Fonts for um problema.
- [ ] **`.gitattributes`.** O Git avisa sobre conversão LF→CRLF no Windows. Fixar a quebra de linha evita ruído em diffs futuros.
- [ ] **Deploy por GitHub Actions.** Hoje o deploy usa o workflow interno do Pages. Migrar para um workflow no repositório dá mais controle (publicar só arquivos específicos, adicionar etapas), e não é necessário enquanto o site for estático e simples.

## Contas, chaves e repositório

- O site pertence à conta GitHub **SantaHelenaAgricola**. O repositório precisa se chamar `santahelenaagricola.github.io` para publicar na raiz. Qualquer outro nome publica em `…github.io/<nome>/`.
- Nome do repositório igual ao da conta (`SantaHelenaAgricola`) é reservado pelo GitHub para o README do perfil, e por isso não serve para o site.
- Os commits deste repositório usam o e-mail `noreply` da conta, configurado só neste repositório, para não expor um e-mail pessoal.
- Cada pessoa que mantém o site usa a própria chave SSH ou token, cadastrados na conta. Nunca versione chaves, tokens ou senhas.

