# language-model-LSTM-
你将使用来自ROC故事语料库的数据集。为了减少单词量，我们将大多数名字都被转换为了Bob和Sue。并且这些句子只用到了一个比较小的单词表。请下载数据文件 链接: https://pan.baidu.com/s/1acN1KdzXoXZMBtYW3I6IMw  密码:dph6

解压文件夹中包含以下数据：

bobsue.lm.train.txt：语言模型训练数据（LMTRAIN）
bobsue.lm.dev.txt：语言模型验证数据（LMDEV）
bobsue.lm.test.txt：语言模型测试数据（LMTEST）
bobsue.prevsent.train.tsv：基于上文的语言模型训练数据（PREVSENTTRAIN）
bobsue.prevsent.dev.tsv：基于上文的上文语言模型验证数据（PREVSENTDEV）
bobsue.prevsent.test.tsv：基于上文的上文语言模型测试数据（PREVSENTTEST）
bobsue.voc.txt：词汇表文件，每行是一个单词
lm文件中的每一行都包含一个故事中的句子。prev文件中的每一行都包含一故事中的一个句子，tab，然后是故事中的下一个句子。注意：prevsent文件中每一行的第二个字段与相应的lm文件中的对应行相同。( 也就是说：cut -f 2 bobsue.prevsent.x.tsv与bobsue.lm.x.txt相同）完整的词汇表包含在文件bobsue.voc.txt中，每一行是一个单词。在这个任务中不会出现未知单词。

评估：
我们使用单词预测准确率作为主要评估指标而非困惑度(perplexity)。因为当你试图比较某些损失函数时，perplexity不太好用。

你需要评估模型预测除了<s>之外每个单词的能力LMDEV (或LMTEST)。在LMDEV文件中，有

7957 (cat bobsue.lm.dev.txt | awk '{print NF-1}' | paste -sd+ - | bc) 个预测单词。在LMTEST文件中，有8059 (cat bobsue.lm.test.txt | awk '{print NF-1}' | paste -sd+ - | bc) 个预测单词。你可以检查一下预测的数量，确保自己的代码中没有明显的bug。对于每一个预测，使用句子中以前的真实词。开头的<s>默认直接给出，不需要预测，但是结尾的</s>需要预测。在预测句子第t+1个单词的时候，我们认为之前的t个单词是已经给定的。（大家可以回顾一下语言模型的定义和目标是什么，就可以理解为什么我们这样定义这个问题了。）

当你有一个softmax层时，预测的单词是softmax中最可能出现的单词。如果你使用其他损失函数，预测的单词应当是得分最高的单词（下文中将定义得分的计算函数）。

注意在用模型训练和预测的时候，每个句子是独立的。也就是说，每个句子都是从<s>开始，</s>结束，前后句子之间没有关系。

请使用LMDEV做early stopping。也就是说，请使用LMDEV上表现最佳的模型来测试LMTEST，并汇报准确率。同时汇报LMDEV的最佳准确率。

你可以这样做early stopping，在训练期间定期计算LMDEV的loss和word预测准确率。至少每个epoch计算一次，计算2次或更多次可能更好。

我们将上面介绍的模型评估流程缩写为EVALLM。EVALLM包括：关于LMTRAIN的训练，使用LMDEV进行early stopping（使用word准确率作为early stopping的标准），并报告LMDEV上最佳word预测准确率，使用在LMDEV上表现最佳的模型评估LMTEST并汇报准确率。
