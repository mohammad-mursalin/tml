
Experiment 1: Write a Python program to convert a given text to lowercase
and remove all punctuation.
Objectives
 To understand the need for text cleaning as a pre-processing step in NLP.
 To learn how to convert text to a uniform case and remove punctuation using Python.
Theory
Text cleaning (also called text normalization) is usually the very first step of any NLP pipeline. Raw text
collected from documents, social media posts, or web pages is messy: it contains mixed letter casing,
punctuation marks, extra symbols, and other artifacts that do not carry useful meaning for most language-
processing tasks. If this noise is not removed, a computer will treat words that are semantically identical
as completely different tokens, which hurts the accuracy of almost every later step (tokenization,
frequency counting, model training, and so on).
One common source of noise is inconsistent capitalization. To a computer, the strings “Word”, “word”,
and “WORD” are three distinct sequences of characters, even though a human reader understands them
as the same word. Converting everything to lowercase using Python's str.lower() method solves this by
forcing every character in the text to a single, uniform case, so that “NLP” and “nlp” are now treated
identically.
The second source of noise is punctuation. Marks such as commas, periods, exclamation points, and
quotation marks are essential for human readability but are often irrelevant (and sometimes harmful) for
tasks like word-frequency counting or keyword extraction, because “processing” and “processing.” would
otherwise be counted as two different words. Python's built-in string module exposes a ready-made
constant, string.punctuation, containing all standard punctuation characters (e.g., !"#$%&'()*+,-./:;<=>?
@[\]^_`{|}~). The expression str.maketrans('', '', string.punctuation) builds a translation table that maps
every punctuation character to nothing, and str.translate() then applies that table to strip all punctuation
from the text in a single, efficient pass.
For example, the sentence “Hello, World! This is Natural Language Processing.” contains a comma, an
exclamation mark, and a full stop, and mixes uppercase and lowercase letters. After lowercasing and
punctuation removal, it becomes the clean, uniform string “hello world this is natural language
processing”, which is far easier for subsequent NLP steps (such as tokenization) to work with
consistently.
As a second example, “Can’t stop, won’t stop! #NLP101” becomes “cant stop wont stop nlp101” after the
same two steps — note that apostrophes are also punctuation, so contractions lose their apostrophe as
well, which is an important side-effect to keep in mind when this technique is applied to informal text
such as tweets.

Algorithm
1. Start.
2. Take the input text.
3. Convert the entire text to lowercase using lower().
4. Create a translation table that maps every punctuation character to None using str.maketrans('', '',
string.punctuation).
5. Apply the translation table to the text using translate() to remove punctuation.
6. Print/display the cleaned text.
7. Stop.

Source Code
import string
text = input("Enter text: ")
cleaned = text.lower().translate(str.maketrans('', '', string.punctuation))
print(cleaned)

Experiment 2: Write a Python program to tokenize a paragraph into
sentences and words using NLTK.
Objectives
 To understand the concept of tokenization in NLP.
 To learn how to split a paragraph into sentences and words using NLTK.
Theory
Tokenization is the process of breaking down a large piece of text into smaller, meaningful units called
tokens. Depending on the level of granularity required, tokens can be whole sentences (sentence
tokenization) or individual words and punctuation marks (word tokenization). It is one of the very first and
most fundamental steps in an NLP pipeline, because nearly every downstream task — stopword removal,
stemming, POS tagging, frequency analysis, and so on — operates on individual tokens rather than on one
long, unstructured string of text.
Sentence tokenization looks deceptively simple (“just split on periods”), but a naive approach fails on
abbreviations like “Mr.”, “e.g.”, or decimal numbers like “3.14”, where a period does not actually mark
the end of a sentence. NLTK's sent_tokenize() function avoids this problem by using a pre-trained,
unsupervised model called “punkt”, which has learned statistically where sentence boundaries are likely
to occur in a given language, rather than relying on a single fixed rule.
Word tokenization, performed by word_tokenize(), splits a sentence into its individual words and
separates out punctuation marks as their own tokens. For instance, the sentence “It is very interesting!”
is split into the tokens ['It', 'is', 'very', 'interesting', '!'] — notice that the exclamation mark becomes a
separate token rather than staying attached to “interesting”. This separation is important because it lets
later steps (like stopword removal or POS tagging) treat words and punctuation independently.
Applying both functions to the paragraph “Hello world. This is an NLP course. It is very interesting!” first
splits it into three sentences at the sentence level, and then, when word_tokenize() is applied to the whole
text, further breaks it down into the individual words and punctuation symbols that make up those
sentences.
As a further example, sent_tokenize() correctly keeps “Dr. Smith arrived at 5 p.m. He was late.” as two
sentences rather than four, because “punkt” recognises “Dr.” and “p.m.” as abbreviations and not as
sentence-ending periods — something a naive split('.') approach would get wrong.

Algorithm
1.Start.
2.Import sent_tokenize and word_tokenize from nltk.tokenize.
3.Download the punkt tokenizer resource.
4.Take the input paragraph.
5.Apply sent_tokenize() to split the paragraph into a list of sentences.
6.Apply word_tokenize() to split the paragraph into a list of words and punctuation.
7.Print the sentences and words.
8.Stop.

Source Code
import nltk
from nltk.tokenize import sent_tokenize, word_tokenize
nltk.download('punkt_tab', quiet=True)
text = input("Enter a paragraph: ")
sentences = sent_tokenize(text)
words = word_tokenize(text)
print("Sentences:", sentences)
print("Words:", words)

Experiment 3: Write a Python program to remove English stopwords from
tokenized words using NLTK.
Objectives
 To understand what stopwords are and why they are removed in NLP.
 To learn how to filter out English stopwords from tokenized text using NLTK.
Theory
Stopwords are extremely common words in a language — such as “is”, “a”, “the”, “in”, “for”, and “of” —
that occur very frequently in almost every sentence but usually carry little meaningful or discriminative
information on their own. For example, in the sentence “This is a simple sentence for removing
stopwords”, words like “this”, “is”, “a”, and “for” appear so often across all kinds of text that they do very
little to distinguish what the sentence is actually about, whereas words like “simple”, “sentence”, and
“stopwords” carry the real content.
In tasks such as text classification, search/information retrieval, or topic modeling, keeping stopwords
adds noise and unnecessarily increases the size of the vocabulary and the data being processed, without
adding much useful signal. Removing them lets the model or algorithm focus its attention on the content-
bearing words that actually differentiate one piece of text from another, and also reduces memory and
computation requirements.
NLTK ships with a predefined, curated list of stopwords for many languages through its stopwords corpus.
In Python, stopwords.words('english') returns this list, which is then usually converted into a set (rather
than a list) because membership checking (x in set) is much faster in a set than in a list, which matters
when filtering large amounts of text. After tokenizing the input into individual words, each token's
lowercase form is checked against this stopword set, and only the tokens that are NOT present in the set
are retained in the final, filtered output.
For example, tokenizing “This is a simple sentence for removing stopwords in NLP.” and removing
stopwords leaves behind only ['simple', 'sentence', 'removing', 'stopwords', 'NLP', '.'] — the common
connective and functional words have been stripped away, while the content words remain (note that
punctuation is not a stopword, so it is not removed by this step).
A second example shows why case-folding matters here: the sentence “The Cat And The Dog Are Friends.”
would NOT have “The” and “And” removed if we forgot to lowercase each token first, since the stopword
set only contains lowercase entries like “the” and “and” — this is exactly why the filtering condition checks
w.lower() rather than w.

Algorithm
1.Start.
2.Import word_tokenize and the stopwords corpus from NLTK.
3.Download the stopwords resource.
4.Take the input text and tokenize it into words.
5.Load the set of English stopwords.
6.Iterate through the tokenized words and keep only those whose lowercase form is not in the
stopword set.
7.Print the filtered list of words.
8.Stop.

Source Code
import nltk
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
nltk.download('stopwords', quiet=True)
text = input("Enter text: ")
words = word_tokenize(text)
stop_words = set(stopwords.words('english'))
filtered = [w for w in words if w.lower() not in stop_words]
print(filtered)

Experiment 4: Write a Python program to apply stemming to words using
NLTK’s PorterStemmer.
Objectives
 To understand the concept of stemming and its role in text normalization.
 To learn how to apply NLTK's PorterStemmer to reduce words to their root form.
Theory
Stemming is a text-normalization technique that reduces a word to its base or root form, called the stem,
typically by chopping off common suffixes such as “-ing”, “-ed”, “-es”, or “-ly”. The underlying idea is that
words like “run”, “running”, “runs”, and “runner” all revolve around the same core concept, so treating
them as one common stem, rather than as four unrelated tokens, reduces vocabulary size and helps text-
analysis algorithms recognize that these words are related.
Stemming algorithms are rule-based and heuristic rather than dictionary-based — they blindly apply a
fixed sequence of suffix-stripping rules without truly understanding grammar or meaning. Because of this,
the resulting “stem” is often not a valid, complete dictionary word. For example, the Porter algorithm
reduces “quickly” to “quickli” and “jumping” to “jump” — the second result happens to be a real word,
but the first is not, simply because the algorithm mechanically strips the “-ly” and does not know that the
correct English adverb root should keep its “y”.
NLTK's PorterStemmer class implements the Porter stemming algorithm, one of the oldest and most
widely used stemmers for English, published by Martin Porter in 1980. It works by applying a series of
measured, ordered rules (organized into multiple steps) that progressively strip common suffixes from a
word, checking conditions such as the number of vowel-consonant sequences in the remaining stem
before each rule is applied, to avoid over-stemming very short words.
For instance, stemming the sentence “The cats are running and jumping quickly.” produces ['the', 'cat',
'are', 'run', 'and', 'jump', 'quickli', '.'] — notice that “cats” becomes “cat”, “running” becomes “run”, and
“jumping” becomes “jump” correctly, while “quickly” is over-stemmed into the non-word “quickli”,
illustrating both the usefulness and the crude, rule-based limitation of stemming.
As another example, ps.stem(“studies”) returns “studi” and ps.stem(“studying”) also returns “studi” —
two words that are grammatically different are correctly collapsed to the same stem, even though “studi”
itself is not a real English word.

Algorithm
1.Start.
2.Import PorterStemmer from nltk.stem and word_tokenize from nltk.tokenize.
3.Create an object of PorterStemmer.
4.Take the input text and tokenize it into words.
5.Apply the stem() method of PorterStemmer to each tokenized word.
6.Collect the stemmed words into a list.
7.Print the list of stemmed words.
8.Stop.
Source Code
import nltk
from nltk.stem import PorterStemmer
from nltk.tokenize import word_tokenize
nltk.download('punkt', quiet=True)
ps = PorterStemmer()
text = input("Enter text: ")
words = word_tokenize(text)
stemmed = [ps.stem(w) for w in words]
print(stemmed)

Experiment 5: Write a Python program to apply lemmatization to words
using NLTK’s WordNetLemmatizer.
Objectives
 To understand the concept of lemmatization and how it differs from stemming.
 To learn how to apply NLTK's WordNetLemmatizer to obtain the dictionary form of words.
Theory
Lemmatization, like stemming, aims to reduce a word to a single base form so that related word variants
are treated as one, but it does so in a fundamentally different, more linguistically informed way. Instead
of mechanically chopping off suffixes with fixed rules, lemmatization uses a vocabulary and detailed
morphological analysis to look up the word's dictionary base form, called its lemma. As a result,
lemmatization always returns a real, valid word, whereas stemming can produce invalid forms such as
“quickli”.
NLTK's WordNetLemmatizer relies on WordNet, a large lexical database of English that groups words into
sets of synonyms and records relationships between different inflected forms of a word (e.g., “is”, “was”,
“be” are all forms of the lemma “be”). Given a word, the lemmatizer looks it up in WordNet and returns
its canonical dictionary form.
A crucial detail is that the correct lemma of a word often depends on its part of speech (POS) — for
example, the word “running” lemmatizes differently depending on whether it is used as a verb (“run”) or
a noun (as in “running is good exercise”). By default, WordNetLemmatizer.lemmatize() assumes every
word is a noun unless told otherwise via a pos argument. This is why, without explicitly supplying POS
tags, verbs like “running” and “jumping” are NOT reduced to “run” and “jump” — they are left almost
unchanged because they are not recognized as nouns needing simplification, while a genuinely plural
noun like “cats” IS correctly reduced to “cat”.
Applying the default (noun-assuming) lemmatizer to “The cats are running and jumping quickly.”
therefore produces ['The', 'cat', 'are', 'running', 'and', 'jumping', 'quickly', '.'] — only “cats” changes to
“cat”, while “running”, “jumping”, and “quickly” remain untouched, in contrast to stemming, which
aggressively modified almost every word in the same sentence.
If the correct POS tag is supplied instead — for example, lemmatizer.lemmatize(“running”, pos='v') — the
result correctly becomes “run”, which shows that lemmatization is more accurate than stemming only
when it is given enough grammatical context; without it, it can under-normalize verbs and adverbs exactly
as seen above.

Algorithm
1.Start.
2.Import WordNetLemmatizer from nltk.stem and word_tokenize from nltk.tokenize.
3.Create an object of WordNetLemmatizer.
4.Take the input text and tokenize it into words.
5.Apply the lemmatize() method of WordNetLemmatizer to each tokenized word.
6.Collect the lemmatized words into a list.
7.Print the list of lemmatized words.
8.Stop.

Source Code
import nltk
from nltk.stem import WordNetLemmatizer
from nltk.tokenize import word_tokenize
nltk.download('wordnet', quiet=True)
lemmatizer = WordNetLemmatizer()
text = input("Enter text: ")
words = word_tokenize(text)
lemmatized = [lemmatizer.lemmatize(w) for w in words]
print(lemmatized)

Experiment 6: Write a Python program to perform Part-of-Speech (POS)
tagging on a sentence using NLTK.
Objectives
 To understand the concept of Part-of-Speech (POS) tagging.
 To learn how to assign grammatical tags to words in a sentence using NLTK.
Theory
Part-of-Speech (POS) tagging is the process of assigning a grammatical category — such as noun, verb,
adjective, adverb, or determiner — to each word (token) in a sentence, based on both its definition and
the context in which it appears. Context matters because many English words can act as different parts of
speech depending on how they are used; for example, “run” can be a verb (“I run every day”) or a noun
(“I went for a run”), and a good POS tagger must use surrounding words to disambiguate correctly.
POS tags reveal the underlying grammatical structure of a sentence, which is why POS tagging is a
foundational building block for many higher-level NLP applications, including syntactic parsing,
information extraction, question answering, and — as seen later in this report — named entity
recognition, since entities are usually built from sequences of proper nouns. NLTK's pos_tag() function
assigns each token a tag from the Penn Treebank tagset using a pre-trained “averaged perceptron” tagger,
a statistical model trained on large amounts of human-annotated text. Some of the most common tags
are: DT (determiner, e.g., “the”), JJ (adjective, e.g., “quick”), NN (singular noun, e.g., “fox”), VBZ (verb,
third-person singular present, e.g., “jumps”), and IN (preposition, e.g., “over”).
For the sentence “The quick brown fox jumps over the lazy dog.”, the tagger correctly labels “The” as a
determiner (DT), “quick”, “brown”, and “lazy” as adjectives (JJ), “fox” and “dog” as singular nouns (NN),
“jumps” as a present-tense verb (VBZ), and “over” as a preposition (IN) — together these tags describe
the grammatical role that every single word plays in the sentence.
Context-dependence is easy to see with the word “book”: in “Please book the flight.” it is tagged VB (verb),
while in “I read a good book.” it is tagged NN (noun) — the tagger uses the surrounding words, not just
the word itself, to decide which tag is correct.

Algorithm
1.Start.
2.Import word_tokenize from nltk.tokenize and pos_tag from nltk.
3.Download the averaged_perceptron_tagger resource.
4.Take the input sentence and tokenize it into words.
5.Apply nltk.pos_tag() on the list of tokenized words to obtain (word, tag) pairs.
6.Print the list of POS-tagged words.
7.Stop.


Source Code
import nltk
from nltk.tokenize import word_tokenize
nltk.download('averaged_perceptron_tagger_eng', quiet=True)
text = input("Enter a sentence: ")
words = word_tokenize(text)
pos_tags = nltk.pos_tag(words)
print(pos_tags)


Experiment 7: Write a Python program to show the most frequent words
in a text using NLTK FreqDist.
Objectives
 To understand how to analyze the frequency of words appearing in a text.
 To learn how to use NLTK's FreqDist class to find the most common words.
Theory
Word frequency analysis is the process of counting how many times each distinct word (or token) occurs
within a given piece of text. It is a simple but powerful technique for identifying important, dominant, or
recurring terms in a document, and it forms the basis of many other tasks in NLP and information retrieval,
such as building word clouds, computing TF-IDF scores for search engines, or constructing simple bag-of-
words feature vectors for text classification models.
NLTK provides the FreqDist class specifically for this purpose. Conceptually, a FreqDist behaves like a
Python dictionary in which each key is a unique token from the input and the corresponding value is the
number of times that token appeared, but it adds several convenience methods on top of a plain
dictionary. It is constructed by simply passing a list of tokens (typically produced by word_tokenize()) to
FreqDist(). One of the most useful of these convenience methods is most_common(n), which returns a
list of the n tokens with the highest frequency counts, sorted in descending order, as (token, count) tuples.
This makes it trivial to answer questions like “what are the five most frequently used words in this
document?” without manually sorting a dictionary by its values.
For example, in the short text “NLP is fun. NLP is interesting. NLP helps in many applications.”, the word
“NLP” appears three times, while “is” and the period “.” each appear twice, and the remaining words
appear only once. Calling most_common(5) on the resulting FreqDist therefore returns [('NLP', 3), ('.', 2),
('is', 2), ('fun', 1), ('interesting', 1)], correctly reflecting how often each of these tokens occurs in the text.
In practice, FreqDist is almost always combined with stopword removal first — otherwise, words like “the”
and “is” tend to dominate the most_common() list for any large document, hiding the actually
informative, content-bearing words underneath them.

Algorithm
1.Start.
2.Import word_tokenize from nltk.tokenize and FreqDist from nltk.probability.
3.Take the input text and tokenize it into words.
4.Construct a FreqDist object from the list of tokenized words.
5.Call most_common(5) on the FreqDist object to get the five most frequent tokens.
6.Print the result.
7.Stop.


Source Code
import nltk
from nltk.tokenize import word_tokenize
from nltk.probability import FreqDist
nltk.download('punkt', quiet=True)
text = input("Enter text: ")
words = word_tokenize(text)
fdist = FreqDist(words)
print(fdist.most_common(5))


Experiment 8: Write a Python program to generate bigrams (2-word
sequences) from a text using NLTK.
Objectives
 To understand the concept of n-grams, particularly bigrams, in NLP.
 To learn how to generate bigrams from a piece of text using NLTK.
Theory
An n-gram is a contiguous sequence of n items — usually words, but sometimes characters — extracted
from a given piece of text. When n = 1 these are called unigrams (single words), when n = 2 they are called
bigrams (word pairs), and when n = 3 they are called trigrams (word triples), and so on for larger values
of n. This lab focuses specifically on bigrams: consecutive pairs of tokens taken from the text.
Bigrams capture simple, local word-pair relationships that a bag-of-words representation (which only
counts individual word frequencies) would otherwise lose. For example, knowing that the words “brown”
and “fox” frequently occur next to each other as the pair (“brown”, “fox”) tells us something about word
order and local context that counting “brown” and “fox” separately does not. This makes bigrams (and n-
grams in general) useful for tasks such as statistical language modeling, next-word prediction (as used in
keyboard autocomplete), and collocation extraction (finding words that habitually co-occur, like “strong
coffee” versus “powerful coffee”).
NLTK's ngrams() function, found in the nltk.util module, generates these sequences automatically. It takes
a list of tokens and an integer n, and returns an iterator (which is typically converted into a list) of tuples,
where each tuple contains n consecutive tokens taken by sliding a window of size n one step at a time
across the token list.
Applying ngrams(words, 2) to the tokenized sentence “The quick brown fox jumps over the lazy dog.”
slides a two-word window across the sentence one token at a time, producing consecutive pairs such as
('The', 'quick'), ('quick', 'brown'), ('brown', 'fox'), and so on, all the way through to ('dog', '.') — together
these bigrams capture every pair of adjacent tokens in the original sentence.
The same function generalizes directly to other values of n: passing ngrams(words, 3) on the same
sentence would instead produce trigrams such as ('The', 'quick', 'brown') and ('quick', 'brown', 'fox'), which
is useful when a task needs slightly longer local context than a simple word pair provides.


Algorithm
1.Start.
2.Import ngrams from nltk.util and word_tokenize from nltk.tokenize.
3.Take the input text and tokenize it into words.
4.Call ngrams(words, 2) to generate all bigrams from the tokenized words.
5.Convert the result into a list using list().
6.Print the list of bigrams.
7.Stop.

Source Code
import nltk
from nltk.util import ngrams
from nltk.tokenize import word_tokenize
nltk.download('punkt', quiet=True)
text = input("Enter a sentence: ")
words = word_tokenize(text)
bigrams_list = list(ngrams(words, 2))
print(bigrams_list)


Experiment 9: Write a Python program to perform sentiment analysis
(Positive/Negative/Neutral) using NLTK’s VADER.
Objectives
 To understand the concept of sentiment analysis in NLP.
 To learn how to classify text as Positive, Negative, or Neutral using NLTK's VADER tool.
Theory
Sentiment analysis, also called opinion mining, is the task of automatically determining the emotional
tone or attitude expressed within a piece of text, most commonly classified into three broad categories:
positive, negative, or neutral. It is widely used in practice to analyze product reviews, social media posts,
and customer feedback at scale, without requiring a human to manually read every single piece of text.
VADER (Valence Aware Dictionary and sEntiment Reasoner), included with NLTK, is a lexicon-and-rule-
based sentiment analysis tool. Unlike machine-learning models that need to be trained on labeled data,
VADER works from a pre-built dictionary (lexicon) that assigns a sentiment intensity score to thousands
of common words, and then applies a set of grammatical and syntactical rules on top — for instance, it
understands that capitalization (“AMAZING” vs. “amazing”), punctuation (“good!!!” vs. “good”), degree
modifiers (“very good” vs. “good”), and negation (“not good”) all intensify or flip the sentiment of a
sentence. It was specifically designed and tuned for short, informal text such as social media posts, which
makes it fast and effective without needing any model training.
The SentimentIntensityAnalyzer.polarity_scores() method returns a dictionary with four values: 'neg',
'neu', and 'pos', which represent the proportion of the text that falls into the negative, neutral, and
positive categories respectively (and always sum to roughly 1), and 'compound', a single normalized score
between -1 (most negative) and +1 (most positive) that summarizes the overall sentiment of the text in
one number. By convention, a compound score >= 0.05 is treated as Positive, a score <= -0.05 is treated
as Negative, and anything in between is treated as Neutral.
For the sentence “I love this NLP course. It is amazing and very useful!”, VADER recognizes strongly
positive words like “love”, “amazing”, and “useful”, along with the intensifier “very” and the exclamation
mark, producing scores of {'neg': 0.0, 'neu': 0.318, 'pos': 0.682, 'compound': 0.7845}. Since the compound
score (0.7845) is well above the 0.05 threshold, the overall sentiment is correctly classified as Positive.
By contrast, negation flips the outcome: “This course is not good.” yields a negative compound score even
though the word “good” on its own is positive, because VADER's rule set detects the preceding “not” and
reduces or reverses the sentiment contribution of the word that follows it.

Algorithm
1.Start.
2.Import SentimentIntensityAnalyzer from nltk.sentiment.
3.Download the vader_lexicon resource.
4.Create an object of SentimentIntensityAnalyzer.
5.Take the input text and compute polarity_scores() on it.
6.Extract the compound score from the result.
7.If compound >= 0.05, classify as Positive; else if compound <= -0.05, classify as Negative;
otherwise classify as Neutral.
8.Print the scores and the overall sentiment.
9.Stop.

Source Code
import nltk
from nltk.sentiment import SentimentIntensityAnalyzer
nltk.download('vader_lexicon', quiet=True)
sia = SentimentIntensityAnalyzer()
text = input("Enter text: ")
scores = sia.polarity_scores(text)
compound = scores['compound']
if compound >= 0.05:
sentiment = "Positive"
elif compound <= -0.05:
sentiment = "Negative"
else:
sentiment = "Neutral"
print("Scores:", scores)
print("Overall Sentiment:", sentiment)

Experiment 10: Write a Python program to perform basic Named Entity
Recognition (NER) and chunking using NLTK.
Objectives
 To understand the concept of Named Entity Recognition (NER) and chunking.
 To learn how to identify entities such as persons, locations, and organizations in a sentence
using NLTK.
Theory
Named Entity Recognition (NER) is the task of automatically locating spans of text that refer to specific,
named “entities” and classifying each one into a predefined category. Common categories include
PERSON (names of people, e.g., “Barack Obama”), ORGANIZATION (companies or institutions, e.g.,
“Microsoft”), and GPE, short for Geo-Political Entity (countries, cities, or states, e.g., “Hawaii”). NER is a
key building block for information extraction systems, question answering, and knowledge-graph
construction, since it turns unstructured free text into structured facts about who, where, and what
organizations are being discussed.
NER is closely related to a technique called chunking, which groups sequences of adjacent tokens into a
single labeled unit rather than treating them individually. This is important because named entities are
often made up of more than one word (for example, “Barack” and “Obama” together refer to a single
PERSON entity, not two separate ones), so chunking is what allows multi-word entities to be recognized
as one coherent unit.
In NLTK's pipeline, NER is built on top of POS tagging: text is first tokenized, then each token is POS-tagged
(since, for example, proper nouns tagged NNP are the primary candidates for named entities), and finally
NLTK's ne_chunk() function is applied to the list of POS-tagged tokens. ne_chunk() uses a pre-trained
classifier (the maxent_ne_chunker) to group and label sequences of tokens, returning the result as a tree
structure: recognized entities appear as labeled subtrees (e.g., a (PERSON ...) or (GPE ...) subtree), while
ordinary words that are not part of any entity remain as plain leaves directly under the root of the tree.
For the sentence “Barack Obama was born in Hawaii and worked at Microsoft.”, the pipeline correctly
groups “Barack” and “Obama” together under a PERSON label, tags “Hawaii” as a GPE, and tags
“Microsoft” as an ORGANIZATION, while ordinary words such as “was”, “born”, “in”, and “and” remain as
plain, unlabeled leaves in the resulting parse tree, exactly as shown in the output below.
As a further example, the sentence “Elon Musk founded SpaceX in California.” would similarly group
“Elon” and “Musk” under PERSON, “SpaceX” under ORGANIZATION, and “California” under GPE —
illustrating that ne_chunk() generalizes to any sentence whose proper nouns follow similar surface
patterns, rather than working only for the one example sentence it was trained to recognise.

Algorithm
1.Start.
2.Import word_tokenize, pos_tag, and ne_chunk from NLTK.
3.Download the maxent_ne_chunker and words resources.
4.Take the input text and tokenize it into words.
5.Apply pos_tag() to obtain POS tags for the tokenized words.
6.Apply ne_chunk() on the POS-tagged words to obtain a chunked tree with named entities
labeled.
7.Print the resulting tree.
8.Stop.

Source Code
import nltk
from nltk.tokenize import word_tokenize
from nltk import pos_tag, ne_chunk
nltk.download('maxent_ne_chunker_tab', quiet=True)
nltk.download('words', quiet=True)
text = input("Enter a sentence: ")
words = word_tokenize(text)
pos_tags = pos_tag(words)
ner_tree = ne_chunk(pos_tags)
print(ner_tree)
