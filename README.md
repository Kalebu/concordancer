[![Support Python
Version](https://img.shields.io/badge/python-%E2%89%A5%203.7-blue.svg)]

# Concordancer

This module loads and indexes a corpus in RAM and provides concordance search to retrieve data from the corpus using (a subset of) Corpus Query Language (CQL).


## Installation

```bash
pip install concordaner
```


## Usage

### Loading a corpus from file

```python
import json
from concordancer.concordancer import Concordancer
from concordancer.kwic_print import KWIC

# Read corpus from file
corpus = []
with open("example-corpus-data/tokenDict.jsonl") as f:
    for l in f:
        corpus.append(json.loads(l))

# Index and initiate the corpus as a concordancer object
C = Concordancer(corpus)
C.set_cql_parameters(default_attr="word", max_quant=3)
```

### CQL Concordance search

```python
cql = '''
[pos="V.*"]+
'''
concord_list = C.cql_search(cql, left=2, right=2)
```

The result of the concordance search is a list of dictionaries, which can easily be converted to JSON or other data structures for further uses:

```python
>>> concord_list[:2]
[
    {
        'left': [{'word': '買', 'pos': 'VC'}, {'word': '了', 'pos': 'Di'}],
        'keyword': [{'word': '覺得', 'pos': 'VK'}, {'word': '材質', 'pos': 'Na'}],
        'right': [{'word': '很', 'pos': 'Dfa'}, {'word': '對', 'pos': 'VH'}],
        'position': {'doc_idx': 78, 'sent_idx': 13, 'tk_idx': 9},
        'captureGroups': {'verb': [{'word': '覺得', 'pos': 'VK'}],
                          'noun': [{'word': '材質', 'pos': 'Na'}]}
    },
    {
        'left': [{'word': '“', 'pos': 'PARENTHESISCATEGORY'},
                 {'word': '不', 'pos': 'D'}],
        'keyword': [{'word': '戴', 'pos': 'VC'}, {'word': '錶', 'pos': 'Na'}],
        'right': [{'word': '世代', 'pos': 'Na'}, {'word': '”', 'pos': 'VC'}],
        'position': {'doc_idx': 52, 'sent_idx': 7, 'tk_idx': 36},
        'captureGroups': {'verb': [{'word': '戴', 'pos': 'VC'}],
                          'noun': [{'word': '錶', 'pos': 'Na'}]}
    }
]
```

To better read the concordance lines, you can pass `concord_list` into `concordancer.kwic_print.KWIC()` to read them as a keyword-in-context format in the console:

```python
>>> KWIC(concord_list[:5])
left                        keyword          right             LABEL: verb    LABEL: noun
--------------------------  ---------------  ----------------  -------------  -------------
買/VC 了/Di                 覺得/VK 材質/Na  很/Dfa 對/VH      覺得/VK        材質/Na
“/PARENTHESISCATEGORY 不/D  戴/VC 錶/Na      世代/Na ”/VC      戴/VC          錶/Na
聯名鞋/Na 趁著/P            過年/VA 期間/Na  穿出去/VB 四處/D  過年/VA        期間/Na
走/VA  /WHITESPACE          燒/VC 錢/Na      啊/T ～/FW        燒/VC          錢/Na
正/VH 韓/Nc                 賣/VD 家/Nc      裡面/Ncd 很/Dfa   賣/VD          家/Nc
```


## Supported CQL features

CQL search is supported through [`cqls`](https://github.com/liao961120/cqls). A subset of useful CQL is supported:

- token: `[]`, `"我"`, `[word="我"]`, `[word!="我" & pos="N.*"]`
- token-level quantifier: `+`, `*`, `?`, `{n,m}`
- grouping: `("a" "b"? "c"){1,2}`
- label: `lab1:[word="我" & pos="N.*"] lab2:("a" "b")`