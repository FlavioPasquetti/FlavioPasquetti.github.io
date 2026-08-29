# Política de privacidade — site estático

As duas páginas desta pasta são a política de privacidade do **Multi Timer**
(`br.com.exiss.timer`), prontas para o GitHub Pages. Elas existem porque a Play
Console exige uma URL pública para a política, e a revisão da loja **abre essa
URL**.

```
index.html      português do Brasil
en/index.html   inglês
.nojekyll       desliga o Jekyll do GitHub Pages
```

## Não edite o HTML

As duas páginas são **geradas** a partir dos arquivos de texto:

```
store/politica_de_privacidade_pt-BR.txt   (só o trecho entre os marcadores INICIO/FIM)
store/privacy_policy_en-US.txt
```

Editar o `.txt` e rodar o gerador é o fluxo inteiro:

```powershell
powershell -File store\render_privacy_pages.ps1
```

Mexer no HTML direto cria uma segunda fonte da verdade. No dia em que a política
mudar — e ela muda sempre que uma biblioteca entra, uma permissão sai ou as regras
de backup mudam — o texto e a página divergem, e quem descobre a divergência é o
revisor da Play.

## Como publicar

1. Crie um repositório **público** no GitHub, só para isto. Sugestão de nome:
   `exis-privacy`. No plano gratuito, o Pages só funciona em repositório público —
   e como a política é pública de qualquer forma, isso não custa nada. O que não
   pode é usar o repositório do app, que ficaria público junto.

2. Suba **o conteúdo desta pasta** na raiz do repositório: `index.html`, a pasta
   `en/` e o `.nojekyll`. Este `README.md` pode ir junto; ele não é publicado
   como página.

3. No repositório: **Settings → Pages → Source: Deploy from a branch**, branch
   `main`, pasta `/ (root)`. Salve e espere um ou dois minutos.

4. A URL fica:

   ```
   https://SEU-USUARIO.github.io/exis-privacy/       (português)
   https://SEU-USUARIO.github.io/exis-privacy/en/    (inglês)
   ```

5. Abra as duas **de fora** — outro navegador, ou o celular na rede móvel, sem
   estar logado no GitHub. É assim que o revisor vai abrir.

6. Cole a URL em português no campo **Política de Privacidade** da ficha da Play
   Console, e a inglesa na ficha `en-US`, se você publicar as duas.

7. Volte ao `.txt` e substitua `[URL DESTA POLÍTICA]` e `[URL DA VERSÃO EM INGLÊS]`
   pelas URLs reais. Rode o gerador de novo e suba. **A política precisa apontar
   para si mesma** — o cabeçalho promete um endereço permanente, e um marcador em
   colchetes no ar é o tipo de coisa que a revisão vê.

## Escolha o nome do repositório uma vez só

A URL vai para a Play Console e, quando o link dentro do app existir, para dentro
do aplicativo. Renomear o repositório ou o usuário do GitHub quebra o endereço, e
link quebrado na ficha é motivo de recusa.

## Antes de subir

- Preencher os marcadores em colchetes. A lista está no fim de
  `store/politica_de_privacidade_pt-BR.txt`.
- Ler as quatro linhas `[DATA DA ...]`: **não são lacunas, são promessas de mudar
  o aplicativo** até a data que você escrever.
- Conferir que este repositório não tem nada do app: nada de `local.properties`,
  nada de `.jks` ou `.keystore`, nada de identificador real do AdMob.
