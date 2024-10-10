# LaTeX

## Setup

### TeX Live

```sh
sudo apt install texlive-full

# if the installation gets stuck at the following...
# Pregenerating ConTeXt MarkIV format. This may take some time...
# press Enter interruptly until the Enter key
```

## Usage

```makefile
.PHONY : all latex bibtex view clean distclean

TARGET=sousa2025
SOURCE=$(TARGET).tex

all:
	pdflatex $(SOURCE)
	bibtex $(TARGET)
	pdflatex $(SOURCE)
	pdflatex $(SOURCE)

latex:
	pdflatex $(SOURCE)
	pdflatex $(SOURCE)

bibtex:
	pdflatex $(SOURCE)
	bibtex $(TARGET)

view:
	open $(TARGET).pdf &

clean:
	rm -f $(TARGET).aux $(TARGET).bbl $(TARGET).blg $(TARGET).log $(TARGET).out

distclean:clean
	rm -f $(TARGET).pdf
```
