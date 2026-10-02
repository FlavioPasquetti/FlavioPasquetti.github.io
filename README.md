# Políticas de privacidade — site estático

Site das políticas de privacidade dos aplicativos da EXIS, pronto para o GitHub
Pages. Ele existe porque a Play Console exige uma URL pública para a política, e a
revisão da loja **abre essa URL**.

```
index.html                      índice dos aplicativos (inglês)
multi-timer/index.html          política em inglês      <- ficha en-US
multi-timer/pt-BR/index.html    política em português   <- ficha pt-BR
.nojekyll                       desliga o Jekyll do GitHub Pages
app-ads.txt                     autoriza a conta AdMob a vender os anúncios
```

## app-ads.txt

O AdMob só verifica o app depois de achar este arquivo **na raiz do domínio** que
está no campo **Site** da ficha da Play (Configurações da loja → Detalhes de
contato). A URL da política não serve para isso: o rastreador lê o campo Site, tira
o caminho e pede `https://flaviopasquetti.github.io/app-ads.txt`. Sem o campo Site
preenchido, o arquivo pode existir e a verificação falha do mesmo jeito.

A linha é `google.com, <ID do editor>, DIRECT, f08c47fec0942fa0`. O ID do editor é
o `pub-…` do identificador do app no AdMob (a parte antes do `~`). Ele não é
segredo: o arquivo é público por definição, e o mesmo ID já vai dentro do APK.

O gerador não apaga nada desta pasta, então o arquivo sobrevive a uma nova
renderização. Um segundo app com AdMob na mesma conta não precisa de outra linha:
a autorização é da conta, não do app.

O **inglês fica na raiz de cada aplicativo** porque a ficha padrão da Play é a
en-US: é a URL que mais gente abre, e a que o revisor abre primeiro.

## Não edite o HTML

As páginas são **geradas** a partir dos arquivos de texto que estão em `store/`:

```
politica_de_privacidade_pt-BR.txt   (só o trecho entre os marcadores INICIO/FIM)
privacy_policy_en-US.txt
```

Editar o `.txt` e rodar o gerador é o fluxo inteiro:

```powershell
powershell -File store\render_privacy_pages.ps1
```

Mexer no HTML direto cria uma segunda fonte da verdade. No dia em que a política
mudar — e ela muda sempre que uma biblioteca entra, uma permissão sai ou as regras
de backup mudam — o texto e a página divergem, e quem descobre a divergência é o
revisor da Play.

## Acrescentar o próximo aplicativo

Uma entrada em `$apps`, no topo do gerador, mais os arquivos de texto que ela
aponta. **Nada de HTML.** A barra lateral, o índice de seções, os links entre
idiomas e o cartão da página inicial saem daí sozinhos.

```powershell
@{
    slug    = 'nome-do-app'          # vira a pasta e a URL
    name    = 'Nome do App'          # o que aparece na barra lateral
    store   = 'Título na Play'
    package = 'br.com.exiss.oapp'
    blurb   = 'One line, in English, for the home page card.'
    langs   = @(
        @{ code = 'en';    dir = '';      label = 'English'
           title = 'Privacy Policy';      toc = 'Contents'
           file = 'privacy_nome_do_app_en-US.txt' }
        @{ code = 'pt-BR'; dir = 'pt-BR'; label = 'Portugu&ecirc;s (Brasil)'
           title = 'Pol&iacute;tica de Privacidade'; toc = 'Conte&uacute;do'
           file = 'politica_nome_do_app_pt-BR.txt' }
    )
}
```

O primeiro idioma da lista é o principal e mora na raiz do aplicativo; os demais
ganham subpasta. Acentos nessas literais vão como entidade HTML — o motivo está no
cabeçalho do gerador, e não é preciosismo: o PowerShell 5.1 lê um `.ps1` sem BOM
como ANSI e um acento cru sai corrompido na página publicada.

