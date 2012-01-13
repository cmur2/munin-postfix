Low volume postfix_mailqueue munin plugin
=========================================

The original postfix_mailqueue plugin delivered with munin does very well
in many aspects. But it has a design related problem: to measure the number
of mails per queue (active, incomming, deferred, ...) it counts the entries
in the corresponding spool directories every 5 minutes (when munin core asks).
If a mail only stays a short time in a queue (especially active and incomming
queues) it won't get recognized by postfix_mailqueue.

Since on my low volume mailserver I'm interested in the total number of sent/
recieved messages in the last 5 minutes (for the deferred and hold queues the
original method is good enough since a mail there probably stays longer than
5 minutes) I changed the behavior and meaning of the "active" and "incomming"
graphs to "outgoing" (to the network) and "incomming" (from the network).
To determine the number of mails for these graphs the recent (5 minutes, hardcoded)
syslog entries are grep'ed for signaling sequences indicating some kind of
action of postfix.

Install
-------

Clone this repository or download the postfix_mailqueue file and create a
symlinkas *root* in /etc/munin/plugins by using e.g.:

	cd /etc/munin/plugins; ln -s /your/path/to/postfix_mailqueue postfix_mailqueue

Since this script does not honor a environment variable to locate your
*mail.log* you currently have to set the `MAILLOG` variable (which defaults
to */var/log/mail.log*) in the script's header.

Next make sure that munin is allowed or at least allows this script to read
your *mail.log*. How to do this depends on your system.

On Debian 6 *mail.log* is owned by `root:adm` and readable by nobody but them.
The recommended way here is to advice munin to run postfix_mailqueue with group
adm (and user postfix, by default) by modifying */etc/munin/plugin-conf.d/munin-node*
where you should change/add a line to the `[postfix_mailqueue]` section:

	group adm

Later you will set the *mail.log* path here, too.

syslog5mins
-----------

This little script returns the entries of a (syslog created) file of the last
5 minutes.
