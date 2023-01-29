---
layout: default
title: Neural Machine Translation
parent: Projects
---

The final project of my Neural Machine Translation course challenged us to build at least 3 different NMT models and test them on the [Tatoeba data set](https://github.com/Helsinki-NLP/Tatoeba-Challenge/tree/master/data/release/test/v2021-08-07).

I first trained a simple Transformer model as my baseline and improved upon it using data cleaning methods, domain adaptation methods, and a multilingual approach. Config files used for training these models with specific model parameters can be found on [my github](https://github.com/jmannisto/Finnish-EnglishNMT).

Check out my report on the models and their results [here](../docs/NMT_Course_Final_Project.pdf)
## Results
A summary of the model results is below:

|     Model    | Tatoeba  |    | Europarl   |    |
|:------------:|:-------:|:----:|:---------:|------|
|              |   BLEU  | chrF |    BLEU   | chrF |
| Baseline     | 28.7    | 51.3 | 29        | 55.2 |
| Multilingual | 24.6    | 45.5 | 33.6      | 60.2 |
| Finetune     | 44.2    | 65.0 | 29.1      | 57.2 |

But what does this mean? To illustrate some of the differences between the models, I've summarized their different abilities to translate the same sentence below.

The sample sentences and their gold standard translation are:
1. Kolme kolmanteen on kaksikymmentäseitsemän. - 3 to the third power is 27.
2. Kaunis myyjätär odotti minua kaupassa. - A beautiful salesgirl waited on me in the shop.
3. Kukkulan takana on kaunis laakso. - A beautiful valley lies behind the hill.


|     Model    | Output (English |
|:------------:|:---------------:|
| Baseline     |Three thirds are twenty-seven. <br> I was ⁇ ited by a beautiful ⁇ er. <br> Behind the chocolate, there is a beautiful valley.|                 
| Multilingual |Three out of three is twenty-seven. <br> I was expected by a vendor in the shop. <br> There is a beautiful valley behind the bath.|                 
| Finetune     | Three thirds is twenty-seven. <br> The beautiful ⁇ er was waiting for me at the store. <br> There's a beautiful valley behind the hill.|         

### Baseline Transformer Model
A limitation of using this baseline model for translating the Tatoeba test set is a domain mismatch. The baseline
model is trained on the Europarl corpus, a corpus of the proceedings of the European Parliament. This corpus is
characterized by formal language with government jargon along with longer sentences (averaging 13.85 words per
sentence in the Finnish source) while the Tatoeba data set consists of generally shorter sentences (averaging 5.54
words per sentence in the Finnish source text) with a more informal register. Notably, the BLEU and chrF
score between the in-domain test set and the Tatoeba test set are quite similar, with the largest difference being
between the chrF scores. This indicates that the model isn’t significantly better at translating in-domain data than
this out of domain data.
### Multilingual Model
This multilingual model did not see improved performance on the Tatoeba task, however it did have a notable improvement in translating the Europarl test set. This may be unexpected given that we expect across the board
improvement multilingual models due transfer learning, however there are a few factors at play to consider.
While it seemed that domain issues may not be the primary concern when examining the baseline model results,
given the similarity for in and out of domain data, the distinct difference in performance for the multilingual model
indicates that domain adaptation is necessary for improvement. In the multilingual model’s case we provided additional Europarl data which is very distinct from the Tatoeba data. The model, with now twice as much exposure to
Europarl data as the baseline model may not be as adept at generalizing in out of domain situations.

We also assume that choosing related languages will automatically benefit a multilingual model, however what makes
languages related is ambiguous. [Kocmi Bojar, 2018](https://aclanthology.org/W18-6325/), also using Europarl corpus, as well as the Rapid corpus, found that when training a child Estonian model off of Finnish and vice versa the amount of gain on performance was less than that of Estonian with Czech or Russian, indicating considering factors outside of language family could be at play and relevant to transfer learning. It would be interesting to explore this further to see if it is possible to leverage even larger improvements with a two-source multilingual system in this set up.
### Finetuned Model
To improve model performance, I finetuned the baseline model with a corpus which was more similar to the data set it was expected to translate. The model was finetuned on data from the OpenSubtitles corpus. This corpus is much more colloquial, frequently with shorter and simpler sentences than the Europarl corpus. It appeared to be a better match in type and language register for the Tatoeba data, making it a good candidate to use for finetuning the model and adapting the domain. The finetuned model benefited significantly from the new, more in-domain data. After finetuning for an additional 60,000 steps, the BLEU and chrF scores increased 15.5 and 13.7 respectively, much closer to the baseline models on the Tatoeba data. It would be particularly interesting to have a baseline model trained on 1.5 million parallel sentences from the OpenSubtitles corpus to have a better understanding off the impact finetuning of the inclusion of the Europarl corpus had.
