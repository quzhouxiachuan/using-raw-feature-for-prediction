# using-raw-feature-for-prediction
```
x=read.csv("LimitedHF_final_combined.csv",header=F)
library(dplyr)
med= x %>% group_by(V1) %>% count(V3)
dia= x %>% group_by(V1) %>% count(V2)
spread(ww, V2, n)
library(tidyr)
diaa= spread(dia, V2, n)
medd= spread(med, V3, n)
dd=merge(diaa, medd, by='V1',all=T)
dd[is.na(dd)]=0
write.csv(dd,'raw_feature_prediction_merge.csv')
savehistory(file = ".Rhistory")

library(caret)
library(doParallel)
#library(kernlab)
set.seed(415)
#edit the following directory to your auc folder directory
registerDoParallel(cores=20)
data=read.csv('raw_feature_prediction.csv',row.names=1)
data=subset(data, select=-c(V1))
data$label=factor(data$label)
inTrain=createDataPartition(y=data$label, p=0.75, list=FALSE)
training=data[inTrain,]
testing=data[-inTrain,]
library(pROC)
control <- trainControl(method="repeatedcv", number=6, repeats=3)
mtry=sqrt(ncol(data))
tunegrid=expand.grid(.mtry=mtry)
fit=train((label) ~., training, tuneGrid=tunegrid, method="rf",trControl=control,ntree=70)
p=predict(fit, testing, type='prob')
auc=roc( testing$label,p$case)
auc=as.character(auc[[9]])
```
