
# Bi-LSTM


DATE:  30-08-24


Tags:  [[Notes/LSTM|LSTM]] [[Notes/RNN|RNN]]

# References:




# Content:

- In RNNs and LSTMs, a word learns the context only from the words that come before it. But in reality context should be learned from the entire sentence, from both words preceeding an coming after.
- Bidirectional lstms try to address this but even here the left to right and right to left context are learnt separately and then they are concatenated and so some of the true context is lost.


