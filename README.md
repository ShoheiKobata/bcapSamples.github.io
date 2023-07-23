# bcapSamples.github.io


bcap communication samples [page](https://shoheikobata.github.io/bcapSamples.github.io/)



# developd

java install https://www.java.com/en/download/ 

Graphviz install https://www.graphviz.org/download/

planUML install https://plantuml.com/ja/download

plantuml.jarを任意のディレクトリに置き
環境変数 PLANUMLにPATHを通す

extension install 
```bash
pip install sphinxcontrib-plantuml
```

change conf.py

```conf.py
extensions += ['sphinxcontrib.plantuml']
plantuml='java -jar $PLANTUML_JAR_PATH'
plantuml_output_format = 'svg'
plantuml_syntax_error_image = True
```

動作確認
```bash
java -jar %PLANTUML%\plantuml.jar aa.pu
```