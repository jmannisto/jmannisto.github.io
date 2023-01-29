---
layout: default
title: Inuktitut Morphological Analyzer
parent: Projects
---
Check out the full report [here](../docs/Neural_Morphological_Analyzer_for_Inuktitut.pdf)

## Background on Inuktitut, FST models, and Neural Morphological Analyzers
Inuktitut is a polysynthetic language of the Eskimo-Aleut language family. It is spoken in Eastern
Canada and is an official language of the territory of Nunavut, Canada. As of 2016, there were
approximately 40,000 speakers of Inuktitut.

Most words in Inuktitut are composed of multiple morphemes. Each word begins with a root, either
a verb, noun, or demonstrative root or an interjective. These roots may be followed by affixes and
then grammatical endings which can express a variety of grammatical or semantic meanings. These
morphemes also exhibit fusional properties, denoting multiple grammatical or semantic meanings
within one morpheme.

Frequently, Finite State Technology (FST) are used to create morphological analyzers and generators for languages. They have the benefit of mostly requiring the linguistic knowledge instead of a large amount of data required by neural models. They also can switch between being an analyzer and generator with the same system, while two separate systems must be trained when using neural architectures. However, FST systems rely on a lexicon to provide analyses for inflected forms and are unable to provide analyses when lemmas or morphemes are not accounted for in either the lexicon or rules. If an FST model comes across unknown morphemes, roots or a combination thereof it will
fail to return an analysis. FST models are also time consuming to build. They require significant knowledge of the language as well as of the tools for finite state modeling.

Neural Morphological Analyzers require data, which is difficult to acquire for many of the world’s languages. However, there are some resources available for Inuktitut, namely the Nunavut Hansard Corpus and the Uqailaut Analyzer.

## My Approach
I approached process of morphological analysis as machine translation task: given
an input or source (i.e. the surface form word) translate to the target (in our case, the morphological
analysis). Specifically I used an encoder-decoder architecture to encode the surface form of the word
and decode it into the deep form of the root with morphological tags.

### Monolingual Models
I trained and evaluated three monolingual models for this project. Each model was implemented
using OpenNMT-py version 2.3.0 with 1 layer, an RNN and word_vec size of 256 and a batch
size of 64. The models were trained for 10,000 steps. Each model used the same training set of size
50,000 and the same validation and test sets, each with a size of 5,000.

|                Model 1                |                  Model 2                  |                         Model 3                        |
|:-------------------------------------:|:-----------------------------------------:|:------------------------------------------------------:|
| Model Type: LSTM <br> Attention: None | Model Type: LSTM <br> Attention: General  | Model Type: bidirectional LSTM <br> Attention: General |

The bidirectional LSTM with general attention performed the best of these 3 models.
### Multilingual Model
Frequently adding multiple languages, or in this case morphological analyses of multiple languages,
into one system can improve performance. Multilingual models benefit from transfer learning and
reduce the risk of overfitting with the presence of more and different data. As data for other Inuit languages’ morphological analyses are unavailable, I decided to use an agglutinative with polysynthetic characteristics language: Navajo. The UniMorph project has many datasets of annotated morphological data in a universal schema that has been used for several SIG-MORPHON shared tasks over the past few years. The UniMorph Project had a data set for [Navajo](https://github.com/unimorph/nav) which included different noun and verb paradigms, however it did not include any adjective
paradigms.

I processed my Inuktitut data to match this [UniMorph](https://unimorph.github.io/doc/unimorph-schema.pdf) schema using Python and the command line to simplify the task for the model.

## Results
Models were analyzed using three metrics:
1. **Overall Accuracy:** This compares the entire predicted stem plus morpheme tags to the
expected output. Any errors in the stem or tags would mark it as incorrect
2. **Average Levenshtein Distance for Lemma:** This compares the similarities between the
predicted and actual lemmas. Smaller values indicate higher accuracy and higher values indicate poorer performance.
3. **Average Levenshtein Distance for Morpheme Tags:** Similar to above, this compares
the similarities between the the predicted morpheme tags and actual morpheme tags. Each
incorrect morpheme tag is considered to be a distance of 1 away from the morpheme tag
prediction. Lower values indicate better model performance

Below is a table which summarizes the results for each metrics listed above for each model trained.

|                               | Model 1 | Model 2 | Model 3 | Multilingual Model |
|-------------------------------|:-------:|:-------:|:-------:|--------------------|
| Average Overall Accuracy    | 0.61    | 0.744   | 0.7628  | 0.0                |
| Avg. Stem Levenshtein Dist.   | 4.5364  | 0.4134  | 0.3254  | 1.3066             |
| Avg. Morph Levenshtein Dist.  | 4.6734  | 0.6628  | 0.6176  | 1.189              |

The model using a bidirectional RNN architecture with general attention achieves the best results. This model makes on average 0.3254 errors on a predicted stem, and 0.6176 errors on the series of predicted morphs, but predicts the correct analysis 76% of the time.
