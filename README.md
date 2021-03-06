DPP - Diagram preprocessor (with pandoc in mind)
================================================

Initially, the [PP](http://cdsoft.fr/pp "PP - Generic Preprocessor (for Pandoc)") package contained three preprocessors for [Pandoc](http://pandoc.org/).

I started using Markdown and [Pandoc](http://pandoc.org/) with [GPP](http://en.nothingisreal.com/wiki/GPP). Then I wrote [DPP](http://cdsoft.fr/pp "DPP - Diagram Preprocessor (for Pandoc)") to embed diagrams in Markdown documents. And finally [PP](http://cdsoft.fr/pp "PP - Generic Preprocessor (for Pandoc)") which merges the functionalities of [GPP](http://en.nothingisreal.com/wiki/GPP) and [DPP](http://cdsoft.fr/pp "DPP - Diagram Preprocessor (for Pandoc)").

[GPP](http://en.nothingisreal.com/wiki/GPP) and [DPP](http://cdsoft.fr/pp "DPP - Diagram Preprocessor (for Pandoc)") are not included anymore in [PP](http://cdsoft.fr/pp "PP - Generic Preprocessor (for Pandoc)") as `pp` can be used standalone.

[DPP](http://cdsoft.fr/pp "DPP - Diagram Preprocessor (for Pandoc)") just contains `dpp` itself as well as [GPP](http://en.nothingisreal.com/wiki/GPP).

`dpp` is obsolete, please consider using [PP](http://cdsoft.fr/pp "PP - Generic Preprocessor (for Pandoc)") instead.

Open source
===========

[DPP](http://cdsoft.fr/pp "DPP - Diagram Preprocessor (for Pandoc)") is an Open source software. Any body can contribute on [GitHub](https://github.com/CDSoft/dpp) to:

-   suggest or add new functionalities
-   report or fix bugs
-   improve the documentation
-   add some nicer examples
-   find new usages
-   ...

Installation
============

1.  Download and extract [dpp.tgz](http://cdsoft.fr/dpp/dpp.tgz).
2.  Run `make`.
3.  Copy `dpp` and `gpp` (`.exe` files on Windows) where you want.

`dpp` require [Graphviz](http://graphviz.org/) and Java ([PlantUML](http://plantuml.sourceforge.net/) and [ditaa](http://ditaa.sourceforge.net/) are embedded in `dpp`).

Usage
=====

`dpp` is a filter and has no options. It takes some text with embedded diagrams on `stdin` and generates a text with image links on `stdout`. Some error messages may be written to `stderr`.

![](doc/img/dpp-pipe1.png)

Being a filter, `dpp` can be chained with other preprocessors. Another good generic purpose preprocessor is `pp` or `gpp`.

A classical usage of `dpp` along with `gpp` and [Pandoc](http://pandoc.org/) is:

![](doc/img/dpp-pipe2.png)

For instance, on any good Unix like system, you can use this command:

``` bash
$ gpp documents... | dpp | pandoc -f markdown -t html5 -o document.html
```

Design
------

`dpp` was initially a preprocessor for [GraphViz](http://graphviz.org/) diagrams. It now also comes with [PlantUML](http://plantuml.sourceforge.net/), [ditaa](http://ditaa.sourceforge.net/) and scripting capabilities. `dpp` requires [GraphViz](http://graphviz.org/) and Java to be installed, [PlantUML](http://plantuml.sourceforge.net/) and [ditaa](http://ditaa.sourceforge.net/) are embedded in `dpp`.

Optionally, `dpp` can call [Bash](https://www.gnu.org/software/bash/), [Bat](https://en.wikipedia.org/wiki/Cmd.exe), [Python](https://www.python.org/) or [Haskell](https://www.haskell.org/) to execute general scripts.

![](doc/img/dpp-design.png)

![](doc/img/dpp-design2.png)

Syntax
======

Diagrams
--------

Diagrams are written in code blocks. The first line contains:

-   the diagram generator
-   the image name (without the extension)
-   the legend (optional)

Block delimiters are made of three or more tilda or back quotes, at the beginning of the line (no space and no tab). Both lines must have the same number of tilda or back quotes.

    ~~~~~ dot path/imagename optional legend
    graph {
        "source code of the diagram"
    }
    ~~~~~

This extremely meaningful diagram is rendered as `path/imagename.png` and looks like:

![optional legend](doc/img/dpp-syntax.png)

The image link in the output markdown document may have to be different than the actual path in the file system. This happens when then `.md` or `.html` files are not generated in the same path than the source document. Brackets can be used to specify the part of the path that belongs to the generated image but not to the link in the output document. For instance a diagram declared as:

    ~~~~~ dot [mybuildpath/]img/diag42
    ...
    ~~~~~

will be actually generated in:

    mybuildpath/img/diag42.png

and the link in the output document will be:

    img/diag42.png

For instance, if you use Pandoc to generate HTML documents with diagrams in a different directory, there are two possibilities:

1.  the document is a self contained HTML file (option `--self-contained`), i.e. the CSS and images are stored inside the document:
    -   the CSS path shall be the actual path where the CSS file is stored
    -   the image path in diagrams shall be the actual path where the images are stored (otherwise Pandoc won't find them)
    -   e.g.: `outputpath/img/diag42`

2.  the document is not self contained, i.e. the CSS and images are stored apart from the document:
    -   the CSS path shall be relative to the output document
    -   the image path in diagrams shall be relative to output document in HTML links and shall also describe the actual path where the images are stored.
    -   e.g.: `[outputpath/]img/diag42`

The diagram generator can be:

-   dot
-   neato
-   twopi
-   circo
-   fdp
-   sfdp
-   patchwork
-   osage
-   uml
-   ditaa

`dpp` will not create any directory, the path where the image is written must already exist.

![](doc/img/dpp-generators.png)

Scripts
-------

Scripts are also written in code blocks. The first line contains only the kind of script.

    ~~~~~ bash
    echo Hello World!
    ~~~~~

With no surprise, this script generates:

    Hello World!

The script language can be:

-   bash (or sh)
-   bat (DOS/Windows batch language)
-   python
-   haskell

`dpp` will create a temporary script before calling the associated interpretor.

![](doc/img/dpp-scripts.png)

Verbatim copy
-------------

Blocks can also contain verbatim text that is preserved in the output.

    `````````` quote
    ~~~ bash
    # this bash script example won't be executed!
    # but only colorized by Pandoc.
    ~~~
    ``````````

becomes

``` bash
# this bash script example won't be executed!
# but only colorized by Pandoc.
```

Examples
========

The [source code](dpp.md) of this document contains some diagrams.

Here are some simple examples. For further details about diagrams' syntax, please read the documentation of [GraphViz](http://graphviz.org/), [PlantUML](http://plantuml.sourceforge.net/) and [ditaa](http://ditaa.sourceforge.net/).

Graphviz
--------

[GraphViz](http://graphviz.org/) is executed when one of these keywords is used: `dot`, `neato`, `twopi`, `circo`, `fdp`, `sfdp`, `patchwork`, `osage`

    ~~~~~ twopi doc/img/dpp-graphviz-example This is just a GraphViz diagram example
    digraph {
        O -> A
        O -> B
        O -> C
        O -> D
        D -> O
        A -> B
        B -> C
        C -> A
    }
    ~~~~~

-   `twopi` is the kind of graph (possible graph types: `dot`, `neato`, `twopi`, `circo`, `fdp`, `sfdp`, `patchwork`).
-   `doc/img/dpp-graphviz-example` is the name of the image. `dpp` will generate `doc/img/dpp-graphviz-example.dot` and `doc/img/dpp-graphviz-example.png`.
-   the rest of the first line is the legend of the graph.
-   other lines are written to `doc/img/dpp-graphviz-example.dot` before running [Graphviz](http://graphviz.org/).

You can use `dpp` in a pipe before [Pandoc](http://pandoc.org/) (as well as `pp` or `gpp`) for instance):

``` bash
pp file.md | dpp | pandoc -s -S --self-contained -f markdown -t html5 -o file.html

or

cat file.md | gpp -T -x | dpp | pandoc -s -S --self-contained -f markdown -t html5 -o file.html
```

Once generated the graph looks like:

![This is just a GraphViz diagram example](doc/img/dpp-graphviz-example.png)

[GraphViz](http://graphviz.org/) must be installed.

PlantUML
--------

[PlantUML](http://plantuml.sourceforge.net/) is executed when the keyword `uml` is used. The lines `startuml` and `enduml` required by [PlantUML](http://plantuml.sourceforge.net/) are added by `dpp`.

    ~~~~~ uml doc/img/dpp-plantuml-example This is just a PlantUML diagram example
    Alice -> Bob: Authentication Request
    Bob --> Alice: Authentication Response
    Alice -> Bob: Another authentication Request
    Alice <-- Bob: another authentication Response
    ~~~~~

Once generated the graph looks like:

![This is just a PlantUML diagram example](doc/img/dpp-plantuml-example.png)

[PlantUML](http://plantuml.sourceforge.net) is written in Java and is embedded in `dpp`. Java must be installed.

Ditaa
-----

[ditaa](http://ditaa.sourceforge.net/) is executed when the keyword `ditaa` is used.

    ~~~~~ ditaa doc/img/dpp-ditaa-example This is just a Ditaa diagram example
        +--------+   +-------+    +-------+
        |        | --+ ditaa +--> |       |
        |  Text  |   +-------+    |diagram|
        |Document|   |!magic!|    |       |
        |     {d}|   |       |    |       |
        +---+----+   +-------+    +-------+
            :                         ^
            |       Lots of work      |
            +-------------------------+
    ~~~~~

Once generated the graph looks like:

![This is just a Ditaa diagram example](doc/img/dpp-ditaa-example.png)

[ditaa](http://plantuml.sourceforge.net) is written in Java and is embedded in `dpp`. Java must be installed.

Bash
----

[Bash](https://www.gnu.org/software/bash/) is executed when the keyword `bash` is used.

    ~~~~~ bash
    echo "Hi, I'm $SHELL $BASH_VERSION"
    RANDOM=42 # seed
    echo "Here are a few random numbers: $RANDOM, $RANDOM, $RANDOM"
    ~~~~~

This script outputs:

    Hi, I'm /bin/bash 4.3.30(1)-release
    Here are a few random numbers: 17766, 11151, 23481

**Note**: the keyword `sh` executes `sh` which is generally a link to `bash`.

Bat
---

[Bat](https://en.wikipedia.org/wiki/Cmd.exe) is executed when the keyword `bat` is used.

    ~~~~~ bat
    echo Hi, I'm %COMSPEC%
    ver
    if not "%WINELOADER%" == "" (
        echo This script is run from wine under Linux
    ) else (
        echo This script is run from a real Windows
    )
    ~~~~~

This script outputs:

    Hi, I'm C:\windows\system32\cmd.exe

    Wine CMD version 5.1.2600 (1.6.2)
    This script is run from wine under Linux

Python
------

[Python](https://www.python.org/) is executed when the keyword `python` is used.

    ~~~~~ python
    import sys
    import random

    if __name__ == "__main__":
        print("Hi, I'm Python %s"%sys.version)
        random.seed(42)
        randoms = [random.randint(0, 1000) for i in range(3)]
        print("Here are a few random numbers: %s"%(", ".join(map(str, randoms))))
    ~~~~~

This script outputs:

    Hi, I'm Python 2.7.9 (default, Mar  1 2015, 12:57:24) 
    [GCC 4.9.2]
    Here are a few random numbers: 640, 25, 275

Haskell
-------

[Haskell](https://www.haskell.org/) is executed when the keyword `haskell` is used.

    ~~~~~ haskell
    import System.Info
    import Data.Version
    import Data.List

    primes = filterPrime [2..]
        where filterPrime (p:xs) =
                p : filterPrime [x | x <- xs, x `mod` p /= 0]

    version = showVersion compilerVersion
    main = do
        putStrLn $ "Hi, I'm Haskell " ++ version
        putStrLn $ "The first 10 prime numbers are: " ++
                    intercalate " " (map show (take 10 primes))
    ~~~~~

This script outputs:

    Hi, I'm Haskell 7.10
    The first 10 prime numbers are: 2 3 5 7 11 13 17 19 23 29

Licenses
========

PP/DPP
------

Copyright (C) 2015, 2016 Christophe Delord <br> <http://www.cdsoft.fr/dpp>

DPP is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

DPP is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with DPP. If not, see <http://www.gnu.org/licenses/>.

PlantUML
--------

PlantUML.jar is integrated in [DPP](http://cdsoft.fr/pp "DPP - Diagram Preprocessor (for Pandoc)"). [PlantUML](http://plantuml.sourceforge.net/) is distributed under the [GPL license](http://www.gnu.org/copyleft/gpl.html). See <http://plantuml.sourceforge.net/faq.html>.

ditaa
-----

ditaa.jar is integrated in [DPP](http://cdsoft.fr/pp "DPP - Diagram Preprocessor (for Pandoc)"). [ditaa](http://ditaa.sourceforge.net/) is distributed under the [GNU General Public License version 2.0 (GPLv2)](http://sourceforge.net/directory/license:gpl/). See <http://sourceforge.net/projects/ditaa/>.

GPP
---

[GPP](http://en.nothingisreal.com/wiki/GPP) is included in the binary distribution of DPP. I have just recompiled the original sources of [GPP](http://en.nothingisreal.com/wiki/GPP).

GPP was written by Denis Auroux <aurouxmath.mit.edu>. Since version 2.12 it has been maintained by Tristan Miller <psychonautnothingisreal.com>.

Copyright (C) 1996-2001 Denis Auroux.<br> Copyright (C) 2003, 2004 Tristan Miller.

Permission is granted to anyone to make or distribute verbatim copies of this document as received, in any medium, provided that the copyright notice and this permission notice are preserved, thus giving the recipient permission to redistribute in turn.

Permission is granted to distribute modified versions of this document, or of portions of it, under the above conditions, provided also that they carry prominent notices stating who last changed them.

Feedback
========

Your feedback and contributions are welcome. You can contact me at <http://cdsoft.fr>
