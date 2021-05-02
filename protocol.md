# Analýza sentimentu

Cílem tohoto úkolu bylo provedení analýzy sentimentu na přiřazeném datasetu. Ke splnění tohoto úkolu jsem využil pythonový wrapper kolem Weky, především kvůli možnosti napsat si jednoduchý skript obdobný makru, který provede operace konzistentně a zároveň slouží jako dokumentace k provedeným krokům. Skript lze nejlépe prohlížet v renderovaném přiloženém souboru weka2.html či případně zde: https://github.com/kdouda/weka-sentiment/blob/master/weka2.ipynb . Postup s použitím wrapperu byl nejprve úspěšně ověřen dle postupu specifikovaném v zadání, následné vybrané postupy byly ověřeny v programu Weka a jsou zcela konzistentní v počtu parametrů i ve výsledcích s GUI verzí Weky.

## Dosažené výsledky

Nejlepší dosažený výsledek byl s klasifikačním algoritmem NaiveBayes využívající Kernel Density Estimation místo normálního pravděpodobnostního rozdělení. Pro nalezení stop slov byl využit slovník poskytnutý od autorů knihovny NTLK (weka.core.stopwords.WordsFromFile, samotný slovník stop slov je přiložen k tomuto dokumentu), využitý stemmer byl weka.core.stemmers.IteratedLovinsStemmer a byl využit výchozí tokenizer. Text byl taktéž upraven na malá písmena. Správně zaklasifikovaných bylo **94.87** % instancí. Velmi úspěšné byly i n-gramové tokenizery, ale kvůli pořadí operací jejich součástí byly i slova ze stop slovníku, tedy stop slovník se pravděpodobně nepoužil vůbec (jelikož neobsahoval vytvořené kombinace z n-gramů), a proto tyto tento způsob nebyl brán dál v potaz. Toto by se potenciálně dalo vyřešit regexovým rozšířením stávajícího stop slovníku, které taktéž Weka poskytuje.

Postup pro replikaci:
* pro nastavení StringToWordVector:
    * `weka.filters.unsupervised.attribute.StringToWordVector -R first-last -W 1000 -prune-rate -1.0 -T -I -N 0 -L -stemmer weka.core.stemmers.IteratedLovinsStemmer -stopwords-handler "weka.core.stopwords.WordsFromFile -stopwords __PATH__\stopwords.txt" -M 1 -tokenizer "weka.core.tokenizers.WordTokenizer -delimiters \" \\r\\n\\t.,;:\\\'\\\"()?!\""`
    * zde je nutno změnit vybrat cestu ke slovníku stop slov poskytnutému spolu s tímto dokumentem
* po aplikování tohoto filtru by mělo vytvořeno 1021 atributů. 
    * `weka.filters.supervised.attribute.AttributeSelection -E "weka.attributeSelection.CfsSubsetEval -M -Z -P 1 -E 1" -S "weka.attributeSelection.BestFirst -D 2 -N 5"`
    * po odfiltrování by mělo zůstat 51 atributů
* v tabu Classify vybrat algoritmus NaiveBayes
    * `weka.classifiers.bayes.NaiveBayes -K`

Screenshoty postupu jsou uvedeny na konci práce.

V průběhu práce v Pythonu bylo zjištěno, že i při použití stejných příkazů na předzpracování dat se významně liší počty atributů oproti programu Weka a výpisu z programu, což se u prvního příkladu nedělo. Může se jednat o nekonzistenci mezi verzemi programu Weka v knihovně a verzi co má autor staženou z dřívejších kurzů, ale to autorovi přijde nepravděpodobné. Případná možnost je i špatné zadání některých parametrů algoritmu, i když je konzistentní s dokumentací knihovny (některé složitější kombinace již ze strany knihovny neprocházely vůbec). Bohužel tato nekonzistence byla zjištěna až při ověření výsledků pro screenshoty z Weky úplně na konci práce a po delší době se autorovi nepodařilo přijít na vhodné řešení těchto nekonzistencí. I přesto je zajímavé, že se dosažené finální výsledky v podstatě neliší, např. u nejlepší varianty NaiveBayes jsou výsledky identické.

## Otázky

### TF-IDF

Pomocí Pythonových skriptů se autor snažil demonstrovat, že na výsledek použití TF-IDF nemá žádný vliv (což je konzistentní i s chování v GUI verzi Weky). Pro analýzu sentimentu Tweetů (velmi krátkého textu) se dá předpokládat, že autor nebude opakovat slova pro jejich "umocnění", jelikož platforma autorům Tweetů na to nedává prostor. Není tedy nutné charakterizovat jednotlivé Tweety podle toho, kolikrát se v něm dané slovo vyskytuje, resp. nepřináší to žádnou citelnou výhodu. Časté termíny jsou odfiltrovány již stopword handlerem, IDF zde nebude mít velkou roli. Např. u Bayesovského klasifikátoru nás taktéž zajímá spíše souvýskyt, než absolutní intenzita výskytu pro klasifikaci.

### Redukce dimenzionality

Pokud bychom vybrali pouze nevhodnou podmnožinu atributů (např. predikující pouze jeden atribut), tak můžeme dosáhnout velmi nekvalitního modelu, který mlže fungovat obdobně jako výchozí algoritmus ZeroR. Pomocí vhodného systému výběru parametrů (dobrá korelace s cílovým atributem, ale nízká mezi sebou) můžeme dosáhnout nízkého počtu parametrů, které mohou zabránit přeučení u mnoha typů algoritmů a tím zvýšit jejich přenositelnost a úspěšnost na neznámých datech. Jistě tedy výběr vhodných atributů při redukci dimenzionality vliv má.

## Screenshoty pro replikaci postupu
![](img1.png)
![](img2.png)
![](img3.png)
![](img4.png)
![](img5-1.png)
![](img5.png)
