# Why Unsupervised Language Modeling?
# 왜 비지도 언어 모델링인가?
## Difficulties of Supervised Natural Language
## 자연어를 지도 하는 방식은 어렵다.

The success of Deep Learning is not enjoyed to the same extent in the domain of Natural Language Processing (NLP), as in other domains such as Computer Vision and Speech. This is in part due to difficulties in collecting and modeling language data.

자연어 처리 분야에서 딥 러닝은 그렇게 흥분되는 일은 아니었습니다. 언어 모델을 위한 데이터를 모으기가 어렵거든요.

For example, creating supervised datasets for vision tasks can be as straightforward as capturing image or video data with a personal device, and tasking a labeler to perform a task in basic classification, bounding boxes, segmentation mask etc.

예컨대 컴퓨터 비전에서 지도 학습을 위한 데이터를 만든다고 생각 해 봅시다. 이미지를 캡쳐하거나 비디오 데이터를 얻고 이제 bounding box를 그리거나 라벨을 붙일 사람을 구하면 됩니다.

This is in contrast with gathering and labeling data for language tasks. For starters, the media of data gathering is wildly different. Visual data is captured through the lens of a camera, a perfect device, while language data must first originate as an idea in a person's mind, and be transcribed in imperfect form by them putting pen to paper, before ever being collected by one of many custom web scrapers. 

언어 모델은 위에서 설명한 방식들이 통하지 않습니다. 쉽게 말하자면, 데이터를 수집하는 방식 부터가 전혀 다릅니다. 시각적 데이터는 얻기가 쉽습니다. 시각적 데이터는 카메라의 렌즈를 통해서 데이터가 모이지만 언어는 사람의 '마음'으로 부터 나옵니다. 그리고 종이 위에 글로 옮겨 적히죠. 종이 위에 있는 데이터를 웹 크롤러가 긁을 수 있을리가 없고요.


Assuming that a relatively clean dataset can be aggregated, labeling has its own unique set of problems. Some tasks have an easily agreed-upon label, some require labelers to converse normally with chatbots, but some can require a high degree of reading comprehension and language understanding. Consider the slightly tangential task of predicting author emotion/mood as opposed to sentiment. This is a straightforward task with a correct answer that has appeared on many a student's standardized tests; however, reading comprehension questions are difficult, and has subsequently been missed by a proportional number of students.

그래도 꽤 정확하고 썩 괜찮은 언어 데이터를 모았다고 가정합시다. 글에 라벨링 작업을 할 때는 고도의 읽기 능력이나 이해력이 필요 할 수도 있습니다. 글을 쓴 사람의 기분이나 감정을 알아내는 작업을 시키면 대부분의 학생들이 정확한 답을 내 놓습니다. 그러나 어떤 글을 읽고 답변을 해야하는 작업은 글의 난이도에 따라서 학생들이 오답을 내놓습니다.

This in turn has limited supervised language to tasks with easily interpreted, collected, and agreed upon labels such as neural machine translation, dictionary definition, tree parsing, and sentiment analysis to name a few.

결국 지도 학습으로 만들어진 언어 모델은 데이터를 모으기 쉽고, 라벨로 해석하기 쉬운 분야에만 쓰이는 제한이 있습니다. 예를 들자면 신경망 번역, 사전 정의, 트리 해석, 그리고 감성분석 같은게 그 예입니다.

Transfering an unsupervised model to the domain of sentiment analysis is attractive from a research standpoint as it both:
 * has a relatively large corpus of research, datasets, and benchmarks to measure performance
 * is a simple discriminitive classification task that easily benefits from robust featurizations of text (provided by an unsupervised language model)
 
 감성 분석을 비지도 학습으로 전환 해 보는게 매력적인 이유는 두가지가 있습니다.
 
 * 
 

Overall, language is hard and communicating with it effectively on a daily basis can elude even the best of us. Communicating language information to AI via labels is even more difficult. Given this bottleneck we see robust unsupervised learning regimes for natural language as an attractive research avenue in order to mitigate tasks' dependences on large, labeled, datasets.  

라벨로 AI에게 언어를 학습시키는건 더 어렵습니다. 

## Data Robustness 
Utilization of byte-level recurrent models allows for learning directly on the binary values of the text (since one character is one byte/8 bits). This is advantageous for data collection and processing as it allows us to work with a wide range of text samples, such as these, without any tokenization and little data cleaning:
 
![Robustness examples](../figures/errors.png "Activation heatmap for erroneous sentiment samples")
 
No feature engineering has to be done at the data processing level to determine how to handle:
 * **Capitalization**: We don't have to create distinct tokenizations between capitalized/uncaptilized words. 
 * **Misspelling**: It's robust to misspelings like GAME -> GMAE without having to clean it or making a unique token.
 * **Unknown entities**: Most importantly, perhaps, it's able to process unknown entities such as twitter handles, hashtags, links, and emojis. 
   * It doesn't get confused by the bad grammar, captilization or spacing associated with handles & hashtags. 
   * It can handle the random strings of uncleaned links. 
   * It doesn't necesarily focus on the entities, rather it focuses on the prepositions around entities that it knows corresponds to certain sentiments. 
   * For frequent entities that tie with sentiment like emojis/ascii art, it is however able to learn the positive or negative sentiment associated with them.

## Model/Optimization Robustness
Accurately labeling and training on supervised language datasets is difficult. As the quality of labelers goes up, the volume of labels shrinks and vice-versa. 

Fitting to small, well-labeled datasets can produce competitive results given domain expertise and intelligent feature engineering; however, more often than not over-parameterized neural networks will pick up on specific niche patterns, miss the bigger picture, and overfit. 

Large noisy datasets have plenty of data, and with good labeling practice, law of large numbers will be in your favor and you'll get a sensible distribution of labels for a particular data point. However, extra walltime will be spent training on and sifting through these data points in order to reach a particular performance threshold. This is further aggravated by known problems in training recurrent models. Specifically,
 * Recurrent models are sequential in nature, not ammenable to parallelization, and take a long time to process forward and backward propagation.
 * Because of gradient vanishing, training models on long sequences takes additional time. 
   * Important gradients at the begining of a long sequence might not be effectively learned without more iterations.
 * The recurrent weight space is difficult to optimize and often filled with saddle points.

Pretraining models with unsupervised learning is effective in both these scenarios:
 * Instead of training classficiation models End2End and backpropagating through time, only the output/classification layers have to be trained, greatly reducing training time on a target dataset.
   * Training the recurrent portion of the network will take a fixed time with each training iteration, since the unsupervised dataset is processed in fixed length sequences.
 * In the event that the target transfer task has a highly correlated single feature, only a single decision boundary (for linear models) can be formed for this feature. This single feature and boundary can help avoid overfitting to complex patterns that appear in small labeled sets of data. Furthermore, the approximate value of this decision boundary can be teased apart with relatively few samples (if there are n values then a decision boundary can be found in O(logn) time with binary search).

-----

[Reproducing Results ->](./reproduction.md) 
