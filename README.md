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

(In case you already used the original plugin you should remove the old symlink.
Then **wait** about 10 minutes before add the new symlink described above.)

Next make sure that munin is allowed or at least allows this script to read
your *mail.log*. How to do this depends on your system.

On Debian 6 *mail.log* is owned by `root:adm` and readable by nobody but them.
The recommended way here is to advice munin to run postfix_mailqueue with group
adm (and user postfix, by default) by modifying */etc/munin/plugin-conf.d/munin-node*
where you should change/add a line to the `[postfix_mailqueue]` section:

	group adm

If your *mail.log* is not located at */var/log/mail.log* (the default)
you should add another line with your custom path:

	env.maillog /var/log/mail.log

**Don't forget to restart your munin-node deamon.**

syslog5mins
-----------

This little bash function returns the entries of a file (created in format of
syslog default template; especially timestamp) of the last 5 minutes.
