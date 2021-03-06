

#

## Citations

所有原始的文献库在 zotero 中, 安装 ZotFile 和 zotero-better-bibtex 插件.

zotero-better-bibtex 格式设为 `[auth:lower][year]`.

导出为 `Better bibtex` (不用 biblatex), 必要时用 Jabref 打开修改.

`\cite{Watterson1975}`: `Watterson 1975`
`\parencite{Watterson1975}`: `(Watterson 1975)`

## arara and latexindent

### arara for TexShop

* arara.engine

```bash
cp ~/Library/TeXShop/Engines/Inactive/Arara/arara.engine ~/Library/TeXShop/Engines/
chmod +x ~/Library/TeXShop/Engines/arara.engine
```

* Perl modules

```bash
#plenv global system
curl -L http://cpanmin.us | perl - --sudo App::cpanminus
sudo cpanm YAML::Tiny File::HomeDir
#plenv global 5.18.4
```

### `indent.yaml` for arara

```bash
# curl -O https://raw.githubusercontent.com/cmhughes/latexindent.pl/master/indent.yaml
# sed -i".bak" "s/indent.pl/indent/" indent.yaml

cat <<'EOF' > indent.yaml
!config
identifier: indent
name: Indent
command: <arara> @{ isWindows( "cmd /c latexindent.exe", "latexindent" ) } @{silent} @{trace} @{localSettings} @{cruft}@{ isNotEmpty( cruft, '="'.concat(parameters.cruft).concat('"') ) } @{overwrite}  @{onlyDefault} @{output} "@{file}" @{ isNotEmpty( output, '"'.concat(parameters.output).concat('"') ) }
arguments:
- identifier: overwrite
  flag: <arara> @{ isTrue( parameters.overwrite, "-w" ) }
- identifier: silent
  flag: <arara> @{ isTrue( parameters.silent, "-s" ) }
- identifier: trace
  flag: <arara> @{ isTrue( parameters.trace, "-t" ) }
- identifier: localSettings
  flag: <arara> @{ isTrue( parameters.localSettings, "-l" ) }
- identifier: output
  flag: <arara> @{ isNotEmpty( parameters.output, "-o" ) }
- identifier: onlyDefault
  flag: <arara> @{ isTrue( parameters.onlyDefault, "-d" ) }
- identifier: cruft
  flag: <arara> @{ isNotEmpty( parameters.cruft, "-c" ) }

EOF

sudo mv indent.yaml /usr/local/texlive/2016/texmf-dist/scripts/arara/rules/
```

### `defaultSettings.yaml` for latexindent

