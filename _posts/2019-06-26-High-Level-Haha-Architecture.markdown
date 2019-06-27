

Hiromi ([Medium](https://medium.com/@hiromi_suenaga) and [Twitter](https://twitter.com/hiromi_suenaga)) and I recently competed in the[ Haha 2019 Humor Detection challenge](https://www.fing.edu.uy/inco/grupos/pln/haha/) and received a second place finish on classification (predicting whether this tweet was intended to be humor by the author) and a third place finish on regression (predicting an average funniness score value for a tweet in a 5-star ranking). 

This post is written by both Hiromi and I and will contain a high-level explanation of our approach. Hopefully, this is something that people find value in and if there are any parts that aren't clear, please reach out to either of us on Twitter or the Fastai forums and we will clarify our approach!

#### Data Preprocessing

First, a tokenizer was created using [SentencePiece](https://github.com/google/sentencepiece) which would chop the tweets into small word-fragments called tokens. The SentencePiece model was trained on a set of 500k tweets downloaded from Twitter using [Tweepy](https://www.tweepy.org/). SentencePiece allows users to fine-tune its underlying model and adjust to better tokenize the corpus provided. The SentencePiece tokenizer was then injected into the [Fastai Data Block API](https://docs.fast.ai/data_block.html). The Data Block API orchestrates the preprocessing, organizing, and grouping the data so it is in the form (cleverly named “databunch”) appropriate to be fed into a neural network . By preprocessing, we mean [tokenizing](https://docs.fast.ai/text.data.html#TokenizeProcessor) and [numericalizing](https://docs.fast.ai/text.data.html#NumericalizeProcessor) each token because neural networks take numbers as inputs, not text/tokens. To numericalize tokens, we needed a vocabulary which maps tokens to numbers. Our vocab consists of approximately 30k tokens that appeared in the Haha 2019 corpus. After running our data through the Data Block API, we had a training set with 90% of our data, a validation set with the remaining 10%, and corresponding data loaders. 

#### Language Model Generation

In order to give the model a better starting point, we trained a language model. A language model predicts what the next word would be given preceding few words. The purpose is not to create something that can generate a sentence, but rather to gain a basic understanding of the language itself. This information is stored in the word embeddings and the model’s weights and biases which can then be transferred to the classifier and regressor to give them a much better base for the problems they are meant to solve.

#### Classifier Architecture

We ensembled five different models for our classification task: 

1. A forward [AWD-LSTM](https://docs.fast.ai/text.models.html#AWD_LSTM) using fastai’s [ULMFiT](https://arxiv.org/pdf/1801.06146.pdf)
2. A backward AWD-LSTM 
3. A pre-trained BERT model (bert-base-multilingual-cased) based on [this repo](https://github.com/huggingface/pytorch-pretrained-BERT).
4. Another BERT pretrained model (bert-base-multilingual-uncased) where everything is lowercase.
5. Naïve Bayes - Support Vector Machine (NBSVM). This was a technique that was borrowed from [Jeremy Howard’s Kaggle kernel](https://www.kaggle.com/jhoward/nb-svm-strong-linear-baseline) and his fantastic [lecture](https://www.youtube.com/watch?v=37sFIak42Sc&feature=youtu.be&t=3745).

#### Regressor Architecture

Our regressor was made up of the first four models from above. The only difference was that the output of the final linear layer is just one number instead of two - a number indicating the funniness score. We initially fed in only the tweets the classifier determined to be humorous into the regressor. The intuition here was not bombard the regressor with not funny tweets (which was 61.4% the training data) and really hone in on the score itself. After experimenting, our initial hypothesis turned out to be false. The regressor trained much better with not-funny tweets included.

#### Generating Final Results

The output of the classifier tells the percent likelihood of a tweet being humorous. The competition’s sample submission file indicated to submit the binary entry of whether it is funny (1) or not (0). The straightforward way to do this is to set the threshold to 50% and anything below it becomes 0; 1 otherwise. We instead examined the validation set predictions to determine the threshold that would maximize the F1-score which turned out to be lower than 0.5 (~0.45). For our final run, we used the entire dataset for training so we kept the predetermined threshold. Below is a graph of what we based our decision on:

![Threshold_Finder](\assets\images\2019-06-26-High-Level-Haha-Architecture\Threshold_Finder.png)

Another tweak we did was to take into account the output of the regressor for the classification prediction. If the regressor determined a funniness score of less than 1, we marked the tweet to be not funny (i.e. 0). Looking back, we feel that there was room for improvement here. Perhaps we could have taken into account the classifier output a little bit more by adding yet another threshold. Finally, we clipped the regressor predictions so that they fell between 0 and 5.  

#### Conclusion

This competition was a wonderful experience and a great learning opportunity. The fastai community has always supported us when we are stuck, pushed us to do more, and gave us a deeper understanding of the materials.

A few things that stuck out to us during this competition were: 

1. Ensembling of the output of many different models is important.
2. Change just one thing at a time and don’t make any assumptions.
3. Using pre-trained language model is becoming much easier thanks to [fastai](https://www.fast.ai/), ULMFit, and BERT.

#### Acknowledgments

We would like to thank [Jeremy Howard](https://twitter.com/jeremyphoward), [Rachel Thomas](https://twitter.com/math_rachel), and the entire Fast.ai team for providing a world-class education to many aspiring ML practitioner around the world.