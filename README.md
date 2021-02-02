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

GNU Emacs is a text editor that is extremely popular among Lisp and
Scheme programmers. Its extension language, Emacs Lisp, offers a
command set for text editing that is eventually learned by most power
users of Emacs. This command set translates easily to other Lisp
dialects, including Scheme, provided that a datatype is supplied for a
text buffer that can grow and shrink.

## Buffer names

GNU Emacs endows each buffer with a name. That name is displayed to
the user, and uniquely identifies the buffer in the global namespace
of all buffers.

This SRFI does not deal with buffer names. Emacs has many functions
that take either a buffer object or a buffer name. In this SRFI, such
functions do not accept a buffer name; a buffer object must be given.

This implies that `get-buffer`, `get-buffer-create`,
`generate-new-buffer` do not exist here.

## Zero-based vs one-based indexing

Emacs buffer positions are 1-based, not 0-based. This is inconsistent
with string, vector, and list indexes, which are 0-based in Emacs Lisp
just as they are in Scheme and other Lisp dialects.

For consistency with the rest of Scheme, this SRFI deviated from Emacs
indexing and uses 0-based indexes for buffer positions just as Scheme
does for strings, vectors, and lists. We can do this because Emacs
Lisp code does not require hard-coded buffer positions. `(point-min)`,
`(point-max)`, `(point-at-bol)`, `(point-at-eol)`, and various search
functions are used to obtain buffer positions instead.

Emacs buffers' column numbers are 0-based at the Emacs Lisp API level.
However, line numbers are 1-based.

## Macros, parameters, procedures that take thunks

Emacs Lisp makes heavy use of macros, perhaps owing to the fact that
for the longest time, that language used dynamic scope instead of
lexical scope. Macros let one use gensyms to avoid variable capture;
with dynamic scope, protecting non-gensym variable names is not easy.

Emacs Lisp's dynamic scope allows for global variables like
`case-fold-search` to be bound using ordinary `let`. Scheme uses
lexical scoping only, and dynamic scoping is done via _parameter
objects_ (see SRFI 39). Therefore, this SRFI implements
`case-fold-search` as a parameter. The user needs to use
`parameterize` instead of `let` to bind it.

## Optional arguments and nil values

The Emacs Lisp language supports optional arguments to functions.
Emacs makes heavy use of this feature: countless library functions
take optional arguments.

When a function call does not supply an optional argument, Emacs Lisp
always fills in the value `nil`. The called function cannot
distinguish between a supplied `nil` value and a missing argument.

This SRFI uses optional arguments wherever Emacs Lisp does. The Scheme
value `#f` is the moral equivalent of the Emacs Lisp value `nil`, so
this SRFI treats an `#f` value passed to an optional argument the same
way as Emacs Lisp treats a `nil` value passed.

Wherever this SRFI gives two or more optional arguments like `[start
end]`, it means that all of them are optional individually. Thus
supplying both _start_ and _end_, supplying only _start_, and
suppyling neither, are all valid combinations.

## Narrowing and marking

An Emacs buffer can be _narrowed_ so that only one contiguous part of
the buffer is visible for editing. Narrowing also affects search and
cursor movement commands in Emacs Lisp, limiting them to the visible
region.

The Emacs Lisp documentation for the `set-mark` function says:

> Novice Emacs Lisp programmers often try to use the mark for the
> wrong purposes. The mark saves a location for the user’s
> convenience. Most editing commands should not alter the mark. To
> remember a location for internal use in the Lisp program, store it
> in a Lisp variable.

While narrowing and the mark ring can be used in Emacs Lisp
programming, they are not that useful since the same effect can be
achieved by remembering region boundaries in ordinary Lisp variables,
and giving those values as boundary arguments to search functions. For
that reason, narrowing and marking commands have been left out of this
SRFI.

Omitted narrowing procedures:

* `narrow-to-region`
* `save-restriction` [syntax]
* `widen`

Omitted marking procedures:

* `mark`
* `pop-mark`
* `push-mark`
* `set-mark`

## Regular expressions

For historical reasons, GNU Emacs uses an idiosyncratic regular
expression syntax that is not found in any other software. The syntax
is mostly the same as the common Perl-compatible regular expressions,
but has arbitrary discrepancies in backslash quoting: for example, an
unquoted `(` matches a literal opening parenthesis, whereas a quoted
`\(` begins a match group; this convention is the opposite of the
usual Perl-compatible syntax.