Cada política nova precisa do mesmo tratamento que esta teve: ela descreve **um
aplicativo específico**, com as bibliotecas dele, as permissões dele e o que ele
guarda. Copiar a do Multi Timer e trocar o nome produz um documento que afirma
coisas falsas — que é exatamente o defeito que uma política não pode ter.

## Como publicar

1. Crie um repositório **público** chamado exatamente **`flaviopasquetti.github.io`**.

   O nome não é escolha de gosto: um repositório com esse nome é o *site de
   usuário* do GitHub Pages, servido na **raiz** do domínio. É isso que faz a URL
   já registrada nas políticas — `https://flaviopasquetti.github.io/multi-timer/`
   — funcionar sem nenhuma pasta a mais no meio. Um repositório chamado
   `multi-timer` serviria em `.../multi-timer/` e a política cairia em
   `.../multi-timer/multi-timer/`.

   No plano gratuito o Pages só funciona em repositório público — e como as
   políticas são públicas de qualquer forma, isso não custa nada. O que não pode é
   usar o repositório do app, que ficaria público junto.

2. Suba **o conteúdo desta pasta** na raiz do repositório. Este `README.md` pode ir
   junto; ele não é publicado como página.

3. **Settings → Pages → Source: Deploy from a branch**, branch `main`, pasta
   `/ (root)`. Salve e espere um ou dois minutos.

4. As URLs ficam:

   ```
   https://flaviopasquetti.github.io/                    índice dos apps
   https://flaviopasquetti.github.io/multi-timer/        inglês
   https://flaviopasquetti.github.io/multi-timer/pt-BR/  português
   ```

   As duas últimas **já estão escritas dentro das políticas**, no campo "endereço
   permanente". Se você mudar o nome do repositório, mude também os dois `.txt` e
   rode o gerador de novo.

5. Abra as três **de fora** — outro navegador, ou o celular na rede móvel, sem
   estar logado no GitHub. É assim que o revisor vai abrir.

6. Na Play Console, campo **Política de Privacidade**: a URL em inglês na ficha
   `en-US`, a em português na ficha `pt-BR`.

## Escolha o nome do repositório uma vez só

A URL já está dentro das políticas, vai para a Play Console e, quando o link dentro
do app existir, para dentro do aplicativo. Renomear o repositório ou o usuário do
GitHub quebra o endereço em todos esses lugares, e link quebrado na ficha é motivo
de recusa. É a mesma lógica do `applicationId`: campo barato de escolher agora,
caro de trocar depois.

## Antes de subir

- **Completar o endereço.** Hoje as políticas dizem "Rua Santa Maria, 424, apto.
  702" e nada mais: falta bairro, cidade, estado, CEP e país. Num aviso legal — e
  para a COPPA, que exige o endereço do operador no aviso a responsáveis — ele
  precisa permitir localizar a pessoa.
- Preencher os marcadores que ainda restam. A lista está no fim de
  `store/politica_de_privacidade_pt-BR.txt`: foro, encarregado, representantes na
  UE e no Reino Unido, faixas etárias e as quatro datas.
- Ler as quatro linhas `[DATA DA ...]`: **não são lacunas, são promessas de mudar
  o aplicativo** até a data que você escrever.
- Trocar o título na Play Console para **EXISS Visual Timer & Focus**. Ele é campo
  do Console, não do pacote, e não se atualiza sozinho — a ficha sairia com "EXIS"
  no topo e "EXISS" no corpo.
- Conferir que este repositório não tem nada do app: nada de `local.properties`,
  nada de `.jks` ou `.keystore`, nada de identificador real do AdMob.

## O que fica público aqui

Nome completo, endereço, e-mail e celular pessoais, numa página aberta e indexada
por buscador. O telefone tem razão de estar: a COPPA (16 CFR 312.4(d)(1)) exige
telefone no aviso a responsáveis, e a seção 11 é um aviso a responsáveis — não dá
para tirá-lo sem sair da COPPA. O que existe é trocar a pessoa pela empresa: aberta
uma PJ, esses quatro campos passam a ser os dela, e a atualização é reeditar os
dois `.txt` e rodar o gerador.
