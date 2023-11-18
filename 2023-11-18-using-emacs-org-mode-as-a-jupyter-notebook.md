# Using Emacs org-mode as a Jupyter Notebook
TODO: intro

First, we need some data. Let's create some semi-realistic usage data about text editors. Here's an org-mode source block which does that:

```org
#+begin_src bash :results verbatim
cat <<EOF > data.njson
{"name": "Spock", "editor": "Emacs"}
{"name": "James Kirk", "editor": "Vim"}
{"name": "Dr McCoy", "editor": "Vim"}
{"name": "Scotty", "editor": "Emacs"}
{"name": "Worf", "editor": "ed"}
{"name": "Geordi LaForge", "editor": "Emacs"}
{"name": "Jean-luc Picard", "editor": "VS Code"}
{"name": "Wesley Crusher", "editor": "VS Code"}
{"name": "William Riker", "editor": "Vim"}
EOF
#+end_src
```

Now, we can load that data into `pandas`, and visualise it with `seaborn`:

```org

```