This SRFI uses the syntax from SRFI 115 (_Scheme Regular Expressions_)
instead of the idiosyncratic Emacs Lisp syntax.

## Text properties, overlays, and extents

GNU Emacs has two systems for attaching style information and other
metadata to regions of text in a buffer. These systems are called
_text properties_ and _overlays_. The now-extant GNU Emacs fork,
XEmacs, had a single unified _extents_ system for the same job.

This SRFI does not implement any of those three APIs. A future SRFI
may extend this SRFI with such support.

## Windows and frames

An Emacs buffer can be shown to the user on the screen in more than
one window at a time. Each window keeps its own cursor position.
Multiple windows can be stacked into one frame.

Since this SRFI does not deal with the user interface aspects of
Emacs, we don't deal with the concepts of window and frame.

* window
* frame
* echo area
* minibuffer
* keymap
* kill ring, killing and yanking
* buffer window position
* buffer visiting file
* syntax tables and syntax procedures

The `current-column` procedure is not provided by this SRFI since it
returns the display position.

The `forward-line` procedure is not provided. It can be simulated
using `goto-char` and `point-at-eol`.

The `delete-blank-lines` procedure is not provided because it has
complicated context-sensitive behavior better suited toward
interactive use than proramming.

The `delete-duplicate-lines` procedure is not provided because it has
complicated behavior: by default, it searches duplicate lines an
arbitrary distance apart, not only adjacent duplicates.

## Undo feature

GNU Emacs supports an unlimited _undo_ list, and with a third-party
extension, even an undo tree. This SRFI does not have an _undo_
feature.

## Character encoding

GNU Emacs can transcode text from one character encoding to another
(it calls them _coding systems_), convert between different newline
encodings, and also deal with raw binary data.

## String library

Emacs Lisp uses `concat` to concatenate strings. Scheme already has
`string-append` for this purpose.

Emacs Lisp has a `format` that uses C-style `%` directives. Many SRFIs
and Scheme libraries provide formatting so this one does not.

Emacs Lisp `substring` is identical to Scheme.

Emacs Lisp `string-join` is a compatible subset of SRFI 13.

Emacs Lisp `string-trim` and `string-trim-left` and
`string-trim-right`.

Emacs Lisp `string-remove-prefix` and `string-remove-suffix`.

Emacs Lisp `string-blank-p`. `string-empty-p` is equivalent to
`string-null` in SRFI 13.

`number-to-string` is `number->string` in Scheme.

`string-to-list` and `string-to-vector`

`replace-regexp-in-string` has an equivalent in SRFI 115

# Specification

## Buffer management

(buffer? _object_) -> _boolean_

(copy-buffer _buffer_) -> _new-buffer_

Return a new buffer whose state is equal to the state of _buffer_.
However, after creation, the new buffer's contents and other state are
completely separate from _buffer_: mutating the new buffer does not
mutate _buffer_ and vice versa.

(make-buffer) -> _buffer_

Return a new buffer whose contents are empty.

(set-buffer! [buffer])

Called `set-buffer` without an exclamation mark in Emacs Lisp.

Do not use.

(current-buffer [buffer]) -> buffer [parameter]

(call-with-save-current-buffer proc)

(call-with-current-buffer buffer proc)

(call-with-temp-buffer buffer proc)

(save-current-buffer body ...) [syntax]

(with-current-buffer buffer body ...) [syntax]

(with-temp-buffer body ...) [syntax]

## Buffer as port

(with-input-from-buffer buffer proc)

(with-output-to-buffer buffer proc)

## Buffer positions

(buffer-size [buffer]) -> integer

Return the number of characters in the current buffer.

(point) -> integer

Return value of point, as a 0-based integer.

(point-min) -> integer

Returns the minimum permissible value of point in the current buffer.

Since this SRFI does not support narrowing, always returns 0.

(point-max) -> integer

Returns the maximum permissible value of point in the current buffer.
Note that since text can be inserted at the end of the buffer, this
value is always one beyond the last character in the buffer.

Since this SRFI does not support narrowing, always returns `(1+
(buffer-size))`.

## Lines and columns

(line-number-at-pos [_pos_]) -> _integer_

Return buffer line number at position _pos_.
If _pos_ is `#f` or not supplied, use current buffer location.

(point-at-bol) -> _integer_

(point-at-eol) -> _integer_

(sort-fold-case [_boolean_]) -> _boolean_ [parameter]

(sort-lines _reverse?_ _start_ _end_)

Sort lines in region.

