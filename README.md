** See https://github.com/bsolomon1124/pycld3 for a more update Python binding version. (And easier pip install) **


# Compact Language Detector v3 (CLD3) Python Edition

* [Model](#model)
* [Installation](#installation)
* [Contact](#contact)
* [Credits](#credits)

Included are Python bindings (via Cython) for [cld3](https://github.com/Google/cld3),
a language classifying library used in Google Chrome. Building the extension does not
require the Chromium repository, instead only these libraries need to
be installed:

- Cython
- msgpack
- Protobuf (with headers and protoc)

To install the extension, from this repo,
run `pip install https://github.com/ThothMedia/cld3/archive/master.zip`

### Model

CLD3 is a neural network model for language identification. This package
 contains the inference code and a trained model. The inference code
 extracts character ngrams from the input text and computes the fraction
 of times each of them appears. For example, as shown in the figure below,
 if the input text is "banana", then one of the extracted trigrams is "ana"
 and the corresponding fraction is 2/4. The ngrams are hashed down to an id
 within a small range, and each id is represented by a dense embedding vector
 estimated during training.

The model averages the embeddings corresponding to each ngram type according
 to the fractions, and the averaged embeddings are concatenated to produce
 the embedding layer. The remaining components of the network are a hidden
 (Rectified linear) layer and a softmax layer.

To get a language prediction for the input text, we simply perform a forward
 pass through the network.

![Figure](model.png "CLD3")

### Installation
Building the Python wheel requires the protobuf compiler and its headers to be installed.
If you run into issues with protobufs not compiling, just go into the `src` directory and run

```
mkdir -p cld_3/protos
protoc --cpp_out=cld_3/protos *.proto
```

To generate a python wheel (from the root of this repo):

```
python setup.py bdist_wheel
```

Builds have been tested with GCC9.0 on Ubuntu 18.04 and Apple Clang 11.0.0 on OSX 10.15 (Catalina Beta)

### Python Usage
Here's some examples:

```python
cld3.get_language("This is a test")
# LanguagePrediction(language='en', probability=0.9999980926513672, is_reliable=True, proportion=1.0)

cld3.get_frequent_languages("This piece of text is in English. Този текст е на Български.", 5)
# [LanguagePrediction(language='bg', probability=0.9173890948295593, is_reliable=True, proportion=0.5853658318519592), LanguagePrediction(language='en', probability=0.9999790191650391, is_reliable=True, proportion=0.4146341383457184)]
```

In short:
- `get_language` returns the most likely language as the named tuple `LanguagePrediction`. Proportion is always 1.0 when called in this way.
- `get_frequent_languages` will return the top number of guesses, up to a maximum specified (in the example, 5). The maximum is mandatory. Proportion will be set to the proportion of bytes found to be the target language in the list.

In the normal cld3 library, "und" may be returned as a language for unknown languages (with no other stats given). This library filters that result out as extraneous; if the language couldn't be detected, nothing will be returned. This also means, as a consequence, `get_frequent_languages` may return fewer results than what you asked for, or none at all.

### Credits

Original authors of the code in this package include (in alphabetical order):

* Alex Salcianu
* Andy Golding
* Anton Bakalov
* Chris Alberti
* Daniel Andor
* David Weiss
* Emily Pitler
* Greg Coppola
* Jason Riesa
* Kuzman Ganchev
* Michael Ringgaard
* Nan Hua
* Ryan McDonald
* Slav Petrov
* Stefan Istrate
* Terry Koo

and Elizabeth Myers for the original Python bindings

