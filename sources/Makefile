#TOP_DIR = $(realpath ..)
TOP_DIR = ..

# Specify the document version.
# This can be overridden when invoked from the automated build process.
DOC_VERSION = X.Y.Z

# Specify whether a spelling error is an error (as opposed to a warning).
# This can be overridden when invoked from the automated build process.
# Please resist the temptation to set this variable to zero. ;-)
# DOC_SPELLCHECK_MUST_PASS = 0
DOC_SPELLCHECK_MUST_PASS = 1

# This can be overridden when invoked from the automated build process.
INSTALL_DIR = $(TOP_DIR)/install

# All of the Markdown source files (that are not generated during build).
SOURCES = $(shell find . -mindepth 2 -name '*.md')

# Special top-level markdown files
EXTRA_SOURCES = glossary.md \
  contributing.md \
  introduction.md \
  main_raw.md \
  obtaining_document.md \
  references.md \

# The Markdown files that are generated during the build process.
GENERATED_MARKDOWN = \
  knowledge_areas_summary.md \
  main.pre.md \
  main.gen.md \
  contributors.md \

# Merge extra sources with detected sources for teaching modules
SOURCES += $(EXTRA_SOURCES)

################################################################################
# Define primary targets.
################################################################################

# The all target builds the document in a minimal set of formats and
# performs a spell check.
.PHONY: all
all: documents spellcheck

# The world target is similar to the all target except that it builds the
# document in some additional formats.
.PHONY: world
world: all documents_extra

# The documents target builds the document in a minimal set of formats.
.PHONY: documents
documents: guidelines.html guidelines_html guidelines.epub

# The documents_extra target builds the document in a few extra formats.
.PHONY: world
documents_extra: guidelines.pdf

# The spellcheck format performs a spell check on the document.
.PHONY: spellcheck
spellcheck: spellcheck_result.txt

# The clean target removes all files generated during the build process.
.PHONY: clean
clean:
	rm -f $(GENERATED_MARKDOWN)
	rm -f guidelines.html guidelines.pdf guidelines.epub guidelines.tex
	rm -rf guidelines.texi
	rm -rf guidelines_html
	rm -f spellcheck_result.txt
	rm -f spellcheck_expected_sorted.txt
	rm -f missfont.log

# The install target installs the build document (in various formats)
# in the directory $(INSTALL_DIR).
.PHONY: install
install: all
	if [ ! -d "$(INSTALL_DIR)" ]; then \
	    mkdir -p "$(INSTALL_DIR)" || exit 1; \
	  fi
	for dir in html html/images; do \
	    if [ ! -d "$(INSTALL_DIR)/$$dir" ]; then \
	        mkdir -p "$(INSTALL_DIR)/$$dir" || exit 1; \
	    fi; \
	  done
	cp -f images/cpp_logo.png "$(INSTALL_DIR)/html/images"
	cp -f guidelines.html "$(INSTALL_DIR)/html/index.html"
	cp -r -f guidelines_html "$(INSTALL_DIR)/html_split"
	cp -f guidelines.epub "$(INSTALL_DIR)"
	if [ -f guidelines.pdf ]; then \
	      cp -f guidelines.pdf "$(INSTALL_DIR)"; \
	  fi

################################################################################
# Some additional configuration.
################################################################################

MAIN_GENERATOR= $(TOP_DIR)/tools/build/generate_main.py
MD_PREPROCESSOR = $(TOP_DIR)/tools/build/preprocessor
MAKE_MARKDOWN = $(TOP_DIR)/tools/build/make_markdown
SPELLCHECK_DIR = $(TOP_DIR)/config/spellcheck

################################################################################
# Preprocessing setup.
################################################################################

main.pre.md: $(SOURCES) contributors.md main_raw.md
	$(MAIN_GENERATOR) --raw main_raw.md --out main.pre.md --module-folder modules

main.gen.md: $(SOURCES) contributors.md main.pre.md
	$(MD_PREPROCESSOR) -v $(DOC_VERSION) < main.pre.md > main.gen.md

