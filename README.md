talks
========

This is the chroju's briefing papers of LTs. Documents that are here markdown only, so if you would like to read slide documents, please visit [my Speaker Deck account](https://speakerdeck.com/chroju).

This repository is made for the Continuoues Integration, that convert a markdown file to slides. You could use it by forking this repository.

talk list
----

* ポエム駆動転職 ＠雑兵Meetup#7 (2016-11-04)

usage (for others)
----

* Clone this repository.
```
$ git clone https://github.com/chroju/talks
```
* Replace this repository with yours. Checkout to `release` branch, remove `.git` directory, and execute `git init` command.
```
$ cd talks
$ git checkout release
$ rm -rf .git
$ git init
```
* Edit the last sentence in `circle.yml` to your repository.
```
before)  - git push git@github.com:chroju/talks gh-pages
after)   - git push git@github.com:(your name)/talks gh-pages
```
* `git push`, and integrates with your CircleCI.
* When make a new slides, follow these steps.
```
# checkout new branch.
$ git checkout -b 160101hoge_meetup
# make the directory the same name as the branch.
$ mkdir 160101hoge_meetup
# make the markdown and `config.yml` for reveal-ck.
$ vim 160101hoge_meetup/slides.md
$ vim 160101hoge_meetup/config.yml
# git push, and CircleCI will test and convert your markdown.
```
