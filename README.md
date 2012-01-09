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

syslog5mins
-----------

This little script returns the entries of a (syslog created) file of the last
5 minutes.
**You should modify the scripts location in postfix_mailqueue script!**
