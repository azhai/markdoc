# Markdoc

Markdoc is a lightweight Markdown-based wiki system. It’s been designed to allow
you to create and manage wikis as quickly and easily as possible.


## What is it good for?

Potential use cases for Markdoc include, but aren’t limited to:

*   **Technical Documentation/Manuals**  
    Markdoc can be used to write and render hand-written guides and manuals for
    software. Such documentation will normally be separate from
    automatically-generated API documentation, and might give a higher-level
    view than API docs alone. It might be used for client documentation for
    web/desktop applications, or even developer documentation for frameworks.

*   **Internal Project Wikis**  
    Markdoc wikis consist of a single plain-text file per page. By combining a
    wiki with a DVCS (such as [Mercurial][] or [Git][]), you can collaborate
    with several other people. Use the DVCS to track, share and merge changes
    with one another, and view the wiki’s history.
    
  [Mercurial]: http://mercurial.selenic.com/
  [Git]: http://git-scm.com/

*   **Static Site Generation**  
    Markdoc converts wikis into raw HTML files and media. This allows you to
    manage a blog, personal website or a collection of pages in a Markdoc wiki,
    perhaps with custom CSS styles, and publish the rendered HTML to a website.
    Markdoc need not be installed on the hosting site, since the resultant HTML
    is completely independent.


## Cool Features

*   Set up [Google Analytics][] tracking in one line of configuration.

*   [Barebones][] wikis that just look like directories with Markdown-formatted
    text files in them.

*   A built-in HTTP server and WSGI application to serve up a compiled wiki with
    a single command.

*   Continuous builds (via `rsync`) mean the server can keep running whilst
    Markdoc re-compiles the wiki. Just refresh your browser to see the changes.

*   Add [Pygments][]-powered syntax highlighting to your Markdoc wiki with a
    single [configuration parameter][syntax-highlighting].

*   Markdoc is [public domain software][licensing]. It will always be completely
    free to use, and you can redistribute it (in part or in whole) under any
    circumstances (open-source, proprietary or otherwise) with no attribution or
    encumberances.

[google analytics]: http://markdoc.org/ref/configuration#metadata
[barebones]: http://markdoc.org/tips/barebones
[pygments]: http://pygments.org/
[syntax-highlighting]: http://markdoc.org/tips/syntax-highlighting
[licensing]: http://markdoc.org/about#license


## Quickstart

### Requirements

The minimum requirements to run the Markdoc utility are:

  * Python 2.4 or later (2.5+ highly recommended)
  * A UNIX (or at least POSIX-compliant) operating system
  * [rsync](http://www.samba.org/rsync/) -- installed out of the box with most
    modern OSes, including Mac OS X and Ubuntu. In the future Markdoc may
    include a pure-Python implementation.


### Installation

    $ pip install https://github.com/azhai/markdoc/tarball/master #OR
    $ pip install https://gitcafe.com/azhai/markdoc/tarball/master #OR
    $ pip install https://git.oschina.net/azhai/markdoc/tarball/master


### Making a Wiki

    markdoc init my-wiki
    cd my-wiki/
    vim wiki/somefile.md
    # ... write some documentation ...
    markdoc build
    markdoc serve
    # .. open http://localhost:8008/ in a browser ...
    
    
### Example of a blog python-md5.md

    date:       2012-05-15
    title:      Python计算文件的MD5
    category:   Python
    tag:        python, md5, hash


    下面两个函数，一个用于计算字符串的MD5，另一个用于计算指定文件的MD5

        :::python
        #-*- coding: utf-8 -*-

        import hashlib


        def md5hex(word):
            """ MD5加密算法，返回32位小写16进制符号 """
            if isinstance(word, unicode):
                word = word.encode("utf-8")
            elif not isinstance(word, str):
                word = str(word)
            m = hashlib.md5()
            m.update(word)
            return m.hexdigest()


        def md5sum(fname):
            """ 计算文件的MD5值 """
            def read_chunks(fh):
                fh.seek(0)
                chunk = fh.read(8096)
                while chunk:
                    yield chunk
                    chunk = fh.read(8096)
                else: #最后要将游标放回文件开头
                    fh.seek(0)
            m = hashlib.md5()
            if isinstance(fname, basestring) \
                    and os.path.exists(fname):
                with open(fname, "rb") as fh:
                    for chunk in read_chunks(fh):
                        m.update(chunk)
            #上传的文件缓存 或 已打开的文件流
            elif fname.__class__.__name__ in ["StringIO", "StringO"] \
                        or isinstance(fname, file):
                for chunk in read_chunks(fname):
                    m.update(chunk)
            else:
                return ""
            return m.hexdigest()
    
    
### Example of a markdoc.yaml
    
    # Metadata
    wiki-name: "xxx's Blog"
    wiki-author: "xxx"
    #google-analytics: UA-XXXXXX-X

    # Directories
    hide-prefix: ""
    wiki-dir: "content"
    static-dir: "static"
    html-dir: "html"
    template-dir: ""
    temp-dir: "tmp"
    cvs-exclude: true

    # Building
    document-extensions: [.md, .rst]
    generate-listing: always
    listing-filename: "default.html"
    use-default-static: true
    use-default-templates: true
    disqus-sitename: "xxx-disqus"

    # Rendering
    markdown:
      safe-mode: false
      output-format: xhtml1
      extensions: [meta, codehilite, def_list, sane_lists, toc]
      extension-configs:
        codehilite:
          css_class: codehilite
          force_linenos: true

    # Serving
    server:
      bind: '192.168.0.1'
      port: 8008
      num-threads: 10
      #name: 'myblog.github.com'
      request-queue-size: 5
      timeout: 10


## (Un)license

This is free and unencumbered software released into the public domain.

Anyone is free to copy, modify, publish, use, compile, sell, or
distribute this software, either in source code form or as a compiled
binary, for any purpose, commercial or non-commercial, and by any
means.

In jurisdictions that recognize copyright laws, the author or authors
of this software dedicate any and all copyright interest in the
software to the public domain. We make this dedication for the benefit
of the public at large and to the detriment of our heirs and
successors. We intend this dedication to be an overt act of
relinquishment in perpetuity of all present and future rights to this
software under copyright law.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR
OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.

For more information, please refer to <http://unlicense.org/>
