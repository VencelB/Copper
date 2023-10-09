# Copper
Welcome to the Copper, module for estimation of code perception!
This model will allow you to quickly and easily assess the human-perceivable quality of answers to questions related to Python development.

To start using the model, just copy the code from the pipeline notebook and download the corresponding files.
The src folder contains the notebooks used in the development.
You can also read more about the architecture and model training in the article [].

Example of usage:

	model = PerceptionEstimator(device="cuda")
	question = """
	I think what I want to do is a fairly common task but I've found no reference on the web.
	I have text with punctuation, and I want a list of the words.

	"Hey, you - what are you doing here!?"
	should be

	<code>['hey', 'you', 'what', 'are', 'you', 'doing', 'here']<\code>
	But Python's <code>str.split()<\code> only works with one argument, so I have all words with the punctuation after I split with whitespace. Any ideas?
	"""

	answer = """
	<code>
	re.split()
	re.split(pattern, string[, maxsplit=0])
	<\code>
	Split string by the occurrences of pattern.
	If capturing parentheses are used in pattern, then the text of all groups in the pattern are also returned as part of the resulting list. If maxsplit is nonzero, at most maxsplit splits occur, and the remainder of the string is returned as the final element of the list. (Incompatibility note: in the original Python 1.5 release, maxsplit was ignored. This has been fixed in later releases.)
	<code>
	>>> re.split('\W+', 'Words, words, words.')
	['Words', 'words', 'words', '']
	>>> re.split('(\W+)', 'Words, words, words.')
	['Words', ', ', 'words', ', ', 'words', '.', '']
	>>> re.split('\W+', 'Words, words, words.', 1)
	['Words', 'words, words.']
	<\code>
	"""
	print(model.predict(question, answer))
Output:

	0.6481162905693054
