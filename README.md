# Custom Mailfilter for Uberspace 7

> Attention: This mailfilter does only work for Uberspace 7 – a setup for Uberspace 6 with DSPAM and SpamAssassin can be found [here](https://github.com/stratmaster/uberspace-tools).

---

This script can be used for setting up a customized mailfilter config with **Rspamd** and **Bogofilter** on an [Uberspace 7](https://uberspace.de) server. Every message will be filtered by these two services. [Custom rules](https://wiki.uberspace.de/mail:maildrop#sonstige_filtereien) can be added too. If messages are not correctly recognised as ham, they can be manually taught as ham by setting up a whitelist (one email address each line).

> At this time Rspamd isn't configurable for learning on userlevel (see [manual](https://manual.uberspace.de/mail-filter.html) for the actual default function). According to Uberspace this might change in the future...

The main goal of this filter is a more intuitive workflow with less special folders (like `Learn as Spam` and `Learn as Ham`) when showing messages to Bogofilter via a customized `spam-learn` script so it can learn. This is realized by marking messages, recognised or reclassified as spam, with the header `X-Spam-Folder: YES`, which is removed for messages that are reclassified as ham. All reclassified messages will be re-delivered via `maildrop`. Finally, teaching spam or ham works like this:

> * When moving a message *from* 'Junk | Spam | Junk-E-Mail' *to* 'Inbox' it will be learned by Bogofilter as Ham
> * When moving a message *from* 'Inbox' *to* 'Junk | Spam | Junk-E-Mail' it will be learned by Bogofilter as Spam

*Please Note*: Only messages of the last 24 hours are interpreted by `spam-learn` to prevent slow scanning due to big folders.

# Installation of Bogofilter

Get [Bogofilter](https://www.bogofilter.org/) and install it like this:

```bash
  ./configure --with-database=sqlite3 --prefix=$HOME
  make
  install
```

# Installation of Mailfilter
### Core Files
* Set up `.qmail` and `maildrop` before (see uberspace Wiki for setting up [.qmail](https://wiki.uberspace.de/mail:dotqmail) and [Maildrop](https://wiki.uberspace.de/mail:maildrop))
* Rename `.mailfilter` to `.mailfilter-EXT` (replace `EXT` by your namespace) before, if you are using [vmailmgr](https://wiki.uberspace.de/mail:vmailmgr).
* Put the file `.mailfilter` (or `.mailfilter-EXT`) and the folder `.mailfilters` in the home directory of your uberspace.
* Remember to set the correct file permissions: `chmod 600 ~/.mailfilter` (or `.mailfilter-EXT`)
* Create directory `~/var/log` - it will be used as path for mailfilter logfile
* `spam-learn` goes to `~/bin` (its logfile and database will later be found in `~/..bogofilter`)

### Adding `spam-learn` as a cronjob

Add `spam-learn` as a cronjob that is running once per hour:

Call crontab

```bash
  crontab -e
```

and add this lines

```bash
  # Call spam-learn hourly
  @hourly ~/bin/spam-learn
```

# Credits and used code (snippets)
* Uberspace [Maildrop](https://wiki.uberspace.de/mail:maildrop) and [DSPAM](https://wiki.uberspace.de/mail:dspam) Tutorials
* Uberspace 7 [manual](https://manual.uberspace.de/mail-filter.html)
* [Thorsten Köster](https://blog.macfrog.de/2014/05/10/maildrop-revisited/)
* [Christian González](https://github.com/nerdoc/uberspace-tools)
* [Jonas Pasche](http://blog.jonaspasche.com/2010/03/23/dspam-automatisch-trainieren/)

# Feedback
This is work in progress, so do not hesitate to give feedback and/or provide pull requests.
