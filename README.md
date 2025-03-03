## [datamodel-code-generator](https://github.com/koxudaxi/datamodel-code-generator/) reproducible example for [issue #2332](https://github.com/koxudaxi/datamodel-code-generator/issues/2332)

#### [Test 1](test_1):white_check_mark:

|Inputs|Outputs|
|-|-|
|[test_1/products.json](test_1/products.json)|[outputs/products.py](outputs/products.py)|
|[test_1/commons.json](test_1/commons.json)|[outputs/commons.py](outputs/commons.py)|

#### [Test 2](test_2) :x:
|Inputs|Outputs|
|-|-|
|[test_2/products.schema.json](test_2/products.schema.json)|[outputs/products_schema.py](outputs/products_schema.py)|
|[test_2/commons.schema.json](test_2/commons.schema.json)|[outputs/commons_schema.py](outputs/commons_schema.py)|

#### [Test 3](test_3) :x:
|Inputs|Outputs|
|-|-|
|[test_3/products.with.more.dots.schema.json](test_3/products.with.more.dots.schema.json)|-|
|[test_3/commons.with.more.dots.schema.json](test_3/commons.with.more.dots.schema.json)|-|

### Description of the problem

- We define an array under `#/$defs/$defaultArray` in the common schema.
- We `$ref` the array in the product schema.


### Goal

We want to generate **a model of any supported type** (pydantic v1, pydantic v2, etc..) for `products`.


### Issue

#### [Test 1](test_1) :white_check_mark:

It works

```python
from . import commons
```

#### [Test 2](test_2)  :x:

Wrong import path.

```python
from ..commons import schema
```

#### [Test 3](test_3) :x:

Can't generate.

```pytb
Traceback (most recent call last):
  File "<path>/.venv/lib/python3.13/site-packages/datamodel_code_generator/__main__.py", line 452, in main
    generate(
    ~~~~~~~~^
        input_=config.url or config.input or sys.stdin.read(),
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    ...<67 lines>...
        no_alias=config.no_alias,
        ^^^^^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "<path>/.venv/lib/python3.13/site-packages/datamodel_code_generator/__init__.py", line 481, in generate
    results = parser.parse()
  File "<path>/.venv/lib/python3.13/site-packages/datamodel_code_generator/parser/base.py", line 1309, in parse
    body = code_formatter.format_code(body)
  File "<path>/.venv/lib/python3.13/site-packages/datamodel_code_generator/format.py", line 188, in format_code
    code = self.apply_black(code)
  File "<path>/.venv/lib/python3.13/site-packages/datamodel_code_generator/format.py", line 196, in apply_black
    return black.format_str(
           ~~~~~~~~~~~~~~~~^
        code,
        ^^^^^
        mode=self.black_mode,
        ^^^^^^^^^^^^^^^^^^^^^
    )
    ^
  File "src/black/__init__.py", line 1208, in format_str
  File "src/black/__init__.py", line 1222, in _format_str_once
  File "src/black/parsing.py", line 98, in lib2to3_parse
black.parsing.InvalidInput: Cannot parse for target version Python 3.9: 5:18: from .....commons.with.more.dots import schema
```

### Workaround for test 2

Generate the models using `--treat-dot-as-module` flag.


### Generate models

Run the generator with `--input <folder>`

```sh
$ pip install -r requirements.txt
$ datamodel-codegen --input [test_1 | test_2 | test_3]
```