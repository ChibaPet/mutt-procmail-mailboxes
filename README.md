# mutt-procmail-mailboxes

Using different tools to receive mail in different mailboxes and read it in
a defined order can be a bit tedious. Given Mutt, procmail, and an IMAP
server, at a minimum you're adding an entry to Mutt's mailboxes list, using
some maildirmake command to create a mailbox, and then adding a filter to
get the right email delivered to that mailbox.

The notion of these scripts is that you maintain a list of lists you want
to follow, along with the correct keyword for filtering them, and scripting
does the tedious bits.

A list of lists will look something like this:

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
them, with the desired keyword for identifying the list. When you've
subscribed to a new list, you add a new line to this file, and run the
'filters' script, which will build your .procmailrc file. For the example
given, the procmail rules would start off as follows:

```
MAILDIR=$HOME/mail/
DEFAULT=$HOME/mail/
LOGFILE=$HOME/.filterlog
LOGABSTRACT=all

:0:
*^Sender:.*shadowpeople-bounces
shadowpeople/

:0:
*^List-ID:.*announce-list
announce-list/

:0:
*^X-Loop:.*native
native/

...
```

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
