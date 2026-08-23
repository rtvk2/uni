build walkthroughs:
https://www.youtube.com/watch?v=YMEHrXSsdo0
https://www.youtube.com/watch?v=yTR00r8vBH8&t=1514s
unix processes in c: 
https://www.youtube.com/playlist?list=PLfqABt5AS4FkW5mOn2Tn9ZZLLDwA3kZUY
texts:
https://craftinginterpreters.com/contents.html (in java, can skip)
https://medium.com/@santiagobedoa/coding-a-shell-using-c-1ea939f10e7e
https://medium.com/@WinnieNgina/guide-to-code-a-simple-shell-in-c-bd4a3a4c41cd
https://meerthika.medium.com/building-a-shell-in-c-understanding-fork-pipes-and-file-descriptors-fc030ca7549d

cfg -> rdt -> ast

tried to implement AST as it would simplify tokenizing, parsing and executing.
design: lexer → parser → AST → executor

raw input string → LEXER → array of tokens → PARSER → AST → EXECUTOR → runs it 
so smth like: "ls -l | grep txt" -> [WORD("ls"), WORD("-l"), PIPE, WORD("grep"), WORD("txt")]

we need to now define the grammar for our shell. this can be implemented using Backus-Naur Form (BNF), Extended Backus-Naur Form, Parsing Expression Grammar (PEG), Augmented Backus-Naur Form, Syntax Diagrams (like Railroad Diagrams). 

the most straightfoward one is BNF with the following syntax:
	`<thing> ::= <option A> | <option B> ; ::= means "is defined as"
	
PEG is better here as it disallows left-recursion by default and also lets us use a recursive decision tree directly.

```
command_line <- job '&'?

job          <- command ('|' command)*

command      <- simple_command (('<' / '>') word)*

simple_command <- word+
```

command_line ::= job | job '&' 
job ::= command | job '|' command 
command ::= simple_command 
			| command '<' word 
			| command '>' word 
simple_command ::= word 
				| simple_command word



spec1

initially set up 
then added guard rails for segfaults


spec2





spec3





spec4






spec5
