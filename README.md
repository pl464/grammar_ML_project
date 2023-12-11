# Description

## Overall Approach
This was a very interesting problem in that I could see multiple ways to tackle it. What I naturally intuited from the problem description was a 2-part approach, in which an example is first classified as grammatical or not (binary classification), and then a correct substitution is suggested. 

However, the way I ultimately chose to approach it was as a 3-way text classification task, where an input sentence is mapped to a 0, 1, or 2, representing which of "their", "there", and "they're" (respectively) should be used in that input sentence. This may seem unnatural given that the input already contains some usage of "their", "there", or "they're" (grammatical or not), but this way, the output label can tell you two things at once:

(1) if it doesn't match the "their/there/they're" word in the example, then the model is suggesting this example is ungrammatical, and 
(2) it makes a suggestion.

Additionally, keeping it as one task made evaluation simpler. Here are some examples of how the model could be interpreted:

"**Their** knowledge of those facts was incomplete." -> 0

The output of 0 means the model thinks the sentence should use the word "their", which matches the form in the input, so the model is saying this example is grammatical.

"**There** knowledge of those faacts was incomplete." -> 0

The output of 0 means the model thinks the sentence should use the word "their". This does not match the form in the input ("there"), so the model is saying this example is ungrammatical, and proposes that it should be "their".

## Files
The two notebooks contain the code I used for the data prep and model training. References can be found in `Model_Training.ipynb`. 
The `project_files` folder contains the data. `their_there_theyre_train.csv` etc. contains all the matches of "their/there/theyre" from the GloWbE corpus, while `train.csv` etc. contain the actual files used for training/evaluation.

## Data
All the examples come from the first half of the US portion of the GloWbE (Global Web-based English) corpus https://www.english-corpora.org/glowbe/, which I have used in my research work; I make the simplifying assumption that all sentences are grammatical. I extracted single sentences containing a single use of "their/there/they're", ultimately sampling 10k examples for each word (total of 30k examples). The labels were assigned at this point (0, 1, and 2 for "their", "there", and "they're", respectively). 

Then, for each of "their/there/they're", I selected half of the examples to be "corrupted" (substituted with one of the other two, evenly) to form the ungrammatical examples. For example, out of the 10k examples originally containing "their", in 2.5k of them, "their" was replaced with "there", and in another 2.5k, "their" was replaced with "they're". In this way, I generated 15k grammatical examples (5k each of "their/there/they're") and 15k ungrammatical examples (featuring 5k each of "their/there/they're"). 

I used an 80-10-10 split to form the training, validation, and test sets, keeping the labels balanced in each set.

## Results
I used a BiLSTM for the classification, reasoning that bidirectional context can definitely help determine which of "their/there/they're" should be used. 

Classification Report:

              precision    recall  f1-score   support
           0     0.9185    0.9130    0.9157      1000
           1     0.9091    0.9100    0.9095      1000
           2     0.9851    0.9900    0.9875      1000

    accuracy                         0.9377      3000

The model performed well (better than I expected), achieving a 93.8% accuracy with high metrics across the labels. Label 2 ("they're") stands out with near-perfect scores, suggesting that grammatical uses of "they're" are easier to distinguish than "their" or "there".

## Future Work
Things I'd really like to try are:
- augmenting the input with linguistic information, like part-of-speech tags, to potentially improve performance
- trying transformers or other kinds of DL models
  - adding tags to the keyword ("their"/"there"/"they're") in the input text to potentially focus models (esp transformers) on the critical word in the input
- using an actual corpus of grammatical mistakes, rather than a general-use corpus
- generally formulating the task in a different way

I tried to summarize my approach here, but I'm happy to discuss more details. I would also love to hear feedback on my approach. You may contact me at leep6@montclair.edu. Thank you!
