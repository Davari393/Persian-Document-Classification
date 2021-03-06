![](https://github.com/Davari393/Persian-Document-Classification/blob/master/0_X7PVc7QwrpFnyo4p.png
)
# Persian Document Classification
The classification subject of Persian articles is sparse, limited, and non-automatic.This repo proposes methods based on **deep learning** for extracting features of Persian texts, classifying texts, and finally identifying the subject matter of the collection.

## Table of contents
* [Dataset](#Dataset)
* [The Main Strategy](#The-Main-Strategy)
* Methods
  * [Fasttext](#Fasttext)
  * [Gensim](#Gensim)
  * [PyText](#PyText)

## Dataset
The [Hamshahri](http://dataheart.ir/article/3487/%D9%85%D8%AC%D9%85%D9%88%D8%B9%D9%87-%D8%AF%D8%A7%D8%AF%D9%87--%DA%A9%D8%A7%D9%85%D9%84-%D9%87%D9%85%D8%B4%D9%87%D8%B1%DB%8C-%D9%86%D8%B3%D8%AE%D9%87-1-%D8%B4%D8%A7%D9%85%D9%84-166-%D9%87%D8%B2%D8%A7%D8%B1-%D8%B3%D9%86%D8%AF-%D8%AF%D8%B1-%D9%81%D8%B1%D9%85%D8%AA-%D8%A7%DA%A9%D8%B3%D9%84-%D9%88-csv) dataset (a sub-body containing 166,000 documents with a specific theme), one of the most prestigious Persian language resources in the field of natural language, has been applied for feature extraction and text classification. 

## The Main Strategy

First of all you should become familiar with this word: **Word Embedding**.

Word embeddings are a type of word representation that allows words with similar meaning to have a similar representation.(For more details please click [here](https://machinelearningmastery.com/what-are-word-embeddings/))

The main strategy has been chosen in this project is Bag-of-Embeddings(BoE). Briefly, it means the numerical average of several word embeddings.

In the paper, a word embedding for each word of every document is extracted using pre-trained FastText model on Persian Wikipedia(you can use both a pre-trained word vector model or train the model from scratch), then the numerical average of these word embeddings in each document is taken. The output of this operation is assigned to a MLP model and done.!


## Fasttext

[FastText](https://github.com/facebookresearch/fastText/) is a library for efficient learning of word representations and sentence classification.

The pre-trained word vector for ***Persian*** language, trained on Common Crawl and Wikipedia has been used for this project. This pre-trained model has been trained using CBOW with position-weights, in dimension 300, with character n-grams of length 5, a window of size 5 and 10 negatives.

For more details and download pre-trained (bin/text) files for 157 languages including **Persian**, go through this link: https://fasttext.cc/docs/en/crawl-vectors.html

You need just download a pre-trained word embedding **text** file to start building your own classifier.
By placing the downloaded embedding text file in the fasttext folder and running [_fasttext_classifier.py_](https://github.com/Davari393/Persian-Document-Classification/tree/master/fasttext) you are given a MLP text classification model (with the report of accuracy and error on the train and test data) which has been used BoE method with fasttext pre-trained word embeddings.

## Gensim

This is an open-source python library for natural language processing. The [Gensim](https://github.com/RaRe-Technologies/gensim) library enables us to extend embedding by training our Word2vec model (Another word representation model like FastTesxt), using CBOW algorithms or skip-gram algorithm.


There is no pre-trained Word2vec model on Persian data so you should train that from scratch on our dataset.
To train this model, data should be convert to .txt file and some cleaning steps be done on it [here](https://github.com/Davari393/Persian-Document-Classification/tree/master/clean_data). 
These cleaning steps are:
  1) Normalization
  2) Elimination of all characters except the Persian characters
  3) Deletion stop words

Then the final text file is given to the model and is trained. You can download pre-trained and cleaned embeddings of the 166,000 documents hamshahri from [here](https://drive.google.com/open?id=1vmdgHgNje5r18VpZ2xf2cbdu5l_bfOXd) or train it and classify with [_train_gensim&classifier.py_](https://github.com/Davari393/Persian-Document-Classification/tree/master/gensim) code.

### Note: 
If you want to train this model for another language, the cleaning steps should be change.(consepts are equal but for e.g. the Persian stop words are not equal Spanish stop words!)

## PyText
PyText is a deep-learning based NLP modeling framework built on PyTorch.
For applying this framwork the first step is cloning the pytext repository in your own directory: https://github.com/facebookresearch/pytext

As you can see in __"Train your first text classifier"__ section of this repo, there is a _docnn.json_ file that is required for training.

This json file is like this:
```
{
  "version": 8,
  "task": {
    "DocumentClassificationTask": {
      "data": {
        "source": {
          "TSVDataSource": {
            "field_names": ["label", "slots", "text"],
            "train_filename": "tests/data/train_data_tiny.tsv",
            "test_filename": "tests/data/test_data_tiny.tsv",
            "eval_filename": "tests/data/test_data_tiny.tsv"
          }
        }
      },
      "model": {
        "DocModel": {
          "representation": {
            "DocNNRepresentation": {}
          }
        }
      }
    }
  },
  "export_torchscript_path": "/tmp/new_docnn.pt1",
  "export_caffe2_path": "/tmp/model.caffe2.predictor"
}
```
But the config of this project is it:
```
{
  "task": {
    
    "DocClassificationTask": {
      "trainer": {
        "epochs": 10,
        "random_seed": 0,
        "early_stop_after": 0,
        "max_clip_norm": null,
        "report_train_metrics": true
      },
      "data_handler": {
        "columns_to_read": ["text", "doc_label"],
        "eval_batch_size": 1,
        "max_seq_len": -1,
        "shuffle": true,
        "sort_within_batch": true,
        "test_batch_size": 1,
        "train_batch_size": 1,
        "train_path": "/content/train.tsv",
        "eval_path": "/content/eval.tsv",
        "test_path": "/content/test.tsv"
      }
    }
  }
}
```
All you have to do is preparing your dataset(train, test, eval) in __.tsv__ format.
For this purpose, some commands are defined below:
```
./app.sh file.csv file.tsv.txt
mv file.tsv.txt file.tsv
```

Also, the app.sh file has placed in this repo.

TSV format: it means the text and its label have to be seprated with _tab_ and except for this tab, there should be no tab in your texts.
If you want, you can change the config file as well. Then train the data, such as how described that in the pytext repo and enjoy it.



## Citation
Please cite our paper in your publications if it helps your research:
```
@inproceedings{li2019exposing,
  title={Persian Document Classification Using Deep Learning Methods},
  author= Nafiseh Davari, Mahya Mahdian Tabrizi, Alireza Akhavanpour, and Negin Daneshpour},
  booktitle={28th Iranian Conference on Electrical Engineering (ICEE2020)},
  year={2019}
}
```
