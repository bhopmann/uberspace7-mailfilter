# Custom Mailfilter for Uberspace 7

> Attention: This mailfilter is intended to work for Uberspace 7 – a setup for Uberspace 6 with DSPAM and SpamAssassin can be found [here](https://github.com/bhopmann/uberspace-tools).

---

This script can be used for setting up a customized mailfilter config with **Rspamd** and **Bogofilter** on an [Uberspace 7](https://uberspace.de) server. Every message will be filtered by these two services: Learning false positives or false negatives is realized with Bogofilter, while Rspamd uses multiple filtering and statistical methods to generate a spam score (which is later used for filtering). [Custom rules](https://wiki.uberspace.de/mail:maildrop#sonstige_filtereien) can be added too. If messages are not correctly recognized as ham, they can be manually taught as ham by setting up a passlist (one email address each line). To teach Bogofilter, in the spam folder one has to mark correctly classified spam messages as read. If messages are not marked as read or moved to another folder directly after reading or if `SILENT_NEW_SPAM` is set, nothing happens.

> At this time Rspamd isn't configurable for learning on userlevel (see [manual](https://manual.uberspace.de/mail-spam.html) for the actual default behavior). According to Uberspace this might change in the future...

The main goal of this filter is a more intuitive workflow with less special folders (like `Learn as Spam` and `Learn as Ham`) when showing messages to Bogofilter via a customized `spam-learn` script so it can learn. This is realized by marking messages, recognized or reclassified as spam, with the header `X-Spam-Folder: YES`, which is removed for messages that are reclassified as ham. All reclassified messages will be re-delivered via `maildrop`. Finally, teaching spam or ham works like this:

> * When moving a message *from* 'Junk | Spam | Junk-E-Mail' *to* 'Inbox' it will be learned by Bogofilter as Ham
> * When moving a message *from* 'Inbox' *to* 'Junk | Spam | Junk-E-Mail' it will be learned by Bogofilter as Spam

*Please Note*: Only messages of the last 24 hours are interpreted by `spam-learn` to prevent slow scanning due to big folders.

# Installation of Bogofilter

Get [Bogofilter](https://www.bogofilter.org/) and install it like this:

```bash
  wget -O bogofilter.tar.xz https://sourceforge.net/projects/bogofilter/files/bogofilter-stable/bogofilter-1.2.5.tar.xz/download
  tar -xvf bogofilter.tar.xz
  cd bogofilter-1.2.5
  ./configure --with-database=sqlite3 --prefix=$HOME
  make
  make install
```
(Please adapt to current bogofilter version in the future)

# Installation of Mailfilter
### Core Files
* Set up `.qmail` and `maildrop` before (see [.qmail](http://www.lifewithqmail.org/lwq.html) and [maildrop](http://www.courier-mta.org/maildrop/) documentation for setting up)
* Rename `.mailfilter` to `.mailfilter-EXT` (replace `EXT` by your namespace) before.
* Put the file `.mailfilter` (or `.mailfilter-EXT`) and the folder `.mailfilters` in the home directory of your uberspace.
* Remember to set the correct file permissions: `chmod 600 ~/.mailfilter` (or `.mailfilter-EXT`)
* mailfilter(-EXT) logfiles can be found in `~/logs` 
* `spam-learn` goes to `~/bin` (its database will later be found in `~/.bogofilter`)
* Remember to set the correct file permissions: `chmod 755 ~/bin/spam-learn`

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
* [Custom Filter](https://github.com/bhopmann/uberspace-tools) for Uberspace 6 with DSPAM and SpamAssassin
* Uberspace 7 [manual](https://manual.uberspace.de/mail-spam.html) for filtering mails

# Feedback
This is work in progress, so do not hesitate to give feedback and/or provide pull requests.
