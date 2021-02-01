# SRFI nnn: Emacs command set

by Firstname Lastname, Another Person, Third Person

# Status

Early Draft

# Abstract

TODO

# Issues

* Find out the history of which people designed the command set and
when.

# Rationale

GNU Emacs is an extremely popular text editor among Lisp and Scheme
programmers. Its extension language, Emacs Lisp, offers a command
set for text editing that eventually learned by most power users of
Emacs. This command set translates easily to other Lisp dialects,
including Scheme, provided that a text buffer datatype is supplied.

Emacs gives a name to each buffer, and all the buffers live together
in one global namespace. This SRFI does not supply such a namespace,
and hence does not deal with buffer names either.

# Specification

## Parameters

Parameter (current-buffer [buffer]) -> buffer

Parameter (case-fold-search [boolean]) -> boolean

## Buffer management

(buffer? object) -> boolean

(make-buffer) -> buffer

Syntax (with-current-buffer buffer body ...)

Syntax (with-temp-buffer body ...)

## Buffer positions

(point-min) -> integer

(point-max) -> integer

(point) -> integer

(mark) -> integer

(set-mark! integer)

## Narrowing

(narrow-to-region start end)

(widen)

## Saving buffer settings

Syntax (save-excursion body ...)

Syntax (save-restriction body ...)

## Text editing

(buffer-string) -> string

(buffer-substring start end) -> string

(insert . args)

## Substring search

(search-forward string [bound no-error? count]) -> integer

(search-backward string [bound no-error? count]) -> integer

## Regular expression search

(re-search-forward regexp [bound no-error? count]) -> integer

(re-search-backward regexp [bound no-error? count]) -> integer

(looking-at regexp) -> boolean

## Search match data

(match-data) -> list

This is not a parameter to avoid incompatibility with the Emacs Lisp
API where `match-data` takes optional arguments unrelated to setting
the match data.

(set-match-data! list)

(match-beginning integer) -> integer or #f

(match-end integer) -> integer or #f

(match-string integer) -> string

# Implementation

# Acknowledgements

TODO: Thank the people who designed the command set.

# References

# Copyright

Copyright (C) Firstname Lastname (20XY).

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