knowledge_areas_summary.md: $(SOURCES) knowledge_areas.dat
	$(MAKE_MARKDOWN) < knowledge_areas.dat > knowledge_areas_summary.md

contributors.md:
	git log --format="%aN%n%(trailers:key=Co-authored-by,valueonly=true)" | sed 's/ <.*>//g' | sort | uniq > contributors.md

################################################################################
# Establish Pandoc settings.
################################################################################

PANDOC_OPTIONS += --verbose
PANDOC_OPTIONS += --toc --toc-depth 3
PANDOC_OPTIONS += --number-sections
PANDOC_OPTIONS += --standalone
PANDOC_OPTIONS += --pdf-engine=xelatex
#PANDOC_OPTIONS += --lua-filter=tools/pandoc_filters/meta_vars.lua
#PANDOC_OPTIONS += --metadata date="`date +%Y-%m-%d`"
PANDOC_OPTIONS += --metadata version=$(DOC_VERSION)

INPUT_FORMAT = markdown+header_attributes+multiline_tables

EPUB_CSS_FILE = css/default-pandoc.css

#HTML_TEMPLATE = templates/bootstrap_menu.html
HTML_TEMPLATE = $(TOP_DIR)/config/pandoc_templates/uikit.html

#MAKEINFO_OPTIONS += --no-warn
MAKEINFO_OPTIONS += --error-limit 1000000000000
#MAKEINFO_OPTIONS += --verbose
MAKEINFO_OPTIONS += --no-validate
MAKEINFO_OPTIONS += --force

################################################################################
# Rules for generating the document in various formats.
################################################################################

guidelines.html: $(GENERATED_MARKDOWN)
	pandoc $(PANDOC_OPTIONS) --template $(HTML_TEMPLATE) --from $(INPUT_FORMAT) -o $@ main.gen.md

guidelines.epub: $(GENERATED_MARKDOWN)
	pandoc $(PANDOC_OPTIONS) -c $(EPUB_CSS_FILE) --from $(INPUT_FORMAT) -o $@ main.gen.md

guidelines.pdf: $(GENERATED_MARKDOWN)
	pandoc $(PANDOC_OPTIONS) --from $(INPUT_FORMAT) -o $@ main.gen.md

guidelines.texi: $(GENERATED_MARKDOWN)
	pandoc $(PANDOC_OPTIONS) --from $(INPUT_FORMAT) -o $@ main.gen.md

guidelines.docbook: $(GENERATED_MARKDOWN)
	pandoc $(PANDOC_OPTIONS) --from $(INPUT_FORMAT) -o $@ main.gen.md

guidelines_html: guidelines.texi
	makeinfo $(MAKEINFO_OPTIONS) --html -o guidelines_html guidelines.texi

guidelines.tex:
	pandoc $(PANDOC_OPTIONS) --from $(INPUT_FORMAT) --to latex -o $@ main.gen.md

################################################################################
# Rule for spellchecking.
################################################################################

spellcheck_result.txt: guidelines.html
	rm -f $@
	PATH="$(TOP_DIR)/tools/build:$$PATH" pandoc --from $(INPUT_FORMAT) \
	  --lua-filter $(TOP_DIR)/tools/pandoc_filters/spellcheck.lua \
	  main.gen.md | sort | uniq > $@
	  if [ -s $@ ]; then \
	      echo "DETECTED SPELLING ERRORS:"; \
	      cat $@ | while read line; do echo "Misspelled '$$line' in:"; \
	      grep -R "$$line" -n --color=auto --include='*.md' --exclude='main.gen.md'; done; \
	      sync; \
	      if [ $(DOC_SPELLCHECK_MUST_PASS) -ne 0 ]; then \
	          echo "ERROR: spelling errors detected, cannot proceed!"; \
	          exit 1; \
	      else \
	          echo "WARNING: spelling errors detected"; \
	      fi; \
	  fi
