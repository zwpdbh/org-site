+++
title = "My Emacs Configuration"
date = 2020-01-15T17:17:00+08:00
lastmod = 2020-08-04T11:28:39+08:00
tags = ["Emacs"]
draft = false
weight = 10
autoCollapseToc = true
mathjax = true
contentCopyright = "MIT"
author = "Z wei"
+++

## Good emacs configuration {#good-emacs-configuration}

-   [Purcell's emacs.d](https://github.com/purcell/emacs.d)
-   [Chen Bin's Emacs configuration](https://github.com/redguardtoo/emacs.d#org-mode)
-   [seagle0128 -- Vincent Zhang](https://github.com/seagle0128/.emacs.d)
-   [John's Emacs Config](https://www.john2x.com/emacs.html)
-   [Ladicle's Emacs Configuration](https://ladicle.com/post/config/)
-   [Aaron Bedra's Emacs 26 Configuration](http://aaronbedra.com/emacs.d/)
-   [Sacha Chua's Emacs configuration](https://pages.sachachua.com/.emacs.d/Sacha.html)
-   [a different way to organize configuration files](https://github.com/CSRaghunandan/.emacs.d/tree/master/setup-files)
-   [jerryhsieh/Emacs-config](https://github.com/jerryhsieh/Emacs-config)
-   [Justin Abrahms](https://justin.abrah.ms/dotfiles/emacs.html)


## Good articles {#good-articles}

-   [A Life Configuring Emacs](https://alhassy.github.io/init/)
-   [Emacs org-mode examples and cookbook](http://ehneilsen.net/notebook/orgExamples/org-examples.html)
-   [A Life Configuring Emacs](https://alhassy.github.io/init/)
-   [GETTING STARTED WITH USE-PACKAGE](http://cachestocaches.com/2015/8/getting-started-use-package/)
-   [Emacs mini manual series](https://tuhdo.github.io/index.html)


## Emacs on Windows10 {#emacs-on-windows10}


### Set terminal in Windows10 (for GitBash and msys2) {#set-terminal-in-windows10--for-gitbash-and-msys2}

-   refs
    -   [Configure Msys's default size, color, and font](https://stackoverflow.com/questions/447824/how-do-you-configure-msyss-default-size-color-and-font)
    -   Right click on the title bar at the top of the shell window and select "Options"
    -   [Setup SSH Authentication for Git Bash on Windows](https://gist.github.com/bsara/5c4d90db3016814a3d2fe38d314f9c23)
-   Edit `.bash_profile` in home directory:

    ```sh
    alias ll="ls -la"
    alias ls='ls --color'

    ##############
    ### Basics ###
    ##############

    # Don't wait for job termination notification
    set -o notify

    # Enables UTF-8 in Putty.
    # See http://www.earth.li/~huggie/blog/tech/mobile/putty-utf-8-trick.html
    echo -ne '\e%G\e[?47h\e%G\e[?47l'

    # My pretty prompt (Yay!)
    PS1='\[\e[32;40m\]\u\[\e[0m\]\[\e[34;40m\]\H\[\e[0m\]\[\e[40;1m\]\w\[\e[0m\] '

    # Start SSH Agent
    #----------------------------

    SSH_ENV="$HOME/.ssh/environment"

    function run_ssh_env {
      . "${SSH_ENV}" > /dev/null
    }

    function start_ssh_agent {
      echo "Initializing new SSH agent..."
      ssh-agent | sed 's/^echo/#echo/' > "${SSH_ENV}"
      echo "succeeded"
      chmod 600 "${SSH_ENV}"

      run_ssh_env;

      ssh-add ~/.ssh/id_rsa;
    }

    if [ -f "${SSH_ENV}" ]; then
      run_ssh_env;
      ps -ef | grep ${SSH_AGENT_PID} | grep ssh-agent$ > /dev/null || {
        start_ssh_agent;
      }
    else
      start_ssh_agent;
    fi
    ```

    -   To generate ssh-key pairs

        ```sh
        ssh-keygen -t rsa -C "hyperion_z@outlook.com"
        ```
-   After installing [git for windows (it also contains ssh)](https://git-scm.com/downloads), configure git global info

    ```sh
    git config --global user.name "zwpdbh"
    git config --global user.email "hyperion_z@outlook.com"

    # To set repository-specific username/email configuration:
    # 1. From the command line, change into the repository directory.
    # 2. git config user.name "FIRST_NAME LAST_NAME"
    # 3. git config user.email "MY_NAME@example.com"
    # 4. Verify it: cat .git/config
    ```


### Install Emacs {#install-emacs}

-   Install Emacs from [Index of /gnu/emacs/pretest/windows](https://alpha.gnu.org/gnu/emacs/pretest/windows/)
-   Edit system environment variables for Windows10
    -   add "C:\Program Files\Emacs\x86\_64\bin" into path variable
-   Now, you should run `emacs &` from gitbash.


### (optional) Emacs in WSL {#optional--emacs-in-wsl}

-   ref: <https://github.com/hubisan/emacs-wsl>
-   enable ssh
    -   edit `/etc/ssh/sshd_config`
        -   change `PassworldAuthentication` to yes
        -   change connection port from 22 to 2222
    -   restart sshd
        -   `service ssh status` check sshd status
        -   `sudo service ssh --full-restart`
            -   If you meet error: sshd: no hostkeys available -- exiting.
                Run: `/usr/bin/ssh-keygen -A`
    -   In case meet error: "could not load host key"
        -   run `ssh-keygen -A`
    -   [Automatically start ssh server on boot on Windows Subsystem for Linux](https://gist.github.com/dentechy/de2be62b55cfd234681921d5a8b6be11)
-   use MobaXterm to connect
    -   address: 127.0.0.1
    -   port: 2222
-   make **emacsclient** use our defined emacs theme, edit `~/.bashrc`

    ```text
    # set a fancy prompt (non-color, unless we know we "want" color)
    # case "$TERM" in
    #     xterm-color|*-256color) color_prompt=yes;;
    # esac
    export TERM=xterm-256color
    ```
-   To start emacs without GUI, run `emacs -nw`
-   Blurry problem for Emacs GUi via MobaXterm
    -   right click mobaxterm executable or shortcut
    -   change high DPI settings
    -   in section High DPI scaling override
    -   select `application` from dropdown list
-   Avoid Git username and password
    -   ref: [How do I avoid the specification of the username and password at every git push?](https://stackoverflow.com/questions/8588768/how-do-i-avoid-the-specification-of-the-username-and-password-at-every-git-push)
    -   copy the content of `/home/zw/.ssh/id_rsa.pub` to GitHub settings/SSH-Keys
    -   make sure the git remote is using ssh instead of https
-   Use native browser in Windows10
    -   Delete ubuntu firefox: `sudo apt remove firefox`.
-   sometimes, you need to run docker inside WSL, see: [setting up docker for windows and wsl](https://nickjanetakis.com/blog/setting-up-docker-for-windows-and-wsl-to-work-flawlessly)
    -   Install and run docker desktop for windows10 (as server)
    -   Add the following setting

        ```sh
        ### for let linux system use remote, our pc docker server
        export DOCKER_HOST=tcp://localhost:2375
        export DOCKER_HOST=tcp://127.0.0.1:2375
        ```


### (optional) Emacs in msys2 {#optional--emacs-in-msys2}

```sh
pacman -Syu

pacman -Su base-devel
pacman -Su mingw-w64-x86_64-toolchain
pacman -Su mingw-w64-x86_64-gcc
pacman -Su mingw-w64-x86_64-cmake
pacman -Su make

# install Emacs dependencies
pacman -Su mingw-w64-x86_64-ctags mingw-w64-x86_64-zlib mingw-w64-x86_64-xpm-nox mingw-w64-x86_64-gnutls mingw-w64-x86_64-libwinpthread

# install Emacs optional dependencies
pacman -Su mingw-w64-x86_64-giflib mingw-w64-x86_64-libjpeg-turbo mingw-w64-x86_64-libpng mingw-w64-x86_64-librsvg mingw-w64-x86_64-libtiff mingw-w64-x86_64-imagemagick mingw-w64-x86_64-libxml2 mingw-w64-x86_64-librsvg mingw-w64-x86_64-graphviz

# install Emacs
pacman -Su mingw-w64-x86_64-emacs
```


## Emacs on Mac OSX {#emacs-on-mac-osx}

-   ref: <https://github.com/daviderestivo/homebrew-emacs-head>
-   steps to install latest Emacs on OSX via brew

    ```sh
    brew tap daviderestivo/emacs-head
    brew install emacs-head --HEAD --with-cocoa
    ```


## Indentation for Lisp and Elisp {#indentation-for-lisp-and-elisp}

-   ref: [A few notes on Elisp indentation](https://oremacs.com/2015/01/28/notes-on-elisp-indentation/)
-   ref: [Indenting Common Lisp](https://dept-info.labri.fr/~idurand/enseignement/lst-info/PFS/Common/Strandh-Tutorial/indentation.html)
-   ref: [Finally fixing indentation of quoted lists](https://www.reddit.com/r/emacs/comments/d7x7x8/finally%5Ffixing%5Findentation%5Fof%5Fquoted%5Flists/)
    -   ref: [How to indent keywords aligned in elisp?](https://emacs.stackexchange.com/questions/10230/how-to-indent-keywords-aligned/52789#52789)
-   When defmacro, we could customize how it will be indented:

    ```lisp
    (defmacro lispy-save-excursion (&rest body)
      "More intuitive (`save-excursion' BODY)."
      (declare (indent 0))
      `(let ((out (save-excursion
                    ,@body)))
         (when (bolp)
           (back-to-indentation))
         out))
    ```


## Terminal setup {#terminal-setup}


### Emacs in Microsoft Windows {#emacs-in-microsoft-windows}

-   ref: [Emacs in Microsoft Windows FAQ](http://ergoemacs.org/emacs/emacs%5Fmswin.html)


### Set terminal env {#set-terminal-env}

```sh
# for WSL, running docker
# see: https://nickjanetakis.com/blog/setting-up-docker-for-windows-and-wsl-to-work-flawlessly
export PATH="$PATH:$HOME/.local/bin"

# if running bash
if [ -n "$BASH_VERSION" ]; then
    # include .bashrc if it exists
    if [ -f "$HOME/.bashrc" ]; then
        . "$HOME/.bashrc"
    fi
fi

# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi

# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/.local/bin" ] ; then
    PATH="$HOME/.local/bin:$PATH"
fi



### set my terminial configuration
alias ll="ls -la"
alias ls='ls --color'

# export TERM=xterm-color
export TERM=xterm-256color
export GREP_OPTIONS='--color=auto' GREP_COLOR='1;32'
export CLICOLOR=1
export LSCOLORS=ExFxCxDxBxegedabagacad

export COLOR_NC='\e[0m' # No Color
export COLOR_WHITE='\e[1;37m'
export COLOR_BLACK='\e[0;30m'
export COLOR_BLUE='\e[0;34m'
export COLOR_LIGHT_BLUE='\e[1;34m'
export COLOR_GREEN='\e[0;32m'
export COLOR_LIGHT_GREEN='\e[1;32m'
export COLOR_CYAN='\e[0;36m'
export COLOR_LIGHT_CYAN='\e[1;36m'
export COLOR_RED='\e[0;31m'
export COLOR_LIGHT_RED='\e[1;31m'
export COLOR_PURPLE='\e[0;35m'
export COLOR_LIGHT_PURPLE='\e[1;35m'
export COLOR_BROWN='\e[0;33m'
export COLOR_YELLOW='\e[1;33m'
export COLOR_GRAY='\e[0;30m'
export COLOR_LIGHT_GRAY='\e[0;37m'
### end of terminal configuration

### for let linux system use remote, our pc docker server
export DOCKER_HOST=tcp://localhost:2375
export DOCKER_HOST=tcp://127.0.0.1:2375

### setup path for go
export PATH=$PATH:/usr/local/go/bin
```

-   add the above content into ~/.bashrc


### Setup zsh {#setup-zsh}

-   ref: [Install Z-shell (Oh My Zsh) on Ubuntu 18.04 LTS](https://dev.to/mskian/install-z-shell-oh-my-zsh-on-ubuntu-1804-lts-4cm4)


## Font {#font}

-   [source code pro](https://github.com/adobe-fonts/source-code-pro)
    -   git clone the repo and copy those .ttf file font files into your font directory (for ubuntu, it is ~/.fonts/)
    -   ref: [How to use the Adobe Source Code Pro font?](https://askubuntu.com/questions/193072/how-to-use-the-adobe-source-code-pro-font)
-   [Patched fonts for Powerline users (including multiple font, easy install)](https://github.com/powerline/fonts)
-   [Office code pro](https://github.com/nathco/Office-Code-Pro)
-   [Hack](https://github.com/source-foundry/Hack)
-   [All-the-icons](https://github.com/domtronn/all-the-icons.el)
    -   It is used to enhance Emacs's icons.
    -   To manually install it:
        -   On Ubuntu, copy the all the files from fonts folder from project into `/.local/share/fonts/`.
        -   On Windows, double click those font files to install.
        -   After installation, restart Emacs.


## Latex {#latex}

-   ref: [LaTeX vs. MiKTeX: The levels of TeX](http://www.tug.org/levels.html)
-   ref: [Setting up Latex with Emacs](http://agent18.github.io/setting-up-latex.html)
-   ref: [LaTeX Source Code Blocks in Org Mode](https://orgmode.org/worg/org-contrib/babel/languages/ob-doc-LaTeX.html)
-   ref: [Emacs Academic Tools](https://people.umass.edu/weikaichen/post/emacs-academic-tools/)
-   ref: [Nasseralkmim's Latex environment](https://nasseralkmim.github.io/notes/2016/08/21/my-latex-environment/)
-   Two components
    -   AUCTeX
        -   for writing and formatting TeX files in Emacs
        -   provides syntax highlighting, smart indentation and formatting, previews of mathematics and other elements directly in the editing buffer
    -   Tex typesetting system
        -   TeX Live is a cross-platform, free software distribution for the TeX typesetting system that includes major TeX-related programs, macro packages, and fonts.
        -   Or, MiKTeX is a free distribution of the TeX/LaTeX typesetting system for Microsoft Windows.


### <span class="org-todo done DONE">DONE</span> Render LaTeX fragments in Org-mode {#render-latex-fragments-in-org-mode}

-   ref: [Emacs org-mode examples and cookbook](http://ehneilsen.net/notebook/orgExamples/org-examples.html)
-   ref: [11.8 Embedded LaTeX](https://www.gnu.org/software/emacs/manual/html%5Fnode/org/Embedded-LaTeX.html#Embedded-LaTeX)
-   ref: [Preview LaTeX in org-mode](https://www.reddit.com/r/emacs/comments/9h44lk/i%5Fcan%5Ffinally%5Fpreview%5Flatex%5Fin%5Forgmode%5Ftook%5Fme/)

    ```emacs-lisp
    (defun krofna-hack ()
      (when (looking-back (rx "$ "))
        (save-excursion
          (backward-char 1)
          (org-toggle-latex-fragment))))

    (add-hook 'org-mode-hook
              (lambda ()
                (org-cdlatex-mode)
                (add-hook 'post-self-insert-hook #'krofna-hack 'append 'local)))
    ```
-   Test
    \\[e^{\pi i }=-1\\]


### <span class="org-todo todo DOING">DOING</span> Org-babel {#org-babel}


## R {#r}

-   ref: [Org-mode and R: An Introduction](https://github.com/erikriverson/org-mode-R-tutorial/blob/master/org-mode-R-tutorial.org)
-   ref: [Emacsで作るオレオレRStudio](https://y-mattu.hatenablog.com/entry/rstudio%5Femacs)


## JavaScript {#javascript}

-   [JavaScript layer from spacemacs](https://develop.spacemacs.org/layers/+lang/javascript/README.html)


## Org {#org}

-   ref: [Use Org-Mode Links for Absolutely Anything](https://endlessparentheses.com/use-org-mode-links-for-absolutely-anything.html?fbclid=IwAR1QDsJ4Ru7bHAEAqgsGofdgeSJJDA1sQPafKfxToEBYH9eohGe83To7pMU)
-   [a very good example how to set org-mode: Another Org-Mode Configuration](http://www.i3s.unice.fr/~malapert/org/tips/emacs%5Forgmode.html)
-   [Defining Custom Task Workflows With Emacs Org-Mode](http://jr0cket.co.uk/2013/08/defining-custom-workflow-with-Emacs-org-mode.html.html)
-   [Beautifying Org Mode in Emacs](https://zzamboni.org/post/beautifying-org-mode-in-emacs/)
-   [Emacs org-mode examples and cookbook](http://ehneilsen.net/notebook/orgExamples/org-examples.html)


### Use emacs to create blog sites {#use-emacs-to-create-blog-sites}

-   [Publishing org-mode files to HTML](https://orgmode.org/worg/org-tutorials/org-publish-html-tutorial.html)


#### Examples of website created from Emacs's org-mode {#examples-of-website-created-from-emacs-s-org-mode}

-   [beatiful websites using Emacs's org mode](https://stackoverflow.com/questions/12052013/beautiful-websites-using-emacss-org-mode)
-   [How this site was created](http://alexott.net/en/writings/EmacsMuseMyPage.html)
-   [Web pages made with org-mode](https://orgmode.org/worg/org-web.html)


#### [Different staitc site generators with org](https://orgmode.org/worg/org-blog-wiki.html) {#different-staitc-site-generators-with-org}


#### Org-mode to GitHub pages with Jekyll {#org-mode-to-github-pages-with-jekyll}

-   [Org-mode to GitHub pages with Jekyll](http://cute-jumper.github.io/emacs/2013/10/06/orgmode-to-github-pages-with-jekyll)
    -   [Using org to Blog with Jekyl](https://orgmode.org/worg/org-tutorials/org-jekyll.html)
    -   [easy-jekyll](https://github.com/masasam/emacs-easy-jekyll)


#### Org file to blog using: [Hugo](https://gohugo.io/), [ox-hugo](https://github.com/kaushalmodi/ox-hugo) and [Netlify](https://www.netlify.com/) {#org-file-to-blog-using-hugo-ox-hugo-and-netlify}

-   [Blogging with Emcas Org](https://www.zcl.space/tools/my-blog-workflow/)
-   [Moved from Jekyll to Hugo and ox-hugo](https://rakhim.org/2018/09/moved-from-jekyll-to-hugo-and-ox-hugo/)
-   [Blogging with org-mode and ox-hugo](https://www.shanesveller.com/blog/2018/02/13/blogging-with-org-mode-and-ox-hugo/)


## C/C++ related {#c-c-plus-plus-related}

-   [Emacs as C++ IDE](http://syamajala.github.io/c-ide.html)
-   [Emacs as a C++ IDE](http://martinsosic.com/development/emacs/2017/12/09/emacs-cpp-ide.html#configuration)
-   <https://ddavis.io/>
-   [使用cquery：C++ language server](http://maskray.me/blog/2017-12-03-c++-language-server-cquery)
-   [tuhdo's C/C++ Development Environment for Emacs](http://tuhdo.github.io/c-ide.html)
-   [Supplement to Tudho's C/C++ Development Enivornment for Emacs Guide](http://syamajala.github.io/c-ide.html)


## Python related {#python-related}

-   [Configuring Emacs, lsp-mode and the python language server](https://vxlabs.com/2018/06/08/python-language-server-with-emacs-and-lsp-mode/)


## Troubleshootings {#troubleshootings}


### Keybindings behave different from GUI in pure text terminal {#keybindings-behave-different-from-gui-in-pure-text-terminal}

-   ref: [Using Emacs as a daemon (emacsclient) exhibiting weird behavior](https://emacs.stackexchange.com/questions/43705/using-emacs-as-a-daemon-emacsclient-exhibiting-weird-behavior)
-   ref: [How do I get my .emacs init files to load correctly with emacs server?](https://superuser.com/questions/537922/how-do-i-get-my-emacs-init-files-to-load-correctly-with-emacs-server)
-   ref: [Emacs: How to Bind Super Hyper Keys](http://ergoemacs.org/emacs/emacs%5Fhyper%5Fsuper%5Fkeys.html)


### Copy and Paste from clipboard {#copy-and-paste-from-clipboard}

-   ref: [Emacs through terminal and clipboard.](https://www.reddit.com/r/emacs/comments/8k2pxv/emacs%5Fthrough%5Fterminal%5Fand%5Fclipboard/)
-   ref: [simpleclip](https://github.com/rolandwalker/simpleclip)
-   use `xclip`


### Font is not set in terminal {#font-is-not-set-in-terminal}

-   In terminal, font is controlled by the terminal app. So, go to the terminal app's option and change it to use installed font.


### Error loading autoloads: (epg-error no usable configuration OpenPGP) {#error-loading-autoloads--epg-error-no-usable-configuration-openpgp}

-   Emacs' version: Emacs26.3 in MSYS2
-   reproduce this error: `Meta-x epa-list-keys`
-   solution
    -   install missing package

        ```text
        pacman -Su gnupg
        ```
    -   setup windows exec-path correctly
    -   set epg-pgp-program

        ```emacs-lisp
        (custom-set-variables '(epg-gpg-program  "/usr/local/bin/gpg2"))
        ```

        -   ref: [Emacs epa and gnupg2: “no usable configuration”](https://stackoverflow.com/questions/41741477/emacs-epa-and-gnupg2-no-usable-configuration)


### Apt install error: ument {#apt-install-error-ument}

-   description
    -   When try to fix it with `sudo apt install -f`, following errors show up

        ```text
        dpkg: error processing archive /var/cache/apt/archives/emacs25-common_25.3~2.gitc09215a-kk1+18.04_all.deb (--unpack):
         trying to overwrite '/usr/share/emacs/site-lisp/subdirs.el', which is also in package emacs26-common 26.3~1.git96dd019-kk1+18.04
        dpkg-deb: error: paste subprocess was killed by signal (Broken pipe)
        Errors were encountered while processing:
         /var/cache/apt/archives/emacs25-common_25.3~2.gitc09215a-kk1+18.04_all.deb
        ```
-   Solution
    -   ref: [apt-get -f install fails with emacs25 conflict with emacs26](https://askubuntu.com/questions/1178815/apt-get-f-install-fails-with-emacs25-conflict-with-emacs26)
    -   run the following commands to solve it

        ```sh
        sudo dpkg -i --force-all /var/cache/apt/archives/emacs25-common_25.3~2.gitc09215a-kk1+18.04_all.deb
        sudo apt install -f
        ```


### Cannot open load file: No such file or directories, adaptive-wrap {#cannot-open-load-file-no-such-file-or-directories-adaptive-wrap}

-   Description
    -   can not do further operation, even open file or switch buffer
-   Solution
    -   delete use-package and re-install use-package


### <span class="org-todo todo DOING">DOING</span> Network: make client process failed: Connection refused {#network-make-client-process-failed-connection-refused}

-   Description

    ```text
    Contacting host: raw.githubusercontent.com:443
    open-network-stream: make client process failed: Connection refused, :name, raw.githubusercontent.com, :buffer, #<killed buffer>, :host, raw.githubusercontent.com, :service, 443, :nowait, nil, :tls-parameters, nil
    ```

    -   When I run `M-x all-the-icons-install-fonts`. The above shows in message and font is not downloaded.
-   Solution


### <span class="org-todo todo DOING">DOING</span> Chinese characters could not be displayed properly {#chinese-characters-could-not-be-displayed-properly}

-   ref: [Installing Asian Fonts on Ubuntu & Debian](https://help.accusoft.com/PrizmDoc/v12.1/HTML/Installing%5FAsian%5FFonts%5Fon%5FUbuntu%5Fand%5FDebian.html)
-   Install Chinese language support on Ubuntu

    ```sh
    sudo apt-get install language-pack-zh*
    sudo apt-get install chinese*
    ```


### Font missing {#font-missing}

-   Description
    -   There is a missfont.log

        ```text
        mktextfm ecrm1000
        ```

        -   Probably is caused by latex/tex feature.
-   Solution
    -   On Ubuntu

        ```sh
        sudo apt-get install texlive-fonts-recommended
        ```


### <span class="org-todo done DONE">DONE</span> org-latex-preview failed with errror: Please adjust ‘dvipng’ part of ‘org-preview-latex-process-alist’ {#org-latex-preview-failed-with-errror-please-adjust-dvipng-part-of-org-preview-latex-process-alist}

-   Description
    -   For preview latex in org-mode, such as: \\[e^{\pi i }=-1\\] (Run: C-c C-x C-l which is org-latex-preview), show error message

        ```text
        Creating LaTeX preview...
        org-compile-file: File "/tmp/orgtexHB1HLg.dvi" wasn’t produced.  Please adjust ‘dvipng’ part of ‘org-preview-latex-process-alist’.
        ```
-   Solution
    -   Install LaTex for Linux, TexLive

        ```sh
        sudo apt install texlive-xetex
        ```
    -   In Emacs configuration

        ```emacs-lisp
        (setq org-latex-compiler "xelatex")
        ```
-   Test
    \\[e^{\pi i }=-1\\]