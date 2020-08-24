+++
title = "Sync two folders with commands in Linux"
date = 2019-08-14T08:41:00+08:00
lastmod = 2020-08-24T10:08:48+08:00
tags = ["Tools", "Bash", "Linux"]
draft = false
weight = 10
autoCollapseToc = true
mathjax = true
contentCopyright = "MIT"
+++

```sh
rsync -av --exclude='.git/' --exclude='*.*~' --include='*.org' /home/zw/code/org/ ~/code/siemens/org --delete
```

-   It puts folder /code/org 's content into folder /code/siemens/org.
-   useful options
    -   -a Do the sync preserving all filesystem attributes
    -   -v run verbosely
    -   --delete delete the files in target folder that do not exist in the source.
-   It could also sync two folders across different hosts

    ```sh
    rsync -av --exclude='.git/' --exclude='*.*~' --include='*.org' ~/code/my-site/content tester1@134.244.221.41:/home/tester1/my-site/content --delete
    ```
-   see: [How to Use Rsync to Sync New or Changed/Modified Files in Linux](https://www.tecmint.com/sync-new-changed-modified-files-rsync-linux/)