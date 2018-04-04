question-generator
============================

Sample usage
------------

Running the command:

```bash
$ quesgen '<search phrase>'
```

yields:

```json
[
  {
    "question": "Bennett is also an accomplished __________, having created works\u2014under the name Anthony Benedetto\u2014that are on permanent public display in several institutions.",
    "answer": "painter", "title": "Tony Bennett",
    "similar_words": ["classic", "classicist", "constructivist", "decorator", "draftsman", "etcher", "expressionist", "illustrator"]
  }
  {
    "question": "He is the __________ of the Frank Sinatra School of the Arts in ..."
  }
]
```

Quickstart
----------

question-generator is a Python 3 project that uses the fantastic [click](http://click.pocoo.org/3/) package to expose itself as a shell command.

You can use the project locally (and quickly) through [Docker](https://www.docker.com/) or a local installation of Python 3.4.


### Installing with Python 3.4

Clone the repo, and then use pyvenv-3.4 (or virtualenv) to create a new virtual environment. Then, install the requirements and the NLTK corpora:

```bash
$ pip install -r requirements.txt
$ python -m textblob.download_corpora
$ pip install -e .
```

Now you can run the tool with the command `quesgen`.

Advanced usage
--------------

By default, the tool will scrape the hard-coded sample articles listed in the `--help` and return its results to stdout.

### Scraping a specific article

You can point the tool to a specific Wikipedia page by specifying its title:

```bash
$ quesgen 'William Shatner'
```

**Be sure to include multi-word titles in quotes, or the tool will treat each word as a separate title.**

### Scraping multiple articles

You can scrape multiple articles at once by providing multiple titles:

```bash
$ quesgen 'Leonard Nimoy' 'George Takei' 'Nichelle Nichols'
```

### Outputting to JSON

If you want to take this data elsewhere, you can output the results to a JSON file:

```bash
$ quesgen --output scotty.json 'James Doohan'
```

Methodology
-----------

### Finding the right ___________'s

1. **Only consider sentences in the summary section of an article.** Sentences from the body often didn't make sense out of context.
1. **Never use the first sentence of the summary.** It's usually too straightforward to make interesting trivia.
1. **Don't use a sentence that starts with an adverb.** They usually depend too heavily on the idea of the previous sentence to make sense out of context.
1. **Blank out the first common noun in the sentence (e.g. 'painter', 'infantryman').** Proper nouns (e.g. 'Frank Sinatra', 'The White House') usually seemed too easy to guess when given the title of the article and the other words in the sentence.
1. **If that noun is part of a noun phrase, blank out the last two words of the phrase**. Blanking out just one word seemed too easy if the phrase was recognizable.

### Creating decoy answers

For sentences where just one word was blanked out, I also used [WordNet](http://wordnet.princeton.edu/) to find similar words to the answer (the blanked out word). These words provide decoy answers during the trivia game.

My approach is to find the hypernym of the answer, and then select other hyponyms of that hypernym.

In the example in the "Sample usage" section, the correct answer is **painter**. The hypernym of painter is **artist**. The hyponyms I found for **artist** appear in the `similar_words` array in the output: "classic", "classicist", "constructivist", "decorator", "draftsman", "etcher", "expressionist", "illustrator".

