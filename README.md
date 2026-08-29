# Políticas de privacidade — site estático

Site das políticas de privacidade dos aplicativos da EXIS, pronto para o GitHub
Pages. Ele existe porque a Play Console exige uma URL pública para a política, e a
revisão da loja **abre essa URL**.

```
index.html                      índice dos aplicativos (inglês)
multi-timer/index.html          política em inglês      <- ficha en-US
multi-timer/pt-BR/index.html    política em português   <- ficha pt-BR
.nojekyll                       desliga o Jekyll do GitHub Pages
```

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

1. Crie um repositório **público** no GitHub, só para isto. Sugestão: `exis-privacy`.
   No plano gratuito, o Pages só funciona em repositório público — e como as
   políticas são públicas de qualquer forma, isso não custa nada. O que não pode é
   usar o repositório do app, que ficaria público junto.

2. Suba **o conteúdo desta pasta** na raiz do repositório. Este `README.md` pode ir
   junto; ele não é publicado como página.

3. **Settings → Pages → Source: Deploy from a branch**, branch `main`, pasta
   `/ (root)`. Salve e espere um ou dois minutos.

4. As URLs ficam:

   ```
   https://SEU-USUARIO.github.io/exis-privacy/                    índice
   https://SEU-USUARIO.github.io/exis-privacy/multi-timer/        inglês
   https://SEU-USUARIO.github.io/exis-privacy/multi-timer/pt-BR/  português
   ```

5. Abra as três **de fora** — outro navegador, ou o celular na rede móvel, sem
   estar logado no GitHub. É assim que o revisor vai abrir.

6. Na Play Console, campo **Política de Privacidade**: a URL em inglês na ficha
   `en-US`, a em português na ficha `pt-BR`.

7. Volte aos `.txt` e substitua `[URL DESTA POLÍTICA]`, `[URL DA VERSÃO EM INGLÊS]`
   e `[URL OF THE PORTUGUESE VERSION]` pelas URLs reais. Rode o gerador de novo e
   suba. **A política precisa apontar para si mesma** — o cabeçalho promete um
   endereço permanente, e um marcador em colchetes no ar é o tipo de coisa que a
   revisão vê.

## Escolha o nome do repositório uma vez só

A URL vai para a Play Console e, quando o link dentro do app existir, para dentro
do aplicativo. Renomear o repositório ou o usuário do GitHub quebra o endereço, e
link quebrado na ficha é motivo de recusa. É a mesma lógica do `applicationId`:
campo barato de escolher agora, caro de trocar depois.

## Antes de subir

- Preencher os marcadores em colchetes, inclusive o `[CONTACT EMAIL]` da página
  inicial. A lista completa está no fim de
  `store/politica_de_privacidade_pt-BR.txt`.
- Ler as quatro linhas `[DATA DA ...]`: **não são lacunas, são promessas de mudar
  o aplicativo** até a data que você escrever.
- Conferir que este repositório não tem nada do app: nada de `local.properties`,
  nada de `.jks` ou `.keystore`, nada de identificador real do AdMob.