If _sort-fold-case_ is `#f` lines are compared as if by `string<?` and
`string=?`.

If _sort-fold-case_ is `#t` lines are compared as if by `string-ci<?`
and `string-ci=?`.

If _reverse?_ if `#t` swap the sense of the comparison.

Use a stable sort in all cases.

## Whitespace

(tab-width [_integer_]) -> _integer_ [parameter]

Maximum width of a tab in columns.

An ASCII horizontal tab character causes the column number to increase
first by 1 and then until it is a multiple of the tab width.

It is an error unless _integer_ is a **positive** exact integer.

(tabify _start_ _end_)

Convert multiple spaces in region to tabs when possible. A group of
spaces is partially replaced by tabs when this can be done without
changing the column they end at. If called interactively with prefix
ARG, convert for the entire buffer.

(untabify _start_ _end_)

Take the region of the current buffer bounded by [_start_, _end_[ and
convert all ASCII horizontal tab characters in it to the equivalent
number of spaces, respecting the current value of _tab-width_.

(delete-trailing-whitespace [start end])



## Saving buffer settings

(call-with-save-excursion proc)

Syntax (save-excursion body ...)

## Text editing

(buffer-substring _start_ _end_) -> string

Copy the region of _buffer_ bounded by [_start_, _end_[ and return it
as an ordinary Scheme string.

(buffer-string) -> string

Same as `(buffer-substring (point-min) (point-max))`.

(insert _args_ ...)

Insert the arguments, either strings or characters, in the current
buffer at point.

Point moves forward to end up after the inserted text.

(insert-buffer-substring _buffer_ [_start_ _end_])

Copy the region of _buffer_ bounded by [_start_, _end_[ and insert it
into the current buffer at point.

If _buffer_ is the current buffer, the effect is like `(insert
(buffer-substring start end))`.

Upon return from this procedure, point is at the end of the inserted
region. If _buffer_ is not the current buffer, the value of point in
_buffer_ does not change.

If _start_ is `#f` or not supplied, it defaults to `(point-min)`.

If _end_ is `#f` or not supplied, it defaults to `(point-max)` in
_buffer_.

(insert-file-contents _filename_ [_ignored_ _start_ _end_])

It is an error to supply the argument _ignored_ with a value other
than `#f`. In Emacs Lisp, that argument controls whether the file is
"visited". This SRFI does not deal with visiting.

(write-region _start_ _end_ _filename_ [_append?_])

Write the region of the current buffer bounded by [_start_, _end_[ to
the file _filename_. Create the file if it doesn't exist.

* If _append?_ is `#t`, append to the file if it exists.
* If _append?_ is `#f`, overwrite the file if is exists.

It is an error to pass something other than a boolean to the `append?`
argument. In particular, GNU Emacs supports a number argument, which
makes it seek to that offset in the file before writing. This SRFI
does not support that.

(delete-region _start_ _end_)

Delete the region of the current buffer bounded by [_start_, _end_[.

* If _point_ lies before the deleted region, it does not change.
* If _point_ lies within the deleted region, it moves to _start_.
* If _point_ lies after the deleted region, it moves back by the
  length of the deleted region.

## Search state

(case-fold-search [boolean]) -> boolean [parameter]

(save-match-data body ...) [syntax]

(match-data) -> list

This is not a parameter to avoid incompatibility with the Emacs Lisp
API where `match-data` takes optional arguments unrelated to setting
the match data.

(set-match-data! list)

(match-beginning integer) -> integer or #f

(match-end integer) -> integer or #f

(match-string integer) -> string

(replace-match _new-string_ _fixed-case?_ _literal?_)

## Substring search

(search-forward string [bound no-error? count]) -> integer

(search-backward string [bound no-error? count]) -> integer

## Regular expression search

(re-search-forward _regexp_ [_bound_ _no-error?_ _count_]) -> _integer_

(re-search-backward _regexp_ [_bound_ _no-error?_ _count_]) -> _integer_

(looking-at _regexp_) -> _boolean_

(regexp-quote _string_) -> _string_

(regexp-opt _string-list_ _paren_) -> _string_

# Implementation

Implementation using Gauche's [`text.gap-buffer`
library](https://practical-scheme.net/gauche/man/gauche-refe/Gap-buffer.html)
is attached.

# Acknowledgements

TODO: Thank the people who designed the command set.

# References

[_The Craft of Text Editing_](https://www.finseth.com/craft/) by Craig
A. Finseth, especially the sections on the _buffer gap_ implementation
method.

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
