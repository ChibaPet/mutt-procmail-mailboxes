# mutt-procmail-mailboxes

Using different tools to receive mail in different mailboxes and read it in
a defined order can be a bit tedious. Given Mutt, procmail, and an IMAP
server, at a minimum you're adding an entry to Mutt's mailboxes list, using
some maildirmake command to create a mailbox, and then adding a filter to
get the right email delivered to that mailbox.

The notion of these scripts is that you maintain a list of lists called
`filter.list` that enumerates what you want to follow, along with the
correct keyword for filtering them. Then, scripting does the tedious bits.

Procmail rules are build from `filter.head` and `filter.list`, where
`filter.head` is a header block put in place directly, and `filter.list` is
a set of keywords determining which lists you want filtered and presented
in order to Mutt. A simple `filter.head` file might look like this:

```
$ cat filter.head
MAILDIR=$HOME/mail/
DEFAULT=$HOME/mail/
LOGFILE=$HOME/.filterlog
LOGABSTRACT=all
```

`filter.list` might look like this:

```
$ head filter.list 
sender shadowpeople-bounces
listid announce-list
xloop native
sender cee-announce-bounces
sender cxno-announce-bounces
xloop usa-announce
listid global-engineering-all
sender boston-announce-bounces
listid remote-nh
listid cee-hci-americas
```

The items in the list are in the order in which you want Mutt to present
them, with the desired keyword for identifying the list. The current
keywords and their expansions are:

```
sender Sender:.*
xloop X-Loop:.*
listid List-ID:.*
rss X-RSS-Feed:.*
to To:.*
from From:.*
deliveredto Delivered-To:.*
```

When you want to have an entry just for Mutt, with no corresponding
Procmail entry, you can use some other keyword that's not on the list. A
safe example would be `none`.

When you've subscribed to a new list, you add a new line to this file, and
run the 'filters' script, which will build your .procmailrc file.

When you start Mutt, instead of a hardcoded `mailboxes` list, it will ssh
to your mail server and run the `mailboxes` script, which dynamically
builds a mailboxes list based on the filter.list file.

To achieve this, include this in your `.mutt/muttrc` or wherever your
appropriate local config lives:

```
mailboxes * ! `ssh mymailserver bin/mailboxes`
```

Note that I strip out `-bounces` from mailbox names and the `mailboxes`
list.

WARNING and CAVEATS: I used this for an old job, and I had a flat
hierarchy of lists. This will need to be modified if you organize your
lists in a hierarchy. I'm putting it up as it is because it's still useful
as it stands, and scratches an itch I have right now.

This has `maildirmake.dovecot` hardcoded right now, but I just want to get
it up. Change that if you need to.
