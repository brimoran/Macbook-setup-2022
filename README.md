# Macbook-setup-2022
Setting up a new Macbook

MacBook Pro (14-inch, 2021)

Apple M1 Pro

16 GB

500 GB

## Settings

Settings > Trackpad, turn on Tap to click.

## Chrome

Download Chrome, launch it and set as default browser.

## Git and SSH keys

Launch the Terminal.

Set up git config:

```git config --global user.name "John Doe"```

If they aren't already installed you'll be prompted now to install developer tools... which can take an hour or so.

```git config --global user.email johndoe@example.com```

Confirm:

```
git config --list
```

Create SSH key:

```
ls -al ~/.ssh
# Lists the files in your .ssh directory, if they exist
```

If none exist:

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Show contents of public SSH key to copy to add to web git service:

```
cat ~/.ssh/id_rsa.pub
```

Add SSH key to git web service and then clone repo using SSH in the area you want it on your computer (for example into a version-controlled folder in your home directory):

e.g. with Gitlab

```
git clone git@gitlab.com:YOURGITUSERNAME/YOURREPO.git

```

Add public key to any servers you need to access:

``ssh-copy-id -i ~/.ssh/id_rsa.pub YOUR_USER_NAME@IP_ADDRESS_OF_THE_SERVER``

## Homebrew

Install homebrew as a package manager:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

I followed the post-install instructions shown in the terminal to add to the default shell's path which was probably not needed as I am going to change that next.

## bash

Using bash so can use same scripts as on Linux box.  Default is version 3.2.57(1)-release over 10 years old, update with:

```brew install bash```

Verify installation (should see install in ```/opt/homebrew/bin/bash```):

```which -a bash```

Allow new shell:

```sudo vim /etc/shells``` and add ```/opt/homebrew/bin/bash``` to the file (/bin/bash is already there - this is the old version).

Set as default shell:

```chsh -s /opt/homebrew/bin/bash```

Change for root user:

```sudo chsh -s /opt/homebrew/bin/bash```

Also check in System Preferences > Users & Groups by clicking the lock icon and entering your password. Hold the Ctrl key, click the user account name in the left pane, and select “Advanced Options." Make sure the shell is /opt/homebrew/bin/bash.

Add homebrew to bash path:

```echo "export PATH=/opt/homebrew/bin:$PATH" >> ~/.bash_profile && source ~/.bash_profile```

check shell with:

```echo $SHELL```

Add some aliases to short cut commands, e.g.:

``vim .bash_profile``

and for example add:

``alias work='ssh YOURUSERNAME@SERVERIPADDRESS'``

## Vim

Default Vim is version  8.2.3489, update with:

```
brew install vim
```

Add alias to ```~/.bash_profile``` file so homebrew version loads instead of system version:

```
which -a vim
```

```
alias vim=/opt/homebrew/bin/vim
```

Then let's install VimPlug:

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Then copy .vimrc which I store in a git repository to home folder:

e.g. navigate to folder and then

```
cp .vimrc ~/
```

And then install the plug ins from within the .vimrc file in Vim by typing:

```
:PlugInstall
```

## LaTeX and Ghostscript

MacTex 2021 comes with ghostscript 9.53.3 to compress pdfs.  So head to:

https://www.tug.org/mactex/mactex-download.html

and download and install MacTeX.

Open Texshop which will be installed via GUI and check Preferences > Engine for location Path, in my case /Library/TeX/texbin, so...

Add to Path:

```echo "export PATH=/Library/TeX/texbin:$PATH" >> ~/.bash_profile && source ~/.bash_profile```

### Poppler

For PDF tools such as pdftotext:

```brew install poppler```

### Pandoc

To convert document formats:

```brew install pandoc```

### OCRmyPDF

To OCR PDFs example: ```ocrmypdf -l eng input.pdf output.pdf```

```brew install ocrmypdf```

### Latex diff

To show diffs in LaTeX:

```brew install latexdiff```

### Rename

For flexible tools to rename file in BASH:

```brew install rename```

### Mosh

For more persistent remote terminal access.

```brew install mosh```

### XQuartz

Replace X11 with Xquartz which is required by some R packages.

```brew install --cask xquartz```

## R and R Studio

