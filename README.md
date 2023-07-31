# bcapSamples.github.io

This repository is for the creation of b-cap communication description pages.

Please see [this page](https://shoheikobata.github.io/bcapSamples.github.io/).


# developd

java install https://www.java.com/en/download/ 
Graphviz install https://www.graphviz.org/download/
planUML install https://plantuml.com/ja/download

plantuml.jar save it in any directory
Pass to "PLANUML" environment variable

extension install 

```sh
pip install sphinxcontrib-plantuml
```

change conf.py

```conf.py
extensions += ['sphinxcontrib.plantuml']
plantuml='java -jar $PLANTUML_JAR_PATH'
plantuml_output_format = 'svg'
plantuml_syntax_error_image = True
```

check
```bash
java -jar %PLANTUML%\plantuml.jar aa.pu
```

Build
```sh
cd docs && pipenv run sphinx-autobuild source build/html
```