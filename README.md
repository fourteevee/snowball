# Snowball Poem Generator

by Paul Thompson - nossidge@gmail.com / snowballpoetry@gmail.com

Generates snowball poems from raw English text, using Markov chains and dictionary
lookups to validate input words.

## What is a snowball poem?
Snowball (also called a Chaterism): A poem in which each line is a single word,
and each successive word is one letter longer. One of the constrained writing
techniques invented by the
[Oulipo](http://en.wikipedia.org/wiki/Oulipo) (Workshop of Potential Literature).

    o
    in
    her
    head
    empty
    noises
    reached
    rattling
    cacophony

## Sample Snowball Output

Beware! The output will, for the most part, be absolute rubbish. But there will
be wheat in the chaff. These are some actual unedited generated poems. The input
was mostly Dickens.

    i am but dust which seemed nothing whatever
    o my two feet again walked through profound solemnity
    i am the dawn light before anybody expected something disorderly
    i do not like being hungry

You can find more curated output on Twitter:
https://twitter.com/SnowballPoetry

# Quick Start Guide

Download the Windows 7 executable program here:
https://github.com/nossidge/snowball/blob/master/bin/Release/snowball.exe?raw=true

Also download these two text files, and save them in the same folder as the program:
https://github.com/nossidge/snowball/blob/master/snowball-lexicon.txt?raw=true
https://github.com/nossidge/snowball/blob/master/snowball-corpus.txt?raw=true

To use it, see the "Most common options" section of console-detailed.txt:
https://github.com/nossidge/snowball/blob/master/console-detailed.txt

    snowball
Create snowball poems by running the program with default settings.

    snowball -r input-directory
Create snowball poems from raw text found in the "input-directory" folder.
    
    snowball -s seed-phrases.txt
Create snowball poems using the phrases found in file "seed-phrases.txt".

# Program Description

This program reads input from plain English text files. It examines each file
line by line, for any word phrases in which the length of each word varies from
the previous word by one letter, e,g. "his face", "in the land".

It saves each phrase it finds as a separate line in a preprocessed "corpus" text
file. In future program runs this file can be read, so that the whole directory
of raw English input files does not have to be processed each time.

The program will generate a batch of Snowball poems each time. In general, it
starts at a one letter word ("A", "I", "O") and randomly traverses a Markov tree
that links the second of one pair to the first of another if they are the same
word. It then repeats this process, stopping when it reaches a dead branch.

While generating the poem, word by word, the program can examine previously
chosen words and use them in combination with the current word to select a more
likely following word. For example if the poem currently being processed started
"i am the lord", and the following word phrases were available:

    "lord spoke"
    "lord lucan"
    "the lord above"

The word "above" would be prioritised above "spoke" and "lucan", as "the lord"
is a longer key than simply "lord".

You can also specify a "seed phrase" to the program. This
ensures that all poems created in that batch will contain the given phrase.
If the seed phrase would form a middle portion of the poem (e.g. "still cannot
believe") then the poem will be generated backwards, down to a one letter word.
As such, this might fail if there are no "parents" to a word's "child". The
program will abort after a certain number of beginning-less poems are generated,
but will output any poems it has found up to that point.

## Input Files - Raw English Text - Corpus

When the "-r" or "-R" option is specified, the program loops through a directory
and loads each file it finds there. It does no error checking, so make sure that
the directory contains only text files.

It then saves all the snowballing phrases to a corpus file, so that it can be
read from quickly in future runs.

A great feature of the corpus files is that, in addition to their use as
preprocessed input, they can also be used as raw input. So if you want to search
more text for snowball phrases, you can put the raw files in a directory with
your existing corpus file and set the program to create phrases from that
directory. This will output a new corpus file which contains all the phrases
from the original corpus file, together with any new phrases found in the new
raw files.

The input that I am currently using for this is mostly the text files
contained in the Project Gutenberg 2010 DVD:
http://www.gutenberg.org/wiki/Gutenberg:The_CD_and_DVD_Project

There are 26,890 files, 9.54GB total size. It takes 1h:40m to process them all.
The resulting processed file, of only the snowballing phrases, is 60MB in size.

## Input Files - Lexicon

**snowball-lexicon.txt** (Use "-l" to change the file name, and "-L" to disable)

The text that I use is a bunch of files from Project Gutenberg. These are not
guaranteed to be in English, and also, authors like to make up words sometimes
when they're bored. Science fiction is a bugger for this sort of thing.

My solution to this is to use a lexicon of acceptable English words and check
each scanned word to make sure it is acceptable. If it doesn't appear in the
lexicon file, then it will be ignored.

The lexicon file I'm using is based on SCOWL (Spell Checker Oriented Word Lists)
found here: http://wordlist.sourceforge.net

This file can be pruned and improved by removing any words that you don't want
in the final snowball poems. This ensures that this word will never be used in
the future, no matter what raw input text is used.

The lexicon file is only used when reading from raw input files and creating
the corpus files.

## Input Files - Thesaurus

**snowball-thesaurus.txt** (Use "-t" to change the file name, and "-T" to disable)

The program will switch words like:

    among amongst
    colour color
    compear compare
    completely utterly totally absolutely

In the last example, if it finds a line with "totally" in it, it adds three more
lines, substituting the word with "completely", "utterly", and "absolutely".

This can be used to switch between British and American English words, to fix
common spelling errors, to correct archaic words to the current spelling, or to
switch between synonyms.

The thesaurus file is only used when reading from raw input files and creating
the corpus files.

## Output Files

**output-snowballPoems-1234567890.txt**
This is the final output of all the created poems. The numbers are a timestamp.

**output-snowballPoems-1234567890-[seed-phrase].txt**
This is the final output of all the created poems. These poems have all been
generated from the seed phrase "seed phrase" within the square brackets.

**snowball-corpus.txt**
This file is generated from raw text when using the -r option. It only contains
the phrases that snowball upwards by one letter. It is used as quick input to
the snowball generator so the program doesn't have to generate from raw text
each time.

**DEBUG FILES:** These files are only generated when the "-d" option is specified:

**output-wordsForwards.txt** and **output-wordsForwards-keyHeader.txt**
Lists all growing snowball phrases in the map table that the program uses for
quick value lookup. Keys with multiple words are separated by a "|" delimiter.
Both files contain the same data, but are saved in different ways.

**output-wordsBackwards.txt** and **output-wordsBackwards-keyHeader.txt**
Lists all shrinking snowball phrases in the map table that the program uses for
quick value lookup. Keys with multiple words are separated by a "|" delimiter.
Both files contain the same data, but are saved in different ways.
This information is only used in the program when a seed phrase necessitates
looping backwards to find the opening of a poem.

**output-wordsWithLength.txt**
Lists all words alphabetically by length. This only includes words that are not
"dead branches" - i.e. words which chain to a subsequent word one letter longer.

**output-wordsDeadBranches.txt**
Lists all single word keys in wordsBackwards that are not keys in wordsForwards.
This is all the words that do not have a subsequent word to follow them. If any
of these words are selected in a poem, the poem will end because there would be
no other word to chain to.

You can use this to help in the filtering down of your lexicon file. If a word
does not have any other word following it, it is more likely that it is not a
valid word. You can then decide if maybe you should remove it from the lexicon.

## Thoughts for the future

### Line breaks and punctuation
The code scans through the input file, and examine each whitespace-separated
word. If the word contains punctuation or numbers then it is ignored. This
means that it also does not word pair between line breaks. This may perhaps be
an issue with books from Project Gutenberg; as their text files are fixed width
we could be missing some word pairs.

### Other languages
It shouldn't be too tricky to alter this to accept Êúröpèāñ type accent
characters, and the lexicon file could just be a different language. But
as I don't personally speak any other language I won't be doing this.

### Other operating systems
Currently Windows 7 only, but probably able to be converted easily enough.
It should just be the file and directory code that needs looking at.