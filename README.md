[comment]: <> "LTeX: language=fr"
<!-- markdownlint-disable MD003 MD025 MD033 -->

Données structurées
===================

[![Licence : CC BY 4.0](https://licensebuttons.net/l/by/4.0/80x15.png)](https://creativecommons.org/licenses/by/4.0/)

Contenus pour le cours « Documents structurés et écriture numérique » du master [Plurital](http://plurital.org).


- [Site du cours](https://loicgrobol.github.io/structured-data/)
- [Dépôt GitHub](https://github.com/LoicGrobol/structured-data)

Contact : [<loic.grobol@parisnanterre.fr>](mailto:loic.grobol@parisnanterre.fr)

## Développement

Pour travailler au développement de ce cours :

1. Créer un environnement virtuel et l'activer
2. Installer les dépendances

   ```console
   pip install -U -r requirements.txt
   ```

3. Démarrer jupyter

   ```console
   jupyter notebook
   ```

   Idéalement ça devrait aussi marcher avec jupyterlab [mais ce n'est pas encore le cas pour les slides](https://github.com/damianavila/RISE/pull/381)
4. On peut alors modifier les fichiers markdown dans jupyter comme si c'étaient des notebooks grâce
   à la magie de [jupytext](https://github.com/mwouts/jupytext)

Autres éléments magiques :

- On peut ouvrir les notebooks en md sur Binder grâce au [postBuild](postBuild) qui dit de compiler
  l'extension jupytext. Par contre, le build initial de l'image est assez lent. (même avec
  `--minimize=False` qui [accélère un
  peu](https://github.com/jupyterlab/jupyterlab/issues/4824#issuecomment-697188390))

## Générer le site

```console
gem install jekyll bundler
bundle exec jekyll build
bundle exec jekyll serve
```

## Licences

[![CC BY Licence badge](https://i.creativecommons.org/l/by/4.0/88x31.png)](http://creativecommons.org/licenses/by/4.0/)


Copyright © 2021 Loïc Grobol [\<loic.grobol@gmail.com\>](mailto:loic.grobol@gmail.com)

Sauf indication contraire, les fichiers présents dans ce dépôt sont distribués selon les termes de
la licence [Creative Commons Attribution 4.0
International](https://creativecommons.org/licenses/by/4.0/).

Un résumé simplifié de cette licence est disponible à <https://creativecommons.org/licenses/by/4.0/>.

Le texte intégral de cette licence est disponible à
<https://creativecommons.org/licenses/by/4.0/legalcode>
