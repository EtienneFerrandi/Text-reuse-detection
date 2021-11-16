# Text-reuse-detection

It'as about to detect the passages from Agustine homilies whose Caesarius has reused for the writing of his own homilies.

```ruby
library(textreuse)
corpus1=TextReuseCorpus(dir = "~/txt_lemmat/", tokenizer = NULL)
corpus1=tokenize(corpus1, tokenize_ngrams)
head(tokens(corpus1[[1]]))
compa = pairwise_compare(corpus1, jaccard_similarity) #mesure de la similarité de Jaccard entre les textes du corpus
df=pairwise_candidates(compa, directional = TRUE)

AU_s_9=TextReuseTextDocument(file = "~/txt_unlemmat/AU_s_9.txt", tokenizer = tokenize_ngrams, n=3)
CAE_s_100A=TextReuseTextDocument(file = "~/txt_unlemmat/CAE_s_100A.txt", tokenizer = tokenize_ngrams, n=3)
CAE_s_109=TextReuseTextDocument(file = "~/txt_unlemmat/C_CAE_s_109.txt", tokenizer = tokenize_ngrams, n=3)
CAE_s_142=TextReuseTextDocument(file = "~/txt_unlemmat/C_CAE_s_142.txt", tokenizer = tokenize_ngrams, n=3)
#détection de remplois par Césaire d'Arles d'un sermon d'Augustin en utilisant l'algorithme de Smith-Waldermann 
#utilisé traditionnellement en biologie pour rapprocher des séquences génétiques
align_local(AU_s_9, CAE_s_100A, match = 1L)

#détection de remplois sur un passage précis recopié par Césaire du sermon 9 d'Augustin avec 
#une approche LSH de recherche des plus proches voisins au sein d'extraits identifiés au préalable comme ressemblants

#détermination d'un seuil de similarité de Jaccard en-dessous duquel deux textes ne peuvent être considérés comme proches
lsh_threshold(h=240, b=80) 
lsh_probability(h = 240, b =  80, s = 0.6)

e=read.csv(file = "AU_s_9,7&CAE_s_100A_compa.csv", sep = "\t", quote = "", header = TRUE)
corpus3=TextReuseCorpus(text=as.character(e[ ,2]), meta = list(title = "AU_CAE"), 
tokenizer=tokenize_ngrams, n=2, minhash_func = minhash)
buckets = lsh(corpus3, bands = 80) #préparation des "galets" et comparaison des paires candidates identifiées dans les extraits
candidates = lsh_candidates(buckets)
comps = lsh_compare(candidates, corpus3, jaccard_similarity)
df3=as.data.frame(comps)[order(as.data.frame(comps)[, 3], decreasing = TRUE),]
df3=df3[which(df3[,3]>0.2320794),] #sélection des valeurs supérieures au seuil
nrow(df3)

#liste des paires candidates
e[c(20,59),2]
e[c(23,62),2]
e[c(26,33),2]
e[c(43,81),2]
e[c(26,64),2]
e[c(33,64),2]
e[c(37,73),2]
e[c(1,46),2]
e[c(3,48),2]
e[c(41,79),2]
e[c(16,53),2]
e[c(18,56),2]
e[c(21,58),2]
e[c(22,60),2]
e[c(25,63),2]
e[c(26,70),2]
e[c(33,70),2]
e[c(42,80),2]
e[c(2,47),2]
e[c(26,71),2]
e[c(33,71),2]

#calcul de la distance cosinus entre les textes du corpus
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

