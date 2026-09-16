# Publicar o dashboard IFN no GitHub Pages

O dashboard é um único arquivo estático (`index.html`) com o Plotly carregado por
CDN e todos os dados embutidos. Não precisa de servidor: qualquer hospedagem de
arquivos estáticos serve, inclusive o GitHub Pages (gratuito).

## O que é gerado

- `outputs/dashboard_IFN/index.html` — a página completa (~13 MB).
- Regenerar quando os CSVs de `tabelas_IFN/` mudarem:
  ```bash
  cd scripts/python
  python3 dashboard_IFN.py
  ```

## Testar localmente antes de publicar

```bash
cd outputs/dashboard_IFN
python3 -m http.server 8000
# abra http://localhost:8000 no navegador
```

## Opção A — Pages a partir da pasta `/docs` (mais simples)

1. Copie a página para `docs/` na raiz do repositório:
   ```bash
   mkdir -p docs
   cp outputs/dashboard_IFN/index.html docs/index.html
   ```
2. Faça commit e push do `docs/index.html`.
3. No GitHub: **Settings → Pages**.
4. Em **Build and deployment → Source**, escolha **Deploy from a branch**.
5. Em **Branch**, selecione a branch (ex.: `main`) e a pasta **`/docs`**. Salve.
6. Aguarde ~1 min. A URL aparece na mesma tela:
   `https://<seu-usuario>.github.io/<repositorio>/`

## Opção B — Branch `gh-pages` só com a página

```bash
git checkout --orphan gh-pages
git rm -rf .
cp outputs/dashboard_IFN/index.html index.html
git add index.html
git commit -m "dashboard IFN estático"
git push origin gh-pages
```
Depois, em **Settings → Pages**, selecione a branch `gh-pages` e a pasta `/root`.

## Notas

- **Tamanho:** o arquivo tem ~13 MB porque embute os pontos. Está bem abaixo do
  limite do GitHub (repositório até 1 GB, arquivo até 100 MB). O primeiro
  carregamento pode levar alguns segundos.
- **Deixar mais leve:** em `scripts/python/dashboard_IFN.py`, reduza
  `MAX_PTS_MAPA` (ex.: 3000) para diminuir os pontos do mapa por bioma.
- **CDN:** a página busca o Plotly em `cdn.plot.ly`. Se precisar funcionar 100%
  offline/sem CDN, baixe `plotly.min.js` para a mesma pasta e troque a tag
  `<script src="https://cdn.plot.ly/...">` por `<script src="plotly.min.js">`.
- **Privacidade:** o GitHub Pages é público. Se os dados não podem ser públicos,
  não use Pages — sirva internamente (`python3 -m http.server`) ou numa
  hospedagem com acesso restrito.
