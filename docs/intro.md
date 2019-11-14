# Introduction

This guide covers writing Rust code at PingCAP and in the TiKV project.
It should expand later to include Go and the other language we use.
The [general advice](general.md) section covers some general principles which apply in any language.

At the highest level, some guidance on writing clear, high quality code is:

* be consistent with surrounding code and other code in the ecosystem (in that order of priority).
* Code is read more often than it is written; write code that is easy to read.
* Avoid surprises for the reader, or code which is misleading in what it does.
* Simple, straightforward code is better than complex, 'clever' code.

These principles influence the writing in this guide.
In addition, we have tried to follow existing conventions if they exist and to try to justify all guidelines.
Some guidelines are a bit arbitrary - sometimes it is more important do something consistently than which particular thing is done.

Another reason to provide rationale for the guidelines is to help you understand the intention behind the guidelines.
It is more important to stick to the spirit of the guide, rather than adhering to small details.
Don't use the guide to be too pedantic, especially in code review.

We've assumed you can use standard tools such as Gofmt and Rustfmt.
Where guidelines can be enforced mechanically with such tools, they are only briefly discussed.
However, we don't assume people reading or writing the code will be using advanced tools such as IDEs.
The rationale is that some people prefer not to, sometimes code is read outside of tools (e.g., on the web), and making code easy to follow without advanced tools usually makes code which is easier for those tools to interpret.


## Terminology

We use the following terminology in this guide:

'Must', 'always': you should always follow these guidelines.
There might be very exceptional circumstances where this is not possible.

'Should', 'prefer': you should follow these guidelines where it is sensible.
We expect that will be a large majority of cases, but if there is a conflict with other guidelines or consistency with existing code, use your judgement.

'Don't' and 'never' are the negative equivalent of 'must' and 'always'.
'avoid' is the negative equivalent of 'prefer'.

'Consider': such a guideline will often be useful, but should be just one factor in deciding how to write the code.

'Rationale': these are not guidelines, but attempt to explain why we chose a guideline.
These sections are non-normative.
