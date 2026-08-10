# nido-legal

Documents légaux publics de l'application **Nido**, servis en pages HTML
statiques par GitHub Pages.

| Page | URL publique |
|---|---|
| Accueil | https://debugd0tlog.github.io/nido-legal/ |
| Politique de confidentialité | https://debugd0tlog.github.io/nido-legal/privacy.html |
| CGU | https://debugd0tlog.github.io/nido-legal/terms.html |

Ces URLs sont référencées dans l'app (`src/constants/legal.ts`) **et** doivent
être déclarées à l'identique dans les fiches Google Play et App Store.

---

## Source de vérité

Le HTML est **généré**, jamais édité à la main. La source est dans le dépôt
Nido :

- `docs/PRIVACY.md` → `privacy.html`
- `docs/TERMS.md` → `terms.html`

Pour republier après une modification :

```bash
cd ~/Wixar/Nido/legal-site
node build.mjs          # régénère index.html, privacy.html, terms.html
git add -A && git commit -m "maj politique de confidentialité"
git push
```

GitHub Pages redéploie tout seul en ~1 minute.

`build.mjs` n'a **aucune dépendance** (Node ≥ 18 suffit) : pas de `npm
install`, pas de CDN, pas de police distante, pas de JavaScript dans les
pages produites. Une page légale doit s'afficher partout, y compris derrière
un navigateur durci.

---

## Mise en ligne initiale

Les fichiers sont prêts et générés. Il reste à en faire un dépôt git, à
créer le dépôt distant, et à activer Pages.

### 0. Dépôt local (dans tous les cas)

```bash
cd ~/Wixar/Nido/legal-site
git init -b main
git add -A
git commit -m "Site légal Nido : politique de confidentialité + CGU"
```

### A. Avec `gh` (GitHub CLI)

`gh` n'est pas installé sur cette machine :

```bash
brew install gh
gh auth login          # choisir le compte DebugD0tLog
```

Puis, depuis `~/Wixar/Nido/legal-site` :

```bash
gh repo create nido-legal --public --source=. --remote=origin --push
gh api -X POST repos/DebugD0tLog/nido-legal/pages \
  -f 'source[branch]=main' -f 'source[path]=/'
```

### B. Sans `gh` (SSH, déjà configuré)

1. Créer le dépôt **public** `nido-legal` sur https://github.com/new
   (aucun README / .gitignore / licence — le dépôt local en a déjà un).
2. Depuis `~/Wixar/Nido/legal-site` :

```bash
git remote add origin git@github-debug:DebugD0tLog/nido-legal.git
git push -u origin main
```

> Le remote utilise l'alias SSH **`github-debug`** (défini dans
> `~/.ssh/config`), qui pointe la clé `id_ed25519_debug` du compte
> **DebugD0tLog**. `git@github.com:` utiliserait l'autre clé
> (BaptisteDev09) et échouerait.

3. Activer Pages : **Settings → Pages → Source: Deploy from a branch →
   Branch `main` / `/ (root)` → Save**.

### Vérification

```bash
curl -sI https://debugd0tlog.github.io/nido-legal/privacy.html | head -1
curl -sI https://debugd0tlog.github.io/nido-legal/terms.html   | head -1
# attendu : HTTP/2 200 (compter ~1 à 3 min après l'activation de Pages)
```

---

## Avant de soumettre aux stores

- [ ] Remplacer `[ÉDITEUR — À COMPLÉTER]`, l'adresse postale et le nom du
      représentant légal dans `docs/PRIVACY.md` §1 et `docs/TERMS.md` §1.
      **Le RGPD exige un responsable de traitement identifiable** ; en l'état
      les deux pages affichent le placeholder en clair.
- [ ] Faire pointer `support@nido-app.com` sur une boîte réellement relevée,
      ou le remplacer partout (3 fichiers : les deux `.md` et
      `src/constants/legal.ts`).
- [ ] Déclarer la permission **microphone** dans Google Play Data Safety et
      dans le questionnaire App Store Privacy (traitement local, aucun
      enregistrement, aucune transmission — cf. §2.5 de la politique).
- [ ] Renseigner la même URL de politique de confidentialité dans les deux
      fiches store.

Ce dossier est ignoré par le dépôt Nido (cf. son `.gitignore`) : c'est un
dépôt git à part entière, versionné séparément.
