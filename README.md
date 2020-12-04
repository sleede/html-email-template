# Créer et tester des emails html

[création](#mjml) | [test](#testi)

# MJML

MJML est un langage de balisage *open-source* créer par [Mailjet](https://www.mailjet.com/).  
Grâce à une syntaxe simple et un ensemble de composants, MJML permet de rapidement créer des emails HTML *responsive* et directement compatibles avec une grande majorité de clients mail.  
C'est le moteur MJML qui se charge de compiler le template en HTML.
![code](demo/assets/code.png)

## Documentation

[https://documentation.mjml.io/](https://documentation.mjml.io/)

## Utilisation
<details>
<summary>MJML dispose de nombreuses options d'utilisations :</summary>

- [éditeur en ligne](https://mjml.io/try-it-live)
- [application desktop](https://mjmlio.github.io/mjml-app/)
- dans **VS Code** grâce à une [extension](https://github.com/mjmlio/vscode-mjml)
- en [ligne de commande](https://github.com/mjmlio/mjml/blob/master/packages/mjml-cli/README.md)
- dans [Node.js](https://documentation.mjml.io/#inside-node-js)
- [API](https://mjml.io/api) gratuite
- \+ les outils développés par la [communauté](https://mjml.io/community)...

</details>

## En pratique

#### Structure :

```html
<!-- balise principale -->
<mjml>
  <!-- métadonnées + styles -->
  <mj-head>
    <!-- texte de l'onglet navigateur -->
    <mj-title></mj-title>
    <!-- texte d'aperçu dans la boite mail -->
    <mj-preview></mj-preview>
    <!-- attributs par défaut des balises MJML + class -->
    <mj-attributes>
      <mj-all padding="0" />
      <mj-text font-size="18px" line-height="24px" />
      <mj-class name="header" font-size="48px" />
    </mj-attributes>
    <!-- styles appliqués au HTML généré -->
    <mj-style>
      .primary div {
        color: tomato !important;
      }
    </mj-style>
  </mj-head>
  
  <!-- corps du mail -->
  <mj-body>
    <!--  -->
    <mj-section>
      <mj-column>
        <mj-text mj-class="header">Hello World !</mj-text>
        <mj-text css-class="primary">Lorem ipsum dolor sit amet...</mj-text>
      </mj-column>
    </mj-section>
  </mj-body>
</mjml>
```
---

#### Style :

MJML va générer une structure de plusieurs éléments HTML en convertissant un composant.  
:warning: Une `<css-class>` est toujours appliquée à l'élément parent. Pour appliquer correctement un style il va donc parfois être nécessaire d'inspecter le code généré pour cibler le bon élément enfant.  
:warning: Comme les styles *inline* sont très utilisés dans les emails HTML il est indispensable de toujours ajouter `!important`.  
![specificity](./demo/assets/specificity.gif)  
[specificity.mjml](./demo/specificity.mjml) : règles de spécificité.

---

#### Unitées :
La largeur des `<mj-column>` est en **pourcentages** (%) mais pour tout le reste il semble préférable de rester en **pixels** (px).  
Un exemple : *Outlook* va transformer un *padding* définit en pourcentages... en pouces, et d'une façon *étrange*.  
L'unité `em` peut également être utilisée sur tout les clients mail [(source)](https://www.caniemail.com/features/css-unit-em/).

---

#### Formats d'images :
- `.gif`(pas d'animation sur Outlook)
- `.png` (même avec alpha)
- `.jpeg`
- Pas de `.svg` :no_entry_sign:  
[source](https://www.caniemail.com/search/?s=image%20format)  

:warning: Donnez à chaque élément `<mj-image>` décoratif un attribut `alt` vide : `alt=""`

---

#### Responsive :

MJML met à disposition un composant `<mj-column>` au comportement *responsive*.  
Placés dans une `<mj-section>`, les `<mj-column>` se partagent la largeur de l'email sur desktop et vont automatiquement s'empiler verticalement sur mobile. La largeur des `<mj-column>` est définie en pourcentage sans que la somme ne dépasse les 100%. Par défaut l'espace est réparti uniformément sur chaque `<mj-column>`.  
Un composant `<mj-group>` englobant des `<mj-column>` va annuler ce comportement responsive pour les grader côte à côte sur mobile.  
![columns](./demo/assets/columns.gif)  
[columns.mjml](./demo/columns.mjml) : layout en 2 colonnes.

---

## Exemples

<details>
<summary>+/-</summary>

#### CSS Media query


Support : [plutôt bon](https://www.caniemail.com/features/css-at-media/).  
Il faut bien les placer dans une balise `<mj-style>` **SANS** l'attribut `<inline>`. Vous pouvez créer plusieurs balises `<mj-style>` dans `<mj-head>`.  
:warning: Utilisez la règle `!important` sur chaque propriété.

```html
<mj-head>
  ...
  <mj-style inline="inline">
    .mobile table {
      display: none !important;
      mso-hide: all;
    }
  </mj-style>

  <!-- mj-style SANS l'attribut inline -->
  <mj-style>
    @media (max-width: 481px) {
      .paragraph div {
        font-size: 16px !important;
        color: tomato !important;
      }
      .desktop table {
        display: none !important;
        mso-hide: all;
      }
      .mobile table {
        display: table !important;
      }
    }
  </mj-style>
</mj-head>
```

![media_query](./demo/assets/media_query.gif)  
[media_query.mjml](./demo/media_query.mjml) : afficher/masquer des images différentes sur mobile et desktop et appliquer différents styles à un même élément.

---

#### Police distante

Support : [très mauvais](https://www.caniemail.com/features/css-at-font-face/).  
Utilisez la balise `<mj-font>` dans `<mj-head>` et pointez l'attribut `href` vers un fichier contenant la déclaration `@font-face` de la police (ex : [Creepster](https://fonts.googleapis.com/css2?family=Creepster)).

```html
<mj-head>
  ...
  <mj-font name="Creepster" href="https://fonts.googleapis.com/css2?family=Creepster" />
  ...
</mj-head>

<mj-body>
  ...
  <mj-section>
    <mj-column>
      <mj-text font-family="Creepster, sans-serif">
        custom font
      </mj-text>
    </mj-column>
  </mj-section>
  ...
</mj-body>
```

![custom_font](./demo/assets/custom_font.gif)  
[custom_font.mjml](./demo/custom_font.mjml) : appliquer une police distante.

---

#### Image de fond (fenêtre client)

Support : bon... sauf sur Outlook (Windows) mais possibilité de faire mieux directement dans le html.
Utilisez l'attribut `full-width="full-width"` sur la balise `<mj-wrapper>` et pointez l'attribut `background-url` vers le fichier image.

```html
  <mj-body>
    <mj-wrapper background-url="./assets/pattern.png" background-size="400px">
      ...
    </mj-wrapper>
  </mj-body>
```

![background_image](./demo/assets/background_image.gif)  
[background_image.mjml](./demo/background_image.mjml) : appliquer une image de fond sur toute la fenêtre du client.

---

#### Dark mode

TODO...

---

</details>

# Testi@ 