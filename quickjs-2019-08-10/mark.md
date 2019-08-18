# notebook

## js token parse

__JS_EvalInternal

js_parse_program

free_token next_token

js_parse_directives

js_parse_source_element  js_parse_statement_or_decl

## others

dump_token

JSAtom

<https://twitter.com/guybedford/status/1149793982974713863>

I made a first scan of the QuickJS source code today, and it's a real joy to read. It has a very interesting approach though - there is no AST construction at all. I'm no JS engine expert, but thought I'd share my rough understanding of it...

Instead of constructing an AST, the parser directly emits scopes, variables, functions and operations as it is doing the tokenizing. This saves the memory overhead of constructing AST nodes, and improves perf in getting as much work done in that first pass.

Variables are emitted into the scopes as expected, while functions are emitted as consisting of a series of OPCODEs, effectively a custom JS machine code. So `await X` becomes an OP_await, an `if` statement gets emitted as a type of GOTO operation in this function machine code.

The process of JS evaluation is then running what is effectively a custom JS stack execution language, and this then provides the basis of the ability to easily compile JS to native (qjsc) as well.

I haven't dug into how it does object shape analysis yet, but thought I'd share what I found interesting about it. I did find a bug to do with the way it's doing early scope population though, but without an issue queue I will keep it to myself :)

I really hope this project can be a seed for lots of new interesting work here. Personally I'm interested to see if it might be possible turn its execution engine into a JS optimization engine, although it's yet to be seen if that will fit the model.
