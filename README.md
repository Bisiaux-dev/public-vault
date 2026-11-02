# public-vault

Vault Obsidian public, publié sur https://bisiaux-dev.github.io/public-vault/ via [Quartz v4](https://quartz.jzhao.xyz) + GitHub Pages.

## Utilisation

1. Ouvrir le dossier `content/` comme vault dans Obsidian.
2. Écrire / modifier des notes (wikilinks `[[...]]`, images, etc. supportés).
3. `git add -A && git commit -m "..." && git push` → le site se redéploie tout seul (~1-2 min).

Tout ce qui est dans `content/` est **public**. Les dossiers `private/` et `templates/` sont ignorés à la publication. Pour exclure une note : frontmatter `draft: true`.
