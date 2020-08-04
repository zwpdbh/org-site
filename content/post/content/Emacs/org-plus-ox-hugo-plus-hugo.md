+++
title = "Org+ox-hugo+HUGO"
date = 2019-12-12T15:28:00+08:00
lastmod = 2020-08-04T11:50:21+08:00
tags = ["Emacs"]
draft = false
weight = 10
autoCollapseToc = true
mathjax = true
contentCopyright = "MIT"
+++

## Introduction {#introduction}

We could use Emacs's org-mode to easily take notes and export into different form. It is probably the best notebook. Now, it becomes more powerful that we could export org file into HUGO-format html to make our notes become our personal website.


## Create my-site using HUGO's academic theme {#create-my-site-using-hugo-s-academic-theme}

(`Hugo Static Site Generator v0.57.2/extended linux/amd64`)

-   [Install Hugo Extended](https://github.com/gohugoio/hugo/releases) (**Notice: It must be the full version, named Hugo Extended**)
-   [Create Academic website from GitHub](https://sourcethemes.com/academic/docs/install/), after following the link to finish all the steps:
    -   You created a repository in GitHub which contains your web-site.
    -   You let Netlify to manage that repo to publish it onto internet
-   Git clone and initialize it to run it locally (here, my repo is `org-site`)

    ```sh
    git clone git@github.com:zwpdbh/org-site.git
    cd org-site
    git submodule update --init --recursive
    hugo server -D
    ```


## Personalize your site with Academic theme {#personalize-your-site-with-academic-theme}

-   `config/_default/params.toml`
-   `config/_default/config.toml`
-   `config/_default/menus.toml`
    -   Controls sections displayed on menu bar which is located on the top of web page.

        ```text
        [[main]]
          name = "Software Engineering"
          url = "#software-engineering"
          weight = 40
        ```

        -   The `url` attribute refers a markdown file under `<HUGO_BASE_DIR>/content/home` named “software-engineering.md”
-   Inside `<HUGO_BASE_DIR>/content/`
    -   `authors/admin/_index.md` controls different contents displayed at your home page, such as your profile.
    -   To create a new section in the content, such as Software Engineering:
        -   Copy posts.md which is created automatically by the academic theme and rename it as software-engineering.md
        -   Edit line 3 as: # This section displays recent blog posts from \`content/software-engineering/\`
        -   Edit line 10 as: title = “Software Engineering”
        -   Edit line 15 as: page\_type = “software-engineering”


## Add custome CSS to make each post’s table of content float at right side {#add-custome-css-to-make-each-post-s-table-of-content-float-at-right-side}

-   (outdated) Edit config/\_default/params.toml plugins\_css = [“my-layout”]
-   Create and edit my-layout.css under themes/academic/assets/css

    ```text
    div.ox-hugo-toc.toc {
        position: fixed;
        width: 27%;
        right:1%;
        top:10%;
        font-size: 13px
    }

    div.article-container {
        width: 100%;
        margin:0;
        max-width: 70%;
        font-size: 16px
    }
    ```


## Configure emacs with ox-hugo {#configure-emacs-with-ox-hugo}

HUGO use markdown to generate the website. To convert our org file into markdown and make it embedded with HUGO markdown's option. We need to use [ox-hugo](https://ox-hugo.scripter.co/). The configuration will help us to do the following things:

1.  Whenever we want to write a post/note, we will use `org-capture` and it will insert a template into different sections files.

    -   each section file is a org file, such as mathematics.org, computer-science.org...etc.
    -   each post/note will be automatically inserted under the first level headline as second level headline. Then you write you note under each second level headline.
    -   we need to create those section files with the following at the top (take software-engineering.org as example):

    <!--listend-->

    ```text
    #+HUGO_BASE_DIR: ~/code/my-site/
    #+HUGO_SECTION: post
    #+SEQ_TODO: TODO DOING | DONE REVIEW ABANDONED
    #+FILETAGS: Software-Engineering
    #+OPTIONS:   *:t <:nil timestamp:nil
    #+HUGO_AUTO_SET_LASTMOD: t
    ​* Software-Engineering
    ```

    -   `#+HUGO_SECTION: post` will make `ox-hugo` to export the generated `<headline_title>.md` file into folder `<HUGO_BASE_DIR>/content/post`.
    -   `#+FILETAGS: Software-Engineering` will add tag `Software-Engineering` to all the notes in this file.
2.  Configuratio that put in your emacs setup

    ```emacs-lisp
    (defun org-hugo-new-subtree-post-capture-template ()
      "Returns `org-capture' template string for new Hugo post.
    See `org-capture-templates' for more information."
      (let* (;; http://www.holgerschurig.de/en/emacs-blog-from-org-to-hugo/
             (date (format-time-string (org-time-stamp-format  :inactive) (org-current-time)))
             (title (read-from-minibuffer "Post Title: ")) ;Prompt to enter the post title
             (fname (org-hugo-slug title)))
        (mapconcat #'identity
                   `(
                     ,(concat "* TODO " title)
                     ":PROPERTIES:"
                     ,(concat ":EXPORT_FILE_NAME: " fname)
                     ,(concat ":EXPORT_DATE: " date) ;Enter current date and time
                     ,(concat ":EXPORT_HUGO_CUSTOM_FRONT_MATTER+: "  ":weight 10 :autoCollapseToc true :mathjax true :contentCopyright MIT :author \"Z wei\"")
                     ":END:"
                     "%?\n")          ;Place th
                   "\n")))

    (defvar hugo-org-path "~/code/capture-org/"
      "define the place where we put our org files for hugo")
    (defvar org-capture-todo (concat hugo-org-path "todo.org"))
    (defvar org-capture-computer-science (concat hugo-org-path "computer-science.org"))
    (defvar org-capture-emacs (concat hugo-org-path "emacs.org"))
    (defvar org-capture-math (concat hugo-org-path "mathematics.org"))
    (defvar org-capture-software (concat hugo-org-path "software-engineering.org"))
    (defvar org-capture-tools (concat hugo-org-path "tools.org"))
    (defvar org-capture-work (concat hugo-org-path "work-notes.org"))
    (defvar org-capture-test (concat hugo-org-path "test.org"))

    (setq org-export-with-author nil)
    (setq org-capture-templates
          '(
            ("t" "todo" entry (file org-capture-todo)
             "* TODO %? :TODO: \n Added:%T\n"
             :clock-in t :clock-resume t)

            ;; ("ht" "test" entry (file org-capture-test)
            ;;  (function org-hugo-new-subtree-post-capture-template)
            ;;  :clock-in t :clock-resume t)

            ("h" "Hugo post")

            ("hc" "Computer-Science"
             entry (file+olp org-capture-computer-science "Computer-Science")
             (function org-hugo-new-subtree-post-capture-template)
             :clock-in t :clock-resume t)

            ("he" "Emacs"
             entry (file+olp org-capture-emacs "Emacs")
             (function org-hugo-new-subtree-post-capture-template)
             :clock-in t :clock-resume t)

            ("hm" "Mathematics"
             entry (file+olp org-capture-math "Mathematics")
             (function org-hugo-new-subtree-post-capture-template)
             :clock-in t :clock-resume t)

            ("hs" "Software-Engineering"
             entry (file+olp org-capture-software "Software-Engineering")
             (function org-hugo-new-subtree-post-capture-template)
             :clock-in t :clock-resume t)

            ("ht" "Tools"
             entry (file+olp org-capture-tools "Tools")
             (function org-hugo-new-subtree-post-capture-template)
             :clock-in t :clock-resume t)

            ("hw" "Work-Notes"
             entry (file+olp org-capture-work "Work-Notes")
             (function org-hugo-new-subtree-post-capture-template)
             :clock-in t :clock-resume t)))
    ```


## Troubleshootings {#troubleshootings}

-   If you install the theme as a submodule, you have to initialize it inside `HUGO_BASE_DIR`.

    ```sh
    git submodule update --init --recursive
    ```
-   By default the hugo server bind to `127.0.0.1`, to make it could be visited from other hosts:

    ```sh
    hugo server -D --bind 0.0.0.0
    ```
-   Hugo version from Ubuntu is lower than requirement
    -   Description

        ```text
        Current theme does not support Hugo version 0.40.1. Minimum version required is 0.50
        ```
    -   Solution
        -   Search "Hugo Release", found <https://github.com/gohugoio/hugo/releases>
        -   Install `xxx_Linux-64bit.deb`

            ```sh
            wget https://github.com/gohugoio/hugo/releases/download/v0.74.3/hugo_0.74.3_Linux-64bit.deb
            sudo dpkg -i hugo_0.74.3_Linux-64bit.deb

            hugo version
            Hugo Static Site Generator v0.74.3-DA0437B4 linux/amd64 BuildDate: 2020-07-23T16:22:34Z
            ```
        -   To uninstall

            ```sh
            sudo dpkg -r hugo
            ```


## References {#references}

-   How to customize Academic theme
    -   [Getting started](https://sourcethemes.com/academic/docs/get-started/)
-   [Blogging with Emcas Org](https://www.zcl.space/tools/my-blog-workflow/)
-   [Blogging with org-mode and ox-hugo](https://www.shanesveller.com/blog/2018/02/13/blogging-with-org-mode-and-ox-hugo/)
-   [How To Install and Use Hugo, a Static Site Generator](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-hugo-a-static-site-generator-on-ubuntu-14-04)
-   [Getting started with the Academic framework for Hugo](http://www.mit.edu/~k2smith/post/getting-started/#remove-unused-widgets-and-pages)
-   [Tips for using the Hugo academic theme](https://lmyint.github.io/post/hugo-academic-tips/)