Download and install R - be sure to choose the Apple silicon arm64 version:

https://cran.r-project.org/

Enter R via the terminal as superuser:

```sudo R```

Let's see how many CPUs we are using:

```getOption("Ncpus", 1L)```

Just 1. So how many can we use?

```
parallel::detectCores()
```

8, so let's use 6 to speed up install of packages:

```
options(Ncpus = 6)
```

Confirm:

```
getOption("Ncpus", 1L)
```

And then:

```install.packages(c("ggplot2","tidyverse","knitr","ggthemes","scales","ggmap","plotly","ggfortify","leaflet","leaflet.extras","rgdal","forecast","treemapify","dbscan","survival","googleVis","rmarkdown","flexdashboard","highcharter","devtools","maptools","mapview","treemap","networkD3","visNetwork","DiagrammeR","DT","ggcorrplot","Hmisc","anomalize", "fpp2", "h2o", "sweep", "timetk", "xgboost", "prophet", "survminer","ggwordcloud", "ggsn", "formattable", "IMD", "car", "maps", "this.path"))```

This will take about 30 minutes.

After it completes, to enable mapshot to work:

```webshot::install_phantomjs()```

```q()``` to exit R

### Troubleshooting R install

However we got an unexpected error on our big install of packages:

```
Warning message:
In install.packages(c("ggplot2", "tidyverse", "knitr", "ggthemes",  :
  installation of one or more packages failed,
  probably ‘rgdal’
```

This may be a compilation issue so downloaded gnu fortran for arm as linked from https://cran.r-project.org/ in case that is the reason.

copied to /opt/R/arm64 and then add to PATH with:

```echo "export PATH=/opt/R/arm64:$PATH" >> ~/.bash_profile && source ~/.bash_profile```

Then attempting to install rgdal package from within R, and we get a different error message:

```configure: error: gdal-config not found or not executable.```

So next trying to install gdal, quit R and:

```brew install gdal```

and also

```brew install proj```

And then back to R to install rgdal which will now work.

Also:

```install.packages("gpclib", type = "source")```

```install.packages("rgeos")```

```install.packages("mapproj")```

test if all packages can be loaded.

mapview errors on load (requires terra?), as do h2o and  ggsn (requires sf?)

solution for sf from: https://github.com/r-spatial/sf/issues/1317#issuecomment-603928225

```remotes::install_github("RcppCore/Rcpp")
install.packages('sf', configure.args = '--with-gdal-config=/usr/local/bin/gdal-config --with-geos-config=/usr/local/bin/geos-config --with-proj-include=/usr/local/include/ --with-proj-lib=/usr/local/lib/', configure.vars = 'GDAL_DATA=/usr/local/opt/gdal/share/gdal/')```

i.e. looks like have to specify where gdal-config and proj are located.

Everything now works

## Download and install R Studio:

https://rstudio.com/products/rstudio/download/

add gruvbox theme: https://github.com/tallguyjenks/gruvboxr


## Python - additionals

Python 3 will have already been installed in this process through brew.


python3 -m pip install --upgrade pip

and

``pip3 install openpyxl``

and

```pip3 install xlsx2csv```

and so available in Python 2 just in case I occassionally fail to reference python3:

```sudo easy_install xlsx2csv```

## EPS2PGF

The usual link is dead: https://sourceforge.net/projects/eps2pgf/files/latest/download

This one works though:

https://www.onworks.net/software/windows/app-eps2pgf

## Acorn

Nice commercial graphics editor:

https://flyingmeat.com/acorn/

## Sublime Text

https://www.sublimetext.com/

## Keynote

Pre-installed.

## Microsoft Office

https://account.microsoft.com/services/office/install

## Additional Fonts

Fira Sans - download and batch install by unpacking, and selecting all files with Finder and dragging into Font Book:

https://fonts.google.com/specimen/Fira+Sans?selection.family=Fira+Sans

Tex Gyre Heroes - download and batch install by unpacking, and selecting all files with Finder and dragging into Font Book:

https://www.fontsquirrel.com/fonts/tex-gyre-heros

## Copy non-backed up files from old machine

In my case, two folders:

```r-resources``` - contains R functions I use a lot.

```shapefiles``` - contains large shapefiles that I point to from other R scripts.
