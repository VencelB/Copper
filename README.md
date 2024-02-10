# Copper
This model will allow you to quickly and easily assess the human-perceivable quality of answers to questions related to Python development.

To start using the model, just copy the code from the pipeline notebook and download the corresponding files. 

The model takes as input the text of the question and answer as strings. All code-like parts in the question and answer must be enclosed in paired $<$code$>$ and $<$/code$>$ tags. The output for a question and answer pair is a number between 0 and 1, and to compare the quality of several answers to the same question, simply compare the numbers - the best answer will have the highest number. You can also read more about the architecture and model training in the article [].

The src folder contains the notebooks used in the development.


Example of usage:

	model = PerceptionEstimator(device="cuda")
	question = """
	I think what I want to do is a fairly common task but I've found no reference on the web.
	I have text with punctuation, and I want a list of the words.
	"Hey, you - what are you doing here!?"
	should be
	<code>['hey', 'you', 'what', 'are', 'you', 'doing', 'here']</code>
	But Python's <code>str.split()<\code> only works with one argument, so I have all words with the punctuation after I split with whitespace. Any ideas?
	"""

	ans_1 = """
	<code>
	re.split()
	re.split(pattern, string[, maxsplit=0])
	</code>
	Split string by the occurrences of pattern.
	If capturing parentheses are used in pattern, then the text of all groups in the pattern are also returned as part of the resulting list. If maxsplit is nonzero, at most maxsplit splits occur, and the remainder of the string is returned as the final element of the list. (Incompatibility note: in the original Python 1.5 release, maxsplit was ignored. This has been fixed in later releases.)
	<code>
	>>> re.split('\W+', 'Words, words, words.')
	['Words', 'words', 'words', '']
	>>> re.split('(\W+)', 'Words, words, words.')
	['Words', ', ', 'words', ', ', 'words', '.', '']
	>>> re.split('\W+', 'Words, words, words.', 1)
	['Words', 'words, words.']
	</code>
	"""
	print(model.predict(question, ans_1))

    ans_2 = """
	A case where regular expressions are justified:
	<code>
	import re
	DATA = "Hey, you - what are you doing here!?"
	print(re.findall(r"[\w']+", DATA))
	# Prints ['Hey', 'you', 'what', 'are', 'you', 'doing', 'here']
	</code>
	"""
	print(model.predict(question, ans_2))

    ans_3 = """
	Another quick way to do this without a regexp is to replace the characters first, as below:
	<code>
	>>> 'a;bcd,ef g'.replace(';',' ').replace(',',' ').split()
	['a', 'bcd', 'ef', 'g']
	</code>
	"""
	print(model.predict(question, ans_3))

    ans_4 = """
	So many answers, yet I can't find any solution that does efficiently what the title of the questions literally asks for (splitting on multiple possible separators—instead, many answers split on anything that is not a word, which is different). So here is an answer to the question in the title, that relies on Python's standard and efficient re module:
	<code>
    >>> import re  # Will be splitting on: , <space> - ! ? :
	>>> filter(None, re.split("[, \-!?:]+", "Hey, you - what are you doing here!?"))
	['Hey', 'you', 'what', 'are', 'you', 'doing', 'here']
	</code>
	where:
	
	the […] matches one of the separators listed inside,
	the \- in the regular expression is here to prevent the special interpretation of - as a character range indicator (as in A-Z),
	the + skips one or more delimiters (it could be omitted thanks to the <code>filter()</code>, but this would unnecessarily produce empty strings between matched single-character separators), and
	<code>filter(None, …)</code> removes the empty strings possibly created by leading and trailing separators (since empty strings have a false boolean value).
	This <code>re.split()</code> precisely "splits with multiple separators", as asked for in the question title.
	
	This solution is furthermore immune to the problems with non-ASCII characters in words found in some other solutions (see the first comment to ghostdog74's answer).
	
	The re module is much more efficient (in speed and concision) than doing Python loops and tests "by hand"!
	"""
	print(model.predict(question, ans_4))

    ans_5 = """
	got same problem as @ooboo and find this topic @ghostdog74 inspired me, maybe someone finds my solution usefull
	<code>
	str1='adj:sg:nom:m1.m2.m3:pos'
	splitat=':.'
	''.join([ s if s not in splitat else ' ' for s in str1]).split()
	</code>
	input something in space place and split using same character if you dont want to split at spaces.
	"""
	print(model.predict(question, ans_5))

    ans_6 = """
	<code>
	def get_words(s):
	    l = []
	    w = ''
	    for c in s.lower():
	        if c in '-!?,. ':
	            if w != '': 
	                l.append(w)
	            w = ''
	        else:
	            w = w + c
	    if w != '': 
	        l.append(w)
	    return l
	</code>
	Here is the usage:
	<code>
	>>> s = "Hey, you - what are you doing here!?"
	>>> print get_words(s)
	['hey', 'you', 'what', 'are', 'you', 'doing', 'here']
	</code>
	"""
	print(model.predict(question, ans_6))

 
 
Output:

	0.5961810350418091
    0.5296962857246399
    0.5159586668014526
    0.7474482059478760
    0.3136502802371979
    0.1201618462800980