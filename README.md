# Text-reuse-detection

It'as about to detect the passages from Agustine homilies whose Caesarius has reused for the writing of his own homilies.

```ruby
#détection de remplois
library(textreuse)
corpus1=TextReuseCorpus(dir = "~/txt_lemmat/", tokenizer = NULL)
corpus1=tokenize(corpus1, tokenize_ngrams)
head(tokens(corpus1[[1]]))
compa = pairwise_compare(corpus1, jaccard_similarity)
df=pairwise_candidates(compa, directional = TRUE)

AU_s_9=TextReuseTextDocument(file = "~/txt_unlemmat/AU_s_9.txt", tokenizer = tokenize_ngrams, n=3)
CAE_s_100A=TextReuseTextDocument(file = "~/txt_unlemmat/CAE_s_100A.txt", tokenizer = tokenize_ngrams, n=3)
CAE_s_109=TextReuseTextDocument(file = "~/txt_unlemmat/C_CAE_s_109.txt", tokenizer = tokenize_ngrams, n=3)
CAE_s_142=TextReuseTextDocument(file = "~/txt_unlemmat/C_CAE_s_142.txt", tokenizer = tokenize_ngrams, n=3)
align_local(AU_s_9, CAE_s_100A, match = 1L)

#détection de remplois plus précises avec la fonction MinHash
d=read.csv(file = "AU_s_9compa.csv", sep = "\t", quote="", header = TRUE)
minhash = minhash_generator(n = 240, seed = NULL)
corpus2 = TextReuseCorpus(text=as.character(d[ ,2]), meta = list(title = "AU_CAE"), tokenizer = tokenize_ngrams, minhash_func = minhash)
corpus2=tokenize(corpus2, tokenize_ngrams)
head(tokens(corpus2[[1]]))

lsh_threshold(h=240, b=80)
lsh_probability(h = 240, b =  80, s = 0.6)

buckets = lsh(corpus2, bands = 80)
candidates = lsh_candidates(buckets)
comps = lsh_compare(candidates, corpus2, jaccard_similarity)
df2=as.data.frame(comps)[order(as.data.frame(comps)[, 3], decreasing = TRUE),]

d[c(537,67),2]
text1 = TextReuseTextDocument(text = as.character(d[537, 2]), 
                              meta = list("id" = "doc-537"), tokenizer = tokenize_ngrams, n = 3)
text2 = TextReuseTextDocument(text = as.character(d[67, 2]), 
                              meta = list("id" = "doc-67"), tokenizer = tokenize_ngrams, n = 3)
align_local(text1, text2)

#calcul de la distance cosinus
library(tm)
library(stylo)
#constitution du corpus
corpus = SimpleCorpus(DirSource("~/txt_lemmat/"), control = list(language = "lat"))
#creating term matrix with TF-IDF weighting
terms <-DocumentTermMatrix(corpus,control = list(weighting = function(x) weightTfIdf(x, normalize = FALSE)))
tf=as.matrix(terms)
#calcul de la distance cosinus
dist.cosine(tf)
df=as.matrix(dist.cosine(tf))
```

