library(data.table)
library(phylotools)
library(ggplot2)

# also some good online analysis
# https://nextstrain.org/ncov


# download all sequences and metadata from
# https://bigd.big.ac.cn/ncov/release_genome

data <- read.fasta ("all.fasta")
class(data)
setDT(data)
data$seq.name

data$name <- unlist(lapply(strsplit(data$seq.name," | ",fixed=T),function(x)x[1]))
data$id <- unlist(lapply(strsplit(data$seq.name," | ",fixed=T),function(x)x[2]))

data[id=="|",.(seq.name,name,id)]



meta <- fread("meta.csv")
head(meta)
table(meta$source)
nrow(meta)
nrow(meta[source!="GISAID"])
# nr sequences matches all non-GISAID entries, also specified that these are not included

# check if all contained
length(meta$id[meta$id%in%data$id])
length(meta$name[meta$name%in%data$name])

# merge
tdat <- merge(data,meta,by="id",all.x=T)
# which have different names
tdat[name.x!=name.y,.(id,name.x,name.y)]
# only typo, thus use name from meta (y)
tdat[,name:=name.y]
# tidy up
tdat[,name.x:=NULL]
tdat[,name.y:=NULL]
tdat[,seq.name:=NULL]
setnames(tdat,"seq.text","sequence")

#############################
data <- tdat
names(data)
data[,length:=nchar(sequence)]

# check if primer sequences occur in sequence
# from 
# https://www.who.int/docs/default-source/coronaviruse/protocol-v2-1.pdf?sfvrsn=a9ef618c_2
seq1 <- "ACAGGTACGTTAATAGTTAATAGCGT" # E gene, E_Sarbeco_F1
data[,avail1:=grepl(seq1,sequence)]
seq2 <- "CAGGTGGAACCTCATCAGGAGATGC" # RdRP gene, RdRP_SARSr-P2
data[,avail2:=grepl(seq2,sequence)]

data[,.N,.(avail1,avail2,complete)]
data[,.(Samples=.N,lMin=min(length),lMax=max(length)),complete]
data[,.(Samples=.N,lMin=min(length),lMax=max(length)),.(complete,avail1,avail2)]

# get position of primer seq
data[,pos1:=regexpr(seq1,sequence)] # matches the E gene in the gene overview in version 1.0 on 
data[,pos2:=regexpr(seq2,sequence)] # is somewhere in ORF
table(data$pos1)

table(data$pos2)

data[id=="MT106053","Submitting Lab"]
names(data)
