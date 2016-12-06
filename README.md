
### 引用 

 
本项目模型基本是参考论文：http://www.aclweb.org/anthology/N16-1030


### 构建

1. 安装好bazel代码构建工具，clone下来tensorflow项目代码，配置好(./configure)
2. clone 本项目地址到tensorflow同级目录，切换到本项目代码目录，运行./configure
3. 编译后台服务 

   > bazel build //kcws/cc:seg_backend_api


### 训练

1. 关注待字闺中公众号 回复 kcws 获取语料下载地址：
   
   ![logo](https://github.com/koth/kcws/blob/master/docs/qrcode_dzgz.jpg?raw=true "待字闺中")
   
   
2. 解压语料到一个目录

3. 切换到代码目录，运行:
  > python kcws/train/process_anno_file.py <语料目录> chars_for_w2v.txt
  
  > bazel build third_party/word2vec:word2vec
  
  > 使用word2vec 训练 chars_for_w2v (注意-binary 0),得到字嵌入结果vec.txt
  
  > ./bazel-bin/third_party/word2vec/word2vec -train chars_for_w2v.txt -output kcws/models/vec.txt -size 50 -sample 1e-4 -negative 5 -hs 1 -binary 0 -iter 5
 
  
  > bazel build kcws/train:generate_training 
  
  > ./bazel-bin/kcws/train/generate_training vec.txt <语料目录> all.txt
  
  > python kcws/train/filter_sentence.py all.txt  （得到train.txt , test.txt)

4. 安装好tensorflow,切换到kcws代码目录，运行:
  > python kcws/train/train_cws_lstm.py --word2vec_path vec.txt --train_data_path <绝对路径到train.txt> --test_data_path test.txt --max_sentence_len 80 --learning_rate 0.001
  
5. 生成vocab
  > bazel  build kcws/cc:dump_vocab
  
  > ./bazel-bin/kcws/cc/dump_vocab kcws/models/vec.txt vocab.txt
  
6. 运行web service
  > ./bazel-bin/kcws/cc/seg_backend_api --model_path=kcws/models/seg_model.pbtxt(绝对路径到seg_model.pbtxt>)   --vocab_path=vocab.txt(<绝对路径到vocab.txt>)   --max_sentence_len=80
 
### demo
http://45.32.100.248:9090/

附： 使用相同模型训练的公司名识别demo:

http://45.32.100.248:18080

### 深度学习交流群

为了 控制群质量 大家先加我微信号 kothme
加群时备注自己从事的方向 最好提了2，3个深度学习术语
我拉大家 进群