```bash
cat <<'EOF' > defaultSettings.yaml
defaultIndent: "    "
backupExtension: .bak
onlyOneBackUp: 1
maxNumberOfBackUps: 1
cycleThroughBackUps: 0
indentPreamble: 0
alwaysLookforSplitBraces: 1
alwaysLookforSplitBrackets: 1
removeTrailingWhitespace: 1

# environments that have tab delimiters, add more
# as needed
lookForAlignDelims:
   tabular:
      delims: 1
      alignDoubleBackSlash: 1
      spacesBeforeDoubleBackSlash: 2
   tabularx:
      delims: 1
   longtable: 1
   tabu: 1
   array: 1
   matrix: 1
   bmatrix: 1
   Bmatrix: 1
   pmatrix: 1
   vmatrix: 1
   Vmatrix: 1
   align: 1
   align*: 1
   alignat: 1
   alignat*: 1
   aligned: 1
   cases: 1
   dcases: 1
   listabla: 1

# if you have indent rules for particular environments
# or commands, put them in here; for example, you might just want
# to use a space " " or maybe a double tab "\t\t"
indentRules:
   myenvironment: "    "
   anotherenvironment: "        "
   chapter: " "
   section: " "
   item: "    "

#  verbatim environments- environments specified
#  in this hash table will not be changed at all!
verbatimEnvironments:
    verbatim: 1
    lstlisting: 1

#  no indent blocks (not necessarily verbatim
#  environments) which are marked as %\begin{noindent}
#  or anything else that the user puts in this hash
#  table
noIndentBlock:
    noindent: 1
    cmhtest: 1

# if you don't want to have additional indentation
# in an environment put it in this hash table; note that
# environments in this hash table will inherit
# the *current* level of indentation they just won't
# get any *additional*.
noAdditionalIndent:
    myexample: 1
    mydefinition: 1
    problem: 1
    exercises: 1
    mysolution: 1
    foreach: 0
    widepage: 1
    comment: 1
    \[: 0
    \]: 0
    document: 1
    frame: 0

# if you want to add indentation after
# a heading, such as \part, \chapter, etc
# then populate it in here - you can add
# an indent rule to indentRules if you would
# like something other than defaultIndent
#
# you can also change the level if you like,
# or add your own title command
indentAfterHeadings:
    part:
       indent: 0
       level: 1
    chapter:
       indent: 0
       level: 2
    section:
       indent: 0
       level: 3
    subsection:
       indent: 0
       level: 4
    subsection*:
       indent: 0
       level: 4
    subsubsection:
       indent: 0
       level: 5
    paragraph:
       indent: 0
       level: 6
    subparagraph:
       indent: 0
       level: 7

# if you want the script to look for \item commands
# and format it, as follows (for example),
#       \begin{itemize}
#           \item content here
#                 next line is indented
#                 next line is indented
#           \item another item
#       \end{itemize}
# then populate indentAfterItems. See also itemNames
indentAfterItems:
    itemize: 1
    enumerate: 1
    list: 1

# if you want to use other names for your items (such as, for example, part)
# then populate them here- note that you can trick latexindent.pl
# into indenting all kinds of commands (within environments specified in
# indentAfterItems) using this technique.
itemNames:
    item: 1
    myitem: 1

# if you want to indent if, else, fi constructs such as, for example,
#       \ifnum#1=2
#               something
#       \else
#               something else
#       \fi
# then populate them in constructIfElseFi
constructIfElseFi:
    ifnum: 1
    ifdim: 1
    ifodd: 1
    ifvmode: 1
    ifhmode: 1
    ifmmode: 1
    ifinner: 1
    if: 1
    ifcat: 1
    ifx: 1
    ifvoid: 1
    ifeof: 1
    iftrue: 1
    ifcase: 1

# latexindent can be called without a file extension,
# e.g, simply
#       latexindent myfile
# in which case the choice of file extension is chosen
# according to the choices made in fileExtensionPreference
# Other file extensions can be added.
fileExtensionPreference:
    .tex: 1
    .sty: 2
    .cls: 3
    .bib: 4

# preferences for information displayed in the log file
logFilePreferences:
    showEveryYamlRead: 1
    showAlmagamatedSettings: 0
    endLogFileWith: '--------------'
    traceModeIncreaseIndent: '>>'
    traceModeAddCurrentIndent: '||'
    traceModeDecreaseIndent: '<<'
    traceModeBetweenLines: "\n"

# \begin{document} and \end{document} are treated differently
# by latexindent within filecontents environments
fileContentsEnvironments:
    filecontents: 1
    filecontents*: 1

# *** NOTE ***
# If you have specified alwaysLookforSplitBraces: 1
# and alwaysLookforSplitBrackets: 1 then you don't need
# to worry about completing
#
#       checkunmatched
#       checkunmatchedELSE
#       checkunmatchedbracket
#
# in other words, you don't really need to edit anything
# below this line- it used to be necessary for older
# versions of the script, but not anymore :)
#***      ***

# commands that might split {} across lines
# such as \parbox, \marginpar, etc
checkunmatched:
    parbox: 1
    vbox: 1

# very similar to %checkunmatched except these
# commands might have an else construct
checkunmatchedELSE:
    pgfkeysifdefined: 1
    DTLforeach: 1
    ifthenelse: 1

# commands that might split []  across lines
# such as \pgfplotstablecreatecol, etc
checkunmatchedbracket:
    pgfplotstablecreatecol: 1
    pgfplotstablesave: 1
    pgfplotstabletypeset: 1
    mycommand: 1
    psSolid: 1

EOF

sudo mv defaultSettings.yaml /usr/local/texlive/2016/texmf-dist/scripts/latexindent/
```
