## Instructions
### This is my Own Implementation of the [Universal Language Model Finetuning For Text Classification](https://arxiv.org/pdf/1801.06146.pdf) Paper. 
Note that the first part of the README is based on the README provided at Fastai repo.

### 0. Preparing Wikipedia

If you want to train your own language model on a Wikipedia in your chosen language,
run `prepare_wiki.sh`. The script will ask for a language and will then
download, extract, and prepare the latest version of Wikipedia for the chosen language.
Note that for English (due to the size of the English Wikipedia), the extraction process
takes quite long.

Example command: `bash prepare_wiki.sh`

This will create a `data` folder in this directory and `wiki_dumps`, `wiki_extr`, and
`wiki` subfolders. In each subfolder, it will furthermore create a folder `LANG`
where `LANG` is the language of the Wikipedia. In our case, we use `ar` as the language. The prepared files are stored in
`wiki/ar` as `train.csv` and `val.csv` to match the format used for text
classification datasets. By default, `train.csv` contains around 100 million tokens
and `val.csv` is 10% the size of `train.csv`.

### 1. Tokenization

Run `create_toks.py` to tokenize the input texts.

Example command: `python create_toks.py data/imdb`

Usage:

```
create_toks.py DIR_PATH [CHUNKSIZE] [N_LBLS] [LANG]
create_toks.py --dir-path DIR_PATH [--chunksize CHUNKSIZE] [--n-lbls N_LBLS] [--lang LANG]
```

- `DIR_PATH`: the directory where your data is located
- `CHUNKSIZE`: the size of the chunks when reading the files with pandas; use smaller sizes with less RAM
- `LANG`: the language of your corpus.

The script expects `train.csv` and `val.csv` files to be in `DIR_PATH`. Each file should be in
CSV format. If the data is labeled, the first column should consist of the label as an integer.
The remaining columns should consist of text or features, which will be concatenated to form
each example. If the data is unlabeled, the file should just consist of a single text column.
The script will then save the training and test tokens and labels as arrays to binary files in NumPy format
in a `tmp` in the above path in the following files:
`tok_trn.npy`, `tok_val.npy`, `lbl_trn.npy`, and `lbl_val.npy`.
In addition, a joined corpus containing white space-separated tokens is produced in `tmp/joined.txt`.

### 2. Mapping tokens to ids

Run `tok2id.py` to map the tokens in the `tok_trn.npy` and `tok_val.npy` files to ids.

Example command: `python tok2id.py data/imdb`

Usage:
```
tok2id.py PREFIX [MAX_VOCAB] [MIN_FREQ]
tok2id.py --prefix PREFIX [--max-vocab MAX_VOCAB] [--min-freq MIN_FREQ]
```
- `PREFIX`: the file path prefix in `data/nlp_clas/{prefix}`
- `MAX_VOCAB`: the maximum vocabulary size
- `MIN_FREQ`: the minimum frequency of words that should be kept

### (3a. Pretrain the Wikipedia language model)

Run 
